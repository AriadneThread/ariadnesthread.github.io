---
layout: post
title:  "AndroidStudio创建文件模板"
author: wuzhen
categories: Android
tags: [Android]
---

**开发环境：Mac OS、Android Studio 2.1.2**

在使用 Android Studio（AS） 创建 Activity 时，会发现 AS 中预定了很多模板，如下图：


本文介绍下怎么自定义一些文件的模板。

## 模板文件的保存路径

AS 中的文件模板保存在以下目录中：

> Mac：
> Android Studio.app/Contents/plugins/android/lib/templates
>
> Windows：
>

在该目录下有 `activities` 和 `other` 两个文件夹用来保存文件模板。
`activities` 目录下主要是一些 Activity 的模板，`other` 目录下有很多其他类型文件的模板，其实 Activity 的模板也可以放在 `other` 目录下。

## 模板文件的目录结构

以 `activities/EmptyActivity` 模板为例，分析下模板文件的目录结构：

```
EmptyActivity
  ├── globals.xml.ftl
  ├── recipe.xml.ftl
  ├── root
    ├── src
      ├── app_package
        ├── SimpleActivity.java.ftl
  ├── template_blank_activity.png
  ├── template.xml
```

可以看到主要文件的后缀名都是 `.ftl`，这些文件都是 [FreeMarker](http://freemarker.org) 模板文件，最终通过 FreeMarker 模板引擎生成 java/xml 文件。

> FreeMarker：免费的模板引擎，基于Apache许可证2.0版本发布。基于模板和要改变的数据，并用来生成输出文本（HTML网页、电子邮件、配置文件、源代码等）的通用工具。其模板编写为FreeMarker Template Language（FTL），属于简单、专用的语言。【From：[百度百科](http://baike.baidu.com/view/1278430.htm)】

下面逐个分析文件中的内容：

### 1. globals.xml.ftl

该文件用于定义全局变量。

``` ftl
<?xml version="1.0"?>
<globals>
    <global id="hasNoActionBar" type="boolean" value="false" />
    <global id="parentActivityClass" value="" />
    <global id="simpleLayoutName" value="${layoutName}" />
    <global id="excludeMenu" type="boolean" value="true" />
    <global id="generateActivityTitle" type="boolean" value="false" />
    <#include "../common/common_globals.xml.ftl" />
</globals>
```

全局变量使用 `<global>` 标签定义：
- `id`: 变量的唯一标识。
- `type`: 变量的类型。
- `value`: 变量的值。`${layoutName}` 是在 `template.xml` 文件中定义的变量，下文中会进行介绍。

该文件又引用了 `../common/common_globals.xml.ftl` 文件，打开 `common_globals.xml.ftl` 文件可以看到其中又有很多定义的全局变量，不再进行详细介绍。

### 2. recipe.xml.ftl

该文件用于指定生成代码、资源文件等的规则。

``` ftl
<?xml version="1.0"?>
<recipe>
    <#include "../common/recipe_manifest.xml.ftl" />

<#if generateLayout>
    <#include "../common/recipe_simple.xml.ftl" />
    <open file="${escapeXmlAttribute(resOut)}/layout/${layoutName}.xml" />
</#if>

    <instantiate from="root/src/app_package/SimpleActivity.java.ftl"
            to="${escapeXmlAttribute(srcOut)}/${activityClass}.java" />

    <open file="${escapeXmlAttribute(srcOut)}/${activityClass}.java" />

    <!-- 因 EmptyActivity 模板较简单，以下是从其他模板文件中复制过来的，用来示例 -->
    <dependency mavenUrl="com.android.support:support-v4:${buildApi}.+" />
    <copy from="root/res/drawable-hdpi"
            to="${escapeXmlAttribute(resOut)}/drawable-hdpi" />
    <merge from="root/build.gradle.ftl"
            to="${escapeXmlAttribute(projectOut)}/build.gradle" />
</recipe>
```

该文件中常用的标签：
- `open`: 创建成功后打开指定的文件。
- `instantiate`: 指定 `FTL` 文件和最终生成的代码文件之间的对应关系。
- `dependency`: 指定文件的依赖库。
- `copy`: 复制指定的文件到项目中。
- `merge`: 合并指定的文件到项目中。

> `<#if generateLayout>` 是 freemarker 的逻辑判断语法，更多详细的语法可以查看下文中的 **参考资料**

### 3. root/src/app_package/SimpleActivity.java.ftl

该文件是具体 java 类的模板，最终生成的代码根据用户的设置动态变化。

``` ftl
package ${packageName};

import ${superClassFqcn};
import android.os.Bundle;

public class ${activityClass} extends ${superClass} {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
<#if generateLayout>
        setContentView(R.layout.${layoutName});
</#if>
    }
}
```

可以看到该文件实际就是一个 Activity 的代码，只不过 包名、类名、xml文件名 等都是根据当前项目环境或用户的设置动态变化的。

### 4. template_blank_activity.png

该图片用于创建文件时设置面板的图片展示，使用 AS `New - Activity - Empty Activity` 即可查看图片的展示效果，不再赘述。

### 5. template.xml

模板的主要配置信息，在该文件中可以配置设置面板。

``` xml
<?xml version="1.0"?>
<template
    format="5"
    revision="5"
    name="Empty Activity"
    minApi="7"
    minBuildApi="14"
    description="Creates a new empty activity">

    <category value="Activity" />
    <formfactor value="Mobile" />

    <parameter
        id="activityClass"
        name="Activity Name"
        type="string"
        constraints="class|unique|nonempty"
        suggest="${layoutToActivity(layoutName)}"
        default="MainActivity"
        help="The name of the activity class to create" />

    <parameter
        id="generateLayout"
        name="Generate Layout File"
        type="boolean"
        default="true"
        help="If true, a layout file will be generated" />

    <parameter
        id="layoutName"
        name="Layout Name"
        type="string"
        constraints="layout|unique|nonempty"
        suggest="${activityToLayout(activityClass)}"
        default="activity_main"
        visibility="generateLayout"
        help="The name of the layout to create for the activity" />

    <parameter
        id="isLauncher"
        name="Launcher Activity"
        type="boolean"
        default="false" />

    <parameter
        id="packageName"
        name="Package name"
        type="string"
        constraints="package"
        default="com.mycompany.myapp" />

    <!-- 128x128 thumbnails relative to template.xml -->
    <thumbs>
        <!-- default thumbnail is required -->
        <thumb>template_blank_activity.png</thumb>
    </thumbs>

    <globals file="globals.xml.ftl" />
    <execute file="recipe.xml.ftl" />

</template>
```

结合创建文件的设置面板介绍下标签的作用：

主要标签的说明介绍：

```
template: 定义模板。
    name: New 菜单中的标题。
    description: 设置面板中的描述信息。
    minApi: 最低的版本要求，如果项目最低版本小于该值则不能使用该模板。
category: New 菜单中的分类，比如：Activity、Fragment、AIDL...
parameter: 设置面板中可配置的参数。
    id: 唯一标识。
    name: 设置项的提示名称。
    type: 值的类型。
    constraints: 值的约束条件。
    suggest: 建议的值。
    default: 默认值。
    visibility: 是否显示的判断条件。
    help: 对该参数进行操作时面板底部的提示信息。
thumbs: 指定面板左边的提示图片。
globals: 全局变量。
execute: 执行的代码生成规则的模板文件。
```

## 总结

以上就是文件模板的主要配置参数，根据需求可以创建一些常用文件的模板，以提高开发效率。

## 参考资料

1. []()
