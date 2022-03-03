---
title: 图片水印的生成方法
date: 2019-05-19
categories: note
tags: 图片水印
---

> 前些时候遇到了预览文档或图片加水印的需求，看资料了解到，水印分明水印(前端实现)和暗水印(后端实现)两类，明水印容易实现但也容易破解，暗水印相比更安全。

#### 明水印的实现方式

- ##### div 实现
- ##### canvas 背景图实现

需要加水印的图片：

```html
<div id="app">
  <img id="poster" src="./poster.jpeg" alt="" />
</div>
```

```css
#app {
  width: 400px;
  margin: 0 auto;
  position: relative;
}
#app #poster {
  width: 100%;
}
```

---

首先我们试试用添加 div 的方式实现水印

```javascript
function cssHelper(el, prototype) {
  for (let i in prototype) {
    el.style[i] = prototype[i]
  }
}

function handleWaterMark(waterMkrText, waterMkrWidth, waterMkrHeight, target) {
  const { clientWidth, clientHeight } = target
  const columns = Math.ceil(clientWidth / waterMkrWidth)
  const rows = Math.ceil(clientHeight / waterMkrHeight)
  const shadow = document.createElement("div")
  cssHelper(shadow, {
    position: "absolute",
    fontSize: `16px`,
    color: "#ffffff",
    opacity: 0.3,
    left: "0px",
    top: "0px",
    bottom: "0px",
    right: "0px",
    display: "flex",
    flexWrap: "wrap", //可转行
  })
  for (let i = 0; i < columns * rows; i++) {
    const item = document.createElement("div")
    item.innerHTML = waterMkrText
    cssHelper(item, {
      flexBasis: `1/${columns}`, //几列
      width: `${waterMkrWidth}px`,
      height: `${waterMkrHeight}px`,
      lineHeight: `${waterMkrHeight}px`,
      transform: `rotate(-15deg)`,
      userSelect: "none", //禁用框选
      whiteSpace: "nowrap",
      overflow: "hidden",
      textAlign: "center",
    })
    shadow.appendChild(item)
  }
  target.parentNode.appendChild(shadow)
}

handleWaterMark("鬼灭之刃", 80, 80, document.getElementById("poster"))
```

效果实现如下：
![311608625084_.pic_hd.jpg](https://segmentfault.com/img/bVcL4kW)

**实现思路：** 1.生成和图片尺寸一样的 shadow，通过绝对定位盖在图片上 2.通过图片的尺寸和水印的尺寸计算出水印有几行几列 3.通过 flex 布局将水印排列到 shadow 上

---

再来试试 canvas 背景图实现水印

markByCanvas.js

```javascript
function createWaterMark(waterMkrText, waterMkrWidth, waterMkrHeight) {
  const angle = -20
  const canvas = document.createElement("canvas")
  canvas.width = waterMkrWidth
  canvas.height = waterMkrHeight
  const ctx = canvas.getContext("2d")
  ctx.clearRect(0, 0, waterMkrWidth, waterMkrHeight)
  ctx.fillStyle = "#fff"
  ctx.globalAlpha = 0.5
  ctx.font = `16px`
  ctx.rotate((Math.PI / 180) * angle)
  ctx.fillText(waterMkrText, 0, waterMkrHeight / 2)
  return canvas.toDataURL()
}
const watermakr = document.createElement("div")
cssHelper(watermakr, {
  position: "absolute",
  left: "0px",
  top: "0px",
  bottom: "0px",
  right: "0px",
  pointerEvents: "none",
  backgroundRepeat: "repeat",
  backgroundImage: `url(${createWaterMark("鬼灭之刃", 80, 80)})`,
})
document.getElementById("app").appendChild(watermakr)
```

效果实现如下：
![321608629593_.pic_hd.jpg](https://segmentfault.com/img/bVcL4YO)

** 实现思路：** 1.利用`canvas` 绘制一个水印 2.通过`canvas.toDataURL()` 来拿到文件流的 url 3.将 url 填充在一个元素的背景中，设置背景图片的属性为`repeat`

---

#### 明水印防破解方式

以上的实现方式，还是很容易破解的，因为原理都是在图片上层添加遮罩实现的，只要用 F12 删除遮罩的 Dom 即可(这也太 EZ 了 😭)

为了避免出现这种事，前端还是可以做出点努力的，这就用到了`Mutation Observer API`,MutationObserver 是元素观察器，字面上就可以理解这是用来观察 Node 变化的。

在这里我们的监听对象为`<div id="app"></div>`, 一旦监听到我们的水印元素被删除，或者属性修改，我们就重新生成一个。可以这样实现：

```javascript
// 观察器的配置（需要观察什么变动）
const config = { attributes: true, childList: true, subtree: true }
// 当观察到变动时执行的回调函数
const callback = function (mutationsList, observer) {
  for (let mutation of mutationsList) {
    mutation.removedNodes.forEach(function (item) {
      if (item === watermakr) {
        document.getElementById("app").appendChild(watermakr)
      }
    })
  }
}
// 监听元素
const targetNode = document.getElementById("app")
// 创建一个观察器实例并传入回调函数
const observer = new MutationObserver(callback)
// 以上述配置开始观察目标节点
observer.observe(targetNode, config)
```

大功告成，再也不怕遮罩 Dom 被手动删除了～
遗憾的是，尽管如此，还是有破解方法，比如删除遮罩 Dom 前先禁用浏览器的 JavaScript。。

---

#### 总结

使用 canvas 作背景或直接添加 div 就是明水印比较常规的实现方式啦，就算是明水印也可以通过 Mutation Observer 做些简单的防护，但是对于直接禁用 Javascript 的浏览器就束手无策了 😄。
