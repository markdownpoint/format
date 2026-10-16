![The title slide, as MarkdownPoint draws it](images/first-deck-title.png)

# MarkdownPoint file format

MarkdownPoint saves a presentation as a folder of plain text files. This repository describes those files. It is for people who edit them by hand and for programs that read or write them.

These lines in presentation.md make the slide above:

```markdown
<!-- slide: title -->

<!-- text: eyebrow -->
MarkdownPoint

<!-- text: title -->
File format==.==

<!-- text: body -->
Your slides are Markdown files you own.

<!-- notes -->
Speaker notes go here. Only the presenter sees them.
```

Its layout, colors and type come from presentation.json and the design. The whole presentation is in [examples/first-deck](examples/first-deck).

## A presentation is a folder

```
my-talk/
  presentation.md      the text: slides, tables, speaker notes, slide order and sections
  presentation.json    everything else: objects, their positions and styles, steps and transitions
  media/               images, videos, sounds and models
  generated/           images made with AI, each with the prompt and settings that made it
  designs/<id>/        the design: master.json, a JSON file per layout, and its own images and fonts
```

To start a presentation by hand, copy an example's whole folder. Its `designs` folder holds Basic. With MarkdownPoint running, `mdpoint create <folder>` makes a new presentation with Basic and opens it.

## Guides

- **[Introduction to presentation.md](presentation-md.md)**: start here. Frontmatter, sections, slides, text and speaker notes, and what to know before editing by hand.
- **[presentation.json](presentation-json.md)**: slides, objects, colors and steps.
- **[Designs](designs.md)**: masters, layouts, color schemes and text styles.
- **[Reference, version 1](reference/v1.md)**: every key in the files and what it does.

## Versions

Every file states its format version as the whole number 1: `"markdownpoint": 1` in JSON and `markdownpoint: 1` in presentation.md's frontmatter. A newer MarkdownPoint writes a higher number. MarkdownPoint refuses a file with a number higher than its own. The format isn't final yet. Until it is, version 1 can still change.

## License

MIT. See [LICENSE](LICENSE). MarkdownPoint's source generates this repository. Each update replaces what is here.
