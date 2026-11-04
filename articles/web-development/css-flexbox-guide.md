---
title: "CSS Flexbox Explained: Align and Distribute Items in One Dimension"
description: "Learn the Flexbox layout model, main and cross axes, alignment properties, wrapping, and practical responsive layout examples."
slug: "css-flexbox-guide"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Web Development"
tags:
  - "CSS"
  - "Flexbox"
  - "Frontend"
  - "Responsive Design"
relatedTools: []
relatedArticles: []
draft: false
---

# CSS Flexbox Explained: Align and Distribute Items in One Dimension

CSS Flexbox is a layout model designed to distribute space and align items along one dimension. It is useful for navigation bars, toolbars, card rows, button groups, and many responsive interface components.

Flexbox can arrange elements in a row or a column and can distribute extra space between them.

## What is a flex container?

To use Flexbox, set display: flex on a parent element:

~~~css
.container {
  display: flex;
}
~~~

The direct children become flex items. The parent becomes the flex container.

By default, items are placed in a row and may shrink to fit the available width.

## Main axis and cross axis

Flexbox uses two axes:

- Main axis: the direction in which flex items are laid out.
- Cross axis: perpendicular to the main axis.

With flex-direction: row, the main axis runs horizontally in the usual writing mode. With flex-direction: column, the main axis runs vertically.

~~~css
.container {
  display: flex;
  flex-direction: row;
}
~~~

Changing flex-direction changes which axis is considered the main axis. Alignment properties should be understood relative to these axes rather than always thinking in terms of horizontal and vertical.

## Align items using justify-content

justify-content distributes items along the main axis.

Common values include:

- flex-start
- flex-end
- center
- space-between
- space-around
- space-evenly

Example:

~~~css
.navigation {
  display: flex;
  justify-content: space-between;
}
~~~

This places the first and last items at opposite ends of the available main-axis space, with space distributed between items.

## Align items using align-items

align-items controls alignment on the cross axis.

~~~css
.toolbar {
  display: flex;
  align-items: center;
}
~~~

Common values include stretch, flex-start, flex-end, center, and baseline.

The effect depends on the cross-axis direction and the size of the flex container.

## Add spacing with gap

The gap property adds space between flex items:

~~~css
.card-row {
  display: flex;
  gap: 1rem;
}
~~~

Gap is often easier to maintain than adding margins to every item. It is supported by modern browsers for Flexbox and Grid.

## Wrapping items

By default, flex items remain on a single line and may shrink. Enable wrapping when items should move onto additional lines:

~~~css
.tags {
  display: flex;
  flex-wrap: wrap;
  gap: 0.5rem;
}
~~~

This is useful for tag lists, button groups, and responsive cards.

## flex-grow, flex-shrink, and flex-basis

The flex shorthand combines three properties:

- flex-grow: how much an item can grow relative to other items.
- flex-shrink: how much an item can shrink when space is limited.
- flex-basis: the initial main-size basis before remaining space is distributed.

Example:

~~~css
.sidebar {
  flex: 0 0 16rem;
}

.content {
  flex: 1 1 auto;
}
~~~

The sidebar uses a fixed basis and does not grow or shrink in this example. The content region can use remaining space and shrink when necessary.

## A responsive navigation example

~~~html
<nav class="navigation">
  <a href="/">Brand</a>
  <div class="links">
    <a href="/docs">Docs</a>
    <a href="/tools">Tools</a>
    <a href="/about">About</a>
  </div>
</nav>
~~~

~~~css
.navigation {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 1rem;
}

.links {
  display: flex;
  flex-wrap: wrap;
  gap: 1rem;
}
~~~

The layout can wrap links when space is limited. Production navigation may require additional menu behavior and responsive design decisions for narrow screens.

## Flexbox versus CSS Grid

| Flexbox | Grid |
|---|---|
| Primarily one-dimensional | Two-dimensional layout |
| Distributes items along one axis at a time | Controls rows and columns together |
| Useful for toolbars and navigation | Useful for page sections and card grids |
| Content-driven distribution is common | Track-based layout is explicit |

Both can be used together. A page may use Grid for the main layout and Flexbox inside individual components.

## Common mistakes

### Confusing main-axis and cross-axis alignment

justify-content and align-items change their directional meaning when flex-direction changes.

### Expecting flex items to wrap automatically

Set flex-wrap: wrap when items should flow onto multiple lines.

### Using margins instead of gap everywhere

Margins can be appropriate, but gap often expresses spacing between siblings more directly.

### Ignoring min-width behavior

Flex items can have automatic minimum sizes that prevent expected shrinking. Check min-width and min-height when content overflows.

## Frequently asked questions

### Is Flexbox suitable for full page layouts?

It can be, but CSS Grid is often more convenient for complex two-dimensional page layouts. Choose based on the relationships between rows and columns.

### What is the difference between align-items and align-content?

align-items aligns items within a flex line. align-content distributes multiple flex lines along the cross axis when there is extra space and wrapping is enabled.

### Can Flexbox be used for vertical layouts?

Yes. Set flex-direction: column and use the alignment properties relative to the new axes.

## Further reading

- [MDN: Basic concepts of Flexbox](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_flexible_box_layout/Basic_concepts_of_flexbox)
- [CSS Flexible Box Layout Module](https://www.w3.org/TR/css-flexbox-1/)

---
