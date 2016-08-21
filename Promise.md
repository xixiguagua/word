Promise是抽象异步处理对象以及对其进行各种操作的组件

Constructor

Promise类似于 XMLHttpRequest，从构造函数 Promise 来创建一个新建新promise对象作为接口。
要想创建一个promise对象、可以使用new来调用Promise的构造器来进行实例化。

var promise = new Promise(function(resolve, reject) {
    // 异步处理
    // 处理结束后、调用resolve 或 reject
});

Instance Method

对通过new生成的promise对象为了设置其值在 resolve(成功) / reject(失败)时调用的回调函数 可以使用promise.then() 实例方法。
promise.then(onFulfilled, onRejected)
resolve(成功)时
onFulfilled 会被调用
reject(失败)时
onRejected 会被调用
onFulfilled、onRejected 两个都为可选参数。
promise.then 成功和失败时都可以使用。 另外在只想对异常进行处理时可以采用 promise.then(undefined, onRejected) 这种方式，只指定reject时的回调函数即可。 不过这种情况下 promise.catch(onRejected) 应该是个更好的选择。
promise.catch(onRejected)

Static Method

像 Promise 这样的全局对象还拥有一些静态方法。
包括 Promise.all() 还有 Promise.resolve() 等在内，主要都是一些对Promise进行操作的辅助方法。

function asyncFunction() {
    
    return new Promise(function (resolve, reject) {
        setTimeout(function () {
            resolve('Hello world');
        }, 16);
    });
}

asyncFunction().then(function (value) {
    console.log(value);    // 'Hello world'
}).catch(function (error) {
    console.log(error);
});

new Promise构造器之后，会返回一个promise对象
<1>为promise对象用设置 .then 调用返回值时的回调函数。
asyncFunction 这个函数会返回promise对象， 对于这个promise对象，我们调用它的 then 方法来设置resolve后的回调函数， catch 方法来设置发生错误时的回调函数。

该promise对象会在setTimeout之后的16ms时被resolve, 这时 then 的回调函数会被调用，并输出 'Async Hello world' 。

在这种情况下 catch 的回调函数并不会被执行（因为promise返回了resolve）

asyncFunction().then(function (value) {
    console.log(value);
}, function (error) {
    console.log(error);
});

用new Promise 实例化的promise对象有以下三个状态。

"has-resolution" - Fulfilled
resolve(成功)时。此时会调用 onFulfilled

"has-rejection" - Rejected
reject(失败)时。此时会调用 onRejected

"unresolved" - Pending
既不是resolve也不是reject的状态。也就是promise对象刚被创建后的初始化状态等
关于上面这三种状态的读法，其中 左侧为在 ES6 Promises 规范中定义的术语， 而右侧则是在 Promises/A+ 中描述状态的术语。

promise对象的状态，从Pending转换为Fulfilled或Rejected之后， 这个promise对象的状态就不会再发生任何变化。

也就是说，Promise与Event等不同，在.then 后执行的函数可以肯定地说只会被调用一次。

另外，Fulfilled和Rejected这两个中的任一状态都可以表示为Settled(不变的)。

创建promise对象的流程如下所示。
new Promise(fn) 返回一个promise对象
在fn 中指定异步等处理
处理结果正常的话，调用resolve(处理结果值)
处理结果错误的话，调用reject(Error对象)

function getURL(URL) {
    return new Promise(function (resolve, reject) {
        var req = new XMLHttpRequest();
        req.open('GET', URL, true);
        req.onload = function () {
            if (req.status === 200) {
                resolve(req.responseText);
            } else {
                reject(new Error(req.statusText));
            }
        };
        req.onerror = function () {
            reject(new Error(req.statusText));
        };
        req.send();
    });
}
// 运行示例
var URL = "http://httpbin.org/get";
getURL(URL).then(function onFulfilled(value){
    console.log(value);
}).catch(function onRejected(error){
    console.error(error);
});

getURL 只有在通过XHR取得结果状态为200时才会调用 resolve - 也就是只有数据取得成功时，而其他情况（取得失败）时则会调用 reject 方法。

