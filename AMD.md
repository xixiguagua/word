通行的Javascript的模板规范共有两种：CommonJS 和 AMD
commonjs即为服务器端模块的规范。 commonjs的规范： 根据commonjs规范，一个单独的文件就是一个模块。加载模块使用require方法，该方法读取一个文件并执行，最后返回文件内部的exports对象
commonjs模块无论加载多少次，都只会在第一次加载时运行一次，以后再加载，就返回第一次运行的结果，除非手动清除系统缓存
commonjs规范加载模块是同步的，也就是说，只有加载完成，才能执行后面的操作。AMD规范则是非同步加载模块，允许指定回调函数。由于Node.js主要用于服务器编程，模块文件一般都已经存在于本地硬盘，所以加载起来比较快，不用考虑非同步加载的方式，所以commonjs规范比较适用。但是，如果是浏览器环境，要从服务器端加载模块，这时就必须采用非同步模式，因此浏览器端一般采用AMD规范。
可以理解为AMD即为能在客户端环境，并且能兼容服务器端模块的一种模块规范
AMD的模块定义：
AMD规范使用define方法定义模块
Define第一个参数表达依赖的模块数组，第二个为加载完依赖的模块数组后，模块执行的函数
AMD的模块加载定义：跟commonjs一样，AMD也采用require()语句来加载模块，但是与commonjs不同的是，它要求有两个参数：
第一个参数[module]，是一个数组，里面的成员就是要加载的模块；第二个参数callback，则是加载成功之后的回调函数
AMD和CMD对比
1,对于依赖的模块，AMD是提前执行，CMD是延迟执行。不过 RequireJS 从 2.0 开始，也改成可以延迟执行（根据写法不同，处理方式不同）。CMD推崇 as lazy as possible.
2,CMD推崇依赖就近，AMD推崇依赖前置
CMD
define(function(require, exports, module) {
var a = require('./a')
a.doSomething()
// 此处略去 100 行
var b = require('./b') // 依赖可以就近书写
b.doSomething()
// ... 
})

// AMD 默认推荐的是
define(['./a', './b'], function(a, b) { // 依赖必须一开始就写好
a.doSomething()
// 此处略去 100 行
b.doSomething()
...
}) 
虽然 AMD 也支持 CMD 的写法，同时还支持将 require 作为依赖项传递，但 RequireJS 的作者默认是最喜欢上面的写法，也是官方文档里默认的模块定义写法。
3,AMD的API默认是一个当多个用，CMD的API 严格区分，推崇职责单一。比如AMD里，require分全局require和局部require，都叫require。CMD里，没有全局 require，而是根据模块系统的完备性，提供seajs.use来实现模块系统的加载启动。CMD里，每个API都简单纯粹。
ES6模块的设计思想，是尽量的静态化，使得编译时就能确定模块的依赖关系，以及输入和输出的变量。commonjs和AMD模块，都只能在运行时确定这些东西。比如，commonjs模块就是对象，输入时必须查找对象属性。

AMD是"Asynchronous Module Definition"的缩写，意思就是"异步模块定义"。它采用异步方式加载模块，模块的加载不影响它后面语句的运行。所有依赖这个模块的语句，都定义在一个回调函数中，等到加载完成之后，这个回调函数才会运行。
AMD也采用require()语句加载模块，但是不同于CommonJS，它要求两个参数：
　　require([module], callback);
第一个参数[module]，是一个数组，里面的成员就是要加载的模块；第二个参数callback，则是加载成功之后的回调函数。如果将前面的代码改写成AMD形式，就是下面这样：
　　require(['math'], function (math) {
　　　　math.add(2, 3);
　　});
math.add()与math模块加载不是同步的，浏览器不会发生假死。所以很显然，AMD比较适合浏览器环境。

