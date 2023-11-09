## 介绍：

`css-to-react-native` 是一个用于将 CSS 样式转换为 React Native 样式的库。
它的作用是将常见的 CSS 样式属性和值转换为 React Native 中的对应属性和值。

## 使用：

### 1. 安装

> npm install --save-dev babel-plugin-transform-react-jsx-to-rn-stylesheet

### 2. 用法

导出了两个函数可供使用：

```javascript
export { transformCSS }

export default transform
```

以`transformCSS`为例：

```javascript
import { transformCSS } from 'taro-css-to-react-native';
// str = 'width:100px;background-color:rgba(0, 0, 0, 0.5);border: 1px solid;'
const string2Object = str => {
  // entries = [["width", "100PX"], [ "background-color", "rgba(0, 0, 0, 0.5)" ], [ "border", "1PX solid" ]]
  const entries = str.replace(/;+$/g, '')
    .split(';')
    .map(l => {
      const arr = l.split(':').map(it => it.trim());
      arr[1] = arr[1]?.replace(/px/g, 'PX');
      return arr;
    });
  try {
    const cssObject = transformCSS(entries);
    return cssObject;
  } catch {
    return str;
  }
};
```

#### i. transformCSS效果

```javascript
render(<div style="width:100px;height:100px;background-color:rgba(0, 0, 0, 0.5);border: 1px solid;"/>)
// ⬇️ 转换后 ⬇️ 
render(<div style={{
  "width": 100,
  "height": 100,
  "backgroundColor": "rgba(0, 0, 0, 0.5)",
  "borderWidth": 1,
  "borderStyle": "solid",
  "borderColor": "black"
}}/>)
```

#### ii. transform效果

| 类型                    | css示例                                  | transform(css)结果                                                                                                                                                           |
|:----------------------|:---------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| empty(空值)             | ''                                     | {}                                                                                                                                                                         |
| flex                  | .test {<br/>&nbsp;&nbsp;flex: 1;<br/>} | {<br/>&nbsp;&nbsp;test:{<br/>&nbsp;&nbsp;&nbsp;&nbsp;flexBasis:0,<br/>&nbsp;&nbsp;&nbsp;&nbsp;flexGrow:1,<br/>&nbsp;&nbsp;&nbsp;&nbsp;flexShrink:1<br/>&nbsp;&nbsp;}<br/>} |
| 说明                    | 此行以下都省略了`.test{ xx: yy; }`类的包裹         | 此行以下都省略了`{ test: { xx: yy} }`对象的包裹                                                                                                                                         |
| numbers(数字)           | left:10px;                             | left: 'scalePx2dp(10)'                                                                                                                                                     |
| unsupported(未支持属性)    | @charset "utf-8";                      | {}                                                                                                                                                                         |
| percent(百分比)          | top: 0%;                               | top: '0%'                                                                                                                                                                  |
| decimal(小数)           | margin-top: 0.5px;                     | marginTop: 'scalePx2dp(0.5)'                                                                                                                                               |
| negative(负数)          | border-radius: -1.5px;                 | borderRadius: 'scalePx2dp(-1.5)'                                                                                                                                           |
| vh                    | line-height: 2vh;                      | lineHeight: "scaleVu2dp(2, 'vh')"                                                                                                                                          |
| 不规范px                 | top: 1Px;                              | top: 1                                                                                                                                                                     |
| 简写                    | margin: 10%;                           | marginTop: '10%', marginRight: '10%',marginBottom: '10%',marginLeft: '10%'                                                                                                 |
| 颜色                    | border-color: red;                     | borderColor: 'red'                                                                                                                                                         |
| shadow offsets        | shadow-offset: 10px 5px;               | shadowOffset: {height: 'scalePx2dp(5)',width: 'scalePx2dp(10)'}                                                                                                            |
| 多个css                 | .a {xx: yy;} .b {zz: dd;}              | { a: {xx: yy}, b: {zz: dd}}                                                                                                                                                |
| 一组选择器                 | .a, .b {xx: yy;}                       | { a: {xx: yy}, b: {xx: yy}}                                                                                                                                                |
| throw error           | margin: 10;                            | ThrowError('Failed to parse declaration "margin: 10"')                                                                                                                     |
| dash class name       | .test-1-2 { xx: yy; }                  | { 'test-1-2' { xx: yy } }                                                                                                                                                  |
| underscore class name | test_1                                 | test_1                                                                                                                                                                     |
| 非类选择器                 | .a {xx: yy;} #b {zz: dd;}              | a {xx: yy}                                                                                                                                                                 |
| 子类选择器                 | .foo > .bar { xx: yy; }                | {}                                                                                                                                                                         |
| transform             | transform: rotate(5deg);               | transform: [{ rotate: '5deg' }]                                                                                                                                            |
| multi transform       | transform: scaleX(5) skewX(1deg);      | transform: [{ skewX: '1deg' }, { scaleX: 5 }]                                                                                                                              |

## 源码：

### 1. 从__test__开始

单步调试这个测试用例，走一下核心transform流程：

```jest
it('transforms flex', () => {
  expect(
    transform(`
      .test {
        flex: 1;
        border-radius: 1vh;
      }
    `)
  ).toEqual({
    test: {
      flexBasis: 0,
      flexGrow: 1,
      flexShrink: 1,
      borderRadius: "scalePx2dp(1, 'vh')";
    }
  })
})
```

### 1. 深入到源码：src/index.js

引用了两个插件能力：`css-mediaquery @0.1.2` `css @3.0.0`
#### i. transform函数
```javascript
import parseCSS from 'css/lib/parse'; // 将CSS代码解析成一个AST
import mediaQuery from 'css-mediaquery'; // 解析和处理CSS媒体查询

const transform = (css, options) => {
  /**
   stylesheet.rules:
   [{
   'type': 'rule',
   'selectors': [
   '.test',
   ],
   'declarations': [
   {
   'type': 'declaration',
   'property': 'flex',
   'value': '1',
   'position': {
   'start': {
   'line': 3,
   'column': 9,
   },
   'end': {
   'line': 3,
   'column': 16,
   },
   },
   },
   {
   'type': 'declaration',
   'property': 'border-radius',
   'value': '1vh',
   'position': {
   'start': {
   'line': 4,
   'column': 9,
   },
   'end': {
   'line': 4,
   'column': 27,
   },
   },
   },
   ],
   'position': {
   'start': {
   'line': 2,
   'column': 7,
   },
   'end': {
   'line': 5,
   'column': 8,
   },
   },
   }]
   */
  const { stylesheet } = parseCSS(css);
  /**
   * sortRules：对:export选择器进行排序处理
   * 1. 如果有 :export选择器，则将此条rule排到后面
   * 2. 如果存在多个:export，start.line小的排到前面，这样就可以实现后面的会覆盖前面的
   */
  const rules = sortRules(stylesheet.rules);
  // 遍历排序后的rules
  for (const r in rules) {
    // 如果是export的，则将属性存到__exportProps对象中
    if (rule.selectors[s] === ':export') {
      result.__exportProps = {}; // :export选择器初始化
    }
    // 如果是普通类，则对所有属性执行单位转换、简写扩展等操作
    transformDecls(styles, rule.declarations, result, options);
    // 如果是媒体查询
    if (rule.type === 'media') {
      const parsed = mediaQuery.parse(rule.media);
      // 将解析后的结果直接挂载到result上
      result.__mediaQueries[media] = parsed;
    }
  }
  // 将__exportProps平铺到最终结果上
  if (result.__exportProps) {
    Object.assign(result, result.__exportProps);
    delete result.__exportProps;
  }
  return result;
};
```
