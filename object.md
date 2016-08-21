
JavaScript的每个对象都继承另一个对象，后者称为“原型”（prototype）对象。只有null除外，它没有自己的原型对象。

原型对象上的所有属性和方法，都能被派生对象共享。这就是JavaScript继承机制的基本设计。

通过构造函数生成实例对象时，会自动为实例对象分配原型对象。每一个构造函数都有一个prototype属性，这个属性就是实例对象的原型对象。

    function Animal (name) {
      this.name = name;
    }

    Animal.prototype.color = 'white';

    var cat1 = new Animal('大毛');
    var cat2 = new Animal('二毛');

    cat1.color // 'white'
    cat2.color // 'white'

上面代码中，构造函数Animal的prototype对象，就是实例对象cat1和cat2的原型对象。在原型对象上添加一个color属性。结果，实例对象都能读取该属性。

原型对象的属性不是实例对象自身的属性。只要修改原型对象，变动就立刻会体现在所有实例对象上。

    Animal.prototype.color = 'yellow';

    cat1.color // "yellow"
    cat2.color // "yellow"

上面代码中，原型对象的color属性的值变为yellow，两个实例对象的color属性立刻跟着变了。

这是因为实例对象其实没有color属性，都是读取原型对象的color属性。

也就是说，当实例对象本身没有某个属性或方法的时候，它会到构造函数的prototype属性指向的对象，去寻找该属性或方法。这就是原型对象的特殊之处。

如果实例对象自身就有某个属性或方法，它就不会再去原型对象寻找这个属性或方法。

    cat1.color = 'black';

    cat2.color // 'yellow'
    Animal.prototype.color // "yellow";

上面代码中，实例对象cat1的color属性改为black，就使得它不再去原型对象读取color属性，后者的值依然为yellow。

总结一下，原型对象的作用，就是定义所有实例对象共享的属性和方法。这也是它被称为原型对象的含义，而实例对象可以视作从原型对象衍生出来的子对象。

    Animal.prototype.walk = function () {
      console.log(this.name + ' is walking');
    };

上面代码中，Animal.protype对象上面定义了一个walk方法，这个方法将可以在所有Animal实例对象上面调用。

由于JavaScript的所有对象都有构造函数，而所有构造函数都有prototype属性（其实是所有函数都有prototype属性），所以所有对象都有自己的原型对象。


对象的属性和方法，有可能是定义在自身，也有可能是定义在它的原型对象。由于原型本身也是对象，又有自己的原型，所以形成了一条原型链（prototype chain）。

比如，a对象是b对象的原型，b对象是c对象的原型，以此类推。

如果一层层地上溯，所有对象的原型最终都可以上溯到Object.prototype，即Object构造函数的prototype属性指向的那个对象。

那么，Object.prototype对象有没有它的原型呢？回答可以是有的，就是没有任何属性和方法的null对象，而null对象没有自己的原型。

    Object.getPrototypeOf(Object.prototype)
    // null

上面代码表示，Object.prototype对象的原型是null，由于null没有任何属性，所以原型链到此为止。

“原型链”的作用是，读取对象的某个属性时，JavaScript引擎先寻找对象本身的属性，如果找不到，就到它的原型去找，如果还是找不到，就到原型的原型去找。

如果直到最顶层的Object.prototype还是找不到，则返回undefined。

如果对象自身和它的原型，都定义了一个同名属性，那么优先读取对象自身的属性，这叫做“覆盖”（overiding）。

需要注意的是，一级级向上，在原型链寻找某个属性，对性能是有影响的。

所寻找的属性在越上层的原型对象，对性能的影响越大。如果寻找某个不存在的属性，将会遍历整个原型链。

举例来说，如果让某个函数的prototype属性指向一个数组，就意味着该函数可以当作数组的构造函数，因为它生成的实例对象都可以通过prototype属性调用数组方法。

      var MyArray = function () {};

      MyArray.prototype = new Array();
      MyArray.prototype.constructor = MyArray;

      var mine = new MyArray();
      mine.push(1, 2, 3);

      mine.length // 3
      mine instanceof Array // true

