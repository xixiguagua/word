
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=0">

宽度自适应

    .div {
      width:100%; height:100px;
    }
    .child {
      float: left; 
    }
    .child {
      float:right;
    }

完全自适应


    html {
     font-size: 10px;
    }
     
    div {
     font-size: 1rem;
     height: 2rem;
     width: 3rem;
     border: .1rem solid #000;
    }

div继承到了html节点的font-size，为本身定义了一系列样式属性，此时1em计算为10px，即根节点的font-size值


数据计算公式 640/100 = device-width / x  可以设置其他设备根元素字体大小

    ihone5: 640  ： 100
    iphone6: 750 : 117
    iphone6s: 1240 : 194

    var deviceWidth = window.documentElement.clientWidth;
    document.documentElement.style.fontSize = (deviceWidth / 6.4) + 'px';


    var deviceWidth = document.documentElement.clientWidth > 1300 ? 1300 : document.documentElement.clientWidth;
    document.documentElement.style.fontSize = (deviceWidth / 6.4) + 'px';

一般的情况下，你是不需要考虑屏幕动态地拉伸和收缩。

当然，假如用户开启了转屏设置，在网页加载之后改变了屏幕的宽度，那么我们就要考虑这个问题了。解决此问题也很简单，监听屏幕的变化就可以做到动态切换元素样式:

    window.onresize = function(){
          var deviceWidth = document.documentElement.clientWidth > 1300 ? 1300 : document.documentElement.clientWidth;
          document.documentElement.style.fontSize = (deviceWidth / 6.4) + 'px';
     };
     为了提高性能，让代码开起来更加完美，可以为它加上节流阀函数:
     window.onresize = _.debounce(function() {
          var deviceWidth = document.documentElement.clientWidth > 1300 ? 1300 : document.documentElement.clientWidth;
          document.documentElement.style.fontSize = (deviceWidth / 6.4) + 'px';
    }, 50);

如果设计稿标准是iphone5，那么拿到设计稿的时候一定会发现，完全不能按照高保真上的标注来写css，而是将各个值取半，这是因为移动设备分辨率不一样。

设计师们是在真实的iphone5机器上做的标注，而iphone5系列的分辨率是640，实际上我们在开发只需要按照320的标准来。

为了节省时间，不至于每次都需要将标注取半，我们可以将整个网页缩放比例，模拟提高分辨率。这个做法很简单，为不同的设备设置不同的meta即可:

    var scale = 1 / devicePixelRatio;
    document.querySelector('meta[name="viewport"]').setAttribute('content', 'initial-scale=' + scale + ', maximum-scale=' + scale + ', minimum-scale=' + scale + ', user-scalable=no');

这样设置同样可以解决在安卓机器下1px像素看起来过粗的问题，因为在像素为1px的安卓下机器下，边框的1px被压缩成了0.5px

       <!DOCTYPE html>
    <html>

    <head>
        <title>测试</title>
        <meta name="viewport" content="width=device-width,user-scalable=no,maximum-scale=1" />
        <script type="text/javascript">
        (function() {
            // deicePixelRatio ：设备像素
            var scale = 1 / devicePixelRatio;
            //设置meta 压缩界面 模拟设备的高分辨率
            document.querySelector('meta[name="viewport"]').setAttribute('content', 'initial-scale=' + scale + ', maximum-scale=' + scale + ', minimum-scale=' + scale + ', user-scalable=no');
            //debounce 为节流函数，自己实现。或者引入underscoure即可。
            var reSize = _.debounce(function() {
                var deviceWidth = document.documentElement.clientWidth > 1300 ? 1300 : document.documentElement.clientWidth;
                //按照640像素下字体为100px的标准来，得到一个字体缩放比例值 6.4
                document.documentElement.style.fontSize = (deviceWidth / 6.4) + 'px';
            }, 50);

            window.onresize = reSize;
        })();
        </script>
        <style type="text/css">
        html {
            height: 100%;
            width: 100%;
            overflow: hidden;
            font-size: 16px;
        }
        
        div {
            height: 0.5rem;
            widows: 0.5rem;
            border: 0.01rem solid #19a39e;
        }
        </style>

        <body>
            <div>
            </div>
        </body>

    </html>

媒体查询

    @media screen and (device-width: 640px) { /*iphone4/iphon5*/
          html {
            font-size: 100px;
          }
        }
     
    @media screen and (device-width: 750px) { /*iphone6*/
          html {
            font-size: 117.188px;
          }
        }
    @media screen and (device-width: 1240px) { /*iphone6s*/
          html {
            font-size: 194.063px;
          }
        }

缺点是灵活性不高，取每个设备的精确值需要自己去计算，所以只能取范围值。
考虑设备屏幕众多，分辨率也参差不齐，把每一种机型的css代码写出来是不太可能的。

但是它也有优点，就是无需监听浏览器的窗口变化，它会跟随屏幕动态变化

     @media screen and (min-width: 320px) and (max-width: 650px) { /*手机*/
      .class {
        float: left;
      }
    }
     
    @media screen and (min-width: 650px) and (max-width: 980px) { /*pad*/
      .class {
        float: right;
      }
    }
     
    @media screen and (min-width: 980px)  and (max-width: 1240px) { /*pc*/
      .class {
        float: clear;
      }
    }