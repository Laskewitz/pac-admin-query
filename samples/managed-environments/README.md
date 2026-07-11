# 🛡️ Managed environments

Count how many environments are Managed Environments versus not.

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
      "FieldName": "IsManaged",
      "Expression": "tostring(properties.isManaged)"
    },
    {
      "$type": "summarize",
      "SummarizeClauseExpression": {
        "OperatorName": "count",
        "OperatorFieldName": "EnvironmentCount",
        "FieldList": [
          "IsManaged"
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

[Download](https://raw.githubusercontent.com/Laskewitz/pac-admin-query/main/samples/managed-environments/query.json)

## Run it

```bash
pac admin query --query-file samples/managed-environments/query.json --output-type Json
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
      "IsManaged": "true",
      "EnvironmentCount": 10
    },
    {
      "IsManaged": "false",
      "EnvironmentCount": 9
    }
  ],
  "facets": []
}
```

[Download](https://raw.githubusercontent.com/Laskewitz/pac-admin-query/main/samples/managed-environments/output.json)
