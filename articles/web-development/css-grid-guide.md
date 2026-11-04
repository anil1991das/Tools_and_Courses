---
title: "CSS Grid Explained: Build Two-Dimensional Web Layouts"
description: "Learn CSS Grid containers, columns, rows, gaps, fractional units, and responsive layouts with practical examples."
slug: "css-grid-guide"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Web Development"
tags:
  - "CSS"
  - "CSS Grid"
  - "Layout"
  - "Responsive Design"
relatedTools: []
relatedArticles:
  - "css-flexbox-guide"
draft: false
---

# CSS Grid Explained: Build Two-Dimensional Web Layouts

CSS Grid is a layout system for arranging content in rows and columns. It is especially useful when a page needs coordinated two-dimensional structure, such as a dashboard, gallery, or application shell.

Grid lets developers define tracks, gaps, and areas while allowing content to occupy one or more cells.

## Create a grid container

Set display: grid on a parent element:

~~~css
.grid {
  display: grid;
}
~~~

The direct children become grid items. Without explicit tracks, the browser creates implicit rows as items are placed.

## Define columns and rows

Use grid-template-columns and grid-template-rows to define tracks:

~~~css
.layout {
  display: grid;
  grid-template-columns: 16rem 1fr;
  grid-template-rows: auto 1fr auto;
}
~~~

The fr unit represents a fraction of the available space after fixed sizes and relevant gaps are accounted for.

In this example, the first column has a fixed width and the second column takes the remaining space.

## Use repeat and minmax

The repeat() and minmax() functions help create flexible track definitions.

~~~css
.cards {
  display: grid;
  grid-template-columns: repeat(3, minmax(0, 1fr));
  gap: 1rem;
}
~~~

For a responsive layout:

~~~css
.cards {
  display: grid;
  grid-template-columns: repeat(
    auto-fit,
    minmax(min(100%, 15rem), 1fr)
  );
  gap: 1rem;
}
~~~

This allows the browser to fit as many columns as space permits, with a minimum track size.

## Add spacing with gap

The gap property defines the spacing between grid rows and columns:

~~~css
.grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 1.5rem;
}
~~~

Use row-gap and column-gap when the two directions need different spacing.

## Place items across tracks

An item can span multiple columns or rows:

~~~css
.featured {
  grid-column: span 2;
}
~~~

You can also specify grid lines:

~~~css
.sidebar {
  grid-column: 1;
  grid-row: 1 / 3;
}
~~~

Grid line placement is useful for defined layouts, but avoid making a page unnecessarily dependent on fixed item positions when content order should remain flexible.

## Use named grid areas

Named areas can make a page layout easier to read:

~~~css
.page {
  display: grid;
  grid-template-areas:
    "header header"
    "sidebar content"
    "footer footer";
  grid-template-columns: 16rem 1fr;
  gap: 1rem;
}

.header { grid-area: header; }
.sidebar { grid-area: sidebar; }
.content { grid-area: content; }
.footer { grid-area: footer; }
~~~

A media query can rearrange the layout for narrow screens:

~~~css
@media (max-width: 700px) {
  .page {
    grid-template-areas:
      "header"
      "content"
      "sidebar"
      "footer";
    grid-template-columns: 1fr;
  }
}
~~~

CSS visual placement does not necessarily change the document's source or keyboard reading order. Keep the HTML order meaningful.

## Grid versus Flexbox

Grid is designed for two-dimensional layout. Flexbox is designed primarily for one-dimensional distribution along a row or column.

Use Grid when relationships between rows and columns matter. Use Flexbox when items are arranged along one main axis. A component can use both.

## Common Grid mistakes

### Defining too many fixed widths

Fixed track sizes can overflow on smaller screens. Use flexible units, minmax(), and media queries where appropriate.

### Forgetting min-width constraints

Long words, code, and images can cause grid items to overflow. minmax(0, 1fr) and min-width: 0 can help when content should be allowed to shrink.

### Confusing visual order with semantic order

Grid can place elements visually in a different location. Keep source order logical for assistive technology and keyboard users.

### Overusing absolute positioning

Grid can handle many layout relationships without removing elements from the normal flow.

## Frequently asked questions

### Can CSS Grid and Flexbox be used together?

Yes. Use Grid for page or section structure and Flexbox for alignment inside individual components when appropriate.

### What does 1fr mean?

It represents a fraction of the available space in the grid container after fixed tracks and other sizing rules are considered.

### Does Grid automatically make a site responsive?

No. Grid provides flexible layout tools, but the developer still needs to choose sensible track sizes, content behavior, and breakpoints.

## Further reading

- [MDN: CSS Grid Layout](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_grid_layout)
- [CSS Grid Layout Module Level 2](https://www.w3.org/TR/css-grid-2/)

---
