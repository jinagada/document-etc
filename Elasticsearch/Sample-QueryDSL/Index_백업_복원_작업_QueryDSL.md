```
GET _snapshot/_status

GET _snapshot/_all

GET _snapshot/.logstash/_all

GET _cat/repositories?v

GET _cat/snapshots/.logstash?v&s=id

GET _cat/snapshots/metricbeat-6.5.4?v&s=id

DELETE /_snapshot/sample_index_backup-1

PUT /_snapshot/sample_index_backup
{
    "type": "fs",
    "settings": {
        "location": "sample_index_backup"
    }
}

DELETE _snapshot/sample_index_backup/sample_backup_20190131

PUT /_snapshot/sample_index_backup/sample_backup_20190308?wait_for_completion=true
{
  "indices": ".kibana*,.logstash*,bigginsight,myindex",
  "ignore_unavailable": true,
  "include_global_state": false
}

DELETE _snapshot/sample-index/sample-index-2019-03-05

POST /_snapshot/sample-index/sample-index-2016/_restore
{
  "indices": "sample-index-2016",
  "ignore_unavailable": true,
  "include_global_state": false
}

GET /_snapshot/sample-index/sample-index-2016/_status
```
