## 介绍：

`postcss-html-transform` 插件的作用是将HTML标签名选择器进行转换或转译

## 使用：

### 1. 安装

> npm install --save-dev postcss-html-transform

### 2. 用法

```javascript
const htmlTransform = require('postcss-html-transform');
plugins.push(htmlTransform({
  /** 当前编译平台 */
  platform: 'h5',
  /** 设置是否去除 cursor 相关样式 (h5默认值：true) */
  removeCursorStyle: true,
}));
```

### 3. 示例

```css
/* 当前platform: 'h5',*/
view {
  background-color: blue;
}

/* ⬇️ 转换后 ⬇️*/

taro-view-core {
  background-color: blue;
}
```

## 源码：

### 1. __test__

这个插件在项目中未提供单测，可以自行添加一个

#### 1. 创建`__test__`文件夹，并新增`index.spec.js`文件

#### 2. 新增测试用例

```javascript
'use strict'
const postcss = require('postcss')
const postcssHtmlTransform = require('../index')

describe('postcss html trans', () => {
  it('should transform HTML tags', () => {
    const css = `
    p {
      display: block;
    }
    p .test {
      display: block;
    }
  `
    const result = postcss([postcssHtmlTransform({ platform: 'weapp' })]).process(css).css

    expect(result).toBe(`.h5-p {
      display: block;
    }
    .h5-p .test {
      display: block;
    }`)
  })
})
```

### 2. 核心函数：walkRules

```javascript
'use strict'
const postcss = require('postcss')
const postcssHtmlTransform = require('../index')

describe('postcss html trans', () => {
  it('should transform HTML tags', () => {
    const css = `
    p {
      display: block;
    }
    p .test {
      display: block;
    }`
    const result = postcss([postcssHtmlTransform({ platform: 'weapp' })]).process(css).css

    expect(result).toBe(`
    .h5-p {
      display: block;
    }
    .h5-p .test {
      display: block;
    }`)
  })
})

```

#### ii. 插件功能演示

在`h5`环境下，遇到了`小程序标签`，则将其转换为taro标记

##### 1）platform === 'h5'，待编译html tag为 `div`，经插件转换后：`div`

##### 2）platform === 'h5'，待编译html为 `view`，经插件转换后：`taro-view-core`

在`小程序`环境下，遇到了`h5标签`，则将其转换为taro标记

##### 1）platform === 'weapp'，待编译html为 `view`，经插件转换后：`view`

##### 2）platform === 'weapp'，待编译html为 `div`，经插件转换后：`.h5-div`
