```
GET _snapshot/_status

GET _snapshot/_all

GET _snapshot/.logstash/_all

GET _cat/repositories?v

GET _cat/snapshots/.logstash?v&s=id

GET _cat/snapshots/metricbeat-6.5.4?v&s=id

DELETE _snapshot/sample-index/sample-index-2019-03-05

POST /_snapshot/sample-index/sample-index-2016/_restore
{
  "indices": "sample-index-2016",
  "ignore_unavailable": true,
  "include_global_state": false
}

GET /_snapshot/sample-index/sample-index-2016/_status
```
