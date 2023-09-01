<h1 align="center">vue-select-page</h1>

## 说明
>下拉框分页使用请求后数据
>使用远程分页搜做

##功能说明

**filterable**<br/>
是否需要搜索
类型:`Boolean`

**isPage**<br/>
是否需要分页
类型:`Boolean`

**pageSize**<br/>
每页显示多少条，默认10条
类型:`Number`

**radio**<br/>
是否单选
类型:`Boolean`

**clickClose**<br/>
是否点击后关闭
类型:`Boolean`

**prop**<br/>
显示名字以及取值的参数名
类型:`Object`<br/>
默认: `{
    name: 'name',
    value: 'value'
}`

**remote**<br/>
是否支持远程调用
类型:`Boolean`

**remoteMethod**<br/>
远程方法调用
类型:`Function`
参数:`(keyword, callback, page)`


##安装说明
```bash
$ npm install --save vue-select-pages
```