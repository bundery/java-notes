# response&request中文乱码问题

## response

### OutputStream
`response.getOutputStream().write("你好".getBytes("utf-8"));`

1. 因为是字节流，所以只能接收字符串按照指定码表解码后的二进制数据
2. 中文数据按照上述指定的utf-8码表编码成二进制数据写入到response缓存
3. 控制浏览器以UTF-8码表打开：response.setHeader("Content-type","text/html;charset=UTF-8");

### Writer
`response.getWriter().write("中国");`

1. writer是字符流，可以直接接收字符数据。
2. 字符数据在response缓存中默认按'iso-8859'编码发送给浏览器，所以要通过response.setCharacterEncoding("utf-8")；来控制response缓存以什么码表编码字符串然后发送给浏览器。
3. 然后控制浏览器以UTF-8码表打开：response.setHeader("Content-type","text/html;charset=UTF-8");

> `response.setContentType("text/html;charset=utf-8");`会执行上述的2和3步骤，所以只需这句代码就可以解决writer的乱码问题了。

### 下载
如果下载的文件是中文文件名，则文件名需要经过url编码
`response.setHeader("content-disposition","attachment;fileName="+URLEncoder.encode(filename,"UTF-8"));`

## request中文乱码问题
request接收中文参数：

1. 客户机发送数据：`http://xxx.html?userName=中国`
    - 默认会按照html页面的码表来编码"中国"，然后发送给服务器
2. 服务器接收数据：`String str=request.getParameter("userName");`
    - 发送的数据解码成字符串，默认会按照'iso-8859'来解码数据，所以会乱码
    - 通过`resquest.setCharacterEncoding('utf-8');`可以设置request以'utf-8'码表解码数据（这句代码只对`Post`提交有效）
    - Get提交只能通过`String str2=new String(str.getBytes('iso-8859'),"utf-8");`
