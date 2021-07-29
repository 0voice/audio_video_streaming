# webRTC是如何实现音视频的录制

## 什么是webRTC

“WebRTC (Web Real-Time Communications) 是一项实时通讯技术，它允许网络应用或者站点，在不借助中间媒介的情况下，建立浏览器之间点对点（Peer-to-Peer）的连接，实现视频流和（或）音频流或者其他任意数据的传输。WebRTC包含的这些标准使用户在无需安装任何插件或者第三方的软件的情况下，创建点对点（Peer-to-Peer）的数据分享和电话会议成为可能。”
总结下来，其实有四点：

1. 跨平台
2. （主要）用于浏览器
3. 实时传输
4. 音视频引擎

在稍稍深入学习了webRTC之后，我发现其不仅可以用于「音视频录制、视频通话」，还可以用在「照相机、音乐播放器、共享远程桌面、即时通信工具、P2P网络加速、文件传输、实时人脸识别」等场景上 —— 当然，是结合了其他众多技术的基础上完成。
不过这些中有几项似乎让我们很“熟悉”：照相机、人脸识别、共享桌面。这是因为RTC使用的是基于音视频流的API！

## webRTC音视频数据采集

实现数据传输最重要的就是数据采集了，这里有一个非常重要的API：

```C++
let promise=navigator.mediaDevices.getUserMedia(containts);
```

这个API会提示用户给予使用媒体输入的许可，媒体输入会产生一个MediaStream，里面包含了请求的媒体类型的轨道。此流可以包含一个视频轨道（来自硬件或者虚拟视频源，比如相机、视频采集设备和屏幕共享服务等等）、一个音频轨道（同样来自硬件或虚拟音频源，比如麦克风、A/D转换器等等），也可能是其它轨道类型。

它返回一个 Promise 对象，成功后会resolve回调一个 MediaStream 对象。若用户拒绝了使用权限，或者需要的媒体源不可用，promise会reject回调一个 PermissionDeniedError 或者 NotFoundError 。

这里最重要的就是“轨道”了：因为它是基于“流”的。它得到的是一个MediaSource 对象（这是一个stream对象）！也就意味着：如果要使用此结果，要么用srcObject属性，要么就得用 URL.createObjectURL() 转为url！

```C++
/**
* params:
*          object: 用于创建URL的File 对象、Blob对象或者MediaSource对象。
* return : 一个DOMString包含了一个对象URL，该URL可用于指定源 object的内容。
*/
objectURL= URL.create0bjectURL(object) ;
```

回到API本身，可以这么理解：通过它可以得到当前页面所有的音视频通道的集合，根据不同的接口将它们分为不同的“轨道”，我们可以对它们进行设置和输出配置项。

>在文章中可以看到实现“拍照”的部分就用到了这个API

我们先在页面上写个video元素：

```HTML
<video autoplay playsinline id="player"></video>
```

因为需要获取音视频流，而HTML5中相关的元素也就video和audio了，能同时获取这两个的就只有video元素了（如果你只需要音频流，你完全可以用audio元素）。

根据上面getUserMedia API 的用法，不难写出如下的语法：

```C++
navigator.mediaDevices.getUserMedia(constraints)
						.then(gotMediaStream)
						.catch(handleError);
```

## webRTC获取约束

“约束”是指：控制对象的一些配置项。约束分为两类：视频约束和音频约束。

在webRTC中，常用的视频约束有：

* width
* height
* aspectRatio：宽高比
* frameRate
* facingMode：摄像头翻转（前后摄像头）（这个配置主要是对于移动端来说，因为浏览器只有前置摄像头）
* resizeMode：是否进行剪裁

而常用的音频约束有：

* volume：音量
* sampleRate：采样率
* sampleSize：采样大小
* echoCancellation：回音消除设置
* autoGainControl：自动增益
* noiseSuppression：降噪
* latency：延迟（根据不同场景，一般来说越小延迟越小体验越好）
* channelCount：单/双声道
…

