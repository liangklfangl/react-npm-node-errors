##### 问题：Warning: Unknown props `level` `index`, `parentMenu`, `eventKey`, `closeSubMenuOnMouseLeave`, `onItemHover`, `active, `openSubMenuOnMouseEnter` on <div> tag. Remove these props from the element

解答：详见,但是要注意，这个仅仅是warning，如果你的网页这时候无法显示不要以为就是这个原因，一般都不是这个原因

##### 问题： A valid React element (or null) must be returned. You may have returned undefined, an array or some other invalid object.

return 
  <div>
    hello world!
  </div>
解答：也就是说在return后面要直接跟上div，而不要空行

##### 问题：Uncaught TypeError: Cannot call a class as a function

解决：请确保你的Component继承了React.Component，否则你使用React语法就会出错。

##### 问题：Uncaught TypeError: Cannot assign to read only property 'exports' of object '#<Object>'

将loader后面的import修改为require就可以了，因为此时还没有经过babel处理，也就是修改为如下：
```js
 return 'const React =  require(\'react\');\n' +
        'const ReactDOM = require(\'react-dom\');\n'+
        code;
```
原来的代码为：
```js
return 'import React from \'react\';\n' +
    'import ReactDOM from \'react-dom\';\n' +
    code;
```

##### 问题：can not react auxiliaryCommentBefore of null

解决方案;将babel-generator降低到6.21.0即可

##### 问题：TypeError: Super expression must either be null or a function, not undefined

解决：说明你extend的那个函数没有导出相应的属性
```js
class AppGenerator extends Generators.Base{}
```
如果Generators没有导出Base，那么报错

##### 问题：内容如下
Module build failed: Error: React Hot Loader: The Webpack loader is now exported     separately. If you use Babel, we recommend that you remove "react-hot-loader" from the "loaders" section of your Webpack configuration altogether, and instead     add "react-hot-loader/babel" to the "plugins" section of your .babelrc file. If                        you prefer not to use Babel, replace "react-hot-loader" or "react-hot" with "rea ct-hot-loader/webpack" in the "loaders" section of your Webpack configuration.
    at Object.warnAboutIncorrectUsage (C:\Users\Administrator\Desktop\MyApp\node   _modules\react-hot-loader\lib\index.js:7:11)
 @ multi ./~/wds-hack?http://localhost:8080 webpack/hot/dev-server ./client.js
Child html-webpack-plugin for "index.html":

在.babelrc文件中添加如下内容:
```js
 {
    "presets": [
      "es2015-ie",
      "stage-0"
    ],
    "plugins": [
      "add-module-exports",
       "react-hot-loader/babel"//在js或者jsx中使用babel-loader加options方法
    ]
  }
```
取消webpack.config.js下面内容:
```js
                        {
                test: /\.jsx$/,
                exclude :path.resolve("node_modules"),
                use: [{
                    loader:'react-hot-loader',
                    options:{

                    }
                }]
              }
```
##### 问题：phantomjs报错

Error during loading "karma-phantomjs-launcher" plugin:
Path must be a string. Received null

解决方案:将phantomjs-prebuilt降级为2.1.7，因为在karma-phantomjs-launcher调用了它：
```js
  var phantomSource = require('phantomjs-prebuilt').path//这里获取到的path在2.1.7中存在
```

##### 问题：karma测试报错
ReferenceError: Can't find variable: webpackJsonp 

解决方法：ReferenceError: Can't find variable: webpackJsonp，也就是去掉commonchunkplugin

##### 问题:karma报错
Error: Cannot resolve module 'react/lib/ReactMount'

解决:参考资料Error: Cannot resolve module 'react/lib/ReactMount'   Module not found: Error: Cannot resolve module 'react/lib/ReactMount' in ...      Working with React 15   Cannot resolve module 'react/addons'

