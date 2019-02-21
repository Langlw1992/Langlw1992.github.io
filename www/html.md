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
`http-equiv`可选值: `X-UA-Compatible`、`refresh`
`name`：`author`:作者、`description`:描述、`keywords`:关键字、`referrer`:定义该文档发送http请求时的http-referrer、`viewport`:定义视窗大小、`robots`:seo相关

#### link
定义资源和当前文档的关系，必须单标签。

- `as`: 只有`rel="preload"`时可用，规定了请求内容的类型
- `crossorigin`:当当前页面存在跨域图片时，如果不进行CORS处理，使用这些图片的`canvas`将会被污染，此时调用该`canvas`的`toDataURL()`、`getImageData()`、`toBlob()`方法会造成`SecurityError`
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
      image.src = 'url from cdn'
    </script>
    ``` 
    ![cors image in canvas](http://albumlang.oss-cn-hangzhou.aliyuncs.com/test/20190221155440.png)
- `rel`: 申明链接和当前文档的关系，为一个链接属性列表：
    - alternate 可选类型


#### style 
   
#### script

#### body

#### properties