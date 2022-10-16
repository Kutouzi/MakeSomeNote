## web笔记-->jQuery总结

### *前言
jquery可理解为别人帮写好的JS脚本,就只管用就行
基本用来做些简单的淡出淡入特效,动画等,以及一些其他功能

------
### *用法
- 一. 怎么调用jquery函数库?类似这样用

```html
<script src="jq/jquery.js"></script>
```
需要去网上下载jquery.js,放到网站某个目录下然后引用

- 二.怎么用里面的函数?类似这样用
```JavaScript
/*$(选择的元素).要用的jq函数(参数)*/
$("div").fadeToggle(slow);
```

------
### *特效
做特效的函数有以下
~~二三可以不看,和一类比使用就行~~

- ##### 一. 显示/隐藏

```JavaScript
$("div").hide(300);
/*↑这函数作用是隐藏块中元素,参数是3000ms延迟*/
$("div").show(slow);
/*↑显示,参数是较慢的延迟*/
$("div").toggle(fast);
/*↑这函数同时有第一和第二个的功能,已显示了就隐藏,反之亦然,参数是较快的延迟*/
```
- ##### 二. 淡出淡入
```JavaScript
$("div").fadeIn(300);
/*↑淡入,参数是300ms延迟*/
$("div").fadeOut(3000);
/*↑淡出*/
$("div").fadeToggle(300);
/*↑同时有前两个的功能*/
```
- ##### 三. 滑动
```JavaScript
$("div").slideDown(300);
/*↑向下滑动,参数是300ms延迟*/
$("div").slideUp(300);
/*↑向上滑动*/
$("div").slideToggle(300);
/*↑同时有前两个的功能*/
```
- ##### 四. 动画
```JavaScript
/*$(选择的元素).animate({CSS属性集},延迟,事件结束回调函数);*/
$("div").animate({
		fontSize:"3em",
		height:'50px',
		width:'+=50px'
	},300);
/*↑将div块中元素的文字变成3个相对单位,长变成50像素,宽比原来多50像素,延时是300ms*/
```
<font size=2>并且你可以创建一个动画队,它会按列顺序执行这些动画</font>

```JavaScript
/*先创建一个变量把选择器("$()"这个叫选择器)赋给它,然后按顺序排就行*/
$("button").click(function(){
  var div=$("div");
  div.animate({height:'300px',opacity:'0.4'},"slow");
  div.animate({width:'300px',opacity:'0.8'},"slow");
  div.animate({height:'100px',opacity:'0.4'},"slow");
});
/*↑鼠标点击按钮时,按顺序执行从上至下的动画*/
```
- ##### 五. 停止
```JavaScript
/*$(选择的元素).stop(停止所有队列,直接完成动画);*/
$("div").stop();
/*↑停止div块中元素当前动画,如果有队列则队列会继续*/
$("div").stop(true);
/*↑停止div块中元素所有动画*/
$("div").stop(true,true);
/*↑停止div块中元素所有动画,并且强制动画直接完成*/
```
- ##### 六. 回调函数
<font size=2>在一二三四中的函数其实还有个回调函数的参数</font>
<font size=2>作用是会在动画完成后调用一个函数</font>
```JavaScript
$("p").hide(1000,function(){alert(" ");});
/*↑在元素p完全隐藏后会弹出提示框*/
```
- ##### 七. 动画链
```JavaScript
/*用点连起来就行*/
$("p").css("color","red").slideUp(2000).slideDown(2000);
/*↑p元素首先会变为红色，然后向上滑动，然后向下滑动*/
```

------

### *操作html

jquery除了可以做特效,它还可以操作html

- ##### 一. 增删查改元素和属性
这样使用可以获取对象元素的一些东西
查:
```JavaScript
/*获取它的文本内容*/
$("p").text();
/*获取这段html代码*/
$("p").html();
/*获取输入的值(test是个可输入内容的文本框)*/
$("test").val();
/*获取这元素的属性里href属性的值*/
$("a").attr("href")
```

