# presentation.json

presentation.json has everything except the text: the design, each slide's layout, the objects on it, their positions and styles, and the steps that animate them.

![The shapes slide, as MarkdownPoint draws it](images/objects-shapes.png)

This slide's text is in presentation.md:

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
  "markdownpoint": "1.0",
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
            "card.opacity": [{"id": "card-shows", "time": 0.6, "value": 1, "easing": "ease-out"}]
          }
        }
      ]
    }
  }
}
```

MarkdownPoint writes each object on one line. It's expanded here for readability.

## Slides

`slides` lists each slide by the id of its marker in presentation.md. presentation.md also sets their order. `layout` is one of the design's layouts. `scheme` is one of its color schemes, if different from the layout's.

## Objects

`objects` lists the slide's objects from back to front. Each has an `id` and a `type`. `frame` is its position and size in canvas units, from the slide's top left. A canvas unit is one pixel of the slide at full size, 1920 by 1080 here.

A text object's text follows `<!-- text: id -->` in presentation.md, using the object's id.

The dot's fill, `text`, is a role in the slide's color scheme, so the dot changes color with the scheme. A hex color such as `"#FA7B26"` never changes.

A key you leave out uses its default. The card has no fill, so it uses a shape's default, `accent`. The [object table](reference/v1.md#object) lists every object key with the objects that use it, its values and its default.

## Steps

`initial` sets a property's value at the start of the slide. Here the card starts hidden.

`steps` are the slide's builds, in order. Each step starts on a click unless its `start` says otherwise. Its `curves` have keys for each object and property: the value a property reaches at a given time, in seconds. This step fades the card in over 0.6 seconds.

## Other tools' keys

A key MarkdownPoint doesn't know is an error. A key that starts with `x-` is for other tools. MarkdownPoint keeps it and ignores it.

```json
{"id": "card", "type": "shape", "shape": "rectangle", "frame": {"x": 120, "y": 360, "width": 720, "height": 420}, "x-reviewed": true}
```

`x-` keys can go at the top level, on a slide, a composition or an object, and in an override.