resolve(req.responseText) 在response的内容中加入了参数。 resolve方法的参数并没有特别的规则，基本上把要传给回调函数参数放进去就可以了。 ( then 方法可以接收到这个参数值)

 .then 中指定的方法调用是异步进行的。
 但是即使在调用 promise.then 注册回调函数的时候promise对象已经是确定的状态，Promise也会以异步的方式调用该回调函数，这是在Promise设计上的规定方针。
var promise = new Promise(function (resolve){
    console.log("inner promise"); // 1
    resolve(42);
});
promise.then(function(value){
    console.log(value); // 3
});
console.log("outer promise"); // 2

inner promise // 1
outer promise // 2
42            // 3

function onReady(fn) {
    var readyState = document.readyState;
    if (readyState === 'interactive' || readyState === 'complete') {
        fn();
    } else {
        window.addEventListener('DOMContentLoaded', fn);
    }
}
onReady(function () {
    console.log('DOM fully loaded and parsed');
});
console.log('==Starting==');

DOM fully loaded and parsed
==Starting==
如果在调用onReady之前DOM已经载入的话
对回调函数进行同步调用
如果在调用onReady之前DOM还没有载入的话
通过注册 DOMContentLoaded 事件监听器来对回调函数进行异步调用
因此，如果这段代码在源文件中出现的位置不同，在控制台上打印的log消息顺序也会不同。

统一使用异步调用的方式。
function onReady(fn) {
    var readyState = document.readyState;
    if (readyState === 'interactive' || readyState === 'complete') {
        setTimeout(fn, 0);
    } else {
        window.addEventListener('DOMContentLoaded', fn);
    }
}
onReady(function () {
    console.log('DOM fully loaded and parsed');
});
console.log('==Starting==');
用Promise重写
function onReadyPromise() {
    return new Promise(function (resolve, reject) {
        var readyState = document.readyState;
        if (readyState === 'interactive' || readyState === 'complete') {
            resolve();
        } else {
            window.addEventListener('DOMContentLoaded', resolve);
        }
    });
}
onReadyPromise().then(function () {
    console.log('DOM fully loaded and parsed');
});
console.log('==Starting==');

then 的promise chain的行为和流程
function taskA() {
    console.log("Task A");
}
function taskB() {
    console.log("Task B");
}
function onRejected(error) {
    console.log("Catch Error: A or B", error);
}
function finalTask() {
    console.log("Final Task");
}

var promise = Promise.resolve();
promise
    .then(taskA)
    .then(taskB)
    .catch(onRejected)
    .then(finalTask);

//Task A
Task B
Final Task
Task A产生异常的例子TaskA → onRejected → FinalTask 这个流程Task B 是不会被调用的。

如果 Task A 想给 Task B 传递一个参数该怎么办呢？
那就是在 Task A 中 return 的返回值，会在 Task B 执行时传给它。

function doubleUp(value) {
    return value * 2;
}
function increment(value) {
    return value + 1;
}
function output(value) {
    console.log(value);// => (1 + 1) * 2
}

var promise = Promise.resolve(1);
promise
    .then(increment)
    .then(doubleUp)
    .then(output)
    .catch(function(error){
        // promise chain中出现异常的时候会被调用
        console.error(error);
    });
  //4
  Promise.resolve(1); 传递 1 给 increment 函数
函数 increment 对接收的参数进行 +1 操作并返回（通过return）
这时参数变为2，并再次传给 doubleUp 函数
最后在函数 output 中打印结果
每个方法中 return 的值不仅只局限于字符串或者数值类型，也可以是对象或者promise对象等复杂类型

每次调用then都会返回一个新创建的promise对象
var aPromise = new Promise(function (resolve) {
    resolve(100);
});
var thenPromise = aPromise.then(function (value) {
    console.log(value);
});
var catchPromise = thenPromise.catch(function (error) {
    console.error(error);
});
console.log(aPromise !== thenPromise); // => true
console.log(thenPromise !== catchPromise);// => true

第1种写法中并没有使用promise的方法链方式，这在Promise中是应该极力避免的写法。这种写法中的 then 调用几乎是在同时开始执行的，而且传给每个 then 方法的 value 值都是 100 。

