```
GET _cat/indices?v&s=index

GET _nodes/hot_threads

GET _cat/aliases?v&s=alias,index

- 서버 설정
PUT _cluster/settings
{
	"transient": {
		"cluster.routing.use_adaptive_replica_selection": true
	}
}
```
