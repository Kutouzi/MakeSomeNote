## web笔记-->初级CSS属性和标签总结

在此将默认已经略微了解W3中CSS的初级教程篇,不包含CSS3.
文章会先将初级篇的基本用到的属性作用介绍一遍,在最后会说明如何在元素中使用介绍的属性

### *元素固有属性(inherent attributes)

在一个元素中一定会包含的属性有:
- height:100px;			//设置元素高100像素
- width:100px;			//设置元素宽100像素
- max-height:20%;			//设置元素最大高为模块的20%
- max-width:none;			//设置元素最大宽为默认的不限制宽度
- min-height:100px;			//设置元素最小高度为100像素
- min-width:100px;			//设置元素最小宽度为100像素

### *背景(backgroud)

其中可以设置的属性有:
- background-color: rgba(0,0,0,0.9);		//设置背景颜色为黑,透明度0.9
- background-image: url("./img/bg.jpg");		//使用同目录下img文件夹的图片做背景
- background-repeat:no-repeat;		//设置背景图片不要重复平铺网页,纯色不会有这问题因为看不出来
- background-attachment:fixed;		//设置背景图片不跟着页面滚动
- background-position:0% 0%;			//设置背景图片位置为左上

### *边框(border)

其中可以设置的属性有:
- border-top-style:dotted;		//设置边框顶为点状,top去掉为整个框
- border-width:2em;		//设置边框的宽度为2em相对长度
- border-color:#FFFFFF;		//设置边框色为白色
- border-radius:5em;			//设置圆角边框5个相对长度
- border-collapse:collapse;			//设置边框合并

### *外边(margin)

外边位置在边框的外面,其中可以设置的属性有:
- margin-top:100em;		//设置上方外边为100个相对长度,简写顺序为上右下左

> 关于边框合并现象: 只有普通文档流中块框的垂直外边距才会发生外边距合并。行内框、浮动框或绝对定位之间的外边距不会合并。

### *内边(padding)

内边位置夹在边框和元素中间,其中可以设置的属性有:
- padding-top:7em;		//设置上方内边为7个相对长度,简写顺序为上右下左
- box-sizing:border-box;		//设置固定的宽度空间

### *轮廓(outline)

轮廓可以设置的属性有:
- outline-style:solid;			//设置轮廓为实线状
- outline-color:rgba(255,0,0,0.5);			//设置轮廓颜色为红色透明度50%
- outline-width:medium;			//设置轮廓宽度为中等宽,即3像素左右
- outline-offset:25px;			//设置一个轮廓与边框间距为25像素的透明空间

>关于轮廓: 轮廓是在元素周围绘制的一条线，在边框之外，以凸显元素。

### *文字(text)

在W3中文本和文字被分开介绍,但由于他们都是对字进行操作,可一同设置;
它们的属性有:
- color:red;		//设置文字为红色
- background-color:#FF0000;		//设置文字背景为红色
- text-align:center;			//设置文字中心对齐
- direction:rtl;		//文本方向设置成从右向左,正常用不到
- text-decoration: line-through;		//设置文本装饰为中划线,一般设置none为不划线
- text-transform:uppercase;			//文本全部设置为大写
- text-indent: 20px;		//段首缩进20像素
- letter-spacing:20px;			//设置字间距为20像素
- word-spacing:20px			//设置词与词的间距为20像素,对中文无效
- line-height: 2;			//设置行与行的间距为默认的2倍
-  white-space: nowrap;			//设置元素内空白的处理方式为不换行
- text-shadow:2px 5px 7px white;			//设置文字阴影水平偏移2像素,垂直偏移5像素,模糊为7像素,为白色
- text-overflow:ellipsis;		//设置当超出元素设置范围时文本显示省略号
- font-family:sans-serif;		//设置字体格式为无衬线字体
- font-style: oblique;			//设置字体样式为斜体
- font-weight: bold;		//设置字体宽度为粗体
- font-size:2.5em;		//设置字体尺寸为2.5个相对单位,建议用em

### *列表(list)

列表可以看做单列的表格,可以设置的属性有:
- list-style-type:circle;		//设置表项标记为圆形
- list-style-image:url('/img/icon.jpg');		//设置表项标记为自定义路径的图片
- list-style-position:inside;		//设置表象标记放在列表内部

### *表格(table)

可以设置的属性有:
- caption-side:top;		//设置表格标题显示在表格上方
- empty-cells:hide;		//设置空单元为不显示
- table-layout:automatic		//设置表格布局的算法为自适应表中文字长

### *以下是用法和一些其他的东西
##### **介绍到的元素伪类

伪类是本质上是元素, 可看做某个元素的分支元素
链接的伪类有:
- a:link  {} 		//正常的，未访问的链接
- a:visited  {}			//用户访问过的链接
- a:hover  {}		//用户将鼠标悬停在链接上时
- a:active  {} 			//链接被点击时
表格的伪类有:
tr:hover {}			//鼠标悬停在表格某行上时

##### **使用层叠样式表(CSS)的方式

在头部中插入link元素并设置它属性为这样:
```html
<head>
<link rel="stylesheet" href="css/style.css">
</head>
```
rel规定当前文档与被链接文档之间的关系是层叠样式表
href指定文档的位置

##### **使用属性的方式

属性在元素内设置,例如一个段落(p)或者一个块元素(div)等
只要它是元素,都可以在.css文件中用这样的方式

```html
p 
{
	width:100px;
	border-style:dotted;
	font-family:sans-serif;
	outline-color:rgba(255,0,0,0.5);
	text-decoration: line-through;
}
div 
{
	width:100px;
	border-style:dotted;
	font-family:sans-serif;
	outline-color:rgba(255,0,0,0.5);
	text-decoration: line-through;
}
```
来设置它的样式,这样在这个元素内就会按属性写的样式显示
上面介绍的各种属性,基本在每个元素内都是通用的(能不能用试一下就知道了)
