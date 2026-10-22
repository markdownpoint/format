# Designs

A design is a folder in the presentation's `designs` folder, named by its id. The .mdpoint file selects it with `"design": "<id>"`. The examples here use Basic, MarkdownPoint's default design.

```
designs/c17f2a67-54b1-492b-8ab3-838711173ab2/
  master.json             size, fonts, color schemes, text styles and what every slide shows
  title.json              one file per layout, named by the layout's key
  content-with-header.json
  ...
  media/                  the design's own images
```

## Master

master.json has the design's settings. Its `objects` appear behind every slide's own. Basic's master has a footer and a hidden slide number. Both show fields:

```json
{"footer": "{{footer}}", "slide-number": "{{slide-number}}"}
```

## Color schemes

A scheme sets a color for each role. Objects use roles, such as `accent`, so they change color with the slide's scheme. Every design has a `light` and a `dark` scheme. Another scheme, such as Basic's butter, states its `appearance`, light or dark:

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

`{brand.ember}` refers to `ember`, one of the colors under `brand.colors`. A scheme must set `background`. A role it leaves out takes the default for its appearance, which the [reference](reference/v1.md#scheme) lists.

## Text styles

A text object gets its type settings from one of the design's text styles. A design defines every style its layouts and slides name, since no name is built in. This is Basic's `title`:

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

`font-family` is a font role. `fonts` sets the family for each role. The reference lists the families MarkdownPoint bundles, under [fonts](reference/v1.md#fonts). A generic family, such as `serif` or `system-ui`, needs no file. Any other family it doesn't bundle comes with the design. `font-files` lists its `.eot` files, one per face. The app keeps them in the design's `fonts` folder.

## Layouts

A layout sets the objects a slide starts with. This is Basic's Title layout:

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

`"placeholder": true` marks an object that each slide fills with its own text. A slide on this layout gives each its text under `<!-- text: title -->`, `<!-- text: body -->` and `<!-- text: eyebrow -->`. `text` has each placeholder's sample text. It shows only while choosing a layout or editing the design. A placeholder a slide leaves without text shows nothing when presenting. `overrides` changes master objects on this layout. `null` removes one.

![The title slide, as MarkdownPoint draws it](images/first-deck-title.png)

## Changing a design by hand

A change to master.json affects every slide. A change to a layout affects every slide that uses it.

MarkdownPoint picks up design changes made outside the app while the presentation is open, unless the presentation is shared for editing together. If a layout can't be read, each slide on it shows “The slides can’t be shown”. The problem is listed under “Issues” in the inspector. If the master can't be read, every slide shows it.

The reference lists every key of [master.json](reference/v1.md#master) and [a layout](reference/v1.md#layout).
