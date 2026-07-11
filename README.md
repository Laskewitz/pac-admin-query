# 🔍 Query your Power Platform Inventory

> [!NOTE]
> The bundled `pac-admin-query` agent skill was written by [Andrew Petrochuk](https://github.com/petrochuk). Thanks, Andrew!

Version `2.9.3` of the Power Platform CLI added a new command, `pac admin query`, for querying your tenant-wide Power Platform inventory. This repo gives you working sample queries for it, and it bundles an agent skill by [Andrew Petrochuk](https://github.com/petrochuk) so [GitHub Copilot](https://github.com/features/copilot) can build and run them for you.

The skill lives in [`.github/skills/pac-admin-query/`](./.github/skills/pac-admin-query/) and bundles a `SKILL.md` reference plus ready-to-adapt example query files.

## ✅ Prerequisites

- A PAC CLI build that includes `pac admin query` (verified with `2.9.3`).
- An active PAC auth profile with tenant context and permission to call the [Power Platform inventory API](https://learn.microsoft.com/power-platform/admin/inventory-api).

```powershell
pac auth list      # inspect profiles
pac auth select    # activate the right one
```

## 🚀 Usage

> [!TIP]
> Prefer a query file over inline JSON to avoid shell-escaping headaches.

### 🎛️ Arguments

| Argument | Alias | Description |
| --- | --- | --- |
| `--query-file` | `-qf` | Read the query body from a `.json` file. |
| `--query` | `-q` | Provide the query body inline as a string. |
| `--output-type` | `-ot` | `Grid` *(default)*, `List`, or `Json`. |
| `--output-file` | `-of` | Write results to a file (`Grid` CSV, `Json` JSON). |

| Output type | Flag | Best for |
| --- | --- | --- |
| `Grid` *(default)* | `-ot Grid` | Quick, readable tables |
| `List` | `-ot List` | Simple line-per-field text |
| `Json` | `-ot Json` | Nested data, metadata & pagination |

### 🖥️ Command samples

Run a query file and print a Grid table to the console (the default output type):

```powershell
pac admin query --query-file .\query.json
```

The `-qf` alias does the same thing:

```powershell
pac admin query -qf .\query.json
```

Get the full JSON response, with metadata, nested props, and the pagination token:

```powershell
pac admin query -qf .\query.json -ot Json
```

Print simple line-per-field List output:

```powershell
pac admin query -qf .\query.json -ot List
```

Export a CSV file (Grid plus `--output-file`):

```powershell
pac admin query -qf .\query.json -ot Grid -of .\inventory.csv
```

Export the raw JSON response to a file:

```powershell
pac admin query -qf .\query.json -ot Json -of .\inventory.json
```

> [!WARNING]
> There's also an inline `--query` / `-q` flag, but shell quoting around the JSON (especially the `$type` keys) frequently trips it up. Stick with `--query-file`: save the JSON to a `.json` file and point `-qf` at it.

### 🔐 Pick the right auth profile first

```powershell
pac auth list                                                # see profiles
pac auth select --index 1                                    # activate one
pac auth who                                                 # confirm the active profile & tenant
```

### 📄 Paginating large result sets

`Grid`/`List` cap what they print. For big exports, use `Json` and follow the `skipToken`: when the response has a non-empty `skipToken`, copy it into `Options.SkipToken` in your query file and rerun to fetch the next page, keeping every other clause unchanged.

```jsonc
{
  "TableName": "PowerPlatformResources",
  "Clauses": [ /* ... */ ],
  "Options": { "Top": 1000, "Skip": 0, "SkipToken": "<paste skipToken here>" }
}
```

See [`SKILL.md`](./.github/skills/pac-admin-query/SKILL.md) for the full request shape, clause reference, and resource-type table.

## 💡 Example queries

Every query below has its own folder under [`samples/`](./samples/) with a runnable `query.json`, a sanitized output captured from a live tenant, and a short write-up. Open a sample to see the full query and results.

> [!IMPORTANT]
> The inventory API exposes resource structure (owner, environment, connectors, created and modified dates), not run or launch telemetry or Entra account status. So treat "stale" as not recently modified rather than unused, and confirm "orphaned" ownership separately.

| Sample | What it answers |
| --- | --- |
| [Maker adoption](./samples/maker-adoption/) | Who builds the most apps, flows, and agents? |
| [Environment sprawl](./samples/environment-sprawl/) | How many resources of each type live in each environment? |
| [Connector audit](./samples/connector-audit/) | Which connectors are used most across the tenant? |
| [New resources](./samples/new-resources/) | What was created in the last 30 days? |
| [Adoption trend](./samples/adoption-trend/) | How many resources are created each month? |
| [Recently modified](./samples/recently-modified/) | What changed in the last 7 days? |
| [Managed environments](./samples/managed-environments/) | How many environments are managed versus not? |
| [Environment types](./samples/environment-types/) | How do environments split across production, sandbox, and developer? |
| [Environments by region](./samples/environments-by-region/) | Where does your environment data live? |
| [Recently used](./samples/recently-used/) | What was used recently, from real usage telemetry? |
| [Unused resources](./samples/unused-resources/) | What hasn't been used in 30+ days? |
| [Orphaned resources](./samples/orphaned-resources/) | Which owners should you cross-check against deleted accounts? |
| [Stale connectors](./samples/stale-connectors/) | Which connectors are only referenced by dormant resources? |
| [Count by type](./samples/count-by-type/) | How many resources of each type and location exist? |
| [Environment inventory](./samples/environments/) | What are all the environments, with region, type, and managed state? |

## 📚 References

- [Power Platform inventory API](https://learn.microsoft.com/power-platform/admin/inventory-api)
- [Power Platform inventory schema](https://learn.microsoft.com/power-platform/admin/inventory-schema)
