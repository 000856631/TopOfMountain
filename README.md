# TopOfMountain
代码之巅，积累学习
# bookScan
第二个小程序
基于water2的总流程
https://www.ifanr.com/minapp/915777
实现图书扫描录入的功能
当前完成客户端与服务的基本框架的熟悉与搭建。
了解KOA框架，同心圆周期的原则。
主要写了这个函数
const urllib = require('urllib')
async function request( options) {
  let data = null;
  const opts = Object.assign({ dataType: 'json' }, options);
  const url = options.url;
  const request = await urllib.request(url, opts);
  data = request.data;
  return data;
}
module.exports = async ctx => {
  // 获取上传之后的结果
  // 具体可以查看：
  const { isbnCode } = ctx.query
  var options = {
    url: 'https://api.douban.com/v2/book/isbn/:' + isbnCode,
    headers: { 'User-Agent': 'request' }
  };

  var response = await request(options); //Yay, HTTP requests with no callbacks! 
  ctx.state.data = response;

}

通过urllib来外接API，之前尝试了https,http，还有KOA-request 遇到了不少挫折
对await和async的js新写法要好好熟悉熟悉，貌似通过这种方式可以消除难看的回调。
仔细想想还都是同心圆的思路。
豆瓣的API还是很不错的，记住只支持https.
对于ctx的用法要好好整理下，获取上下文的关键参数。 
本来我就对node的request和response就不是很熟，在这块也踩坑不少。
Request aliases

以下访问器和别名与 Request 等价：

    ctx.header
    ctx.method
    ctx.method=
    ctx.url
    ctx.url=
    ctx.originalUrl
    ctx.path
    ctx.path=
    ctx.query
    ctx.query=
    ctx.querystring
    ctx.querystring=
    ctx.host
    ctx.hostname
    ctx.fresh
    ctx.stale
    ctx.socket
    ctx.protocol
    ctx.secure
    ctx.ip
    ctx.ips
    ctx.subdomains
    ctx.is()
    ctx.accepts()
    ctx.acceptsEncodings()
    ctx.acceptsCharsets()
    ctx.acceptsLanguages()
    ctx.get()

Response aliases

以下访问器和别名与 Response 等价：

    ctx.body
    ctx.body=
    ctx.status
    ctx.status=
    ctx.length=
    ctx.length
    ctx.type=
    ctx.type
    ctx.headerSent
    ctx.redirect()
    ctx.attachment()
    ctx.set()
    ctx.remove()
    ctx.lastModified=
    ctx.etag=

Anyway，I have done it。
服务端的内容还需要补很多坑，之前第一个小程序只是采用现成的模版来实现的。
下一步，对于扫码获取的图书信息进行填充渲染。
记录我这个客户的图书信息。这里要涉及数据库了。
两张表，一张是我的图书列表信息，用openID做主键吧 其余字段有ISBN,图书图片url。
第二张表是图书表，通过ISBN号搜索出图书的信息，就用ISBN做主键吧，其余信息参考json来设计。
又是一个坑。慢慢填吧
或者看看小程序本地缓存来实现。貌似也可以。哈哈。
后续可以做分享我的图书清单。
分享图书清单功能：
1、调整书架UI，添加头像和昵称的展现，添加分享按钮、以及首页按钮。
2、加入数据库功能，存储该用户的昵称、头像、和书架信息。
// 初始化 SDK
// 将基础配置和 sdk.config 合并传入 SDK 并导出初始化完成的 SDK
这一步很关键，链接了数据库的动作。所以可以直接引用了。
const { mysql } = qcloud
mysql('testBook').select('*').where({ nickName: Samel }) // => { id:1, name: 'leo', age: 20 }
可以运行试试。看能不能返回这个数据回来。
可以的话就应该设计表结构了。
存储登录的用户信息这块，还没有调试。可以先缓缓吗？
查询动作

3、调用微信小程序的分享功能。

