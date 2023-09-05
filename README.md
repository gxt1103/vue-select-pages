<h1 align="center">vue-select-page</h1>

## 说明
>下拉框分页使用请求后数据<br>
>使用远程分页搜索

## 效果展示
![单选](http://img6.lwhs.me/mail/common/radio.png)
![多选](http://img6.lwhs.me/mail/common/checkbox.png)

## 功能说明

|参数|描述|默认值|类型|回调参数|
|:---|---|---|---|---|
| `filterable`|是否需支持搜索|`true`|`Boolean`| `-` |
|`isPage`|是否需要分页|`false`|`Boolean`| `-` |
|`pageSize`|每页显示多少条|`10`|`Number`| `-` |
|`radio`|是否单选|`false`|`Boolean`| `-` |
|`clickClose`|是否点击后关闭|`false`| `Boolean` | `-` |
|`prop`| 显示名字以及取值的参数名 |`{ name: 'name', value: 'value' }`| `Object` | `-` |
|`remote`| 是否支持远程调用 |`false`| `Boolean` | `-` |
|`remote-method`| 远程搜索方法 | ` ` | `function` | `(keyword, callback, page)` |
|`initValue`| 初始化选中的数据,值为定义的prop.value取得值,需要在data中存在 |` `| `Array` | `-` |
|`theme`| 主题配置 |` `| `String` | `-` |
|`styles`| 自定义样式 |` `| `Object` | `-` |
| `selectChange`|回调方法|` `|`function`| `(data)` |
| `getSelect`|内置方法,选中的值|` `|`function`| `(data)` |
| `getSelectArray`|内置方法,选中的对象|` `|`function`| `(data)` |
| `clearSelect`|内置方法,清空选中的内容|` `|`function`| ` ` |

## 安装说明
```bash
$ npm install --save vue-select-pages
```

## 引用说明
```bash
$ import selectPage from 'vue-select-pages'
```