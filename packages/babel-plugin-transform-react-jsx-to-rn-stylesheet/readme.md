## 介绍：
是一个 Babel 插件，它的作用是将 React JSX 中的样式转换为 React Native 的样式表。
它可以帮助你在 React Native 中使用 JSX 编写样式，并将其转换为相应的 React Native 样式表。

这个插件通常在 React Native 项目中使用，它可以帮助你在 React Native 项目中使用 JSX 编写样式，并将其转换为兼容的 React Native 样式表。

你可以在 .babelrc 或 babel.config.js 文件中找到它的配置。

简单理解就是：
将 className={'header'} 转为 style={_styleSheet.header}

## 使用：

### 1. 安装
> npm install --save-dev babel-plugin-transform-react-jsx-to-rn-stylesheet

### 2. 用法
```yml
// .babelrc
{
  "plugins": ["transform-react-jsx-to-rn-stylesheet", {
      enableCSSModule: false,
      /* 是否开启多类名转换 */
      enableMultipleClassName: false,
    }]
}
```
### 3. 示例
```javascript
import { Component } from 'react';
import './app.css';
class App extends Component {
  render() {
    return <div className="header" />
  }
}
```
⬇️ 转换后
```javascript
import { Component } from 'react';
import appCssStyleSheet from './app.css';

var _styleSheet = appCssStyleSheet;

class App extends Component {
  render() {
    return <div style={_styleSheet.header} />;
  }
}
```

## 源码：
### 1. 从__test__开始
```js
// 可以看到有一个主要依赖一个核心函数完成转换：getTransformCode
it('转换只有一个className的元素', () => {
  expect(getTransformCode(`
import { createElement, Component } from 'rax';
import './app.css';

class App extends Component {
  render() {
    return <div className="header" />;
  }
}`)).toMatchSnapshot()
})
```
#### i. 核心函数：getTransformCode
通过babel的transform方法，获取转换后的code，**jSXStylePlugin**就是```babel-plugin-transform-react-jsx-to-rn-stylesheet```导出的插件
```javascript
const code = transform(source, {
  plugins: [[jSXStylePlugin, { enableCSSModule, enableMultipleClassName }], syntaxJSX],
  configFile: false
}).code
```
#### ii. 插件功能演示
> 1）没有import css文件
```javascript
// 未引入样式表，比如index.css或者index.less这种
import { createElement, Component } from 'rax';

class App extends Component {
  render() {
    return <div className="header" />;
  }
}

// ⬇️ 转换后，不会有变化 ⬇️

import { createElement, Component } from 'rax';
class App extends Component {
  render() {
    return <div className="header" />;
  }
}
```

> 2）引入了css文件，且只有一个className
```javascript
import { createElement, Component } from 'rax';
import './app.css';

class App extends Component {
  render() {
    return <div className="header" />;
  }
}

// ⬇️ 转换后，会解析出样式表对象，并赋值到jsx的style属性上 ⬇️

import { createElement, Component } from 'rax';
import _appCssStyleSheet from "./app.css";
var _styleSheet = _appCssStyleSheet;
class App extends Component {
  render() {
    return <div style={_styleSheet["header"]} />;
  }
}
```

> 3）引入css文件，且包含多个className
```javascript
import { createElement, Component } from 'rax';
import './app.css';

class App extends Component {
  render() {
    return <div className="header1 header2" />;
  }
}

// ⬇️ 转换后，会解析出样式表对象，合并后，赋值到jsx的style属性上 ⬇️

import { createElement, Component } from 'rax';
import _appCssStyleSheet from "./app.css";
function _mergeEleStyles() {
  return [].concat.apply([], arguments).reduce((pre, cur) => Object.assign(pre, cur), {});
}
var _styleSheet = _appCssStyleSheet;
class App extends Component {
  render() {
    return <div style={_mergeEleStyles(_styleSheet["header1"], _styleSheet["header2"])} />;
  }
}
```

可以看到，插件的核心功能就是将JSX上的className，以及style结合引入的stylesheet文件做一个结合，并输出修改后的AST