//require.js的诞生，
//（1）实现js文件的异步加载，避免网页失去响应；
//（2）管理模块之间的依赖性，便于代码的编写和维护。
require.js的加载
使用require.js的第一步，是先去官方网站下载最新版本。
下载后，假定把它放在js子目录下面，就可以加载了。
　　<script src="js/require.js"></script>
有人可能会想到，加载这个文件，也可能造成网页失去响应。解决办法有两个，一个是把它放在网页底部加载，另一个是写成下面这样：
　　<script src="js/require.js" defer async="true" ></script>
async属性表明这个文件需要异步加载，避免网页失去响应。IE不支持这个属性，只支持defer，所以把defer也写上。
加载require.js以后，下一步就要加载我们自己的代码了。假定我们自己的代码文件是main.js，也放在js目录下面。那么，只需要写成下面这样就行了：
　　<script src="js/require.js" data-main="js/main"></script>
data-main属性的作用是，指定网页程序的主模块。在上例中，就是js目录下面的main.js，
这个文件会第一个被require.js加载。由于require.js默认的文件后缀名是js，
所以可以把main.js简写成main。
上一节的main.js，我把它称为"主模块"，意思是整个网页的入口代码。它有点像C语言的main()函数，所有代码都从这儿开始运行。
下面就来看，怎么写main.js。
如果我们的代码不依赖任何其他模块，那么可以直接写入javascript代码。
　　// main.js
　　alert("加载成功！");
但这样的话，就没必要使用require.js了。真正常见的情况是，主模块依赖于其他模块，这时就要使用AMD规范定义的的require()函数。
　　// main.js
　　require(['moduleA', 'moduleB', 'moduleC'], function (moduleA, moduleB, moduleC){
　　　　// some code here
　　});
require()函数接受两个参数。第一个参数是一个数组，表示所依赖的模块，上例就是['moduleA', 'moduleB', 'moduleC']，即主模块依赖这三个模块；第二个参数是一个回调函数，当前面指定的模块都加载成功后，它将被调用。加载的模块会以参数形式传入该函数，从而在回调函数内部就可以使用这些模块。
require()异步加载moduleA，moduleB和moduleC，浏览器不会失去响应；它指定的回调函数，只有前面的模块都加载成功后，才会运行，解决了依赖性的问题。
下面，我们看一个实际的例子。
假定主模块依赖jquery、underscore和backbone这三个模块，main.js就可以这样写：
　　require(['jquery', 'underscore', 'backbone'], function ($, _, Backbone){
　　　　// some code here
　　});
require.js会先加载jQuery、underscore和backbone，然后再运行回调函数。主模块的代码就写在回调函数中。
上一节最后的示例中，主模块的依赖模块是['jquery', 'underscore', 'backbone']。默认情况下，require.js假定这三个模块与main.js在同一个目录，文件名分别为jquery.js，underscore.js和backbone.js，然后自动加载。
使用require.config()方法，我们可以对模块的加载行为进行自定义。require.config()就写在主模块（main.js）的头部。参数就是一个对象，这个对象的paths属性指定各个模块的加载路径。
　　require.config({
　　　　paths: {
　　　　　　"jquery": "jquery.min",
　　　　　　"underscore": "underscore.min",
　　　　　　"backbone": "backbone.min"
　　　　}
　　});
上面的代码给出了三个模块的文件名，路径默认与main.js在同一个目录（js子目录）。如果这些模块在其他目录，比如js/lib目录，则有两种写法。一种是逐一指定路径。
require.config({
　　　　paths: {
　　　　　　"jquery": "lib/jquery.min",
　　　　　　"underscore": "lib/underscore.min",
　　　　　　"backbone": "lib/backbone.min"
　　　　}
　　});
另一种则是直接改变基目录（baseUrl）。
　　require.config({
　　　　baseUrl: "js/lib",
　　　　paths: {
　　　　　　"jquery": "jquery.min",
　　　　　　"underscore": "underscore.min",
　　　　　　"backbone": "backbone.min"
　　　　}
　　});
如果某个模块在另一台主机上，也可以直接指定它的网址，比如：
　　require.config({
　　　　paths: {
　　　　　　"jquery": "https://ajax.googleapis.com/ajax/libs/jquery/1.7.2/jquery.min"
　　　　}
　　});
require.js要求，每个模块是一个单独的js文件。这样的话，如果加载多个模块，就会发出多次HTTP请求，会影响网页的加载速度。因此，require.js提供了一个优化工具，当模块部署完毕以后，可以用这个工具将多个模块合并在一个文件中，减少HTTP请求数。
理论上，require.js加载的模块，必须是按照AMD规范、用define()函数定义的模块。但是实际上，虽然已经有一部分流行的函数库（比如jQuery）符合AMD规范，更多的库并不符合。那么，require.js是否能够加载非规范的模块呢？
回答是可以的。
这样的模块在用require()加载之前，要先用require.config()方法，定义它们的一些特征。
举例来说，underscore和backbone这两个库，都没有采用AMD规范编写。如果要加载它们的话，必须先定义它们的特征。
　　require.config({
　　　　shim: {

　　　　　　'underscore':{
　　　　　　　　exports: '_'
　　　　　　},
　　　　　　'backbone': {
　　　　　　　　deps: ['underscore', 'jquery'],
　　　　　　　　exports: 'Backbone'
　　　　　　}
　　　　}
　　});
require.config()接受一个配置对象，这个对象除了有前面说过的paths属性之外，还有一个shim属性，专门用来配置不兼容的模块。具体来说，每个模块要定义（1）exports值（输出的变量名），表明这个模块外部调用时的名称；（2）deps数组，表明该模块的依赖性。
比如，jQuery的插件可以这样定义：
　　shim: {
　　　　'jquery.scroll': {
　　　　　　deps: ['jquery'],
　　　　　　exports: 'jQuery.fn.scroll'
　　　　}
　　}
require.js还提供一系列插件，实现一些特定的功能。
domready插件，可以让回调函数在页面DOM结构加载完成后再运行。
　　require(['domready!'], function (doc){
　　　　// called once the DOM is ready
　　});
text和image插件，则是允许require.js加载文本和图片文件。
　　define([
　　　　'text!review.txt',
　　　　'image!cat.jpg'
　　　　],

　　　　function(review,cat){
　　　　　　console.log(review);
　　　　　　document.body.appendChild(cat);
　　　　}
　　);


