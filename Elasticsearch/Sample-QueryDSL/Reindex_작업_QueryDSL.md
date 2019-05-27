```
DELETE hunet-prime-goods-info-test-1

GET _tasks?detailed=true&actions=*reindex

POST _reindex
{
  "source": {
    "index": "sample-index-2016-1"
  },
  "dest": {
    "index": "sample-index-2016",
    "op_type": "create"
  },
  "conflicts": "proceed"
}
```
