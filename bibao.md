在 JavaScript 中，无需特别的关键词就可以使用闭包，一个函数可以任意访问在其定义的作用域外的变量。需要注意的是，函数的作用域是静态的，即在定义时决定，与调用的时机和方式没有任何关系。

闭包会阻止一些变量的垃圾回收，对于较老旧的 JavaScript 引擎，可能导致外部所有变量均无法回收。

首先一个较为明确的结论是，以下内容会影响到闭包内变量的回收：

嵌套的函数中是否有使用该变量。
嵌套的函数中是否有 直接调用eval。
是否使用了 with 表达式。
Chakra、V8 和 SpiderMonkey 将受以上因素的影响，表现出不尽相同又较为相似的回收策略，而 JScript.dll 和 Carakan 则完全没有这方面的优化，会完整保留整个 LexicalEnvironment 中的所有变量绑定，造成一定的内存消耗。

由于对闭包内变量有回收优化策略的 Chakra、V8 和 SpiderMonkey 引擎的行为较为相似，因此可以总结如下，当返回一个函数 fn 时：

如果 fn 的 [[Scope]] 是 ObjectEnvironment（with 表达式生成 ObjectEnvironment，函数和 catch 表达式生成 DeclarativeEnvironment），则：
如果是 V8 引擎，则退出全过程。
如果是 SpiderMonkey，则处理该 ObjectEnvironment 的外层 LexicalEnvironment。
获取当前 LexicalEnvironment 下的所有类型为 Function 的对象，对于每一个 Function 对象，分析其 FunctionBody：
如果 FunctionBody 中含有 直接调用 eval，则退出全过程。
否则得到所有的 Identifier。
对于每一个 Identifier，设其为 name，根据查找变量引用的规则，从 LexicalEnvironment 中找出名称为 name 的绑定 binding。
对 binding 添加 notSwap 属性，其值为 true。
检查当前 LexicalEnvironment 中的每一个变量绑定，如果该绑定有 notSwap 属性且值为 true，则：
如果是 V8 引擎，删除该绑定。
如果是 SpiderMonkey，将该绑定的值设为 undefined，将删除 notSwap 属性。
对于 Chakra 引擎，暂无法得知是按 V8 的模式还是按 SpiderMonkey 的模式进行。

如果有 非常庞大 的对象，且预计会在 老旧的引擎 中执行，则使用闭包时，注意将闭包不需要的对象置为空引用。

使用 IIFE 避免 Lift 效应。
在引用函数外部变量时，函数执行时外部变量的值由运行时决定而非定义时，最典型的场景如下：

var tasks = [];
for (var i = 0; i < 5; i++) {
    tasks[tasks.length] = function () {
        console.log('Current cursor is at ' + i);
    };
}

var len = tasks.length;
while (len--) {
    tasks[len]();
}
以上代码对 tasks 中的函数的执行均会输出 Current cursor is at 5，往往不符合预期。

此现象称为 Lift 效应 。解决的方式是通过额外加上一层闭包函数，将需要的外部变量作为参数传递来解除变量的绑定关系：
var tasks = [];
for (var i = 0; i < 5; i++) {
    // 注意有一层额外的闭包
    tasks[tasks.length] = (function (i) {
        return function () {
            console.log('Current cursor is at ' + i);
        };
    })(i);
}

var len = tasks.length;
while (len--) {
    tasks[len]();
}


有两种作用域：全局作用域和函数作用域。函数内部可以直接读取全局变量。
在函数外部无法读取函数内部声明的变量。如果出于种种原因，需要得到函数内的局部变量。
那就是在函数的内部，再定义一个函数。
函数f2就在函数f1内部，这时f1内部的所有局部变量，对f2都是可见的
这就是JavaScript语言特有的”链式作用域”结构（chain scope），子对象会一级一级地向上寻找
所有父对象的变量。所以，父对象的所有变量，对子对象都是可见的，反之则不成立。
函数f1的返回值就是函数f2，由于f2可以读取f1的内部变量，所以就可以在外部获得f1的内部变量了。
闭包就是函数f2，即能够读取其他函数内部变量的函数。由于在JavaScript语言中，
只有函数内部的子函数才能读取内部变量，因此可以把闭包简单理解成“定义在一个函数内部的函数”。
闭包最大的特点，就是它可以“记住”诞生的环境，比如f2记住了它诞生的环境f1，所以从f2可以得到f1的内部变量。
原因就在于f2始终在内存中，而f2的存在依赖于f1，因此f1也始终在内存中，不会在调用结束后，被垃圾回收机制回收。
在本质上，闭包就是将函数内部和函数外部连接起来的一座桥梁。
闭包的最大用处有两个，一个是可以读取函数内部的变量，

另一个就是让这些变量始终保持在内存中，即闭包可以使得它诞生环境一直存在
由于闭包会使得函数中的变量都被保存在内存中，内存消耗很大，所以不能滥用闭包，否则会造成网页的性能问题，在IE中可能导致内存泄露。解决方法是，在退出函数之前，将不使用的局部变量全部删除。
闭包会在父函数外部，改变父函数内部变量的值。所以，如果你把父函数当作对象（object）使用，把闭包当作它的公用方法（Public Method），把内部变量当作它的私有属性（private value），这时一定要小心，不要随便改变父函数内部变量的值。

