# 🧪 `pac admin query` samples

Each folder has its own `README.md` with a short write-up, a ready-to-run `query.json`, and a matching sanitized output captured from a live tenant. Run any sample from the repo root:

```bash
pac admin query --query-file samples/<folder>/query.json --output-type <Grid|List|Json>
```

> [!NOTE]
> Outputs are sanitized. GUIDs become deterministic `aaaaaaaa-bbbb-cccc-dddd-…` placeholders, display names become `Contoso …` or `Sample …`, and custom connector IDs become `shared_customconnector…`. Standard `shared_*` connectors, resource types, locations, counts, and timestamps stay as-is. Large outputs are truncated to 20 rows, and `totalRecords` shows the real total.

## 🌍 Environments

| Sample | Question it answers | Output |
| --- | --- | --- |
| [`environments/`](./environments/) | Every environment with region, type, and managed state. | [`output.csv`](./environments/output.csv) |
| [`environment-sprawl/`](./environment-sprawl/) | How many resources of each type live in each environment? | [`output.json`](./environment-sprawl/output.json) |
| [`managed-environments/`](./managed-environments/) | How many environments are managed versus not? | [`output.json`](./managed-environments/output.json) |
| [`environment-types/`](./environment-types/) | Production, sandbox, developer split? | [`output.json`](./environment-types/output.json) |
| [`environments-by-region/`](./environments-by-region/) | Where does your environment data live? | [`output.json`](./environments-by-region/output.json) |

## 📦 Resources

| Sample | Question it answers | Output |
| --- | --- | --- |
| [`count-by-type/`](./count-by-type/) | How many resources of each type and location? | [`output.json`](./count-by-type/output.json) |
| [`maker-adoption/`](./maker-adoption/) | Who builds the most apps, flows, and agents? | [`output.json`](./maker-adoption/output.json) |
| [`new-resources/`](./new-resources/) | What was created in the last 30 days? | [`output.json`](./new-resources/output.json) |
| [`adoption-trend/`](./adoption-trend/) | How many resources are created each month? | [`output.json`](./adoption-trend/output.json) |
| [`recently-modified/`](./recently-modified/) | What changed in the last 7 days? | [`output.json`](./recently-modified/output.json) |
| [`orphaned-resources/`](./orphaned-resources/) | Which owners to diff against deleted accounts? | [`output.json`](./orphaned-resources/output.json) |

## 🔌 Connectors

| Sample | Question it answers | Output |
| --- | --- | --- |
| [`connector-audit/`](./connector-audit/) | Which connectors are used most across the tenant? | [`output.json`](./connector-audit/output.json) |
| [`stale-connectors/`](./stale-connectors/) | Which connectors are only used by dormant resources? | [`output.json`](./stale-connectors/output.json) |

## 📊 Usage

| Sample | Question it answers | Output |
| --- | --- | --- |
| [`recently-used/`](./recently-used/) | What was used recently (real usage telemetry)? | [`output.json`](./recently-used/output.json) |
| [`unused-resources/`](./unused-resources/) | What hasn't been used in 30+ days? | [`output.json`](./unused-resources/output.json) |

