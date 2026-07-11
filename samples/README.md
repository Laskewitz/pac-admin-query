# 🧪 `pac admin query` samples

Each folder contains a ready-to-run **`query.json`** input and a matching **sanitized output** captured from a live tenant. Run any sample from the repo root:

```bash
pac admin query --query-file samples/<folder>/query.json --output-type <Grid|List|Json>
```

> 🔒 **Outputs are sanitized.** GUIDs are replaced with deterministic `aaaaaaaa-bbbb-cccc-dddd-…` placeholders, display names with `Contoso …` / `Sample …`, and custom connector IDs with `shared_customconnector…`. Standard `shared_*` connectors, resource types, locations, counts, and timestamps are left as-is. The point is to show what real results look like without exposing anyone's actual tenant.

| 📁 Folder | ❓ Question it answers | ▶️ Command | 📤 Output |
|-----------|----------------------|-----------|-----------|
| [`count-by-type/`](./count-by-type/) | How many resources of each type/location exist? | `pac admin query -qf samples/count-by-type/query.json -ot Json -of output.json` | [`output.json`](./count-by-type/output.json) |
| [`environments/`](./environments/) | What environments exist, and their type/managed state/group? | `pac admin query -qf samples/environments/query.json -ot Grid -of output.csv` | [`output.csv`](./environments/output.csv) |
| [`top-makers/`](./top-makers/) | Who owns the most resources? | `pac admin query -qf samples/top-makers/query.json -ot Json -of output.json` | [`output.json`](./top-makers/output.json) |
| [`connector-audit/`](./connector-audit/) | Which connectors are used most across resources? | `pac admin query -qf samples/connector-audit/query.json -ot Json -of output.json` | [`output.json`](./connector-audit/output.json) |
| [`orphaned-resources/`](./orphaned-resources/) | Which owners hold resources (to diff against deleted accounts)? | `pac admin query -qf samples/orphaned-resources/query.json -ot Json -of output.json` | [`output.json`](./orphaned-resources/output.json) |
| [`recently-used/`](./recently-used/) | What was used recently (usage telemetry joined to names)? | `pac admin query -qf samples/recently-used/query.json -ot Json -of output.json` | [`output.json`](./recently-used/output.json) |

> ℹ️ `recently-used/output.json` is truncated to 20 rows for readability (`totalRecords` shows the real total).
