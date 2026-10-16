# Designs

A design is a folder in the presentation's `designs` folder, named by its id. presentation.json selects it with `"design": "<id>"`. The examples here use Basic, MarkdownPoint's default design.

```
designs/c17f2a67-54b1-492b-8ab3-838711173ab2/
  master.json             size, fonts, color schemes, text styles and what every slide shows
  title.json              one file per layout, named by the layout's key
  content-with-header.json
  ...
  media/                  the design's own pictures
```

## Master

master.json has the design's settings. Its `objects` appear behind every slide's own. Basic's master has a footer and a slide number. Their text is fields:

```json
{"footer": "{{footer}}", "slide-number": "{{slide-number}}"}
```

## Color schemes

A scheme sets a color for each role. Objects use roles, such as `accent`, so they change color with the slide's scheme. This is Basic's butter scheme:

```json
{
  "appearance": "light",
  "background": "#FBE991",
  "text": "#17120F",
  "muted": "#524843",
  "accent": "{brand.ember}",
  "stroke": "#17120F1F"
}
```

`{brand.ember}` is one of the brand colors defined in `brand`.

## Text styles

A text object gets its type settings from a text style, such as `title` or `body`:

```json
{
  "font-family": "display",
  "font-size": 80,
  "font-weight": 760,
  "line-height": 1.05,
  "tracking": -0.025,
  "color": "text"
}
```

`font-family` is a font role. `fonts` sets the font family for each role.

## Layouts

A layout sets the objects a slide starts with. This is Basic's Title layout:

```json
{
  "markdownpoint": "1.0",
  "kind": "layout",
  "scheme": "apricot",
  "overrides": {"footer": null, "slide-number": null},
  "objects": [
    {
      "id": "title",
      "type": "text",
      "placeholder": true,
      "frame": {"x": 120, "y": 470, "width": 1560, "height": 310},
      "text-style": "display",
      "vertical-align": "bottom"
    },
    {
      "id": "body",
      "type": "text",
      "placeholder": true,
      "frame": {"x": 120, "y": 800, "width": 1560, "height": 130},
      "text-style": "body",
      "color": "muted"
    },
    {
      "id": "eyebrow",
      "type": "text",
      "placeholder": true,
      "frame": {"x": 120, "y": 112, "width": 1560, "height": 48},
      "text-style": "meta",
      "font-weight": 600,
      "vertical-align": "center",
      "tracking": 0.06,
      "case": "upper"
    }
  ],
  "text": {
    "title": "Presentation title",
    "body": "Presentation subtitle",
    "eyebrow": "Author and date"
  }
}
```

`"placeholder": true` marks an object that each slide fills with its own text. A slide on this layout writes it under `<!-- text: title -->`, `<!-- text: body -->` and `<!-- text: eyebrow -->`. `text` has each placeholder's sample text. `overrides` changes the master's objects on this layout, and `null` removes one.

![The title slide, as MarkdownPoint draws it](images/first-deck-title.png)

## Changing a design by hand

Every slide that uses a design shares it. A change to master.json affects every slide. A change to a layout affects every slide that uses it.

MarkdownPoint picks up design changes made outside the app while the presentation is open, unless it's shared. If a layout can't be read, its slides show empty and the Format inspector shows the problem. If the master can't be read, every slide shows empty.

The reference lists every key of [master.json](reference/v1.md#master) and [a layout](reference/v1.md#layout).
