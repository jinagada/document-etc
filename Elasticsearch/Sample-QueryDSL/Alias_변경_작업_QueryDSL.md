```
POST _reindex
{
  "source": {
    "index": "sample-index-2019-03-06"
  },
  "dest": {
    "index": "sample-index-2019-03-05"
  }
}

POST _aliases?pretty
{
  "actions": [
    {
      "remove": {
        "index": "sample-index-*",
        "alias": "sample-index"
      }
    },
    {
      "add": {
        "index": "sample-index-2019-03-05",
        "alias": "sample-index"
      }
    }
  ]
}
```
