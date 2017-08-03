# readystatechange #

>IE9以下不支持onload，而所有浏览器都支持onreadystatechange
>（readystatechange：监控当前服务器响应到了第几步）
>
**XMLHttpRequest对象的三个重要属性：**

		1.	监听事件
				onreadystatechange
				放在send前面和后边的区别：能多监听一步，但是意义不大，只有第四步才算请求响应完毕
		2.	执行的步骤
				ajax.readystate
		3.	状态码
				ajax.status
				
**当从发送到服务器处理完成一共有5个步骤，每个步骤都是用数字来表示**		

-	0：请求未初始化（监控不到）
-	1：服务器连接已建立
-	2：请求已接收
-	3：请求处理中
-	4：请求已完成，且响应已就绪（说明服务器已接收并响应完成）
