# MarkdownPoint agent contract, version 1

The `mdpoint` command and its MCP server use the `markdownpoint/agent/1` contract to talk to the running app. The app's own Agent uses the same methods. The app describes the contract itself. `mdpoint capabilities` lists its methods, operations, and limits, and `mdpoint schema <operation>` gives one operation's schema. `mdpoint mcp` serves the tools to an MCP client. Each has an input schema. From MCP 2025-06-18 on, each tool whose result contains no picture also has an output schema.

## The contract

`capabilities` is a compact index of the app's contract: the methods it
serves, the operation names it accepts, its limits, and its interpolations.
The app answers it with any number of presentations open, or none. Its `methods`
are `{ app, presentation }`: the methods the app answers on its own with no
presentation open, and the methods it answers for a presentation. `schema` gives
the request contracts one operation at a time. An agent never needs the whole
proposal schema in its context. `mdpoint
schema <operation> --fields` (MCP
`detail: "fields"`) lists the operation's
fields instead of its schema: each field's type, whether every request needs
it, and the values it accepts. `--json` prints one compact line for machines.

The contract is `markdownpoint/agent/1`, and the app serves one contract at a
time. From MarkdownPoint 1.0 on, the contract only grows. A new method, field,
or value is added to `/1`, and a rename, a removal, or a change of meaning
comes in `/2`. Methods are grouped by what they act on into plural collections, such as
`presentations`, `slides`, and `themes`. There are also two action groups, `present`
and `history`. The contract names its own facts in camelCase (`slides.get`,
`insertSlide`, `nextStep`, `slideId`). A field that contains a file key's value is
spelled as the file spells the key (`text-styles`, `unit-scale`, `aspect-ratio`). MCP's
tools are named after their methods in snake case (`slides_get`). A CLI command is its
method's names in kebab-case, with each collection in the singular:
`mdpoint
presentation get` for `presentations.get`, `mdpoint image suggest-prompt` for
`images.suggestPrompt`. A request's `type` is an operand:
`mdpoint present
control next-slide`. On the CLI's command line, a value is written in
kebab-case (`toggle-black-screen` for `toggleBlackScreen`), but a method or an
operation name is written as the contract spells it (`mdpoint schema insertSteps`,
`mdpoint call
presentations.get`). The contract's codes and the file's keys and values
are kebab-case
(`stale-revision`, `text-overflow`). Order has no
meaning in the contract's schemas. Most fields, branches, and values are listed
alphabetically, the same on every build. `type` is rarely an operation's first
field.

A CLI request goes to the presentation that `--presentation` names by its folder
or by a file in it. Without `--presentation`, the request goes to the
presentation in the current folder, if there is one, then to the focused
presentation, then to the only open one. When several presentations are open and
none is focused, pass `--presentation "/absolute/presentation/folder"` with a
folder from `window list`. An MCP request names its presentation in
`presentation`. Without it, the request goes to the focused presentation, then
to the only open one. Configure an MCP client to run `mdpoint mcp` over stdio.
The CLI and MCP use the same live app API, validation, and proposals.
`mdpoint
call` also accepts a method name and JSON parameters directly. MCP
lists each tool with a title and hints. The hints say whether the tool only
reads, whether it may remove or replace what is there, whether calling it again
changes nothing more, and whether it reaches outside MarkdownPoint. Outside
means a paid service, an AI provider, another application, or the web. A tool
whose result
is plain data also has an output schema.

## Errors

A failure has a code and a message. With `--json`, a failed command prints one
line on stdout, `{"error": {"code": …, "message": …}}`, and nothing on stderr.
A failed MCP tool answers with `isError` and the same `{"error": …}` as its
text. An error also includes what a caller needs to read beside its code: a
refused proposal's `validation` or a stale revision's `revision`. A check's
findings are its result, whether it passes or fails. `validate --json` prints
`{ issues }` and exits 1 when one of them is an error. `format --check --json`
prints `{ changed }` and exits 1 when anything would change. An error record
means the check couldn't run. MCP's own errors are JSON-RPC errors: an unknown
tool, or arguments that aren't an object. The command exits 0 when it
succeeds, 1 when it fails, 2 on a usage error, and 3 when MarkdownPoint is
unavailable.

