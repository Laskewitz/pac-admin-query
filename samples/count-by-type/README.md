# 📊 Resource count by type and location

How many resources of each type and location exist across the tenant? The simplest useful aggregate, and a good first query to confirm your auth profile is pointed at the right tenant.

## Query

```json
{
  "TableName": "PowerPlatformResources",
  "Clauses": [
    {
      "$type": "summarize",
      "SummarizeClauseExpression": {
        "OperatorName": "count",
        "OperatorFieldName": "ResourceCount",
        "FieldList": [
          "type",
          "location"
        ]
      }
    },
    {
      "$type": "orderby",
      "FieldNamesAscDesc": {
        "ResourceCount": "desc"
      }
    }
  ],
  "Options": {
    "Top": 500,
    "Skip": 0,
    "SkipToken": ""
  }
}
```

[`query.json` (raw)](https://raw.githubusercontent.com/Laskewitz/pac-admin-query/main/samples/count-by-type/query.json)

## Run it

```bash
pac admin query --query-file samples/count-by-type/query.json --output-type Json
```

## Sample output

Sanitized excerpt from a live tenant.

```json
{
  "totalRecords": 10,
  "count": 10,
  "resultTruncated": 1,
  "skipToken": null,
  "data": [
    {
      "type": "microsoft.powerplatformusage/usagerecords",
      "location": "unitedstates",
      "ResourceCount": 2352
    },
    {
      "type": "microsoft.powerplatformconnector/connectors",
      "location": "unitedstates",
      "ResourceCount": 1593
    },
    {
      "type": "microsoft.powerautomate/cloudflows",
      "location": "westus",
      "ResourceCount": 231
    },
    {
      "type": "microsoft.powerautomate/agentflows",
      "location": "westus",
      "ResourceCount": 217
    },
    {
      "type": "microsoft.powerapps/modeldrivenapps",
      "location": "unitedstates",
      "ResourceCount": 66
    },
    {
      "type": "microsoft.copilotstudio/agents",
      "location": "unitedstates",
      "ResourceCount": 46
    },
    {
      "type": "microsoft.powerplatform/environments",
      "location": "unitedstates",
      "ResourceCount": 19
    },
    {
      "type": "microsoft.powerapps/codeapps",
      "location": "unitedstates",
      "ResourceCount": 4
    },
    {
      "type": "microsoft.powerapps/apps",
      "location": "unitedstates",
      "ResourceCount": 1
    },
    {
      "type": "microsoft.powerplatform/environmentgroups",
      "location": "unitedstates",
      "ResourceCount": 1
    }
  ],
  "facets": []
}
```

[`output.json` (raw)](https://raw.githubusercontent.com/Laskewitz/pac-admin-query/main/samples/count-by-type/output.json)
