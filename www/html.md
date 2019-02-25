## HTML 拾遗

#### 基本标签

- [html](#html)
    - [head](#head)
    - [meta](#meta)
    - [link](#link)
    - [style](#style)
    - [script](#script)
    - title 即页面标题
    - [body](#body)
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

#### properties