## 1. 懒加载占位图自适应
图片懒加载效果的目的: 占位，清除页面抖动。
```
// html
<div class="img-ratio">
  <img src="">
</div>

// css
.img-ratio {
  position: relative;
  width: 100%;
  padding-top: 75%;
}

.img-ratio > img {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}
```

## 2. 移动端横向滚动条
> 应用场景：

> 隐藏移动端横向滚动条，目的是保持各个手机浏览器下的样式统一，因为移动端的滚动条样式属于浏览器内置样式，和浏览器有很大的关系。

> 解决思路：

> 利用最外层容器和内容容器的高度差，做[overflow: hidden]，隐藏滚动条，不能使用overflow-x: hidden;的原因是，这样横向滚动就失效了。

```
// html
<div class="wrapper">
  <ul>
    <li>1</li>
    <li>2</li>
    <li>3</li>
    <li>4</li>
    <li>5</li>
    <li>6</li>
  </ul>
</div>

// css
.wrapper {
  width: 340px;
  height: 80px;
  overflow: hidden;
}

.wrapper ul {
  width: 100%;
  height: 96px;
  margin: 0;
  padding: 0;
  white-space: nowrap;
  overflow-x: scroll;
}

.wrapper li {
  display: inline-block;
  width: 80px;
  height: 80px;
  margin-right: 10px;
  background-color: #ddd;
  line-height: 80px;
  text-align: center;
  font-size: 20px;
}
```

## 3. footer 置底
> 应用场景：

> 有些页面因为内容不够，会出现footer上浮的情况，样式就很难看了。

> 解决思路：

> 1. margin && padding
> 2. flex 布局

```
// html结构
<html>
  <body>
    <div id="content">...</div>
    <div id="footer">...</div>
  </body>
</html>
```

#### 1. margin && padding
```
html, body {
  height: 100%;
}

// 一般footer底部高度是固定的，所以默认主体内容最小值要铺满整个高度，然后再负值回拉padding空白区，这段空白区就是给footer占位的。
// 3个要点：
// 1. 主体内容最小高度铺满
// 2. margin负值将底部上拉
// 3. padding值 = footer高度，防止上拉和原来的主体内容布局冲突
#content {
  min-height: 100%;
  margin-bottom: -30px;
  padding-bottom: 30px;
}

#footer {
  line-height: 30px;
  text-align: center;
}
```

#### 2. flex布局
```
// flex纵向布局，主体内容自适应，footer高度定值
// 除了低版本浏览器还存在兼容性，对于大部分浏览器而言，这种实现方式很方便。
html {
  height: 100%;
}

body {
  min-height: 100%;
  display: flex;
  flex-direction: column;
}

#content {
  flex: 1;
}

#footer {
  line-height: 30px;
  text-align: center;
}
```

## 4. 超宽的背景图片居中
> 应用场景：

> 对于有些非响应式网站，定宽1190px，但希望有一张满屏的背景图片

```
.wrapper {
  min-width: 1190px;
  height: 800px;
  background: url(test.jpg) no-repeat center top;
}

.mainContent {
  position: relative;
  width: 1190px;
  margin: 0 auto;
}
```

## 5. 伪元素实现图片水平垂直居中
> 原理：

> 利用的是行内元素对齐方式vertical-align: middle;

> 这种方式的好处是，兼容性好

```
// html
<div class="wrapper">
  <img src="test.png">
</div>

// css
.wrapper {
  width: 300px;
  height: 300px;
  border: 1px solid #ccc;
  
  // 对所有行内元素，都能实现水平居中
  text-align: center;
}

.wrapper::after {
  content: '';
  display: inline-block;
  
  // 高度100% + vertical-align， 就会以行内最高元素作为对齐基准，也就是伪元素
  height: 100%;
  vertical-align: middle;
}
```
