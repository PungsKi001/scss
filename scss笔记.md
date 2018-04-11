# 基于个人关于scss的笔记 #

编写：PungsKi

## 1.vue加入scss的方法 ##

>package.json内加入

```json
dependencies:{
  "sass": "^1.1.1"
}
devDependencies:{
  "node-sass": "^4.8.3",
  "sass-loader": "^6.0.7",
}
```

>或者

```scss
cnpm install sass --save
cnpm install node-sass --save-dev
cnpm install sass-loader --save-dev
```

## 2.导入scss样式 ##

```scss
@import "./common/scss/common.scss";
```

## 3.变量 ##

+ 普通变量：`$fontSize: 12px;`

+ 默认变量：`$fontSize: 12px!default;`（可被覆盖，放在覆盖变量之后）

+ 特殊变量：`#{$variables}`

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

+ 多值变量：

  >list：`nth($var,$index)`取值，具体使用可方法搜索[sass Functions](http://sass-lang.com/documentation/Sass/Script/Functions.html) 内List Functions

      ```scss
      //一维数据
      $px: 5px 10px 20px 30px;

      //二维数据，相当于js中的二维数组
      $px: 5px 10px, 20px 30px;
      $px: (5px 10px) (20px 30px);
      ```

  >map：map数据以key和value成对出现，其中value又可以是list。`map-get($map,$key)`取值，具体使用可方法搜索[sass Functions](http://sass-lang.com/documentation/Sass/Script/Functions.html) 内Map Functions

      ```scss
      格式为： $map: (key1: value1, key2: value2, key3: value3);
      //例
      $headings: (h1: 2em, h2: 1.5em, h3: 1.2em);
      ```

+ 全局变量：`$fontSize: 12px!global;`（没用过）

## 4.嵌套 ##

+ 选择器嵌套

  >&表示父元素选择器

+ 属性嵌套

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

## 5.@at-root（没用过，暂时不管） ##

>跳出所有上级选择器

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

>`@at-root (without: ...)`和 `@at-root (with: ...)`

## 6.混合(mixin) ##

>@include来调用

+ 无参数

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

+ 有参数或多个

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

+ 多组值参数（供多种属性值使用）

  ```scss
  //sass style
  //-------------------------------
  //box-shadow可以有多组属性值，所以在变量参数后面添加...
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

+ @content

  >用来解决css3的@media等带来的问题。它可以使 @mixin接受一整块样式，接受的样式从@content开始

  ```scss
  //sass style
  //-------------------------------
  //定义media-queries的最小
  @mixin max-screen($res){
    @media only screen and ( max-width: $res )
    {
      @content;
    }
  }
  //定义media-queries的最小最大宽度
  @mixin screen($res-min, $res-max) {
    @media screen and ( min-width: $res-min) and ( max-width: $res-max) {
      @content;
    }
  }
  @include max-screen(480px) {
    body { color: red }
  }
  @include screen(780px, 1000px) {
    body {
      font-size: 14px;
    }
  }

  //css style
  //-------------------------------
  @media only screen and (max-width: 480px) {
    body { color: red }
  }
  @media screen and (min-width: 780px) and (max-width: 1000px) {
    body {
      font-size: 14px;
    }
  }
  ```

## 7.继承 ##

>`@extend`后面紧跟需要继承的选择器

### 占位选择器 % ###

>如果不调用则不会有任何多余的css文件，避免了以前在一些基础的文件中预定义了很多基础的样式

```scss
//sass style
//-------------------------------
$lte7:true;
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

## 8.函数 ##

>px2rem

```scss
//sass style
//-------------------------------
@function px2rem($px, $base: 16) {
  @return ($px / $base) * 1rem;
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

>960网格布局，要几个格子就传递数字几。有些特别需求，要不了刚好的网格，比喻比4个格子要少30px，get_width的第三个参数，专门负责搞定额外需求的，get_width(4,true,30px)得到的就是270px。

+ 默认的如get_width(3)得到220px；
+ 不需要左右margin的如get_width(3，false)得到240px；
+ 可以灵活缩小点宽度如get_width(3,true,10px)得到210px。

```scss
//sass style
//-------------------------------
$_columns: 12 !default; // 总列数
$_column-width: 60px !default; // 单列宽
$_gutter: 20px !default; // 间隔
@function get_width($columns:$_columns, $onlyInnerWidth: true, $_subtract:0) {
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

## 9.运算 ##

>可以对数值型的Value(如：数字、颜色、变量等)进行加减乘除四则运算，注意运算符前后请留一个空格。

```scss
$baseFontSize : 14px !default;
$baseLineHeight : 1.5 !default;
$baseGap : $baseFontSize * $baseLineHeight !default;
$halfBaseGap : $baseGap / 2 !default;
$samllFontSize : $baseFontSize - 2px !default;
```

## 10.条件判断及循环 ##

+ @if判断
  >可一个单独使用，也可以和 @else结合多条件使用
+ 三目判断
  + `if($condition, $if_true, $if_false)`

    条件，条件为真的值，条件为假的值
+ @for循环
  + `@for $var from <start> through <end>`

    through表示包括end这个数；
  + `@for $var from <start> to <end>`

    to表示不包括end这个数
+ @each
  + `@each $var in <list or map>`

    $var表示变量，而list和map表示list类型数据和map类型数据。sass 3.3.0新加入了多字段循环和map数据循环；

    >

### 单个字段list数据循环 ###

```scss
//sass style
//-------------------------------
$animal-list: puma, sea-slug, egret, salamander;
@each $animal in $animal-list {
  .#{$animal}-icon {
    background-image: url('/images/#{$animal}.png');
  }
}

//css style
//-------------------------------
.puma-icon {
  background-image: url('/images/puma.png'); 
}
.sea-slug-icon {
  background-image: url('/images/sea-slug.png'); 
}
.egret-icon {
  background-image: url('/images/egret.png'); 
}
.salamander-icon {
  background-image: url('/images/salamander.png'); 
}
```

### 多个字段list数据循环 ###

```scss
//sass style
//-------------------------------
$animal-data: (puma, black, default),(sea-slug, blue, pointer),(egret, white, move);
@each $animal, $color, $cursor in $animal-data {
  .#{$animal}-icon {
    background-image: url('/images/#{$animal}.png');
    border: 2px solid $color;
    cursor: $cursor;
  }
}

//css style
//-------------------------------
.puma-icon {
  background-image: url('/images/puma.png');
  border: 2px solid black;
  cursor: default; 
}
.sea-slug-icon {
  background-image: url('/images/sea-slug.png');
  border: 2px solid blue;
  cursor: pointer; 
}
.egret-icon {
  background-image: url('/images/egret.png');
  border: 2px solid white;
  cursor: move; 
}
```

### 多个字段map数据循环 ###

```scss
//sass style
//-------------------------------
$headings: (h1: 2em, h2: 1.5em, h3: 1.2em);
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

triangle例子

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
.test { content: ""; height: 0; width: 0; border-top: 12px solid #ff5200; border-right: 12px dashed transparent; border-left: 12px dashed transparent; }
```