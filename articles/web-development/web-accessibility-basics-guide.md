---
title: "Web Accessibility Basics: Build Websites More People Can Use"
description: "Learn the foundations of accessible web design, including semantic HTML, keyboard support, text alternatives, contrast, and form labels."
slug: "web-accessibility-basics-guide"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Web Development"
tags:
  - "Accessibility"
  - "HTML"
  - "Frontend Development"
  - "WCAG"
relatedTools: []
relatedArticles:
  - "rest-api-design-basics"
draft: false
---

# Web Accessibility Basics: Build Websites More People Can Use

A website should be usable by people with different abilities, devices, and interaction methods. Some users navigate with a keyboard, use a screen reader, magnify content, or rely on captions and clear visual structure.

Web accessibility is the practice of designing and developing digital experiences so that more people can perceive, understand, navigate, and interact with them.

## What is web accessibility?

Accessibility includes more than adding alt text to images. It involves the whole experience:

- Content structure and semantics.
- Keyboard navigation.
- Focus visibility.
- Color contrast.
- Form labels and error messages.
- Captions and transcripts for media.
- Responsive layout and zoom support.
- Clear language and predictable navigation.

Accessibility should be considered from the planning stage, not treated as a final visual adjustment.

## Use semantic HTML

Semantic HTML elements describe the role of content.

Use headings in a meaningful hierarchy:

~~~html
<main>
  <h1>Account settings</h1>

  <section>
    <h2>Profile</h2>
    <p>Manage your profile information.</p>
  </section>
</main>
~~~

Use button for actions and anchor for navigation. Avoid replacing native controls with generic div elements unless there is a strong reason and the necessary keyboard and accessibility behavior is implemented.

Semantic HTML helps browsers and assistive technologies communicate the document structure.

## Provide text alternatives for images

Meaningful images should have appropriate alternative text.

~~~html
<img
  src="/images/temperature-chart.png"
  alt="Temperature rises from 18 degrees in the morning to 29 degrees in the afternoon."
>
~~~

Decorative images should generally use an empty alt attribute so they do not create unnecessary announcements.

Alt text should communicate the relevant information, not describe every visual detail automatically. Complex charts may need a longer description or a nearby data table.

## Support keyboard navigation

A website should be usable without a mouse.

Test whether users can:

- Move through interactive controls using Tab and Shift+Tab.
- Activate buttons and links using the expected keys.
- Operate menus and dialogs.
- See which element currently has focus.
- Escape or close overlays where appropriate.

Do not remove the browser's focus outline unless you replace it with a clearly visible focus indicator.

## Use accessible forms

Every form control should have a programmatically associated label.

~~~html
<label for="email">Email address</label>
<input id="email" name="email" type="email" required>
~~~

Placeholder text is not a replacement for a label. It disappears while the user types and may have poor contrast.

When validation fails, identify the field, explain the problem in text, and associate the error message with the control. Do not rely only on a red border or color change.

## Check color contrast

Text and interactive elements should have sufficient contrast against their backgrounds. Low contrast can make content difficult to read.

The WCAG contrast requirements depend on text size and the type of content. Use an approved contrast checker and verify normal, hover, focus, disabled, and error states.

Color should not be the only way to convey meaning. Pair color with labels, icons that have accessible names, patterns, or text.

## Make media accessible

For prerecorded video with meaningful audio, provide captions. For audio-only content, provide a transcript. Live media may require real-time captioning or other accommodations depending on the use case.

Do not autoplay sound unexpectedly. Give users control over animation, audio, and video where appropriate.

## Understand WCAG principles

The Web Content Accessibility Guidelines are organized around four principles, often remembered as POUR:

- **Perceivable:** information and interface components must be presentable to users in ways they can perceive.
- **Operable:** interface components and navigation must be operable.
- **Understandable:** information and operation should be understandable.
- **Robust:** content should work with current and future user agents, including assistive technologies.

WCAG conformance levels are A, AA, and AAA. Many policies and procurement requirements reference WCAG 2.2 Level AA, but the applicable legal and contractual standard depends on jurisdiction and context.

## Test accessibility in practice

Use a combination of methods:

1. Navigate the page with only a keyboard.
2. Check headings, landmarks, labels, and names with a screen reader.
3. Inspect contrast and focus indicators.
4. Zoom the page and test responsive layouts.
5. Run an automated accessibility checker.
6. Ask people with disabilities to test important workflows where possible.

Automated tools can detect some issues, but they cannot determine whether every interaction is understandable or whether alternative text is meaningful.

## Common accessibility mistakes

### Using placeholder text instead of labels

Controls need persistent, programmatic labels.

### Using clickable div elements

Native buttons and links provide built-in keyboard and accessibility behavior.

### Removing focus outlines

Keyboard users need to see which control is active.

### Providing vague alt text

Text such as "image" or "photo" rarely communicates useful information.

### Relying only on automated scans

Manual testing and user feedback are necessary to catch context and usability problems.

## Frequently asked questions

### Is accessibility only for screen-reader users?

No. It benefits people with visual, hearing, motor, cognitive, and temporary impairments, as well as users in difficult environments or with different devices.

### Does semantic HTML improve accessibility?

It provides meaningful structure and built-in browser behavior that assistive technologies can use. It is an important foundation, though not the whole solution.

### Can an automated tool guarantee WCAG compliance?

No. Automated tools find some detectable issues but cannot fully evaluate semantics, context, and real user experience.

## Further reading

- [W3C: Web Content Accessibility Guidelines (WCAG) 2.2](https://www.w3.org/TR/WCAG22/)
- [WAI: Introduction to Web Accessibility](https://www.w3.org/WAI/fundamentals/accessibility-intro/)
- [MDN: Accessibility](https://developer.mozilla.org/en-US/docs/Web/Accessibility)

---
