### 如何做小流量


### 1.前提
网站pv已经达到了一定的规模，比如上百万pv，不做小流量冒然更新功能，可能会带来大面积流量损失。在这种前提下需要做小流量实验


### 2.什么是小流量？

让pv总量的一小部分使用到新功能，而其余用户仍然使用原来的功能


### 3.怎么命中小流量

> 小流量方案可以取随机数，但是随机数有个问题是：某个用户前面看到了该页面，第二次打开又看不到了，比较怪；所以通常的做法是根据UID: 一类用户命中后就始终能命中。


小流量的方案通常是根据cookie转发请求。

+ 通常会在cookie里面取XXXUID，注意不登陆也得有一个ID，但是需要注意的点是如果用户没登陆过你们的网站，第一次很可能取不到该ID。

`T.cookie.get('XXXUID');`

+ 取到id后可以截取前面1到几位；或者后面1到几位。

`xxxUid.substr(0, 6)`

+ 需要注意是10进制还是16进制；16进制需要转换成10进制


`parseInt(xxxUid.substr(0, 6), 16)`


+ 然后和你做小流量的总量取余，得出一个随机数


` var index =  parseInt(xxxUid.substr(0, 6), 16) % 10;`


+ 根据小流量的百分比生成一个小流量数组

```
var smallArray = [1, 1, 0, 0, 0, 0, 0, 0, 0, 0];

```
如20%小流量就前面两位是1，后面8位是0

+ 是否命中小流量

根据index去数组里面取，是否命中小流量


### 4.预置始终命中的cookie方便测试和回归

> 可以预置几个cookie，始终命中小流量，方便测试和回归

```
if (xxxUid && (xxxUid === '812347'
    || xxxUid === '37FFDF'
    || xxxUid === 'C113D3')) {
    smallflow = 1;
}
```
只要在浏览器端种下cookie，就能方便的命中小流量了

```
document.cookie="XXXUID= 812347";

```


### 5.完整代码如下


```
// 小流量
var smallflow = 0;
var xxxUid = T.cookie.get('XXXUID');
var smallArray = [1, 1, 0, 0, 0, 0, 0, 0, 0, 0];
if (baiduId) {
    var index =  parseInt(xxxUid.substr(0, 6), 16) % 10;
    smallflow = smallArray[index];
}

if (xxxUid && (xxxUid === '812347'
    || baiduId === '37FFDF'
    || baiduId === 'C113D3')) {
    smallflow = 1;
}

if (smallflow === 0) {
    return;
}

```


### 6.应用场景

#### 是否展现某个新增的模块（功能）

如果是新功能的话：
在前端可以来决定是否require新的js, less；是否new新的组件； 是否引入新的tpl；
在后端可以cookie拦截及转发处理。在filter中拦截所有请求，加入用于http分流的cookie标志，然后302到当前请求的url


#### 是否展现新的交互

这个的话必然要保留两份代码了。

### 7.其他思考

其实小流量实验是比较复杂的，在很多公司内部有专门做小流量实验的小组。
+ 在接入层nginx层、后端路由层、后端代码逻辑层、前端都可以做小流量，需要根据实际情况实际采用
+ 是否需要单独部署小流量实验用的服务器集群，把新功能部署到这些集群上。http服务器配置根据cookie的转发
+ 日志监控。做好pv统计，做好来源统计。
+ 实验组需要多少pv，根据什么理论确定
+ 设置多少个对照组
+ 置信度怎么算
+ 监控哪些指标（pv？uv？订单量？）



### 8.代码地址

[demo](https://github.com/zuopf769/notebook/tree/master/fe/%E5%89%8D%E7%AB%AF%E5%A6%82%E4%BD%95%E5%81%9A%E5%B0%8F%E6%B5%81%E9%87%8F%E5%AE%9E%E9%AA%8C/code)

> node端的一个代码示例：在根路由层拦截到请求，根据cookie来判断是否命中小流量，命中则301到新的url




