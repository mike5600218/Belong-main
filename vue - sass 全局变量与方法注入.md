# VUE - sass 全局变量与方法注入

> 在vue-cli中，使用 sass-resources-loader 实现全局变量、方法注入

 vue-cli项目初始化后配置 sass ，想要在项目中使用 sass 的全局变量与方法需安装 sass-resources-loader 



##  安装 sass-resources-loader

```
$ npm i sass-resources-loader --save-dev
```



## 修改 build / utils.js

在以下代码 `scss: generateLoaders('sass'),`  的后面添加 `.concat({})`：

```js
return {
    css: generateLoaders(),
    postcss: generateLoaders(),
    less: generateLoaders('less'),
    sass: generateLoaders('sass', {
      indentedSyntax: true
    }),
    // 添加 .concat({loader: 'sass-resources-loader',options:{}})
    // 其中 path.resolve(__dirname, '路径文件名') 
    // 改为你想要全局注入的那个sass 路径文件名
    scss: generateLoaders('sass').concat(
      {
        loader: 'sass-resources-loader',
        options: {
          resources: path.resolve(__dirname, '../src/assets/styles/mixin.scss')
        }
      }
    ),
    stylus: generateLoaders('stylus'),
    styl: generateLoaders('stylus')
  }　
```



## 创建全局变量与方法

在上而引入文件的路径创建 `mixin.scss` 文件

- 创建全局变量：使用 `$` 符开头  `:`  号后面设置需要的变量值 

  `$app-blue: #4698e7;`

- 创建方法：使用 `@mixin` 开头后面为一个 `function` 函数，形参需使用 `$` 符开头使用时与形参保持一致

  `@mixin wh($width, $height) { width: $width; height: $height; }`

```scss
// 主色调
$app-blue: #4698e7;
// 字体大小
$size10: 20px;
//宽高
@mixin wh($width, $height) {
    width: $width;
    height: $height;
}
```



## 在页面中使用全局变量与方法

- 使用全局变量：直接将设置的全局变量名放置的要使用的值后面即可

  `.title { background-color: $app-blue }` 

  转译结果：

  ```css
  .title {
  	background-color: #4698e7;
  }
  ```

- 使用方法：需使用 `@include 方法名()` 的方式使用

  `.title { background-color: $app-blue; @include wh(100px,50px) }`

  转译结果：

  ```css
  .title {
      background-color: #4698e7;
      width: 100px;
      height: 50px;
  }
  ```




## 源文件

### mixin.scss

```scss
// 主色调
$app-blue: #4698e7;
$app-blue-6: #314156;
$app-yellow: #f5c94d;
$app-pink: #f73564;

$app-white-bb: #a9b3bb;
$app-white-ed: #e6e9ed;
$app-white-f5: #f1f4f5;
$app-white: #fff;

// 文字颜色
$text-color: #383838;
$text-active: #f5c94d;
$text-white: #fff;
$text-gray: #d5d5d5;

// 字体大小
$size18: 36px;
$size16: 32px;
$size14: 28px;
$size12: 24px;
$size10: 20px;

// 背景图片地址和大小
@mixin bis($url) {
    background-image: url($url);
    background-repeat: no-repeat;
    background-size: 100% 100%;
}

@mixin borderRadius($radius) {
    -webkit-border-radius: $radius;
    -moz-border-radius: $radius;
    -ms-border-radius: $radius;
    -o-border-radius: $radius;
    border-radius: $radius;
}

//定位全屏 
@mixin allcover {
    position: absolute;
    top: 0;
    right: 0;
}

//定位上下左右居中
@mixin center {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
}

//定位上下居中
@mixin ct {
    position: absolute;
    top: 50%;
    transform: translateY(-50%);
}

//定位上下居中  
@mixin cl {
    position: absolute;
    left: 50%;
    transform: translateX(-50%);
}

//宽高
@mixin wh($width, $height) {
    width: $width;
    height: $height;
}

//字体大小、行高、字体
@mixin font($size, $line-height, $family: 'Microsoft YaHei') {
    font: #{$size}/#{$line-height} $family;
}

//字体大小，颜色
@mixin sc($size, $color) {
    font-size: $size;
    color: $color;
}

//flex 布局和 子元素 对其方式
@mixin faj($atype: center,$jtype: space-between) {
    display: flex;
    align-items: $atype;
    justify-content: $jtype;
}

// 超出范围字体使用···隐藏
@mixin to($line){
    text-overflow:ellipsis;        // 用省略号“…”隐藏超出范围的文本
    overflow:hidden;		       // 超出范围的内容隐藏
    display:-webkit-box;	       // 将对象作为弹性伸缩盒子模型显示 
    -webkit-line-clamp: $line;     // 第几行出现省略号
    -webkit-box-orient:vertical;   // 设置或检索伸缩盒对象的子元素的排列方式 
}
```

### common.scss

```scss
body, div, span, header, footer, nav, section, aside, article, ul, dl, dt, dd, li, a, p, h1, h2, h3, h4,h5, h6, i, b, textarea, button, input, select, figure, figcaption {
    padding: 0;
    margin: 0;
    list-style: none;
    font-style: normal;
    text-decoration: none; 
    border: none;
    font-family: "Microsoft Yahei",sans-serif;
    -webkit-tap-highlight-color:transparent;
    -webkit-font-smoothing: antialiased;
    &:focus {
        outline: none;
    }
}

/*定义滚动条高宽及背景 高宽分别对应横竖滚动条的尺寸*/  
::-webkit-scrollbar  
{  
    width: 4px;  
    height: 5px;  
    background-color: #F5F5F5;  
}  
  
/*定义滚动条轨道 内阴影+圆角*/  
::-webkit-scrollbar-track  
{  
    -webkit-box-shadow: inset 0 0 1px rgba(0,0,0,0);  
    border-radius: 10px;  
    background-color: #F5F5F5;  
}  
  
/*定义滑块 内阴影+圆角*/  
::-webkit-scrollbar-thumb  
{  
    border-radius: 10px;  
    -webkit-box-shadow: inset 0 0 6px rgba(0,0,0,.3);  
    background-color: #999;  
}  

input[type="button"], input[type="submit"], input[type="search"], input[type="reset"] {
    -webkit-appearance: none;
}

textarea { -webkit-appearance: none;}   

html,body{
    height: 100%;
    width: 100%;
    background-color: #fff;
}
#app {
    height: 100%;
    width: 100%;
}
.fillcontain{
    height: 100%;
    width: 100%;
}
.clear:after{
    content: '';
    display: block;
    clear: both;
}
.clear_80{
    clear: both;
    height: 80px;
    width: 100%;
}
.clear_90{
    clear: both;
    height: 90px;
    width: 100%;
}
.clear{
    zoom:1;
}

.back_img{
    background-repeat: no-repeat;
    background-size: 100% 100%;
}

.margin{
    margin: 0 auto;
}

.left{
    float: left;
}

.right{
    float: right;
}

.hide{
    display: none;
}

.show{
    display: block;
}

.ellipsis{
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
}
```

