# MarkdownPoint agent contract, version 1

The `mdpoint` command and its MCP server use the `markdownpoint/agent/1` contract to talk to the running app. The app's own Agent uses the same methods. The app describes the contract itself.

## The contract

`capabilities` is a compact index of the app's contract: the methods it
serves, its limits, and its interpolations.
The app answers it with any number of presentations open, or none. Its `methods`
are `{ app, presentation }`: the methods the app answers on its own with no
presentation open, and the methods it answers for a presentation.
`mdpoint --help` lists the tools and the CLI's own commands, such as `validate`
and `format`. A tool's own `--help` gives its description and its request's
fields. The description says what the tool does. The fields are the ones its
input schema gives. MCP lists each tool with both.

The contract is `markdownpoint/agent/1`, and the app serves one contract at a
time. From MarkdownPoint 1.0 on, the contract only grows. A new method, field,
or value is added to `/1`, and a rename, a removal, or a change of meaning
comes in `/2`. Most methods are grouped by what they act on into plural collections, such as
`presentations`, `files`, and `slides`. `capabilities`, `reference`, and `command`
stand alone. `reference` reads the format reference or one of the app's guides.
The contract names its own facts in camelCase (`slides.measureContrast`,
`objectIds`, `slideId`). A field that contains a file key's value is
spelled as the file spells the key (`text-styles`, `unit-scale`, `aspect-ratio`). MCP's
tools are named after their methods in snake case (`files_read`). Each tool is a CLI
command by that name too. It takes its request as JSON:
`mdpoint files_read '{"slideIds":["intro"]}'`, and prints its result as one line
of JSON on stdout. `3D` is one word: `slides.measure3D`
is `slides_measure_3d`. The contract's codes, an issue's disposition among them,
and the format's own keys and keyword values are kebab-case
(`unknown-target`, `text-overflow`, `chart-type`). Order has no
meaning in the contract's schemas. Most fields, branches, and values are listed
alphabetically. The order is the same on every build.

A request names its presentation in `presentation`: its folder or a file at
its root. Without it, a CLI request goes to the current folder when that folder is a
presentation. Otherwise, and from MCP, it goes to the focused presentation, then
to the only open one. When several presentations are open and none is focused,
it fails with `unknown-target`. Name one with a folder from `windows_list`. Configure an MCP client to run `mdpoint mcp` over stdio.
The CLI and MCP use the same live app API and validation. MCP
lists each tool with a title and hints. The hints say whether the tool only
reads, whether it may remove or replace what is there, whether calling it again
changes nothing more, and whether it reaches outside MarkdownPoint. Outside
means a paid service, an AI provider, another application, or the web. From
MCP 2025-06-18 on, a tool whose result holds no picture also has an output
schema.

## Changing a presentation

An agent changes a presentation by editing its files. `files_read` reads them a
part at a time: the deck's part, or the part of each slide and composition it
names. With `theme: true`, it reads the theme's files. Its description says what
each part holds. A page's part comes with what the app works out of it, such as
where each object is drawn. `files_edit` replaces an exact `old` text in a part
with `new`. Each `old` occurs exactly once in its part as the request's edits
before it leave the part. An empty `old` writes a part that is empty now. Its
description says how to add, move, or remove a page. One request is one Undo
step. When the app refuses it with `refused`, `validation-failed`, or
`conflict`, nothing changed. Its answer gives
each part it touched as the file now spells it, the `issues` the request
brought in with every `text-overflow` and `text-hidden` warning standing on the
slides it edited, and those it `cleared`. Take the next `old` from there. A tool also
reads its request from a file, as in `mdpoint files_edit edits.json`, or from
stdin with `-`.

An edit or a command changes the presentation in its window. MarkdownPoint
saves it as it saves the author's own edits. `files_read` reads the window's
presentation. `validate` and `format` read the files on disk. They see an edit
once it is saved.

