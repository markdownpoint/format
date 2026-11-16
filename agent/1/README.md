# MarkdownPoint agent contract, version 1

The `mdpoint` command and its MCP server use the `markdownpoint/agent/1` contract to talk to the running app. The app's own Agent uses the same methods. The app describes the contract itself. `mdpoint capabilities` lists its methods and limits. `mdpoint mcp` serves the tools to an MCP client. Each tool's input schema gives its request's fields, and `mdpoint help <tool>` prints them. From MCP 2025-06-18 on, each tool whose result contains no picture also has an output schema.

## The contract

`capabilities` is a compact index of the app's contract: the methods it
serves, its limits, and its interpolations.
The app answers it with any number of presentations open, or none. Its `methods`
are `{ app, presentation }`: the methods the app answers on its own with no
presentation open, and the methods it answers for a presentation. Each tool's
input schema gives its request's fields. `mdpoint help files_edit` prints them
for one tool, and MCP lists each tool with its own.

The contract is `markdownpoint/agent/1`, and the app serves one contract at a
time. From MarkdownPoint 1.0 on, the contract only grows. A new method, field,
or value is added to `/1`, and a rename, a removal, or a change of meaning
comes in `/2`. Methods are grouped by what they act on into plural collections, such as
`presentations`, `files`, and `slides`. There is also an action group, `history`.
The contract names its own facts in camelCase (`slides.measureContrast`,
`themeRevision`, `slideId`). A field that contains a file key's value is
spelled as the file spells the key (`text-styles`, `unit-scale`, `aspect-ratio`). MCP's
tools are named after their methods in snake case (`files_read`). Each tool is a CLI
command by that name too. It takes its request as JSON:
`mdpoint files_read '{"slideIds":["intro"]}'`. `3D` is one word: `slides.measure3D`
is `slides_measure_3d`. The contract's codes, an issue's disposition among them,
and the file's keys and values are kebab-case
(`stale-revision`, `text-overflow`). Order has no
meaning in the contract's schemas. Most fields, branches, and values are listed
alphabetically. The order is the same on every build.

A request names its presentation in `presentation`: its folder or a file in
it. Without it, an MCP request goes to the focused presentation, then
to the only open one, and a CLI request to the presentation in the current
folder, if there is one, then to the focused presentation, then to the only open
one. When several presentations are open and none is focused, name one with a
folder from `windows_list`. Configure an MCP client to run `mdpoint mcp` over stdio.
The CLI and MCP use the same live app API and validation. MCP
lists each tool with a title and hints. The hints say whether the tool only
reads, whether it may remove or replace what is there, whether calling it again
changes nothing more, and whether it reaches outside MarkdownPoint. Outside
means a paid service, an AI provider, another application, or the web. A tool
whose result
is plain data also has an output schema.

## Changing a presentation

An agent changes a presentation by editing its files. `files_read` reads them
a part at a time. With no request, it reads the deck's part. With `slideIds` or
`compositionIds`, it reads each slide's or composition's part. With
`theme: true`, it reads the theme's files. A page's part comes with what the app
works out of it, such as where each object is drawn. `files_edit` replaces an
exact `old` text in a part with `new`. Each `old` occurs exactly once in its
part. One request is one Undo step. Its answer gives each part it touched as the
file now spells it, the new `revision`, the `issues` the request brought in, and
those it `cleared`. Take the next `old` from there. `mdpoint files_edit
edits.json` reads a request from a file.

`command` runs one of the app's own commands by its `id`. Each is one Undo
step. Among them are “Change layout”, “Reset to Layout”, “Import 3D Model…”,
the Arrange menu's commands, and one that brings a font from this computer into
the theme. `history_undo` takes back the agent's own step by the `revision` the
step answered, and `history_redo` does it again. A theme step also needs its
`themeRevision`.

## Errors

A failure has a code and a message. With `--json`, a failed command prints one
line on stdout, `{"error": {"code": …, "message": …}}`, and nothing on stderr.
Without it, the error prints on stderr. Its first line starts with its code,
such as `refused:`.
A failed MCP tool answers with `isError` and the same `{"error": …}` as its
text. An error also includes what a caller needs to read beside its code: the
`issues` that fail an edit's validation, the `edit` whose `old` its part
doesn't hold once, the numbers of a conflict's `slides`, or a stale revision's
`revision` and a stale theme's `themeRevision`. A check's
findings are its result, whether it passes or fails. `validate --json` prints
`{ issues }` and exits 1 when one of them is an error. `format --check --json`
prints `{ changed }` and exits 1 when anything would change. An error record
means the check couldn't run. MCP's own errors are JSON-RPC errors: an unknown
tool, or arguments that aren't an object. The command exits 0 when it
succeeds or a check passes, 1 when a check finds what fails it, and 2 when an
error stops it.

