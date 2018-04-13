# 基于个人关于 scss 问题的笔记

编写：PungsKi(彭苏琦)

版本:1.4

## 1.vue 加入 scss 的方法

> package.json 内加入

```json
dependencies:{
  "sass": "^1.1.1"
}
devDependencies:{
  "node-sass": "^4.8.3",
  "sass-loader": "^6.0.7",
}
```

> 或者

```bash
$ cnpm install sass --save
$ cnpm install node-sass --save-dev
$ cnpm install sass-loader --save-dev
```

## 2.导入 scss 样式

```scss
@import "./common/scss/common.scss";
```

### 分音

如果需要导入 SCSS 或者 Sass 文件，但又不希望将其编译为 CSS，只需要在文件名前添加下划线，这样会告诉 Sass 不要编译这些文件，但导入语句中却不需要添加下划线。

例如，将文件命名为 \_colors.scss，便不会编译 \_colours.css 文件。

> 注意，不可以同时存在添加下划线与未添加下划线的同名文件，添加下划线的文件将会被忽略。

## 3.变量

* 普通变量：`$fontSize: 12px;`

* 默认变量：`$fontSize: 12px !default;`

  > 可以在变量的结尾添加 !default 给一个未通过 !default 声明赋值的变量赋值，此时，如果变量已经被赋值，不会再被重新赋值，但是如果变量还没有被赋值，则会被赋予新的值。

* 特殊变量（插值语句）：`#{$variables}`

  ```scss
  $borderDirection: top;
  $baseFontSize: 12px;
  $baseLineHeight: 1.5;

  //应用于class和属性
  .border-#{$borderDirection} {
    border-#{$borderDirection}: 1px solid #ccc;
  }
  //应用于复杂的属性值
  body {
    font: #{$baseFontSize}/#{$baseLineHeight};
  }
  ```

