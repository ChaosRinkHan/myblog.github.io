---
title: Hexo 博文简单美化
date: 2018-04-19 10:49:26
tags:
  - Hexo
---



#### 文字背景色块

##### 添加

打开`themes/next/source/css/_custom` 下的 `custom.styl` 文件,添加自定属性样式

```css
// 颜色块-黄
span#inline-yellow {
display:inline;
padding:.2em .6em .3em;
font-size:80%;
font-weight:bold;
line-height:1;
color:#fff;
text-align:center;
white-space:nowrap;
vertical-align:baseline;
border-radius:0;
background-color: #f0ad4e;
}
```

想添加自定义颜色，修改最后的backgroud-color即可。

 <!-- more -->

##### 使用

在md正文中如下格式：

`<span id="inline-blue"> Text here </span>`

就可使用了。

效果：

<span id="inline-blue"> Text </span> <span id="inline-red"> Text </span>  <span id="inline-yellow"> Text </span> <span id="inline-purple"> Text </span> 

#### 使用[FA图标](https://fontawesome.com/icons)

在md正文中使用

`<i class="fa fa-subway"></i>`

来调用FA图标。第二个fa后单词改为需要的图标名称即可。

改变大小：经过测试，有用的包括

```html
<i class="fa fa-camera-retro fa-lg"></i>
<i class="fa fa-camera-retro fa-2x"></i>
<i class="fa fa-camera-retro fa-3x"></i>
<i class="fa fa-camera-retro fa-5x"></i>
```



##### 效果

<i class="fa fa-subway"></i>
<i class="fa fa-camera-retro fa-lg"></i>
<i class="fa fa-camera-retro fa-2x"></i>
<i class="fa fa-camera-retro fa-3x"></i>
<i class="fa fa-camera-retro fa-5x"></i>

#### 文本居中的引用

这是<span id="inline-blue"> Next</span>提供的，方法如下：

```html
{% cq %}
文本
{% endcq %}
```

效果如下：

{% cq %}
文本
{% endcq %}