上面代码中，mine是构造函数MyArray的实例对象，由于MyArray的prototype属性指向一个数组实例，使得mine可以调用数组方法（这些方法定义在数组实例的prototype对象上面）。

至于最后那行instanceof表达式，我们知道instanceof运算符用来比较一个对象是否为某个构造函数的实例，最后一行就表示mine为Array的实例。

    mine instanceof Array

    // 等同于

    (Array === MyArray.prototype.constructor) ||
    (Array === Array.prototype.constructor) ||
    (Array === Object.prototype.constructor )

上面代码说明了instanceof运算符的实质，它依次与实例对象的所有原型对象的constructor属性（关于该属性的介绍，请看下一节）进行比较，只要有一个符合就返回true，否则返回false。

下面的代码可以找出，某个属性到底是原型链上哪个对象自身的属性。

    function getDefiningObject(obj, propKey) {
      while (obj && !{}.hasOwnProperty.call(obj, propKey)) {
        obj = Object.getPrototypeOf(obj);
      }
      return obj;
    }

prototype对象有一个constructor属性，默认指向prototype对象所在的构造函数。

    function P() {}

    P.prototype.constructor === P
    // true

由于constructor属性定义在prototype对象上面，意味着可以被所有实例对象继承。

      function P() {}
      var p = new P();

      p.constructor
      // function P() {}

      p.constructor === P.prototype.constructor
      // true

      p.hasOwnProperty('constructor')
      // false

上面代码中，p是构造函数P的实例对象，但是p自身没有contructor属性，该属性其实是读取原型链上面的P.prototype.constructor属性。

constructor属性的作用，是分辨原型对象到底属于哪个构造函数。

    function F() {};
    var f = new F();

    f.constructor === F // true
    f.constructor === RegExp // false

上面代码表示，使用constructor属性，确定实例对象f的构造函数是F，而不是RegExp。

有了constructor属性，就可以从实例新建另一个实例。

    function Constr() {}
    var x = new Constr();

    var y = new x.constructor();
    y instanceof Constr // true

上面代码中，x是构造函数Constr的实例，可以从x.constructor间接调用构造函数。

这使得在实例方法中，调用自身的构造函数成为可能。

    Constr.prototype.createCopy = function () {
      return new this.constructor();
    };

这也提供了继承模式的一种实现。

    function Super() {}

    function Sub() {
      Sub.superclass.constructor.call(this);
    }

    Sub.superclass = new Super();

上面代码中，Super和Sub都是构造函数，在Sub内部的this上调用Super，就会形成Sub继承Super的效果。

由于constructor属性是一种原型对象与构造函数的关联关系，所以修改原型对象的时候，务必要小心。

    function A() {}
    var a = new A();
    a instanceof A // true

    function B() {}
    A.prototype = B.prototype;
    a instanceof A // false

上面代码中，a是A的实例。修改了A.prototype以后，constructor属性的指向就变了，导致instanceof运算符失真。

所以，修改原型对象时，一般要同时校正constructor属性的指向。

    // 避免这种写法
    C.prototype = {
      method1: function (...) { ... },
      // ...
    };

    // 较好的写法
    C.prototype = {
      constructor: C,
      method1: function (...) { ... },
      // ...
    };

    // 好的写法
    C.prototype.method1 = function (...) { ... };

上面代码中，避免完全覆盖掉原来的prototype属性，要么将constructor属性重新指向原来的构造函数，要么只在原型对象上添加方法，这样可以保证instanceof运算符不会失真。

此外，通过name属性，可以从实例得到构造函数的名称。

    function Foo() {}
    var f = new Foo();
    f.constructor.name // "Foo"

