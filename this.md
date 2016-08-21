首先，this总是返回一个对象，简单说，就是返回属性或方法“当前”所在的对象。
总结一下，JavaScript语言之中，一切皆对象，运行环境也是对象，所以函数都是在某个对象之中运行，this就是这个对象（环境）。这本来并不会让用户糊涂，但是JavaScript支持运行环境动态切换，也就是说，this的指向是动态的，没有办法事先确定到底指向哪个对象.
如果一个函数在全局环境中运行，那么this就是指顶层对象（浏览器中为window对象）。

this的使用可以分成以下几个场合。
（1）全局环境
在全局环境使用this，它指的就是顶层对象window。
this === window // true

function f() {
  console.log(this === window); // true
}
2）构造函数
构造函数中的this，指的是实例对象。
var Obj = function (p) {
  this.p = p;
};

Obj.prototype.m = function() {
  return this.p;
};
var o = new Obj('Hello World!');

o.p // "Hello World!"
o.m() // "Hello World!"

（3）对象的方法
当A对象的方法被赋予B对象，该方法中的this就从指向A对象变成了指向B对象。
var obj ={
  foo: function () {
    console.log(this);
  }
};

obj.foo() // obj

但是，只有这一种用法（直接在obj对象上调用foo方法），this指向obj；其他用法时，this都指向代码块当前所在对象（浏览器为window对象）。

// 情况一
(obj.foo = obj.foo)() // window

// 情况二
(false || obj.foo)() // window

// 情况三
(1, obj.foo)() // window
上面代码中，obj.foo先运算再执行，即使它的值根本没有变化，this也不再指向obj了。

可以这样理解，在JavaScript引擎内部，obj和obj.foo储存在两个内存地址，简称为M1和M2。只有obj.foo()这样调用时，是从M1调用M2，因此this指向obj。但是，上面三种情况，都是直接取出M2进行运算，然后就在全局环境执行运算结果（还是M2），因此this指向全局环境。
上面三种情况等同于下面的代码。

// 情况一
(obj.foo = function () {
  console.log(this);
})()

// 情况二
(false || function () {
  console.log(this);
})()

// 情况三
(1, function () {
  console.log(this);
})()

同样的，如果某个方法位于多层对象的内部，这时为了简化书写，把该方法赋值给一个变量，往往会得到意料之外的结果。

var a = {
  b: {
    m: function() {
      console.log(this.p);
    },
    p: 'Hello'
  }
};

var hello = a.b.m;
hello() // undefined
上面代码中，m是多层对象内部的一个方法。为求简便，将其赋值给hello变量，结果调用时，this指向了顶层对象。为了避免这个问题，可以只将m所在的对象赋值给hello，这样调用时，this的指向就不会变。

var hello = a.b;
hello.m() 

(4）Node
在Node中，this的指向又分成两种情况。全局环境中，this指向全局对象global；模块环境中，this指向module.exports。


由于this的指向是不确定的，所以切勿在函数中包含多层的this。

var o = {
  f1: function () {
    console.log(this);
    var f2 = function () {
      console.log(this);
    }();
  }
}

o.f1()
// Object
// Window
上面代码包含两层this，结果运行后，第一层指向该对象，第二层指向全局对象。

一个解决方法是在第二层改用一个指向外层this的变量。

var o = {
  f1: function() {
    console.log(this);
    var that = this;
    var f2 = function() {
      console.log(that);
    }();
  }
}

o.f1()
// Object
// Object

JavaScript 提供了严格模式，也可以硬性避免这种问题。在严格模式下，如果函数内部的this指向顶层对象，就会报错。

avaScript提供了call、apply、bind这三个方法，来切换/固定this的指向。

函数实例的call方法，可以指定该函数内部this的指向（即函数执行时所在的作用域），然后在所指定的作用域中，调用该函数。

apply方法的作用与call方法类似，也是改变this指向，然后再调用该函数。唯一的区别就是，它接收一个数组作为函数执行时的参数
通过apply方法，利用Array构造函数将数组的空元素变成undefined。
Array.apply(null, ["a",,"b"])
// [ 'a', undefined, 'b' ]
空元素与undefined的差别在于，数组的forEach方法会跳过空元素，但是不会跳过undefined。因此，遍历内部元素的时候，会得到不同的结果。
利用数组对象的slice方法，可以将一个类似数组的对象（比如arguments对象）转为真正的数组。
Array.prototype.slice.apply({0:1,length:1})
// [1]

Array.prototype.slice.apply({0:1})
// []

Array.prototype.slice.apply({0:1,length:2})
// [1, undefined]

Array.prototype.slice.apply({length:1})
// [undefined]
（4）绑定回调函数的对象

bind方法用于将函数体内的this绑定到某个对象，然后返回一个新函数。
如果bind方法的第一个参数是null或undefined，等于将this绑定到全局对象，函数运行时this指向顶层对象（在浏览器中为window）。
bind方法每运行一次，就返回一个新函数，这会产生一些问题。比如，监听事件的时候，不能写成下面这样。
element.addEventListener('click', o.m.bind(o));
上面代码中，click事件绑定bind方法生成的一个匿名函数。这样会导致无法取消绑定，所以，下面的代码是无效的。