第2中写法则采用了方法链的方式将多个 then 方法调用串连在了一起，各函数也会严格按照 resolve → then → then → then 的顺序执行，并且传给每个 then 方法的 value 的值都是前一个promise对象通过 return 返回的值。
// 1: 对同一个promise对象同时调用 `then` 方法
var aPromise = new Promise(function (resolve) {
    resolve(100);
});
aPromise.then(function (value) {
    return value * 2;
});
aPromise.then(function (value) {
    return value * 2;
});
aPromise.then(function (value) {
    console.log("1: " + value); // => 100
})

// vs

// 2: 对 `then` 进行 promise chain 方式进行调用
var bPromise = new Promise(function (resolve) {
    resolve(100);
});
bPromise.then(function (value) {
    return value * 2;
}).then(function (value) {
    return value * 2;
}).then(function (value) {
    console.log("2: " + value); // => 100 * 2 * 2
});

返回返回新创建的promise对象
function anAsyncCall() {
    var promise = Promise.resolve();
    return promise.then(function() {
        // 任意处理
        return newVar;
    });
}

通过回调方式来进行多个异步调用
function getURLCallback(URL, callback) {
    var req = new XMLHttpRequest();
    req.open('GET', URL, true);
    req.onload = function () {
        if (req.status === 200) {
            callback(null, req.responseText);
        } else {
            callback(new Error(req.statusText), req.response);
        }
    };
    req.onerror = function () {
        callback(new Error(req.statusText));
    };
    req.send();
}
// <1> 对JSON数据进行安全的解析
function jsonParse(callback, error, value) {
    if (error) {
        callback(error, value);
    } else {
        try {
            var result = JSON.parse(value);
            callback(null, result);
        } catch (e) {
            callback(e, value);
        }
    }
}
// <2> 发送XHR请求
var request = {
        comment: function getComment(callback) {
            return getURLCallback('http://azu.github.io/promises-book/json/comment.json', jsonParse.bind(null, callback));
        },
        people: function getPeople(callback) {
            return getURLCallback('http://azu.github.io/promises-book/json/people.json', jsonParse.bind(null, callback));
        }
    };
// <3> 启动多个XHR请求，当所有请求返回时调用callback
function allRequest(requests, callback, results) {
    if (requests.length === 0) {
        return callback(null, results);
    }
    var req = requests.shift();
    req(function (error, value) {
        if (error) {
            callback(error, value);
        } else {
            results.push(value);
            allRequest(requests, callback, results);
        }
    });
}
function main(callback) {
    allRequest([request.comment, request.people], callback, []);
}
// 运行的例子
main(function(error, results){
    if(error){
        return console.error(error);
    }
    console.log(results);
});

用 Promise#then 来完成同样的工作。
function getURL(URL) {
    return new Promise(function (resolve, reject) {
        var req = new XMLHttpRequest();
        req.open('GET', URL, true);
        req.onload = function () {
            if (req.status === 200) {
                resolve(req.responseText);
            } else {
                reject(new Error(req.statusText));
            }
        };
        req.onerror = function () {
            reject(new Error(req.statusText));
        };
        req.send();
    });
}
var request = {
        comment: function getComment() {
            return getURL('http://azu.github.io/promises-book/json/comment.json').then(JSON.parse);
        },
        people: function getPeople() {
            return getURL('http://azu.github.io/promises-book/json/people.json').then(JSON.parse);
        }
    };
function main() {
    function recordValue(results, value) {
        results.push(value);
        return results;
    }
    // [] 用来保存初始化的值
    var pushValue = recordValue.bind(null, []);
    return request.comment().then(pushValue).then(request.people).then(pushValue);
}
// 运行的例子
main().then(function (value) {
    console.log(value);
}).catch(function(error){
    console.error(error);
});

Promise.all 接收一个 promise对象的数组作为参数，当这个数组里的所有promise对象全部变为resolve或reject状态的时候，它才会去调用 .then 方法。
之前例子中的 getURL 返回了一个promise对象，它封装了XHR通信的实现。 向 Promise.all 传递一个由封装了XHR通信的promise对象数组的话，则只有在全部的XHR通信完成之后（变为FulFilled或Rejected状态）之后，才会调用 .then 方法。