Object.getPrototypeOf方法返回一个对象的原型。这是获取原型对象的标准方法。

      // 空对象的原型是Object.prototype
      Object.getPrototypeOf({}) === Object.prototype
      // true

      // 函数的原型是Function.prototype
      function f() {}
      Object.getPrototypeOf(f) === Function.prototype
      // true

      // f 为 F 的实例对象，则 f 的原型是 F.prototype
      var f = new F();
      Object.getPrototypeOf(f) === F.prototype
      //true

Object.create方法用于从原型对象生成新的实例对象，可以替代new命令。

它接受一个对象作为参数，返回一个新对象，后者完全继承前者的属性，即原有对象成为新对象的原型。

      var A = {
       print: function () {
         console.log('hello');
       }
      };

      var B = Object.create(A);

      B.print // hello
      B.print === A.print // true

上面代码中，Object.create方法在A的基础上生成了B。此时，A就成了B的原型，B就继承了A的所有属性和方法。这段代码等同于下面的代码。

    var A = function () {};
    A.prototype = {
     print: function () {
       console.log('hello');
     }
    };

    var B = new A();

    B.print === A.prototype.print // true

实际上，Object.create方法可以用下面的代码代替。如果老式浏览器不支持Object.create方法，可以就用这段代码自己部署。

    if (typeof Object.create !== 'function') {
      Object.create = function (o) {
        function F() {}
        F.prototype = o;
        return new F();
      };
    }

上面代码表示，Object.create方法实质是新建一个构造函数F，然后让F的prototype属性指向作为原型的对象o，最后返回一个F的实例，从而实现让实例继承o的属性。

下面三种方式生成的新对象是等价的。

    var o1 = Object.create({});
    var o2 = Object.create(Object.prototype);
    var o3 = new Object();

如果想要生成一个不继承任何属性（比如没有toString和valueOf方法）的对象，可以将Object.create的参数设为null。

      var o = Object.create(null);

      o.valueOf()
      // TypeError: Object [object Object] has no method 'valueOf'

上面代码表示，如果对象o的原型是null，它就不具备一些定义在Object.prototype对象上面的属性，比如valueOf方法。

使用Object.create方法的时候，必须提供对象原型，否则会报错。

    Object.create()
    // TypeError: Object prototype may only be an Object or null

object.create方法生成的新对象，动态继承了原型。在原型上添加或修改任何方法，会立刻反映在新对象之上。

    var o1 = { p: 1 };
    var o2 = Object.create(o1);

    o1.p = 2;
    o2.p
    // 2

上面代码表示，修改对象原型会影响到新生成的对象。

除了对象的原型，Object.create方法还可以接受第二个参数。该参数是一个属性描述对象，它所描述的对象属性，会添加到新对象。

var o = Object.create({}, {
      p1: { value: 123, enumerable: true },
      p2: { value: 'abc', enumerable: true }
    });

    // 等同于
    var o = Object.create({});
    o.p1 = 123;
    o.p2 = 'abc';

Object.create方法生成的对象，继承了它的原型对象的构造函数。

    function A() {}
    var a = new A();
    var b = Object.create(a);

    b.constructor === A // true
    b instanceof A // true

上面代码中，b对象的原型是a对象，因此继承了a对象的构造函数A。


对象实例的isPrototypeOf方法，用来判断一个对象是否是另一个对象的原型。

    var o1 = {};
    var o2 = Object.create(o1);
    var o3 = Object.create(o2);

    o2.isPrototypeOf(o3) // true
    o1.isPrototypeOf(o3) // true

上面代码表明，只要某个对象处在原型链上，isProtypeOf都返回true。

Object.getOwnPropertyNames方法返回一个数组，成员是对象本身的所有属性的键名，不包含继承的属性键名。

    Object.getOwnPropertyNames(Date)
    // ["parse", "arguments", "UTC", "caller", "name", "prototype", "now", "length"]

上面代码中，Object.getOwnPropertyNames方法返回Date所有自身的属性名。

