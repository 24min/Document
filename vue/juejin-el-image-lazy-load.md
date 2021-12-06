![关于el-image懒加载在视图内但却没有显示图片这件事](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bd2683efa3fa43deb13fb91c0cbd4b15~tplv-k3u1fbpfcp-zoom-crop-mark:1304:1304:1304:734.awebp)

# 关于el-image懒加载在视图内但却没有显示图片这件事

### Bug显现

最近，我在单位日常编写Bug的时候，写了一个很奇怪的bug。项目是基于Vue2.x + ElementUI(2.15.3)开发的,在使用`el-image`组件lazy属性时候，发现**初次进入页面的图片居然无法被加载进来，需要滚动一下才能被加载**。如下图显示：

![first-can-not-load.gif](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/122c0efe38de48d9af604e10661a6a56~tplv-k3u1fbpfcp-watermark.awebp)

### Bug追踪

##### el-image使用代码

```html
<template>
  <div class="home">
    <div class="image-box" v-for="num in 6" :key="num">
      <el-image :src="require(`../assets/yileina${num}.jpeg`)" lazy :ref="`image${num}`">
        <div slot="placeholder" class="image-slot">
          <em class="el-icon-loading"></em>加载图片伊蕾娜{{ num }}中...
        </div></el-image
      >
    </div>
  </div>
</template>
复制代码
```

[更详细的代码请看这个git仓库](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2F24min%2Fel-image-lazy-example)

##### 网络问题？

一开始我认为这个只是网络问题，但直到我打开了浏览器的调试工具，发现在这种情况下，刚刷新过后的页面图片根本没有被请求加载。而且当我去掉lazy属性的时候，图片都能被顺利加载进来，这也证明了，这根本不是网络问题。而是项目的代码问题，可项目代码也不复杂，也就是简单使用了el-image组件而已，难道我发现了el-image的组件Bug？想到这里，我不由得兴奋起来，要是解决了这个Bug并且提个PR，那我也算是参与过开源大项目的人了。

![R-C.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/69cb959055894ae5b309ecf041468f2f~tplv-k3u1fbpfcp-watermark.awebp)

##### 分析Bug

根据官方文档的对`scroll-container`属性的描述，我们得知，在我没有显示指定`scroll-container`的时候，懒加载监听scroll事件的容器为**最近一个overflow值为auto或scroll的父元素（overflow ='auto' || 'scroll'）**。所以我查找了一下我的代码，那么距离最近的父元素为.home元素：

```css
.home {
  overflow:auto;
  overflow-x:hidden;
  width: 100vw;
  height: 100vh;
  background: lightcyan;
}
复制代码
```

所以.home元素是就是监听scroll事件的容器。我也查看了元素布局，el-image的组件元素确确实实就在.home的容器中：

![f12image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2b4a2fb78a6241df97c7bc2bb7abcbdb~tplv-k3u1fbpfcp-watermark.awebp) 这个时候我不禁怀疑，**.home元素真的是在el-image组件代码中用来监听scroll的元素容器吗？**

##### el-iamge源码

带着这个疑问，我去翻了el-image的源码，试图解答我的疑问。

![el-image-main-vue.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a5c17ffd03c643fbb02c0388144b5721~tplv-k3u1fbpfcp-watermark.awebp) 在main.vue中，在初始化组件的过程中，会先根据`lazy`的值执行`this.addLazyLoadListener();`的函数。而在这个函数当中会根据用户传入的`scroll-container`的值来处理计算具体的监听容器元素。而我并没有传入任何值，所以执行的代码逻辑是`_scrollContainer = getScrollContainer(this.$el);`。

那得嘞，去瞅一眼位于element-ui/src/utils/dom.js文件中的`getScrollContainer`函数。

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/38e5c7b2516b467eaf7d1ae647f30053~tplv-k3u1fbpfcp-watermark.awebp) getScrollContainer函数做的事情就是不断往上递归直到找到带有overflow为scroll或auto的元素（当找到window等元素也会停止）。 在完成这一步之后，就会监听此元素容器(`_scrollContainer`)的滚动操作（`throttle`节流优化），并且默认会执行一次`handleLazyLoad()`函数，以防止在未滚动时没有加载图片。

组件中的封装已经考虑到了在初次进入页面没有任何操作时候应当执行一次`handleLazyLoad()`函数以防止图片没有被加载。所以并不是我所认为的el-image组件中存在初始化的bug。

**难道在handleLazyLoad函数中的isInContainer的判断有问题？**

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/44199a2919af4b02b8c10d2295017301~tplv-k3u1fbpfcp-watermark.awebp)

### 代码验证

> 分析的再多，不如debug一下

##### debug前的准备工作

为了更加方便的debug,我将核心的计算函数拷贝到我的组件中，如下所示：

