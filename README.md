![The title slide, as MarkdownPoint draws it](images/first-deck-title.png)

# MarkdownPoint file format

MarkdownPoint is a presentation app for Mac and Windows: [markdownpoint.com](https://markdownpoint.com).

MarkdownPoint saves a presentation as a folder of plain text files. This repository describes those files. It is for people who edit them by hand and for programs that read or write them.

These lines in the .md file make the slide above:

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

Its layout, colors and type come from the .mdpoint file and the theme. The whole presentation is in [examples/first-deck](examples/first-deck).

## A presentation is a folder

```
My talk/
  slides.md              the text: slides, tables, speaker notes, slide order and sections
  presentation.mdpoint   everything else: objects, their positions and styles, steps and transitions
  media/                 images, videos, sounds and models
  generated/             what MarkdownPoint makes: images made with AI, and 3D text models. A record beside each image says how it was made.
  themes/<key>/          a theme: master.json, a JSON file per layout, and its own images and fonts.
                         The .mdpoint file names the one in use. Changing the theme keeps the earlier ones.
  versions/              saved versions: a numbered folder for each, and versions.json, which lists them
```

The folder's name is the presentation's. Rename the folder to rename the presentation. The two files are always named `slides.md` and `presentation.mdpoint`. Capital letters don't matter. A second copy of either, such as `slides 2.md` or a sync service's conflicted copy, stops the presentation from opening. MarkdownPoint tells you which files they are. Keep one of each, named as above, and the presentation opens. Anything else in the folder is yours. MarkdownPoint never copies it. A file reached through a symbolic link or an alias is never one of the presentation's files. Copy the file itself into the folder.

To start a presentation by hand, copy an example's whole folder. Its `themes` folder holds Basic. With MarkdownPoint running, `mdpoint create <folder>` makes a new presentation with Basic and opens it.

## Saved versions

File › Save Version… keeps the presentation as it is now. Each version is a folder in `versions/`, named by its number: `versions/12/` holds copies of `slides.md` and of `presentation.mdpoint` as they were saved. The copy of the .mdpoint file is named `presentation.json`, so a version's folder is never a presentation of its own. `versions/versions.json` lists each version's number, when it was saved and its name, if it was given one. A version keeps the slides, their text and their objects. It uses the presentation's media and shows its theme as the theme is now. Delete Theme refuses a theme that a saved version uses, and Delete Unused Media keeps a file that one uses. A file deleted from the Media list goes all the same, and older versions show it missing. While a version has only partly arrived, as a sync service brings it, no theme can be deleted and Delete Unused Media deletes nothing.

In git, commit `versions/` when the history should travel with the presentation. Leave it out when it shouldn't.

## Guides

- **[The .md file](md-file.md)**: start here. Frontmatter, sections, slides, text and speaker notes, and what to know before editing by hand.
- **[The .mdpoint file](mdpoint-file.md)**: slides, objects, colors and steps.
- **[Themes](themes.md)**: masters, layouts, color schemes and text styles.
- **[Reference, version 1](reference/v1.md)**: every key in the files and what it does.

## Format versions

Every file states its format version as a major and a minor in quotes: `"markdownpoint": "1.0"` in JSON and `markdownpoint: "1.0"` in the .md file's frontmatter. The quotes matter, since 1.10 and 1.1 are the same number.

MarkdownPoint 1.x opens any version 1.x file. A later minor only adds what an earlier MarkdownPoint can pass over. That one keeps what it doesn't know as the file has it, through every save, and tells you once that the presentation holds it. A save never lowers a file's version. A version 2.0 file needs MarkdownPoint 2. The format isn't final yet. Until it is, version 1.0 can still change.

## License

MIT. See [LICENSE](LICENSE). MarkdownPoint's source generates this repository. Each update replaces what is here. To report a mistake, open an issue in this repository.
