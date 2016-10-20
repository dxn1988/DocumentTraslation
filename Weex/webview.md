[英文原文](http://alibaba.github.io/weex/doc/modules/webview.html)

# 概述

一系列的网页操作api，例如`goBack`、`goForward`和`reload`。

# API

## goBack(ref)

**说明**

从历史堆栈里加载前一个地址。

### 参数

- `ref`_(string)_: 该web组件的引用。

**示例**

```Javascript
var webview = require('@weex-module/webview');
var webElement = this.$el('webview');
webview.goBack(webElement.ref);
```

## goForward(ref)

**说明**

从历史堆栈里加载下一个地址。

### 参数

- `ref`_(string)_: 该web组件的引用。

**示例**

```Javascript
var webview = require('@weex-module/webview');
var webElement = this.$el('webview');
webview.goForward(webElement.ref);
```

## reload(ref)

**说明**

重新加载当前页面。

### 参数

- `ref`_(string)_: 该web组件的引用。

**示例**

```Javascript
var webview = require('@weex-module/webview');
var webElement = this.$el('webview');
webview.reload(webElement.ref);
```


