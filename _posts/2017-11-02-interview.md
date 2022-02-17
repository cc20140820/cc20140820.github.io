---
title: 前端笔试小结
date: 2017-11-02
categories: interview
---

> 最近面试遇到的一些前端问题，在此把一些感兴趣的总结一下

1.HTML5 有哪些新特性、移除了哪些元素？如何处理 HTML5 新标签的浏览器兼容问题？如何区分 HTML 和 HTML5？

1)新特性：HTML5 已经不是 SGML 的子集，主要是关于图像，位置，存储，多任务等功能的增加

- **绘画`<canvas>`**
- **用于媒介回放的`<video>`和`<audio>`**
- **Web Storage 的本地存储 localStorage 和会话存储 sessionStorage**
- **语意化更好的内容元素，比如`<article> <aside> <footer> <header> <nav> <section> <hgroup>`**
- **表单控件:`<calendar> <date> <time> <email> <url> <search>`**
- **新的技术 webworker,websocket,Geolocation**

  2)移除了：

  - **纯表现的元素：`<basefont> <big> <center> <font> <s> <strike> <tt> <u>`**
  - **对可用性产生负面影响的元素：`<frame> <frameset> <noframes>`**

    3)当在页面中使用 HTML5 新标签时，可能会得到三种不同的结果：

- **新标签被当作错误处理并被忽略，在 DOM 构建时会当作这个标签不存在**
- **新标签被当作错误处理，并在 DOM 构建时，这个新标签会被构造成行内元素**
- **新标签被识别为 HTML5 标签，然后用 DOM 节点对其进行替换**

**解决方法 1：实现标签被识别**

> 通过 document.createElement（tagName）方法即可让浏览器识别新标签，浏览器支持新标签后，还可以为新标签添加 CSS 样式

**解决方法 2：JavaScript 解决方案**

a)使用 html5shim：
在<head>中调用以下代码：

```html
<!--[if lt IE 9]>
  <script>
    src = "http://html5shim.googlecode.com/svn/trunk/html5.js"
  </script>
<![endif]-->
```

b)使用 kill IE6
在</body>之前调用以下代码：

```html
<!--if lte IE 6]>
    <script src="http://letskillie6.googlecode.com/svn/trunk/letskillie6.zh_CN.pack.js"></script>
<![endif]-->
```

4)DOCTYPE 声明、新增的元素

---

2.请描述一下 cookies，sessionStorage 和 localStorage 的区别？

- **cookie 在浏览器和服务器间来回传递。 sessionStorage 和 localStorage 不会**
- **sessionStorage 和 localStorage 的存储空间更大**
- **sessionStorage 和 localStorage 有更多丰富易用的接口**
- **sessionStorage 和 localStorage 各自独立的存储空间**

---

3.如何实现浏览器内多个标签页之间的通信?

- **调用 localstorge、cookies 等本地存储方式**

---

4.11.CSS3 有哪些新特性？

- **CSS3 实现圆角（border-radius:8px），阴影（box-shadow:10px），文字特效（text-shadow），线性渐变（gradient），旋转（transform）**
- **transform:rotate(9deg) scale(0.85,0.90) translate(0px,-30px) skew(-9deg,0deg);//旋转,缩放,定位,倾斜
  增加了更多的 CSS 选择器 多背景 rgba**

---

5.页面导入样式时，使用 link 和@import 有什么区别？

- **link 属于 XHTML 标签，除了加载 CSS 外，还能用于定义 RSS, 定义 rel 连接属性等作用；而@import 是 CSS 提供的，只能用于加载 CSS**
- **页面被加载的时，link 会同时被加载，而@import 引用的 CSS 会等到页面被加载完再加载**
- **import 是 CSS2.1 提出的，只在 IE5 以上才能被识别，而 link 是 XHTML 标签，无兼容问题**

---

6.CSS 选择符有哪些？哪些属性可以继承？优先级算法如何计算？ CSS3 新增伪类有那些？

- **id 选择器（ # myid）**
- **类选择器（.myclassname）**
- **标签选择器（div, h1, p）**
- **相邻选择器（h1 + p）**
- **子选择器（ul > li）**
- **后代选择器（li a）**
- **通配符选择器（ \* ）**
- **属性选择器（a[rel = “external”]）**
- **伪类选择器（a: hover, li: nth – child）**

- **可继承的样式： font-size font-family color, UL LI DL DD DT**
- **不可继承的样式：border padding margin width height**

- **优先级:!important > id > class > tag**

- **X:nth-child(n)**
- **X:nth-last-child(n)**
- **X:nth-of-type(n)**
- **X:nth-lastof-type(n)**
- **X:first-child**
- **X:last-child**
- **input:enabled**
