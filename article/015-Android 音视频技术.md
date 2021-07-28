# Android 音视频技术

## 1. 整体流程

以手机直播为例，其整体流程如下：

![image](https://user-images.githubusercontent.com/87458342/127281849-20717a61-791a-48b5-b987-b35fe0bfaecc.png)

## 2. 数据采集

### 2.1. 音频采集

音频采集涉及到以下几点：

检测麦克风是否可以使用；
需要检测手机对某个音频采样率的支持；
在一些情况下需要对音频进行回声消除处理；
音频采集时设置正确的缓冲区大小。

在 Android 系统中，一般使用 AudioRecord 或者 MediaRecord 来采集音频。AudioRecord 是一个比较偏底层的 API,它可以获取到一帧帧 PCM 数据，之后可以对这些数据进行处理。而 MediaRecorder 是基于 AudioRecorder 的 API (最终还是会创建AudioRecord 用来与 AudioFlinger 进行交互) ，它可以直接将采集到的音频数据转化为执行的编码格式，并保存。

### 2.2 视频采集

视频采集涉及到以下几点：

检测摄像头是否可以使用；
摄像头采集到的图像是横向的，需要对采集到的图像进行一定的旋转后再进行显示；
摄像头采集时有一系列的图像大小可以选择，当采集的图像大小和手机屏幕大小比例不一致时，需要进行特殊处理；
Android 手机摄像头有一系列的状态，需要在正确的状态下才能对摄像头进行相应的操作。
Android 手机摄像头的很多参数存在兼容性问题，需要较好地处理这些兼容性的问题。

在 Android 系统下有两套 API 可以进行视频采集，它们是 Camera 和 Camera2 。Camera是以前老的 API ，从 Android 5.0(21) 之后就已经放弃了。和音频一样，也有高层和低层的 API，高层就是 Camera 和 MediaRecorder，可以快速实现编码，低层就是直接使用 Camera，然后将采集的数据进行滤镜、降噪等前处理，处理完成后由 MediaCodec 进行硬件编码，最后采用 MediaMuxer 生成最终的视频文件。

## 3. 数据处理

### 3.1 音频处理

可以对音频的原始流做处理，如降噪、回音、以及各种 filter 效果。

### 3.2 视频处理
现在抖音、美图秀秀等，在拍摄，视频处理方面，都提供了很多视频滤镜，而且还有各种贴纸、场景、人脸识别、特效、添加水印等。

其实对视频进行美颜和添加特效都是通过 OpenGL 进行处理的。Android 中有 GLSurfaceView，这个类似于 SurfaceView，不过可以利用 Renderer 对其进行渲染。通过 OpenGL 可以生成纹理，通过纹理的 Id 可以生成 SurfaceTexture，而 SurfaceTexture 可以交给 Camera，最后通过纹理就将摄像头预览画面和 OpenGL 建立了联系，从而可以通过 OpenGL 进行一系列的操作。

美颜的整个过程无非是根据 Camera 预览的纹理通过 OpenGL 中 FBO 技术生成一个新的纹理，然后在 Renderer 中的onDrawFrame() 使用新的纹理进行绘制。添加水印也就是先将一张图片转换为纹理，然后利用 OpenGL 进行绘制。添加动态挂件特效则比较复杂，先要根据当前的预览图片进行算法分析识别人脸部相应部位，然后在各个相应部位上绘制相应的图像，整个过程的实现有一定的难度，人脸识别技术目前有 OpenCV、Dlib、MTCNN 等。

## 4. 数据编码

### 4.1 音频编码

Android 中利用 AudioRecord 可以录制声音，录制出来的声音是 PCM 声音，使用三个参数来表示声音，它们是：声道数、采样位数和采样频率。如果音频全部用 PCM 的格式进行传输，则占用带宽比较大，因此在传输之前需要对音频进行编码。

现在已经有一些广泛使用的声音格式，如：WAV、MIDI、MP3、WMA、AAC、Ogg 等等。相比于 PCM 格式而言，这些格式对声音数据进行了压缩处理，可以降低传输带宽。对音频进行编码也可以分为软编和硬编两种。软编则下载相应的编码库，写好相应的 JNI，然后传入数据进行编码。硬编则是使用 Android 自身提供的 MediaCodec。

>硬编码和软编码的区别是：软编码可以在运行时确定、修改；而硬编码是不能够改变的。

### 4.2 视频编码

在 Android 平台上实现视频的编码有两种实现方式：一种是软编，一种是硬编。软编的话，往往是依托于 cpu，利用 cpu 的计算能力去进行编码。比如我们可以下载 x264 编码库，写好相关的 JNI 接口，然后传入相应的图像数据。经过 x264 库的处理以后就将原始的图像转换成为 h264 格式的视频。

硬编则是采用 Android 自身提供的 MediaCodec，使用 MediaCodec 需要传入相应的数据，这些数据可以是 YUV 的图像信息，也可以是一个 Surface，一般推荐使用 Surface，这样的话效率更高。Surface 直接使用本地视频数据缓存，而没有映射或复制它们到 ByteBuffers；因此，这种方式会更加高效。在使用 Surface 的时候，通常不能直接访问原始视频数据，但是可以使用ImageReader 类来访问不可靠的解码后 (或原始) 的视频帧。这可能仍然比使用 ByteBuffers 更加高效，因为一些本地缓存可以被映射到 direct ByteBuffers。当使用 ByteBuffer 模式，可以利用 Image 类和 getInput/OutputImage(int) 方法来访问到原始视频数据帧。

## 5. 音视频混合

下面我盗了一张图，画图实在太费时间：

![image](https://user-images.githubusercontent.com/87458342/127282286-ce81d398-43a8-4864-a615-0b87af159e44.png)

以合成 MP4 视频为例：
1. 整体来看，合成的 MP4 文件，视频部分为 H.264 编码格式的数据，音频部分为 AAC 编码格式的数据。
2. 通过 MediaMuxer 提供的接口-writeSampleData()，将 H.264 和 AAC 数据分别同时写入到 MP4 文件。

## 6. 数据传输

目前比较主流的视频推流协议有 RTMP 协议、RTSP 协议。

### 7. 需要用到的技术

涉及到如下技术，我将从图像、音频、视频的顺序来罗列：
* Camera、Camera2。
* SurfaceView、TextureView、SurfaceTexture、GLSurfaceView。
* OpenGL ES。
* OpenCV、DLIB。
* YUV、PCM、H.264、H.265、ACC。
* AudioRecord、AudioTrack。
* MediaRecorder。
* MediaCodec。
* MediaExtractor、MediaMuxer。
* ffmpeg、ijkplayer。
* RTMP、RTSP。

<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>

原文作者: 况众文
