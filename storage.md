h5之前，存储主要是用cookies。cookies缺点有在请求头上带着数据，大小是4k之内。主Domain污染。
主要应用：购物车、客户登录
对于IE浏览器有UserData，大小是64k,只有IE浏览器支持。
解决4k的大小问题
解决请求头常带存储信息的问题
解决关系型存储的问题
跨浏览器

1.本地存储localstorage
存储方式：
以键值对(Key-Value)的方式存储，永久存储，永不失效，除非手动删除。
大小：每个域名5M
if(window.localStorage){
 alert('This browser supports localStorage');
}else{
 alert('This browser does NOT support localStorage');
}
getItem //取记录
setIten//设置记录
removeItem//移除记录
key//取key所对应的值
clear//清除记录
存储的内容：数组，图片，json，样式，脚本。。。（只要是能序列化成字符串的内容都可以存储）

2.本地存储sessionstorage
HTML5 的本地存储 API 中的 localStorage 与 sessionStorage 在使用方法上是相同的，区别在于 sessionStorage 在关闭页面后即被清空，而 localStorage 则会一直保存。

3.离线缓存（application cache）
本地缓存应用所需的文件
使用方法：
①配置manifest文件
页面上：
<!DOCTYPE HTML>
<html manifest="demo.appcache">
...
</html>
Manifest 文件：
manifest 文件是简单的文本文件，它告知浏览器被缓存的内容（以及不缓存的内容）。
manifest 文件可分为三个部分：
①CACHE MANIFEST - 在此标题下列出的文件将在首次下载后进行缓存
②NETWORK - 在此标题下列出的文件需要与服务器的连接，且不会被缓存
③FALLBACK - 在此标题下列出的文件规定当页面无法访问时的回退页面（比如 404 页面）
完整demo：
CACHE MANIFEST
# 2016-07-24 v1.0.0
/theme.css
/main.js
NETWORK:
login.jsp
FALLBACK:
/html/ /offline.html
服务器上：manifest文件需要配置正确的MIME-type，即 "text/cache-manifest"。
如Tomcat:<mime-mapping>
     <extension>manifest</extension>
     <mime-type>text/cache-manifest</mime-type>
</mime-mapping>
常用API：
核心是applicationCache对象，有个status属性，表示应用缓存的当前状态：
0（UNCACHED） :  无缓存， 即没有与页面相关的应用缓存
1（IDLE） : 闲置，即应用缓存未得到更新
2 （CHECKING） : 检查中，即正在下载描述文件并检查更新
3 （DOWNLOADING） : 下载中，即应用缓存正在下载描述文件中指定的资源
4 （UPDATEREADY） : 更新完成，所有资源都已下载完毕
5 （IDLE） :  废弃，即应用缓存的描述文件已经不存在了，因此页面无法再访问应用缓存
相关的事件：
表示应用缓存状态的改变：
checking : 在浏览器为应用缓存查找更新时触发
error : 在检查更新或下载资源期间发送错误时触发
noupdate : 在检查描述文件发现文件无变化时触发
downloading : 在开始下载应用缓存资源时触发
progress：在文件下载应用缓存的过程中持续不断地下载地触发
updateready : 在页面新的应用缓存下载完毕触发
cached : 在应用缓存完整可用时触发
Application Cache的三个优势：
① 离线浏览
② 提升页面载入速度
③ 降低服务器压力
离线缓存与传统浏览器缓存区别：
1. 离线缓存是针对整个应用，浏览器缓存是单个文件
2. 离线缓存断网了还是可以打开页面，浏览器缓存不行
3. 离线缓存可以主动通知浏览器更新资源

5.IndexedDB
索引数据库 (IndexedDB) API（作为 HTML5 的一部分）对创建具有丰富本地存储数据的数据密集型的离线 HTML5 Web 应用程序很有用。同时它还有助于本地缓存数据，使传统在线 Web 应用程序（比如移动 Web 应用程序）能够更快地运行和响应。
异步API：
在IndexedDB大部分操作并不是我们常用的调用方法，返回结果的模式，而是请求——响应的模式，比如打开数据库的操作
这样，我们打开数据库的时候，实质上返回了一个DB对象，而这个对象就在result中。由上图可以看出，除了result之外。还有几个重要的属性就是onerror、onsuccess、onupgradeneeded（我们请求打开的数据库的版本号和已经存在的数据库版本号不一致的时候调用）。这就类似于我们的ajax请求那样。我们发起了这个请求之后并不能确定它什么时候才请求成功，所以需要在回调中处理一些逻辑。
数据存储：
indexedDB中没有表的概念，而是objectStore，一个数据库中可以包含多个objectStore，objectStore是一个灵活的数据结构，可以存放多种类型数据。也就是说一个objectStore相当于一张表，里面存储的每条数据和一个键相关联。
我们可以使用每条记录中的某个指定字段作为键值（keyPath），也可以使用自动生成的递增数字作为键值（keyGenerator），也可以不指定。选择键的类型不同，objectStore可以存储的数据结构也有差异。　

4.Web SQL
关系数据库，通过SQL语句访问
Web SQL 数据库 API 并不是 HTML5 规范的一部分，但是它是一个独立的规范，引入了一组使用 SQL 操作客户端数据库的 APIs。
支持情况：
 Web SQL 数据库可以在最新版的 Safari, Chrome 和 Opera 浏览器中工作。
核心方法：
①openDatabase：这个方法使用现有的数据库或者新建的数据库创建一个数据库对象。
②transaction：这个方法让我们能够控制一个事务，以及基于这种情况执行提交或者回滚。
③executeSql：这个方法用于执行实际的 SQL 查询。
打开数据库：
var db = openDatabase('mydb', '1.0', 'Test DB', 2 * 1024 * 1024,fn);
//openDatabase() 方法对应的五个参数分别为：数据库名称、版本号、描述文本、数据库大小、创建回调
执行查询操作：
var db = openDatabase('mydb', '1.0', 'Test DB', 2 * 1024 * 1024);
db.transaction(function (tx) { 
   tx.executeSql('CREATE TABLE IF NOT EXISTS WIN (id unique, name)');
});
插入数据：　
var db = openDatabase('mydb', '1.0', 'Test DB', 2 * 1024 * 1024);
db.transaction(function (tx) {
   tx.executeSql('CREATE TABLE IF NOT EXISTS WIN (id unique, name)');
   tx.executeSql('INSERT INTO WIN (id, name) VALUES (1, "winty")');
   tx.executeSql('INSERT INTO WIN (id, name) VALUES (2, "LuckyWinty")');
});
读取数据：
db.transaction(function (tx) {
   tx.executeSql('SELECT * FROM WIN', [], function (tx, results) {
      var len = results.rows.length, i;
      msg = "<p>查询记录条数: " + len + "</p>";
      document.querySelector('#status').innerHTML +=  msg;
     
      for (i = 0; i < len; i++){
         alert(results.rows.item(i).name );
      }
     
   }, null);
});

