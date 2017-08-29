# Struts2标签

- checkboxlist：`<s:checkboxlist list="authorityList" listKey="id" listValue="name" name="role.authorities.id" value="role.authorities.{id}"></s:checkboxlist>`
    - list：要遍历的集合
    - listKey：提交时要使用的值
    - listValue：要显示的值
    - name：提交的名字
    - value：默认值（勾选哪几个），是集合字符串