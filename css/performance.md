# 性能优化

####慎重选择高消耗的样式

高消耗属性在绘制前需要浏览器进行大量计算：
* box-shadows
* border-radius
* transparency
* transforms
* CSS filters（性能杀手）

####避免过分重排
当发生重排的时候，浏览器需要重新计算布局位置与大小，[更多详情](http://www.jianshu.com/p/e305ace24ddf)。

常见的重排元素:
* width
* height
* padding
* margin
* display
* border-width
* position
* top
* left
* right
* bottom
* font-size
* float
* text-align
* overflow-y
* font-weight
* overflow
* font-family
* line-height
* vertical-align
* clear
* white-space
* min-height

####正确使用 Display 的属性
Display 属性会影响页面的渲染，请合理使用。

* display: inline后不应该再使用 width、height、margin、padding 以及 float；

* display: inline-block 后不应该再使用 float；

* display: block 后不应该再使用 vertical-align；

* display: table-* 后不应该再使用 margin 或者 float；

####不滥用 Float

Float在渲染时计算量比较大，尽量减少使用。

####动画性能优化
动画的实现原理，是利用了人眼的“视觉暂留”现象，在短时间内连续播放数幅静止的画面，使肉眼因视觉残象产生错觉，而误以为画面在“动”。

动画的基本概念：

* 帧：在动画过程中，每一幅静止画面即为一“帧”;
* 帧率：即每秒钟播放的静止画面的数量，单位是fps(Frame per second);
* 帧时长：即每一幅静止画面的停留时间，单位一般是ms(毫秒);
* 跳帧(掉帧/丢帧)：在帧率固定的动画中，某一帧的时长远高于平均帧时长，导致其后续数帧被挤压而丢失的现象。

一般浏览器的渲染刷新频率是 60 fps，所以在网页当中，帧率如果达到 50-60 fps 的动画将会相当流畅，让人感到舒适。

* 如果使用基于 javaScript 的动画，尽量使用 requestAnimationFrame. 避免使用 setTimeout, setInterval.
* 避免通过类似 jQuery animate()-style 改变每帧的样式，使用 CSS 声明动画会得到更好的浏览器优化。
* 使用 translate 取代 absolute 定位就会得到更好的 fps，动画会更顺滑。

![High Performance Animations](http://www.html5rocks.com/en/tutorials/speed/high-performance-animations/cheap-operations.jpg)

####多利用硬件能力，如通过 3D 变形开启 GPU 加速
一般在 Chrome 中，3D或透视变换（perspective transform）CSS属性和对 opacity 进行 CSS 动画会创建新的图层，在硬件加速渲染通道的优化下，GPU 完成 3D 变形等操作后，将图层进行复合操作（Compesite Layers），从而避免触发浏览器大面积重绘和重排。

注：3D 变形会消耗更多的内存和功耗。

使用 translate3d 右移 500px 的动画流畅度要明显优于直接使用 left：
```css
.ball-1 {
  transition: -webkit-transform .5s ease;
  -webkit-transform: translate3d(0, 0, 0);
}
.ball-1.slidein{
  -webkit-transform: translate3d(500px, 0, 0);
}
.ball-2 {
  transition: left .5s ease; left：0;
}
.ball-2.slidein {
  left：500px;
}
```

####提升 CSS 选择器性能
CSS 选择器对性能的影响源于浏览器匹配选择器和文档元素时所消耗的时间，所以优化选择器的原则是应尽量避免使用消耗更多匹配时间的选择器。而在这之前我们需要了解 CSS 选择器匹配的机制， 如子选择器规则：

```css
#header > a {font-weight:blod;}
```

我们中的大多数人都是从左到右的阅读习惯，会习惯性的设定浏览器也是从左到右的方式进行匹配规则，推测这条规则的开销并不高。

我们会假设浏览器以这样的方式工作：寻找 id 为 header 的元素，然后将样式规则应用到直系子元素中的 a 元素上。我们知道文档中只有一个 id 为 header 的元素，并且它只有几个 a 元素的子节点，所以这个 CSS 选择器应该相当高效。

事实上，却恰恰相反，CSS 选择器是从右到左进行规则匹配。了解这个机制后，例子中看似高效的选择器在实际中的匹配开销是很高的，浏览器必须遍历页面中所有的 a 元素并且确定其父元素的 id 是否为 header 。

如果把例子的子选择器改为后代选择器则会开销更多，在遍历页面中所有 a 元素后还需向其上级遍历直到根节点。

```css
#header  a {font-weight:blod;}
```

理解了CSS选择器从右到左匹配的机制后，明白只要当前选择符的左边还有其他选择符，样式系统就会继续向左移动，直到找到和规则匹配的选择符，或者因为不匹配而退出。我们把最右边选择符称之为**关键选择器**。——[更多详情](http://www.jianshu.com/p/268c7f3dd7a6)

1、避免使用通用选择器
```css
/* Not recommended */
.content * {color: red;}
```

浏览器匹配文档中所有的元素后分别向上逐级匹配 class 为 content 的元素，直到文档的根节点。因此其匹配开销是非常大的，所以应避免使用关键选择器是通配选择器的情况。

2、避免使用标签或 class 选择器限制 id 选择器
```css
/* Not recommended */
button#backButton {…}
/* Recommended */
#newMenuIcon {…}
```

3、避免使用标签限制 class 选择器
```css
/* Not recommended */
treecell.indented {…}
/* Recommended */
.treecell-indented {…}
/* Much to recommended */
.hierarchy-deep {…}
```

4、避免使用多层标签选择器。使用 class 选择器替换，减少css查找
```css
/* Not recommended */
treeitem[mailfolder="true"] > treerow > treecell {…}
/* Recommended */
.treecell-mailfolder {…}
```

5、避免使用子选择器
```css
/* Not recommended */
treehead treerow treecell {…}
/* Recommended */
treehead > treerow > treecell {…}
/* Much to recommended */
.treecell-header {…}
```

6、使用继承
```css
/* Not recommended */
#bookmarkMenuItem > .menu-left { list-style-image: url(blah) }
/* Recommended */
#bookmarkMenuItem { list-style-image: url(blah) }
```
