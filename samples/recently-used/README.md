# 📆 Recently used resources

The tenant exposes a `microsoft.powerplatformusage/usagerecords` type carrying `properties.lastUsed` and `properties.resourceId`, which is real usage telemetry. Join it back to the resource to get the friendly name, newest activity first.

> [!NOTE]
> Output is truncated to 20 of 2363 rows. `totalRecords` shows the real count.

## Query

```json
{
  "TableName": "PowerPlatformResources",
  "Clauses": [
    { "$type": "where", "FieldName": "type", "Operator": "==", "Values": ["'microsoft.powerplatformusage/usagerecords'"] },
    { "$type": "extend", "FieldName": "LastUsed", "Expression": "todatetime(properties.lastUsed)" },
    { "$type": "extend", "FieldName": "joinKey", "Expression": "tolower(tostring(properties.resourceId))" },
    { "$type": "join", "JoinKind": "leftouter", "RightTable": { "TableName": "PowerPlatformResources", "Clauses": [ { "$type": "project", "FieldList": [ "joinKey = tolower(name)", "ResourceName = properties.displayName", "ResourceType = type" ] } ] }, "LeftColumnName": "joinKey", "RightColumnName": "joinKey" },
    { "$type": "project", "FieldList": ["ResourceName", "ResourceType", "LastUsed"] },
    { "$type": "orderby", "FieldNamesAscDesc": { "LastUsed": "desc" } }
  ]
}
```

[download](https://raw.githubusercontent.com/Laskewitz/pac-admin-query/main/samples/recently-used/query.json)

## Run it

```bash
pac admin query --query-file samples/recently-used/query.json --output-type Json
```

## Sample output

Sanitized excerpt from a live tenant.

```json
{
  "totalRecords": 2363,
  "count": 20,
  "resultTruncated": 1,
  "skipToken": "ew0KICAiJGlkIjogIjEiLA0KICAiTWF4Um93cyI6IDEwMDAsDQogICJSb3dzVG9Ta2lwIjogMTAwMCwNCiAgIkt1c3RvQ2x1c3RlclVybCI6ICJodHRwczovL2s4cy1uZXUtb25lLXNmLmFyZy5jb3JlLndpbmRvd3MubmV0Ig0KfQ==",
  "data": [
    {
      "ResourceName": "Sample Agent 1",
      "ResourceType": "microsoft.copilotstudio/agents",
      "LastUsed": "2026-07-10T23:59:59Z"
    },
    {
      "ResourceName": "Sample Cloud Flow 2",
      "ResourceType": "microsoft.powerautomate/cloudflows",
      "LastUsed": "2026-07-10T23:59:59Z"
    },
    {
      "ResourceName": "Sample Cloud Flow 3",
      "ResourceType": "microsoft.powerautomate/cloudflows",
      "LastUsed": "2026-07-10T23:59:59Z"
    },
    {
      "ResourceName": null,
      "ResourceType": "",
      "LastUsed": "2026-07-10T23:59:59Z"
    },
    {
      "ResourceName": null,
      "ResourceType": "",
      "LastUsed": "2026-07-10T23:59:59Z"
    },
    {
      "ResourceName": null,
      "ResourceType": "",
      "LastUsed": "2026-07-10T23:59:59Z"
    },
    {
      "ResourceName": null,
      "ResourceType": "",
      "LastUsed": "2026-07-10T23:59:59Z"
    },
    {
      "ResourceName": null,
      "ResourceType": "",
      "LastUsed": "2026-07-10T23:59:59Z"
    },
    {
      "ResourceName": null,
      "ResourceType": "",
      "LastUsed": "2026-07-10T23:59:59Z"
    },
    {
      "ResourceName": null,
      "ResourceType": "",
      "LastUsed": "2026-07-10T23:59:59Z"
    },
    {
      "ResourceName": null,
      "ResourceType": "",
      "LastUsed": "2026-07-10T23:59:59Z"
    },
    {
      "ResourceName": null,
      "ResourceType": "",
      "LastUsed": "2026-07-10T23:59:59Z"
    },
    {
      "ResourceName": null,
      "ResourceType": "",
      "LastUsed": "2026-07-10T23:59:59Z"
    },
    {
      "ResourceName": null,
      "ResourceType": "",
      "LastUsed": "2026-07-10T23:59:59Z"
    },
    {
      "ResourceName": null,
      "ResourceType": "",
      "LastUsed": "2026-07-10T23:59:59Z"
    },
    {
      "ResourceName": null,
      "ResourceType": "",
      "LastUsed": "2026-07-10T23:59:59Z"
    },
    {
      "ResourceName": "Sample Agent Flow 17",
      "ResourceType": "microsoft.powerautomate/agentflows",
      "LastUsed": "2026-07-10T23:59:59Z"
    },
    {
      "ResourceName": null,
      "ResourceType": "",
      "LastUsed": "2026-07-10T23:59:59Z"
    },
    {
      "ResourceName": null,
      "ResourceType": "",
      "LastUsed": "2026-07-10T23:59:59Z"
    },
    {
      "ResourceName": null,
      "ResourceType": "",
      "LastUsed": "2026-07-10T23:59:59Z"
    }
  ],
  "facets": []
}
```

[download](https://raw.githubusercontent.com/Laskewitz/pac-admin-query/main/samples/recently-used/output.json)
