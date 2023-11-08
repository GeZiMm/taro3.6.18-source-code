## 介绍：

## 使用：

### 1. 安装
> npm install -g create-app

### 2. 用法
> create-app my-app

### 3. 单步调试
#### i. 在package.json中新增一条script，利用编辑器功能实现单步调试
```json
{
  "scripts": {
    "test": "ts-node ./src/index.ts"
  }
}
```

## 源码：
### 1. 从src/index.ts开始
```javascript
#!/usr/bin/env node

import { init } from './createApp'
import { printPkgVersion } from './util'

printPkgVersion()
init()
```
#### i. 核心函数：init()
*以在控制台输入 `create-app my-app` 为例*，捋一下create-app的主要流程：
```javascript
// 1. 通过minimist库获取命令行参数：argv = ["my-app"]
const argv = minimist(process.argv.slice(2), {
  alias: {
    typescript: ['ts'],
  },
  boolean: ['typescript'],
});

// 2. 得到以下参数：projectName、typescript、appPath
const projectName = argv._[0]; // "my-app"
const typescript = argv.typescript; // undefined
const appPath = process.cwd(); // /users/xxx/taro-next/packages/create-app

// 3. 将这些参数传入'@tarojs/cli'的Project实例中
// import { Project } from '@tarojs/cli'
const project = new Project({
  projectName,
  projectDir: appPath,
  description: undefined,
  templateSource: undefined,
  css: undefined,
  clone: undefined,
  template: undefined,
  npm: undefined,
  typescript,
});

// 4. 最后调用'@tarojs/cli'的create方法来生成项目
project.create();
```
`create-app`包的逻辑整体比较简单，就是对命令行传入的projectName做一层转发，核心是调用`@tarojs/cli`的`project.create()`来创建项目，相关源码解析详见：
[@tarojs/cli源码阅读](../../packages/taro-cli/readme.md)