function getURL(URL) {
    return new Promise(function (resolve, reject) {
        var req = new XMLHttpRequest();
        req.open('GET', URL, true);
        req.onload = function () {
            if (req.status === 200) {
                resolve(req.responseText);
            } else {
                reject(new Error(req.statusText));
            }
        };
        req.onerror = function () {
            reject(new Error(req.statusText));
        };
        req.send();
    });
}
var request = {
        comment: function getComment() {
            return getURL('http://azu.github.io/promises-book/json/comment.json').then(JSON.parse);
        },
        people: function getPeople() {
            return getURL('http://azu.github.io/promises-book/json/people.json').then(JSON.parse);
        }
    };
function main() {
    return Promise.all([request.comment(), request.people()]);
}
// 运行示例
main().then(function (value) {
    console.log(value);
}).catch(function(error){
    console.log(error);
});

在上面的代码中，request.comment() 和 request.people() 会同时开始执行，而且每个promise的结果（resolve或reject时传递的参数值），和传递给 Promise.all 的promise数组的顺序是一致的。

传递给 Promise.all 的promise数组是同时开始执行的。
// `delay`毫秒后执行resolve
function timerPromisefy(delay) {
    return new Promise(function (resolve) {
        setTimeout(function () {
            resolve(delay);
        }, delay);
    });
}
var startDate = Date.now();
// 所有promise变为resolve后程序退出
Promise.all([
    timerPromisefy(1),
    timerPromisefy(32),
    timerPromisefy(64),
    timerPromisefy(128)
]).then(function (values) {
    console.log(Date.now() - startDate + 'ms');
    // 約128ms
    console.log(values);    // [1,32,64,128]
});

timerPromisefy 会每隔一定时间（通过参数指定）之后，返回一个promise对象，状态为FulFilled，其状态值为传给 timerPromisefy 的参数。

而传给 Promise.all 的则是由上述promise组成的数组。

var promises = [
    timerPromisefy(1),
    timerPromisefy(32),
    timerPromisefy(64),
    timerPromisefy(128)
];
这时候，每隔1, 32, 64, 128 ms都会有一个promise发生 resolve 行为。

也就是说，这个promise对象数组中所有promise都变为resolve状态的话，至少需要128ms。实际我们计算一下Promise.all 的执行时间的话，它确实是消耗了128ms的时间。
从上述结果可以看出，传递给 Promise.all 的promise并不是一个个的顺序执行的，而是同时开始、并行执行的。

Promise.all 在接收到的所有的对象promise都变为 FulFilled 或者 Rejected 状态之后才会继续进行后面的处理， 与之相对的是 Promise.race 只要有一个promise对象进入 FulFilled 或者 Rejected 状态的话，就会继续进行后面的处理。

// `delay`毫秒后执行resolve
function timerPromisefy(delay) {
    return new Promise(function (resolve) {
        setTimeout(function () {
            resolve(delay);
        }, delay);
    });
}
// 任何一个promise变为resolve或reject 的话程序就停止运行
Promise.race([
    timerPromisefy(1),
    timerPromisefy(32),
    timerPromisefy(64),
    timerPromisefy(128)
]).then(function (value) {
    console.log(value);    // => 1
});

上面的代码创建了4个promise对象，这些promise对象会分别在1ms，32ms，64ms和128ms后变为确定状态，即FulFilled，并且在第一个变为确定状态的1ms后， .then 注册的回调函数就会被调用，这时候确定状态的promise对象会调用 resolve(1) 因此传递给 value 的值也是1，控制台上会打印出1来。

var winnerPromise = new Promise(function (resolve) {
        setTimeout(function () {
            console.log('this is winner');
            resolve('this is winner');
        }, 4);
    });
var loserPromise = new Promise(function (resolve) {
        setTimeout(function () {
            console.log('this is loser');
            resolve('this is loser');
        }, 1000);
    });
// 第一个promise变为resolve后程序停止
Promise.race([winnerPromise, loserPromise]).then(function (value) {
    console.log(value);    // => 'this is winner'
});

this is winner
this is winner
this is loser
执行上面代码的话，我们会看到 winnter和loser promise对象的 setTimeout 方法都会执行完毕， console.log 也会分别输出它们的信息。

也就是说， Promise.race 在第一个promise对象变为Fulfilled之后，并不会取消其他promise对象的执行。