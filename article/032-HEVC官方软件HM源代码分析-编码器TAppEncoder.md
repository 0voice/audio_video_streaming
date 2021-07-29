# HEVC官方软件HM源代码分析-编码器TAppEncoder

## 函数调用关系图

HM中的HEVC视频编码器TAppEncoder的函数调用关系图如下所示。

![image](https://user-images.githubusercontent.com/87458342/127458355-23bc552f-8c13-49d7-afb1-ba9b05f133d7.png)

下面解释一下图中关键标记的含义。

>函数背景色<br/>
>* 函数在图中以方框的形式表现出来。不同的背景色标志了该函数不同的作用：
>* 白色背景的函数：不加区分的普通内部函数。
>* 黄色背景函数：滤波函数（Filter）。用于环路滤波，半像素插值，SSIM/PSNR的计算。
>* 绿色背景的函数：CU编码函数（Encode）。通过对残差的DCT变换、量化等方式对CU进行编码。
>* 紫色背景的函数：熵编码函数（Entropy Coding）。对CU编码后的数据进行CABAC熵编码。
>* 浅蓝色背景函数：码率控制函数（Rate Control）。对码率进行控制的函数。

>箭头线<br/>
>* 箭头线标志了函数的调用关系：
>* 黑色箭头线：不加区别的调用关系。
>* 黄色箭头线：滤波函数（Filter）之间的调用关系。
>* 绿色箭头线：CU编码函数（Encode）之间的调用关系。
>* 紫色箭头线：熵编码函数（Entropy Coding）之间的调用关系。
 
>函数所在的文件<br/>
>* 每个函数标识了它所在的文件路径。

下文记录结构图中的几个关键部分。

## 普通内部函数

普通内部函数指的是TAppEncoder中还没有进行分类的函数。例如：

>编码器的main()函数中调用的TAppEncTop类的配置读取函数parseCfg()、编码函数encode()等。<br/>
>编码器最主要的函数：TEncTop中的encode()、TEncGOP中的compressGOP()、TEncSlice的compressSlice()等。

## CU编码函数

CU编码函数通过运动估计、DCT变换、量化等步骤对图像数据进行编码。编码的工作都是在TEncCu类中的compressCtu()中完成的。compressCtu()调用了xCompressCU()完成了CU的编码工作。xCompressCU()本身是一个递归调用的函数，其中的xCheckRDCostInter()完成了分析帧间CU编码代价的工作；其中的xCheckRDCostIntra()则完成了分析帧内CU编码代价的工作。

## 熵编码函数

熵编码函数使用CABAC的方式对CU编码后的数据进行熵编码。熵编码的工作都是在TEncCu类中的encodeCtu()中完成的。

## 环路滤波函数

环路滤波函数对重建帧数据进行滤波，去除方块效应和振铃效应。TComLoopFilter类的loopFilterPic()完成了去块效应滤波器的工作； TComSampleAdaptiveOffset类的SAOProcess()完成了去除振铃效应的SAO滤波器的工作。

## 码率控制函数

码率控制模块函数分布在lencod源代码不同的地方，包括rc_init_seq()、rc_init_GOP()、rc_init_frame()、rc_handle_mb()等。

<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>

原文作者：雷霄骅
