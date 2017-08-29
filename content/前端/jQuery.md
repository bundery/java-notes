# jQuery

> jQuery是为处理Html事件而特别设计的。使用原则：应当把所有的jQuery代码置于事件处理函数，把所有事件处理函数置于文档就绪事件处理器中。

## 函数：
由于Javascript语句是逐一执行的，按照次序没红花之后的语句可能会产生错误或页面冲突，因为动画还没有完成，为了解决这个问题，就可以以参数的形式添加Callback函数，它是在动画100%完成后，才调用Callback函数。

### 函数事件
- ${document}.ready(function)：将函数绑定到文档的就绪事件（当页面DOM完成加载执行。在html的头部的script标签中的，不处于ready()中的JS代码将早于ready()执行。）jQuery使用document.ready来保证所要执行的代码是在DOM元素被加载完成的情况下执行。可以在同一个页面中无限次地使用$(document).ready()事件。其中注册的函数会按照（代码中的）先后顺序依次执行。
- $(document).load()：当web页面以及其附带的资源文件，如CSS，Scripts，图片等，加载完毕后执行此方法。常用于检测页面（及其附带资源）是否加载完毕。onload()的方法是在页面加载完成后才发生，这包括DOM元素和其他页面元素（例如图片）的加载，因此，使用document.ready()方法的执行速度比onload()的方法要快。
- $(document).unload()：
此事件在停止浏览页面的时候触发，此操作可能存在于刷新操作/F5，单击上一页按钮，进入其他页面或关闭整个tab或窗口。
- $(selector).click(function)：触发或将函数绑定到被选元素的点击事件
- $(selector).dbclick(function)：触发或将函数绑定到被选元素的双击事件
- $(selector).focus(function)：触发或将函数绑定到被选元素的获取焦点事件
- $(selector).mouseover(function)：触发或将函数绑定到被选元素的悬停事件

### 隐藏显示
- ${selector}.hide(speed,callback);：隐藏元素
- ${selector}.show(speed,callback);：显示元素
- ${selector}.toggle(speed,callback);：显示被隐藏的元素，并隐藏已显示的元素

### 淡入淡出
- ${selector}.fadeIn(speed,callback);：淡入已隐藏元素
- ${selector}.fadeOut(speed,callback);：淡出已显示元素
- ${selector}.fadeToggle(speed,callback);：淡入已隐藏元素或淡出已显示元素
- ${selector}.fadeTo(speed,opacity,callback);：渐变为不透明度(0与1之间)

### 滑动
- $(selector).slideDown(speed,callback);：向下滑动元素
- $(selector).slideUp(speed,callback);：向上滑动元素
- $(selector).slideToggle(speed,callback);：向上向下滑动元素

### 动画
- $(selector).animate({params},speed,callback);：创建自定义动画，params用于定义形成动画的CSS属性
- $(selector).stop(stopAll,goToEnd);：停止正在执行的动画，都是可选的参数

### Chaining
可在相同的元素上运行多条jQuery命令。
- $("#p1").css("color","red").slideUp(2000).slideDown(2000);：先变红再向上滑动，再向下滑动。

## jQuery DOM
- text() ： 设置或返回所选元素的文本内容
- html() ： 设置或返回所选元素的内容（包括 HTML 标记）
- val()  ： 设置或返回表单字段的值
- attr() ： 设置或返回属性值
以上函数都有回调函数，回调函数有俩个参数，被选元素列表中当前元素的下标，以及旧值，然后新值返回希望使用的字符串：

```javascript
$("#btn1").click(function(){
  $("#test1").text(function(i,origText){
    return "Old text: " + origText + " New text: Hello world!
    (index: " + i + ")";
  });
});

$("#btn2").click(function(){
  $("#test2").html(function(i,origText){
    return "Old html: " + origText + " New html: Hello <b>world!</b>
    (index: " + i + ")";
  });
});
```

- $("p").append()  ：在被选元素的结尾插入内容
- $("p").prepend() ：在被选元素的开头插入内容
- $("p").after()   ：在被选元素之后插入内容
- $("p").before()  ：在被选元素之前插入内容

```javascript
    var txt=$("<p></p>").text("Text.");//创建新元素
    $("p").append(txt); //追加新元素
```

- $("#div").remove() ：删除被选元素及其子元素，可以传递参数过滤被删除元素
- $("#div").empty()  ：删除被选元素的子元素

