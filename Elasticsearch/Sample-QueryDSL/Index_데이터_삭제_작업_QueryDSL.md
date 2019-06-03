```
GET sample-index/_search
{
  "query": {
    "range": {
      "@timestamp": {
        "lte": "2019-05-29T09:01:00"
      }
    }
  },
  "sort": [
    {
      "@timestamp": {
        "order": "desc"
      }
    }
  ]
}

POST sample-index/_delete_by_query?wait_for_completion=true
{
  "query": {
    "range": {
      "@timestamp": {
        "lte": "2019-05-29T09:01:00"
      }
    }
  }
}
```
