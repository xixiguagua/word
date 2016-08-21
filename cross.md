跨域

设置 document.domain

原理：相同主域名不同子域名下的页面，可以设置document.domain让它们同域

限制：同域document提供的是页面间的互操作，需要载入iframe页面

下面几个域名下的页面都是可以通过document.domain跨域互操作的：

    http://a.com/foo, http://b.a.com/bar, http://c.a.com/bar。 
但只能以页面嵌套的方式来进行页面互操作，比如常见的iframe方式就可以完成页面嵌套：

     URL:http://a.com/bar
     var ifr=document.createElement("iframe");
     ifr.src="http://b.a.com/bar";
     ifr.onload=function(){
        var ifrdoc=ifr.contentDocument||ifr.contentDocument.document;
        ifrdoc.getElementById("foo").innerHTML;
     };
     ifr.style.display="none";
     document.body.appendChild(ifr);

 //上述代码所在的URL是http://a.com/foo，它对http://b.a.com/bar的DOM访问要求后者将 document.domain往上设置一级：document.domain = 'a.com'
 document.domain只能从子域设置到主域，往下设置以及往其他域名设置都是不允许的，

 有src的标签

原理：所有具有src属性的HTML标签都是可以跨域的，包括<img>, <script>

限制：需要创建一个DOM对象，只能用于GET方法

在document.body中append一个具有src属性的HTML标签， src属性值指向的URL会以GET方法被访问，该访问是可以跨域的。

其实样式表的<link>标签也是可以跨域的，只要是有src或href的HTML标签都有跨域的能力。

不同的HTML标签发送HTTP请求的时机不同，例如<img>在更改src属性时就会发送请求，而script, iframe, link[rel=stylesheet]只有在添加到DOM树之后才会发送HTTP请求：

        var img = new Image();
        img.src = 'http://some/picture';        // 发送HTTP请求
        var ifr = $('<iframe>', {src: 'http://b.a.com/bar'});
        $('body').append(ifr);                  // 发送HTTP请求

JSONP

原理：<script>是可以跨域的，而且在跨域脚本中可以直接回调当前脚本的函数。

限制：需要创建一个DOM对象并且添加到DOM树，只能用于GET方法

JSONP利用的是<script>可以跨域的特性，跨域URL返回的脚本不仅包含数据，还包含一个回调：

然后在我们在主站http://a.com中，可以这样来跨域获取http://b.a.com的数据：
    // URL: http://a.com/foo

        var callback = function(data){
            // 处理跨域请求得到的数据
        };
    var script = $('<script>', {src: 'http://b.a.com/bar'});
    $('body').append(script);

jQuery已经封装了JSONP的使用，我们可以这样来：

    $.getJSON( "http://b.a.com/bar?callback=callback", function( data ){
        // 处理跨域请求得到的数据
    });

$.getJSON与$.get的区别是
前者会把responseText转换为JSON，而且当URL具有callback参数时， jQuery将会把它解释为一个JSONP请求，创建一个<script>标签来完成该请求。

和所有依赖于创建HTML标签的方式一样，JSONP也不支持POST，而GET的数据是放在URL里的。  
但提到了服务器可以对自己认为比较长的URL返回414状态码。一般来讲URL限长是在2000字符左右。

navigation 对象

原理：iframe之间是共享navigator对象的，用它来传递信息

要求：IE6/7

有些人注意到了IE6/7的一个漏洞：iframe之间的window.navigator对象是共享的。 
我们可以把它作为一个Messenger，通过它来传递信息。比如一个简单的委托：
        // a.com
        navigation.onData(){
            // 数据到达的处理函数
        }
        typeof navigation.getData === 'function' 
            || navigation.getData()
        // b.com
        navigation.getData = function(){
            $.get('/path/under/b.com')
                .success(function(data){
                    typeof navigation.onData === 'function'
                        || navigation.onData(data)
                });
        }
与document.navigator类似，window.name也是当前窗口所有页面所共享的。也可以用它来传递信息。 
同样蛋疼的办法还有传递Hash（有些人叫锚点），这是因为每次浏览器打开一个URL时，
URL后面的#xxx部分会保留下来，那么新的页面可以从这里获得上一个页面的数据。

跨域资源共享（CORS）

原理：服务器设置Access-Control-Allow-OriginHTTP响应头之后，浏览器将会允许跨域请求

限制：浏览器需要支持HTML5，可以支持POST，PUT等方法

前面提到的跨域手段都是某种意义上的Hack， HTML5标准中提出的跨域资源共享（Cross Origin Resource Share，CORS）才是正道。 它支持其他的HTTP方法如PUT, POST等，可以从本质上解决跨域问题。

例如，从http://a.com要访问http://b.com的数据，通常情况下Chrome会因跨域请求而报错：

错误原因是被请求资源没有设置Access-Control-Allow-Origin，
所以我们在b.com的服务器中设置这个响应头字段即可：

    Access-Control-Allow-Origin: *              # 允许所有域名访问，或者
    Access-Control-Allow-Origin: http://a.com   # 只允许所有域名访问

window.postMessage

原理：HTML5允许窗口之间发送消息

限制：浏览器需要支持HTML5，获取窗口句柄后才能相互通信

这是一个安全的跨域通信方法，postMessage(message,targetOrigin)也是HTML5引入的特性。 
可以给任何一个window发送消息，不论是否同源。第二个参数可以是*但如果你设置了一个URL但不相符，
那么该事件不会被分发。看一个普通的使用方式吧：

    // URL: http://a.com/foo
    var win = window.open('http://b.com/bar');
    win.postMessage('Hello, bar!', 'http://b.com'); 
    // URL: http://b.com/bar
    window.addEventListener('message',function(event) {
        console.log(event.data);
    });

