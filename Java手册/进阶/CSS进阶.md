### 1. 网页的布局方式

固定宽度布局：为网页设置一个固定的宽度，通常以px作为长度单位，常见于PC端网页

流式布局：为网页设置一个相对的宽度，通常以百分比作为长度单位

栅格化布局：将页面宽度人成均等的长度，然后排版布局时则以这些均等的长度作为度量单位，通常利用百分比作为长度单位来划分均等的长度

响应式布局：通过检测设备信息，决定网页布局方式，即用户如果采用不同的设备访问一个网页，有可能看到不一样的内容，一般情况下是检测设备屏幕的宽度来实现

> 以上几种布局方式并不是独立存在的，时机开发过程中往往是互相结合使用的


### 2. 响应式布局

响应式布局意在实现不同分辨率的终端上浏览网页的不同展示方式，通过响应式设计能使网站在手机和平板电脑上有更好的浏览阅读体验

我们利用**媒体查询**可以检测到屏幕的尺寸(主要检测宽度)，并设置不同的CSS样式，就可以实现响应式的布局

我们利用响应式布局可以满足不同尺寸的终端设备非常完美的展现网页内容，使得用户体验得到了很大的提升，但是为了实现这一目的我们不得不利用媒体查询编写大量的冗余代码，使整体网页的体积变大，应用在移动设备上会带来严重的性能问题


> 响应式布局常应用于企业的官网、博客、新闻资讯类型的网站，这些网站以浏览内容为主，没有复杂的交互

一般我们会对常见的设备尺寸进行划分后，再分别确定不同尺寸的设备设计专门的布局方式

| 类型      | 布局宽度       |
| :-------- | :------------ |
| 大屏幕     | 大于等于1200px |
| 默认      | 大于等于980px  |
| 平板      | 大于等于768px  |
| 手机到平板 | 小于等于767px  |
| 手机      | 小于等于480px  |

以上是我们对常见尺寸进行分类后的结果，下面是与之对应的媒体查询条件

```css
/*大屏幕*/
@media(min-width:1200px){...}
/*平板电脑和小屏电脑之间的分辨率*/
@media(min-width:768px) and (max-width:979px){...}
/*横向放置的手机和竖向放置的平板之间的分辨率*/
@media(max-width:767px){...}
/*横向放置的手机及分辨率更小的设备*/
@media(max-width:480px)
```

#### 2.1. 什么是响应式开发

 在移动互联日益成熟的时候，桌面浏览器上开发的网页已经不能满足移动端设备的展示和阅读。 之前，通常的做法是针对移动端单独开发一套特定的版本但是，如果移动终端设备越来越多的时候开发成本太大，是因为需要做所有屏幕的适配

响应式开发的目的就是：一个网站能够兼容多种终端，在新建的网站上一般都会使用响应式开发，移动web开发和响应式开发是必须具备的技能

响应式开发和移动端开发的区别

| 开发方式 | 移动web开发+PC端开发                                  | 响应式开发                                                        |
| :------ | :--------------------------------------------------- | :--------------------------------------------------------------- |
| 应用场景 | 一般已经有PC端的网站，开发移动站的时候只需要单独开发移动端 | 针对新建站的一些网站，现在要求适配移动端，所以一套页面兼容各种终端、灵活 |
| 开发     | 针对性强，开发效率高                                   | 兼容各种终端、效率低                                               |
| 适配     | 只使用移动设备，PAD上体验相对较差                       | 可以适配各种终端                                                   |
| 效率     | 代码简洁，加载快                                       | 代码相对复杂，加载慢                                               |



#### 2.2. 响应式开发原理：媒体查询

查询媒介，查询到当前屏幕的宽度，针对不同的屏幕宽度设置不同的样式来使用不同的屏幕，当你重置浏览器大小的过程中，页面也会根据浏览器的宽度和高度重新渲染页面，简单说，你可以设置不同屏幕下面的不同样式，达到适配不同屏幕的目的

媒体查询的基本实现方式


```css
@media mediatype and |not | only(media feature){
    //样式代码
}
```

你也可以针对不同的媒体使用的不同的样式

```css
<link rel="stylesheet" media="mediatype and |not | only(media feature)" href="style.css">
```

媒体类型

| 值     | 描述                            |
| :----- | :----------------------------- |
| all    | 用于所有设备                     |
| print  | 用于打印机和打印预览             |
| screen | 用于电脑屏幕、平板电脑、只能手机等 |
| speech | 应用于屏幕阅读器等发生设备        |


媒体功能



