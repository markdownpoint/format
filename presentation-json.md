# presentation.json

presentation.json holds everything but the words. It names the design, the layout of each slide, the objects on it, their places and styles, and the steps that animate them.

![The shapes slide, as MarkdownPoint draws it](images/objects-shapes.png)

This slide's words are in presentation.md:

```markdown
<!-- slide: shapes -->

<!-- text: heading -->
Objects and steps==.==

<!-- text: caption -->
The card fades in on the first click.

<!-- notes -->
A text, two shapes and a line with an arrow, drawn on the Blank layout. One step fades the card in.
```

Everything else is in presentation.json:

```json
{
  "markdownpoint": 1,
  "kind": "presentation",
  "document": "64ea077f-d2ca-4f8c-9015-61feb4de5cb8",
  "design": "c17f2a67-54b1-492b-8ab3-838711173ab2",
  "slides": {
    "shapes": {
      "layout": "blank",
      "scheme": "butter",
      "objects": [
        {
          "id": "heading",
          "type": "text",
          "frame": {"x": 120, "y": 110, "width": 1680, "height": 150},
          "text-style": "title"
        },
        {
          "id": "card",
          "type": "shape",
          "shape": "rectangle",
          "frame": {"x": 120, "y": 360, "width": 720, "height": 420},
          "corner-radius": 32
        },
        {
          "id": "dot",
          "type": "shape",
          "shape": "ellipse",
          "frame": {"x": 1320, "y": 420, "width": 300, "height": 300},
          "fill": "text"
        },
        {
          "id": "arrow",
          "type": "line",
          "start": {"x": 900, "y": 570},
          "end": {"x": 1260, "y": 570},
          "cap-end": "arrow",
          "stroke-weight": 8
        },
        {
          "id": "caption",
          "type": "text",
          "frame": {"x": 120, "y": 860, "width": 1680, "height": 80},
          "text-style": "body"
        }
      ],
      "initial": {"card.opacity": 0},
      "steps": [
        {
          "id": "fade-in",
          "name": "Fade in the card",
          "duration": 0.6,
          "curves": {
            "card.opacity": [{"id": "card-shows", "time": 0.6, "value": 1, "easing": "ease-in"}]
          }
        }
      ]
    }
  }
}
```

MarkdownPoint writes each object on one line. It's opened up here to read.

## Slides

`slides` holds each slide by the id its marker gives in presentation.md. Their order is presentation.md's. `layout` names one of the design's layouts. `scheme` picks one of its color schemes when the layout's own doesn't suit.

## Objects

`objects` lists the slide's own objects back to front. Each has an `id` and a `type`. Its `frame` is its place and size in canvas units, from the slide's top left. A canvas unit is a pixel of the slide at its size, 1920 by 1080 here.

A text object's words follow `<!-- text: id -->` in presentation.md, under the object's id.

The dot's fill, `text`, is a role of the slide's color scheme, so the dot changes color with the scheme. A hex color such as `"#FA7B26"` stays as it is.

A key left out takes its default. The card leaves out its fill, so it takes a shape's default, `accent`. The [object table](reference/v1.md#object) lists every object key with the objects that take it, its values and its default.

## Steps

`initial` sets the value a property starts the slide with. Here the card starts hidden.

`steps` are the slide's builds, in order. Each step starts on a click unless its `start` says otherwise. Its `curves` hold keys for each object and property: the value a property reaches at a time, in seconds. This step fades the card in over 0.6 seconds.

## Other tools' keys

A key MarkdownPoint doesn't read is an error. A key that starts with `x-` belongs to another tool. MarkdownPoint keeps it and never reads it.

```json
{"id": "card", "type": "shape", "shape": "rectangle", "frame": {"x": 120, "y": 360, "width": 720, "height": 420}, "x-reviewed": true}
```

`x-` keys go at the top level, on a slide, on a composition, on an object and in an override.
