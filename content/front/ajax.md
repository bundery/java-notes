# AJAX
AJAX = 异步 JavaScript 和 XML（Asynchronous JavaScript and XML）。

> 在不重载整个网页的情况下，AJAX通过后台加载数据，并在网页上进行显示。通过 jQuery AJAX 方法，能够使用 HTTP Get 和 HTTP Post  从远程服务器上请求文本、HTML、XML 或 JSON - 同时能够把这些外部数据直接载入网页的被选元素中。
> 通过AJAX，JavaScript无需等待服务器的响应，而是：
> - 在等待服务器响应时执行其他脚本
> - 当响应就绪后对相应进行处理

## XMLHttpRequest
XMLHttpRequest对象是AJAX的基础。
创建对象 `var variable=new XMLHttpRequest();`

### 服务器请求
- open(method,url,async)：
    - method:（get/post）规定请求的类型
    - URL:(文件在服务器上的位置)以及是否异步处理请求
    - async:true(异步)/false(同步，这种情况下会等到服务器响应就绪才继续执行，如果服务器繁忙或缓慢，应用程序会挂起或停止。)
- send(string)将将请求发送到服务器。string:仅用于POST请求

```javascript
//向服务器发送数据
//url参数是服务器上文件的地址，该文件可以回任何类型的文件，如.txt和xml或者服务器脚本文件，如.asp和.php（在传回响应之前，能够在服务器上执行任务。）
//如果要XMLHttpRequest对象可用于AJAX，async的参数必须设置为true。
xmlhttp.open("GET","test.txt",true);
xmlhttp.send();
```

与POST相比，GET更简单也更快，并且在大部分情况下都能用。
然而，在下列情况下，要使用POST请求：

- 无法使用缓存文件（更新服务器上的文件或者数据库）
- 向服务器发送大量数据（POST没有数据量限制）
- 发送包含未知字符的用户输入时，POST比GET更稳定可靠

使用GET请求可能得到的是缓存的结果。为了避免这种情况，想URL添加一个唯一的ID:

```javascript
xmlhttp.open("GET","demo_get.asp?t="+Math.random(),true);
xmlhttp.send();
```

通过GET请求发送信息：

```javascript
xmlhttp.open("GET","demo_get2.asp?fname=Bill&lname=Gates",true);
xmlhttp.send();
```

如需要像 HTML 表单那样 POST 数据，使用 setRequestHeader() 来添加 HTTP 头。然后在 send() 方法中指定要发送的数据：

```javascript
xmlhttp.open("POST","ajax_test.asp",true);
//向请求添加HTTP头
xmlhttp.setRequestHeader("Content-type","application/x-www-form-urlencoded");
xmlhttp.send("fname=Bill&lname=Gates");
```

### 服务器响应
如果要获得来自服务器的响应，要使用XMLHttpRequest对象的responseText或者responseXML属性：

- responseText：获得字符串形式的响应数据。如果服务器的响应并非XML，就使用responseText属性，它以字符串形式返回，eg:`document.getElementById("myDiv").innerHTML=xmlhttp.responseText;`
- responseXML：获得XML形式的响应数据。如果来自服务器的响应是XML，而且需要作为XML对象进行解析，就使用responseXML属性

### onreadystatechange事件
>每当readyState改变时，就会触发onreadystatechange事件（也就是说该事件会触发5次，每次对应着readyState的每个变化），readyState属性存有XMLHttpRequest的状态信息。

XMLHttpRequest对象的三个重要属性：

- onreadystatechange :存储函数（或函数名），每当readyState属性改变时，就会调用该函数
- 存有XMLHttpRequest的状态，从0到4发生变化。
    - `0`：请求未初始化
    - `1`：服务器连接已建立
    - `2`：请求已接收
    - `3`：请求处理中
    - `4`：请求已完成，且响应已就绪
- status
    - `200`:"OK"
    - '404':未找到页面

eg:指定当readyState等于4且状态为200时，所执行的函数：

```javascript
xmlhttp.onreadystatementchange=function(){
    if(xmlHttp.readyState==4&&xmlhttp.status==200){
        document.getElementById("myDiv").innerHTML=xmlhttp.responseText;
    }
}
```