### 操作CSS
- addClass() ：向被选元素添加一个或多个class属性
- removeClass() ：向被选元素移除一个或多个属性
- toggleClass() ：对被选元素进行添加/移除class的切换操作
- css() ：设置或者返回被选元素的一个或者多个样式属性eg:`$("p").css("background-color");`设置指定的CSS属性：`css("background-color","yellow");`

## 遍历
> jQuery遍历，意为“移动”，用于根据其相对于其他元素的关系来“查找”（或选取）HTML元素。以某项选择开始，并沿着这个选择移动，直到抵达期望的元素为止。通过 jQuery 遍历，能够从被选（当前的）元素开始，在家族树中向上移动（祖先），向下移动（子孙），水平移动（同胞）。这种移动被称为对 DOM 进行遍历。遍历方法中最大的种类是树遍历（tree-traversal）。

![DOM图](http://www.w3school.com.cn/i/dom_tree.gif "Dom图")

### 向上遍历父元素
- $("span").parent(); ：返回每个span元素的直接父元素
- $("span").parents(); ：返回每个元素的所有父元素，直到根元素html(包括)，可以传参数过滤父元素`$("span").praents("ul");：`返回所有是ul元素的父元素
- $("span").parentsUntil(); ：返回介于俩个给定元素之间的所有元素，eg:`$("span").parentsUntil("div");`：返回span与div之间的所有父元素

### 向下遍历子元素
- $("div").children(); ：返回被选元素的所有直接子元素，可以传参数过滤
- $("div").find("span"); ：返回所有属于div后代的所有span元素

### 同胞遍历
拥有相同父元素的元素叫做同胞。

- $("h2").siblings();：返回被选元素的所有同胞元素，可以传参数过滤
- $("h2").next();：返回被选元素的下一个同胞元素，只返回一个同胞元素
- $("h2").nextAll();：返回被选元素的下面的所有同胞元素
- $("h2").nextUntil("h6");：返回被选元素下面与给定元素之间的所有同级元素
- prev(),prevAll(),prevUntil();：与上面类似，不过方向相反，向上遍历
- first()：返回匹配元素的首个元素
- last()：返回匹配元素的最后个元素
- eg(index)：按照索引匹配元素，索引从0开始
- $("p").filter(".intro");：返回所有类名带intro的所有p元素
- $("p").not(".intro");：返回所有类名不带intro的所有p元素

## 操作AJAX
jQuery load() 方法是简单但强大的 AJAX 方法，load()方法从服务器加载数据，并把返回的数据放入被选元素中。

- $(selector).load(URL,data,callback);：URL参数必需，指定希望加载的URL。

## GET&POST
- GET 从指定的资源请求数据，基本用于从服务器取回数据，可能会返回缓存数据。
`$.get(URL,callback);`，URL参数必须，指定希望加载的URL，callback是请求成功后所执行的函数名。

```javascript
$("button").click(function(){
  $.get("demo_test.asp",function(data,status){
    alert("Data: " + data + "\nStatus: " + status);
  });
});
```
$.get() 的第一个参数是我们希望请求的 URL（"demo_test.asp"），第二个参数是回调函数。第一个回调参数存有被请求页面的内容，第二个回调参数存有请求的状态。

- POST 向指定的资源提交要处理的数据，也可用于从服务器获取数据，不过不会缓存数据，且常用于连同请求一起发送数据。
`$.post(URL,data,callback);`URL参数必须，指定希望请求的URL；data参数可选，指定连同请求发送的数据；callback可选，是请求成功后所执行的函数名。

```javascript
$("button").click(function(){
  $.post("demo_test_post.asp",
  {
    name:"Donald Duck",
    city:"Duckburg"
  },
  function(data,status){
    alert("Data: " + data + "\nStatus: " + status);
  });
});
JSP文件：
<%
dim fname,city
fname=Request.Form("name")
city=Request.Form("city")
Response.Write("Dear " & fname & ". ")
Response.Write("Hope you live well in " & city & ".")
%>
```

$.post() 的第一个参数是我们希望请求的 URL ("demo_test_post.asp")。然后我们连同请求（name 和 city）一起发送数据。"demo_test_post.asp" 中的 ASP 脚本读取这些参数，对它们进行处理，然后返回结果。第三个参数是回调函数。