| Code | What happened | What to do |
|---|---|---|
| `usage` | The command line is wrong (CLI only). | Read the command's `--help`. |
| `refused` | The app or the command won't do what was asked. The cause is in the request, or in what it asks of this presentation, theme, or file. | Read the message, then change the request. |
| `unknown-method` | This MarkdownPoint has no method with that name. Either it's older than the contract you read, or the name is wrong. | Read `capabilities` for the methods it serves. |
| `stale-revision` | The presentation has changed since the revision you sent. | Read it again. `revision` is its current revision. |
| `validation-failed` | Errors stop the request. `validation` lists the proposal's errors, and `presentations.get` lists the presentation's own. | Fix those errors. |
| `unknown-target` | The presentation doesn't exist or no window shows it, or there's no such slide, step, object, theme, layout, proposal, or action of Meshy's library. | Read the state again to get the ids. |
| `conflict` | The presentation's files changed on disk while MarkdownPoint was saving them. Another change was saved first. | Read the state again, then retry. |
| `unavailable` | MarkdownPoint isn't running or can't be reached, the connection closed, or the presentation's files can't be read right now. | Start MarkdownPoint. After a closed connection, read the state before you retry a change. |
| `timeout` | MarkdownPoint didn't answer in time. | The request may still take effect. Read the state before you retry a change. |
| `too-large` | The request is too large for one call: its size, a proposal's operations, or a range's samples. | Send less at once, or lower `sampleRate`. |
| `busy` | Something that can't run at the same time is in progress, such as accepting that proposal or another movie export. | Try again when it's done. |
| `internal` | Something went wrong inside MarkdownPoint or the command. | Report it with the message. |

`MARKDOWNPOINT_CALL_TIMEOUT` sets one timeout in seconds for every call the CLI
makes. Without it, each method uses its own timeout. The CLI connects to
the app's endpoint: a socket on a Mac and a named pipe on Windows. It
looks for the endpoint in `--endpoint`, then `MARKDOWNPOINT_ENDPOINT`, then in
the app's state folder. The app records it there. The state folder is
`MARKDOWNPOINT_STATE_DIR` when that is set. `mdpoint --help` lists them.

### Issues

An issue is a problem that `validate` and `presentation get` find in the
files. It has a `severity` (`error` or `warning`), a `source`, a `code`, a
`message` in English, and a `disposition`: what MarkdownPoint does about it.
With `show-presentation`, the presentation is shown. Whatever can't be used
shows a default or nothing. Each message says which. With `skip-object`,
the object is left out. With `refuse-slide`, the slide is refused. With
`refuse-presentation`, the whole presentation is refused. The fields that locate an issue depend on its
`source`:

- `mdpoint`: `path` selects the value in the `.mdpoint` file, one key after
  another. A key of ASCII letters, digits, `_`, and `-` that starts with a
  letter or `_` follows a dot. Any other key is in brackets, quoted as a JSON
  string, and a list item is its index in brackets, as in
  `slides.intro.objects[2]` and `slides.intro.initial["title.opacity"]`. Once
  an object's id can be read, the object is named by its id, as in
  `slides.intro.objects.title.opacity`. Once the slide is read, a step or a
  keyframe is named by its id, as in
  `slides.intro.steps.reveal.curves["title.opacity"].keyframes.fade.value`.
  In it, the curve's name is one key. A composition is under `compositions`.
  `slideId` names the slide that contains the value, `compositionId` names the
  composition, and `objectId` names the object.
- `md`: `location` names the file and the line, counted from 1. It also names
  the column when it's known. `path` names the frontmatter key or the section
  the
  issue is about, such as `frontmatter.footer` or `sections.intro`. For an
  issue in a slide's words, it names the slide, the composition, the object,
  or the control in the `.mdpoint` file, written as for `mdpoint`, such as
  `slides.intro.objects.title`. An instance's text block is named by the
  instance's value for its control, such as
  `slides.intro.objects.card.control-values.label`. An operation names it the
  same way.
- `folder`: the two files disagree, or the folder can't be read. `path`
  selects the record in the `.mdpoint` file. It is written as for `mdpoint`.
  `location` names the line in the `.md` file. Each one is there only when the issue has
  one.
- `theme`: when only the slides that use a layout or scheme are refused,
  `slideId` names the slide, and `path` names its `layout` or `scheme` in the
  `.mdpoint` file. It is written as for `mdpoint`. `objectId` is added when the issue is about
  one of the layout's objects. An issue about the whole theme, such as a
  missing theme that the presentation names, has none of these fields.
- `file`: `file` is the file's path in the folder that `fileScope` names. The
  issue has no `path`. A `path` is only ever a place inside a file.
  `presentation` is the presentation's folder, and `theme` is its theme's.
  `cache` is the folder on this computer where MarkdownPoint saves the models
  it builds from a text's letters. `bundled` is the fonts that ship with
  MarkdownPoint. `slideId`, `objectId`, and `property` name each place where
  the file is used. An issue about a theme's own file, its `master.json` or a
  layout's file, has none of these fields.
- `slide`: `slideId`, and `objectId` or `objectIds`.
- `render`, for what drawing a slide finds: `slideId`, and `objectId` or
  `objectIds`. A `render-failed` issue has neither.

A refused value of a key with a code of its own has that code, such as
`invalid-layout` for a slide's `layout` or `invalid-locale` for the
presentation's `locale`. Every other key, an object's keys and settings such
as `countdown` among them, has `invalid-value`. Either way, `path` names the
key.