盈利点：支付的用户可以实现导出到Excel。
已经通过本地缓存 简单记录了个人录入的图书信息了。
无需获取个人登录信息，还是比较良心的。
下一步就需要搞搞数据库。起码得获取用户的openID。
和表记录关联。
对于录入的图书。点开可以展现更丰富的内容。简介、作者、摘要之类。
并且可以录入用户的一些备注信息。
当然，也能让用户有自己的读书计划也是不错的。

这部分完善后，就可以做分享图书清单，以及陌生人借书社交的套路了。
一步一个脚印，慢慢来吧。
前面的分享以及数据库搭建已经完成。
接下来分享列表页加入本人登录后的头像吧。
这快应该能很快实现
现在基本流程 插入数据库 以及读取详情页这块还有些小bug
1、先把基本流程弄通。全部改为在线模式。
2、然后把分享弄通。提交一次github。
3、就再次提交一次送审吧。

都不是什么大问题。
后续加啥功能呢？
加入删除功能吧
把个人简介加进去吧，以及用户留言反馈功能
后续加入附近的书这个功能吧。

写到插入书籍详情这里，然后书籍详情就再封装一个接口来查，不再从缓存取了。
建议后续加入一个私密扫描的书架，不会上传
应该是这样，用户扫描的和自己看的时候都是本次存储，
就是转发出去的时候才去服务器取，但是这样不好，还要区分是本地还是在线
不如统一改为在线模式好了。
这也是一开始的设计没想好的原因。

先做把小程序添加到桌面
然后是加入定期提醒推送看书的通知。
实现思路，在具体的书籍下面可以设置提醒下次看书时间。
设置限定为七天内。
每设置一次可以获得一颗星星，积满10颗行 我就把这本书定义为我已经熟读的书籍。
分享的时候可以标识出来。
布局的优化建议：分享改为一个悬浮在右边中间区域的透明模糊色块
加入各类按钮功能。
例如分享，以及填写提醒计划，可以设置未来七天内的读书提醒计划
提醒计划这个可以这么玩，一点击直接就是弹框，是否在今晚8:30提醒我阅读此书？
是的话就直接发推送咯。
然后试试推送的玩法
后续加入附近图书馆的功能吧。
一个图片按钮，居然卡了我很久
通过盖了一层隐藏的按钮，来解决这个问题，后续看看有没有更好的办法。

貌似微信token这里，已经预先定义好了一个变量，但是🈶️没有去使用。
后续看看能不能找微信支持的人问下了

https://www.jianshu.com/p/17138713d601
这里能查看会话服务器配置的secret和appID

先用野蛮写法吧，把通知搞掂先。
关于nodejs urllib的用法：
https://www.npmjs.com/package/urllib
这个链接搞掂get和post的调用区别。

当前情况，通知能调起了，但是需要服务器执行一个定时批量。
调那个先暂时把要发消息的传入列表，以及加入一个状态为未发送。
用户点击这个按钮的时候插入一张消息通知表。

每天晚上8:30再去定时查询这张表，然后调微信的通知接口。
然后更新这个表的状态。

先试试定时打印日志的代码吧。我们通过看服务器后台日志
https://segmentfault.com/a/1190000006206193
这片东西介绍了later这个库

表设计漏了书名，还有书的进度状态。进度状态通过缓存来实现，记录星星使用。
先把基本流程搞通，再补充书名的设计。
补了书名了
还有update的流程忘记写了

记得提交git,晚上解决三个问题。工作的时候写项目
哈哈，就差最后一步update表的状态了

https://juejin.im/post/59cb487ef265da065075409f 
const { mysql } = require('../qcloud')
const uuid = require('node-uuid')
module.exports = async ctx => 
{  
var id = uuid.v1()  // 增 
 var book = {    
id: id,    
name: "冰与火之歌",  
  price: 88  
}  
   await mysql("Book").insert(book)  
 // 查  var res = await mysql("Book").where({ id }).first() 
 // 改  await mysql("Book").update({ price: 66 }).where({ id }) 
 // 删  await mysql("Book").del().where({ id }) 
 ctx.state.data = "OK"}

