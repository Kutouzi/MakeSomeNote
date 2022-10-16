## web笔记-->ajax

### *ajax是干什么
ajax用来更新局部网页，并且发送的是异步请求，在服务器响应的那一段时间可以操作网页（同步请求则在服务器响应那段时间不能对网页操作）

------
### *jQuery中用法
方法一：
```javascript
$.ajax({
	url:"AServlet", //请求的路径
	type:"POST", //请求的方式
	data:{"数据a":"内容a","数据b":"内容b"}, //请求的参数
	success:function(data){ //data一般用来接收服务器传回来的东西
	 alert(data); //服务器响应成功，数据传回来后要干的事，即回调函数
	},
	error:function(){
	 alert("error"); //服务器响应失败要干的事，也是回调函数
	},
	dataType:"json" //设置接收的返回的数据格式
})
```
方法二：
```javascript
/*和上面差不多*/
$.get("AServlet",{"数据a":"内容a","数据b":"内容b"},function(data){
alert(data);},"json")
```
方法三：
```javascript
/*和上面基本一样，变成post请求而已*/
$.post("AServlet",{"数据a":"内容a","数据b":"内容b"},function(data){
alert(data);},"json")
```