contraints是什么？官方把它称作“MediaStreamContraints”。通过代码我们可以更清晰地看到它：
```C++
dictionary MediaStreamContraints{
	(boolean or MediaTrackContaints) video = false;
	(boolean or MediaTrackContaints) audio = false;
}
```

它是负责对音视频约束的采集

* 如果video/audio是简单的设置为bool类型，则只是简单决定是否要采集
* 如果video/audio是Media Track，则可进一步设置比如：视频的分辨率、帧率、音频的音量、采样率等

根据上面说明，我们可以在代码中完善一下配置项 —— 当然，通常使用这种API第一件事是要判断用户当前浏览器是否支持：

```C++
if(!navigator.mediaDevices || !navigator.mediaDevices.getUserMedia){
	console.log('getUserMedia is not supported!');
	return;
}else{
	var constraints={
		// 这里也可以直接：video:false/true，则知识简单的表示不采集/采集视频
		video:{
			width:640,
			height:480,
			frameRate:60
		},
		// 这里也可以直接：audio:false/true，则只是简单的表示不采集/采集音频
		audio:{
			noiseSuppression:true,
			echoCancellation:true
		}
	}
	navigator.mediaDevices.getUserMedia(constraints)
							.then(gotMediaStream)
							.catch(handleError);

}
```

下面就该实现getUserMedia API 成功和失败时的回调函数了，这里我们要先搞懂“在回调时要干什么” —— 其实无非是将“获取到的流”交出去：给全局变量保存起来或者直接作为video/audio的srcObject值（成功）或者抛出错误（失败）

```C++
var videoplay=document.querySelector('#player');
function gotMediaStream(stream){
	// 【1】
	videoplay.srcObject=stream;
	// 【2】
	// return navigator.mediaDevices.enumerateDevices();
}
function handleError(err){
	console.log('getUserMedia error:',err);
}
```

