# The .md file

The .md file has a presentation's text, slide by slide, in Markdown. It's always named `slides.md`. You can edit it in any text editor. MarkdownPoint watches the folder and shows your saved changes right away.

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

<!-- slide: points -->

<!-- text: title -->
Two files, one deck==.==

<!-- text: body -->
- **first-deck.md** holds the words
- **first-deck.mdpoint** holds the layout, styles and steps
- The theme is a folder in **themes**

<!-- section: finish -->
# Finish

<!-- slide: close -->

<!-- text: body -->
The words are Markdown==.==
```

<img src="images/first-deck-title.png" alt="The title slide, as MarkdownPoint draws it" width="32%"> <img src="images/first-deck-points.png" alt="The points slide, as MarkdownPoint draws it" width="32%"> <img src="images/first-deck-close.png" alt="The close slide, as MarkdownPoint draws it" width="32%">

## Frontmatter

The file opens with frontmatter between two `---` lines. `markdownpoint` is the format version, the whole number 1, without quotes: `markdownpoint: 1`. `document` is the presentation's id, a UUID in lowercase, the same as in the .mdpoint file. `footer` is the footer text. Put it in double quotes if it holds a space followed by `#`, which otherwise starts a comment. MarkdownPoint writes it in double quotes. Write each of these on one line, as `key: value`. The app writes `save` itself when people take turns editing a shared folder. Leave it as it is.

You can add your own keys. MarkdownPoint keeps them as written and ignores them.

## Sections

`<!-- section: id -->` starts a section. An id is lowercase letters, digits and single hyphens, and starts with a letter: `intro`, `q3-numbers`. Under the marker goes one line, `# Name`: a level-1 heading with the section's name. MarkdownPoint drops any formatting in it. Nothing else goes in a section's block. Sections are optional. Once there is one, every slide must be in a section.

## Slides

`<!-- slide: id -->` starts a slide. Only a marker starts one. A slide runs to the next slide, section or composition marker, or to the end of the file. Slides appear in the order they're listed. To move a slide, move its lines.

Each slide also has an entry in [the .mdpoint file](mdpoint-file.md) under the same id. The order of entries there doesn't matter. `layout` is the theme layout the slide uses.

```json
{
  "close": {"layout": "one-liner"},
  "points": {"layout": "content-with-header", "scheme": "sky"},
  "title": {"layout": "title"}
}
```

A new slide needs both its marker and its entry. To delete or rename a slide, change both files.

## Text

`<!-- text: id -->` starts an object's text. It runs to the next marker. Here `eyebrow`, `title` and `body` are placeholders from the slide's layout. For any other object, the id is its id in the .mdpoint file. A shape, a table or a chart takes its text the same way.

One blank line separates two paragraphs. Each further blank line is an empty paragraph, and keeps its line on the slide. A blank line right under the marker is one too. Before the next marker, one blank line only separates.

## Speaker notes

`<!-- notes -->` starts the slide's speaker notes. Only the presenter sees them. They use the same Markdown as slide text.

## Rules

A line of `---` is a horizontal rule. On a slide, it runs across its text box. It can stand anywhere in a text or notes block: first, between paragraphs or last. A `---` at the end of a slide belongs to the slide's last block.

Leave a blank line above a rule. Right under a line of text, `---` makes that line a heading instead.

A `---` has to stand in a block. Right under a slide's marker, before its first text or notes marker, it's an error.

## Escapes

A line that looks like a marker needs a change to stay in its block. Write a backslash after `<!--`, as `<!--\ slide: example -->`. MarkdownPoint removes the backslash when it reads the file. The line then shows as the comment you meant.

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

Indent an item to nest it under the one above, as far as the text after that item's marker: two spaces under `- `, three under `1. `. A numbered list can start at any number of up to nine digits.

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

### Quotes and inline code

````markdown
<!-- slide: quote -->

<!-- text: title -->
Quotes and code==.==

<!-- text: body -->
> Plain text in, a point made beautifully out.

