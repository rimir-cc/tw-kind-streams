# kind streams

Bridges [Streams](https://github.com/saqimtiaz/streams) and the [kind](https://github.com/rimir-cc/tw-kind) schema engine so that individual Streams *nodes* can be turned into typed kind instances, marked as such, and edited — all from inside the Streams outline.

## What it does

- **Identify** — every Streams node with a `kind.type` shows a small pill with its type key, plus a subtle row accent.
- **Edit in place** — click the pill to expand kind's compact field bar for that node (click a field to edit; toggle inline/popup). The bar renders *lazily*, only for the node you open.
- **Assign a type** — right-click any node → **Kind type ▸** opens a flyout submenu (styled like the Streams menu) listing the kind types; pick one to set, change (✓ marks the current) or remove its `kind.type`.

- **Extend rows** — other plugins can add something to every row by tagging a tiddler `$:/tags/rimir/kind-streams/row-addon` (used by [rimir/streams-ports](https://github.com/rimir-cc/tw-streams-ports) for port handles), without overriding the Streams row template themselves.

## Performance

Designed to stay cheap in large outlines:

| Node state | Cost |
| --- | --- |
| Untyped | one `has[kind.type]` test; renders nothing |
| Typed, collapsed | one pill (single field read, no type lookup) + an unopened `<$reveal>` |
| Typed, expanded | full field bar — only for the nodes you expand |
| Assigning | submenu in the context menu; type list renders only when the flyout opens |

## How it hooks in

Streams renders each node's content through `$:/plugins/sq/streams/templates/stream-row-body`, a bare `<$transclude mode="block"/>` that bypasses `$:/core/ui/ViewTemplate`. This plugin ships its own copy of that template at `plugin-priority: 2` (Streams is `1`), so TiddlyWiki's shadow-precedence rules make it win. The override adds a cheap gate so only typed nodes render the extra component.

No field or type logic is duplicated: the expanded bar transcludes kind's `stream-view`, and the assign submenu reuses kind's type filter (top-level types in the active namespace).

## Prerequisites

- **kind** (required) — the type system, field bar and type picker.
- **Streams** (required for the in-outline features).

## License

MIT — see [LICENSE.md](LICENSE.md).
