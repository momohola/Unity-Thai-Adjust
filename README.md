# Unity-Thai-Adjust
Correctly render Thai on Unity, Fix Thai's vowels for Text&amp;TextMeshPro on Unity
> 前言：使用Unity开发的游戏需要支持泰语本地化，以及解决显示泰语时Unity的bug

现在很多游戏都需要显示泰语，下面将介绍Unity如何显示泰语，（仅介绍Unity字体方面的设置步骤以及Unity在显示泰语时的bug和解决方案，具体游戏里面如何切换语言不做过多介绍）

Unity里面显示文本的组件主要有下面几个：

>  - Text组件
>  - TextMesh组件（不介绍）
>  - TextMeshPro组件

TextMesh组件的解决方案和Text组件类似，所以不做介绍。本文章中所用到的资源位于Thai文件夹下面。

## 1、Text组件显示泰语
首先需要找到一套能支持泰语的字体，这里推荐Tahoma.ttf字体，当我们拿到这套字体之后，在Unity里面试试效果。

额。。。看起来很完美，文字都是正常显示的。但是这个音标和泰语的字符中间怎么有这么大的间隙呢？那是因为Unity字体渲染器缺乏对 GPOS 和 GSUB 的支持。因为泰语字体很大程度上依赖于这些功能，所以如果没有它们，渲染的图像看起来很难看。而且对于泰国人来说这样的文字不方便阅读。

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/2d1291b4fde64265935844795fca91d9.png#pic_center)

我们将文字复制到其他软件中看看效果：

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/1e8067e7154d42709c8d84fe6cc139a6.png#pic_center)

可以看到在其他软件中泰语的音标和字符之间没有任何间隙，这样看起来就舒服多了。接下来介绍如何在Unity里面实现类似效果。

对于Text组件显示泰语需要用ThaiFontUtility.Adjust接口在复制之前进行转换一下。在执行一下代码之后泰语音标就正常了。

```csharp
    private void Update()
    {
        text.text = ThaiFontUtility.Adjust("กิจกรรมเช็กอินฤดูใบไม้ร่วงสิ้นสุดแล้ว ระบบตรวจพบว่าคุณมีรางวัลล็อกอินที่ยังไม่ได้รับและได้ส่งทางจดหมายแล้ว กรุณาตรวจรับ");
    }
```
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/0c5b24772e3747418f380006abc238d7.png#pic_center)
调用这个接口的时机也可以放到Text组件里面给text赋值的时候
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/dd41341272bf49c484ad8b6921f07561.png#pic_center)

**注意：**
ThaiFontAdjuster 只能处理特殊字体。这种字体需要具备从 U+F700 到 U+F71A 的扩展字形，从而提供泰语字符的各种位置。这也就是为什么之前推荐使用Tahoma.ttf字体了。

**原理：**
其实原理就是将原本的字符替换为其他字符，被替换的字符含义不变，只是相对位置变了而已。替换规则如下：

```csharp
    // ========== EXTENDED CHARACTER TABLE ==========
    // F700:     uni0E10.descless    (base.descless)
    // F701~04:  uni0E34~37.left     (upper.left)
    // F705~09:  uni0E48~4C.lowleft  (top.lowleft)
    // F70A~0E:  uni0E48~4C.low      (top.low)
    // F70F:     uni0E0D.descless    (base.descless)
    // F710~12:  uni0E31,4D,47.left  (upper.left)
    // F713~17:  uni0E48~4C.left     (top.left)
    // F718~1A:  uni0E38~3A.low      (lower.low)
    // ==============================================
    // [base]             [top] -> [base]             [top.low]
    // [base]     [lower] [top] -> [base]     [lower] [top.low]
    // [base.asc]         [top] -> [base.asc]         [top.lowleft]
    // [base.asc] [lower] [top] -> [base.asc] [lower] [top.lowleft]
```
---
<br>
 
 ## 2、TextMeshPro组件显示泰语
第一步需要创建一套泰语的SDF字体，这里使用AlibabaSansThaiMedium.ttf字体（任意字体均可，无硬性要求），右键该字体 ->Create->TextMeshPro->FontAsset。

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/f5b8ff6290c04e10bafc9ad933d05f62.png)

然后选中改字体点击【Update Atlas Texture】

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/004c64af47174d9da7b2e3bc1aa55f47.png)

按照下面的示意图进行设置

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/ebc0c8ac39154e03837b66e5e7b62b41.png)

其中蓝色方框内的内容填入下面这个：

```c
③CНฉ์3v≧ü#fрฬVаผ以FРฌ6y฿&（iуฯYгฟ【I…ฏⅡ　9；|～Бโ)lา\□•ย	Lฒ④Ⅴ<⑥ข❦,oี_йล“O⑤ต?м่/한ุ！b¥ГศRธB어Мзจ๋ωuя≦"ếeใหUปEПซ5x¡%hтฮXвพH《2ฎⅠู8：{แ(kñั[еá—KฑⅣ。;~กไr+nิиäฤNด>◆ค็.qыืaлçวQ×ท①สAЛง๊1t·≥!dюо”Tบ》Dชํ4wД$gсíอWฝGСญ7zเ'）jะZภ】J②ฐⅢ、:＜}￥*，mệóำ] ร
MЧณ=？ฃๆ-pึ﻿к’
Pมถ@ฆ้0s≤ ‘cнéษSน
```
这一套操作下来就可以发现现在TextMeshPro组件现在就可以显示泰语字体了。

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/fb14d929392e47b09bc395e5179b93dc.png#pic_center)

但是，是不是发现了这个位置有点不正常，这里有两个字符重叠了（可以对照上面的Text组件的显示效果）。下面来解决TMP组件显示泰语音标重叠的问题。
<br>
解决这个问题也简单，使用AdjustWannayuk工具设置一下音标表的偏移值就行。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/365516f8ebc248869656f223c722b213.png#pic_center)


右键SDF的字体文件选择AdjustWannayuk命令，然后就设置好了。这个时候就正常了，而且不需要调用ThaiFontUtility.Adjust接口，音标的位置也是正确的。

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/bb595f8d454c4cd09e361697376a6171.png#pic_center)

最后感谢这两位博主：

https://github.com/Nolkeg/AdjustTHWannayuk

https://github.com/SaladLab/Unity3D.ThaiFontAdjuster
