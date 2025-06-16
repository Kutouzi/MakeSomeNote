## web笔记-->高级css



这些 CSS 属性都涉及到元素在容器中的对齐方式，但它们的应用场景和效果有所不同：

1. **`justify-content`**：用于主轴方向（通常是水平）对齐子元素。它控制元素在容器中如何分布，常见的值有：
   1. `flex-start`（起始位置）
   2. `center`（居中）
   3. `flex-end`（结束位置）
   4. `space-between`（元素之间平均分配空间）
   5. `space-around`（元素周围有相等的空白空间）
2. **`align-items`**：用于交叉轴方向（通常是垂直）对齐子元素。它控制元素在容器的交叉轴（垂直方向）上的位置。常见的值有：
   1. `flex-start`（起始位置）
   2. `center`（居中）
   3. `flex-end`（结束位置）
   4. `stretch`（拉伸到容器高度）
3. **`align-content`**：用于多行的容器，控制多行之间的对齐方式。适用于 `flex-wrap` 生效时（即容器内有多行元素）。常见的值有：
   1. `flex-start`
   2. `center`
   3. `flex-end`
   4. `space-between`
   5. `space-around`
   6. `stretch`
4. **`align-self`**：允许单个元素覆盖 `align-items` 的对齐方式，只影响当前元素，而不会影响其他元素。常见的值有：
   1. `auto`（继承父容器的 `align-items` 值）
   2. `flex-start`
   3. `center`
   4. `flex-end`
   5. `stretch`
5. **`text-align`**：用于文本在元素内部的对齐方式，通常用于行内元素或文本内容的对齐。常见的值有：
   1. `left`（左对齐）
   2. `right`（右对齐）
   3. `center`（居中对齐）
   4. `justify`（两端对齐）

总结：

- `justify-content` 控制主轴对齐，通常用于水平布局。
- `align-items` 和 `align-content` 控制交叉轴的对齐，分别适用于单行和多行布局。
- `align-self` 允许单个元素自定义交叉轴对齐。
- `text-align` 控制文本的对齐方式。

`display` 和 `position` 是 CSS 中非常重要的布局属性，它们在定义元素的显示方式和定位方式上起到了不同的作用。下面我会详细解释它们的区别和使用方法：

### 1. **`display`** **属性**

`display` 属性用于定义元素的显示类型，决定了元素在页面中是如何表现的，主要决定了元素是块级元素、行内元素还是其他类型的布局模式。

#### 常见的 `display` 值：

- **`block`**：使元素成为块级元素，占据一整行，宽度默认填满父容器，可以设置 `width` 和 `height`。常见的块级元素有 `<div>`, `<p>`, `<h1>` 等。
- **`inline`**：使元素成为行内元素，不占据一整行，只占据它内容的宽度，无法设置 `width` 或 `height`。常见的行内元素有 `<span>`, `<a>`, `<strong>` 等。
- **`inline-block`**：结合了 `block` 和 `inline` 的特性，元素既可以设置 `width` 和 `height`，又不会换行。它的行为像行内元素，但可以设置大小。
- **`flex`**：让元素成为一个 flex 容器，它的子元素（flex 项）会自动根据 flexbox 布局规则排列。常用来创建响应式布局。
- **`grid`**：让元素成为一个网格容器，它的子元素（grid 项）会根据网格布局规则排列。适用于二维布局。
- **`none`**：让元素完全从页面中消失（不占据空间），其子元素也不会被渲染。

#### 使用方法：

```CSS
/* 让元素成为块级元素 */
.container {
  display: block;
}
/* 让元素成为行内元素 */
.element {
  display: inline;
}
/* 让元素成为弹性容器 */
.flex-container {
  display: flex;
}
/* 让元素成为网格容器 */
.grid-container {
  display: grid;
}
/* 隐藏元素 */
.hidden {
  display: none;
}
```

### 2. **`position`** **属性**

`position` 属性用于定义元素的定位方式，决定了元素的位置是相对于父元素、视口还是其他元素来定位的。

#### 常见的 `position` 值：

- **`static`**：这是默认值，元素按照文档流进行布局，不会受到 `top`、`right`、`bottom`、`left` 等属性的影响。元素位置根据其在 HTML 中的顺序和父容器决定。
- **`relative`**：元素相对于它自己原始的位置进行定位。它会占据原来的位置，但可以通过 `top`、`right`、`bottom`、`left` 来进行微调。
- **`absolute`**：元素相对于最近的定位祖先元素（即 `position` 不为 `static` 的元素）进行定位。如果没有定位的祖先元素，则相对于 `body` 元素定位。它脱离文档流，不会影响其他元素的布局。
- **`fixed`**：元素相对于浏览器视口定位，滚动页面时元素会固定在视口的位置。它脱离文档流，不会影响其他元素。
- **`sticky`**：元素在特定的滚动位置开始“粘住”，即在滚动到达指定位置时会固定在页面上。它有一个 `top`、`bottom` 等属性来确定粘住的位置。

#### 使用方法：

```CSS
/* 默认定位 (按文档流布局) */
.element {
  position: static;
}
/* 相对定位 (相对于元素原始位置调整) */
.relative-element {
  position: relative;
  top: 10px;  /* 向下偏移 10px 
/
*  left: 20px; /*
 向右偏移 20px */
}
/* 绝对定位 (相对于最近的定位父元素) */
.absolute-element {
  position: absolute;
  top: 50px;  /* 距离定位祖先元素 50px 
/
*  left: 100px; /*
 距离定位祖先元素 100px */
}
/* 固定定位 (相对于视口定位) */
.fixed-element {
  position: fixed;
  top: 10px; /* 距离视口顶部 10px 
/
*  right: 20px; /*
 距离视口右侧 20px */
}
/* 粘性定位 (滚动时保持粘住) */
.sticky-element {
  position: sticky;
  top: 0;  /* 滚动时，元素会固定在视口顶部 */
}
```

### 3. **`display`** **和** **`position`** **的区别**

- **功能不同**：
  - `display` 主要控制元素的显示方式（是否为块级元素、行内元素或弹性布局容器等），以及它如何在页面中占据空间。
  - `position` 主要控制元素的定位方式，决定了元素在父容器或视口中的具体位置。
- **影响的范围**：
  - `display` 会影响元素的布局行为及其占据的空间。例如，`display: flex` 会使元素成为一个弹性容器，`display: none` 会使元素消失。
  - `position` 更关注元素的位置调整，它不会影响元素是否占据空间。元素的 `position` 为 `absolute` 或 `fixed` 时，它会脱离文档流，不会影响其他元素的布局。
- **如何结合使用**：
  - 如果你使用了 `display: flex` 或 `display: grid`，你通常会结合使用 `position: relative` 来控制子元素的精确位置，或者使用 `position: absolute` 来将子元素定位到容器内的特定位置。
  - 使用 `position: relative` 时，元素的原始位置依然会占据空间，配合 `top`, `left` 等属性可以微调位置。

### 总结：

- **`display`** 控制元素的显示类型（块级、行内、弹性、网格等）。
- **`position`** 控制元素的定位方式（静态、相对、绝对、固定或粘性定位）。
- 它们可以一起使用，`display` 决定了元素的布局行为，`position` 决定了它的位置。