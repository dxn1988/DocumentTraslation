# JS Bundle格式
JS Bundle版本: v0.3.0

## v0.5.0
## 语法和结构
JS Bundle实质上是一个符合**ES5**标准的JavaScript文件。代码里包括名字、配置和数据，用来定义某些实例和bootstrap的自定义组件。开发者可以试用任意JS代码打包器(比如webpack、browserify和requirejs)来组织全部实例代码。

## Meta信息
JS Bundle必须以像如下一样的JSON对象的一行注释来开头：
```
// { "framework": "Weex", "version": "0.5.0" }
```

JSON对象至少包含: 

 - `framework`: 属性必须是`Weex`
 - `version`: 属性必须是相应的JS Bundle格式版本

## 全局成员
- `__weex_define__(name, options)`
- `__weex_bootstrap__(name, config, data)`
- `__weex_document__`
- `__weex_require__(name)`

**`__weex_define__(name: string, options: object)`**

为当前实例定义一个带有`options`对象、名字是`name`的自定义组件。

**举例**

```
__weex_define__('rmb', {
  template: {
    type: 'div',
    style: {flexDirection: 'row'},
    children: [
      {type: 'text', attr: {value: '￥'}},
      {type: 'text', attr: {value: this.value}}
    ]
  },
  data: function () {
    return {
      value: '0'
    }
  },
  methods: {...}
})
```

启用的组件选项中包含:

- `template`: 同v0.3.0。
- `style`: 同v0.3.0。
- `data`: 返回值是一个供ViewModel观察的对象的方法。
- `methods`: 
- `computed`: 
- `init`, `created`, `ready`: 生命周期方法。
- `events`: ViewModel的事件处理器。
































