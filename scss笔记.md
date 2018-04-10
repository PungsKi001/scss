# 基于个人关于scss的笔记 #

编写：PungsKi
目录
[1.vue加入scss的方法](#1)
[2.导入scss样式](#2)
[3.变量](#3)
[4.嵌套](#4)
[5.@at-root](#5)
[6.混合(mixin)](#6)
[7.继承](#7)
[8.函数](#8)

## 1.vue加入scss的方法 {#1} ##

package.json内加入

```scss
dependencies:{
  "sass": "^1.1.1"
}
devDependencies:{
  "node-sass": "^4.8.3",
  "sass-loader": "^6.0.7",
}
```

或者

```scss
cnpm install sass --save
cnpm install node-sass --save-dev
cnpm install sass-loader --save-dev
```

## 2.导入scss样式 {#2} ##

```scss
@import "./common/scss/common.scss";
```

## 3.变量 {#3} ##

### 普通变量：`$fontSize: 12px;` ###

### 默认变量：`$fontSize: 12px!default;`（可覆盖） ###

### 特殊变量：`#{$variables}` ###

```scss
$borderDirection:       top !default; 
$baseFontSize:          12px !default;
$baseLineHeight:        1.5 !default;

//应用于class和属性
.border-#{$borderDirection}{
  border-#{$borderDirection}:1px solid #ccc;
}
//应用于复杂的属性值
body{
    font:#{$baseFontSize}/#{$baseLineHeight};
}
```

### 多值变量： ##

#### list ####

`nth($var,$index)`取值，具体使用可方法搜索[sass Functions](http://sass-lang.com/documentation/Sass/Script/Functions.html) 内List Functions

```scss
//一维数据
$px: 5px 10px 20px 30px;

//二维数据，相当于js中的二维数组
$px: 5px 10px, 20px 30px;
$px: (5px 10px) (20px 30px);
```

#### map ####
map数据以key和value成对出现，其中value又可以是list。`map-get($map,$key)`取值，具体使用可方法搜索[sass Functions](http://sass-lang.com/documentation/Sass/Script/Functions.html) 内Map Functions

```scss
格式为： $map: (key1: value1, key2: value2, key3: value3);
//例
$headings: (h1: 2em, h2: 1.5em, h3: 1.2em);
```

### 全局变量：`$fontSize: 12px!global;`（没用过） ###

## 4.嵌套 {#4} ##

### 选择器嵌套 ###

>&表示父元素选择器
### 属性嵌套 ###

```scss
//sass style
//-------------------------------
.fakeshadow {
  border: {
    style: solid;
    left: {
      width: 4px;
      color: #888;
    }
    right: {
      width: 2px;
      color: #ccc;
    }
  }
}

//css style
//-------------------------------
.fakeshadow {
  border-style: solid;
  border-left-width: 4px;
  border-left-color: #888;
  border-right-width: 2px;
  border-right-color: #ccc; 
}
```

## 5.@at-root（没用过，暂时不管） {#5} ##

跳出所有上级选择器

```scss
//单个选择器跳出
.parent-2 {
  color:#f00;
  @at-root .child {
    width:200px;
  }
}
//多个选择器跳出
.parent-3 {
  background:#f00;
  @at-root {
    .child1 {
      width:300px;
    }
    .child2 {
      width:400px;
    }
  }
}
```

`@at-root (without: ...)`和 `@at-root (with: ...)`

## 6.混合(mixin) {#6} ##

@include来调用

### 无参数 ###

```scss
//sass style
//-------------------------------
@mixin center-block {
    margin-left:auto;
    margin-right:auto;
}
.demo{
    @include center-block;
}

//css style
//-------------------------------
.demo{
    margin-left:auto;
    margin-right:auto;
}
```

### 有参数或多个 ###

```scss
//sass style
//-------------------------------   
@mixin opacity($opacity:50) {
  opacity: $opacity / 100;
  filter: alpha(opacity=$opacity);
}
.opacity{
  @include opacity; //参数使用默认值
}
.opacity-80{
  @include opacity(80); //传递参数
}

//css style
//-------------------------------
.opacity{
  opacity: 0.5;
  filter: alpha(opacity=50);
}
.opacity-80{
  opacity: 0.8;
  filter: alpha(opacity=80);
}
```

### 多组值参数 ###

```scss
//sass style
//-------------------------------   
//box-shadow可以有多组值，所以在变量参数后面添加...
@mixin box-shadow($shadow...) {
  -webkit-box-shadow:$shadow;
  box-shadow:$shadow;
}
.box{
  border:1px solid #ccc;
  @include box-shadow(0 2px 2px rgba(0,0,0,.3),0 3px 3px rgba(0,0,0,.3),0 4px 4px rgba(0,0,0,.3));
}

//css style
//-------------------------------
.box{
  border:1px solid #ccc;
  -webkit-box-shadow:0 2px 2px rgba(0,0,0,.3),0 3px 3px rgba(0,0,0,.3),0 4px 4px rgba(0,0,0,.3);
  box-shadow:0 2px 2px rgba(0,0,0,.3),0 3px 3px rgba(0,0,0,.3),0 4px 4px rgba(0,0,0,.3);
}
```

### @content ###

用来解决css3的@media等带来的问题。它可以使 @mixin接受一整块样式，接受的样式从@content开始

```scss
 //sass style
//-------------------------------                     
@mixin max-screen($res){
  @media only screen and ( max-width: $res )
  {
    @content;
  }
}

@include max-screen(480px) {
  body { color: red }
}

//css style
//-------------------------------
@media only screen and (max-width: 480px) {
  body { color: red }
}
```

## 7.继承 {#7} ##

`@extend`后面紧跟需要继承的选择器

### 占位选择器 % ###

如果不调用则不会有任何多余的css文件，避免了以前在一些基础的文件中预定义了很多基础的样式

## 8.函数 {#8} ##
