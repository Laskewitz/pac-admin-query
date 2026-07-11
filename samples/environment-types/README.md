# 🏷️ Environment types

Break your environments down by type. A pile of Developer environments usually means everyone spun up their own, which is worth knowing before you tighten governance.

## Query

```json
{
  "TableName": "PowerPlatformResources",
  "Clauses": [
    {
      "$type": "where",
      "FieldName": "type",
      "Operator": "==",
      "Values": [
        "'microsoft.powerplatform/environments'"
      ]
    },
    {
      "$type": "extend",
      "FieldName": "EnvironmentType",
      "Expression": "tostring(properties.environmentType)"
    },
    {
      "$type": "summarize",
      "SummarizeClauseExpression": {
        "OperatorName": "count",
        "OperatorFieldName": "EnvironmentCount",
        "FieldList": [
          "EnvironmentType"
        ]
      }
    },
    {
      "$type": "orderby",
      "FieldNamesAscDesc": {
        "EnvironmentCount": "desc"
      }
    }
  ]
}
```

[`query.json` (raw)](https://raw.githubusercontent.com/Laskewitz/pac-admin-query/main/samples/environment-types/query.json)

## Run it

```bash
pac admin query --query-file samples/environment-types/query.json --output-type Json
```

## Sample output

Sanitized excerpt from a live tenant.

```json
{
  "totalRecords": 4,
  "count": 4,
  "resultTruncated": 1,
  "skipToken": null,
  "data": [
    {
      "EnvironmentType": "Sandbox",
      "EnvironmentCount": 12
    },
    {
      "EnvironmentType": "Developer",
      "EnvironmentCount": 5
    },
    {
      "EnvironmentType": "Production",
      "EnvironmentCount": 1
    },
    {
      "EnvironmentType": "Default",
      "EnvironmentCount": 1
    }
  ],
  "facets": []
}
```

[`output.json` (raw)](https://raw.githubusercontent.com/Laskewitz/pac-admin-query/main/samples/environment-types/output.json)