```js
const SPECIAL_CHARS_REGEXP = /([\:\-\_]+(.))/g;
const MOZ_HACK_REGEXP = /^moz([A-Z])/;
const camelCase = function (name) {
  return name
    .replace(SPECIAL_CHARS_REGEXP, function (_, separator, letter, offset) {
      return offset ? letter.toUpperCase() : letter;
    })
    .replace(MOZ_HACK_REGEXP, "Moz$1");
};
const getStyle = function (element, styleName) {
  // if (isServer) return;
  if (!element || !styleName) return null;
  styleName = camelCase(styleName);
  if (styleName === "float") {
    styleName = "cssFloat";
  }
  try {
    var computed = document.defaultView.getComputedStyle(element, "");
    return element.style[styleName] || computed ? computed[styleName] : null;
  } catch (e) {
    return element.style[styleName];
  }
};
const isScroll = (el, vertical) => {
  const determinedDirection = vertical !== null && vertical !== undefined;
  const overflow = determinedDirection
    ? vertical
      ? getStyle(el, "overflow-y")
      : getStyle(el, "overflow-x")
    : getStyle(el, "overflow");

  return overflow.match(/(scroll|auto)/);
};
const getScrollContainer = (el, vertical) => {
  // if (isServer) return;
  debugger;
  let parent = el;
  while (parent) {
    if ([window, document, document.documentElement].includes(parent)) {
      return window;
    }
    if (isScroll(parent, vertical)) {
      return parent;
    }
    parent = parent.parentNode;
  }

  return parent;
};
const isInContainer = (el, container) => {
  // if (isServer || !el || !container) return false;
  const elRect = el.getBoundingClientRect();
  let containerRect;

  if (
    [window, document, document.documentElement, null, undefined].includes(
      container
    )
  ) {
    containerRect = {
      top: 0,
      right: window.innerWidth,
      bottom: window.innerHeight,
      left: 0,
    };
  } else {
    containerRect = container.getBoundingClientRect();
  }

  return (
    elRect.top < containerRect.bottom &&
    elRect.bottom > containerRect.top &&
    elRect.right > containerRect.left &&
    elRect.left < containerRect.right
  );
};
复制代码
```

那么我在我的组件中调用这些函数打断点看到底哪一步出了问题。

##### .home元素真的是在el-image组件代码中用来监听scroll的元素容器吗？

为了验证这个怀疑，我在mounted()函数中输出了getScrollContainer的返回计算值：

```js
  mounted() {
    console.log('elImage',getScrollContainer(this.$refs.image1[0].$el))
  },
复制代码
```

*输出结果：*

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a10f7d2b28f24efe811e683418c2087e~tplv-k3u1fbpfcp-watermark.awebp)

.home元素确实是在el-image组件代码中用来监听scroll的元素容器。

##### 难道在handleLazyLoad函数中的isInContainer的判断有问题？

为此我又加入了如下代码：

```js
  mounted() {
    // console.log('this.$refs.image1',this.$refs.image1)
    console.log('elImage',getScrollContainer(this.$refs.image1[0].$el))
    console.log('isInContainer?',isInContainer(this.$refs.image1[0].$el,getScrollContainer(this.$refs.image1[0].$el)))
  },
复制代码
```

*输出结果：*

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/98aa931e73da46e99d3db0bc352b1443~tplv-k3u1fbpfcp-watermark.awebp)

结果为**false**！！！,说明el-image并不在容器内。那么为什么呢？我明明在f12中看到el-image在.home的元素中啊。

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/28e6ad266b704b73a904a28d9088ce46~tplv-k3u1fbpfcp-watermark.awebp)

##### 打断点

于是我在`isInContainer`函数中打了一个断点，得到`elRect`和`containerRect`的一些边界信息如下：

**当前元素（elRect）的边界信息：**

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0e777ef12569407ea17aff8af7021f22~tplv-k3u1fbpfcp-watermark.awebp)

**监听滚动事件容器（containerRect）的边界信息：**

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f5125d81ad2242bd8562d28934bc3442~tplv-k3u1fbpfcp-watermark.awebp)

##### 新的发现

在通过打断点的输出图片中，我们可以看到**el-image和.home元素的宽度都为0**，所以在后续判断中，就出现了函数给出el-image不在.home元素中的结论。我又陷入了沉思，为什么他们的宽度都是0？而且我也均设置了他们的宽高信息。

##### 新的春天 transition name="el-zoom-in-center"

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d174efeab98a445b9a2814e2b24ffc99~tplv-k3u1fbpfcp-watermark.awebp)

Two thousand years later...

后来啊，我发现这么一段代码：

```js
<template>
  <div id="app">
    <transition name="el-zoom-in-center">
      <router-view />
    </transition>
  </div>
</template>

<style lang="scss">
</style>
复制代码
```

对，没有错，就是这边加入的路由动画导致的。el-zoom-in-center是element内置的过渡动画，它的动画效果是从中间向两边打开，而其中使用了`transform: scaleX(0)`，那么scaleX是会改变元素的宽度的，所以，通过路由匹配进来的页面的宽度都是0.

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b003b29b21054e8cb1056fbe91d32b2c~tplv-k3u1fbpfcp-watermark.awebp) **那么我们去掉这个路由动画或者换个不影响判断的路由动画，再次尝试，发现初次进入未进行任何操作也可以加载首页图片。**

### 总结

本文是记录我解决这个Bug中的整个心路历程。希望文章中的解决Bug的思路能对大家有所帮助