`command` runs one of the app's own commands by its `id`. Each is one Undo
step. Among them are “Change layout”, “Reset to layout”, “Import 3D model…”,
the Arrange menu's commands, and one that brings a font from this computer into
the theme. The author takes back a request with Edit > Undo. To take back your
own change, edit the files again.

## Errors

A failure has a code and a message. With `--json`, a failed command prints one
line on stdout, `{"error": {"code": …, "message": …}}`, and nothing on stderr.
Without it, the error prints on stderr. Its first line starts with its code,
such as `refused:`.
A failed MCP tool answers with `isError` and the same `{"error": …}` as its
text. That record also includes what a caller needs to read beside its code: the
`issues` of a `validation-failed` from `files_edit` or `files_read`, the `edit` of a `refused` request
whose `old` isn't empty and its part doesn't hold once, or the ids of a conflict's `slideIds`
and `compositionIds`. That `edit` is `{ index, found }`. `index` counts its
place in `edits` from 0. `found` is how many times its part holds its `old`. A check's
findings are its result, whether it passes or fails. `validate --json` prints
`{ issues }` and exits 1 when one of them is an error. `format --check --json`
prints `{ changed }`, the files that would change, changes none, and exits 1
when it lists any. These two are the CLI's checks. An error record means the
check couldn't run. Without `--check`, `format --json` prints the files it
changed in `changed`. Either way, it names each as the folder spells it.
`format` writes only the presentation's two files. It refuses a presentation
that doesn't read, one whose theme doesn't read, one in which laying out its
slides finds an error, or one that changed while it was formatted. MCP's own errors are JSON-RPC errors: an unknown
tool, or arguments that aren't an object. The command exits 0 when it
succeeds or a check passes, 1 when a check finds what fails it, and 2 when an
error stops it.

| Code | What happened | What to do |
|---|---|---|
| `usage` | The command line is wrong (CLI only). | Read the command's `--help`. |
| `output-failed` | The command made the picture but couldn't write it where `--output` names (CLI only). The error's `output` names that file, and its `result` is the method's answer with the picture in it. | Save the picture from `result.image`: its `data` in base64, of the type `mimeType` names. Running the command again makes it again. A paid method spends again. |
| `refused` | The app or the command won't do what was asked. The cause is in the request, or in what it asks of this presentation, theme, or file. | Read the message, then change the request. |
| `unknown-method` | This MarkdownPoint has no method with that name. Either it's older than the contract you read, or the name is wrong. | Read `capabilities` for the methods it serves. |
| `validation-failed` | Errors stopped the request. From `files_edit`, `issues` lists what the edits would bring in, their errors among them. A `files_edit` call that edits the presentation's parts stops only on the errors its description names. It lands with any other error its edits bring in, such as a slide's own object under an id the slide inherits. Its answer's `issues` give that error. From `files_read`, the files have never read, and `issues` gives the reader's errors. Another request stops on the presentation's errors that keep it from its work. `files_read` lists them. | Fix those errors. Files that have never read take no `files_edit`. Fix them on disk, then read them again. |
| `unknown-target` | The presentation doesn't exist or no window shows it, or there's no such slide, step, object, theme, layout, or action of Meshy's library. | Read the state again to get the ids. `windows_list` names the presentations that are open. |
| `conflict` | The author or a collaborator changed what the edits change while they were taken in, the theme's files changed meanwhile, or the slide or theme a command acts on changed while it ran. From `files_edit`, `slideIds` and `compositionIds` give the slides and compositions where it happened. A conflict only in the deck's part or the theme names neither. | Read the deck's part again, then the parts of those pages it still lists, or read the theme again. Then retry. |
| `unavailable` | MarkdownPoint can't be reached or the connection closed. A service it calls isn't answering, such as Meshy, an image provider or the collaboration service. Or the presentation's files can't be read or written right now. The message names which. | When MarkdownPoint isn't running, start it: `mdpoint` doesn't. Otherwise, try again later. After a closed connection, read the state before you retry a change. |
| `timeout` | MarkdownPoint didn't take the connection within 30 seconds, or didn't answer within `MARKDOWNPOINT_CALL_TIMEOUT`. | A connection it didn't take sent nothing: check that it's running, then try again. A request it didn't answer may still take effect. Read the state before you retry a change. |
| `too-large` | The request is too large for one call: its size or a range's samples. | Send less at once or lower `sampleRate`. |
| `busy` | Something that can't run at the same time is in progress. It might be another movie export, the presentation opening or closing, another device's turn to edit it, or your earlier requests to Meshy. | Try again when it's done. |
| `internal` | Something went wrong inside MarkdownPoint or the command. | Report it with the message. |