>注释中用了一个API：mediaDevices.enumerateDevices()。MediaDevices方法列举了所有可用的媒体输入和输出设备（如麦克风、相机、耳机等）。返回的承诺通过描述设备的MediaDevice 信息阵列得到解决。<br/>
>也可以把它看做上面说的“轨道集合”。比如这里如果你把注释按promise方式写出，就会发现它是一个数组，里面包含了三个“轨道”。两个audio（输入、输出）一个video：<br/>
>![image](https://user-images.githubusercontent.com/87458342/127454906-58feb013-531d-456c-b902-33c99bea710c.png)<br/>
>而当你输出stream参数后你会发现<br/>
>![image](https://user-images.githubusercontent.com/87458342/127454948-7fbf2ef1-ad14-4d05-82e7-a15224e4dc29.png)<br/>
>在一些场景下，通过这个API我们可以将一些设备配置暴露给用户

至此视频中的第一个效果——实时捕获音视频就完成了。
这也是下面的基础：不能捕获，又怎么录制呢？

先再加一些需要的节点：

```HTML
<video id="recplayer" playsinline></video>
<button id="record">Start Record</button>
<button id="recplay" disabled>Play</button>
<button id="download" disabled>Download</button>
```

前面说了，经过捕获后得到的是一个“流”对象，那么接收时也一定需要一个能拿到流并进行操作的API ：MediaStream API！

MDN上是这样介绍的：“MediaRecorder 是 MediaStream Recording API 提供的用来进行媒体轻松录制的接口, 他需要通过调用 MediaRecorder() 构造方法进行实例化。”
因为是 MediaStream Recording API 提供的接口，所以它有一个构造函数：

* MediaRecorder.MediaRecorder()：创建一个新的MediaRecorder对象,对指定的MediaStream 对象进行录制,支持的配置项包括设置容器的MIME 类型 (例如"video/webm" 或者 “video/mp4”)和音频及视频的码率或者二者同用一个码率

根据MDN上提供的方法，我们可以得到一个比较清晰的思路：先调用构造函数，将前面获取到的流传入，经过API的解析后拿到一个对象，在规定的时间切片下将他们依次传入数组中（因为至此工作基本就已经完成了，用数组接收方便以后转为Blob对象操作）：

```HTML
function startRecord(){
	// 定义一个接收数组
	buffer=[];

	var options={
		mimeType:'video/webm;codecs=vp8'
	}
	// 返回一个Boolean值,来表示设置的MIME type 是否被当前用户的设备支持.
	if(!MediaRecorder.isTypeSupported(options.mimeType)){
		console.error(`${options.mimeType} is not supported`);
		return;
	}

	try{
		mediaRecorder=new MediaRecorder(window.stream,options);
	}catch(e){
		console.error('Fail to create');
		return;
	}
	mediaRecorder.ondataavailable=handleDataAvailable;
	// 时间片
	// 开始录制媒体,这个方法调用时可以通过给timeslice参数设置一个毫秒值,如果设置这个毫秒值,那么录制的媒体会按照你设置的值进行分割成一个个单独的区块
	mediaRecorder.start(10);
}
```

这里面有两点需要注意的地方：

* 获取的stream流：因为之前捕获音视频的函数必然要封装起来，所以这里如果再要使用就一定要把这个流对象设置为全局对象 —— 笔者直接将其挂载到了window对象上，在前面代码中注释[1]的地方接收对象： window.stream=stream;
* 定义了一个buffer数组，和mediaRecorder对象一样，考虑到要在ondataavailable 方法中的使用以及在完成后需要停止继续捕获录制音视频流，也放在全局下声明变量

```C++
var buffer;
var mediaRecorder;
```

ondataavailable方法就是在录制数据准备完成后才触发的。在里面我们需要做的就是按照之前的思路将切片好的数据依次存入

```
function handleDataAvailable(e){
	// console.log(e)
	if(e && e.data && e.data.size>0){
		buffer.push(e.data);
	}
}

//结束录制
function stopRecord(){
	mediaRecorder.stop();
}
```

然后调用：

```C++
let recvideo=document.querySelector('#recplayer');
let btnRecord=document.querySelector('#record');
let btnPlay=document.querySelector('#recplay');
let btnDownload=document.querySelector('#download');
// 开始/停止录制
btnRecord.onclick=()=>{
	if(btnRecord.textContent==='Start Record'){
		startRecord();
		btnRecord.textContent='Stop Record';
		btnPlay.disabled=true;
		btnDownload.disabled=true;
	}else{
		stopRecord();
		btnRecord.textContent='Start Record';
		btnPlay.disabled=false;
		btnDownload.disabled=false;
	}
}
// 播放
btnPlay.onclick=()=>{
	var blob=new Blob(buffer,{type: 'video/webm'});
	recvideo.src=window.URL.createObjectURL(blob);
	recvideo.srcObject=null;
	recvideo.controls=true;
	recvideo.play();
}
// 下载
btnDownload.onclick=()=>{
	var blob=new Blob(buffer,{type:'video/webm'});
	var url=window.URL.createObjectURL(blob);
	var a=document.createElement('a');
	a.href=url;
	a.style.display='none';
	a.download='recording.webm';
	a.click();
}
```

文章到这里视频中的功能就基本实现了，但是还有一点问题：如果只想要录制音频，这时候在你说话的时候因为捕获流一直在工作所以其实这时候有两个声源 —— 你的声音和捕获到的你的声音。
所以我们需要将捕获时的声音关掉！
但是如果你根据前面说的在getUserMedia API中添加 volume:0 是不会有任何效果的 —— 因为至今还没有任何浏览器对这个属性支持。

但是联想到我们承载音视频的是一个video/audio容器，所以我们其实可以直接用其对应DOM节点的volume属性控制：

```C++
// 在前面代码中注释为【2】的地方添加代码
videoplay.volume=0;
```

<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>

原文作者： 恪愚