模块 id 必须符合标准。

解释：

模块 id 必须符合以下约束条件：

类型为 string，并且是由 / 分割的一系列 terms 来组成。例如：this/is/a/module。
term 应该符合 [a-zA-Z0-9_-]+ 规则。
不应该有 .js 后缀。
跟文件的路径保持一致。
4.1.2 define

 定义模块时不要指明 id 和 dependencies。

解释：

在 AMD 的设计思想里，模块名称是和所在路径相关的，匿名的模块更利于封包和迁移。模块依赖应在模块定义内部通过 local require 引用。

所以，推荐使用 define(factory) 的形式进行模块定义。

示例：

define(
    function (require) {
    }
);
 使用 return 来返回模块定义。

解释：

使用 return 可以减少 factory 接收的参数（不需要接收 exports 和 module），在没有 AMD Loader 的场景下也更容易进行简单的处理来伪造一个 Loader。

示例：

define(
    function (require) {
        var exports = {};

        // ...

        return exports;
    }
);
4.1.3 require

[强制] 全局运行环境中，require 必须以 async require 形式调用。

解释：

模块的加载过程是异步的，同步调用并无法保证得到正确的结果。

示例：

// good
require(['foo'], function (foo) {
});

// bad
var foo = require('foo');
[强制] 模块定义中只允许使用 local require，不允许使用 global require。

解释：

在模块定义中使用 global require，对封装性是一种破坏。
在 AMD 里，global require 是可以被重命名的。并且 Loader 甚至没有全局的 require 变量，而是用 Loader 名称做为 global require。模块定义不应该依赖使用的 Loader。
[强制] Package 在实现时，内部模块的 require 必须使用 relative id。

解释：

对于任何可能通过 发布-引入 的形式复用的第三方库、框架、包，开发者所定义的名称不代表使用者使用的名称。因此不要基于任何名称的假设。在实现源码中，require 自身的其它模块时使用 relative id。

示例：

define(
    function (require) {
        var util = require('./util');
    }
);
 不会被调用的依赖模块，在 factory 开始处统一 require。

解释：

有些模块是依赖的模块，但不会在模块实现中被直接调用，最为典型的是 css / js / tpl 等 Plugin 所引入的外部内容。此类内容建议放在模块定义最开始处统一引用。

示例：

define(
    function (require) {
        require('css!foo.css');
        require('tpl!bar.tpl.html');

        // ...
    }
);