闭包封装
立即调用的函数表达式(function(){})()
JavaScript引擎规定，如果function关键字出现在行首，一律解释成语句。
因此，JavaScript引擎看到行首是function关键字之后，认为这一段都是函数的定义，不应该以圆括号结尾，所以就报错了。
解决方法就是不要让function出现在行首，让引擎将其理解成一个表达式。最简单的处理，就是将其放在一个圆括号里面。
它的目的有两个：一是不必为函数命名，避免了污染全局变量；
二是IIFE内部形成了一个单独的作用域，可以封装一些外部无法读取的私有变量
(function(){
    var item='items';
    var id=123;
    var exp={};
    function constr(id){
        return ++id;
    }
    exp.getitem=function(){
        return item;
    }
    exp.getid=function(){
        return id;
    }
    window.asd=exp;
})();
console.log(asd.getitem(),asd.getid());


闭包封装
(function(){
    var q=0;
     var w=0;
    var exp={};
    function add(m,n){
        return m+n;
    }
    exp.setA=function(a){
        q=a;
    }
    exp.getA=function(){
        return a;
    }
    exp.setB=function(b){
       w=b;
    }
    exp.sum=function(){
        return add(q,w);
    }
    window.init=exp;
})();
init.setA(1);
init.setB(2);
init.sum();


function这个关键字即可以当作语句，也可以当作表达式。为了避免解析上的歧义，JavaScript引擎规定，如果function关键字出现在行首，一律解释成语句。因此，JavaScript引擎看到行首是function关键字之后，认为这一段都是函数的定义。
解决方法就是不要让function出现在行首，让引擎将其理解成一个表达式。最简单的处理，就是将其放在一个圆括号里面。
它的目的有两个：一是不必为函数命名，避免了污染全局变量；二是IIFE内部形成了一个单独的作用域，可以封装一些外部无法读取的私有变量


在代码中任何地方都能访问到的对象拥有全局作用域，一般来说以下 3 种情形拥有全局作用域。
最外层函数和在最外层函数外面定义的变量拥有全局作用域
所有末定义直接赋值的变量自动声明为拥有全局作用域
所有window对象的属性拥有全局作用域 


在作用域链
在 JavaScript 中，函数也是对象，实际上，JavaScript 里一切都是对象。
函数对象和其它对象一样，拥有可以通过代码访问的属性和一系列仅供 JavaScript 引擎访问的内部属性。
其中一个内部属性是 [[Scope]]，由 ECMA-262 标准第三版定义，该内部属性包含了函数被创建的作用域中对象的集合，
这个集合被称为函数的作用域链，它决定了哪些数据能被函数访问。
在函数创建时，它的作用域链中会填入一个全局对象，该全局对象包含了所有全局变量。
函数执行时会创建一个称为“运行期上下文(execution context)”的内部对象，运行期上下文定义了函数执行时的环境。
每个运行期上下文都有自己的作用域链，用于标识符解析，当运行期上下文被创建时，
而它的作用域链初始化为当前运行函数的[[Scope]]所包含的对象。
这些值按照它们出现在函数中的顺序被复制到运行期上下文的作用域链中。它们共同组成了一个新的对象，
叫“活动对象(activation object)”，该对象包含了函数的所有局部变量、命名参数、参数集合以及this，然后此对象会被推入作用域链的前端。
当运行期上下文被销毁，活动对象也随之销毁。
在函数执行过程中，每遇到一个变量，都会经历一次标识符解析过程以决定从哪里获取和存储数据。
该过程从作用域链头部，也就是从活动对象开始搜索，查找同名的标识符，如果找到了就使用这个标识符对应的变量，
如果没找到继续搜索作用域链中的下一个对象，如果搜索完所有对象都未找到，则认为该标识符未定义。
函数执行过程中，每个标识符都要经历这样的搜索过程。
对于 innerFun()，其作用域链包含 3 个对象：
innerFun() 自己的变量对象、outFun()的变量对象、全局变量对象。

特例：通过构造器创建的函数是访问不到外层的局部变量的。
function outer() {
    var i = 1;
    var func = new Function("console.log(typeof i);");
    func(); // undefined
}
console.log(outer());

延长作用域链
有些语句可以在作用域链的前端临时增加一个变量对象，
该变量对象会在代码执行后被移除。有两种情况下会发生这种现象。
try-catch 语句中的 catch 块
with 语句
对 with 来说，将会指定对象添加到作用域链中。对 catch 来说，会创建一个新的变量对象，其中包含的是被抛出的错误对象的声明。

函数本身也是一个值，也有自己的作用域。
它的作用域与变量一样，就是其声明时所在的作用域，与其运行时所在的作用域无关。
var a = 1;
var x = function () {
  console.log(a);
};
function f() {
  var a = 2;
  x();
}
console.log(f()) // 1
同样的，函数体内部声明的函数，作用域绑定函数体内部。
function foo() {
  var x = 1;
  function bar() {
    console.log(x);
  }
  return bar;
}
var x = 2;
var f = foo();
console.log(f())//1