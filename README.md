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
  presentation.json    everything else: objects, their places and styles, steps and transitions
  media/               pictures, movies and sounds
  designs/<id>/        the design: master.json and a JSON file per layout
```

## Guides

- **[Introduction to presentation.md](presentation-md.md)**: start here. Frontmatter, sections, slides, text and speaker notes, and what to know before editing by hand.
- **[presentation.json](presentation-json.md)**: slides, objects, colors and steps.
- **[Designs](designs.md)**: masters, layouts, color schemes and text styles.
- **[Reference, version 1](reference/v1.md)**: every key in the files and what it does.

## Versions

Both files state their format version: `markdownpoint: 1`. Earlier versions' references stay in [reference](reference). The format is not final yet. Until MarkdownPoint's first release, version 1 still changes in place.

## License

MIT. See [LICENSE](LICENSE). This repository is generated from MarkdownPoint's source, so changes made here are overwritten.
