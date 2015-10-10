# LESS

#### 代码组织
代码按一下顺序组织：
1. @import
2. 变量声明
3. 样式声明

```css
@import "mixins/size.less";

@default-text-color: #333;

.page {
  width: 960px;
  margin: 0 auto;
}
```

#### @import 语句
@import 语句引用的文需要写在一对引号内，.less 后缀不得省略。引号使用 `'` 和 `"` 均可，但在同一项目内需统一。
```css
/* Not recommended */
@import "mixins/size";
@import 'mixins/grid.less';

/* Recommended */
@import "mixins/size.less";
@import "mixins/grid.less";
```

#### 混入（Mixin）
1. 在定义 `mixin` 时，如果 `mixin` 名称不是一个需要使用的 className，必须加上括号，否则即使不被调用也会输出到 CSS 中。

2. 如果混入的是本身不输出内容的 mixin，需要在 mixin 后添加括号（即使不传参数），以区分这是否是一个 className。

```css
/* Not recommended */
.big-text {
  font-size: 2em;
}

h3 {
  .big-text;
  .clearfix;
}

/* Recommended */
.big-text() {
  font-size: 2em;
}

h3 {
  .big-text(); /* 1 */
  .clearfix(); /* 2 */
}
```

#### 避免嵌套层级过多
- 将嵌套深度限制在2级。对于超过3级的嵌套，给予重新评估。这可以避免出现过于详实的CSS选择器。
- 避免大量的嵌套规则。当可读性受到影响时，将之打断。推荐避免出现多于20行的嵌套规则出现。

#### 字符串插值
变量可以用类似ruby和php的方式嵌入到字符串中，像@{name}这样的结构:
`@base-url: "http://assets.fnord.com";`
`background-image: url("@{base-url}/images/bg.png");`