The codes an issue can have:

| What | Codes |
|---|---|
| A file, a record, or a value MarkdownPoint refuses | `invalid-3d-parent`, `invalid-animation`, `invalid-binding`, `invalid-color`, `invalid-colors`, `invalid-comments`, `invalid-composition`, `invalid-composition-id`, `invalid-compositions`, `invalid-control-value`, `invalid-file-path`, `invalid-font`, `invalid-format-version`, `invalid-frame`, `invalid-frontmatter`, `invalid-generation`, `invalid-group-parent`, `invalid-icon`, `invalid-image-style`, `invalid-initial`, `invalid-json`, `invalid-json-root`, `invalid-layout`, `invalid-line-end`, `invalid-link-target`, `invalid-locale`, `invalid-marker`, `invalid-marker-id`, `invalid-media`, `invalid-object-id`, `invalid-object-type`, `invalid-objects`, `invalid-override-value`, `invalid-paragraph-id`, `invalid-parent-inverse`, `invalid-parent-node`, `invalid-placeholder`, `invalid-point`, `invalid-presentation-field`, `invalid-prompt`, `invalid-restyle`, `invalid-scheme`, `invalid-section-heading`, `invalid-skipped`, `invalid-slide-id`, `invalid-slides`, `invalid-steps`, `invalid-table`, `invalid-table-lengths`, `invalid-theme-file`, `invalid-theme-key`, `invalid-theme-master`, `invalid-theme-root`, `invalid-type`, `invalid-uuid`, `invalid-value`, `invalid-web-video` |
| Something needed that is missing | `missing-alt`, `missing-composition`, `missing-composition-marker`, `missing-control-block`, `missing-font`, `missing-frontmatter`, `missing-generation`, `missing-key`, `missing-marker-line-break`, `missing-media`, `missing-poster`, `missing-slide`, `missing-slide-marker`, `missing-text`, `missing-text-block`, `missing-theme-master` |
| Something given twice | `duplicate-composition-id`, `duplicate-composition-marker`, `duplicate-control-block`, `duplicate-notes-block`, `duplicate-object-id`, `duplicate-paragraph-id`, `duplicate-section-marker`, `duplicate-slide-marker`, `duplicate-text-block` |
| A name that nothing defines | `unknown-action`, `unknown-animation-target`, `unknown-color-role`, `unknown-composition`, `unknown-constraint-target`, `unknown-control`, `unknown-key`, `unknown-layout`, `unknown-link-slide`, `unknown-object`, `unknown-parent-node`, `unknown-scheme`, `unknown-text-style` |
| Something that can't be read, resolved, matched, or played | `camera-spring-unsettled`, `loops-misaligned`, `render-failed`, `repeat-shares-click`, `travel-in-loop`, `unclosed-code-block`, `unclosed-frontmatter`, `undrawn-line`, `unreadable-collaborator-value`, `unreadable-files`, `unreadable-layout`, `unreadable-themes`, `unresolved-field`, `unresolved-line-end`, `unresolved-theme`, `unsupported-locale`, `words-outside-block` |
| Two parts that disagree, or something in a place where it isn't allowed | `clashing-curves`, `clashing-keyframes`, `clashing-orient`, `file-spelling-mismatch`, `id-mismatch`, `inherited-id-collision`, `marker-outside-slide`, `marker-type-mismatch`, `media-type-mismatch`, `merge-conflict`, `slide-outside-section` |
| Content written by a theme, a newer version, or another program, or left by an interrupted layout move | `generation-in-theme`, `instance-in-theme`, `interrupted-layout-move`, `newer-format-version`, `newer-minor-kept`, `newer-theme-master`, `rounded-number`, `text-model-in-theme` |
| Objects that a slide can't contain together | `3d-object-in-composition`, `aim-cycle`, `composition-cycle`, `group-cycle`, `group-depth`, `group-order`, `object-count-limit`, `parent-cycle` |
| A key or a value that its object can't have there | `bound-value`, `camera-in-slide`, `color-needs-svg`, `delivery-with-text-path`, `drift-with-scroll`, `effect-size-limit`, `extra-table-lengths`, `ignored-key`, `ignored-override`, `ignored-override-value`, `ignored-text-block`, `key-not-for-light-type`, `link-letters-out-of-place`, `override-type`, `parent-inverse-without-parent`, `parent-node-without-parent`, `position-animation-in-auto-layout`, `reserved-camera-id`, `reserved-world-id`, `text-value-in-mdpoint`, `world-in-slide` |
| What the rendered slide shows | `image-enlarged`, `low-text-contrast`, `object-off-slide`, `object-overlap`, `text-hidden`, `text-overflow`, `text-overflow-unchecked` |
