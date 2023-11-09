# taro3.6.18-source-code

taro3.6.18源码阅读笔记

### 1. 仓库预览

| package name                                                                                                                | use                                   |
|:----------------------------------------------------------------------------------------------------------------------------|:--------------------------------------|
| [babel-plugin-transform-react-jsx-to-rn-stylesheet](./packages/babel-plugin-transform-react-jsx-to-rn-stylesheet/readme.md) | 插件：将样式表转化成JSX的样式                      |
| [babel-plugin-transform-taroapi](./packages/babel-plugin-transform-taroapi/readme.md)                                       | 插件：将Taro API转化成各端支持的API               |
| [babel-preset-taro](./packages/babel-preset-taro/readme.md)                                                                 | 根据项目的技术栈，选择性地使用 `presets` 和 `plugins` |
| [create-app](./packages/create-app/readme.md)                                                                               | 提供bin命令：create-app，支持快捷创建taro项目       |
| [css-to-react-native](./packages/css-to-react-native/readme.md)                                                             | 库：CSS 样式转换为 React Native 样式           |
| [eslint-config-taro](./packages/eslint-config-taro/readme.md)                                                               | 阅读中...                                |
| [postcss-html-transform](./packages/postcss-html-transform/readme.md)                                                       |                                       |
| [postcss-plugin-constparse](./packages/postcss-plugin-constparse/readme.md)                                                 |                                       |
| [postcss-pxtransform](./packages/postcss-pxtransform/readme.md)                                                             |                                       |
| [postcss-unit-transform](./packages/postcss-unit-transform/readme.md)                                                       |                                       |
| [shared](./packages/shared/readme.md)                                                                                       |                                       |
| [stylelint-config-taro-rn](./packages/stylelint-config-taro-rn/readme.md)                                                   |                                       |
| [stylelint-taro-rn](./packages/stylelint-taro-rn/readme.md)                                                                 |                                       |
| [taro](./packages/taro/readme.md)                                                                                           |                                       |
| [taro-alipay](./packages/taro-alipay/readme.md)                                                                             |                                       |
| [taro-api](./packages/taro-api/readme.md)                                                                                   |                                       |
| [taro-cli](./packages/taro-cli/readme.md)                                                                                   |                                       |
| [taro-cli-convertor](./packages/taro-cli-convertor/readme.md)                                                               |                                       |
| [taro-components](./packages/taro-components/readme.md)                                                                     |                                       |
| [taro-components-advanced](./packages/taro-components-advanced/readme.md)                                                   |                                       |
| [taro-components-library-react](./packages/taro-components-library-react/readme.md)                                         |                                       |
| [taro-components-library-vue2](./packages/taro-components-library-vue2/readme.md)                                           |                                       |
| [taro-components-library-vue3](./packages/taro-components-library-vue3/readme.md)                                           |                                       |
| [taro-components-react](./packages/taro-components-react/readme.md)                                                         |                                       |
| [taro-components-rn](./packages/taro-components-rn/readme.md)                                                               |                                       |
| [taro-extend](./packages/taro-extend/readme.md)                                                                             |                                       |
| [taro-h5](./packages/taro-h5/readme.md)                                                                                     |                                       |
| [taro-helper](./packages/taro-helper/readme.md)                                                                             |                                       |
| [taro-jd](./packages/taro-jd/readme.md)                                                                                     |                                       |
| [taro-loader](./packages/taro-loader/readme.md)                                                                             |                                       |
| [taro-mini-runner](./packages/taro-mini-runner/readme.md)                                                                   |                                       |
| [taro-platform-h5](./packages/taro-platform-h5/readme.md)                                                                   |                                       |
| [taro-plugin-html](./packages/taro-plugin-html/readme.md)                                                                   |                                       |
| [taro-plugin-http](./packages/taro-plugin-http/readme.md)                                                                   |                                       |
| [taro-plugin-inject](./packages/taro-plugin-inject/readme.md)                                                               |                                       |
| [taro-plugin-mini-ci](./packages/taro-plugin-mini-ci/readme.md)                                                             |                                       |
| [taro-plugin-react](./packages/taro-plugin-react/readme.md)                                                                 |                                       |
| [taro-plugin-react-devtools](./packages/taro-plugin-react-devtools/readme.md)                                               |                                       |
| [taro-plugin-vue-devtools](./packages/taro-plugin-vue-devtools/readme.md)                                                   |                                       |
| [taro-plugin-vue2](./packages/taro-plugin-vue2/readme.md)                                                                   |                                       |
| [taro-plugin-vue3](./packages/taro-plugin-vue3/readme.md)                                                                   |                                       |
| [taro-qq](./packages/taro-qq/readme.md)                                                                                     |                                       |
| [taro-react](./packages/taro-react/readme.md)                                                                               |                                       |
| [taro-rn](./packages/taro-rn/readme.md)                                                                                     |                                       |
| [taro-rn-runner](./packages/taro-rn-runner/readme.md)                                                                       |                                       |
| [taro-rn-style-transformer](./packages/taro-rn-style-transformer/readme.md)                                                 |                                       |
| [taro-rn-supporter](./packages/taro-rn-supporter/readme.md)                                                                 |                                       |
| [taro-rn-transformer](./packages/taro-rn-transformer/readme.md)                                                             |                                       |
| [taro-router](./packages/taro-router/readme.md)                                                                             |                                       |
| [taro-router-rn](./packages/taro-router-rn/readme.md)                                                                       |                                       |
| [taro-runner-utils](./packages/taro-runner-utils/readme.md)                                                                 |                                       |
| [taro-runtime](./packages/taro-runtime/readme.md)                                                                           |                                       |
| [taro-runtime-rn](./packages/taro-runtime-rn/readme.md)                                                                     |                                       |
| [taro-service](./packages/taro-service/readme.md)                                                                           |                                       |
| [taro-swan](./packages/taro-swan/readme.md)                                                                                 |                                       |
| [taro-transformer-wx](./packages/taro-transformer-wx/readme.md)                                                             |                                       |
| [taro-tt](./packages/taro-tt/readme.md)                                                                                     |                                       |
| [taro-weapp](./packages/taro-weapp/readme.md)                                                                               |                                       |
| [taro-webpack-runner](./packages/taro-webpack-runner/readme.md)                                                             |                                       |
| [taro-webpack5-prebundle](./packages/taro-webpack5-prebundle/readme.md)                                                     |                                       |
| [taro-webpack5-runner](./packages/taro-webpack5-runner/readme.md)                                                           |                                       |
| [taro-with-weapp](./packages/taro-with-weapp/readme.md)                                                                     |                                       |
| [taroize](./packages/taroize/readme.md)                                                                                     |                                       |


