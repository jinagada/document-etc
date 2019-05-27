```
GET _cat/templates?v&s=name

GET _template/hunet

GET _template/sample-index-template

DELETE _template/sample-index-template

PUT _template/sample-index-template
{
  "order": 2,
  "index_patterns": [
    "sample-index*"
  ],
  "aliases": {},
  "settings": {
    "number_of_shards": 1,
    "number_of_replicas": 1,
    "index": {
      "analysis": {
        "analyzer": {
          "korean_analyzer": {
            "type": "custom",
            "tokenizer": "nori_user_dict",
            "filter": [
              "npos_filter",
              "nori_readingform",
              "lowercase",
              "custom_synonym_filter"
            ]
          },
          "ngram_analyzer": {
            "type": "custom",
            "tokenizer": "custom_ngram_tokenizer",
            "filter": [
              "lowercase",
              "trim"
            ]
          },
          "edge_ngram_analyzer": {
            "type": "custom",
            "tokenizer": "custom_edge_ngram_tokenizer",
            "filter": [
              "lowercase",
              "trim",
              "edge_nagram_filter_front"
            ]
          },
          "edge_ngram_analyzer_back": {
            "type": "custom",
            "tokenizer": "custom_edge_ngram_tokenizer",
            "filter": [
              "lowercase",
              "trim",
              "edge_nagram_filter_back"
            ]
          }
        },
        "filter": {
          "npos_filter": {
            "type": "nori_part_of_speech",
            "stoptags": [
              "E",
              "IC",
              "J",
              "MAG",
              "MAJ",
              "MM",
              "SP",
              "SSC",
              "SSO",
              "SC",
              "SE",
              "XPN",
              "XSA",
              "XSN",
              "XSV",
              "UNA",
              "NA",
              "VSV"
            ]
          },
          "custom_synonym_filter": {
            "type": "synonym_graph",
            "synonyms_path": "synonym.txt"
          },
          "edge_nagram_filter_front": {
            "type": "edgeNGram",
            "min_gram": 1,
            "max_gram": 50,
            "side": "front"
          },
          "edge_nagram_filter_back": {
            "type": "edgeNGram",
            "min_gram": 1,
            "max_gram": 50,
            "side": "back"
          }
        },
        "tokenizer": {
          "nori_user_dict": {
            "type": "nori_tokenizer",
            "user_dictionary": "userdic_ko.txt",
            "decompound_mode": "discard"
          },
          "custom_ngram_tokenizer": {
            "type": "ngram",
            "min_gram": 1,
            "max_gram": 50,
            "token_chars": [
              "letter",
              "digit",
              "punctuation",
              "symbol"
            ]
          },
          "custom_edge_ngram_tokenizer": {
            "type": "edge_ngram",
            "min_gram": 1,
            "max_gram": 50,
            "token_chars": [
              "letter",
              "digit",
              "punctuation",
              "symbol"
            ]
          }
        }
      }
    }
  },
  "mappings": {
    "doc": {
      "properties": {
        "goodsname_nm": {
          "type": "text",
          "fields": {
            "keyword": {
              "type": "keyword",
              "ignore_above": 256
            },
            "korean": {
              "type": "text",
              "analyzer": "korean_analyzer"
            },
            "ngram": {
              "type": "text",
              "analyzer": "ngram_analyzer",
              "search_analyzer": "ngram_analyzer",
              "boost": 3
            },
            "edge_ngram": {
              "type": "text",
              "analyzer": "edge_ngram_analyzer",
              "search_analyzer": "ngram_analyzer",
              "boost": 2
            },
            "edge_ngram_back": {
              "type": "text",
              "analyzer": "edge_ngram_analyzer_back",
              "search_analyzer": "ngram_analyzer",
              "boost": 1
            }
          }
        }
      }
    }
  }
}
```
