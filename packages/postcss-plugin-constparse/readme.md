## 介绍：

在 H5 环境中 `tabbar` 的高度固定在 50px。

## 使用：

### 1. 安装

> npm install --save-dev postcss-plugin-constparse

### 2. 用法

```javascript
const postcssPluginConstparse = require('postcss-plugin-constparse');
plugins.push(postcssPluginConstparse({ platform: 'h5' }));
```

### 3. 示例

这个插件的源码也相当简单，就是在后的情况下，将`taro-tabbar-height`设置为`50PX`

```javascript
module.exports = (opts = {}) => {
  opts = Object.assign({
    constants: [{
      key: 'taro-tabbar-height',
      val: '50PX'
    }]
  }, opts)

  return {
    postcssPlugin: 'postcss-plugin-constparse',
    Declaration(decl) {
      if (opts.platform === PLATFORM.WEAPP) return
      let value = decl.value
      opts.constants.forEach(item => {
        value = value.replace(new RegExp(item.key, 'g'), item.val)
      })
      decl.value = value
    }
  }
}
```
