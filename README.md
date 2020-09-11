# vue-gsap-slider-wapper

一个基于 GSAP 动画实现的 Vue 轮播图组件。

核心文件： `components/SliderWapper.vue`

### 效果

![](https://cdn.jsdelivr.net/gh/fz6m/Private-picgo@moe/img/20200912035438.gif)

### 使用

需要安装 GSAP 动画依赖库：

```bash
    yarn add gsap
```

轮播图需要以如下形式传入组件：

```html
    <slider-wapper @click-img="" :images="" />
```

`images` ：轮播图数据的 `object`，结构为 `[{ src: 'https://domain.com/image.jpg' },...]`

`click-img` ：当点击中央轮播图时会触发该传入事件，其第一个参数为该轮播图在 `images` 中的数字索引

注：为了保证整个轮播图和网页边缘交错，你应该将 `body` 零边距化：

```css
    body {
        margin: 0;
        padding: 0;
    }
```



### 其他
创意来自 Vuetelemetry