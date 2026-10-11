# Introduction to presentation.md

presentation.md holds a presentation's words, slide by slide, in Markdown. Edit it in any text editor. MarkdownPoint watches the folder, so a saved change shows on the open presentation at once.

This is the whole file of a presentation with three slides:

```markdown
---
markdownpoint: 1
document: 318494ee-4e44-456f-818a-ec761b41fec2
footer: "Plain text decks"
---

<!-- section: start -->
# Start

<!-- slide: title -->

<!-- text: eyebrow -->
MarkdownPoint

<!-- text: title -->
File format==.==

<!-- text: body -->
Your slides are Markdown files you own.

<!-- notes -->
Speaker notes go here. Only the presenter sees them.

---

<!-- slide: points -->

<!-- text: title -->
Two files, one deck==.==

<!-- text: body -->
- **presentation.md** holds the words
- **presentation.json** holds the layout, styles and steps
- The design is a folder beside them

---

<!-- section: finish -->
# Finish

<!-- slide: close -->

<!-- text: body -->
The words are Markdown==.==
```

<img src="images/first-deck-title.png" alt="The title slide, as MarkdownPoint draws it" width="32%"> <img src="images/first-deck-points.png" alt="The points slide, as MarkdownPoint draws it" width="32%"> <img src="images/first-deck-close.png" alt="The close slide, as MarkdownPoint draws it" width="32%">

## Frontmatter

The file opens with frontmatter between two `---` lines. `markdownpoint` is the format version. `document` is the presentation's id, the same as presentation.json's. `footer` holds the footer's words. Write each of these on one line, as `key: value`.

Any other key is yours. MarkdownPoint keeps it as you wrote it and never reads it.

## Sections

`<!-- section: id -->` opens a section. The `# Name` line after it names the section. A presentation can have no sections. Once it has one, every slide stands in a section.

## Slides

`<!-- slide: id -->` opens a slide. The slides show in the order presentation.md lists them. Move a slide's lines to move the slide.

Each slide also has an entry in [presentation.json](presentation-json.md) under the same id. presentation.json lists them by id, so their order there doesn't matter. `layout` names the design's layout the slide is built on.

```json
{
  "close": {"layout": "one-liner"},
  "points": {"layout": "content-with-header", "scheme": "sky"},
  "title": {"layout": "title"}
}
```

A new slide needs both its marker and its entry. Deleting or renaming a slide changes both files.

An id is lowercase letters, digits and single hyphens, and starts with a letter: `intro`, `q3-numbers`.

## Text

`<!-- text: id -->` holds an object's words, up to the next marker. Here `eyebrow`, `title` and `body` are placeholders of the slide's layout. For one of the slide's own objects, the id is the object's id in presentation.json.

## Speaker notes

`<!-- notes -->` holds the slide's speaker notes. Only the presenter sees them. They take the same Markdown as a slide's words.

## Separators

A line of `---` ends a slide. It's optional, since the next slide or section marker ends a slide too. Write a horizontal rule in a slide's words as `***`, so it never reads as a separator.

A line inside a block that would read as a marker or a separator starts with a backslash: `\<!-- slide: example -->`. The backslash keeps it as words.

## Words

A slide's words are Markdown. Text objects, tables and speaker notes all take it. The [reference](reference/v1.md#words) lists every form.

### Marks, links and colors

```markdown
<!-- slide: marks -->

<!-- text: title -->
Marks, links and colors==.==

<!-- text: body -->
Words can be **bold**, *italic*, ==highlighted==, ~~struck through~~ or `code`.

[A link](https://markdownpoint.com) opens a webpage, and [these words]{color=accent} take the accent color.
```

![The marks slide, as MarkdownPoint draws it](images/words-marks.png)

`==highlighted==` words and `[colored]{color=accent}` words are MarkdownPoint's own. A color is a role of the slide's color scheme, such as `accent`, or a hex color: `[words]{color="#2F7D4F"}`.

### Lists

```markdown
<!-- slide: lists -->

<!-- text: title -->
Lists==.==

<!-- text: body -->
- A bullet
  - A deeper bullet
- Another bullet

1. First
2. Second
```

![The lists slide, as MarkdownPoint draws it](images/words-lists.png)

Indent an item under the one before it for a deeper level. A numbered list can start at any number.

### Fields

```markdown
<!-- slide: fields -->

<!-- text: title -->
Fields==.==

<!-- text: body -->
This is slide {{slide-number}} of {{slide-count}}, in section {{section-number}}, {{section-name}}.
```

![The fields slide, as MarkdownPoint draws it](images/words-fields.png)

A field shows a value MarkdownPoint fills in, such as the slide's number. The [reference](reference/v1.md#words) names every field. The `footer` field shows the footer the frontmatter sets.

### Quotes and code

````markdown
<!-- slide: quote -->

<!-- text: title -->
Quotes and code==.==

<!-- text: body -->
> Plain text in, a point made beautifully out.

```js
const deck = "presentation.md"
```
````

![The quote slide, as MarkdownPoint draws it](images/words-quote.png)

### Tables

A table is an object of its own. Its words are a Markdown table.

```markdown
<!-- slide: table -->

<!-- text: title -->
Tables==.==

<!-- text: prices -->
| Plan | Seats | Price |
| --- | --- | --- |
| Solo | 1 | Free |
| Team | 10 | $8 |
```

Its place, columns and look are in presentation.json:

```json
[
  {
    "id": "prices",
    "type": "table",
    "frame": {"x": 120, "y": 380, "width": 1680, "height": 420},
    "inset": 24,
    "text-style": "body",
    "columns": [840, 420, 420],
    "alternating-rows": true
  }
]
```

![The table slide, as MarkdownPoint draws it](images/words-table.png)

### Markdown a slide doesn't read

A picture, an HTML tag or a footnote stays in the file as you wrote it. The slide shows its characters. Put a picture on a slide as an image object in [presentation.json](presentation-json.md).

An HTML comment is a note to yourself. It stays in the file and shows on no slide. So does a comment between a slide's `---` and the next slide.

## Editing by hand

These keep MarkdownPoint from reading the file:

- words outside a text or notes block
- a marker written wrong, such as `<!-- Slide: intro -->`, or the same marker twice
- a slide marker without its entry in presentation.json, or an entry without its marker
- a code block that doesn't close
- one of MarkdownPoint's own frontmatter keys not written as `key: value` on one line

MarkdownPoint then names the file, the line and what is wrong. It doesn't open the presentation. If the presentation is open, MarkdownPoint waits for the fix and never writes over your file. To check a folder without the app, run `mdpoint validate --files` in it.

A save keeps your spelling. Slides you didn't edit in the app stay as you wrote them. A block you edited in the app is written in MarkdownPoint's spelling.
