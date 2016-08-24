- [API Blueprint](https://github.com/apiaryio/api-blueprint/blob/master/API%20Blueprint%20Specification.md#def-api-name-section)文档
- [aglio](https://github.com/danielgtaylor/aglio)主页


demo 示例

```
### Demo

FORMAT: 1A
HOST: http://demo.xxx.com

# api 文档

# Group 通用模块

## Api测试 [/api/test]

### Api测试 [GET]
api描述

+ `param1`: (required,string) - param1 desc
+ `param2`: (required,number) - param2 desc

+ Response 200
```js
{
    "result": 0
}
``````


`aglio -i input.md -s` 实时预览

`aglio -i input.md -o output.html` 输出到html

`aglio -i input.md --theme-full-width -s` 占满屏幕