| Code | What happened | What to do |
|---|---|---|
| `usage` | The command line is wrong (CLI only). | Read the command's `--help`. |
| `output-failed` | The command made the picture but couldn't write it where `--output` names (CLI only). `output` names that file, and `result` is the method's answer with the picture in it. | Save the picture from `result.image`. Running the command again makes it again. A paid method spends again. |
| `refused` | The app or the command won't do what was asked. The cause is in the request, or in what it asks of this presentation, theme, or file. | Read the message, then change the request. |
| `unknown-method` | This MarkdownPoint has no method with that name. Either it's older than the contract you read, or the name is wrong. | Read `capabilities` for the methods it serves. |
| `stale-revision` | The presentation has changed since the `revision` you sent, or its theme since the `themeRevision` you sent. | Read it again. `revision` is the presentation's current revision, and `themeRevision` the theme's. |
| `validation-failed` | Errors stop the request. `issues` lists the errors the edits' files would have, and `files.read` lists the presentation's own. | Fix those errors. |
| `unknown-target` | The presentation doesn't exist or no window shows it, or there's no such slide, step, object, theme, layout, or action of Meshy's library. | Read the state again to get the ids. |
| `conflict` | The author or a collaborator changed what the edits change while they were taken in, or the theme's files changed meanwhile. `slides` gives the numbers of the slides where it happened. | Read the part again, then retry. |
| `unavailable` | MarkdownPoint can't be reached or the connection closed. A service it calls isn't answering, such as Meshy, an image provider or the collaboration service. Or the presentation's files can't be read or written right now. The message names which. | Try again later. After a closed connection, read the state before you retry a change. |
| `timeout` | MarkdownPoint didn't take the connection in time, or didn't answer within `MARKDOWNPOINT_CALL_TIMEOUT`. | The request may still take effect. Read the state before you retry a change. |
| `too-large` | The request is too large for one call: its size or a range's samples. | Send less at once or lower `sampleRate`. |
| `busy` | Something that can't run at the same time is in progress. It might be another movie export, the presentation opening or closing, another device's turn to edit it, or your earlier requests to Meshy. | Try again when it's done. |
| `internal` | Something went wrong inside MarkdownPoint or the command. | Report it with the message. |

`MARKDOWNPOINT_CALL_TIMEOUT` sets one timeout in seconds for every call the CLI
makes. Without it, a call waits until the app answers or the connection closes.
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
Its keys are the `.mdpoint` file's, and the `.md` file's frontmatter is under
`frontmatter`. A key of ASCII letters, digits, `_`, and `-` that starts with
a letter or `_` follows a dot. A list item is its index in brackets, and any
other key is a JSON string in brackets, as in `slides.intro.objects[2]` and
`slides.intro.initial["title.opacity"]`. An issue with a `path` always has a
`location`. The other fields depend on its `source`:

- `mdpoint`: `location` names the `.mdpoint` file. Once an object's id can
  be read, `path` names the object by its id, as in
  `slides.intro.objects.title.opacity`. Once the slide is read, a step is
  named by its id, as in
  `slides.intro.steps.reveal.curves["title.opacity"].keyframes.fade.value`.
  In it, the curve's name is one key. A keyframe and a cue are always named
  by their ids. A composition is under `compositions`. Once a control's id can
  be read, the control is named by its id, as in
  `compositions.pulse.controls.label`.
  `slideId` names the slide that contains the value, `compositionId` names the
  composition, and `objectId` names the object. An `invalid-json` or
  `merge-conflict` issue has no `path`, and its `location` has the line when
  it's known.
- `md`: `location` names the `.md` file, and most issues have its line.
  `path` names the frontmatter key or the section the issue is about, such as
  `frontmatter.footer` or `sections.intro`. For an issue in a slide's words,
  it names the slide, the composition, the object, or the control in the
  presentation's record, such as `slides.intro.objects.title`. An instance's
  text block is named by the instance's value for its control, such as
  `slides.intro.objects.card.control-values.label`. A table's words that the theme gives its own object are in the
  theme's file instead. `location` names that file, and `path` the words under
  its `text`, such as `text.summary`.
- `folder`: the two files disagree, or the folder can't be read. `path`
  selects the record in the presentation's record. `location` names the line
  in the `.md` file when the issue has one, and otherwise the `.mdpoint` file.
  An issue about the folder as a whole has neither.
- `theme`: an issue written in one of the theme's files names that file in
  `location`. Its `file` is the path in the theme's folder, such as
  `master.json` or `title.json`. `path` selects the value in that file. A file
  that isn't valid JSON gives its line and column. A warning about an object
  that a layout or the master gives its slides, or about their background,
  comes once for each slide on that layout. Its `slideId` names the slide,
  and its `path` the value, such as `overrides.logo.opacity`. A scheme that a
  layout names and the master lacks comes once for each slide on the layout
  too. Its `path` is `scheme`. A file that a master or layout names and the
  theme's folder lacks is at each key that names it, as in
  `objects.logo.file`. A theme font that no file brings is at its role in
  `master.json`, as in `fonts.heading`. When only the slides that
  use a layout or scheme are refused, `slideId` names the slide. A theme's
  file that doesn't read is reported for its first fault only. The issue's
  code is the one the same fault has in a `.mdpoint` file, such as
  `unknown-key`, `missing-key`, or `invalid-json`. A fault in `master.json`
  refuses the whole presentation. A fault in a layout's file gives one issue
  for each slide on that layout. Each of them has the slide's `slideId` and
  that file's `location`. A layout that no slide uses gives its fault in one
  warning. A layout
  the theme lacks, and a slide's own scheme the master lacks, are at the
  slide's `layout` or `scheme` in the `.mdpoint` file. `objectId` is added
  when the issue is about one of the layout's objects. An issue about the
  whole theme, such as a missing theme that the presentation names, has none
  of these fields.
