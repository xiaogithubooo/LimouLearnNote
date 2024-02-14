[从 object 到 iframe——其他嵌入技术 - 学习 Web 开发 | MDN (mozilla.org)](https://developer.mozilla.org/zh-CN/docs/Learn/HTML/Multimedia_and_embedding/Other_embedding_technologies)

# 1.iframe

## 1.1.标签作用

提供了将整个`Web`页嵌入到另外一个网页的方法，类似`<img>`标签的嵌入。

## 1.2.标签属性

1. `allowfullscreen`：如果设置，`<iframe>`则可以通过全屏`API`设置为全屏模式（稍微超出本文的范围）

2. `frameborder`：如果设置为`1`，则会告诉浏览器在此框架和其他框架之间绘制边框，这是默认行为。`0`删除边框。不推荐这样设置，因为在 CSS 中可以更好地实现相同的效果`border: none;`

3. `src`：该属性与`<video>/`元素表示文档中的图像。`<img>`一样包含指向要嵌入文档的`URL`路径

4. `width`和`height`：这些属性指定你想要的`iframe`的宽度和高度

5. `sandbox`：该属性需要在已经支持其他 `<iframe>` 功能（例如 IE 10 及更高版本）但稍微更现代的浏览器上才能工作，该属性可以提高安全性设置

# 2.embeb

## 2.1.标签作用

 `<embed>` 元素将外部内容嵌入文档中的指定位置。此内容由外部应用程序或其他交互式内容源（如浏览器插件）提供。

## 2.2.标签属性（略）

# 3.object

## 3.1.标签作用

表示引入一个外部资源（`HTML`嵌入对象元素）这个资源有可能是图片、一个嵌入的浏览上下文、或者一个插件用到的资源。

## 3.2.标签属性（略）