`MARKDOWNPOINT_CALL_TIMEOUT` sets one timeout in seconds for every answer the
CLI waits for. It takes a number above 0 and at most 2,147,483.647, about 25
days. It counts from when the CLI sends the request. Without it, or with any
other value, a call waits until the app answers or the connection closes. A
connection the app doesn't take within 30 seconds times out either way.
An export or a model build takes as long as its work. The CLI connects to
the app's endpoint: a socket on a Mac and a named pipe on Windows. It
looks for the endpoint in `--endpoint`, then `MARKDOWNPOINT_ENDPOINT`, then in
the app's state folder. The app records it there. The state folder is
`MARKDOWNPOINT_STATE_DIR` when that is set. `mdpoint --help` lists them.

### Issues

An issue is a problem that `validate` and `files_read` find in the
files. It has a `severity` (`error` or `warning`), a `source`, a `code`, a
`message` in English, and a `disposition`: what MarkdownPoint does about it.
With `show-presentation`, the presentation is shown. Whatever can't be used
shows a default or nothing. Each message says which. With `skip-object`,
the object is left out. With `refuse-slide`, the slide is refused. With
`refuse-presentation`, the whole presentation is refused.

An issue about something written in a file has a `location`: `file` names
the file and `fileScope` the folder it's in. `presentation` is the
presentation's folder, and `theme` is its theme's. `line` and `column` say
where in the file the problem is, counted from 1, when they're known. `path`
selects the value the issue is about, one key after another from the root of
the file `location` names. The presentation's two files read as one record.
Its keys are the `.mdpoint` file's, and the `.md` file's frontmatter and
sections are under `frontmatter` and `sections`. A key of ASCII letters, digits, `_`, and `-` that starts with
a letter or `_` follows a dot. A list item is its index in brackets, and any
other key is a JSON string in brackets, as in `slides.intro.objects[2]` and
`slides.intro.initial["title.opacity"]`. An issue with a `path` always has a
`location`. Unless its source's own rules below say otherwise, an issue at a
value in a slide's, a composition's, or an object's record names them in
`slideId`, `compositionId`, and `objectId` once their ids can be read. The other
fields depend on its `source`:

- `mdpoint`: `location` names the `.mdpoint` file. Once an object's id can
  be read, `path` names the object by its id, as in
  `slides.intro.objects.title.opacity`. Once the slide is read, a step is
  named by its id, as in
  `slides.intro.steps.reveal.curves["title.opacity"].keyframes.fade.value`.
  In it, the curve's name is one key. A keyframe and a cue are always named
  by their ids. A warning about a step's own key is at that key, as in
  `slides.intro.steps.reveal.repeat-count`. One about where a loop starts is
  at `loop-start`, or at `loop` when the loop starts at 0. A composition is
  under `compositions`. Once a control's id can be read, the control is named
  by its id, as in `compositions.pulse.controls.label`. An `invalid-json` or
  `merge-conflict` issue has no `path`, and its `location` has the line when
  it's known.
