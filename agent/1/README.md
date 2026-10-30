# MarkdownPoint agent contract, version 1

The `mdpoint` command and its MCP server speak `markdownpoint/agent/1` to the running app, and the app's own Agent uses the same methods. These files describe it, generated from the app's source:

- [contract.json](contract.json): every method, where it is served, what it takes and what it answers, and the envelopes and the error, as JSON Schema.
- [propose.json](propose.json): a proposal's request, with every operation's fields.
- [theme-edit.json](theme-edit.json): a theme edit's request.
- [export.json](export.json): an export's request.
- [tools.json](tools.json): the tools `mdpoint mcp` lists, with their input and output schemas.

## The contract

`capabilities` is a compact index of the app's contract: served methods,
accepted operation names, limits and interpolations, answered with any number of presentations
open, or none. Its `methods` are `{ app, presentation }`: what the app answers
itself, with no presentation open, and what it answers for one. Request contracts come from `schema`,
one selected operation at a time, so an agent never needs the whole proposal
schema in context. `mdpoint schema <operation> --fields` (MCP `detail: "fields"`)
lists the operation's fields in place of its schema: each one's type, whether
every request needs it, and the values it takes. `--json` prints one compact
line for machines.

The contract is `markdownpoint/agent/1`, and the app serves one contract at a
time. From MarkdownPoint 1.0 it only grows: a new method, field or value joins
`/1`, and a rename, a removal or a changed meaning comes as `/2`. Methods are
grouped by what they act on, in plural collections such as `presentations`,
`slides` and `themes`, beside two action groups, `present` and `history`. The
names the contract gives are camelCase (`slides.get`, `insertSlide`, `noChange`,
`slideId`), and MCP's tools are their methods in snake case (`slides_get`). Its
codes are kebab-case (`stale-revision`, `text-overflow`), as the file's keys
and values are.

Requests reach the focused presentation, or the only open one when no window
is focused. With several presentations open and none focused, pass
`--presentation "/absolute/presentation/folder"` from `window list`. Configure an MCP
client to run `mdpoint mcp` over stdio. Both use the same live app API,
validation and proposals; `mdpoint call` also accepts a method name
and JSON parameters directly. MCP lists each tool with a title and hints.
They say whether the tool only reads, may remove or replace what is there,
changes nothing more when called again, and reaches outside MarkdownPoint: a
paid service, an AI provider, another application or the web. A tool whose
result is plain data also gives its output schema.

## Errors

A failure has a code and a message. With `--json`, a failed command prints one
line on stdout, `{"error": {"code": …, "message": …}}`, and nothing on stderr;
a failed MCP tool answers `isError` with the same `{"error": …}` as its text.
An error keeps what a caller reads beside its code: a refused proposal's
`validation` or a stale revision's `revision`. A check's findings are its result
whether it passes or fails: `validate --json` prints `{ issues }` and exits 1 when
one is an error, and `format --check --json` prints `{ changed }` and exits 1 while
anything would change; an error record means the check couldn't run. MCP's own errors, an unknown tool or arguments that
aren't an object, are JSON-RPC errors. The command exits 0 when it succeeds, 1
when it fails, 2 on a usage error and 3 when MarkdownPoint is unavailable.

| Code | What happened | What to do |
|---|---|---|
| `usage` | The command line is wrong (the CLI only). | Read the command's `--help`. |
| `refused` | The app, or the command, won't do it as asked. | Read the message; change the request. |
| `unknown-method` | This MarkdownPoint serves no method of that name: it is older than the contract you read, or the name is wrong. | Read `capabilities` for the methods it serves. |
| `stale-revision` | The presentation changed since the revision you sent. | Read it again; `revision` is where it is now. |
| `validation-failed` | A proposal found errors. | Fix what `validation` lists. |
| `unknown-target` | No such presentation, slide, step, object, theme, layout or proposal. | Read the state again for the ids. |
| `conflict` | Another change got there first. | Read the state again, then retry. |
| `unavailable` | MarkdownPoint isn't running or can't be reached, the connection closed, or the presentation's files can't be read now. | Start it; after a closed connection, read the state before you retry a change. |
| `timeout` | MarkdownPoint didn't answer in time. | The request may still take effect: read the state before you retry a change. |
| `too-large` | The request is more than one call takes: its size, a proposal's operations or a range's samples. | Send less at once, or lower `fps`. |
| `busy` | Something that can't run beside it is under way, such as accepting that proposal or another movie export. | Try again once it's done. |
| `internal` | Something went wrong inside MarkdownPoint or the command. | Report it with the message. |

`MARKDOWNPOINT_CALL_TIMEOUT_MS` sets one deadline, in milliseconds, for every
call the CLI makes; without it, each method keeps its own. The CLI reaches the
app on `--socket`, then `MARKDOWNPOINT_SOCKET`, then the socket the app records
in its state folder, `MARKDOWNPOINT_STATE_DIR` when set. `mdpoint --help` lists
them.

### Issues

An issue is what `validate` and `presentation get` find in the files: `severity`
(`error` or `warning`), `source`, `code`, `message` in English and
`disposition`, what MarkdownPoint does about it. `fallback` shows the
presentation with a default in its place, `skip-object` leaves the object out,
`refuse-slide` refuses the slide and `refuse-document` the whole presentation.
Where an issue stands depends on its `source`:

