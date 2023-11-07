## 介绍：

## 使用：

### 1. 用法

```javascript
const babelPresetTaro = require("babel-preset-taro")
const config = babelPresetTaro({}, {
  framework: 'react'
})
```

### 3. 示例

```javascript
const config = babelPresetTaro({}, {
  framework: 'react'
})

// ⬇️ 转换后 ⬇️
/**
 * 使用 'unambiguous' 可以确保 Babel 不会对源代码进行任何假设，并且可以正确地处理不同类型的源代码。
 * 这对于处理多种不同类型的源代码非常有用，例如浏览器代码、小程序代码等。
 */
const config = {
  'sourceType': 'unambiguous', // `unambiguous` 是 Babel 7 中引入的一个新的源代码类型选项
  'overrides': [
    {
      'exclude': [
        {},
        {},
        {},
      ],
      'presets': [
        [
          {},
          {
            'loose': false,
            'debug': false,
            'modules': 'commonjs',
            'targets': {
              'node': 'current',
            },
            'ignoreBrowserslistConfig': true,
            'useBuiltIns': false,
          },
        ],
        [
          {},
          {
            'runtime': 'automatic',
          },
        ],
      ],
      'plugins': [
        [
          {},
          {
            'legacy': true,
          },
        ],
        [
          {},
          {
            'loose': false,
          },
        ],
        [
          {},
          {
            'regenerator': true,
            'corejs': false,
            'helpers': true,
            'useESModules': false,
            'absoluteRuntime': false,
            'version': '7.23.2',
          },
        ],
        [
          null,
        ],
        null,
      ],
    },
  ],
};
```

## 源码：

### 1. 从__test__开始

```javascript
```

#### i. 核心函数：getTransformCode

```javascript
```

#### ii. 插件功能演示

> 1）没有import css文件

```javascript
```

> 2）引入了css文件，且只有一个className

```javascript
```

> 3）引入css文件，且包含多个className

```javascript
```

### 2. 深入到源码：babel-preset-taro/index.js

```javascript
module.exports = (_, options) => {
  const presets = [], plugins = [], overrides = []; // 初始化

  // 根据框架，选择对应的preset、plugins、overrides
  if (options.framework === 'nerv') {
    presets.push(xxx)
  }
  if (options.framework === 'react') {
  }
  if (options.framework === 'vue') {
  }
  if (options.framework === 'vue3') {
  }
  if (options.ts) {
    presets.push([require('@babel/preset-typescript'), config])
  }

  return {
    sourceType: 'unambiguous', // 固定返回unambiguous
    overrides: [{
      exclude: [/@babel[/|\\\\]runtime/, /core-js/, /\bwebpack\/buildin\b/],
      presets,
      plugins,
    }, ...overrides],
  };
};
```