数据库操作都是异步的，前面需要加入await才能调起使用。
update的问题也已经解决。

this总是指向词法作用域，也就是外层调用者obj
所以在  formSubmit: function (e) {
    var self = this;把this提前之后就能正确引用 self.data.movie
箭头函数的用法：https://www.cnblogs.com/hailun/p/6279029.html


function (x) {
    return x * x;
}

module.exports = async ctx => {

}
等价于
async function noticeUser()
{

}
module.exports = noticeUser;

接下来还需要搞懂js的几种异步模式
Javascript 语言的执行环境是“ 单线程” 的
http://blog.csdn.net/qq_30100043/article/details/53508080
http://www.ruanyifeng.com/blog/2015/05/async.html
http://www.ruanyifeng.com/blog/2015/04/generator.html
Promise 就是为了解决这个问题而提出的。 它不是新的语法功能， 而是一种新的写法， 允许将回调函数的嵌套， 改成链式调用。 采用 Promise， 连续读取多个文件， 写法如下。
当初链式调用，原来就是这个特性。


异步的几种方式：
1、回调函数

JavaScript 语言对异步编程的实现， 就是回调函数。 所谓回调函数， 就是把任务的第二段单独写在一个函数里面， 等到重新执行这个任务的时候， 就直接调用这个函数。 它的英语名字 callback， 直译过来就是 " 重新调用 "。
读取文件进行处理， 是这样写的。
    fs.readFile('/etc/passwd', function(err, data) {  
        if(err) throw err;  
        console.log(data);  
    });  
2、Promise 解决传统回调 从横向发展到纵向发展的问题：
传统的回调函数写法使得代码混成一团，变得横向发展而不是向下发展
代码事例===
function ajax(URL) {
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
var URL = "/try/ajax/testpromise.php"; 
ajax(URL).then(function onFulfilled(value){
	document.write('成功内容是：' + value); 
}).catch(function onRejected(error){
	document.write('错误：' + error); 
});
3、Generator
4、async函数



搞定那三题
1、平衡二叉树。
2、数据归并。
3、链式调用。


以及js 的模块化编程
下一步：统计下推送清单表，看看推送列表里拿不到用户openId的多不多，多的话再想想应该把openID缓存起来的做法。
做成通用方法，启动的时候去登录拿，拿不到的话再去缓存拿，最终还是拿不到提示用户无法获取openId，请重新打开。
登录每次拿到之后都更新本地缓存的openID
改为放在缓冲吧，否则经常拿不到。
经常拿不到是修改了main的那个方法，第一种方式，没有经过request的，是拿不到的。

现在经常收不到通知，但是单个却有可以。怀疑是循环没有做好，每次只调用了一次。
测试下，一会弄两台手机同时试试。

理解下js的数据结构，以及一些异步的特性。
Firefox  alt+command+s 打开调试器，可以直接运行js的代码
Firefox  fn+shirt+f4 直接打开代码草稿纸，也可以运行js的代码

腾讯云居然把我的用户数据和表结构都删除了，真是MMB的
看来以后建表要自己写好脚本保存起来才行。然后定期保存下数据
然后尽快切换生产服务器搭建好来吧。
如下表：
bookInfo
-openid
-isbnNum
-nickName
-headImageUrl
-bookLogoUrl
-bookName
-authorName
-content

noticeList
-openid
-bookName
- 

bookOwnerRalation
书和人本来是多对多的关系，根据范式设计应该拆分为三张表。
一个和人有关、一个和书籍有关、一个记录关系。
但是这里是小程序，可以追求反范式设计，变为一个人对应多本书籍。这样的话书籍的字段就会有冗余的记录。
这样我的SQL处理会快一点，用空间换时间。（还有写代码偷懒的时间。）
下次把正式环境投上去就完美了

接下来的步骤，备案域名，搭建好我生产的服务器，提供稳定的服务。
后续加入 没了
继续咯

数组尾部添加 push
尾部删除 pop
头部添加 unshift
头部删除 shift
闭包的案例
坑的写法：
function createFunctions(){
  var result = new Array();
  for (var i=0; i < 10; i++){
    result[i] = function(){
      return i;
    };
  }
  return result;
}
var funcs = createFunctions();
for (var i=0; i < funcs.length; i++){
  console.log(funcs[i]());
}
闭包技术的匿名函数，能让函数立即执行，从而返回正确的结果。
正确写法：
function createFunctions(){
  var result = new Array();
  for (var i=0; i < 10; i++){
    result[i] = (function(i){
      return function(){
         return i;
      };
    })(i);
  }
  return result;
}
var funcs = createFunctions();
for (var i=0; i < funcs.length; i++){
  console.log(funcs[i]());
}

柯里化，有点意思：
function curryIt(fn) {
        var length = fn.length,
        args = [];
    var result =  function (arg){
        args.push(arg);
        length --;
        console.log(length);
        if(length <= 0 ){
            return fn.apply(this, args);
        } else {
            return result;
        }
    }
     
    return result;
}


//延迟这个函数，只是记录变量而已，通过判断变量是否存在，决定是否延迟执行。
//每一个函数，其实都不执行，都只是记录变量的值而已。
//为啥要另外定义一个obj,因为需要这个公共类直接就能生成一个实例化对象。 后来发现其实也不需要，例如你看我就直接返回this
//这道题的关键是 setTimeout(this.settime); 通过这句，形成一个异步处理，先把调用方法的变量给赋值完
//然后这个异步根据变量的值来决定走什么分支，所以以后对于那种顺序动态变化的，需找到执行切入点。
function Hard(str){
    this.str = "I am " + str;
    var that = this;
    this.restFirst = function ( n ) {
    this._restFirst = n;
    this._rest = 0;
    return this;
}
this.rest = function( num ){
    this._rest = num;
    return this;
}
this.yanchi = function(m){
    console.log("sleep")
    var d = new Date();
    var n = this._restFirst*1000;
    while(true){
        if( new Date() - d > n ){
            console.log("sleep end")
            return false;
        }
    }
}
this.learn = function( sr ) {
    this._learn = sr;
    return this;
}
    this.settime = function(){
    var { str, _learn, _rest, _restFirst} = that;
    var num = _rest || _restFirst;

    

    if( _restFirst !== undefined ){
        that.yanchi();
    }else{
        console.log(str);
    }

    if(num){
        setTimeout(function(){
            console.log( `Start learning after ${ num } seconds` );
            if( _restFirst !== undefined ){
                console.log(str);
            }
            if( _learn ){
                console.log( "Learning " + _learn )
            }
        },_rest*1000)
    }
     }
    setTimeout(this.settime);
    return this;
}
// Hard('jack1').rest(3).learn('chinese2');
Hard('jack1').restFirst(5).learn('chinese');

解决调小bug，显示按钮白色

开发一个以书会友的功能。
开发一个关于作者的功能，留下联系方式。
开发一个星辰大海的动画

Velocity 和 jQuery.animate 的工作方式类似，也是用来实现 JavaScript 动画的一个很棒的选择
引入Tweenmax.js文件,也是实现动画的好东东
<script src = “https://cdnjs.cloudflare.com/ajax/libs/gsap/2.0.1/TweenMax.min.js” > </ script> 
学会了用火狐调试前端界面了，可以看到console.log的信息，也能通过命令行修改里面的元素
在线学习前端的好网站 http://jsfiddle.net/boilerplate/vue

纠结vue究竟有没有触摸事件？

第一天，搭建好git hub库  整理好IDE webStorm，并保存在百度网盘内。
把以前的学习心得记录下来。
