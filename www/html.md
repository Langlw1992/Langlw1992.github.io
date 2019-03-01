## HTML标签拾遗

#### 基本标签

- [html](#doctype)
    - [head](#head)
        - [meta](#meta)
        - [link](#link)
        - [style](#style)
        - [script](#script)
        - title 即页面标题
    - [body](#body)
        - [常规元素](#常规元素)
        - [a标签](#anchor)
        - [img](#图片)
        - [audio](#audio)
        - [video](#video)
        - [iframe](#iframe)
        - [canvas](#canvas)
- [全局属性](#properties)
    
#### doctype
```html
<!DOCTYPE html>
```
必须定义在文档的顶部位置，强制浏览器使用`standard mode`，即标准模式，而不是怪异模式

#### html
`html`标签是html文档的根元素，只能包含`head`和`body`元素，在xhtml中含有必要属性`xmlns`，在html中该属性可选。
可添加`lang`属性，有助于屏幕阅读技术确定需要陈述的语言

#### head
一般用于相关页面配置，一般包括`meta`、`link`、`style`、`script`等标签，文档中如果忽略了`head`，一般浏览器会自动创建
#### meta
表示文档的元数据,为单标签元素，置于`head`内部，主要包含`charset`、`http-equiv`、`name`、`content` 四个主要属性
通常格式
```html
<!--申明文档字符编码，一般都置于第一行-->
<meta charset="UTF-8">
<meta name="viewport"
      content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
<meta http-equiv="X-UA-Compatible" content="ie=edge">
<title>title</title>
```
- `http-equiv`
    - `X-UA-Compatible` 兼容相关
    - `refresh` 自动刷新
- `name`
    - `author` 作者
    - `description` 描述
    - `keywords` 关键字
    - `referrer` 定义该文档发送http请求时的http-referrer
    - `viewport` 定义视窗大小
    - `robots` seo相关

#### link
定义资源和当前文档的关系，必须单标签。

- `as`  只有`rel="preload"`时可用，规定了请求内容的类型
- `crossorigin` 分为`anonymous`(仅包含Origin,不携带Cookie和其他认证信息，证书等)、`use-credentials`(包含授权信息，包括Cookie).当`link`不含此属性时，资源以非CORS形式加载，即请求中不含`Origin`的`HTTP-Head`。当前页面存在跨域图片时，如果不进行CORS处理，使用这些图片的`canvas`将会被污染，此时调用该`canvas`的`toDataURL()`、`getImageData()`、`toBlob()`方法会造成`SecurityError`
    ```html
    <canvas id="canvas" width="500px" height="300px">
        your browser not support canvas
    </canvas>
    <script>
      const image = new Image()
      image.onload = function() {
        const canvas = document.querySelector('#canvas')
        const context = canvas.getContext('2d')
        context.drawImage(this, 0, 0, 500, 300)
        const imageData = context.getImageData(0, 0, 500, 300)
        console.log(imageData)
      }
      image.src = 'url from cdn with CORS'
    </script>
    ``` 
    ![cors image in canvas](http://albumlang.oss-cn-hangzhou.aliyuncs.com/test/20190221155440.png)
- `media` 媒体查询，常用于响应式加载
    ```html
      <link rel="preload" href="http://localhost:3000" as="image" crossorigin="anonymous" media="(min-width   400px)">  
      <link rel="preload" href="http://localhost:3000/1" as="image" crossorigin="anonymous" media="(max-width   400px)">
    ```
- `href`  申明资源位置
- `rel`  申明链接和当前文档的关系，为一个链接属性列表
    - `alternate`  可选类型，主要用于优化网站的SEO，或为用户提供页面可选样式(rel包含`stylesheet`)或多语言版本。用户通过搜索引擎可以对应访问设备适配的页面
    - `author`  一般定义一个联系作者的超链接
    - `canonical`  对同个页面有多个URL访问时的SEO优化
    - `dns-prefetch`  提示对用户需要点击访问的链接进行预先的DNS探查和协议握手，缩短响应时间
    - `help`  帮助文档
    - `icon`  页面title图标，苹果设备有特别属性`apple-touch-icon `、`apple-touch-startup-image`
    - `license`  一般放置权益说明的超链接
    - `manifest`  指定manifest.json文件，用于pwa
    - `nofollow`  页面链接免责
    - `noopener`  类似`window.opener`无法指向原始页面，处于安全考虑
    - `noreferrer`  防止当前页面跳转至其他页面后，新页面请求中的`Referrer`HTTP header显示为本页面的地址
    - `preconnect` 预先链接指定的地址，不涉及任何隐私信息，也不会下载内容，提高页面的打开速度
    - `prerender` 预加载内容并进行离屏预渲染
    - `preload`  预加载资源。可提前加载用户明确需要在页面中使用的资源，如字体文件等，可自定义优先级，加载的脚步不会直接执行
    - `prefetch` 预加载资源。和`preload`类似，一般用于页面和下一页可能用到的资源，优先级较低
    - `search`  指定一个用于搜索的页面
    - `stylesheet`  指定资源类型为样式文件
- `sizes`  定义`rel`为`icon`时的图标大小
- `type`  定义引用资源的MIME类型，如`text/html`

#### style 
样式信息
- `type` MIME类型，默认为`text/css`
- `media` 媒体查询适用
- `title` 当`rel= alternate` 时，指定样式表的名字
   
#### script
JavaScript或GLSL WebGL编程语言标签
- `async`  异步加载脚本，通常浏览器会同步记载阻塞页面。适用js动态加载脚本默认为异步加载，非严格按顺序执行
- `crossorigin`  适用CROS请求加载脚本
- `defer`  延迟加载脚本，使用`defer`标记的脚本的会在文档转化后`DOMContentLoaded`事件触发前触发，`DOMContentLoaded`事件会被推迟直至所有defer的脚本解析评估完毕，使用`defer`标记的脚本会按其在文档中的位置顺序执行。
- `type`  设置脚本的MIME类型，当`type="module"`时，会使用`es module`语法
- `nomodule`  当浏览器不支持`ES Module`时会执行，作为`module`的fallback
- `src`  资源路径，当存在该属性时，标签内部的脚本将会被忽略

#### noscript
当浏览器的JavaScript被禁用时呈现的内容

#### body
文档内容载体，文档中只能存在一个body

- `onhashchange`  hash值变动时触发

    ```html
    <body onhashchange="console.log('hash changed')">
      <script>window.location.hash = 'dick'</script>
    </body>
    ```
- `onload`  当文档载入完成时触发，需要页面所有的资源加载完成(图片、script等)
- `onmessage`  当文档收到信息时 //TODO
- `online`  联网失败触发
- `onoffline`  联网成功触发
- `onpopstate`  当页面`history`变动时触发
    ```html
    <body onpopstate="console.log('popstate trigger')">
        <script>
          /** history.pushState接收三个参数:
              1.state object 
              2.title
              3.URL
          **/
          history.pushState({page: 1}, "title 1", "?page=1");
        </script>
    </body>
    // 当页面返回到该页面时，触发popstate事件
    ```
#### 常规元素
`header`、`footer`、`aside`、`main`、`section`等容器元素
#### anchor
超链接
- `dowmload`  构建下载，如果有value,则将其作为保存文件的预设的名字。`download`只有在`href`为同源下有效
- `href`  链接的目标位置，包含url、hash(页面内某个元素id)
- `hreflang`  指定目标的人类语言类型
- `ping`  当点击标签时，会ping一次目标地址,value为指定的url，ping不会接受响应
- `referrerpolicy`  指定请求的referrer
    - `no-referrer`  请求中不出现`Referrer` header
    - `no-referrer-when-downgrade`  当请求为非https时，请求中不出现`Referrer` header
    - `origin`  发送`Referrer` header
    - `origin-when-cross-origin`  当跨域时不发送`Referrer` header
    - `strict-origin-when-cross-origin` mdn未标明
- `rel`  同`link -rel`
- `target`  指定目标资源的显示位置，当使用该属性时，设置`rel="referrer"`避免xss攻击
    - `_self`  在当前页面加载页面
    - `_blank`  在新tab 或新窗口打开
    - `_parent`  在当前页的父窗口打开，如果没有父窗口，则在当前页打开
    - `_top`  在当前页的祖先窗口打开，如无父窗口，则在当前页打开

#### 图片
- `src`  定义图片资源，相当与`scrset`中的`1x`情况
- `srcset`  提供图片资源和断点，各组之间使用逗号分隔，组内使用空格分隔`url`和`匹配情况`，可以设配多个分辨率和多种屏幕尺寸，`w`描述符匹配具体资源
- `sizes`  定义一组图片资源的尺寸，由逗号分隔，每组由媒体查询和宽高组成，最后一组为默认，不添加媒体查询，如果当前元素没有`srcset`存在，或者其中没有`w`描述符，则该属性失效

```html
只有src属性时以src为准，当存在sizes，经媒体查询获得图片尺寸，后由设备像素密度window.devicePixelRatio*图片尺寸确定对应的图片源
<img src="../img/480.jpg"
     alt="responsive images"
     srcset="../img/480.jpg 480w,../img/960.jpg 960w, ../img/1920.jpg 1920w"
     sizes="(max-width: 375px) 300px,(max-width: 450px) 400px, 455px"
>
```

#### audio
作为音频的根元素(设置src)或内嵌音频资源(内嵌`<source>`，可以有多个source作为fallback),`audio`含有`audioTrackList`元素，`onaddtrack`、`onremovetrack`事件均添加在该元素上
- `autoplay`  自动播放
- `controls`  是否显示浏览器默认的控制面板
- `corssorigin`  跨域设置，同`link`中的`crossorigin`
- `loop`  是否循环播放
- `muted`  是否静音
- `preload`  设置缓冲
    - `none`  不缓冲
    - `metadata`  只缓冲文件信息
    - `auto`  缓冲整个文件
    
```html
<audio id="music" preload="auto" muted>
    <source src="../audio/test.mp3">
</audio>
<script>
  const element = document.getElementById('music')
  element.addEventListener('loadedmetadata', () => {
    console.log('loadedmetadata trigger')
  })
  element.addEventListener('canplay', () => {
    console.log('canplay trigger')
  })
  element.addEventListener('playing', () => {
    console.log('playing')
  })
  element.addEventListener('pause', () => {
    console.log('pause trigger')
  })
  element.addEventListener('ended', () => {
    console.log('ended trigger')
  })
  element.addEventListener('timeupdate', () => {
    console.log('timeupdate trigger')
  })
</script>
```
#### video  
视频根元素，或嵌套`<source>`元素 [mdn示例](https://github.com/iandevlin/iandevlin.github.io/tree/master/mdn/video-player)

- `autoplay`  自动播放
- `poster`  作为视频的封面
- `playinline`  移动端视频播放经常会自动全屏，设置该属性可将视频作为行内元素放置
#### iframe
iframe有自己的`session history`和`document`对象

- `allowfullscreen`  是否可全屏，即调用`requestFullScreen()`
- `name`  可最为`windowName`用于区分和调用`window.open(url, windowName,[windowFeatures])`
- `referrerpolicy`  当在该页面发生请求时，HTTP Header中`referrer Header`的值，同[A标签](#anchor)
- `sandbox`  对框架中的内容应用额外的限制，当属性值为空是全部采用，或者使用空格分隔的形式放松部分限制
    
    - `allow-forms`  允许提交表单
    - `allow-modals`  允许模态窗口包含`window.alert()`、`window.confirm()` `window.print()`、`window.prompt()`以及`beforeunload`
    
#### canvas  
画布，可渲染canvas2d或者WebGL,只有`height`、`width`数值属性，通过`HTMLCanvasElement.getContext()`获取`canvas`的`context`
```html
<canvas id="canvas">your browser not support canvas</canvas>
<script >
    const canvas = document.getElementById('canvas')
    const context = canvas.getContext('2d')
    context.strokeStyle = '#aaaaaa'
    context.strokeRect(10, 10, 480, 280)
</script>
```
`HTMlCanvasElement.getContext(contextType，contextAttributes)`具有两个参数
- `contextType`  画布类型
    - `2d`  普通二位坐标画布，创建`CanvasRenderingContext2D`对象
    - `webgl`  3d渲染画布，`WebGLRenderingContext`version1
    - `webgl2` 3d渲染画布，`WebGL2RenderingContext` version2
    - `bitmaprenderer` 用于渲染已有的`Imagebitmap`canvas元素，创建`ImageBitmapRenderingContext`对象
- `contextAttributes`
    - 当context为`2d`时，只有`alpha`,设置canvas中是否包含alpha通道，如果设置为false，可加速canvas运行效率
    - 当context为`webgl`时，包含一下选项    
    
        - `alpha`  是否包含alpha通道
        - `antialias`  是否抗锯齿
        - `depth`   色深是否达到16bit
        - `failIfMajorPerformanceCaveat`  当系统性能较差时，是否创建context
        - `powerPreference`  GPU的性能设置
        - `premultipliedAlpha`  //TODO
        - `preserveDrawingBuffer`  //TODO
        - `stencil`  //TODO
- `CanvasRenderingContext2D` API
    - `clearRect` 清除一块矩形区域，以`point`和`size`为参数
    - `fillRect`  以当前`fillStyle`填充一块区域
    - `strokeRect`  为给定的矩形区域描边  
    - `rect`  生成一个矩形path,不会直接绘制，可用于fill和stroke
        ```html
          <script>
              const context = document.querySelector('canvas').getContext('2d')
              context.fillStyle = 'red'
              context.fillRect(0,0,200,100)
              context.strokeStyle = 'blue'
              context.strokeRect(10,10,180,80)
              context.clearRect(20,20,160,60)
          </script>
        ```
    - `fillText`  在画布上填充文本，(text,x,y,\[maxWidth])
    - `stokeText`  字体描边
    - `measureText`  测量指定字符串的长度
    - `beginPath`  创建一个新的路径
    - `closePath`  闭合路径，若原路径已闭合，则不做处理
    - `arc`  画圆弧,不会改变当前点位置
    - `arcTo`   根据当前点和两个控制点画圆弧，会改变当前点的位置
    - `isPointInPath`  判定指定点是否在指定的path区域内
    ```html
    <script>
        const canvas = document.getElementById('canvas')
        const context = canvas.getContext('2d')
        context.clearRect(0, 0, canvas.width, canvas.height)
        const path = new Path2D()
        path.rect(10, 10, 180, 80)
        context.strokeStyle = '#aaa'
        context.stroke(path)
        canvas.addEventListener('mousemove', function(event) {
            if (context.isPointInPath(path, event.clientX, event.clientY)) {
                context.fillStyle = 'red'
            } else {
                context.fillStyle = 'transparent'
            }
            context.clearRect(0, 0, canvas.width, canvas.height)
            context.fill(path)
            context.stroke(path)
        })
    
    
    </script>

    ```
    - `isPintInStroke`  判定指定点是否在指定的描边区域内
    - `clip` 对区域裁剪
    ```html
        // clip策略默认nonzero，即相交区域重叠，evenodd：相交区域镂空
        const canvas = document.getElementById('canvas')
        const context = canvas.getContext('2d')
        const path = new Path2D()
        path.rect(80, 10, 29, 130)
        path.rect(40, 50, 100, 50)
        context.clip(path, 'evenodd')
        context.fillStyle = 'blue'
        context.fillRect(0, 0, canvas.width, canvas.height)
    ```
    - `save`  保存当前的样式设置
    - `restore`  恢复之前保存的样式设置
    ```html
        const canvas = document.getElementById('canvas')
        const context = canvas.getContext('2d')
        context.fillStyle = 'red'
        context.save()
        context.fillStyle = 'blue'
        context.fillRect(0, 0, 50, 50)
        context.restore()
        context.fillRect(50, 50, 50, 50)
    ```
    - `drawImage`  在画布上绘制图片
    - `getImageData`  返回一个`ImageData`对象
    ...
    
#### form
表单元素，用户交互和提交信息
- `acttion`  表单提交URL
- `autocomplete`  自动完成表单项填充
- `enctype`  表单`method`为`post`时，`enctype`用于指定提交内容的`MIME`类型，包含`application/x-www-form-urlencoded`(默认类型)、`multipart/form-data`(上传文件)、`text/plain`(文本类型)
- `method`  http method,包括`post`、`get`、`dialog`
- `novalidate`  是否不校验表单
- `target`  提交后的呈现形似，同`<a>`中target，附加`iframe`name可选，

#### input
input是html中交互的最主要元素，在不同设备下具有大量可选类型
- `text`  最基本类型，输入字符
- `button`  基本按钮
- `checkbox`  多选 
- `color`  颜色选择器
- `date`  日期选择器
- `date-local`  当地时区日期
- `file`  文件选择
- `password`  密码输入
- `range`  范围选择器
- `time`  时间选择器
- `submit`  提交按钮
- `reset`  表单重置

#### properties