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
div {
  background-color: blue;
}

/* ⬇️ 转换后 ⬇️*/

taro-div-core {
  background-color: blue;
}
```

## 源码：

### 1. 核心函数：walkRules

```javascript
const postcssHtmlTransform = (options: IOptions = {}) => {
  switch (options.platform) { /* 当前编译平台 */
    case 'h5': {
      selectorFilter = tags2Rgx(miniAppTags); // miniAppTags 小程序相关标签：'cover-image', 'cover-view', 'match-media', 'movable-area'...
      walkRules = (rule: Rule) => {
        rule.selector = rule.selector.replace(selectorFilter, '$1taro-$2-core');
      };
      break;
    }
    case 'rn': {
      break;
    }
    case 'quickapp': {
      break;
    }
    default: {
      const selector = tags2Rgx(htmlTags); // htmlTags 相关标签：'html', 'body', 'a', 'audio', 'button', 'canvas'...
      walkRules = (rule: Rule) => {
        if (/(^| )\*(?![=/*])/.test(rule.selector)) {
          rule.remove();
          return;
        }
        rule.selector = rule.selector.replace(selector, '$1.h5-$2');
      };
    }
  }
};
```

#### ii. 插件功能演示

在`h5`环境下，遇到了`小程序标签`，则将其转换为taro标记

##### 1）platform === 'h5'，待编译html为 `<div></div>`，经插件转换后：`<div></div>`

##### 2）platform === 'h5'，待编译html为 `<view></view>`，经插件转换后：`<taro-view-core></taro-view-core>`

在`小程序`环境下，遇到了`h5标签`，则将其转换为taro标记

##### 1）platform === 'weapp'，待编译html为 `<view></view>`，经插件转换后：`<view></view>`

##### 2）platform === 'weapp'，待编译html为 `<div></div>`，经插件转换后：`<.h5-div></.h5-div>`