| 值                | 描述                            |
| :---------------- | :----------------------------- |
| device-height     | 定义输出设备的屏幕可见高度        |
| device-width      | 定义输出设备的可见宽度            |
| max-device-height | 定义输出设备的的屏幕可见的最大高度 |
| min-device-height | 定义输出设备的的屏幕可见的最小高度 |
| max-device-width  | 定义输出设备的的屏幕可见的最大宽度 |
| min-device-width  | 定义输出设备的的屏幕可见的最小宽度 |
| min-width         | 定义输出设备中页面最小可见区域宽度 |
| max-width         | 定义输出设备中页面最大可见区域宽度 |
| max-height        | 定义输出设备中页面最大可见区域高度 |
| min-height        | 定义输出设备中页面最小可见区域高度 |





> 当判断最小的值，并且从小到大进行判断
> - 向上兼容
> - 向下覆盖，后面设置的样式将覆盖前面设置的样式覆盖


```css
 <style>
        /* 768以下 */
        body {
            background-color: red;
        }

        /* 当屏幕宽度大于等于768 */
        @media screen and (min-width: 768px) {
            body {
                background-color: lightblue;
            }
        }

        /* 当屏幕宽度大于等于992 */
        @media screen and (min-width: 992px) {
            body {
                background-color: green;
            }
        }

        /* 当屏幕宽度大于等于1200 */
        @media screen and (min-width: 1200px) {
            body {
                background-color: yellowgreen;
            }
        }
</style>
```



### 3. CSS预处理器Less

- Less是一种动态样式语言，属于CSS 预处理器的范畴，它扩展了CSS语言

- 增加了变量、Mixin、函数等特性，使CSS更易维护和扩展

- Less既可以在客户端上运行，也可以借助NodeJS在服务器端运行

#### 3.1. less编译工具

- koala    官网：www.koala-app.com

#### 3.2. less中的注释

- 以 //开头的注释，不会被编译到CSS文件中

- 以/**/包裹的注释会被编译到CSS文件中

#### 3.3. less中的变量

使用@来申明一个变量：@pink:pink

- 作为普通属性值值来使用：直接使用@pink

- 作为选择器和属性名：#@{selector的值}的形式

- 作为URL：@{url}

- 变量的延迟加载

#### 3.4. less的嵌套规则

基本嵌套规则

&的使用

#### 3.5. less中的混合

混合就是将一系列属性从一个规则集引入到另一个规则集的方式

1. 普通混合

2. 不带输出的混合

3. 带参数的混合

4. 带参数并且有默认值的混合

5. 带多个参数的混合

6. 命名参数

7. 匹配模式

8. arguments变量

#### 3.6. less运算

在less中可以进行加减乘除的运算

#### 3.7. less避免编译








#### 3.8. less继承

性能比混合高

灵活度比混合低


### 4. bootstrap中的less

简洁、直观、强悍的前端开发框架，让web开发更迅速、简单

中文网 ： http://www.bootcss.com/

英文网  :  http://getbootstrap.com/

#### 4.1. 容器

流体布局容器

- 容器的width为auto，只是两边加了15px的padding。


- 固定布局

   - 容器的width会随设备分辨率的不同而生产变化

   - 分辨率阈值

```
w >=1200	 		容器的width为1170   左右padding为15 （注意是borderBox）
1200>w >=992		容器的width为970     左右padding为15 （注意是borderBox）
992 > w >=768		容器的width为750     左右padding为15  （注意是borderBox）
768 > w >=992		容器的width为auto    左右padding为15  （注意是borderBox）
```

#### 4.2. 栅格系统

col-lg-x

col-md-x

col-sm-x

col-xs-x

x默认拥有12个等级


#### 4.3. 列偏移

调整的是margin-left，分13个等级（0到12）0时为0%


#### 4.4. 列排序

push的时候调整的是left，分13个等级（0到12）0时为auto


pull的时候调整的是right，分13个等级（0到12）0时为auto




#### 4.5. 容器与栅格盒模型设计的精妙之处

container 提供了一个15px的padding，row 是 column 直接存在的容器，row 默认最多可有12个 column，同时作为都是左浮动的 column 的 wrapper，自带 clearfix 的性质。

同时 row 还有一个很特殊的地方，就是左右各有 －15px 的 margin，为了抵消 container 中15px的 padding，每个column 也会有15px的水平方向的 padding，colunmn 只能在 row 中生存，由于 row 的 margin 为－15px，那么位于两边的 column 就碰到了 container 的边界。

但是 colunmn 本身又有 15px 的 padding 使得它其中的内容并不会碰到 container，同时 不同column的内容之间就有了30px的槽，目的是为了确保列与列之间有30px的槽，列与容器之间有15px的槽









