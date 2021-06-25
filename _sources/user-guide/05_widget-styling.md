---
jupytext:
  formats: ipynb,md:myst
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.10.3
kernelspec:
  display_name: ai
  language: python
  name: ai
---

(widget-styling)=
# 布局和样式

参考：[Layout and Styling of Jupyter widgets](https://ipywidgets.readthedocs.io/en/stable/examples/Widget%20Styling.html#The-Flexbox-layout)

本笔记本介绍了如何布局和样式的 Jupyter 交互式小部件，以构建丰富的和反应性的基于小部件的应用程序。

你可以直接跳转到以下部分：

+ [The `layout` attribute](#The-layout-attribute)
+ [The Flexbox layout](#The-Flexbox-layout)
+ [Predefined styles](#Predefined-styles)
+ [The `style` attribute](#The-style-attribute)
+ [The Grid layout](#The-Grid-layout)
+ [Image layout and sizing](#Image-layout-and-sizing)

## `layout` 属性

Jupyter 交互式小部件有一个 `layout` 属性，它暴露了许多影响小部件布局方式的 CSS 属性。

### 暴露的 CSS 属性

```{div} class="alert alert-info" style="margin: 20px
以下属性映射到同名的 CSS 属性值（下划线被破折号替换），这些属性应用于相应小部件的顶级 DOM 元素。
```

#### Sizes

- `height`
- `width`
- `max_height`
- `max_width`
- `min_height`
- `min_width`

#### Display

- `visibility`
- `display`
- `overflow`

#### Box model

- `border` 
- `margin`
- `padding`

#### Positioning

- `top`
- `left`
- `bottom`
- `right`

#### Flexbox

- `order`
- `flex_flow`
- `align_items`
- `flex`
- `align_self`
- `align_content`
- `justify_content`

#### Grid layout

- `grid_auto_columns`
- `grid_auto_flow`
- `grid_auto_rows`
- `grid_gap`
- `grid_template`
- `grid_row`
- `grid_column`

### 速记 CSS 属性

可能已经注意到某些 CSS 属性，如 `margin-[top/right/bottom/left]` 似乎缺失了。同样适用于 `padding-[top/right/bottom/left]` 等。

事实上，你可以自动指定 `[top/right/bottom/left]` 的 `margin` 属性，通过传递字符串 `'100px 150px 100px 80px'`，分别为 `top`, `right`, `bottom` 和 `left` margin 为 `100`, `150`, `100` 和 `80` 像素。

类似地，`flex` 属性可以保存 `flex-grow`、`flex-shrink` 和 `flex-basis` 的值。`border` 属性是 `border-width`、`border-style`（必需的）和 `border-color` 的简写属性。

### 简单例子

下面的例子展示了如何调整一个 `Button` 的大小，使其视图的高度为 `80px`，宽度为可用空间的 `50%`:


```python
from ipywidgets import Button, Layout

b = Button(description='(50% width, 80px height) button',
           layout=Layout(width='50%', height='80px'))
b
```


    Button(description='(50% width, 80px height) button', layout=Layout(height='80px', width='50%'), style=ButtonS…


`layout` 属性可以在多个小部件之间共享并直接分配。


```python
Button(description='Another button with the same layout', layout=b.layout)
```


    Button(description='Another button with the same layout', layout=Layout(height='80px', width='50%'), style=But…


### `description`

您可能已经注意到，较长的描述会被截断。这是因为描述长度在默认情况下是固定的。


```python
from ipywidgets import IntSlider

IntSlider(description='A too long description')
```


    IntSlider(value=0, description='A too long description')


您可以更改描述的长度以适应描述文本。但是，这将使小部件本身更短。您可以通过使用小部件的样式调整描述宽度和小部件宽度来改变这两者。


```python
style = {'description_width': 'initial'}
IntSlider(description='A too long description', style=style)
```


    IntSlider(value=0, description='A too long description', style=SliderStyle(description_width='initial'))


如果您需要更灵活的布局小部件和描述，您可以直接使用 `Label` 小部件。


```python
from ipywidgets import HBox, Label

HBox([Label('A too long description'), IntSlider()])
```


    HBox(children=(Label(value='A too long description'), IntSlider(value=0)))


### 自然大小，并使用 `HBox` 和 `VBox` 安排

大多数核心小部件的默认高度和宽度都平铺在一起。这允许基于 `HBox` 和 `VBox` 助手函数的简单布局自然对齐：


```python
from ipywidgets import Button, HBox, VBox

words = ['correct', 'horse', 'battery', 'staple']
items = [Button(description=w) for w in words]
left_box = VBox([items[0], items[1]])
right_box = VBox([items[2], items[3]])
HBox([left_box, right_box])
```


    HBox(children=(VBox(children=(Button(description='correct', style=ButtonStyle()), Button(description='horse', …


### Latex

小部件（如滑块和文本输入）有一个描述属性，可以呈现 Latex 方程。`Label` 小部件还显示 Latex 方程。


```python
from ipywidgets import IntSlider, Label

IntSlider(description=r'\(\int_0^t f\)')
```


    IntSlider(value=0, description='\\(\\int_0^t f\\)')



```python
Label(value=r'\(e=mc^2\)')
```


    Label(value='\\(e=mc^2\\)')


### 数字格式化属性

滑块有一个 `readout` 字段，可以使用 Python 的 [格式规范迷你语言进行格式化](https://docs.python.org/3/library/string.html#format-specification-mini-language)。如果可用的读取空间对于滑块值的字符串表示来说太窄，则应用不同的样式来显示并非所有数字都可见。

## Flexbox 布局

上面的 `HBox` 和 `VBox` 类是 `Box` 小部件的特殊情况。

`Box` 小部件支持整个 CSS flexbox 规范和 Grid 布局规范，在 Jupyter 笔记本中支持丰富的响应式布局。它的目的是提供一种有效的方式来布局，对齐和分配空间之间的项目在一个容器。

### 致谢

以下关于 flexbox 布局的 flexbox 教程遵循 Chris Coyier 的文章 [A Complete Guide to flexbox](https://css-tricks.com/snippets/css/a-guide-to-flexbox/) 的行，并使用文本和各种图片[从文章的许可](https://css-tricks.com/license/)。

### 基础和术语

因为 flexbox 是一个完整的模块，而不是一个单独的属性，所以它涉及到很多东西，包括它的整个属性集。其中一些是在容器上设置(父元素，称为“flex container”)，而其他的是在子元素上设置(称为“flex items”)。

If regular layout is based on both block and inline flow directions, the flex layout is based on "flex-flow directions". Please have a look at this figure from the specification, explaining the main idea behind the flex layout.

![Flexbox](https://media.githubusercontent.com/media/xinetzone/ipywidgets-media/main/images/flexbox.png)

Basically, items will be laid out following either the `main axis` (from `main-start` to `main-end`) or the `cross axis` (from `cross-start` to `cross-end`).

- `main axis` - The main axis of a flex container is the primary axis along which flex items are laid out. Beware, it is not necessarily horizontal; it depends on the flex-direction property (see below).
- `main-start | main-end` - The flex items are placed within the container starting from main-start and going to main-end.
- `main size` - A flex item's width or height, whichever is in the main dimension, is the item's main size. The flex item's main size property is either the ‘width’ or ‘height’ property, whichever is in the main dimension.
cross axis - The axis perpendicular to the main axis is called the cross axis. Its direction depends on the main axis direction.
- `cross-start | cross-end` - Flex lines are filled with items and placed into the container starting on the cross-start side of the flex container and going toward the cross-end side.
- `cross size` - The width or height of a flex item, whichever is in the cross dimension, is the item's cross size. The cross size property is whichever of ‘width’ or ‘height’ that is in the cross dimension.

### Properties of the parent

![Container](https://media.githubusercontent.com/media/xinetzone/ipywidgets-media/main/images/flex-container.svg)


#### display

`display` can be `flex` or `inline-flex`. This defines a flex container (block or inline).

#### flex-flow

`flex-flow` is a shorthand for the `flex-direction` and `flex-wrap` properties, which together define the flex container's main and cross axes. Default is `row nowrap`.

- `flex-direction` (column-reverse | column | row | row-reverse )

  This establishes the main-axis, thus defining the direction flex items are placed in the flex container. Flexbox is (aside from optional wrapping) a single-direction layout concept. Think of flex items as primarily laying out either in horizontal rows or vertical columns.
![Direction](https://media.githubusercontent.com/media/xinetzone/ipywidgets-media/main/images/flex-direction1.svg)

- `flex-wrap` (nowrap | wrap | wrap-reverse)

  By default, flex items will all try to fit onto one line. You can change that and allow the items to wrap as needed with this property. Direction also plays a role here, determining the direction new lines are stacked in.
![Wrap](https://media.githubusercontent.com/media/xinetzone/ipywidgets-media/main/images/flex-wrap.svg)

#### justify-content

`justify-content` can be one of `flex-start`, `flex-end`, `center`, `space-between`, `space-around`. This defines the alignment along the main axis. It helps distribute extra free space left over when either all the flex items on a line are inflexible, or are flexible but have reached their maximum size. It also exerts some control over the alignment of items when they overflow the line.
  ![Justify](https://media.githubusercontent.com/media/xinetzone/ipywidgets-media/main/images/justify-content.svg)

#### align-items

`align-items` can be one of `flex-start`, `flex-end`, `center`, `baseline`, `stretch`. This defines the default behaviour for how flex items are laid out along the cross axis on the current line. Think of it as the justify-content version for the cross-axis (perpendicular to the main-axis).
   ![Items](https://media.githubusercontent.com/media/xinetzone/ipywidgets-media/main/images/align-items.svg)
   
#### align-content
`align-content` can be one of `flex-start`, `flex-end`, `center`, `baseline`, `stretch`. This aligns a flex container's lines within when there is extra space in the cross-axis, similar to how justify-content aligns individual items within the main-axis.
![Items](https://media.githubusercontent.com/media/xinetzone/ipywidgets-media/main/images/align-content.svg)

**Note**: this property has no effect when there is only one line of flex items.

### Properties of the items

![Item](https://media.githubusercontent.com/media/xinetzone/ipywidgets-media/main/images/flex-items.svg)

The flexbox-related CSS properties of the items have no impact if the parent element is not a flexbox container (i.e. has a `display` attribute equal to `flex` or `inline-flex`).

#### order
By default, flex items are laid out in the source order. However, the `order` property controls the order in which they appear in the flex container.
<img src="https://media.githubusercontent.com/media/xinetzone/ipywidgets-media/main/images/order-2.svg" alt="Order" style="width: 500px;"/>
   
#### flex
`flex` is shorthand for three properties, `flex-grow`, `flex-shrink` and `flex-basis` combined. The second and third parameters (`flex-shrink` and `flex-basis`) are optional. Default is `0 1 auto`.
   
  - `flex-grow`

    This defines the ability for a flex item to grow if necessary. It accepts a unitless value that serves as a proportion. It dictates what amount of the available space inside the flex container the item should take up.

    If all items have flex-grow set to 1, the remaining space in the container will be distributed equally to all children. If one of the children a value of 2, the remaining space would take up twice as much space as the others (or it will try to, at least).
      ![Grow](https://media.githubusercontent.com/media/xinetzone/ipywidgets-media/main/images/flex-grow.svg)

  - `flex-shrink`

    This defines the ability for a flex item to shrink if necessary.

  - `flex-basis`

    This defines the default size of an element before the remaining space is distributed. It can be a length (e.g. `20%`, `5rem`, etc.) or a keyword. The `auto` keyword means *"look at my width or height property"*.
 
#### align-self

`align-self` allows the default alignment (or the one specified by align-items) to be overridden for individual flex items.

![Align](https://media.githubusercontent.com/media/xinetzone/ipywidgets-media/main/images/align-self.svg)

### The VBox and HBox helpers

The `VBox` and `HBox` helper classes provide simple defaults to arrange child widgets in vertical and horizontal boxes. They are roughly equivalent to:

```Python
def VBox(*pargs, **kwargs):
    """Displays multiple widgets vertically using the flexible box model."""
    box = Box(*pargs, **kwargs)
    box.layout.display = 'flex'
    box.layout.flex_flow = 'column'
    box.layout.align_items = 'stretch'
    return box

def HBox(*pargs, **kwargs):
    """Displays multiple widgets horizontally using the flexible box model."""
    box = Box(*pargs, **kwargs)
    box.layout.display = 'flex'
    box.layout.align_items = 'stretch'
    return box
```


### 例子


```python
from ipywidgets import Layout, Button, Box

items_layout = Layout( width='auto')     # override the default width of the button to 'auto' to let the button grow

box_layout = Layout(display='flex',
                    flex_flow='column',
                    align_items='stretch',
                    border='solid',
                    width='50%')

words = ['correct', 'horse', 'battery', 'staple']
items = [Button(description=word, layout=items_layout, button_style='danger') for word in words]
box = Box(children=items, layout=box_layout)
box
```


    Box(children=(Button(button_style='danger', description='correct', layout=Layout(width='auto'), style=ButtonSt…


`HBox` 中有三个按钮。flex 项可按其权重比例伸缩。


```python
from ipywidgets import Layout, Button, Box, VBox

# Items flex proportionally to the weight and the left over space around the text
items_auto = [
    Button(description='weight=1; auto', layout=Layout(flex='1 1 auto', width='auto'), button_style='danger'),
    Button(description='weight=3; auto', layout=Layout(flex='3 1 auto', width='auto'), button_style='danger'),
    Button(description='weight=1; auto', layout=Layout(flex='1 1 auto', width='auto'), button_style='danger'),
 ]

# Items flex proportionally to the weight
items_0 = [
    Button(description='weight=1; 0%', layout=Layout(flex='1 1 0%', width='auto'), button_style='danger'),
    Button(description='weight=3; 0%', layout=Layout(flex='3 1 0%', width='auto'), button_style='danger'),
    Button(description='weight=1; 0%', layout=Layout(flex='1 1 0%', width='auto'), button_style='danger'),
 ]
box_layout = Layout(display='flex',
                    flex_flow='row',
                    align_items='stretch',
                    width='70%')
box_auto = Box(children=items_auto, layout=box_layout)
box_0 = Box(children=items_0, layout=box_layout)
VBox([box_auto, box_0])
```


    VBox(children=(Box(children=(Button(button_style='danger', description='weight=1; auto', layout=Layout(flex='1…


#### 一个更高级的例子：响应式表单

表单是一个宽度为 50% 的 `VBox`。`VBox` 中的每一行都是一个 `HBox`，它用空格分隔内容。


```python
from ipywidgets import Layout, Button, Box, FloatText, Textarea, Dropdown, Label, IntSlider

form_item_layout = Layout(
    display='flex',
    flex_flow='row',
    justify_content='space-between'
)

form_items = [
    Box([Label(value='Age of the captain'), IntSlider(min=40, max=60)], layout=form_item_layout),
    Box([Label(value='Egg style'),
         Dropdown(options=['Scrambled', 'Sunny side up', 'Over easy'])], layout=form_item_layout),
    Box([Label(value='Ship size'),
         FloatText()], layout=form_item_layout),
    Box([Label(value='Information'),
         Textarea()], layout=form_item_layout)
]

form = Box(form_items, layout=Layout(
    display='flex',
    flex_flow='column',
    border='solid 2px',
    align_items='stretch',
    width='50%'
))
form
```


    Box(children=(Box(children=(Label(value='Age of the captain'), IntSlider(value=40, max=60, min=40)), layout=La…


#### 一个更高级的例子：旋转木马。


```python
from ipywidgets import Layout, Button, VBox, Label

item_layout = Layout(height='100px', min_width='40px')
items = [Button(layout=item_layout, description=str(i), button_style='warning') for i in range(40)]
box_layout = Layout(overflow='scroll hidden',
                    border='3px solid black',
                    width='500px',
                    height='',
                    flex_flow='row',
                    display='flex')
carousel = Box(children=items, layout=box_layout)
VBox([Label('Scroll horizontally:'), carousel])
```


    VBox(children=(Label(value='Scroll horizontally:'), Box(children=(Button(button_style='warning', description='…


### 预定义的风格

如果你希望小部件的样式利用环境定义的颜色和样式（例如与笔记本主题一致），许多小部件可以在预定义的样式列表中进行选择。

例如，`Button` 小部件有一个 `button_style` 属性，它可以取 5 个不同的值：

 - `'primary'`
 - `'success'`
 - `'info'`
 - `'warning'`
 - `'danger'`
 - `''`（默认）


```python
from ipywidgets import Button

Button(description='Danger Button', button_style='danger')
```


    Button(button_style='danger', description='Danger Button', style=ButtonStyle())


### `style` 属性

虽然 `layout` 属性仅为小部件的顶级 DOM 元素公开与布局相关的 CSS 属性，但 `style` 属性用于公开与布局无关的小部件样式属性。

但是，`style` 属性的属性是特定于每个小部件类型的。


```python
b1 = Button(description='Custom color')
b1.style.button_color = 'lightgreen'
b1
```


    Button(description='Custom color', style=ButtonStyle(button_color='lightgreen'))


您可以获得具有 `keys` 属性的小部件的样式属性列表。


```python
b1.style.keys
```




    ['_model_module',
     '_model_module_version',
     '_model_name',
     '_view_count',
     '_view_module',
     '_view_module_version',
     '_view_name',
     'button_color',
     'font_weight']



就像 `layout` 属性一样，小部件样式可以分配给其他小部件。


```python
b2 = Button()
b2.style = b1.style
b2
```


    Button(style=ButtonStyle(button_color='lightgreen'))


小部件样式属性特定于每种小部件类型。


```python
s1 = IntSlider(description='Blue handle')
s1.style.handle_color = 'lightblue'
s1
```


    IntSlider(value=0, description='Blue handle', style=SliderStyle(handle_color='lightblue'))


## 网格布局

The `GridBox` class is a special case of the `Box` widget.

The `Box` widget enables the entire CSS flexbox spec, enabling rich reactive layouts in the Jupyter notebook. It aims at providing an efficient way to lay out, align and distribute space among items in a container.

Again, the whole grid layout spec is exposed via the `layout` attribute of the container widget (`Box`) and the contained items. One may share the same `layout` attribute among all the contained items.

The following flexbox tutorial on the flexbox layout follows the lines of the article [A Complete Guide to Grid](https://css-tricks.com/snippets/css/complete-guide-grid/) by Chris House, and uses text and various images from the article [with permission](https://css-tricks.com/license/).

### Basics and browser support

To get started you have to define a container element as a grid with display: grid, set the column and row sizes with grid-template-rows, grid-template-columns, and grid_template_areas, and then place its child elements into the grid with grid-column and grid-row. Similarly to flexbox, the source order of the grid items doesn't matter. Your CSS can place them in any order, which makes it super easy to rearrange your grid with media queries. Imagine defining the layout of your entire page, and then completely rearranging it to accommodate a different screen width all with only a couple lines of CSS. Grid is one of the most powerful CSS modules ever introduced.

As of March 2017, most browsers shipped native, unprefixed support for CSS Grid: Chrome (including on Android), Firefox, Safari (including on iOS), and Opera. Internet Explorer 10 and 11 on the other hand support it, but it's an old implementation with an outdated syntax. The time to build with grid is now!

### Important terminology

Before diving into the concepts of Grid it's important to understand the terminology. Since the terms involved here are all kinda conceptually similar, it's easy to confuse them with one another if you don't first memorize their meanings defined by the Grid specification. But don't worry, there aren't many of them.

**Grid Container**

The element on which `display: grid` is applied. It's the direct parent of all the grid items. In this example container is the grid container.

```html
<div class="container">
  <div class="item item-1"></div>
  <div class="item item-2"></div>
  <div class="item item-3"></div>
</div>
```

**Grid Item**

The children (e.g. direct descendants) of the grid container. Here the item elements are grid items, but sub-item isn't.

```html
<div class="container">
  <div class="item"></div> 
  <div class="item">
  	<p class="sub-item"></p>
  </div>
  <div class="item"></div>
</div>
```

**Grid Line**

The dividing lines that make up the structure of the grid. They can be either vertical ("column grid lines") or horizontal ("row grid lines") and reside on either side of a row or column. Here the yellow line is an example of a column grid line.

![grid-line](https://media.githubusercontent.com/media/xinetzone/ipywidgets-media/main/images/grid-line.png)

**Grid Track**

The space between two adjacent grid lines. You can think of them like the columns or rows of the grid. Here's the grid track between the second and third row grid lines.

![grid-track](https://media.githubusercontent.com/media/xinetzone/ipywidgets-media/main/images/grid-track.png)

**Grid Cell**

The space between two adjacent row and two adjacent column grid lines. It's a single "unit" of the grid. Here's the grid cell between row grid lines 1 and 2, and column grid lines 2 and 3.

![grid-cell](https://media.githubusercontent.com/media/xinetzone/ipywidgets-media/main/images/grid-cell.png)

**Grid Area**

The total space surrounded by four grid lines. A grid area may be comprised of any number of grid cells. Here's the grid area between row grid lines 1 and 3, and column grid lines 1 and 3.

![grid-area](https://media.githubusercontent.com/media/xinetzone/ipywidgets-media/main/images/grid-area.png)

### Properties of the parent

**grid-template-rows, grid-template-colums**

Defines the columns and rows of the grid with a space-separated list of values. The values represent the track size, and the space between them represents the grid line.

Values:

- `<track-size>` - can be a length, a percentage, or a fraction of the free space in the grid (using the `fr` unit)
- `<line-name>` - an arbitrary name of your choosing

**grid-template-areas** 

Defines a grid template by referencing the names of the grid areas which are specified with the grid-area property. Repeating the name of a grid area causes the content to span those cells. A period signifies an empty cell. The syntax itself provides a visualization of the structure of the grid.

Values:

- `<grid-area-name>` - the name of a grid area specified with `grid-area`
- `.` - a period signifies an empty grid cell
- `none` - no grid areas are defined

**grid-gap** 

A shorthand for `grid-row-gap` and `grid-column-gap`

Values:

- `<grid-row-gap>`, `<grid-column-gap>` - length values

where `grid-row-gap` and `grid-column-gap` specify the sizes of the grid lines. You can think of it like setting the width of the gutters between the columns / rows.

- `<line-size>` - a length value

*Note: The `grid-` prefix will be removed and `grid-gap` renamed to `gap`. The unprefixed property is already supported in Chrome 68+, Safari 11.2 Release 50+ and Opera 54+.*

**align-items**

Aligns grid items along the block (column) axis (as opposed to justify-items which aligns along the inline (row) axis). This value applies to all grid items inside the container.

Values:

- `start` - aligns items to be flush with the start edge of their cell
- `end` - aligns items to be flush with the end edge of their cell
- `center` - aligns items in the center of their cell
- `stretch` - fills the whole height of the cell (this is the default)

**justify-items**

Aligns grid items along the inline (row) axis (as opposed to `align-items` which aligns along the block (column) axis). This value applies to all grid items inside the container.

Values:

- `start` - aligns items to be flush with the start edge of their cell
- `end` - aligns items to be flush with the end edge of their cell
- `center` - aligns items in the center of their cell
- `stretch` - fills the whole width of the cell (this is the default)

**align-content**

Sometimes the total size of your grid might be less than the size of its grid container. This could happen if all of your grid items are sized with non-flexible units like `px`. In this case you can set the alignment of the grid within the grid container. This property aligns the grid along the block (column) axis (as opposed to justify-content which aligns the grid along the inline (row) axis).

Values:

- `start` - aligns the grid to be flush with the start edge of the grid container
- `end` - aligns the grid to be flush with the end edge of the grid container
- `center` - aligns the grid in the center of the grid container
- `stretch` - resizes the grid items to allow the grid to fill the full height of the grid container
- `space-around` - places an even amount of space between each grid item, with half-sized spaces on the far ends
- `space-between` - places an even amount of space between each grid item, with no space at the far ends
- `space-evenly` - places an even amount of space between each grid item, including the far ends

**justify-content**

Sometimes the total size of your grid might be less than the size of its grid container. This could happen if all of your grid items are sized with non-flexible units like `px`. In this case you can set the alignment of the grid within the grid container. This property aligns the grid along the inline (row) axis (as opposed to align-content which aligns the grid along the block (column) axis).

Values:

- `start` - aligns the grid to be flush with the start edge of the grid container
- `end` - aligns the grid to be flush with the end edge of the grid container
- `center` - aligns the grid in the center of the grid container
- `stretch` - resizes the grid items to allow the grid to fill the full width of the grid container
- `space-around` - places an even amount of space between each grid item, with half-sized spaces on the far ends
- `space-between` - places an even amount of space between each grid item, with no space at the far ends
- `space-evenly` - places an even amount of space between each grid item, including the far ends

**grid-auto-columns, grid-auto-rows**

Specifies the size of any auto-generated grid tracks (aka implicit grid tracks). Implicit tracks get created when there are more grid items than cells in the grid or when a grid item is placed outside of the explicit grid. (see The Difference Between Explicit and Implicit Grids)

Values:

- `<track-size>` - can be a length, a percentage, or a fraction of the free space in the grid (using the `fr` unit)

### Properties of the items

*Note: `float`, `display: inline-block`, `display: table-cell`, `vertical-align` and `column-??` properties have no effect on a grid item.*

**grid-column, grid-row**

Determines a grid item's location within the grid by referring to specific grid lines. `grid-column-start`/`grid-row-start` is the line where the item begins, and `grid-column-end`/`grid-row-end` is the line where the item ends.

Values:

- `<line>` - can be a number to refer to a numbered grid line, or a name to refer to a named grid line
- `span <number>` - the item will span across the provided number of grid tracks
- `span <name>` - the item will span across until it hits the next line with the provided name
- `auto` - indicates auto-placement, an automatic span, or a default span of one

```css
.item {
  grid-column: <number> | <name> | span <number> | span <name> | auto / 
               <number> | <name> | span <number> | span <name> | auto
  grid-row: <number> | <name> | span <number> | span <name> | auto /
            <number> | <name> | span <number> | span <name> | auto
}
```

Examples:

```css
.item-a {
  grid-column: 2 / five;
  grid-row: row1-start / 3;
}
```

![grid-start-end-a](https://media.githubusercontent.com/media/xinetzone/ipywidgets-media/main/images/grid-start-end-a.png)

```css
.item-b {
  grid-column: 1 / span col4-start;
  grid-row: 2 / span 2;
}
```

![grid-start-end-b](https://media.githubusercontent.com/media/xinetzone/ipywidgets-media/main/images/grid-start-end-b.png)

If no `grid-column` / `grid-row` is declared, the item will span 1 track by default.

Items can overlap each other. You can use `z-index` to control their stacking order.

**grid-area**

Gives an item a name so that it can be referenced by a template created with the `grid-template-areas` property. Alternatively, this property can be used as an even shorter shorthand for `grid-row-start` + `grid-column-start` + `grid-row-end` + `grid-column-end`.

Values:

- `<name>` - a name of your choosing
- `<row-start> / <column-start> / <row-end> / <column-end>` - can be numbers or named lines

```css
.item {
  grid-area: <name> | <row-start> / <column-start> / <row-end> / <column-end>;
}
```

Examples:

As a way to assign a name to the item:

```css
.item-d {
  grid-area: header
}
```

As the short-shorthand for `grid-row-start` + `grid-column-start` + `grid-row-end` + `grid-column-end`:

```css
.item-d {
  grid-area: 1 / col4-start / last-line / 6
}
```

![grid-start-end-d](https://media.githubusercontent.com/media/xinetzone/ipywidgets-media/main/images/grid-start-end-d.png)

**justify-self**

Aligns a grid item inside a cell along the inline (row) axis (as opposed to `align-self` which aligns along the block (column) axis). This value applies to a grid item inside a single cell.

Values:

- `start` - aligns the grid item to be flush with the start edge of the cell
- `end` - aligns the grid item to be flush with the end edge of the cell
- `center` - aligns the grid item in the center of the cell
- `stretch` - fills the whole width of the cell (this is the default)

```css
.item {
  justify-self: start | end | center | stretch;
}
```

Examples:

```css
.item-a {
  justify-self: start;
}
```

![Example of `justify-self` set to start](https://media.githubusercontent.com/media/xinetzone/ipywidgets-media/main/images/grid-justify-self-start.png)

```css
.item-a {
  justify-self: end;
}
```

![Example of `justify-self` set to end](https://media.githubusercontent.com/media/xinetzone/ipywidgets-media/main/images/grid-justify-self-end.png)

```css
.item-a {
  justify-self: center;
}
```

![Example of `justify-self` set to center](https://media.githubusercontent.com/media/xinetzone/ipywidgets-media/main/images/grid-justify-self-center.png)

```css
.item-a {
  justify-self: stretch;
}
```

![Example of `justify-self` set to stretch](https://media.githubusercontent.com/media/xinetzone/ipywidgets-media/main/images/grid-justify-self-stretch.png)

To set alignment for *all* the items in a grid, this behavior can also be set on the grid container via the `justify-items` property.


```python
from ipywidgets import Button, GridBox, Layout, ButtonStyle
```

按名称摆放：


```python
header  = Button(description='Header',
                 layout=Layout(width='auto', grid_area='header'),
                 style=ButtonStyle(button_color='lightblue'))
main    = Button(description='Main',
                 layout=Layout(width='auto', grid_area='main'),
                 style=ButtonStyle(button_color='moccasin'))
sidebar = Button(description='Sidebar',
                 layout=Layout(width='auto', grid_area='sidebar'),
                 style=ButtonStyle(button_color='salmon'))
footer  = Button(description='Footer',
                 layout=Layout(width='auto', grid_area='footer'),
                 style=ButtonStyle(button_color='olive'))

GridBox(children=[header, main, sidebar, footer],
        layout=Layout(
            width='50%',
            grid_template_rows='auto auto auto',
            grid_template_columns='25% 25% 25% 25%',
            grid_template_areas='''
            "header header header header"
            "main main . sidebar "
            "footer footer footer footer"
            ''')
       )
```


    GridBox(children=(Button(description='Header', layout=Layout(grid_area='header', width='auto'), style=ButtonSt…


设置行、列模板和间隙


```python
GridBox(children=[Button(layout=Layout(width='auto', height='auto'),
                         style=ButtonStyle(button_color='darkseagreen')) for i in range(9)
                 ],
        layout=Layout(
            width='50%',
            grid_template_columns='100px 50px 100px',
            grid_template_rows='80px auto 80px',
            grid_gap='5px 10px')
       )
```


    GridBox(children=(Button(layout=Layout(height='auto', width='auto'), style=ButtonStyle(button_color='darkseagr…


## `Image` layout and sizing

The layout and sizing of images is a little different than for other elements for a combination of historical reasons (the HTML tag `img` existed before CSS) and practical reasons (an image has an intrinsic size).

Sizing of images is particularly confusing because there are two plausible ways to specify the size of an image. The `Image` widget has attributes `width` and `height` that correspond to attributes of the same name on the HTML `img` tag. In addition, the `Image` widget, like every other widget, has a `layout`, which also has a `width` and `height`.

In addition, some CSS styling is applied to images that is not applied to other widgets: `max_width` is set to `100%` and `height` is set to `auto`. 

You should not rely on `Image.width` or `Image.height` to determine the display width and height of the image widget. Any CSS styling, whether via `Image.layout` or some other source, will override `Image.width` and `Image.height`.

When displaying an `Image` widget by itself, setting `Image.layout.width` to the desired width will display the `Image` widget with the same aspect ratio as the original image.

When placing an `Image` inside a `Box` (or `HBox` or `VBox`) the result depends on whether a width has been set on the `Box`. If it has, then the image will be stretched (or compressed) to fit within the box because `Image.layout.max_width` is `100%` so the image fills the container. This will usually not preserve the aspect ratio of image.

### Controlling the display of an `Image` inside a container

Use `Image.layout.object_fit` to control how an image is scaled inside a container like a box. The possible values are:

+ `'contain'`: Fit the image in its content box while preserving the aspect ratio. If any of the container is not covered by the image, the background of the container is displayed. The content box is the size of the container if the container is smaller than the image, or the size of the image if the container is larger.
+ `'cover'`: Fill the content box completely while preserving the aspect ratio of the image, cropping the image if necessary.
+ `'fill'`: Completely fill the content box, stretching/compressing the image as necessary. 
+ `'none'`: Do no resizing; image will be clipped by the container.
+ `'scale-down'`: Do the same thing as either `contain` or `none`, using whichever results in the smaller dispayed image.
+ `None` (the Python value): Remove `object_fit` from the layout; effect is same as `'fill'`.

Use `Image.layout.object_position` to control how where an image is positioned within a container like a box. The default value ensures that the image is centered in the box. The effect of `Image.layout.object_position` depends, in some cases, on the value of `Image.layout.object_fit`.

There are severl ways to specify the value for `object_position`, described below.

### Examples of `object_fit`

In the example below an image is displayed inside a green box to demonstrate each of the values for `object_fit`.

To keep the example uniform, define common code here.


```python
from ipywidgets import Layout, Box, VBox, HBox, HTML, Image

fit_options = ['contain', 'cover', 'fill', 'scale-down', 'none', None]

hbox_layout = Layout()
hbox_layout.width = '100%'
hbox_layout.justify_content = 'space-around'

green_box_layout = Layout()
green_box_layout.width = '100px'
green_box_layout.height = '100px'
green_box_layout.border = '2px solid green'


def make_box_for_grid(image_widget, fit):
    """
    Make a VBox to hold caption/image for demonstrating
    option_fit values.
    """
    # Make the caption
    if fit is not None:
        fit_str = "'{}'".format(fit)
    else:
        fit_str = str(fit)

    h = HTML(value='' + str(fit_str) + '')

    # Make the green box with the image widget inside it
    boxb = Box()
    boxb.layout = green_box_layout
    boxb.children = [image_widget]

    # Compose into a vertical box
    vb = VBox()
    vb.layout.align_items = 'center'
    vb.children = [h, boxb]
    return vb

# Use this margin to eliminate space between the image and the box
image_margin = '0 0 0 0'

# Set size of captions in figures below
caption_size = 'h4'
```

### `object_fit` 在一个比原始图像小的 `Box` 中

每一个的效果可以在下面的图片中看到。在每种情况下，图像都位于一个带有绿色边框的框中。原始图像是 600x300，图像中的网格框是正方形。因为图像比框宽，所以内容框就是容器的大小。


```python
with open('images/gaussian_with_grid.png', 'rb') as f:
    im_600_300 = f.read()
```


```python
boxes = []
for fit in fit_options:
    ib = Image(value=im_600_300)
    ib.layout.object_fit = fit
    ib.layout.margin = image_margin

    boxes.append(make_box_for_grid(ib, fit))

vb = VBox()
h = HTML(value='<{size}>Examples of <code>object_fit</code> with large image</{size}>'.format(size=caption_size))
vb.layout.align_items = 'center'
hb = HBox()
hb.layout = hbox_layout
hb.children = boxes

vb.children = [h, hb]
vb
```


    VBox(children=(HTML(value='<h4>Examples of <code>object_fit</code> with large image</h4>'), HBox(children=(VBo…


### `object_fit` 在一个比原始图像大的 `Box` 中

每一个的效果可以在下面的图片中看到。在每种情况下，图像都位于一个带有绿色边框的框中。原始图像是50x25，图像中的网格是正方形。


```python
with open('images/gaussian_with_grid_tiny.png', 'rb') as f:
    im_50_25 = f.read()

boxes = []
for fit in fit_options:
    ib = Image(value=im_50_25)
    ib.layout.object_fit = fit
    ib.layout.margin = image_margin
    boxes.append(make_box_for_grid(ib, fit))

vb = VBox()
h = HTML(value='<{size}>Examples of <code>object_fit</code> with small image</{size}>'.format(size=caption_size))
vb.layout.align_items = 'center'
hb = HBox()
hb.layout = hbox_layout
hb.children = boxes

vb.children = [h, hb]
vb
```


    VBox(children=(HTML(value='<h4>Examples of <code>object_fit</code> with small image</h4>'), HBox(children=(VBo…


It may be surprising, given the description of the values for `option_fit`, that in none of the cases does the image actually fill the box. The reason is that the underlying image is only 50 pixels wide, half the width of the box, so `fill` and `cover` mean "fill/cover the content box determined by the size of the image".

### `object_fit` in a `Box` larger than the original image: use image layout width `100%`  to fill container

If the width of the image's layout is set to `100%` it will fill the box in which it is placed. This example also illustrates the difference between `'contain'` and `'scale-down'`. The effect of `'scale-down'` is either the same as `'contain'` or `'none'`, whichever leads to the smaller displayed image. In this case, the smaller image comes from doing no fitting, so that is what is displayed.


```python
boxes = []
for fit in fit_options:
    ib = Image(value=im_50_25)
    ib.layout.object_fit = fit
    ib.layout.margin = image_margin

    # NOTE WIDTH IS SET TO 100%
    ib.layout.width = '100%'

    boxes.append(make_box_for_grid(ib, fit))

vb = VBox()
h = HTML(value='<{size}>Examples of <code>object_fit</code> with image '
               'smaller than container</{size}>'.format(size=caption_size))
vb.layout.align_items = 'center'
hb = HBox()
hb.layout = hbox_layout
hb.children = boxes

vb.children = [h, hb]
vb
```


    VBox(children=(HTML(value='<h4>Examples of <code>object_fit</code> with image smaller than container</h4>'), H…


### Examples of `object_position`

There are several ways to set object position:

+ Use keywords like `top` and `left` to describe how the image should be placed in the container.
+ Use two positions, in pixels, for the offset from the top, left corner of the container to the top, left corner of the image. The offset may be positive or negative, and can be used to position the image outside of the box.
+ Use a percentage for the offset in each direction. The percentage is a the fraction of the vertical or horizontal *whitespace* around the image if the image is smaller than the container, or the portion of the image outside the container if the image is larger than the container.
+ A mix of pixel and percent offsets.
+ A mix of keywords and offsets.

Image scaling as determined by `object_fit` will take precendence over the positioning in some cases. For example, if `object_fit`  is `fill`, so that the image is supposed to fill the container without preserving the aspect ratio, then `object_position` will have no effect because there is effectively no positioning to do. 

Another way to think about it is this: `object_position` specifies how the *white space* around an image should be distributed in a container if there is white space in a particular direction. 

### Specifying `object_position` with keywords 

This form of `object_position` takes two keywords, one for horizontal position of the image in the container and one for the vertical position, in that order. 

+ The horizontal position must be one of: 
    * `'left'`: the left side of the image should be aligned with the left side of the container
    * `'center'`: the image should be centered horizontally in the container.
    * `'right'`: the right side of the image should be aligned with the rigth side of the container.
+ The vertical position must be one of
    * `'top'`: the top of the image should be aligned with the top of the container.
    * '`center`': the image should be centered vertically in the container. 
    * `'bottom'`: the bottom of the image should be aligned with the bottom of the container.
    
The effect of each is display below, once for an image smaller than the container and once for an image larger than the container. 

In the examples below the `object_fit` is set to `'none'` so that the image is not scaled.


```python
object_fit = 'none'
image_value = [im_600_300, im_50_25]
horz_keywords = ['left', 'center', 'right']
vert_keywords = ['top', 'center', 'bottom']

rows = []
for image, caption  in zip(image_value, ['600 x 300 image', '50 x 25 image']):
    cols = []
    for horz in horz_keywords:
        for vert in vert_keywords:
            ib = Image(value=image)
            ib.layout.object_position = '{horz} {vert}'.format(horz=horz, vert=vert)
            ib.layout.margin = image_margin
            ib.layout.object_fit = object_fit
            # ib.layout.height = 'inherit'
            ib.layout.width = '100%'
            cols.append(make_box_for_grid(ib, ib.layout.object_position))
    hb = HBox()
    hb.layout = hbox_layout
    hb.children = cols
    rows.append(hb)

vb = VBox()

h1 = HTML(value='<{size}><code> object_position </code> by '
                'keyword with large image</{size}>'.format(size=caption_size))
h2 = HTML(value='<{size}><code> object_position </code> by '
                'keyword with small image</{size}>'.format(size=caption_size))

vb.children = [h1, rows[0], h2, rows[1]]
vb.layout.height = '400px'
vb.layout.justify_content = 'space-around'
vb.layout.align_items = 'center'
vb
```


    VBox(children=(HTML(value='<h4><code> object_position </code> by keyword with large image</h4>'), HBox(childre…


### Specifying `object_position` with offsets in pixels

One can specify the offset of the top, left corner of the image from the top, left corner of the container in pixels. The first of the two offsets is horizontal, the second is vertical and either may be negative. Using a large enough offset that the image is outside the container will result in the image being hidden.

The image is scaled first using the value of `object_fit` (which defaults to `fill` if nothing is specified) then the offset is applied.

Offsets can be specified from the bottom and/or right side by combining keywords and pixel offsets. For example, `right 10px bottom 20px` offsets the right side of the image 10px from the right edge of the container and the image bottom 20px from the bottom of the container.


```python
object_fit = ['none', 'contain', 'fill', 'cover']
offset = '20px 10px'
image_value = [im_600_300]

boxes = []
for image, caption  in zip(image_value, ['600 x 300 image', ]):
    for fit in object_fit:
        ib = Image(value=image)
        ib.layout.object_position = offset
        ib.layout.margin = image_margin
        ib.layout.object_fit = fit
        # ib.layout.height = 'inherit'
        ib.layout.width = '100%'
        title = 'object_fit: {}'.format(ib.layout.object_fit)
        boxes.append(make_box_for_grid(ib, title))

vb = VBox()
h = HTML(value='<{size}><code>object_position</code> by '
               'offset {offset} with several '
               '<code>object_fit</code>s with large image</{size}>'.format(size=caption_size,
                                                         offset=offset))
vb.layout.align_items = 'center'
hb = HBox()
hb.layout = hbox_layout
hb.children = boxes

vb.children = [h, hb]
vb
```


    VBox(children=(HTML(value='<h4><code>object_position</code> by offset 20px 10px with several <code>object_fit<…


### Specifying `object_position` with offsets as a percentage

One can specify the offset of the top, left corner of the image from the top, left corner of the container as a percent.The first of the two offsets is horizontal, the second is vertical and either may be negative. Using a large enough offset that the image is outside the container will result in the image being hidden. 

The important thing to understand is that this is a percent of the *white space* in each direction if the image is smaller than the container, so that `50% 50%` centers the image. 

If the image is larger than the container after scaling using the `object_fit`, then the offset is a percentage of the overflow of the image outside the container. That means that `50% 50%` also centers an image *larger* than the container. A value of `10% 90%` would put 10% of the out-of-container part of the image left of  the left edge and 90% of the out-of-container part vertically above the top edge.

As with specifying the `object_position` by keywords, the `object_fit` can prevent any offset from being applied to the image.