对象本身的属性之中，有的是可以枚举的（enumerable），有的是不可以枚举的，Object.getOwnPropertyNames方法返回所有键名。只获取那些可以枚举的属性，使用Object.keys方法。

    Object.keys(Date) // []


对象实例的hasOwnProperty方法返回一个布尔值，用于判断某个属性定义在对象自身，还是定义在原型链上。
  
    Date.hasOwnProperty('length')
    // true

    Date.hasOwnProperty('toString')
    // false

hasOwnProperty方法是JavaScript之中唯一一个处理对象属性时，不会遍历原型链的方法。



如果要拷贝一个对象，需要做到下面两件事情。

确保拷贝后的对象，与原对象具有同样的prototype原型对象。
确保拷贝后的对象，与原对象具有同样的属性。
下面就是根据上面两点，编写的对象拷贝的函数。

    function copyObject(orig) {
      var copy = Object.create(Object.getPrototypeOf(orig));
      copyOwnPropertiesFrom(copy, orig);
      return copy;
    }

    function copyOwnPropertiesFrom(target, source) {
      Object
      .getOwnPropertyNames(source)
      .forEach(function(propKey) {
        var desc = Object.getOwnPropertyDescriptor(source, propKey);
        Object.defineProperty(target, propKey, desc);
      });
      return target;
    }




构建类

第一种构造函数法

用构造函数模拟"类"，在其内部用this关键字指代实例对象。

主要缺点是，比较复杂，用到了this和prototype，编写和阅读都很费力

新建实例cat1，cat3互不影响

      function cat(){
          // console.log(this)
          this.name='mao';
      }
      cat.prototype.sound=function(){
          console.log("miao");
      }
      cat.prototype.age=function(x){
          this.age=x;
      }
      cat.prototype.getage=function(){
          return this.age;
      }
      var cat1=new cat();
      console.log(cat1.name);//mao
      cat1.age(2)
      console.log(cat1.getage())//2
      console.log(cat1)//cat { name: 'mao', age: 2 }
      var cat3=new cat();
      cat3.age(3);
      console.log(cat3.name);//mao
      console.log(cat3.getage())//3
      console.log(cat3)//cat { name: 'mao', age: 3 }
      console.log(cat1.age);//2

第二种Object.create()法

不能实现私有属性和私有方法，实例对象之间也不能共享数据

      var Cat={
          name:'mao',
          sound:function(){
              console.log('miao');
          }
      }

      var cat2=Object.create(Cat);
      console.log(cat2.name);//mao
      console.log(cat2.sound());//miao
      cat2.age=3;
      console.log(cat2.age)//3
      console.log(cat2)//{ age: 3 }
      var cat6=Object.create(Cat);
      console.log(cat6.name);
      console.log(cat6.sound());
      cat6.age=6;
      console.log(cat6.age)//6
      console.log(cat6)//{ age: 6 }

      //兼容Object.create
      if(!Object.create){
          Object.create=function(o){
              function F(){}
              F.prototype=o;
               return new F();
          }
      }

极简主义法

    var Cat={
        createNew:function(){
            var cat={};
            cat.name='mao';
            cat.sound=function(){
                console.log('miao');
            }
            return cat;
        }
    };

    var cat4=Cat.createNew();
    cat4.sound()//miao
    cat4.age=4;
    console.log(cat4.age)//4
    console.log(cat4)//{ name: 'mao', sound: [Function], age: 4 }
    var cat5=Cat.createNew();
    cat5.sound();
    cat5.age=5;
    console.log(cat5.age);//5
    console.log(cat5)//{ name: 'mao', sound: [Function], age: 5 }
    console.log(cat4.age);//4
    //类的继承
    var Animal={
        createNew:function(){
            var animal={};
            animal.sleep=function(){
                console.log('sleep');
            }
            return animal;
        }
    }
    // //继承Animal
    var Qcat={
        createNew:function(){
            var cat=Animal.createNew();
            cat.name='mao';
            cat.sound=function(){
                console.log("miao");
            }
            return cat;
        }
    }
    var cat7=Qcat.createNew();
    cat7.sleep();//sleep
    cat7.sound();//miao