- `file`: `file` is the path of the file the issue is about. It is a path in
  the folder that `fileScope` names. `presentation` is the presentation's
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
  each slide that shows it. A missing generation record is at the `id` that
  names it, as in `slides.intro.objects.photo.generation.id`. A theme font's
  file comes once. It is at its entry in `master.json`'s `font-files`, as in
  `font-files.Lato[0]`. A file that a member of a composition's instance or
  a repeater's copy shows has no `path`. A font that ships with MarkdownPoint
  has none either. No key names it, and its issue comes once.
- `slide`: `slideId`, and `objectId` or `objectIds`. When the issue is about
  one of the object's keys, such as `text-style`, `key` names it, and `path`
  and `location` say where its value is written. The slide's own object and
  its override of an object it inherits are in the `.mdpoint` file, as in
  `slides.intro.overrides.logo.text-style`. An override that is ignored
  leaves the inherited value showing, and the issue is at that value. A
  layout's own object and its override of the master's are in the layout's
  file, and the master's object is in `master.json`. A value the object
  takes from its text style, and a member of a composition's instance, have
  no `path`. A link in a slide's words is in the `.md` file. Its `path` names
  the words' record. A link in words the theme gives its own object is in
  the theme's file.
- `render`, for what drawing a slide finds: `slideId`, and `objectId` or
  `objectIds`. A `render-failed` issue from drawing has neither.

An issue about one object names it by `objectId`. An issue about several
objects names them by `objectIds`, back to front as the slide paints them. No
issue has both.

A refused value of a key with a code of its own has that code, such as
`invalid-layout` for a slide's `layout`, `invalid-locale` for the
presentation's `locale`, or `invalid-icon` for an icon's `icon`. A refused
value of any other key, such as `countdown` or an object's `opacity`, has
`invalid-value`. An override's value that no object can take has
`invalid-override-value`, whatever its key. Either way, `path` names the key.
In any record, a key MarkdownPoint doesn't read has `unknown-key`. A record
that lacks a key it needs has `missing-key`. A record that lacks a key one of
its other keys needs has `missing-key` too, such as a group with `auto-layout`
and no `frame`. For both codes, `path` names that key. `invalid-steps`,
`invalid-initial`, and `invalid-comments` are only for a fault of the list or
record as a whole. A step that isn't a record, or that shares another step's
id, has `invalid-steps`. A value refused inside them has `invalid-value` at its
own key. `invalid-control-value` is only for a value a control's type refuses:
the control's `default` or an instance's value. A value a collaborator set that
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
| Something that can't be read, resolved, matched, or played | `camera-spring-unsettled`, `composition-length-limit`, `issue-count-limit`, `loop-start-past-end`, `loops-misaligned`, `render-failed`, `repeat-count-limit`, `repeat-shares-click`, `stride-with-loop`, `table-cell-limit`, `unclosed-code-block`, `unclosed-frontmatter`, `unreadable-files`, `unreadable-themes`, `unresolved-field`, `unresolved-line-end`, `unresolved-theme`, `unsupported-date-locale` |
| Two parts that disagree, or something in a place where it isn't allowed | `clashing-curves`, `clashing-keyframes`, `clashing-motion-paths`, `file-spelling-mismatch`, `id-mismatch`, `inherited-id-collision`, `marker-outside-slide`, `marker-type-mismatch`, `media-type-mismatch`, `merge-conflict`, `slide-outside-section`, `text-outside-block` |
| Content written by a theme, a newer version, or another program, or left by an interrupted layout move | `generation-in-theme`, `instance-in-theme`, `interrupted-layout-move`, `newer-major-version`, `newer-minor-content`, `rounded-number`, `text-model-in-theme` |
| Objects that a slide can't contain together | `3d-parent-cycle`, `aim-cycle`, `composition-cycle`, `group-depth-limit`, `group-order`, `group-parent-cycle`, `object-count-limit` |
| A key or a value that its object can't have there | `bound-value`, `camera-outside-master`, `color-needs-svg`, `delivery-with-text-path`, `drift-with-scroll`, `effect-size-limit`, `extra-table-lengths`, `extrusion-with-text-path`, `ignored-animation`, `ignored-animation-value`, `ignored-control-block`, `ignored-key`, `ignored-override`, `ignored-override-value`, `ignored-text-block`, `link-letters-out-of-place`, `mask-without-group`, `position-animation-in-auto-layout`, `reserved-camera-id`, `reserved-world-id`, `text-value-in-mdpoint`, `world-outside-master` |
| What the rendered slide shows | `image-enlarged`, `low-text-contrast`, `object-off-slide`, `object-overlap`, `text-hidden`, `text-overflow`, `text-overflow-unchecked` |
