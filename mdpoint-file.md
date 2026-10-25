# The .mdpoint file

The .mdpoint file has everything except the text: the theme, each slide's layout, the objects on it, their positions and styles, and the steps that animate them. It's always named `presentation.mdpoint`. The presentation's name is its folder's. Rename the folder to rename the presentation.

![The shapes slide, as MarkdownPoint draws it](images/objects-shapes.png)

This slide's text is in the .md file:

```markdown
<!-- slide: shapes -->

<!-- text: heading -->
Objects and steps==.==

<!-- text: caption -->
The card fades in on the first click.

<!-- notes -->
Two texts, two shapes and a line with an arrow, drawn on the Blank layout. One step fades the card in.
```

Everything else is in the .mdpoint file:

```json
{
  "markdownpoint": "1.0",
  "kind": "presentation",
  "id": "64ea077f-d2ca-4f8c-9015-61feb4de5cb8",
  "theme": "basic",
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
            "card.opacity": [
              {"id": "card-shows", "time": 0.6, "value": 1, "interpolation": "ease-out"}
            ]
          }
        }
      ]
    }
  }
}
```

MarkdownPoint writes each key of an object on a line of its own. Here a short object stands on one line to save room. Objects, and a slide's overrides of its layout's objects, have a blank line between them. So git merges two people's edits to different objects without a conflict. Two edits to one key conflict, as do edits to keys on neighboring lines.

A save writes the .mdpoint file in MarkdownPoint's own spelling whenever its values change. Keys stand in a set order, a value equal to its default is left out, and numbers are rounded to six decimals. A save that changes only words leaves the .mdpoint file as it was.

## Slides

`slides` lists each slide by the id of its marker in the .md file. Their order comes from the .md file. `layout` is one of the theme's layouts. `scheme` names one of the theme's color schemes. Without it, the slide uses its layout's.

## Objects

`objects` lists the slide's objects from back to front. Each has an `id` and a `type`. `frame` is its position and size in canvas units, from the slide's top left. A canvas unit is one pixel of the slide at full size. The theme sets that size: 1920 by 1080 here.

A text object's text is in the .md file, under `<!-- text: id -->` with the object's id. A shape's text goes there the same way.

The dot's fill, `text`, is a role in the slide's color scheme, so the dot changes color with the scheme. A hex color such as `"#FA7B26"` stays the same in every scheme.

A key you leave out uses its default. The card has no fill, so it uses a shape's default, `accent`. The [object table](reference/v1.md#object) lists every object key with the objects that use it, whether they must have it, its values and its default.

## Steps

`initial` sets a property's value at the start of the slide, as `object.property`. Here the card starts hidden.

`steps` are the slide's builds, in order. A step starts on a click. Its `start` can say `with` the previous step or `after` it instead. On the first step, `after` starts it once the slide's transition ends. A scroll is different: it plays the next slide's first step as it moves, however that step starts.

`curves` lists each animated property as `object.property`. Each has keyframes: the value the property reaches and its `time`, in seconds of the step. A step has no delay of its own. A keyframe's `start` delays its move. This step fades the card in over 0.6 seconds.

Motion ends unless it repeats. Four kinds can repeat for as long as the slide shows: a keyframe's `loop`, a wave from `oscillate`, a step's `wait-loop` and an Action strip's `loop`. The camera's [drift](reference/v1.md#drift) doesn't repeat, so it must state its `duration`.

## Keys MarkdownPoint doesn't read

A key MarkdownPoint doesn't read is an error, most likely a typo. A save could not keep it, so the app refuses the file rather than lose the key. A file a newer MarkdownPoint wrote, of a later minor such as version 1.3, is different. Its new keys are kept as written, through every save.

To keep your own data with a presentation, write it in the .md file's frontmatter. MarkdownPoint keeps keys there that it doesn't read.
