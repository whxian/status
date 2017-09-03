# Ajax-XML #
##  ##
#### **XML**：是一种数据格式 ####
	例1：
		'<person>
			<name>小强</name>
			<age>23</age>
		</person>'
	例2：'{name:"小青",age:18}'
	注：如何处理像例1那样的xml的数据->ajax.responseXML（专门用来解析xml数据的）
#### XML应用实例 ####
	<button id="btn">点击</button>
	<ul id="ul">
	//处理第一种格式的数据：  
	const btn = document.getElementById('btn');
	const ul = document.getElementById('ul');
	var html = '';
	btn.addEventListener('click',click);
	function click(){
		const ajax = new XMLHttpRequest;
		ajax.open('get','../2017-8-1/php/data.xml');
		ajax.send();
		ajax.onload = function(){
			const doc = ajax.responseXML;
			const person = doc.getElementsByTagName('person');
			const persons = Array.from(person);
			persons.forEach(e=>{
				//此时的this指向的是ajax
				const name = e.getElementsByTagName('name')[0];
				console.log(name)
				const sex = e.getElementsByTagName('sex')[0];
				const age = e.getElementsByTagName('age')[0];
				const info = e.getElementsByTagName('info')[0];
				html +=`<li>我的名字叫:${name.innerHTML}, 性别:${sex.innerHTML}, 年龄:${age.innerHTML}, 此刻我想说:${info.innerHTML}</li>`;
			})
			ul.innerHTML = html;
		}
	}
#### 浏览器缓存  ####
通过浏览器的url访问的地址，只要每次访问就会把这个地址给缓存下来，以便下次访问直接就走缓存,下一次访问同一个地址的时候不会再进行HTTP请求了，大大减少了服务器的压力。有些时候后端改的东西因为有缓存机制，所以再次刷新页面的时候依然显示
之前的内容。  
**解决：**    
&emsp;&emsp;只要不是同一个地址就OK  
****
**ajax.status:**&emsp;专门用查看HTTP状态码值的。

成功的范围：status >= 200 && status < 300 || status === 304
#### 状态码小栗子：  ####
	<input type="text" id="txt" /><span id="span"></span>

	var txt = document.getElementById('txt');
	var span = document.getElementById('span');
	txt.onblur = function(){
		const ajax = new XMLHttpRequest;
		ajax.open('get','../2017-8-1/php/get.php?user='+txt.value);  //请求成功会显示
		//ajax.open('get','../2017-8-1/php/get2.php?user='+txt.value);	//请求失败走else
		ajax.send();
		ajax.onload = function(){
			if(ajax.status>=200 && ajax.status<=207 || ajax.status ==  304){
				//成功
				span.innerHTML = ajax.responseText;
			}else{
				alert('请求失败')
			}
		}
	}
注：
IE9以下的浏览器不会触发onload事件  
但在所有的浏览器都支持onreadystatechange  
//每执行一步，就会触发一次onreadystatechange事件  
此事件从发送到服务器处理完成一共有5个步骤：0-4  
readstate：监听步骤的  
0：一般监控不到  
0-3:过程  
4 :说明服务器接收并响应了完成了  

XMLHttpRequest的三个重要属性  
&emsp;&emsp;1.监听事件  
&emsp;&emsp;&emsp;onreadystatechange  
&emsp;&emsp;2.执行步骤  
&emsp;&emsp;&emsp;ajax.readState  
&emsp;&emsp;3.状态码  
&emsp;&emsp;&emsp;ajax.status  
注：onreadystatechange放在send前面和后面的区别：  
	放在send前边能多监听一步，然而并没有什么卵用，只需要4就好  
#### onreadstatuschange-状态测试  ####
	<input type="text" id="txt" /><span id="span"></span>
		var txt = document.getElementById('txt');
		var span = document.getElementById('span');
		txt.onblur = function(){
			const ajax = new XMLHttpRequest;
			ajax.open('get','../2017-8-1/php/get.php?user='+txt.value);  //请求成功会显示
			//ajax.open('get','../2017-8-1/php/get2.php?user='+txt.value);	//请求失败走else
			ajax.send();
			ajax.onload = function(){
				if(ajax.status>=200 && ajax.status<=207 || ajax.status ==  304){
					//成功
					span.innerHTML = ajax.responseText;
				}else{
					alert('请求失败')
				}
			}
		}
同步(sync)：  
&emsp;&emsp;&emsp;代码要一步一步执行，下一句代码**始终要等待上一句代码执行完成**才会执行  
异步(async)：   
&emsp;&emsp;&emsp;代码也是一步一步执行，但是下一段代码**不用等待上一句代码执行完**就可以执行   
举个例子：同步->打电话模式  
&emsp;&emsp;&emsp;&emsp;&emsp;异步->发短信模式
#### 对象(json)与 字符串的互相转换 ####
	//对象转字符串
	/*var obj = {
			user:'leo',
			pass:12345
		}
	
	function objTostring(obj){
		var arr = [];
		for(attr in obj){
			arr.push(attr+'='+obj[attr])
		}
		return arr.join('&')
	}
	
	console.log(objTostring(obj))*/


	//字符串转对象：
	var str ='user=leo&pass=12345';
	function stringToobj(str){
		var arr = str.split('&');
		var obj = {};
		for(var i =0;i<arr.length;i++){
			var arr2 = arr[i].split('=');
			obj[arr2[0]] = arr2[1];
		}
		return obj
	}
	console.log(stringToobj(str))
#### 自己封的ajax ####
	function ajax(json){
	//配置默认参数
	var settings = {
		url:'',
		method:'get',
		dataType:'string',
		data:{},
		success:function(){},
		error:function(){}
	}
	//有配置走配置，没配置走默认
	Object.assign(settings,json);
	//创建一个ajax对象
	var ajax = new XMLHttpRequest;
	//判断data传入的数据,转成可以传入url的数据
	/*
	 obj = {
					user:leo,
					pass:12345
				}
	user=leo&pass=12345
	 */
	var arr= [];
	for(attr in settings.data){
		arr.push(attr+'='+settings.data[attr])
	}
	settings.data = arr.join('&');
	//判断传入方式是get还是post,并设置对应请求地址，以及传输方式
	if(settings.method.toLowerCase() == 'get'){
		ajax.open('get',settings.url+'?'+settings.data);
		ajax.send();
	}else if(settings.method.toLowerCase() == 'post'){
		ajax.open('post',settings.url);
		ajax.setRequestHeader('Content-Type','application/x-www-form-urlencoded');
		ajax.send(settings.data);
	}else{
		alert('nonono')
	}
	//等待响应
	ajax.onreadystatechange = function(){
		if(ajax.readyState == 4){
			if(ajax.status >=200 && ajax.status<=207 || ajax.status == 304){
				//通话->判断dataType
				if(settings.dataType=='string'){
					settings.success(ajax.responseText)
				}else if(settings.dataType == 'json'){
					settings.success(JSON.parse(ajax.responseText));
				}else if(settings.dataType == 'xml'){
					settings.success(ajax.responseXML)
				}else{
					settings.success('请核对信息');
				}
			}else{
				settings.success({state:ajax.readyState,status:ajax.status})
			}
		}
	}