私有属性和私有方法:只要不是定义在cat上的方法都是私有的

内部变量sound，外部无法读取，只有通过cat的公有方法makeSound()来读取。

 数据共享:有时候，我们需要所有实例对象，能够读写同一项内部数据。

这个时候，只要把这个内部数据，封装在类对象的里面、createNew()方法的外面即可

      var Qcat={
          eat:'fish',             //共享数据
          createNew:function(){
              var cat=Animal.createNew();
              cat.name='mao';
              var soundword='miao';        //私有
              cat.sound=function(){
                  console.log(soundword);
              }
              cat.eatwhat=function(){
                  console.log(Qcat.eat);
              }
              cat.eatchange=function(x){
                  Qcat.eat=x;
              }
              return cat;
          }
      }
      var cat8 = Qcat.createNew();
      console.log(cat8.soundword);//undefined
      cat8.sound()//miao
      cat8.eatwhat();//fish
      var cat9 = Qcat.createNew();
      console.log(cat9.soundword);//undefined
      cat9.sound()//miao
      cat9.eatwhat();//fish
      cat8.eatchange('allfish');
      cat8.eatwhat();//allfish;
      cat9.eatwhat();//allfish;
      cat9.age=9;
      console.log(cat8)//{ sleep: [Function],name: 'mao',sound: [Function],eatwhat: [Function],eatchange: [Function] }
      console.log(cat9);//{ sleep: [Function], name: 'mao',sound: [Function],eatwhat: [Function],eatchange: [Function] ,age: 9}


让一个构造函数继承另一个构造函数，是非常常见的需求。

这可以分成两步实现。第一步是在子类的构造函数中，调用父类的构造函数。

    function Sub(value) {
      Super.call(this);
      this.prop = value;
    }

上面代码中，Sub是子类的构造函数，this是子类的实例。在实例上调用父类的构造函数Super，就会让子类实例具有父类实例的属性。

第二步，是让子类的原型指向父类的原型，这样子类就可以继承父类原型。

    Sub.prototype = Object.create(Super.prototype);
    Sub.prototype.constructor = Sub;
    Sub.prototype.method = '...';

上面代码中，Sub.prototype是子类的原型，要将它赋值为Object.create(Super.prototype)，而不是直接等于Super.prototype。否则后面两行对Sub.prototype的操作，会连父类的原型Super.prototype一起修改掉。

另外一种写法是Sub.prototype等于一个父类实例。

Sub.prototype = new Super();

上面这种写法也有继承的效果，但是子类会具有父类实例的方法。有时，这可能不是我们需要的，所以不推荐使用这种写法。

举例来说，下面是一个Shape构造函数。

    function Shape() {
      this.x = 0;
      this.y = 0;
    }

    Shape.prototype.move = function (x, y) {
      this.x += x;
      this.y += y;
      console.info('Shape moved.');
    };

我们需要让Rectangle构造函数继承Shape。

    // 第一步，子类继承父类的实例
    function Rectangle() {
      Shape.call(this); // 调用父类构造函数
    }
    // 另一种写法
    function Rectangle() {
      this.base = Shape;
      this.base();
    }

    // 第二步，子类继承父类的原型
    Rectangle.prototype = Object.create(Shape.prototype);
    Rectangle.prototype.constructor = Rectangle;

采用这样的写法以后，instanceof运算符会对子类和父类的构造函数，都返回true。

    var rect = new Rectangle();
    rect.move(1, 1) // 'Shape moved.'

    rect instanceof Rectangle  // true
    rect instanceof Shape  // true

上面代码中，子类是整体继承父类。有时只需要单个方法的继承，这时可以采用下面的写法。

    ClassB.prototype.print = function() {
      ClassA.prototype.print.call(this);
      // some code
    }

