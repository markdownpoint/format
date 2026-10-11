# Designs

A design is a folder in the presentation's `designs` folder, named by its id. presentation.json picks it with `"design": "<id>"`. The examples here use Basic, the design MarkdownPoint starts a presentation with.

```
designs/c17f2a67-54b1-492b-8ab3-838711173ab2/
  master.json             size, fonts, color schemes, text styles and what every slide shows
  title.json              one file per layout, named by the layout's key
  content-with-header.json
  ...
  media/                  the design's own pictures
```

## Master

master.json holds the design's settings. Every slide shows its `objects` behind its own. Basic's master has a footer and a slide number. Their words are fields:

```json
{"footer": "{{footer}}", "slide-number": "{{slide-number}}"}
```

## Color schemes

A scheme gives a color to each role. A slide's objects name roles, such as `accent`, so they change color with the slide's scheme. This is Basic's butter scheme:

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

`{brand.ember}` is one of the brand's own colors, which `brand` names.

## Text styles

A text object takes its type from a style, such as `title` or `body`:

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

`font-family` names a font role, which `fonts` gives a family.

## Layouts

A layout places the objects a slide starts with. This is Basic's Title layout:

```json
{
  "markdownpoint": 1,
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

`"placeholder": true` marks an object a slide fills with its own words. A slide on this layout writes them under `<!-- text: title -->`, `<!-- text: body -->` and `<!-- text: eyebrow -->`. `text` holds a placeholder's sample words. `overrides` changes the master's objects on this layout, and `null` takes one off.

![The title slide, as MarkdownPoint draws it](images/first-deck-title.png)

## Changing a design by hand

A design is shared by every slide that uses it. A change to master.json reaches every slide. A change to a layout reaches every slide on it.

MarkdownPoint picks up a design changed outside it while the presentation is open, unless the presentation is shared. A layout that doesn't read leaves its slides empty, and the Format inspector names the problem. A master that doesn't read leaves every slide empty.

The reference lists every key of [master.json](reference/v1.md#master) and [a layout](reference/v1.md#layout).
