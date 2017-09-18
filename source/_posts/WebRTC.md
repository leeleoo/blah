---
title: WebRTC
date: 2016-10-18 11:55:05
tags: 
  -learn
categories: 
  - html5
---

## 1.获取用户媒体

 ```javascript
  navigator.getUserMedia(
    { video: true, audio: false },
    function (myStream) {
      stream = myStream;
      yourVideo.src = window.URL.createObjectURL(stream);
      if (hasRTCPeerConnection()) {
  	   //创建PeerConnection
        setupPeerConnection(stream);
      } else {
        alert("Sorry, your browser does not support WebRTC.");
      }
    }, 
    function (error) {
        console.log(error);
    }
  );
```
<!-- more -->
#### 1.1创建RTCPeerConnection`init`
>#### 这里主要做了四件事情

1. 创建RTCPeerConnection对象
2. 给它添加媒体stream
3. 给它添加onaddstream事件
4. 给它添加onicecandidate事件



```javascript
function setupPeerConnection(stream) {
  var configuration = {
    "iceServers": [{ "url": "stun:127.0.0.1:9876" }]
  };
  yourConnection = new RTCPeerConnection(configuration);

  // Setup stream listening
  yourConnection.addStream(stream);
  yourConnection.onaddstream = function (e) {
    theirVideo.src = window.URL.createObjectURL(e.stream);
  };

  // Setup ice handling
  yourConnection.onicecandidate = function (event) {
    if (event.candidate) {
      send({
        type: "candidate",
        candidate: event.candidate
      });
    }
  };
  
}
```


## 2.createOffer`call` 
>建立 SDP会话描述协议 offer 和 answer
>
>创建之后发送给服务器 	
```javascript
function startPeerConnection(user) {
  connectedUser = user;

  // Begin the offer
  yourConnection.createOffer(function (offer) {
    send({
      type: "offer",
      offer: offer
    });
    yourConnection.setLocalDescription(offer);

  }, function (error) {
    alert("An error has occurred.");
  });
};

```
#### 2.1 createAnswer `call`
> 服务器收到offer之后反馈给客户端,客户端发送 answer
```javascript
function onOffer(offer, name) {
  connectedUser = name;

  yourConnection.setRemoteDescription(new RTCSessionDescription(offer));

  yourConnection.createAnswer(function (answer) {
    yourConnection.setLocalDescription(answer);
    send({
      type: "answer",
      answer: answer
    });
  }, function (error) {
    alert("An error has occurred");
  });
};
```
#### 2.2 响应 Answer`call`
> 
```javascript
function onAnswer(answer) {

  yourConnection.setRemoteDescription(new RTCSessionDescription(answer));
  
};
```
## 3.Question 
既然 webrtc 能实现点对点传输视频和音频, 那能不能 .....  能!

当然可以传点其他东西

#### 使用 DataChannel

1.创建
```javascript
yourConnection.createDataChannel('name',options)
```
第一个参数是卖萌的随便取一个就行了

这里的 options 可以搞下面的事情
____
* reliable `设置消息传递是否进行担保(担保?黑人问号)`
* ordered `用来设置消息的接受是否需要按照发送时的顺序`
* maxRetransmitTime `设置消息发送失败时,多久重新发送`
* maxRetransmits `设置消息发送失败时,最多重发几次`
* protocol `设置强制使用其他子协议`
* negotiated `此选项用来设置开发人员是否有责任在两边创建数据通道,还是浏览器来自动完成这个步骤`
* id  `ID 本人亲测不设ID 根本连不上`

这里我只想说,我不是针对id, 我是说, 在座的options,都是辣鸡(开个小玩耍)
**说说我对这几个配置的感受**
- 首先 ID 感觉是必填的. 这应该是链路层的地址,不然你知道传给谁?
- 然后有几个可配置的属性 `reliable` `ordered` `maxRetransmitTime ` `maxRetransmits`
- 最后两个值得注意的属性 `protocol` `negotiated` 并不知道有什么用(先挖坑,日后再填)
---
> negotiated
当此属性为 TRUE 时

这里会有一个 ondatachannel 事件触发在 RTCPeerConnection 对象上本来由浏览器自动处理的数据通道现在必须由你来创建一个相同 ID 的数据通道

---

DataChannel 非常容易使用这里有几个事件和几个方法
#### 事件
- onerror
- onmessage
- onopen
- onclose

其中 onerror 和 onmessage 会传递一个参数供你使用

#### 方法
- dataChannel.send

意思已经很明显了. 其中发送的内容可以如下几种类型								
1. String 
2. Blob
3. ArrayBuffer
4. ArrayBufferView

[什么是 ArrayBuffer](http://www.jb51.net/article/58281.htm) 
[阮一峰:ArrayBuffer](http://javascript.ruanyifeng.com/stdlib/arraybuffer.html#toc0)

所以你有可能写这样的代码
```javascript
dataChannel.onmessage = function(event){
	var data = event.data;
	if(data instanceof Blob){
		
	}else if(data instanceof ArrayBuffer){
		 
	}
}
```
 ## 4. 让我们来传点真正的东西吧!

> 首先要明确一点,  由于种种原因, 我们只能通过 base64 来传输数据 (是的, 这不是你家,这是浏览器,想在这里搞事情,就得听浏览器的,为什么?因为 js 和 底层网络协议有很多不可描述的约定, 所以大家商量就用可读字符串来传输数据吧	)

由于上面的BUG, 噢, 不 特性,,, 我们的流程会是这样的
1. 先利用filereaderAPI 把文件 convert 为 buffer `reader.readAsArrayBuffer(file)`
2. 使用 `String.fromCharCode[int]` 返回对应整数编码的字符
3. 使用 `btoa` 转换为base64编码
4. 接收端拿到 base64 使用 `String.charCodeAt[string]` 返回 `Unicode` 编码
5. 组装生成 BufferArray
6. 最后变成 `new Blob(byteArrays, {type: contentType})`

然后下载
```javascript
function saveFile(meta, data) {
  var blob = base64ToBlob(data, meta.type);
  var link = document.createElement('a');
  link.href = window.URL.createObjectURL(blob);
  link.download = meta.name;
  link.click();
}
```
## 5.移动端的适配

 IOS 无法使用 不想适配了

