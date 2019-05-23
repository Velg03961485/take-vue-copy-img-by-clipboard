# take-vue-copy-img-by-clipboard
火箭 (*^▽^*)在vue中使用clipboard.js API 点击复制，把一张图片复制到粘贴板中，然后复制到微信对话框中
<br>
此功能可以使用在vue项目中，通过点击触发copy一张图片到粘贴板中，并能够复制到微信对话中
<br>
![image](https://github.com/Velg03961485/take-vue-copy-img-by-clipboard/blob/master/img/take.gif)
<br>
#安装clipboard
<br>
```
npm install clipboard --save
```
<br>
正常模式下，clipboard只可以能复制文本
<br>
有人提出可以把图片使用canvas生成一遍，然后再转成base64的格式，然后复制base64
<br>
这样是一个方式，但是，经过实践，确实不能达到要求

#查看clipboard API
<br>
[clipboard API](https://w3c.github.io/clipboard-apis/#override-copy)
<br>
其中有这么介绍
![image](https://github.com/Velg03961485/take-vue-copy-img-by-clipboard/blob/master/img/APIType.png)
<br>
是可以使用iamges的类型进行粘贴复制的
<br>
但是文档对使用images的介绍却这样说:
    ```
    To prevent malicious image data from being placed on the clipboard, the image data may be transcoded to produce a safe version of the image.
    ```
<br>
意思大概就是，要保证安全模式下进行复制图片（(＃￣～￣＃)，对如何复制图片是一点都没有讲解，我去看了github 上的issues，有人确认提出如何copy img，结果到了最后，竟然发现都是在讨论，在复制图片的时候如何保证安全性，通过浏览器的认证）
<br>
我就想默默，如果是存在服务器的图片，应该是已经经过安全校验单的吧0.0
<br>
看看源码吧，还是有方法的，这里确认通过另一种方式实现的
<br>


#实现copy
<br>
####安装完后，要在页面js中import引入
<br>
```
import Clipboard from 'clipboard';
```
<br>
不要问为什么不在全局，因为我测试在全局的时候，会发现new 的函数会找不到
<br>
####vue中
```
<div class="globalSearchBtn posterDivRightBtn btn" @click="clickCopy_b" id="aaa">
     <a class="globalSearchBtn_a">复制</a>
</div>

<div class="posterDivRight_text" id="bar">复制图片到剪切板可直接粘贴发送微信群</div>

<div id="wechat-copy-main">
    <img class='' src='//dev-api.yy.ibetwo.com/upload/2019/05/09/15573952783104.png' style='width:50px;' >
</div>
```
<br>
这里的讲解:
<br>
aaa id  是必须存在的，因为要通过这个id触发函数，虽然这里不是JQuery 0.0
<br>
复制的元素的是img，这个img的标签必须被一个div标签包裹着，需要通过，外部元素的id找到里面的Img的内容
<br>
wechat-copy-main id 也是必须存在的，这里会通过getElementById，找到img
<br>
img的地址，一定要去掉 http:
<br>
####js中
<br>
```
clickCopy_b(){
    var a = document.getElementById("wechat-copy-main");
    console.log(a);
    this.copyFunction(a, "#aaa");
},

copyFunction(t, a){
    let _this = this;
    var e = new Clipboard(a, {
        target: function () {
            return console.log(t, a),
            t
        }
    });
    e.on("success", function (t) {
        _this.$message({
            message: '复制成功',
            type: 'success'
        });
        t.clearSelection()
    }),
    e.on("error", function (t) {
        // console.log('复制失败');
        _this.$message({
            message: '复制失败',
            type: 'error'
        });
        t.clearSelection()
    })
},
```
<br>
这样就轻松实现了，在pc端，通过一键点击就可以复制一张图片到粘贴板上
<br>
并能直接copy到微信的对话中