- `md`: `location` names the `.md` file, and most issues have its line.
  `path` names the frontmatter key or the section the issue is about, such as
  `frontmatter.footer` or `sections.intro`. For an issue in a slide's words,
  it names the slide, the composition, the object, or the control in the
  presentation's record, such as `slides.intro.objects.title`. An instance's
  text block, such as `<!-- text: card.label -->`, is named as its value for
  that control would be: `slides.intro.objects.card.control-values.label`.
- `folder`: the two files disagree, or the folder can't be read. `path`
  selects the record in the presentation's record. `location` names the line
  in the `.md` file when the issue has one, and otherwise the `.mdpoint` file.
  An issue about the folder as a whole has neither.
- `theme`: an issue written in one of the theme's files names that file in
  `location`. Its `file` is the path in the theme's folder, such as
  `master.json` or `title.json`. `path` selects the value in that file. A file
  that isn't valid JSON gives its line and column. A warning about an object
  that a layout or the master gives its slides, or about their background,
  comes once for each of those slides where it holds. Its `slideId` names the slide,
  and its `path` the value, such as `overrides.logo.opacity`. Other than a
  `file` issue, an issue whose `location` is in the theme's folder is the
  theme's. It has `key` where a `slide` issue would. A layout's own object, its override of the master's,
  and its background are in the layout's file. The master's object and its
  background are in `master.json`. Words that the theme gives its own object
  are under `text` in its file, such as `text.summary`. A slide's override
  that is ignored leaves the inherited value showing. An issue about that
  value is at the inherited value. The override's own issue is at the
  override in the `.mdpoint` file. A scheme that a
  layout names and the master lacks comes once for each slide on the layout
  too. Its `path` is `scheme`. A file that a master or layout names and the
  theme's folder lacks is at each key that names it, as in
  `objects.logo.file`, and each slide that shows it has the file's own issue
  too. A theme font that no file brings is at its role in `master.json`, as in
  `fonts.display`. When only the slides that use a layout are refused,
  `slideId` names the slide. A theme's
  file that doesn't read is reported for its first fault only. The issue's
  code is the one the same fault has in a `.mdpoint` file, such as
  `unknown-key`, `missing-key`, or `invalid-json`. Such a fault in
  `master.json` refuses the whole presentation. Such a fault in a layout's
  file gives one issue for each slide on that layout. Each of them has the slide's `slideId` and
  that file's `location`. A layout that no slide uses gives its fault in one
  warning. A layout
  the theme lacks, and a slide's own scheme the master lacks, are at the
  slide's `layout` or `scheme` in the `.mdpoint` file. `objectId` is added
  when the issue is about one of the master's or a layout's objects. An issue about the
  whole theme, such as a missing theme that the presentation names, has none
  of these fields.
- `file`: the issue's own `file` is the path of the file it is about. It is a
  path in the folder that the issue's own `fileScope` names. `presentation` is the presentation's
  folder, and `theme` is its theme's. `cache` is the folder on this computer
  where MarkdownPoint saves the models it builds from a text's letters.
  `bundled` is the fonts that ship with MarkdownPoint. The issue comes once
  for each place where a key names the file. `slideId`, `objectId`, and
  `key` name the place, and `key` is the key that names the file there, such
  as `file`, `background.image.file`, or `soundtrack.file`. `path` and
  `location` say where that key's value is written, as in
  `slides.intro.objects.photo.file` in the `.mdpoint` file. A theme picture
  with one file per scheme is at the file for the slide's scheme, as in
  `objects.logo.file.dark` in `master.json`. A theme picture comes once for
  each slide that shows it. Only `validate` finds a missing generation record.
  It is at the `id` that names it, as in
  `slides.intro.objects.photo.generation.id`. A theme font's
  file comes once. It is at its entry in `master.json`'s `font-files`, as in
  `font-files.Lato[0]`. A file that a member of a composition's instance or
  a repeater's copy shows is at the key the record that draws it writes. A font that ships with MarkdownPoint has no
  `path`. No key names it, and its issue comes once.