- `json`: `path` selects the value in the `.mdpoint` file, keys joined by dots
  and a list's item by its index in brackets, such as
  `slides.intro.objects.title.opacity`; `slideId`, `objectId` or `contentId`
  names what holds it.
- `markdown` and `pair`: `location` names the file and its line, counted from
  1, with the column when known.
- `theme`: `slideId` when only the slides that name the layout or scheme are
  refused.
- `file`: `path` is the file's path as the presentation, or its theme when
  `fileScope` says so, names it; `slideId`, `objectId` and `property` name
  each place that uses it.
- `scene`, `master` and `layout`: `slideId`, and `objectId` or `objectIds`.

Each code an issue carries:

| What | Codes |
|---|---|
| A file, a record or a value MarkdownPoint refuses | `invalid-binding`, `invalid-boolean`, `invalid-clip`, `invalid-color`, `invalid-colors`, `invalid-comments`, `invalid-composition`, `invalid-compositions`, `invalid-connector-anchor`, `invalid-file-path`, `invalid-font`, `invalid-format`, `invalid-frame`, `invalid-frontmatter`, `invalid-generation-prompt`, `invalid-generation-style`, `invalid-group-parent`, `invalid-icon`, `invalid-image-filter`, `invalid-initial`, `invalid-json`, `invalid-json-root`, `invalid-kind`, `invalid-layout`, `invalid-link-target`, `invalid-mapping`, `invalid-marker-id`, `invalid-master-id`, `invalid-media`, `invalid-motion`, `invalid-name`, `invalid-object-id`, `invalid-object-type`, `invalid-objects`, `invalid-optional-value`, `invalid-override`, `invalid-override-value`, `invalid-parent-inverse`, `invalid-parent-joint`, `invalid-placeholder`, `invalid-point`, `invalid-poster`, `invalid-presentation-field`, `invalid-presentation-root`, `invalid-required-value`, `invalid-section-heading`, `invalid-selected-master`, `invalid-set-root`, `invalid-slide-id`, `invalid-slides`, `invalid-space-parent`, `invalid-steps`, `invalid-table`, `invalid-table-lengths`, `invalid-text-format`, `invalid-theme-document`, `invalid-theme-key`, `invalid-uuid`, `invalid-web-video-source`, `malformed-marker`, `paragraph-id-misspelled` |
| Something needed that isn't there | `missing-alt`, `missing-connector-endpoint`, `missing-content`, `missing-file`, `missing-font`, `missing-frontmatter`, `missing-input-block`, `missing-key`, `missing-light-intensity`, `missing-light-type`, `missing-link-slide`, `missing-marker-newline`, `missing-master`, `missing-object-type`, `missing-parent`, `missing-parent-joint`, `missing-position`, `missing-poster`, `missing-slide-section`, `missing-text-block`, `missing-track-target` |
| Something given twice | `duplicate-notes`, `duplicate-object-id`, `duplicate-section`, `duplicate-slide-id`, `duplicate-slide-section`, `duplicate-text-block`, `paragraph-id-repeated` |
| A name nothing defines | `unknown-animation-target`, `unknown-composition`, `unknown-input`, `unknown-key`, `unknown-layout`, `unknown-object`, `unknown-role`, `unknown-scheme`, `unknown-type-style` |
| What can't be read, resolved, matched or played | `discovery-io-error`, `render-failed`, `unattached-markdown`, `unbound-text`, `unclosed-code-block`, `unclosed-frontmatter`, `unmatched-slide-section`, `unpaired-motion-path`, `unplayable-chain`, `unreadable-files`, `unreadable-peer-value`, `unresolved-connector-anchor`, `unresolved-field`, `unresolved-theme` |
| Two parts that disagree, or something where it can't stand | `clashing-keys`, `id-mismatch`, `marker-outside-slide`, `media-type-mismatch`, `merge-conflict`, `object-collision`, `slide-kind-mismatch`, `slide-outside-section` |
| A file a theme, a newer version or another program wrote | `interrupted-layout-move`, `newer-format`, `newer-minor-kept`, `newer-selected-master`, `newer-text-format`, `spelled-file`, `theme-composition`, `theme-image-result`, `theme-text-model` |
| Objects a slide can't hold together | `aim-cycle`, `auto-layout-held-key`, `blur-past-page`, `bound-value`, `color-needs-svg`, `composition-cycle`, `derived-budget`, `drift-with-scroll`, `group-cycle`, `group-depth`, `group-order`, `link-glyphs-away`, `not-for-light-type`, `override-type`, `own-camera`, `own-world`, `parent-cycle`, `parent-inverse-without-parent`, `parent-joint-without-parent`, `path-delivery`, `reserved-camera-id`, `reserved-world-id`, `shared-parent-joint`, `shared-track-joint`, `space-object-in-group`, `table-track-count`, `text-input-in-json`, `tilt-with-3d` |
| What a slide's layout shows | `content-overlap`, `low-text-contrast`, `object-off-canvas`, `raster-upscaled`, `text-hidden`, `text-overflow`, `text-overflow-unchecked` |
