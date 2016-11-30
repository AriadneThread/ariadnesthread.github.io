---
title: Android选中放大Gallery效果的实现
date: 2016-11-30 14:24:35 +08:00
categories:
- Android
layout: post
author: wuzhen
---

> **需求：**横向滚动的列表，操作类似 Gallery，选中的 Item 居中且放大显示，UI 效果如下图：
![UI效果示例.jpg](http://upload-images.jianshu.io/upload_images/1320493-3929dd01896887fc.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 实现方案：

从 API 16 开始，因为效率问题系统控件 [Gallery](http://developer.android.com/intl/zh-cn/reference/android/widget/Gallery.html) 已被废弃，官方建议使用 [`HorizontalScrollView`](http://developer.android.com/reference/android/widget/HorizontalScrollView.html) 实现，但是根据效果图使用 [`HorizontalScrollView`](http://developer.android.com/reference/android/widget/HorizontalScrollView.html) 实现工作量较大，所以引入了第三方控件：[EcoGallery](https://github.com/falnatsheh/EcoGallery)。

**1. 重写 `EcoGallery` 的 `getChildStaticTransformation(View, Transformation)` 方法：**

``` java
// View mSelectedChild：记录选中的 Item 的 View
// float mSelectedScale：选中的 Item 放大的倍数
@Override
protected boolean getChildStaticTransformation(View child,
        Transformation t) {
    if (mSelectedScale != 1.f) {
        t.clear();
        t.setTransformationType(Transformation.TYPE_MATRIX);

        // 判断需要变换的 child 是否为选中的 View
        if (child == mSelectedChild) {
            // 根据效果图，child 以 (x轴中心、y轴最底部) 为中心点进行缩放
            // 设置缩放的中心点横坐标为 child 的 x 轴中心位置
            float pivotX = child.getWidth() / 2.f;

            // 设置缩放的中心点纵坐标为 child 的 y 轴最底部
            float pivotY = child.getHeight() - child.getPaddingBottom();
            Matrix matrix = t.getMatrix();
            matrix.postScale(mSelectedScale, mSelectedScale,
                    pivotX, pivotY);
        }
    }
    return true;
}
```

该实现方案有两个问题：
1. 如果不指定默认选中的 Item，进入页面后第0个 Item 会处于选中状态，之后滚动 Gallery 选中其他的 Item 后，第0个 Item 不会变小，一直显示成放大后的效果，直到再次选中第0个 Item 后才能恢复正常，打印 Log 发现进入页面后从第0个滚动到其他 Item 时 `getChildStaticTransformation()` 方法中的 child 没有第0个 Item，具体原因未知。
2. Item 选中后会瞬间变大、变小，体验上不是很好。

因为上面的两个问题，于是放弃了该实现方案。

之后搜索了一些资料，发现了这篇文章：
[关于getChildStaticTransformation在android4.1失效问题解决方案](http://blog.csdn.net/csusunxgg/article/details/8692034)

> 通过在画 child 之前对 Canvas 进行必要的变换，如上 Transformation 变换类似，重载 drawChild 方法

根据效果图，具体的实现方案思路为：因为 Gallery 选中的 Item 居中显示，所以在绘制 Item 时先计算 Item 和 Gallery 中心点的距离。如果距离＝0，此时 Item 应该放大显示，且放大的比例为最大值；列表滚动后，当前放大显示的 Item 远离中心点，放大的比例逐渐变小，而此时列表另一端的 Item 正在接近中心点，显示的比例逐渐变大；当 Item 与中心点的距离超过 Item 的宽度时不再进行放大显示。

``` java
// View mSelectedChild：记录选中的 Item 的 View
// float mSelectedScale：选中的 Item 放大的倍数
@Override
protected boolean drawChild(Canvas canvas, View child,
        long drawingTime) {

    if (mSelectedScale != 1.f) {
        // 获取 Item 的 View 的宽度和高度
        int childWidth = child.getWidth();
        int childHeight = child.getHeight();

        // 获取 Gallery 中心点坐标的 x 值
        final int center = getCenterOfGallery();

        // 获取 Item 中心点坐标的 x 值
        final int childCenter = child.getLeft() + childWidth / 2;

        // 计算 child 中心点和 Gallery 中心点的距离
        final int offsetCenter = Math.abs(center - childCenter);
        final float offsetScale = (childWidth - offsetCenter)
                * 1.f / childWidth;

        // 如果 child 和中心点的距离小于 child 的宽度，此时需要放大显示
        if (offsetCenter < childWidth) {
            // 设置缩放的中心点坐标
            child.setPivotX(childWidth / 2.f);
            child.setPivotY(childHeight - child.getPaddingBottom());

            // 根据和中心点的距离计算缩放比例
            float scale = 1 + (mSelectedScale - 1) * offsetScale;
            child.setScaleX(scale);
            child.setScaleY(scale);
        }
        // 如果 child 和中心点的距离不小于 child 的宽度，不需要放大显示
        else {
            child.setScaleX(1.f);
            child.setScaleY(1.f);
        }
    }
    return super.drawChild(canvas, child, drawingTime);
}
```

最终实现的效果如下图：

![Gallery最终效果图](http://upload-images.jianshu.io/upload_images/1320493-4ab9967c4ae0e834.gif?imageMogr2/auto-orient/strip)

---

## 参考

1. [GitHub: EcoGallery](https://github.com/falnatsheh/EcoGallery)
2. [关于getChildStaticTransformation在android4.1失效问题解决方案](http://blog.csdn.net/csusunxgg/article/details/8692034)

## 源码

[GitHub: EcoGalleryDemo](https://github.com/wuzhendev/samples/tree/master/EcoGalleryDemo)

---

## 开源

目前该控件已开源：[GitHub: android-wgallery](https://github.com/wuzhendev/android-wgallery)
