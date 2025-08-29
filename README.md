# Ink Format

**Ink** is a lightweight, storage-optimized JSON format for representing rich text content. It supports multi-line text, inline styles, links, images, lists, and tables, while remaining compact and easy to parse.

This README explains the structure and conventions of the format for developers who want to render, manipulate, or extend Ink content.

---

## Table of Contents

1. [Block Types](#block-types)
2. [Text Content and Styles](#text-content-and-styles)
3. [Lists](#lists)
4. [Tables](#tables)
5. [Images](#images)
6. [Example JSON](#example-json)
7. [Rendering Notes](#rendering-notes)

---

## Block Types

Each content item is represented as a **block object**. Blocks are stored in a JSON array.

| Type             | Description                                                                  |
| ---------------- | ---------------------------------------------------------------------------- |
| `paragraph`      | Multi-line text block. Supports inline styles and links.                     |
| `image`          | Image block with source path/URL and optional alt text.                      |
| `ordered_list`   | Ordered list block containing multiple items.                                |
| `unordered_list` | Unordered (bullet) list block containing multiple items.                     |
| `table`          | Table block containing rows and cells, with optional inline styles in cells. |

---

## Text Content and Styles

### Block Structure

```json
{
  "type": "paragraph",
  "content": "Hello world!\nCheck out Google.",
  "styles": [
    { "offset": 0, "length": 5, "style": "bold" },
    { "offset": 13, "length": 6, "style": "link", "href": "https://google.com" }
  ]
}
```

### Style Properties

| Key      | Description                                                               |
| -------- | ------------------------------------------------------------------------- |
| `offset` | Start index of the styled substring in `content`. Counts `\n` characters. |
| `length` | Number of characters the style applies to.                                |
| `style`  | Inline style type: `"bold"`, `"italic"`, `"underline"`, `"link"`, etc.    |
| `href`   | URL for `"link"` style. Only used when `style` is `"link"`.               |

**Notes:**

* `\n` characters represent line breaks and are included in offset calculations.
* Multiple styles can overlap on the same text.

---

## Lists

### Ordered and Unordered Lists

```json
{
  "type": "ordered_list",
  "items": [
    { "content": "Step one" },
    { "content": "Step two", "styles": [{ "offset": 5, "length": 3, "style": "bold" }] }
  ]
}
```

* `items` is an array of objects, each with `content` and optional `styles`.
* Both `ordered_list` and `unordered_list` use the same structure.

---

## Tables

Tables are stored as a **list of rows**, and each row is a **list of cells**. Each cell can have its own inline styles.

```json
{
  "type": "table",
  "rows": [
    [
      { "content": "Name" },
      { "content": "Age" },
      { "content": "Country" }
    ],
    [
      { "content": "Alice", "styles": [{ "offset": 0, "length": 5, "style": "bold" }] },
      { "content": "25" },
      { "content": "USA", "styles": [{ "offset": 0, "length": 3, "style": "link", "href": "https://en.wikipedia.org/wiki/United_States" }] }
    ]
  ]
}
```

* `rows` → array of rows.
* Each row → array of cells.
* Each cell → object with `content` and optional `styles`.

---

## Images

```json
{
  "type": "image",
  "src": "local://image1.png",
  "alt": "Example image"
}
```

* `src` → image path or URL.
* `alt` → optional alt text for accessibility.

---

## Example JSON

```json
[
  {
    "type": "paragraph",
    "content": "Hello world!\nThis is multi-line text.\nCheck Google.",
    "styles": [
      { "offset": 0, "length": 5, "style": "bold" },
      { "offset": 13, "length": 4, "style": "italic" },
      { "offset": 42, "length": 6, "style": "link", "href": "https://google.com" }
    ]
  },
  {
    "type": "image",
    "src": "local://image1.png",
    "alt": "Example image"
  },
  {
    "type": "ordered_list",
    "items": [
      { "content": "Step one" },
      { "content": "Step two", "styles": [{ "offset": 5, "length": 3, "style": "bold" }] }
    ]
  },
  {
    "type": "unordered_list",
    "items": [
      { "content": "Bullet 1" },
      { "content": "Bullet 2", "styles": [{ "offset": 0, "length": 6, "style": "italic" }] }
    ]
  },
  {
    "type": "table",
    "rows": [
      [
        { "content": "Name" },
        { "content": "Age" },
        { "content": "Country" }
      ],
      [
        { "content": "Alice", "styles": [{ "offset": 0, "length": 5, "style": "bold" }] },
        { "content": "25" },
        { "content": "USA", "styles": [{ "offset": 0, "length": 3, "style": "link", "href": "https://en.wikipedia.org/wiki/United_States" }] }
      ]
    ]
  }
]
```

---

## Rendering Notes

1. **Paragraphs**: split by `\n` when rendering line by line. Adjust offsets for styles relative to each line.
2. **Inline styles**: can overlap and combine (e.g., bold + link).
3. **Lists**: iterate `items` array and apply inline styles per item.
4. **Tables**: iterate `rows` → `cells` and render cell content with inline styles.
5. **Images**: render using `src`; optionally show `alt`.

---

This format is **compact, flexible, and easy to extend** for future features like mentions, hashtags, or additional block types.

