单例模式
非透明单例
var single=function(name){
    this.name=name;
}
single.prototype.getName=function(){
    console.log(this.name);
}
single.getInstance=function(){
    var instance=null;
    return function(){
        if(!instance){
            instance=new single();
        }
        return instance;
    }
}
透明单例模式
用代理实现单例模式
js 中的单例
全局变量：使用命名空间，使用闭包封装私有变量
var myApp={};
myApp.namespace=function(name) {
    // body...
    var parts=name.split(".");
    console.log(parts);
    var current=myApp;
    for(var i in parts){
        if(!current[parts[i]]){
            current[parts[i]]={};
        }
        console.log(current)
        current=current[parts[i]];
    }
};

// myApp.namespace('event');
// console.log(myApp);
myApp.namespace('dom.style.width');
console.log(myApp);

惰性单例
var createLoginLayer=(function(){
    var div;
    return function(){
        if(!div){
            div=document.createElement("div");
            div.innerHTML="登陆";
            div.style.display="none";
            document.body.appendChild(div);
        }
        return div;
    }
})();
document.getElementsByTagName('button')[0].onclick=function(){
    var login=createLoginLayer();
    login.style.display="block";
}


通用惰性单例
var single=function(fn){
    var result;
    return function(){
        return result || (result=fn.apply(this,arguments));
    }
};

var createLogin=function(){
    var div=document.createElement('div');
    div.innerHTML="登陆";
    div.style.display='none';
    document.body.appendChild(div);
    return div;
};
var createSingleLogin=single(createLogin);
document.getElementById("button").onclick=function(){
    var Loginlayer=createSingleLogin();
    Loginlayer.style.display="block";
}

ajx渲染时只绑定一次
var bindEvent=single(function(){
    document.getElementsByTagName('button')[0].onclick=function(){
        console.log(1);
    }
    return true;
});
var rend=function(){
    console.log("开始");
    bindEvent();
};
rend();
rend();
rend();