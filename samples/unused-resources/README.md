# 🧹 Unused resources

Flip the usage query around and surface resources whose last activity is older than 30 days. These are cleanup candidates based on real usage rather than modified dates.

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
        "'microsoft.powerplatformusage/usagerecords'"
      ]
    },
    {
      "$type": "extend",
      "FieldName": "LastUsed",
      "Expression": "todatetime(properties.lastUsed)"
    },
    {
      "$type": "where",
      "FieldName": "LastUsed",
      "Operator": "<",
      "Values": [
        "ago(30d)"
      ]
    },
    {
      "$type": "extend",
      "FieldName": "joinKey",
      "Expression": "tolower(tostring(properties.resourceId))"
    },
    {
      "$type": "join",
      "JoinKind": "leftouter",
      "RightTable": {
        "TableName": "PowerPlatformResources",
        "Clauses": [
          {
            "$type": "project",
            "FieldList": [
              "joinKey = tolower(name)",
              "ResourceName = properties.displayName",
              "ResourceType = type",
              "Owner = properties.ownerId"
            ]
          }
        ]
      },
      "LeftColumnName": "joinKey",
      "RightColumnName": "joinKey"
    },
    {
      "$type": "project",
      "FieldList": [
        "ResourceName",
        "ResourceType",
        "LastUsed",
        "Owner"
      ]
    },
    {
      "$type": "orderby",
      "FieldNamesAscDesc": {
        "LastUsed": "asc"
      }
    }
  ]
}
```

## Run it

```bash
pac admin query --query-file samples/unused-resources/query.json --output-type Json
```

## Sample output

Sanitized excerpt from a live tenant.

```json
{
  "totalRecords": 18,
  "count": 18,
  "resultTruncated": 1,
  "skipToken": null,
  "data": [
    {
      "ResourceName": null,
      "ResourceType": "",
      "LastUsed": "2026-06-09T23:59:59Z",
      "Owner": null
    },
    {
      "ResourceName": "Sample resource 258",
      "ResourceType": "microsoft.copilotstudio/agents",
      "LastUsed": "2026-06-10T23:59:59Z",
      "Owner": "aaaaaaaa-bbbb-cccc-dddd-000000000015"
    },
    {
      "ResourceName": "Sample resource 185",
      "ResourceType": "microsoft.copilotstudio/agents",
      "LastUsed": "2026-06-10T23:59:59Z",
      "Owner": "aaaaaaaa-bbbb-cccc-dddd-000000000015"
    },
    {
      "ResourceName": "Sample resource 259",
      "ResourceType": "microsoft.copilotstudio/agents",
      "LastUsed": "2026-06-10T23:59:59Z",
      "Owner": "aaaaaaaa-bbbb-cccc-dddd-000000000015"
    },
    {
      "ResourceName": null,
      "ResourceType": "",
      "LastUsed": "2026-06-10T23:59:59Z",
      "Owner": null
    },
    {
      "ResourceName": null,
      "ResourceType": "",
      "LastUsed": "2026-06-10T23:59:59Z",
      "Owner": null
    },
    {
      "ResourceName": null,
      "ResourceType": "",
      "LastUsed": "2026-06-10T23:59:59Z",
      "Owner": null
    },
    {
      "ResourceName": null,
      "ResourceType": "",
      "LastUsed": "2026-06-10T23:59:59Z",
      "Owner": null
    },
    {
      "ResourceName": null,
      "ResourceType": "",
      "LastUsed": "2026-06-10T23:59:59Z",
      "Owner": null
    },
    {
      "ResourceName": null,
      "ResourceType": "",
      "LastUsed": "2026-06-10T23:59:59Z",
      "Owner": null
    },
    {
      "ResourceName": null,
      "ResourceType": "",
      "LastUsed": "2026-06-10T23:59:59Z",
      "Owner": null
    },
    {
      "ResourceName": null,
      "ResourceType": "",
      "LastUsed": "2026-06-10T23:59:59Z",
      "Owner": null
    },
    {
      "ResourceName": null,
      "ResourceType": "",
      "LastUsed": "2026-06-10T23:59:59Z",
      "Owner": null
    },
    {
      "ResourceName": null,
      "ResourceType": "",
      "LastUsed": "2026-06-10T23:59:59Z",
      "Owner": null
    },
    {
      "ResourceName": null,
      "ResourceType": "",
      "LastUsed": "2026-06-10T23:59:59Z",
      "Owner": null
    },
    {
      "ResourceName": null,
      "ResourceType": "",
      "LastUsed": "2026-06-10T23:59:59Z",
      "Owner": null
    },
    {
      "ResourceName": null,
      "ResourceType": "",
      "LastUsed": "2026-06-10T23:59:59Z",
      "Owner": null
    },
    {
      "ResourceName": null,
      "ResourceType": "",
      "LastUsed": "2026-06-10T23:59:59Z",
      "Owner": null
    }
  ],
  "facets": []
}
```