最终的解决方案在这里karma.config.js,其实我就添加了下面这句:
```js
externals: {
      'jsdom': 'window',
      'cheerio': 'window',
      'react/lib/ExecutionEnvironment': true,
      'react/addons': true,
      'react/lib/ReactContext': 'window',
      'sinon': 'window.sinon'
    },
  resolve: {
    alias: {
      actions: srcPath + '/actions/',
      components: srcPath + '/components/',
      sources: srcPath + '/sources/',
      stores: srcPath + '/stores/',
      styles: srcPath + '/styles/',
      config: srcPath + '/config/test'//,
      'react/lib/ReactMount': 'react-dom/lib/ReactMount'
    }
  }
```

##### 问题：模块加载报错
<pre>
Module not found: Error: Can't resolve 'react/lib/ReactTestUtils' in 'C:\Users\Administrator\Desktop\yo-my\node_modules\react-addons-test-utils'
</pre>
那么因为该模块被移动到react-dom模块下面去了

解决方案:
```js
resolve: {
    alias: {
      'react/lib/ReactMount': 'react-dom/lib/ReactMount',
      'react/lib/ReactTestUtils': 'react-dom/lib/ReactTestUtils'
    }
  }
```
##### 问题：打包过程中遇到下面报错
<pre>
ERROR in ./src/styles/app.css
Module build failed: Unknown word (5:1)

  3 | // load the styles
  4 | var content = require("!!../../../../../AppData/Roaming/npm/node_modules/webpackcc/node_modules/css-loader/index.js??ref--16-1!../../../../../AppData/Roaming/npm/node_modules/webpackcc/node_modules/postcss-loader/index.js??ref--16-2!./app.css");
> 5 | if(typeof content === 'string') content = [[module.id, content, '']];
    | ^
  6 | // add the styles to the DOM
  7 | var update = require("!../../../../../AppData/Roaming/npm/node_modules/webpackcc/node_modules/style-loader/addStyles.js")(content, {});
 @ ./src/entry.js 12:12-39
 @ multi C:/Users/Administrator/AppData/Roaming/npm/~/webpackcc/~/wds-hack?http://localhost:8080 webpack/hot/dev-server ./src/entry.js
ERROR in ./src/static/header.css
Module build failed: Unknown word (5:1)
</pre>

解决方案：是因为两次添加了webpack中loader来处理css/less/scss文件，导致loader重复了，所以直接去掉就可以了！

##### 问题：打包的时候说没有extract-text-webpack-plugin插件
<pre>
 Error: "extract-text-webpack-plugin" loader is used without the corresponding  plugin, refer to https://github.com/webpack/extract-text-webpack-plugin for the   
</pre> 
解决方法：报错其实很明显，我没有添加webpack的extract-text-webpack-plugin插件，但是我在loader中使用了extract方法。当然，在这之前请确认你的extract-text-webpack-plugin插件的版本没有问题

##### 问题：报错说require不是一个函数
<pre>
TypeError: require(...) is not a function
    at Object.<anonymous> (C:\Users\Administrator\Desktop\mdw\bin\mdw:15:26)
    at Module._compile (module.js:570:32)
    at Object.Module._extensions..js (module.js:579:10)
    at Module.load (module.js:487:32)
    at tryModuleLoad (module.js:446:12)
    at Function.Module._load (module.js:438:3)
    at Module.runMain (module.js:604:10)
    at run (bootstrap_node.js:394:7)
    at startup (bootstrap_node.js:149:9)
    at bootstrap_node.js:509:3
