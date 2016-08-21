
function xssCheck(str,reg){
  return str ? str.replace(reg ||/[&<">'](?:(amp|lt|quot|gt|#39|nbsp|#\d+);)?/g,function (a, b) {
    if(b){
      return a;
    }else{
      return{
        '<':'&lt;',
        '&':'&amp;',
        '"':'&quot;',
        '>':'&gt;',
        "'":'''
      }[a]
    }
  }): '';
}

for(vari=0,tags=document.querySelectorAll('iframe[src],frame[src],script[src],link[rel=stylesheet],object[data],embed[src]'),tag;tag=tags[i];i++){
  var a = document.createElement('a');
  a.href= tag.src||tag.href||tag.data;
  if(a.hostname!=location.hostname){
    console.warn(location.hostname+'发现第三方资源['+tag.localName+']:'+a.href);
  }
}

首先我们需要一个白名单列表，用于放置网站允许第三方加载的url地址：
var scriptList = [
  location.hostname,
]
这里只是默认的只允许当前域名加载，打击爱可以根据自己的需要添加。
然后就是获取当前网页的所有script标签：
varwebScript = document.querySelectorAll(‘script[src]‘);
在把当前的地址赋值varwebHost =location.hostname;至于为什么不放在for循环里，因为根据js优化规则，for循环里避免多次一样的赋值。
接下来就是for循环里的代码了：

for(vari = 0;i < webScript.length;i++){
  var a = document.createElement('a');  //建立一个新的a标签，方便取值
  a.href= webScript[i].src; //把script里的src赋值给a标签里的href属性
    if(a.hostname!= webHost){   //对比，是否为第三方资源
    for(varj = 0;j < scriptList.length;j++){
      if(a.hostname!= scriptList[i]){   //判断当前的第三方资源是否在白名单里
        newImage().src = 'http://fecm.cn/Api/addVul/category/2';    //发送给FECM
      }
    }
  }
}

跨站脚本（Cross-site scripting，通常简称为XSS）是一种网站应用程序的安全漏洞攻击，是代码注入的一种。它允许恶意用户将代码注入到网页上，其他用户在观看网页时就会受到影响。这类攻击通常包含了HTML以及用户端脚本语言
常用的XSS攻击手段和目的有：

盗用cookie，获取敏感信息。
利用植入Flash，通过crossdomain权限设置进一步获取更高权限；或者利用Java等得到类似的操作。
利用iframe、frame、XMLHttpRequest或上述Flash等方式，以（被攻击）用户的身份执行一些管理动作，或执行一些一般的如发微博、加好友、发私信等操作。
利用可被攻击的域受到其他域信任的特点，以受信任来源的身份请求一些平时不允许的操作，如进行不当的投票活动。
在访问量极大的一些页面上的XSS可以攻击一些小型网站，实现DDoS攻击的效果。