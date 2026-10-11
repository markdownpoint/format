# Introduction to presentation.md

presentation.md has a presentation's text, slide by slide, in Markdown. You can edit it in any text editor. MarkdownPoint watches the folder and shows your saved changes right away.

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

The file opens with frontmatter between two `---` lines. `markdownpoint` is the format version. `document` is the presentation's id, the same as in presentation.json. `footer` is the footer text. Write each of these on one line, as `key: value`.

You can add your own keys. MarkdownPoint keeps them as written and ignores them.

## Sections

`<!-- section: id -->` starts a section. The `# Name` line below it is the section's name. Sections are optional. Once there is one, every slide must be in a section.

## Slides

`<!-- slide: id -->` starts a slide. Slides appear in the order they're listed. To move a slide, move its lines.

Each slide also has an entry in [presentation.json](presentation-json.md) under the same id. The order of entries there doesn't matter. `layout` is the design layout the slide uses.

```json
{
  "close": {"layout": "one-liner"},
  "points": {"layout": "content-with-header", "scheme": "sky"},
  "title": {"layout": "title"}
}
```

A new slide needs both its marker and its entry. To delete or rename a slide, change both files.

An id is lowercase letters, digits and single hyphens, and starts with a letter: `intro`, `q3-numbers`.

## Text

`<!-- text: id -->` starts an object's text. It runs to the next marker. Here `eyebrow`, `title` and `body` are placeholders from the slide's layout. For any other object, the id is its id in presentation.json.

## Speaker notes

`<!-- notes -->` starts the slide's speaker notes. Only the presenter sees them. They use the same Markdown as slide text.

## Separators

A line of `---` ends a slide. It's optional, since the next slide or section marker also ends one. Write a horizontal rule as `***`, so it isn't read as a separator.

To use a line that looks like a marker or a separator as plain text, start it with a backslash: `\<!-- slide: example -->`.

## Markdown

Text objects, tables and speaker notes use Markdown. The [reference](reference/v1.md#markdown) lists everything that's supported.

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

`==highlighted==` and `[colored]{color=accent}` text are MarkdownPoint additions. A color is either a role from the slide's color scheme, such as `accent`, or a hex color: `[text]{color="#2F7D4F"}`.

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

Indent an item to nest it under the one above. A numbered list can start at any number.

### Fields

```markdown
<!-- slide: fields -->

<!-- text: title -->
Fields==.==

<!-- text: body -->
This is slide {{slide-number}} of {{slide-count}}, in section {{section-number}}, {{section-name}}.
```

![The fields slide, as MarkdownPoint draws it](images/words-fields.png)

A field shows a value MarkdownPoint fills in, such as the slide number. The [reference](reference/v1.md#markdown) lists every field. The `footer` field shows the footer set in the frontmatter.

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

A table is its own object, written as a Markdown table.

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

Its position, columns and style are in presentation.json:

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

### Unsupported Markdown

Images, HTML tags and footnotes are kept in the file as written. The slide shows them as plain text. To put a picture on a slide, add an image object in [presentation.json](presentation-json.md).

An HTML comment inside a text or notes block is a note to yourself. It's kept in the file and never shown. A comment between a slide's `---` and the next slide belongs to the slide's last block.

## Editing by hand

MarkdownPoint can't read the file if it has:

- text or a comment outside a text or notes block
- a marker written wrong, such as `<!-- Slide: intro -->`, or the same marker twice
- a slide marker without its entry in presentation.json, or an entry without its marker
- a code block that doesn't close
- one of MarkdownPoint's own frontmatter keys not written as `key: value` on one line

MarkdownPoint then shows the file, the line and the problem. It won't open the presentation. If it's already open, MarkdownPoint waits for the fix and never overwrites your file. To check a folder without the app, run `mdpoint validate --files` in it.

Saving keeps your formatting. Slides you didn't edit in the app stay as you wrote them. Text you edit in the app is saved in MarkdownPoint's standard Markdown.