</pre>
请看我的用法 [require("../lib/DllPlugin")(program)](https://github.com/liangklfangl/wcf/blob/master/bin/wcf);

解决方法：因为我使用了export default而不是module.exports,所以你需要添加babel插件 "add-module-exports",

##### 问题：Uncaught Error: The root route must render a single element
解决方法：请问你的组件是不是没有导出一个方法？如export default /module.export

##### 问题：这个问题是一个很简单的问题，但是可能报错的位置不正确导致很难找到
<pre>
     message: 'Module build failed: Missed semicolon (6:1)\n\n \u001b[90m 4 | \u001b[39m margin-bottom\u001b[33m:\u001b[39m 24px\u001b[33m;\u001b[39m\n \u001b[90m 5 | \u001b[39m padding\u001b[33m:\u001b[
39m0 48px\u001b[33m;\u001b[39m\n\u001b[31m\u001b[1m>\u001b[22m\u001b[39m\u001b[90m 6 | \u001b[39m width\u001b[33m:\u001b[39m 100%\n \u001b[90m   | \u001b[39m\u001b[31m\u001b[1m^\u001b[22m\u001b[39m\n \u0
01b[90m 7 | \u001b[39m border\u001b[33m:\u001b[39m2px solid blue\u001b[33m;\u001b[39m\n \u001b[90m 8 | \u001b[39m\u001b[33m}\u001b[39m\n',
     details: 'Syntax Error: Missed semicolon (6:1)\n\n \u001b[90m 4 | \u001b[39m margin-bottom\u001b[33m:\u001b[39m 24px\u001b[33m;\u001b[39m\n \u001b[90m 5 | \u001b[39m padding\u001b[33m:\u001b[39m0 48
px\u001b[33m;\u001b[39m\n\u001b[31m\u001b[1m>\u001b[22m\u001b[39m\u001b[90m 6 | \u001b[39m width\u001b[33m:\u001b[39m 100%\n \u001b[90m   | \u001b[39m\u001b[31m\u001b[1m^\u001b[22m\u001b[39m\n \u001b[90m
 7 | \u001b[39m border\u001b[33m:\u001b[39m2px solid blue\u001b[33m;\u001b[39m\n \u001b[90m 8 | \u001b[39m\u001b[33m}\u001b[39m\n\n    at C:\\Users\\Administrator\\Desktop\\mdw\\node_modules\\postcss-loa
der\\index.js:146:22\n    at process._tickCallback (internal/process/next_tick.js:103:7)',
</pre>

解决方法：我当时因为报错的位置不正确的原因，我直接去打印了webpack的Compiler对象，其中会有到底是哪一行出错的。下面是compiler中的error部分的内容，所以很显然就找到了原因了：
<pre>
  error:
   { ModuleBuildError: Module build failed: Missed semicolon (6:1)

     4 |  margin-bottom: 24px;
     5 |  padding:0 48px;
   > 6 |  width: 100%
       | ^
     7 |  border:2px solid blue;
     8 | }

       at C:\Users\Administrator\Desktop\mdw\node_modules\webpack\lib\NormalModule.js:141:35
       at C:\Users\Administrator\Desktop\mdw\node_modules\loader-runner\lib\LoaderRunner.js:364:11
       at C:\Users\Administrator\Desktop\mdw\node_modules\loader-runner\lib\LoaderRunner.js:230:18
       at context.callback (C:\Users\Administrator\Desktop\mdw\node_modules\loader-runner\lib\LoaderRunner.js:111:13)
       at C:\Users\Administrator\Desktop\mdw\node_modules\postcss-loader\index.js:146:13
</pre>

##### 问题：当Link中迭代一个对象的时候报错
<pre>
Uncaught Error: Objects are not valid as a React child (found: object with keys {zh-CN, en-US}). If you meant to render a collection of children, use an array instead or wrap the object using createFragment(object) from the React add-ons. Check the render method of `Link`.
Uncaught TypeError: Cannot read property '__reactInternalInstance$nxdvao9iv2sfaq4x9z4pr2j4i' of null
</pre>

我一开始的Link是这样迭代的：
```js
 <Link to={url} disabled={disabled}>
            {item.title}
      <\/Link>
```
而我们的item.title是如下的内容：
```json
{
  "zh-CN": "典型页面",
  "en-US": "Classic"
}
```
所以报错了。所以修改成为如下就不报错了：
```js
 <Link to={url} disabled={disabled}>
            {item.title["zh-CN"]}
  <\/Link>
```
##### 问题：markdown转化为Jsonml时候出错
<pre>
ERROR in ./lib/utils/data.js
Module build failed: YAMLException: can not read a block mapping entry; a multi
ine key may not be an implicit key at line 3, column 5:
    type: Feedback
        ^
    at generateError (C:\Users\Administrator\Desktop\mdw\node_modules\js-yaml\l
b\js-yaml\loader.js:162:10)
    at throwError (C:\Users\Administrator\Desktop\mdw\node_modules\js-yaml\lib\
s-yaml\loader.js:168:9)
    at readBlockMapping (C:\Users\Administrator\Desktop\mdw\node_modules\js-yam
\lib\js-yaml\loader.js:1041:9)
    at composeNode (C:\Users\Administrator\Desktop\mdw\node_modules\js-yaml\lib
js-yaml\loader.js:1327:12)
    at readDocument (C:\Users\Administrator\Desktop\mdw\node_modules\js-yaml\li
\js-yaml\loader.js:1489:3)
    at loadDocuments (C:\Users\Administrator\Desktop\mdw\node_modules\js-yaml\l
b\js-yaml\loader.js:1545:5)
    at Object.load (C:\Users\Administrator\Desktop\mdw\node_modules\js-yaml\lib
js-yaml\loader.js:1562:19)
    at Object.jsYaml.parse (C:\Users\Administrator\Desktop\mdw\node_modules\yam
-front-matter\lib\js-yaml-front.js:16:21)
    at Object.jsYaml.loadFront (C:\Users\Administrator\Desktop\mdw\node_modules
yaml-front-matter\lib\js-yaml-front.js:34:19)
    at MT (C:\Users\Administrator\Desktop\mdw\node_modules\mark-twain\src\MT.js
10:19)
 @ ./tmp/entry.index.js 25:11-42
 </pre>
原因，是因为在key和value之间少了一个空格，如下：
```js
category: Components
subtitle:警告提示
type: Feedback
title: Alert
```
是因为"subtitle:"和"警告提示"之间少了一个空格，加上就没问题了！

##### 问题：使用react-a11y时候报错
<pre>
jsx报错Uncaught RangeError: Maximum call stack size exceeded
</pre>
解决方法如下：
```js
constructor(){
    super();
    //这个a11y必须设置到constructor上，如果是render上设置会堆栈溢出
     if (process.env.NODE_ENV !== 'development'){
        a11y(React);
    }
  }
```
我们不要将a11y(React)放在render方法中，而是放在constructor里面!

##### 问题：无状态组件的书写问题
<pre>
Uncaught Error: Module build failed: SyntaxError: C:/Users/Administrator/Desktop/sy-template/theme/template/content/Article.jsx: Unexpected token, expected ; (8:10)

   6 |  */
   7 | export default (props)=>{
>  8 |   render(){
     |           ^
   9 |     return (
  10 |               <div>hello</div>
  11 |     )
</pre>

解决：无状态组件是一个函数而不是一个类，所以不能直接写render，只要return就ok了。
```js
export default (props)=>{
        return (
              <div>hello<\/div>
            )
}
```

##### 问题：reactJS报错
<pre>
ReactJs 报错 Element type is invalid: expected a string (from built-in components) or a class/function (for composite components) but got: undefined. Check the render method of `Me`.
</pre>
解决方法1：我发现是我导入react-router的时候采用了如下方法：
```js
import { Link } from 'react-router';
```
而且我安装的react-router是4.x,所以导致报错，最后把react-router降级为3.0.2问题消失。这个问题好像大家出现的也比较多，大多数情况都是因为导入的方式与版本不匹配而导致的问题。

解决方法2：后面我又遇到了这个问题:
<pre>
  Uncaught Error: Element type is invalid: expected a string (for built-in components) or a class/function (for composite components) but got: undefined. You likely forgot to export your component from the file it's defined in. Check the render method of `Home`.
</pre>
最后我定位到下面的HOME组件:
```js
var Home = function Home(_ref) {
  var routes = _ref.routes,
      params = _ref.params,
      children = _ref.children;
  return React.createElement(
    'div',
    null,
    React.createElement(
      'div',
      { className: 'demo-nav' },
      React.createElement(
        _reactRouter.Link,
        { to: '/' },
        'Home'
      ),
      React.createElement(
        _reactRouter.Link,
        { to: '/apps' },
        'Application List'
      )
    ),
    children || 'Home Page',
    React.createElement(_alert2.default, { style: { margin: '16px 0' }, message: 'Click the navigation above to switch:' }),
    React.createElement(_breadcrumb2.default, { routes: routes, params: params })
  );
};
```
发现是_alert2为undefined，其实和前面的解决方法是一样的，那就是我们import进来的不是一个对象，而是空~~

##### 问题：React的setState的问题
<pre>
Uncaught TypeError: Cannot read property 'setState' of null
    at handleCodeExpand (http://localhost:8090/components/alert/index.js:15093:9)
</pre>
解决方法：
```js
 shouldComponentUpdate(nextProps, nextState) {
    return (this.state.codeExpand || this.props.expand) !== (nextState.codeExpand || nextProps.expand);
  }
  handleCodeExapnd = () => {
    this.setState({ codeExpand: !this.state.codeExpand });
  }
```
同时在jsx中就可以简单的如下使用:
```jsx
  <Icon type="down-circle-o" title="Show Code" className="collapse" onClick={this.handleCodeExapnd} \/>
```
生命周期方法我们还是采用普通的写法，而我们自定义的方法采用了变量的形式~~~~

##### 问题：使用expose-loader的时候出错
<pre>
Uncaught ReferenceError: global is not defined
    at Object._hyphenPattern (jquery.js:1)
    at __webpack_require__ (bootstrap 22d237f…:691)
    at fn (bootstrap 22d237f…:114)
    at Object.<anonymous> (index.jsx:44)
    at Object.defineProperty.value (index.jsx:100)
    at __webpack_require__ (bootstrap 22d237f…:691)
    at fn (bootstrap 22d237f…:114)
    at webpackContext (index.js:22)
    at templateWrapper (routes.js:59)
    at processRoutes (routes.js:107)
</pre>

解决方法：是因为我在webpack的配置中有下面这句代码，去掉就可以了。
```js
 noParse: [/jquery/],//此时jquery
```
你也可以参考这里问题1 问题2

##### 问题：记一次React排错过程
<pre>
报错信息：
Uncaught Error: Minified React error #37; visit http://facebook.github.io/react/docs/error-decoder.html?invariant=37 for the full message or use the non-minified dev environment for full errors and additional helpful warnings.
</pre>

分析：这次报错其实很容易理解，它说了访问后面的链接看到的信息就是本次报错decode的信息(如果`process.env.NODE_ENV=="production"`那么此时就是encode的报错信息，而``process.env.NODE_ENV="development"`得到的报错信息就是decode后的信息)。顺着这个思路，我设置了下面这段代码：
```js
process.env.NODE_ENV = 'development';
```
最后我再重新编译，得到的结果就是下面的内容了：
<pre>
_registerComponent(...): Target container is not a DOM element.
</pre>
是不是很容易理解，对于我的问题最后发现是因为if判断的问题，导致我这里都会使用自己的htmlTemplate，而我们的这个模版本身不含id为"react-content"的元素导致的~~。更加有意思的是，设置了`process.env.NODE_ENV="development"`后，我们看到的信息就是后面链接的信息。以后记住这个就好了。之所以出现这个是因为React本身将报错信息在production模式下进行了encode  Error decoder

##### 问题：Babel打包出错
<pre>
  { SyntaxError: unknown: Unexpected token (4:8)
  2 |
  3 | class ButtonSize extends React.Component {
> 4 |   state = {
    |         ^
  5 |     size: 'default',
  6 |   };
  7 |
    at Parser.pp$5.raise (C:\Users\Administrator\Desktop\mdw\node_modules\babylon\lib\index.js:4373:13)
    at Parser.pp.unexpected (C:\Users\Administrator\Desktop\mdw\node_modules\babylon\lib\index.js:1716:8)
    at Parser.pp$1.parseClassProperty (C:\Users\Administrator\Desktop\mdw\node_modules\babylon\lib\index.js:2507:50)
    at Parser.parseClassProperty (C:\Users\Administrator\Desktop\mdw\node_modules\babylon\lib\index.js:5940:20)
    at Parser.pp$1.parseClassBody (C:\Users\Administrator\Desktop\mdw\node_modules\babylon\lib\index.js:2424:34)
    at Parser.pp$1.parseClass (C:\Users\Administrator\Desktop\mdw\node_modules\babylon\lib\index.js:2361:8)
    at Parser.pp$1.parseStatement (C:\Users\Administrator\Desktop\mdw\node_modules\babylon\lib\index.js:1798:19)
    at Parser.parseStatement (C:\Users\Administrator\Desktop\mdw\node_modules\babylon\lib\index.js:5712:22)
    at Parser.pp$1.parseBlockBody (C:\Users\Administrator\Desktop\mdw\node_modules\babylon\lib\index.js:2223:21)
    at Parser.pp$1.parseTopLevel (C:\Users\Administrator\Desktop\mdw\node_modules\babylon\lib\index.js:1733:8)
    at Parser.parse (C:\Users\Administrator\Desktop\mdw\node_modules\babylon\lib\index.js:1628:17)
    at parse (C:\Users\Administrator\Desktop\mdw\node_modules\babylon\lib\index.js:7083:37)
    at File.parse (C:\Users\Administrator\Desktop\mdw\node_modules\babel-core\lib\transformation\file\index.js:527:15)
    at File.parseCode (C:\Users\Administrator\Desktop\mdw\node_modules\babel-core\lib\transformation\file\index.js:612:20)
    at C:\Users\Administrator\Desktop\mdw\node_modules\babel-core\lib\transformation\pipeline.js:49:12
    at File.wrap (C:\Users\Administrator\Desktop\mdw\node_modules\babel-core\lib\transformation\file\index.js:574:16)
  pos: 96,
  loc: Position { line: 4, column: 8 },
  _babel: true,
  codeFrame: '\u001b[0m \u001b[90m 2 | \u001b[39m\n \u001b[90m 3 | \u001b[39m\u001b[36mclass\u001b[39m \u001b[33mButtonSize\u001b[39m \u001b[36mextends\u
001b[39m \u001b[33mReact\u001b[39m\u001b[33m.\u001b[39m\u001b[33mComponent\u001b[39m {\n\u001b[31m\u001b[1m>\u001b[22m\u001b[39m\u001b[90m 4 | \u001b[39m
  state \u001b[33m=\u001b[39m {\n \u001b[90m   | \u001b[39m        \u001b[31m\u001b[1m^\u001b[22m\u001b[39m\n \u001b[90m 5 | \u001b[39m    size\u001b[33m
:\u001b[39m \u001b[32m\'default\'\u001b[39m\u001b[33m,\u001b[39m\n \u001b[90m 6 | \u001b[39m  }\u001b[33m;\u001b[39m\n \u001b[90m 7 | \u001b[39m\u001b[0m
' }
</pre>

解决方法：请确认preset是否添加了两次，如下：
```js
   presets: ['es2015', 'react'].map(m =>
      require.resolve(`babel-preset-${m}`)
   ),
   //是否有两个es2015呢
```