```js
const deck = "slides.md"
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

Its position, columns, row heights and style are in the .mdpoint file. A row is taller when its words need it. The table is as tall as its rows:

```json
[
  {
    "id": "prices",
    "type": "table",
    "frame": {"x": 120, "y": 380, "width": 1680},
    "inset": 24,
    "text-style": "body",
    "columns": [840, 420, 420],
    "rows": [140, 140, 140],
    "alternating-fills": true
  }
]
```

![The table slide, as MarkdownPoint draws it](images/words-table.png)

### Code blocks

A code block sits between two fences. A fence is three or more backticks or tildes. The closing fence uses the same character and is at least as long. The language after the opening fence colors the code. Its name can be in any case. `js` works as well as `javascript`. The [reference](reference/v1.md#markdown) lists every name. `{lines:true}` after the language numbers the lines.

### Where it differs from CommonMark

Bold and italic follow CommonMark, with one difference. CommonMark lets a run of `*` between punctuation and a letter open toward the letter but never close. MarkdownPoint lets such a run both open and close. So `**C++**17` reads as bold “C++” before “17”. `**Note:**This` reads as bold “Note:” before “This”. CommonMark shows the stars in both as written.

The same rule covers Chinese and Japanese, which run without spaces. `これは**「重要」**です` reads as bold “「重要」”. This matches the [CJK-friendly amendment](https://github.com/tats-u/markdown-cjk-friendly) to CommonMark.

The rule changes one rare spelling. `*(*foo*)*` reads as italic parentheses around a plain “foo”, where CommonMark nests one italic inside the other. A run of `_` keeps CommonMark's reading.

A word can change style once: `**Mark**down` reads back as a bold “Mark” before “down”. A word that changes style twice or more in a row, such as from bold to bold italic to italic, may save as asterisks that read back as plain asterisks.

### Unsupported Markdown

Images, HTML tags and footnotes are kept in the file as written. The slide shows them as plain text. Only `<br>` does something: it breaks the line in a text block. To put an image on a slide, add an image object in [the .mdpoint file](mdpoint-file.md).

An HTML comment inside a text or notes block is a note to yourself. It's kept in the file and never shown. A comment at the end of a slide belongs to the slide's last block. A table's or a chart's block holds only its table. A comment can't follow it.

## Editing by hand

MarkdownPoint can't read the file if it has one of these, among others:

- text, a comment or a `---` outside a text or notes block
- a comment after a table, or after a chart's table
- a marker written wrong, such as `<!-- Slide: intro -->`, or the same marker twice
- a section without its one `# Name` line
- `markdownpoint` in quotes, or a version other than 1
- a slide marker without its entry in the .mdpoint file, or an entry without its marker
- a code block that takes in a marker and never closes
- one of MarkdownPoint's own frontmatter keys not written as `key: value` on one line

A comment line is a marker written wrong when it holds a marker's word and at most one more word. Capitals don't matter. A closing slash and words after `-->` don't change it.

MarkdownPoint then shows the file, the line and the problem. It won't open the presentation. If it's already open, MarkdownPoint waits for the fix and never overwrites your file.

A table's column widths and row heights in the .mdpoint file follow the table's order. When you add or remove a column or a row by hand, add or remove its width or height at the same place. MarkdownPoint warns when a table has more widths or heights than columns or rows.

`mdpoint`, MarkdownPoint's command-line tool, checks a folder without the app. It reports each problem MarkdownPoint finds in the files. That includes a missing image, model or font. To install it, open MarkdownPoint's Settings, choose “Coding agents” and click “Install” under “Command line”. Then run `mdpoint validate --files` in the folder, or `mdpoint --deck <folder> --json validate --files` for JSON. Each issue has a `severity`, `error` or `warning`, a `code` and a `message`. Most also give the `path` of the key. Its `disposition` says what MarkdownPoint does: `fallback`, `skip-object`, `refuse-slide` or `refuse-document`. `mdpoint` exits with 1 when an issue is an error.

Saving keeps your formatting. The frontmatter, each slide and each block stay as you wrote them until you edit them in the app. An edited one is saved in MarkdownPoint's standard Markdown. `mdpoint format` writes both files in MarkdownPoint's spelling on purpose.