* 多值变量：

  > list：`nth($var,$index)`取值，具体使用可方法搜索[sass Functions](http://sass-lang.com/documentation/Sass/Script/Functions.html) 内 List Functions

      ```scss
      //一维数据
      $px: 5px 10px 20px 30px;

      //二维数据，相当于js中的二维数组
      $px: 5px 10px, 20px 30px;
      $px: (5px 10px) (20px 30px);
      ```

  > map：map 数据以 key 和 value 成对出现，其中 value 又可以是 list。`map-get($map,$key)`取值，具体使用可方法搜索[sass Functions](http://sass-lang.com/documentation/Sass/Script/Functions.html) 内 Map Functions

      ```scss
      格式为： $map: (key1: value1, key2: value2, key3: value3);
      //例
      $headings: (h1: 2em, h2: 1.5em, h3: 1.2em);
      ```

* 全局变量：`$fontSize: 12px!global;`
  > 在块作用域内定义全局变量

## 4.嵌套

* 选择器嵌套

  > &表示父元素选择器

* 属性嵌套

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

## 5.@at-root（没未用过）

> 跳出所有上级选择器

```scss
//单个选择器跳出
.parent-2 {
  color: #f00;
  @at-root .child {
    width: 200px;
  }
}
//多个选择器跳出
.parent-3 {
  background: #f00;
  @at-root {
    .child1 {
      width: 300px;
    }
    .child2 {
      width: 400px;
    }
  }
}
```

> `@at-root (without: ...)`和 `@at-root (with: ...)`

## 6.混合(mixin)

> @include 来调用

* 无参数

  ```scss
  //sass style
  //-------------------------------
  @mixin center-block {
    margin-left: auto;
    margin-right: auto;
  }
  .demo {
    @include center-block;
  }

  //css style
  //-------------------------------
  .demo {
    margin-left: auto;
    margin-right: auto;
  }
  ```

* 有参数或多个

  ```scss
  //sass style
  //-------------------------------
  @mixin opacity($opacity: 50) {
    opacity: $opacity / 100;
    filter: alpha(opacity=$opacity);
  }
  .opacity {
    @include opacity; //参数使用默认值
  }
  .opacity-80 {
    @include opacity(80); //传递参数
  }

  //css style
  //-------------------------------
  .opacity {
    opacity: 0.5;
    filter: alpha(opacity=50);
  }
  .opacity-80 {
    opacity: 0.8;
    filter: alpha(opacity=80);
  }
  ```

* 多组值参数（供多种属性值使用）

  ```scss
  //sass style
  //-------------------------------
  //box-shadow可以有多组属性值，所以在变量参数后面添加...
  @mixin box-shadow($shadow...) {
    -webkit-box-shadow: $shadow;
    box-shadow: $shadow;
  }
  .box {
    border: 1px solid #ccc;
    @include box-shadow(
      0 2px 2px rgba(0, 0, 0, 0.3),
      0 3px 3px rgba(0, 0, 0, 0.3),
      0 4px 4px rgba(0, 0, 0, 0.3)
    );
  }

  //css style
  //-------------------------------
  .box {
    border: 1px solid #ccc;
    -webkit-box-shadow: 0 2px 2px rgba(0, 0, 0, 0.3), 0 3px 3px rgba(0, 0, 0, 0.3),
      0 4px 4px rgba(0, 0, 0, 0.3);
    box-shadow: 0 2px 2px rgba(0, 0, 0, 0.3), 0 3px 3px rgba(0, 0, 0, 0.3),
      0 4px 4px rgba(0, 0, 0, 0.3);
  }
  ```

* @content

  > 用来解决 css3 的@media 等带来的问题。它可以使 @mixin 接受一整块样式，接受的样式从@content 开始

  ```scss
  //sass style
  //-------------------------------
  //定义media-queries的最小最大宽度
  @mixin screen($res-min, $res-max) {
    @media screen and (min-width: $res-min) and (max-width: $res-max) {
      @content;
    }
  }
  @include screen(780px, 1000px) {
    body {
      font-size: 14px;
    }
  }

  //css style
  //-------------------------------
  @media screen and (min-width: 780px) and (max-width: 1000px) {
    body {
      font-size: 14px;
    }
  }
  ```

## 7.继承

> `@extend`后面紧跟需要继承的选择器

### 占位选择器 %

> 如果不调用则不会有任何多余的 css 文件，避免了以前在一些基础的文件中预定义了很多基础的样式

```scss
//sass style
//-------------------------------
$lte7: true;
%clearfix {
  @if $lte7 {
    *zoom: 1;
  }
  &:before,
  &:after {
    content: "";
    display: table;
    font: 0/0 a;
  }
  &:after {
    clear: both;
  }
}
```

## 8.函数

> px2rem

```scss
//sass style
//-------------------------------
$designWidth: 375;
@function px2rem($px) {
  @return $px * 320 / $designWidth/20 + rem;
}

p {
  font-size: px2rem(20);
}

//css style
//-------------------------------
p {
  font-size: 1.25rem;
}
```

> 960 网格布局，要几个格子就传递数字几。有些特别需求，要不了刚好的网格，比喻比 4 个格子要少 30px，get_width 的第三个参数，专门负责搞定额外需求的，get_width(4,true,30px)得到的就是 270px。

* 默认的如 get_width(3)得到 220px；
* 不需要左右 margin 的如 get_width(3，false)得到 240px；
* 可以灵活缩小点宽度如 get_width(3,true,10px)得到 210px。

```scss
//sass style
//-------------------------------
$_columns: 12 !default; // 总列数
$_column-width: 60px !default; // 单列宽
$_gutter: 20px !default; // 间隔
@function get_width($columns: $_columns, $onlyInnerWidth: true, $_subtract: 0) {
  // 默认返回值
  $return: ($_column-width + $_gutter) * $columns - $_subtract !default;
  @if $onlyInnerWidth==true {
    //如果$onlyInnerWidth为true，那么减掉一个间隔$_gutter
    $return: ($_column-width + $_gutter) * $columns - $_gutter - $_subtract;
  }
  @return $return;
}
#container {
  width: get_width(12, false); //960px
}
.col-four {
  // @extend %clearfix;
  .col {
    // @include float;
    margin: 0 $_gutter / 2;
    width: get_width(3); //220px
    h2 {
      padding-left: 10px;
      width: get_width(3, true, 10px); //210px
    }
  }
}

//css style
//-------------------------------
#container {
  width: 960px;
}
.col-four .col {
  margin: 0 10px;
  width: 220px;
}
.col-four .col h2 {
  padding-left: 10px;
  width: 210px;
}
```

## 9.运算

> 可以对数值型的 Value(如：数字、颜色、变量等)进行加减乘除四则运算，注意运算符前后请留一个空格。

```scss
$baseFontSize: 14px !default;
$baseLineHeight: 1.5 !default;
$baseGap: $baseFontSize * $baseLineHeight !default;
$halfBaseGap: $baseGap / 2 !default;
$samllFontSize: $baseFontSize - 2px !default;
```

## 10.条件判断及循环

* @if 判断
  > 可一个单独使用，也可以和 @else 结合多条件使用
* 三目判断

  * `if($condition, $if_true, $if_false)`

    条件，条件为真的值，条件为假的值

* @for 循环

  * `@for $var from <start> through <end>`

    through 表示包括 end 这个数；

  * `@for $var from <start> to <end>`

    to 表示不包括 end 这个数

* @each

  * `@each $var in <list or map>`

    $var 表示变量，而 list 和 map 表示 list 类型数据和 map 类型数据。sass 3.3.0 新加入了多字段循环和 map 数据循环；

    >

### 单个字段 list 数据循环

```scss
//sass style
//-------------------------------
$animal-list: puma, sea-slug, egret, salamander;
@each $animal in $animal-list {
  .#{$animal}-icon {
    background-image: url("/images/#{$animal}.png");
  }
}

//css style
//-------------------------------
.puma-icon {
  background-image: url("/images/puma.png");
}
.sea-slug-icon {
  background-image: url("/images/sea-slug.png");
}
.egret-icon {
  background-image: url("/images/egret.png");
}
.salamander-icon {
  background-image: url("/images/salamander.png");
}
```

### 多个字段 list 数据循环

```scss
//sass style
//-------------------------------
$animal-data: (puma, black, default), (sea-slug, blue, pointer),
  (egret, white, move);
@each $animal, $color, $cursor in $animal-data {
  .#{$animal}-icon {
    background-image: url("/images/#{$animal}.png");
    border: 2px solid $color;
    cursor: $cursor;
  }
}

//css style
//-------------------------------
.puma-icon {
  background-image: url("/images/puma.png");
  border: 2px solid black;
  cursor: default;
}
.sea-slug-icon {
  background-image: url("/images/sea-slug.png");
  border: 2px solid blue;
  cursor: pointer;
}
.egret-icon {
  background-image: url("/images/egret.png");
  border: 2px solid white;
  cursor: move;
}
```

### 多个字段 map 数据循环

```scss
//sass style
//-------------------------------
$headings: (
  h1: 2em,
  h2: 1.5em,
  h3: 1.2em
);
@each $header, $size in $headings {
  #{$header} {
    font-size: $size;
  }
}

//css style
//-------------------------------
h1 {
  font-size: 2em;
}
h2 {
  font-size: 1.5em;
}
h3 {
  font-size: 1.2em;
}
```

triangle 例子

```scss
//sass style
//-------------------------------
@mixin triangle($direction, $size, $borderColor) {
  $dirArray: (top, bottom), (right, left);
  content: "";
  height: 0;
  width: 0;
  font-size: index($dirArray, $direction);
  @each $dir in $dirArray {
    $indexA: index($dirArray, $dir);
    @if index($dir, $direction) {
      $index: index($dir, $direction);
      border-#{nth($dir,length($dir) - $index + 1)}: $size solid $borderColor;
      @each $other in nth($dirArray, length($dirArray) - $indexA + 1) {
        border-#{$other}: $size dashed transparent;
      }
    }
  }
}
.test {
  @include triangle(bottom, 12px, #ff5200);
}

//css style
//-------------------------------
.test {
  content: "";
  height: 0;
  width: 0;
  border-top: 12px solid #ff5200;
  border-right: 12px dashed transparent;
  border-left: 12px dashed transparent;
}
```

## 11.屏幕自适应问题

+ DPR

那么什么是 DPR (Device Pixel Ratio)？这里不妨先给它个定义：window.devicePixelRatio 是设备上物理像素 (physical pixels) 和设备无关像素 (device-independent pixels (dips)) 的比例。公式表示就是：window.devicePixelRatio = physical pixels / dips

这个定义里的 dips 并不太好理解，现在我们尝试通过下面几点来搞明白：

首先，dips 是一种度量单位
然后，要知道浏览器并不是根据物理硬件的像素来工作的，而是根据 dips 宽度来工作。
最后，还要知道 dips 是将像素值与实际距离联系起来的，不管屏幕的像素密度是多少，dips 为 1px，那么实际宽度就是 1px，也就是对应 CSS 中的 1px，而不是对应物理像素 1px。
这里我们就可以看到，1px 并不总是等于 1px。

搞明白 dips，我们举个简单例子，MacBook Pro 13.3 英寸的显示器分辨率是 2560 x 1600，这个 2560px 就是我们前面说的设备上的物理像素值，而浏览器全屏显示的宽度只有 1280px，这个就是 dips 值，最终可以知道这台 MacBook Pro 电脑屏幕的 DPR 为 2，DPR 在这里所表达的意思就是：1280 dips 在实际显示的时候，被硬件扩展到了 2560 的硬件像素宽度，2 个物理像素对应 1 个 CSS 像素（这个指的水平方向或垂直方向，如果在一个平面内的话 4 个物理像素点对应 1 个 CSS 像素点）。

如果现在上面这台电脑里有一张实际宽度为 200px 的高清图片，在浏览器里被 css 设置宽度为 200px，那么这张图片看起来就会有点模糊，因为它实际被硬件扩展到了 400px 的硬件像素宽度，是它实际宽度的两倍。但是，如果它被 css 设置宽度为 100px，这时候它实际被硬件扩展到了 200px 的硬件像素宽度，和它实际像素一致，就不会模糊了。

* 使用 hotcss + px2rem 方案
  > 字体无法缩小到系统默认字体以下

```bash
$ cnpm install px2rem-loader --save-dev
```

```js
// main.js，引入hotcss.js
import "./api/hotcss";
// utils.js
const px2remLoader = {
  loader: "px2rem-loader",
  options: {
    remUnit: 23.4375 // 设计稿的宽度/16
  }
};
function generateLoaders(loader, loaderOptions) {
  const loaders = [cssLoader, px2remLoader];
  if (loader) {
    loaders.push({
      loader: loader + "-loader",
      options: Object.assign({}, loaderOptions, {
        sourceMap: options.sourceMap
      })
    });
  }
  ...//其余代码
}
```

```scss
//scss style
//-------------------------------
img {
  width: 300px;
}
a {
  font-size:15px;/*no*/no为不转换
}
```