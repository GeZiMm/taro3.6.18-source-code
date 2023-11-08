## 介绍：

是一个 Babel 预设插件，用于转换 Taro 框架的代码。
具体来说，`babel-preset-taro` 插件提供了一组 Babel 插件和配置，
用于将 Taro 框架的代码转换为可以在不同环境中运行的代码。
它包括了一些常用的 Babel
插件，例如 `babel-plugin-transform-taroapi`、`babel-plugin-transform-class-properties`、`babel-plugin-transform-decorators-legacy`
等，用于处理 Taro 框架的特定语法和语义。

使用 `babel-preset-taro` 插件可以帮助开发者将使用 Taro
框架编写的代码转换为可以在不同环境中运行的代码，例如浏览器、小程序等。这样可以确保代码的兼容性和可移植性，并且可以在不同的环境中运行。

## 使用：

### 1. 用法

```javascript
const babelPresetTaro = require("babel-preset-taro")
const config = babelPresetTaro({}, {
  framework: 'react'
})
```

### 2. 示例

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

根据框架的不同，导出适配对应框架的babel presets

```javascript
module.exports = (_, options) => {
  const presets = [], plugins = [], overrides = []; // 初始化

  // 根据框架，选择对应的preset、plugins、overrides
  if (options.framework === 'nerv') {
    presets.push(xxx)
  }
  if (options.framework === 'react') {
    // push(xxx)
  }
  if (options.framework === 'vue') {
    // push(xxx)
  }
  if (options.framework === 'vue3') {
    // push(xxx)
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
