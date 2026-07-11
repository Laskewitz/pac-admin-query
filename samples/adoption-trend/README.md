# 📈 Adoption trend

Bin every app, flow, and agent by the month it was created to see how fast the tenant is growing over time. `startofmonth(todatetime(...))` does the bucketing.

## Query

```json
{
  "TableName": "PowerPlatformResources",
  "Clauses": [
    {
      "$type": "where",
      "FieldName": "type",
      "Operator": "in~",
      "Values": [
        "'microsoft.powerapps/canvasapps'",
        "'microsoft.powerautomate/cloudflows'",
        "'microsoft.powerautomate/agentflows'",
        "'microsoft.copilotstudio/agents'"
      ]
    },
    {
      "$type": "extend",
      "FieldName": "Month",
      "Expression": "startofmonth(todatetime(properties.createdAt))"
    },
    {
      "$type": "summarize",
      "SummarizeClauseExpression": {
        "OperatorName": "count",
        "OperatorFieldName": "ResourceCount",
        "FieldList": [
          "Month"
        ]
      }
    },
    {
      "$type": "orderby",
      "FieldNamesAscDesc": {
        "Month": "asc"
      }
    }
  ]
}
```

[download](https://raw.githubusercontent.com/Laskewitz/pac-admin-query/main/samples/adoption-trend/query.json)

## Run it

```bash
pac admin query --query-file samples/adoption-trend/query.json --output-type Json
```

## Sample output

Sanitized excerpt from a live tenant.

```json
{
  "totalRecords": 2,
  "count": 2,
  "resultTruncated": 1,
  "skipToken": null,
  "data": [
    {
      "Month": "2026-06-01T00:00:00Z",
      "ResourceCount": 111
    },
    {
      "Month": "2026-07-01T00:00:00Z",
      "ResourceCount": 383
    }
  ],
  "facets": []
}
```

[download](https://raw.githubusercontent.com/Laskewitz/pac-admin-query/main/samples/adoption-trend/output.json)