上面代码中，子类B的print方法先调用父类A的print方法，再部署自己的代码。这就等于继承了父类A的print方法。

JavaScript不提供多重继承功能，即不允许一个对象同时继承多个对象。但是，可以通过变通方法，实现这个功能。

    function M1() {
      this.hello = 'hello';
    }

    function M2() {
      this.world = 'world';
    }

    function S() {
      M1.call(this);
      M2.call(this);
    }
    S.prototype = M1.prototype;

    var s = new S();
    s.hello // 'hello'
    s.world // 'world'

上面代码中，子类S同时继承了父类M1和M2。当然，从继承链来看，S只有一个父类M1，但是由于在S的实例上，同时执行M1和M2的构造函数，所以它同时继承了这两个类的方法。



以下参考[https://github.com/ecomfe/spec]

类的继承方案，实现时需要修正 constructor。

通常使用其他 library 的类继承方案都会进行 constructor 修正。如果是自己实现的类继承方案，需要进行 constructor 修正。

示例：

/**
 * 构建类之间的继承关系
 *
 * @param {Function} subClass 子类函数
 * @param {Function} superClass 父类函数
 */
function inherits(subClass, superClass) {
    var F = new Function();
    F.prototype = superClass.prototype;
    subClass.prototype = new F();
    subClass.prototype.constructor = subClass;
}
 声明类时，保证 constructor 的正确性。

示例：

function Animal(name) {
    this.name = name;
}

// 直接prototype等于对象时，需要修正constructor
Animal.prototype = {
    constructor: Animal,

    jump: function () {
        alert('animal ' + this.name + ' jump');
    }
};

// 这种方式扩展prototype则无需理会constructor
Animal.prototype.jump = function () {
    alert('animal ' + this.name + ' jump');
};
 属性在构造函数中声明，方法在原型中声明。

解释：

原型对象的成员被所有实例共享，能节约内存占用。所以编码时我们应该遵守这样的原则：原型对象包含程序不会修改的成员，如方法函数或配置项。

function TextNode(value, engine) {
    this.value = value;
    this.engine = engine;
}

TextNode.prototype.clone = function () {
    return this;
};
[强制] 自定义事件的 事件名 必须全小写。

解释：

在 JavaScript 广泛应用的浏览器环境，绝大多数 DOM 事件名称都是全小写的。为了遵循大多数 JavaScript 开发者的习惯，在设计自定义事件时，事件名也应该全小写。

[强制] 自定义事件只能有一个 event 参数。如果事件需要传递较多信息，应仔细设计事件对象。

解释：

一个事件对象的好处有：

顺序无关，避免事件监听者需要记忆参数顺序。
每个事件信息都可以根据需要提供或者不提供，更自由。
扩展方便，未来添加事件信息时，无需考虑会破坏监听器参数形式而无法向后兼容。
 设计自定义事件时，应考虑禁止默认行为。

解释：

常见禁止默认行为的方式有两种：

事件监听函数中 return false。
事件对象中包含禁止默认行为的方法，如 preventDefault。
3.10 动态特性

3.10.1 eval

[强制] 避免使用直接 eval 函数。

解释：

直接 eval，指的是以函数方式调用 eval 的调用方法。直接 eval 调用执行代码的作用域为本地作用域，应当避免。

如果有特殊情况需要使用直接 eval，需在代码中用详细的注释说明为何必须使用直接 eval，不能使用其它动态执行代码的方式，同时需要其他资深工程师进行 Code Review。

 尽量避免使用 eval 函数。

3.10.2 动态执行代码

 使用 new Function 执行动态代码。

解释：

通过 new Function 生成的函数作用域是全局使用域，不会影响当当前的本地作用域。如果有动态代码执行的需求，建议使用 new Function。

示例：

var handler = new Function('x', 'y', 'return x + y;');
var result = handler($('#x').val(), $('#y').val());