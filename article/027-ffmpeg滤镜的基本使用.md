# ffmpeg滤镜的基本使用

## 滤镜

滤镜主要是用来实现图像的各种特殊效果。

ffmpeg转码流程图：

![image](https://user-images.githubusercontent.com/87458342/127450563-55f6c0f1-1ec4-4ca0-9a2c-5fc87ced45f9.png)

从图中可以看到滤镜前后画的是虚线，表示可有可无，在术语中，滤镜指的是在编码之前针对解码器解码出来的原始数据（即音视频帧）进行处理的动作，我们还可以称它为过滤器。

ffmpeg内置了大概近400种滤镜，我们可以用 ffmpeg -filters 命令查看所有的滤镜，也可以用命令 ffmpeg -h filter=xxx 或者查看官方文档了解每一种滤镜。

实际在大部分音视频的处理过程中都离不开滤镜，所以你应该能明白其重要性。

多个滤镜可以结合在一起使用形成滤镜链或者滤镜图，在每一个滤镜中，不仅可以对输入源进行处理，A滤镜处理好的结果还可以作为B滤镜的输入参数，通过B滤镜继续处理。

针对滤镜的处理，ffmpeg提供了两种处理方式，简单滤镜和复杂滤镜。

## 简单滤镜

简单滤镜指的是只有一个输入和输出，而且保证输入和输出的流类型相同。

比如在末尾提到的把原视频 r3.mp4 等比例缩放一倍
```C++
ffmpeg -i r3.mp4 -vf scale=272:480 -y filter.mp4
```

-vf 是 -filter:v 的简写，类似的我们还可以使用 -filter:a 或者 -af 针对音频流做处理。

>-filter的语法规则：-filter[:stream_specifier] filtergraph (output,per-stream)
>stream_specifier流的类型我们一般用a表示音频，v表示视频，filtergraph表示具体的滤镜，这里用的是scale滤镜。

scale滤镜用于调整视频的大小，比如等比例缩放、等比例放大，不做等比例操作输出就变形了，变形结果我们一般不考虑。

因为我们知道原视频 r1ori.mp4 的分辨率是 544x960，所以等比例缩放一倍，上面的命令直接指定了 272x480，scale滤镜自带很多参数，我们介绍几个常用的。

>in_w in_h 或者 iw ih 表示输入视频的宽高
>out_w out_h 或者 ow oh 表示输出视频的宽高

当然不一定是视频，输入输出也可以是图片。

所以原视频缩放一倍我们还可以这样写：

```C++
ffmpeg -i r3.mp4 -vf scale=iw/2:ih/2 -y filter.mp4
```

##### 问题1：如果要把原视频的宽度调整为300且保持原分辨率，怎么办？

列一个方程 544/960 = 300/x ，x=300x960/540，很麻烦，结果还不一定能整除，为此我们可以直接指定高度等于-1，它会自动做等比例处理。
```C++
ffmpeg -i r1ori.mp4 -vf scale=300:-1 -y filter.mp4
```
结果发现转码失败了，提示

```C++
[libx264 @ 0x7ff509053a00] height not divisible by 2 (300x529) 
Error initializing output stream 0:0 -- 
Error while opening encoder for output stream #0:0 - 
maybe incorrect parameters such as bit_rate, rate, width or height [aac @ 0x7ff50904e200] 
Qavg: 28010.410 [aac @ 0x7ff50904e200] 2 frames left in the queue on closing
```

提示我们 height not divisible by 2 (300x529)即高度529不能被2整除。这是因为一些编解码器要求很多视频的宽高必须是n的倍数（这里n是2），所以我们写脚本处理视频或者图片宽高的时候，切记不要使用-1，正确的用法是使用-2。

```C++
ffmpeg -i r1ori.mp4 -vf scale=300:-2 -y filter.mp4 输出结果视频的分辨率是 300 × 530
```

##### 问题2：老板为了刁难你，提出了一个新的要求：“我想要所有输出视频的分辨率是 300x500且不能变形”，怎么办？

我们知道3:5的宽高比是很少见的，现在常见的分辨率是16:9、4:3，也就是说原视频我们必须要经过一番处理才可以满足老板的变态需求。

针对原视频 r1ori.mp4，如果保证宽度是300，等比例缩放后高度是530，强制设置高度为500就会变形，也就是说我们只能让高度等于500，尽量缩小宽度试试。

```C++
ffmpeg -i r1ori.mp4 -vf scale=-2:500 -y filter.mp4 输出的结果视频的分辨率是284x500
```

![image](https://user-images.githubusercontent.com/87458342/127451223-9d5ef6d0-89dc-47f2-908d-263590f39fe8.png)

如上图，蓝色框表示视频的真实宽高，红色框表示目标宽高，有些像html中的css一样，可以给空出来的部分填充颜色即内边距不就可以了？

查阅了文档发现pad滤镜可以解决我们的问题。

>pad滤镜的语法规则：-pad=width[:height[:x[:y[:color]]]]

```C++
1、ffmpeg -i r1ori.mp4 -vf "scale=-2:500,pad=300:500:(300-iw)/2:0" -y filter2.mp4 
2、ffmpeg -i r1ori.mp4 -vf scale=-2:500,pad=300:500:-1:0 -y filter.mp4 
3、ffmpeg -i r1ori.mp4 -vf scale=-2:500,pad=300:500:-1:0:black -y filter.mp4 
4、ffmpeg -i r1ori.mp4 -vf "scale=-2:500,pad=300:ih:(ow-iw)/2:0:green" -y filter.mp4
```

上面提供4中写法，我们以方法4做个简单介绍。

scale=-2:500，指原视频按照等比例缩放，高度等于500，就是上面大家看到的284x500。

pad=300:ih:(ow-iw)/2:0:green，300:ih即300:500就是红色框的宽高(ow-iw)/2，指的是红色框和蓝色框差值的一半，即两边各需要填充的范围；最后一个参数表示需要填充的颜色，默认是黑色 black，为了调试方便我们把颜色设为green。

现在我们保证了当前视频一定会按照300x500的比例输出且不会变形，但是请注意老板说的“所有输出视频”，也就是说输入视频的分辨率可能是200x300、544x960、500x400、200x800等等各种比例都要保证按照300x500输出，很显然，上面的写法不完全通用，怎么办？

现在我们已知原输入视频的宽高和想要的宽高，针对这种情况，我们制定一套处理规则即可解决：

1. 宽高都偏小，不拉伸，不缩放
2. 宽高都偏大，等比例缩小，以高度为准
3. 宽超出范围，等比例缩小，以宽为准
4. 高超出范围，等比例缩小，以高为准

在实际的开发过程中，我们要跟代码打交道，平时在命令行中的实现都是练习，所以基于该规则，我们有了下面一段代码

```PHP
<?php
declare(strict_types=1);

class CalculatorService
{
    /**
     * 用户视频分辨率转换
     * 规则：
     *  宽高都偏小，不拉伸，不缩放
     *  宽高都偏大，等比例缩小，以高度为准
     *  宽超出范围，等比例缩小，以宽为准
     *  高超出范围，等比例缩小，以高为准
     * @param int $inputWidth 输入视频的宽度
     * @param int $inputHeight 输入视频的高度
     * @param int $outWidth 输出视频的宽高
     * @param int $outHeight 输出视频的高度
     * @return string scale
     */
    public function getSize(int $inputWidth, int $inputHeight, int $outWidth, int $outHeight): string
    {
        $scale = "";
        if ($inputWidth <= $outWidth && $inputHeight <= $outHeight) {
            $scale = "scale={$inputWidth}:{$inputHeight},pad={$outWidth}:{$outHeight}:-1:-1:green";
        } elseif (($inputWidth > $outWidth && $inputHeight > $outHeight)
            || ($inputHeight > $outHeight)
        ) {
            $scale = "scale=-2:{$outHeight},pad={$outWidth}:{$outHeight}:-1:0:green";
        } elseif ($inputWidth > $outWidth) {
            $scale = "scale={$outWidth}:-2,pad={$outWidth}:{$outHeight}:0:-1:green";
        }

        return $scale;
    }
}

$calculatorService = new CalculatorService();
var_dump($calculatorService->getSize(200, 300, 300, 500));
var_dump($calculatorService->getSize(544, 960, 300, 500));
var_dump($calculatorService->getSize(500, 400, 300, 500));
var_dump($calculatorService->getSize(200, 600, 300, 500));

// 结果
string(37) "scale=200:300,pad=300:500:-1:-1:green"
string(35) "scale=-2:500,pad=300:500:-1:0:green"
string(35) "scale=300:-2,pad=300:500:0:-1:green"
string(35) "scale=-2:500,pad=300:500:-1:0:green"
```

为了方便理解，大家可以参考下面的图一一对应。

![image](https://user-images.githubusercontent.com/87458342/127451486-e2de6757-b64d-4d3d-b3b5-51a1bcc6c09e.png)

## 复杂滤镜

相对于简单滤镜，复杂滤镜是可以处理任意数量输入和输出效果的滤镜图，它几乎无所不能。

复杂滤镜用命令 -filter_complex 表示，它还有一个别名 -lavfi。

上篇文章介绍到流和滤镜结合是一种最重要、最常用的方法。依然是将输入视频 r3.mp4 等比例缩放一倍，我们以手动选择流的方式为例。

```C++
ffmpeg -i r3.mp4 -filter_complex "[0]scale=272:480[out]" -map 0:a -map "[out]" -y filter.mp4
```

简单分析如下：

1. 命令 "[0]scale=272:480[out]" 中的[0]表示第一个输入的视频，因为要对视频做处理，所以也可以用[0:v]表示，如果要对音频单独处理，就需要用 [0:a] 了；
2. [0] 结合scale滤镜，表示的就是把第一个输入的视频作为scale滤镜的参数输入；
3. [out] 中括号是必须要的，out是自定义的一个别名，结合scale滤镜，表示的是把scale滤镜输出的结果命名为[out]，但并非是最终输出的结果，只能作为中间过程输出的一个结果；
4. -map "[out]" 就是直接选择[out] 流作为输出

我们说过，一个滤镜的输出作为另一个滤镜的输入，这样就极大的避免了写多条命令反复编解码操作，我们的原则只有一个，能用一条命令处理的绝不用两条命令。

>有损编解码器反复编解码操作会降低原视频质量。

比如现在要把原视频 r1ori.mp4 的中间部分裁剪出来，但仍保持原视频的分辨率544x960，如何做呢？

```C++
ffmpeg -i r1ori.mp4 -filter_complex "nullsrc=s=544x960[background]; \
crop=iw:(ih/2 - 110):0:250[middle]; \
[background][middle]overlay=shortest=1:x=(main_w-overlay_w)/2:y=(main_h-overlay_h)/2[out]" \
-map "[out]" 
-map 0:a 
-movflags +faststart 
-y fc.mp4
```

这个命令就显得稍微长了一些，在这条命令中使用了nullsrc、crop、overlay三种常见滤镜。

nullsrc滤镜用于创建一个空的视频，简单的说就是一个空的画布或者说是绿布，因为默认创建的颜色是绿色的。s用于指定画布的大小，默认是320x240，这里表示我们创建一个544x960的画布，并命名为background；

关于nullsrc还有很多种不同的用户，比如使用nullsrc和CIQRCodeGenerator创建一个“白狼栈”首页的二维码

```C++
ffmpeg -f lavfi -i nullsrc=s=200x200,coreimage=filter=CIQRCodeGenerator@inputMessage=\ 
http\\\\\://manks.top/@inputCorrectionLevel=H -frames:v 1 manks.png
```

crop滤镜用于裁剪视频，也就是说视频的任意区域任意大小，我们都可以裁剪出来。crop=iw:(ih/2 - 110):0:250[middle]; 这里我们裁剪原视频的中间部分并命名为middle；

overlay滤镜表示两个视频相互叠加，shortest官网是这么介绍的：“If set to 1, force the output to terminate when the shortest input terminates. Default value is 0.”，因为我们使用nullsrc创建了一个没有时间轴的画布，所以这里需要以middle的视频时间为最终时间，故设置为1。main_w和main_h表示主视频的宽高，overlay_w和overlay_h表示叠加视频的宽高。如果要把A视频叠加到B视频上，则main_w和main_h表示B视频的宽高，overlay_w和overlay_h表示A视频的宽高。合起来便是把middle叠加到background之上且置于background的中间（相当于有个叠加层的概念）；

最后一个参数是-movflags，它跟mp4的元数据有关，设为faststart表示会将moov移动到mdat的前面，在线播放的时候会稍微快一些。
