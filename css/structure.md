# 模块组织

任何超过 1000 行的 CSS 代码，你都曾经历过这样的体验：
1. 这个 class 到底是什么意思呢？
2. 这个 class 在哪里被使用呢？
3. 如果我创建一个 `xxoo` class，会造成冲突吗？

`Reasonable System for CSS Stylesheet Structure` 的目标就是解决以上问题，它不是一个框架，而是通过规范，让你构建更健壮和可维护的 CSS 代码。

#### Components（组件）
![Components](https://github.com/rstacruz/rscss/raw/master/images/component-example.png)

从 `Components` 的角度思考，将网站的模块都作为一个独立的 `Components`。

##### Naming components （组件命名）
`Components` **最少以两个单词命名**，通过 `-` 分离，例如：
- 点赞按钮 (`.like-button`)
- 搜索框 (`.search-form`)
- 文章卡片 (`.article-card`)

#### Elements （元素）
![Elements](https://github.com/rstacruz/rscss/raw/master/images/component-elements.png)

`Elements` 是 `Components` 中的元素

##### Naming elements （元素命名）
`Elements` 的类名应尽可能仅有一个单词。
```css
 .search-form {
    > .field { /* ... */ }
    > .action { /* ... */ }
  }
```

##### On multiple words （多个单词）
对于倘若需要两个或以上单词表达的 `Elements` 类名，不应使用中划线和下划线连接，应**直接连接**。
```css
  .profile-box {
    > .firstname { /* ... */ }
    > .lastname { /* ... */ }
    > .avatar { /* ... */ }
  }
```

##### Avoid tag selectors （避免标签选择器）
任何时候尽可能使用 `classnames`。标签选择器在使用上没有问题，但是其性能上稍弱，并且表意不明确。
```css
  .article-card {
    > h3    { /* ✗ avoid */ }
    > .name { /* ✓ better */ }
  }
```

#### Variants （变体）
![Variants](https://github.com/rstacruz/rscss/raw/master/images/component-modifiers.png)

`Components` 和 `Elements` 可能都会拥有 `Variants`。

##### Naming variants （变体命名）
`Variants` 的 `classname` 应带有前缀中划线 `-`
```css
  .like-button {
    &.-wide { /* ... */ }
    &.-short { /* ... */ }
    &.-disabled { /* ... */ }
  }
```

##### Element variants （元素变体）
```css
  .shopping-card {
    > .title { /* ... */ }
    > .title.-small { /* ... */ }
  }
```

##### Dash prefixes （中划线前缀）
为什么使用中划线作为变体的前缀？
- 它可以避免歧义与 `Elements`
- CSS class 仅能以单词和 `_` 或 `-` 开头
- 中划线比下划线更容易输出

#### Layout （布局）
![Layout](https://github.com/rstacruz/rscss/raw/master/images/layouts.png)

##### Avoid positioning properties （避免定位属性）
Components 应该在不同的上下文中都可以复用，所以应避免设置以下属性：
- Positioning (position, top, left, right, bottom)
- Floats (float, clear)
- Margins (margin)
- Dimensions (width, height) *

##### Fixed dimensions （固定尺寸）
头像和 logos 这些元素应该设置固定尺寸（宽度，高度...）。

##### Define positioning in parents （在父元素中设置定位）
倘若你需要为组件设置定位，应将在组件的上下文（父元素）中进行处理，比如以下例子中，将 `widths` 和 `floats` 应用在 `list component(.article-list)` 当中，而不是 `component(.article-card)` 自身。
```css
  .article-list {
    & {
      @include clearfix;
    }

    > .article-card {
      width: 33.3%;
      float: left;
    }
  }

  .article-card {
    & { /* ... */ }
    > .image { /* ... */ }
    > .title { /* ... */ }
    > .category { /* ... */ }
  }
```

#### Avoid over-nesting （避免过分嵌套）
当出现多个嵌套的时候容易失去控制，应保持不超过一个嵌套。
```css
  /* ✗ Avoid: 3 levels of nesting */
  .image-frame {
    > .description {
      /* ... */

      > .icon {
        /* ... */
      }
    }
  }

  /* ✓ Better: 2 levels */
  .image-frame {
    > .description { /* ... */ }
    > .description > .icon { /* ... */ }
  }
```

#### Apprehensions （顾虑）
- **中划线`-`是一坨糟糕的玩意**：其实你可以选择性的使用，只要将 `Components, Elements, Variants` 记在心上即可。
- **我有时候想不出两个单词唉**：有些组件的确使用一个单词就能表意，比如 `aleter` 。但其实你可以使用后缀，使其意识更加明确。

比如块级元素：
 * .alert-box
 * .alert-card
 * .alert-block

或行内级元素
 * .link-button
 * .link-span

#### Terminologies （术语）
RSCSS 与其他 CSS 模块组织系统相似的概念

| RSCSS | BEM | SMACSS |
| -- | -- | -- |
| Component	| Block | Module |
| Element	| Element |	? |
| Layout	| ?	| Layout |
| Variant	| Modifier | Theme & State |

#### Summary （总结）
- 以 `Components` 的角度思考，以两个单词命名（`.screenshot-image`）
- `Components` 中的 `Elements`，以一个单词命名（`.blog-post .title`）
- `Variants`，以中划线`-`作为前缀（`.shop-banner.-with-icon`）
- `Components` 可以互相嵌套
- 记住，你可以通过继承让事情变得更简单

---
[译自:Reasonable System for CSS Stylesheet Structure](https://github.com/rstacruz/rscss#readme)
