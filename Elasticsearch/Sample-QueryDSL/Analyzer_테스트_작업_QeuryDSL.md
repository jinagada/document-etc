```
GET sample-index/_analyze
{
  "analyzer": "ngram_analyzer",
  "text": ["전환온도를 활용한 고객 Segmentation"]
}
GET sample-index/_analyze
{
  "analyzer": "edge_ngram_analyzer",
  "text": ["전환온도를 활용한 고객 Segmentation"]
}
GET sample-index/_analyze
{
  "analyzer": "edge_ngram_analyzer_back",
  "text": ["전환온도를 활용한 고객 Segmentation"]
}

GET sample-index/_analyze
{
  "analyzer": "korean_analyzer",
  "text": ["타이어가"]
}
```
