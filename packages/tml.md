## 介绍：

## 使用：

### 1. 安装
> npm install --save-dev babel-plugin-transform-react-jsx-to-rn-stylesheet

### 2. 用法
```javascript
```
### 3. 示例
```javascript
// ⬇️ 转换后 ⬇️
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

可以看到，插件的核心功能就是将JSX上的className，以及style结合引入的stylesheet文件做一个结合，并输出修改后的AST

### 2. 深入到源码：src/index.ts
其实整个ts文件，就是为了提供了一个插件给babel：jSXStylePlugin
```javascript
```
#### i. 具体看看这个插件做了啥：
```javascript
```
#### ii. 具体分析JSXOpeningElement处理函数
```javascript
```