- `slide`: `slideId`, and `objectId` or `objectIds`. An issue about the
  slide's background or its transition names no object. Its `key` is the key,
  such as `background.fill`, and `path` and `location` say where its value is
  written. When the issue is about
  one of the object's keys, such as `text-style`, `key` names it, and `path`
  and `location` say where its value is written. The slide's own object and
  its override of an object it inherits are in the `.mdpoint` file, as in
  `slides.intro.overrides.logo.text-style`. An issue in a slide's words, such
  as a link, a field name that no field has, or a color role that a word
  names, is in the `.md` file. Its `path` names the words as the `md` rules
  name them. An issue about a value the object takes from its text style, a
  field with no value, or a member that a composition's instance draws has
  neither `path` nor `location`. A member's issue in the words its instance
  gives has none either. A link in those words is the instance's issue, and
  its `path` names them. An issue whose `location` is in the theme's folder is
  a `theme` issue instead.
- `render`, for what drawing a slide finds: `slideId`, and `objectId` or
  `objectIds`. `text-overflow-unchecked` and a `render-failed` issue from
  drawing have no `slideId`, `objectId`, or `objectIds`. `validate`
  doesn't measure words: `text-overflow`, `text-hidden`, and
  `text-overflow-unchecked` come only from the app.

An issue about one object names it by `objectId`. An issue about several
objects names them by `objectIds`, back to front as the slide paints them. No
issue has both.

A refused value of a key with a code of its own has that code, such as
`invalid-layout` for a slide's `layout`, `invalid-locale` for the
presentation's `locale`, or `invalid-icon` for an icon's `icon`. A refused
value of any other key, such as `countdown` or an object's `opacity`, has
`invalid-value`. An override's value that no object can take has
`invalid-override-value`, whatever its key. Either way, `path` names the key.
In a JSON file of a version MarkdownPoint knows, a key it doesn't read has
`unknown-key`. The frontmatter keeps a key it doesn't read with no issue. A record
that lacks a key it needs has `missing-key`. A record that lacks a key one of
its other keys needs has `missing-key` too, such as a group with `auto-layout`
and no `frame`. An object that lacks a key it draws without, an icon's `icon`,
a video's `file`, or a shape's `shape-type`, has `missing-key` as a warning,
unless a binding names the key. For these codes, `path` names that key. `invalid-steps`,
`invalid-initial`, and `invalid-comments` are only for a fault of the list or
record as a whole. A step that isn't a record, or that shares another step's
id, has `invalid-steps`. A value refused inside them has `invalid-value` at its
own key, whatever its key. `invalid-control-value` is only for a control's `default` or an
instance's value for a control: in `control-values`, or a text control's words
in the instance's text block in the `.md` file. A value a collaborator set that
MarkdownPoint can't read has the code and `path` the same value in the file
would have. It is a warning, since the save leaves the value out, and its
message says so.

When MarkdownPoint reads the presentation's two files, it lists at most
1,000 issues. The first error it finds is listed even past them. When it
leaves issues out, an `issue-count-limit` warning from `folder` says how
many. The warning has no `path`. Issues found later, such as a slide's or
a layout's, are listed after the read's issues. None of them is left out.

The codes an issue can have:

| What | Codes |
|---|---|
| A file, a record, or a value that isn't valid | `invalid-3d-parent`, `invalid-binding`, `invalid-chart-colors`, `invalid-comments`, `invalid-composition`, `invalid-composition-id`, `invalid-composition-object`, `invalid-compositions`, `invalid-constraint-target`, `invalid-control-value`, `invalid-file-path`, `invalid-font`, `invalid-format-version`, `invalid-frame`, `invalid-frontmatter`, `invalid-generation`, `invalid-group-parent`, `invalid-icon`, `invalid-image-style`, `invalid-initial`, `invalid-json`, `invalid-json-root`, `invalid-layout`, `invalid-line-end`, `invalid-link`, `invalid-locale`, `invalid-marker`, `invalid-marker-id`, `invalid-media`, `invalid-object-id`, `invalid-object-type`, `invalid-objects`, `invalid-override-value`, `invalid-paragraph-id`, `invalid-parent-inverse`, `invalid-parent-node`, `invalid-placeholder`, `invalid-prompt`, `invalid-restyle`, `invalid-scheme`, `invalid-section-heading`, `invalid-slide-id`, `invalid-slides`, `invalid-steps`, `invalid-table`, `invalid-table-lengths`, `invalid-theme-file`, `invalid-theme-key`, `invalid-type`, `invalid-uuid`, `invalid-value`, `invalid-web-video` |
| Something needed that is missing | `missing-alt`, `missing-composition`, `missing-composition-marker`, `missing-control-block`, `missing-font`, `missing-frontmatter`, `missing-generation`, `missing-key`, `missing-marker-line-break`, `missing-media`, `missing-slide`, `missing-slide-marker`, `missing-text`, `missing-text-block`, `missing-theme-master` |
| Something given twice | `duplicate-composition-id`, `duplicate-composition-marker`, `duplicate-control-block`, `duplicate-notes-block`, `duplicate-object-id`, `duplicate-paragraph-id`, `duplicate-section-marker`, `duplicate-slide-marker`, `duplicate-text-block` |
| A name that nothing defines | `unknown-action`, `unknown-brand-color`, `unknown-category`, `unknown-color-role`, `unknown-composition`, `unknown-constraint-node`, `unknown-control`, `unknown-field`, `unknown-key`, `unknown-layout`, `unknown-link-slide`, `unknown-object`, `unknown-paragraph`, `unknown-parent-node`, `unknown-scheme`, `unknown-text-style` |
| Something that can't be read, resolved, matched, or played | `camera-spring-unsettled`, `composition-length-limit`, `issue-count-limit`, `loop-start-past-end`, `loops-misaligned`, `render-failed`, `repeat-count-limit`, `repeat-shares-click`, `stride-with-loop`, `table-cell-limit`, `unclosed-code-block`, `unclosed-frontmatter`, `unreadable-files`, `unreadable-themes`, `unresolved-field`, `unresolved-line-end`, `unsupported-date-locale` |
| Two parts that disagree, or something in a place where it isn't allowed | `clashing-curves`, `clashing-keyframes`, `clashing-motion-paths`, `file-spelling-mismatch`, `id-mismatch`, `inherited-id-collision`, `marker-outside-slide`, `marker-type-mismatch`, `media-type-mismatch`, `merge-conflict`, `slide-outside-section`, `text-outside-block` |
| Content written by a theme, a newer version, or another program, or left by an interrupted layout move | `generation-in-theme`, `instance-in-theme`, `interrupted-layout-move`, `newer-major-version`, `newer-minor-content`, `rounded-number`, `text-model-in-theme` |
| Objects that a slide can't contain together | `3d-parent-cycle`, `aim-cycle`, `composition-cycle`, `group-depth-limit`, `group-order`, `group-parent-cycle`, `object-count-limit` |
| A key or a value that its object can't have there | `bound-value`, `camera-outside-master`, `color-needs-svg`, `delivery-with-text-path`, `drift-with-scroll`, `effect-size-limit`, `extra-table-lengths`, `extrusion-with-text-path`, `ignored-animation`, `ignored-animation-value`, `ignored-control-block`, `ignored-key`, `ignored-override`, `ignored-override-value`, `ignored-text-block`, `link-letters-out-of-place`, `mask-without-group`, `position-animation-in-auto-layout`, `reserved-camera-id`, `reserved-world-id`, `text-value-in-mdpoint`, `world-outside-master` |
| What the rendered slide shows | `image-enlarged`, `low-text-contrast`, `text-hidden`, `text-overflow`, `text-overflow-unchecked` |
