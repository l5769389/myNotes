# 创建HTTPS 本地服务

## http-server

1. 安装http-server

   `npm install http-server -g`

2. `http-server` /`http-server -S` 启动



## mkcert 开启https

1. 

   ```js
   brew install mkcert
   brew install nss # if you use Firefox
   ```

2. ```js
   mkcert -install
   ```

3. 在项目目录：

   ```js
   mkcert 0.0.0.0 localhost 127.0.0.1 ::1
   ```

4. 重命名生成的ca证书

   ```js
   0.0.0.0+3-key.pem -> key.pem
   0.0.0.0+3.pem -> cert.pem
   ```

5. `http-server -S -C cert.pem -o`





# 基础实现

> 本地网页上启动本地摄像头和声音

```html
<video id="video" autoplay playsinline></video>
<script>
    const videoplay =document.querySelector('#video');
    navigator.mediaDevices.getUserMedia({video:true,audio:true})
    .then(res=>{
        videoplay.srcObject =res
    })
    .catch(e=>console.log(e))
</script>
```



## 使用adapter适配不同浏览器





# 音视频约束(contraints)

## 视频约束

1. 宽高
2. frameRate ，帧率
3. facingMode
   1. user 前置
   2. environment 后置
   3. left 前置左侧
   4. right 前置右侧
4. 裁减



## 音频约束

1. volume
2. sampleRate
3. sampleSize
4. echoCancellation,回音消除
5. autoGainControl，回音增益
6. noiseSuppression 降噪
7. latency 延迟大小 <=500ms 200ms好
8. channelCount  单声道/双声道
9. deviceID 
10. groupID 



## 视频特效

通过css 加入以下过滤等效果。



## 截图

通过canvas实现：`pic.getContext('2d').drawImage(videoplay,0,0,pic.width,pic.height)`





# 录制

MediaRecorder类



## 录制屏幕数据

getDisplayMedia







# socket.io

* 是websocket的超集
* 有房间的概念
* 跨平台、跨终端、跨语言



## api

## webRTC信令服务器原理

1. 为什么需要信令服务器？
   1. 交换编解码信息
   2. 交换网络信息 

<img src="https://tva1.sinaimg.cn/large/008i3skNgy1gr9bc1f92nj30m70bijtq.jpg" alt="image-20210607064348960" style="zoom:80%;" />



## 使用koa实现最简单socketio

index.js

```js
const Koa =require('koa')
const app = new Koa();
const http = require('http');
const static =require('koa-static');
const path = require("path");
const server =http.createServer(app.callback());
const { Server } = require("socket.io");
const io = new Server(server);

app.use(static(path.join(__dirname,'./static')))

io.on('connection', (socket) => {
    console.log('a user connected');
});

server.listen(3000, () => {
    console.log('listening on *:3000');
});
```

/static/index.html

```html
<!DOCTYPE html>
<html>
<head>
    <title>Socket.IO chat</title>
    <style>

    </style>
</head>
<body>

<script crossorigin="anonymous" integrity="sha512-iZIBSs+gDyTH0ZhUem9eQ1t4DcEn2B9lHxfRMeGQhyNdSUz+rb+5A3ummX6DQTOIs1XK0gOteOg/LPtSo9VJ+w==" src="https://lib.baomitu.com/socket.io/4.1.2/socket.io.js"></script>
<script>
    var socket = io();
</script>
</body>
</html>
```



# webrtc 基础

## 基础协议

1. NAT协议

> 内网地址转为公网

* 完全锥形NAT
* 地址限制锥型NAT
* 端口限制锥型NAT
* 对称型NAT