### 2. 深入到源码：src/index.ts
其实整个ts文件，就是为了提供了一个插件给babel：jSXStylePlugin
```javascript
import { transform } from '@babel/core'
import jSXStylePlugin from '../src/index'

const code = transform(source, {
  plugins: [[jSXStylePlugin, { enableCSSModule, enableMultipleClassName }], syntaxJSX],
  configFile: false
}).code
```
#### i. 具体看看这个插件做了啥：
babel插件中的visitor对象：
visitor是一个包含不同节点类型的处理函数的对象。
每个节点类型都有一个对应的处理函数，当Babel遍历AST时，如果遇到匹配的节点类型，就会调用相应的处理函数来处理该节点。
```javascript
// return了一个babel插件
return {
  name: 'transform-react-jsx-to-rn-stylesheet',
  visitor: {
    Program: {
      enter() {
        // 对AST做一些前置判断
        if (t.isImportDeclaration(stmt.node)) {
          if (isStyle(stmt.node.source.value)) {
            existStyleImport = true // 是否存在样式文件的引入
          }
        }
        // 将styleSheet和cssSheet汇总，并添加一行代码到最顶层
        // expressionAst = template.ast(var _styleSheet = _appCssStyleSheet;)
        lastImportAstPath.insertAfter(expressionAst)
      },
      exit() {
        // 从最后一个import语句到行数开始，++依次插入处理好的语句
        if (injectGetStyle) {
          // @ts-ignore
          node.body.splice(++lastImportIndex, 0, getClassNameFunctionStmt)
          // @ts-ignore
          node.body.splice(++lastImportIndex, 0, getStyleFunctionStmt)
        }
        // 如果存在多个类，则需要插入mergeStyle函数
        if (file.get('hasMultiStyle')) {
          // @ts-ignore
          node.body.splice(++lastImportIndex, 0, getMergeEleStyleFunctionStmt)
        }
      },
    },
    // 在这里，visitor上的处理函数类型为：JSXOpeningElement，表示处理的JSX元素。
    JSXOpeningElement() {
      // 1. 遍历node.attributes
      // 2. 获取样式的插入方式，并通过file.set来记录
      if (arrayExpression.length > 1) {
        file.set('hasMultiStyle', true)
      }
      if (hasStyleAttribute && styleAttribute.value) {
        file.set('hasMultiStyle', true)
      }
      // 3. 修改attribute
      // 3.1 先删除原始的
      attributes.splice(attributes.indexOf(classNameAttribute), 1);
      // 3.2 根据场景处理加工attribute
      if(是否行内样式) { /* 处理逻辑 */ }
      if(是否数组样式) { /* 处理逻辑 */ }
      // 3.3 再将处理后的attribute表达式嵌入
      attributes.push(t.jSXAttribute(t.jSXIdentifier(key === DEFAULT_STYLE_KEY ? key : (key + 'Style')), t.jSXExpressionContainer(expression)))
    }
  }
}
```
#### ii. 具体分析JSXOpeningElement处理函数
```javascript
JSXOpeningElement (astPath, state: PluginPass) {
  const { node } = astPath; // 1. 拿到对应的JSX节点
  const { file, opts = {} } = state; // 2. 拿到源码文件
  const { styleMatchRule, classNameMathRule } 
    = getMatchRule(enableMultipleClassName); // 3. 获取样式、类名的正则匹配规则

  const styleNameMapping = {};
  const attributes = node.attributes; // 4. 获取jsx元素属性，name=className; value=header;
  for (let i = 0; i < attributes.length; i++) {
    const attrNameString = attributes[i].name.name; // 5. 获取属性名，className
    if (attrNameString.match(styleMatchRule)) { // 6. 判断是否为style
      styleNameMapping.style = {
        hasStyleAttribute: true,
        styleAttribute: attributes[i],
      };
    }
    if (attrNameString.match(classNameMathRule)) { // 7. 判断是否为className
      styleNameMapping.style = {
        hassClassName: true,
        classNameAttribute: attributes[i],
      };
    }
  }

  for (const key in styleNameMapping) { // 8. 遍历刚刚得到的样式对象
    // 8.1 有样式属性 style=xxx
    // 8.1.1 样式属性为字符串：style="background: red;"
    if (!(hasClassName && existStyleImport) && hasStyleAttribute) {
      styleAttribute.value 
      = t.jSXExpressionContainer(object2Expression(template, cssObject));
    }
    // 有样式属性 style=xxx 或 className=xxx
    if (hasClassName && existStyleImport) {
      // 8.2 删除原始属性
      attributes.splice(attributes.indexOf(classNameAttribute), 1);
      // 8.3.1 判断className是否为JSX表达式
      if(className是否为JSX表达式) {
        file.set('injectGetStyle', true);
      }
      // 8.3.2 是否有多个样式，比如：className={'header1 header2'}
      if (arrayExpression.length > 1) {
        file.set('hasMultiStyle', true);
      }
      // 将行内 style 转成oject：
      // 例如：style="width:100;height:100;" => style={{width:'100',height:'100'}}
      // 8.3.3 对多种场景，比如rn，做一些兼容处理
      // 8.4 将处理后的表达式插入node
      attributes.push(t.jSXAttribute(xxx));
    }
  }
}
```
