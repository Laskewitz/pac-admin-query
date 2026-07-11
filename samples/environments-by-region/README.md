# 🌐 Environments by region

Count environments per region. On a single-geo tenant this is one row, but across geographies it's a quick data-residency check.

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
      "FieldName": "Region",
      "Expression": "tostring(location)"
    },
    {
      "$type": "summarize",
      "SummarizeClauseExpression": {
        "OperatorName": "count",
        "OperatorFieldName": "EnvironmentCount",
        "FieldList": [
          "Region"
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

[download](https://raw.githubusercontent.com/Laskewitz/pac-admin-query/main/samples/environments-by-region/query.json)

## Run it

```bash
pac admin query --query-file samples/environments-by-region/query.json --output-type Json
```

## Sample output

Sanitized excerpt from a live tenant.

```json
{
  "totalRecords": 1,
  "count": 1,
  "resultTruncated": 1,
  "skipToken": null,
  "data": [
    {
      "Region": "unitedstates",
      "EnvironmentCount": 19
    }
  ],
  "facets": []
}
```

[download](https://raw.githubusercontent.com/Laskewitz/pac-admin-query/main/samples/environments-by-region/output.json)