![image-20210611054102517](https://tva1.sinaimg.cn/large/008i3skNgy1grdvzyj26nj30nv0cw7dh.jpg)







2. STUN

> 主要目的就是NAT穿越

5389规范之前只能用UDP，5389可以TCP/UDP





3. TURN

> 云端流媒体服务转发，解决对称NAT无法穿越的问题。
>
> 建立在STUN协议之上，消息格式采用STUN格式

![image-20210611055608028](https://tva1.sinaimg.cn/large/008i3skNgy1grdwfmiz5ej30gh0dijvu.jpg)





4. ICE

> 将双方可能的通路收集起来。对 Candidate pair进行排序。 连通性测试。

<img src="https://tva1.sinaimg.cn/large/008i3skNgy1grdwo1yti7j30m60ekq6h.jpg" alt="image-20210611060414431" style="zoom:100%;" />

1. ICE Candidate 每一个 Candidate是 一个地址。







## 端对端传输基本流程



![image-20210611064817134](https://tva1.sinaimg.cn/large/008i3skNgy1grdxxw2un7j30qy0on47s.jpg)



本地P2P demo，演示了上面的流程图。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>

<video id="local" style="width: 300px;height: 300px" autoplay></video>
<video id="remote" style="width: 300px;height: 300px" autoplay playsinline></video>
<button id="btnLocal">local</button>
<button id="connect">connect</button>
<button id="hangup">hangup</button>

<script src="https://webrtc.github.io/adapter/adapter-latest.js"></script>
<script>
    const videoLocal =document.querySelector('#local');
    const videoRemote =document.querySelector('#remote');
    const btnLocal =document.querySelector('#btnLocal')
    const btnConnect =document.querySelector('#connect');
    const btnHangup =document.querySelector('#hangup');
    let localStream ;
    let pc1;
    let pc2;
    btnLocal.onclick=start;
    btnConnect.onclick =call;
    function start() {
        navigator.mediaDevices.getUserMedia({
            video:true,
        })
            .then(res=>{
                getLocalStream(res)
            })
    }
    function getLocalStream(res) {
        videoLocal.srcObject =res;
        localStream=res;

    }

    function getRemoteStream(e) {
        videoRemote.srcObject=e.streams[0]
    }
    //pc1设置本地，pc2收到后设置远端。同时发起响应
    // desc 为sdp媒体信息格式
    function sendOffer(desc) {
        debugger;
        pc1.setLocalDescription(desc);
        // 发送desc到信令服务器
        // 从信令服务器接收远端的desc
        pc2.setRemoteDescription(desc);
        pc2.createAnswer()
        .then(sendAnswer)
            .catch(error=>console.log(error))

    }
    function sendAnswer(desc) {
        pc1.setRemoteDescription(desc);
        pc2.setLocalDescription(desc);
    }
    function call() {
         pc1 =new RTCPeerConnection();
         pc2 =new RTCPeerConnection();

         //  A拿到所有candidate 都交给B添加
        pc1.onicecandidate=(e)=>{
            pc2.addIceCandidate(e.candidate)
        }
        pc2.onicecandidate=(e)=>{
            pc1.addIceCandidate(e.candidate)
        }
        // 接收端收到流时处理
        pc2.ontrack =getRemoteStream;
        // 发送方往轨中添加数据
        localStream.getTracks().forEach(track=>{
            pc1.addTrack(track,localStream)
        })
        const offerOptions ={
            offerToReceiveVideo:1,
            offerToReceiveAudio:0,
        }
        //发起offer
        pc1.createOffer(offerOptions)
            .then(sendOffer)
            .catch(error=>console.log(error))
    }
</script>
</body>
</html>
```





![image-20210614180640241](https://tva1.sinaimg.cn/large/008i3skNgy1grhyenz8rrj30j50een2b.jpg)







## 媒体协商过程 myPeerConnection

offer:本地的媒体信息

setLocalDescription: 搜集本地的Candidate

![image-20210611063254037](https://tva1.sinaimg.cn/large/008i3skNgy1grdxhvmxo7j30ot0c8783.jpg)



![image-20210611063730898](https://tva1.sinaimg.cn/large/008i3skNgy1grdxmogirij30i50f6n17.jpg)





### 媒体协商方法

* createOffer
* createAnswer
* setLocalDescription
* addTrack





## SDP媒体信息规范

* 媒体格式
* 传输协议
* 传输IP和端口
* 媒体负载类型



## STUN/TURN服务器

服务器选型：

coTurn



### STUN/TURN 服务器的搭建

1. [git地址](https://github.com/coturn/coturn)

2. `sudo apt-get install coturn` 安装

3. ```undefined
   turnserver 启动
   ```

4. 服务器配置，配置的目录为：/etc/turnserver.conf

   ![image-20210614172532426](https://tva1.sinaimg.cn/large/008i3skNgy1grhx7wqtzyj30o804u429.jpg)

5. [测试](https://webrtc.github.io/samples/src/content/peerconnection/trickle-ice/)

使用brew安装，可以通过brew list xx命令查找安装位置。







### 基础实现

```html
// index.html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<button id="connserver">Connect Sig Server</button>
<div id="preview">
    <div >
        <h2>Local:</h2>
        <video id="localvideo" autoplay playsinline muted></video>
        <h2>Offer SDP:</h2>
        <textarea id="offer"></textarea>
    </div>
    <div>
        <h2>Remote:</h2>
        <video id="remotevideo" autoplay playsinline></video>
        <h2>Answer SDP:</h2>
        <textarea id="answer"></textarea>
    </div>
</div>
<script crossorigin="anonymous" integrity="sha512-iZIBSs+gDyTH0ZhUem9eQ1t4DcEn2B9lHxfRMeGQhyNdSUz+rb+5A3ummX6DQTOIs1XK0gOteOg/LPtSo9VJ+w==" src="https://lib.baomitu.com/socket.io/4.1.2/socket.io.js"></script>
<script src="https://webrtc.github.io/adapter/adapter-latest.js"></script>
<script src="main1.js"></script>
</body>
</html>
```



```js
// main.js
var localVideo = document.querySelector('video#localvideo');
var remoteVideo = document.querySelector('video#remotevideo');
var btnConn =  document.querySelector('button#connserver');
var pcConfig = {
    'iceServers': [{
        'urls': 'turn:stun.landinguy.top:3478',
        'credential': "bbbbbb",
        'username': "aaaaaa"
    }]
};

var localStream = null;
var remoteStream = null;

var pc = null;

var roomid =1;
var socket = null;
btnConn.onclick =start;
async function start() {
   const stream  = await getLocalStream();
        //设置本地流，完成之后进行连接
        localStream =stream;
        localVideo.srcObject =localStream
    await connectToSignalServer();
    socket.emit('join',roomid);
}
function getLocalStream() {
    // 获取本地流
    const constraints ={
        video:true,
        audio:false
    }
  return  navigator.mediaDevices.getUserMedia(constraints)

}
function connectToSignalServer() {
    socket =io.connect();
    socket.on('joined',(room,data)=>{
        createPeerConnection();
    })
    socket.on('otherjoin',(room,data)=>{
        console.log('otherjoin');
        call()
    })
    socket.on('message',(room,data)=>{
        console.log(data);
        switch (data.type){
            case 'offer':
                pc.setRemoteDescription(new RTCSessionDescription(data))
                pc.createAnswer()
                    .then(desc=>{
                        pc.setLocalDescription(desc)
                        socket.emit('message',roomid,desc)
                    })
                break;
            case 'answer':
                pc.setRemoteDescription(new RTCSessionDescription(data))
                break;
            case 'candidate':
                const candidate =new RTCIceCandidate({
                    sdpMLineIndex:data.label,
                    candidate:data.candidate
                })
                pc.addIceCandidate(candidate)
                break;
            default:
                break;
        }
    })
}
function   createPeerConnection() {
    pc =new RTCPeerConnection(pcConfig);
    pc.onicecandidate=(e)=>{
        if (e.candidate){
            socket.emit('message',roomid,{
                type:'candidate',
                label:e.candidate.sdpMLineIndex,
                id:e.candidate.sdpMid,
                candidate:e.candidate.candidate
            })
        }
    }
    pc.ontrack=(e)=>{
        remoteVideo.srcObject=e.streams[0];
    }
    localStream.getTracks().forEach(track=>{
        pc.addTrack(track,localStream)
    })
}
function call() {
    const offerOptions ={
        offerToReceiveVideo:1,
        offerToReceiveAudio:0,
    }
    pc.createOffer(offerOptions)
        .then(desc=>{
            pc.setLocalDescription(desc);
            socket.emit('message',roomid,desc)
        })
}
```

```js
//服务器端
const Koa =require('koa')
const app = new Koa();
const http = require('http');
const https =require('https');
const static =require('koa-static');
const path = require("path");
const fs = require("fs");
const { Server } = require("socket.io");

const options = {
    key: fs.readFileSync('./ssl/server.key'),  //ssl文件路径
    cert: fs.readFileSync('./ssl/server.pem')  //ssl文件路径
};


// const server1 = http.createServer(app.callback()).listen(8081);
const server2  =https.createServer(options,app.callback()).listen(443);
const io =new Server(server2)
const Router =require('koa-router');

const router =new Router();
const MAX_NUMS =2;
const  sslify =require('koa-sslify').default;
app.use(sslify());
app.use(static(path.join(__dirname,'./static')))


USERCOUNT =3;

io.on('connection', (socket)=> {

    socket.on('message', (room, data)=>{
        socket.to(room).emit('message',room, data);
    });

    socket.on('join', (room)=>{
        console.log('join');
        socket.join(room);
        var users = io.engine.clientsCount;
        console.log(users);
        if(users < USERCOUNT){
            socket.emit('joined', room, socket.id); //发给除自己之外的房间内的所有人
            if(users > 1){
                socket.to(room).emit('otherjoin', room, socket.id);
            }

        }else{
            socket.leave(room);
            socket.emit('full', room, socket.id);
        }

    });

    socket.on('leave', (room)=>{
        var myRoom = io.sockets.adapter.rooms[room];
        var users = (myRoom)? Object.keys(myRoom.sockets).length : 0;
        socket.to(room).emit('bye', room, socket.id);
        socket.emit('leaved', room, socket.id);
    });

});


app.use(router.routes()).use(router.allowedMethods())


```





###  RTP Media

> RTCPeerConnection的底层。

1. RTCRTPSender发送器
2. RTCRTPReceiver接收器

![image-20210622072531377](https://tva1.sinaimg.cn/large/008i3skNgy1grqou1xuvpj30o70br41w.jpg)



#### 控制传输速率





## 传输非音视频数据

pc.createDataChannel

