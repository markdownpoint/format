---
markdownpoint: "1.0"
id: 9d98709f-0aac-407e-b6d3-56da6b8494aa
---

<!-- section: words -->
# Words

<!-- slide: marks -->

<!-- text: title -->
Marks, links and colors==.==

<!-- text: body -->
Words can be **bold**, *italic*, ==highlighted==, ~~struck through~~ or `code`.

[A link](https://markdownpoint.com) opens a webpage, and [these words]{color=accent} are in the accent color.

<!-- slide: lists -->

<!-- text: title -->
Lists==.==

<!-- text: body -->
- A bullet
  - A deeper bullet
- Another bullet

1. First
2. Second

<!-- slide: fields -->

<!-- text: title -->
Fields==.==

<!-- text: body -->
This is slide {{slide-number}} of {{slide-count}}, in section {{section-number}}, {{section-name}}.

<!-- slide: quote -->

<!-- text: title -->
Quotes and code==.==

<!-- text: body -->
> Plain text in, a point made beautifully out.

```js
const deck = "slides.md"
```

<!-- slide: table -->

<!-- text: title -->
Tables==.==

<!-- text: prices -->
| Plan | Seats | Price |
| --- | --- | --- |
| Solo | 1 | Free |
| Team | 10 | $8 |

<!-- slide: data -->

<!-- text: title -->
Tables and charts==.==

<!-- text: specs -->
| Size | 1920 × 1080 |
| --- | --- |
| Frame rate | 60 fps |
| Film | MP4 |

<!-- text: sales -->
| Quarter | North | South |
| --- | --- | --- |
| Q1 | 40 | 24 |
| Q2 | 65 | 48 |
| Q3 | 52 | 71 |
| Q4 | 90 | 60 |

<!-- text: visitors -->
| Visitors | Count |
| --- | --- |
| Total | 1280 |

<!-- notes -->
The table's first column is its header (`header-column`), and its first row is a row like the others (`header-row` set to `false`). The chart's `colors` give its two series the text and accent colors. The counter rolls its digits (`roll`). With a `progress` of 0.5, it shows the halfway value, 0640. Its unused leading zero has an opacity of 0.4 (`leading-zero-opacity`).

<!-- slide: rules -->

<!-- text: title -->
Rules and comments==.==

<!-- text: body -->
A rule runs across its text box.

---

<!-- Check these numbers before the talk. -->

<!--\ slide: draft -->

Neither comment above shows on the slide.

<!-- slide: type -->

<!-- text: title -->
Text boxes==.==

<!-- text: grows -->
This box has no height in the file. It grows to fit its words.

<!-- text: figures -->
1,250.00

980.50

75.25

<!-- text: outline -->
Outlined

<!-- notes -->
The first box has `auto-size` set to height. Its frame has no height. The figures have `tabular` set. Every digit has the same width, and the columns line up. The outlined word's letters have the background's color. Its `text-stroke` is the accent color, and its `text-stroke-weight` is 3 canvas units.
