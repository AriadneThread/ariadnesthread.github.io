---
title: Android基于javacv环境编译ffmpeg
date: 2016-12-08 00:00:00 +08:00
categories:
- jekyll
layout: post
author: Darre
---

## 背景：

最近在项目里用Javacv+FFmpeg来实现视频的实时处理，而FFmpeg提供了音视频编码库libavcodec，为了能够在安卓平台上运行，这时候需要对FFmpeg进行编译，最后生成我们所需要的so文件。下面来说说具体编译步骤：

## 环境及准备工作：

1、	Linux系统（我用的是cent os虚拟机）

2、	安装yasm-1.3.0.tar.gz

3、	Last_stable_x264.tar.bz2

4、	Ffmpeg-2.1.1.tar.bz2

5、	Android-ndk-r10b

6、	FFmpeg-2.1.1-android-arm.patch（javacv提供的修补文件）

## 步骤：

1、	安装yasm-1.3.0.tar.gz

(1)	解压：
tar -jxvf yasm-1.3.0.tar.gz

(2)	编译：
./configure

(3)	安装：
make&&make install

解压android-ndk-r10b同上

2、	编写脚本文件：build_ffmpeg-android-arm.sh

//声明ndk存放路径

ANDROID_NDK=/root/android-ndk-r10b

//FFmpeg版本

FFMPEG_VERSION=2.1.1

//ndk编译库路径

ANDROID_BIN=$ANDROID_NDK/toolchains/arm-linux-androideabi-4.9/prebuilt/linux-x86_64/bin

//编译的版本

ANDROID_ROOT=$ANDROID_NDK/platforms/android-14/arch-arm

//解压FFmpeg

tar -xjvf ffmpeg-$FFMPEG_VERSION.tar.bz2

//重命名解压后的文件

mv ffmpeg-$FFMPEG_VERSION ffmpeg-$FFMPEG_VERSION-android-arm

cd ffmpeg-$FFMPEG_VERSION-android-arm

//解压last_stable

tar -xjvf ../last_stable_x264.tar.bz2

//H.264视频编码函数库

X264=`echo x264-snapshot-*` 

cd $X264

//这步坑了很久有很多参数，可以./configure –help查看 编译

./configure --enable-static --enable-pic --disable-asm --disable-cli --cross-prefix=$ANDROID_BIN/arm-linux-androideabi- --sysroot=$ANDROID_ROOT --host=arm-linux --extra-cflags="-DANDROID -fPIC -ffunction-sections -funwind-tables -fstack-protector -march=armv7-a -mfloat-abi=softfp -mfpu=vfpv3-d16 -mfpu=neon -fomit-frame-pointer -fstrict-aliasing -funswitch-loops -finline-limit=300" --extra-ldflags="-nostdlib -Wl,--fix-cortex-a8 -lgcc -ldl -lz -lm -lc"

make -j4

cd ../

//添加修补文件

patch -p1 < ../ffmpeg-$FFMPEG_VERSION-android-arm.patch

//重新编译

./configure --prefix=$ANDROID_NDK/../ --enable-shared --enable-gpl --enable-version3 --enable-runtime-cpudetect --disable-asm --disable-outdev=sdl --enable-libx264 --extra-cflags="-I$X264" --extra-ldflags="-L$X264" --enable-cross-compile --cc=$ANDROID_BIN/arm-linux-androideabi-gcc --sysroot=$ANDROID_ROOT --target-os=linux --arch=arm --extra-cflags="-DANDROID -fPIC -ffunction-sections -funwind-tables -fstack-protector -march=armv7-a -mfloat-abi=softfp -mfpu=vfpv3-d16 -mfpu=neon -fomit-frame-pointer -fstrict-aliasing -funswitch-loops -finline-limit=300" --extra-ldflags="-nostdlib -Wl,--fix-cortex-a8" --extra-libs="-lgcc -ldl -lz -lm -lc" --disable-stripping --disable-symver --disable-programs

make -j4

//申明生成的so文件

LIBS="libavcodec/libavcodec.so libavdevice/libavdevice.so libavfilter/libavfilter.so libavformat/libavformat.so libavutil/libavutil.so libpostproc/libpostproc.so libswresample/libswresample.so libswscale/libswscale.so"
$ANDROID_NDK/toolchains/arm-linux-androideabi-4.8/prebuilt/linux-x86_64/bin/arm-linux-androideabi-strip $LIBS

//创建so文件存放路径

mkdir -p com/googlecode/javacv/cpp/android-arm/

cp $LIBS com/googlecode/javacv/cpp/android-arm/

//打包，将最后的so文件打成ffmpeg-2.1.1-android-arm.jar包

jar cvf ../ffmpeg-$FFMPEG_VERSION-android-arm.jar com/

rm -Rf com/

cd ../

通过上面部分就生成了我们需要的so文件。但是要特别注意一点—disable-asm要加上否则在6.0以上版本会出现UnsafisfiedLinkError:dlopen failed text relocations错误，官方的解释是在Android6.0之前，text reloactions问题，会在编译的过程中作为warning报出来，而之后的版本原来的warning升级为error了，因此，同样的库文件，在Android 6.0之前的环境运行，不报错，6.0后的版本就会crash掉，解决的方法是在编辑源码的的时候加上—disable-asm，这样就会忽略这个错误信息，最后的生成的so文件也能正常使用。

## 总结：

FFmpeg提供的编解码库基于C来做的，所以不懂C会比较麻烦，现在为止也是不断摸索，在过程中会有一些未知问题。只能慢慢研究了。
