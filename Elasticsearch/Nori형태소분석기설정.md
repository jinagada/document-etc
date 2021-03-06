# 목차
- [Nori 형태소 분석기 설정](#user-content-nori-형태소-분석기-설정)
  - [Nori Plugin 설치](#user-content-nori-plugin-설치)
  - [사용자정의 사전(userdic_ko.txt) 설정](#user-content-사용자정의-사전userdic_kotxt-설정)
  - [동의어 사전(synonym.txt) 설정](#user-content-동의어-사전synonymtxt-설정)
  - [불용어 사전(stopwords.txt) 설정](#user-content-불용어-사전stopwordstxt-설정)
  - [Nori 형태소 분석기를 사용한 Index 생성 예제](#user-content-nori-형태소-분석기를-사용한-index-생성-예제)
  - [사용자정의 사전 내용 갱신 후 적용 방법](#user-content-사용자정의-사전-내용-갱신-후-적용-방법)
  - [Nori 형태소 분석기를 사용한 Template 생성 예제(Enroll List)](#user-content-nori-형태소-분석기를-사용한-template-생성-예제enroll-list)
  - [사용자 정의사전과 동의어 사전 테스트](#user-content-사용자-정의사전과-동의어-사전-테스트)
    - [decompound_mode 가 mixed 인경우](#user-content-decompound_mode-가-mixed-인경우)
    - [decompound_mode 가 discard 인경우](#user-content-decompound_mode-가-discard-인경우)
  - [NGram 테스트](#user-content-ngram-테스트)
  - [불용어 테스트](#user-content-불용어-테스트)
- [참고](#user-content-참고)

# Nori 형태소 분석기 설정
## Nori Plugin 설치
- 모든 노드서버에 Plugin 설치 해야함
- 설치 후 모든 노드서버의 Elasticsearch 재실행을 해야 적용됨
- 노드서버 재실행은 1대 씩 순차적으로 진행 할 것!!
```shell
# 디렉토리 이동
~$ cd ~/local/elasticsearch
 
# Nori Plugin 설치
elasticsearch$ bin/elasticsearch-plugin install analysis-nori
 
# Elasticsearch 서버 재실행
elasticsearch$ ./stop.sh
elasticsearch$ ./start.sh
```

## 사용자정의 사전(userdic_ko.txt) 설정
- 사용자정의 사전 파일은 모든 노드서버의 동일한 위치에 파일을 생성 하여야함!
- userdic_ko.txt 파일의 내용은 예제이며, 실제 서버에 설정 시 필요한 단어로만 설정 해야함
```shell
# 디렉토리 이동
~$ cd ~/local/elasticsearch/config
 
# userdic_ko.txt 파일 생성
config$ touch userdic_ko.txt
 
# file charset 확인
config$ file -i userdic_ko.txt
-- 출력 내용 -----------------------------------------------------------------------------------
userdic_ko.txt: inode/x-empty; charset=binary
-- 출력 내용 -----------------------------------------------------------------------------------
 
# vi 에디터로 열어 파일을 텍스트 파일로 변경
config$ vi userdic_ko.txt
-- 내용 수정 -----------------------------------------------------------------------------------
c++
C샤프
세종
세종시 세종 시
가곡역 가곡 역
-- 내용 수정 -----------------------------------------------------------------------------------
## :wq 로 변경 내용을 저장 후 나옴
 
# file charset 확인
config$ file -i userdic_ko.txt
-- 출력 내용 -----------------------------------------------------------------------------------
userdic_ko.txt: text/plain; charset=utf-8
-- 출력 내용 -----------------------------------------------------------------------------------
 
# charset 부분이 UTF-8 이 아닌경우 UTF-8 로 변경
config$ iconv -f us-ascii -t UTF-8//TRANSLIT userdic_ko.txt -o userdic_ko.txt
```

## 동의어 사전(synonym.txt) 설정
- 동의어 사전 파일은 모든 노드서버의 동일한 위치에 파일을 생성 하여야함!
- synonym.txt 파일의 내용은 예제이며, 실제 서버에 설정 시 필요한 단어로만 설정 해야함
```shell
# synonym.txt 파일 생성
config$ touch synonym.txt
 
# file charset 확인
config$ file -i synonym.txt
-- 출력 내용 -----------------------------------------------------------------------------------
synonym.txt: inode/x-empty; charset=binary
-- 출력 내용 -----------------------------------------------------------------------------------
 
# vi 에디터로 열어 파일을 텍스트 파일로 변경
config$ vi synonym.txt
-- 내용 수정 -----------------------------------------------------------------------------------
풋사과, 햇사과
플립러닝, 플립 러닝, flipped learning
-- 내용 수정 -----------------------------------------------------------------------------------
## :wq 로 변경 내용을 저장 후 나옴
 
# file charset 확인
config$ file -i synonym.txt
-- 출력 내용 -----------------------------------------------------------------------------------
synonym.txt: text/plain; charset=utf-8
-- 출력 내용 -----------------------------------------------------------------------------------
 
# charset 부분이 UTF-8 이 아닌경우 UTF-8 로 변경
config$ iconv -f us-ascii -t UTF-8//TRANSLIT synonym.txt -o synonym.txt
```

## 불용어 사전(stopwords.txt) 설정
- 불용어 사전 파일은 모든 노드서버의 동일한 위치에 파일을 생성 하여야함!
- stopwords.txt 파일의 내용은 예제이며, 실제 서버에 설정 시 필요한 단어로만 설정 해야함
```shell
# stopwords.txt 파일 생성
config$ touch stopwords.txt
 
# file charset 확인
config$ file -i stopwords.txt
-- 출력 내용 -----------------------------------------------------------------------------------
stopwords.txt: inode/x-empty; charset=binary
-- 출력 내용 -----------------------------------------------------------------------------------
 
# vi 에디터로 열어 파일을 텍스트 파일로 변경
config$ vi stopwords.txt
-- 내용 수정 -----------------------------------------------------------------------------------
양상민
-- 내용 수정 -----------------------------------------------------------------------------------
## :wq 로 변경 내용을 저장 후 나옴
 
# file charset 확인
config$ file -i stopwords.txt
-- 출력 내용 -----------------------------------------------------------------------------------
stopwords.txt: text/plain; charset=utf-8
-- 출력 내용 -----------------------------------------------------------------------------------
 
# charset 부분이 UTF-8 이 아닌경우 UTF-8 로 변경
config$ iconv -f us-ascii -t UTF-8//TRANSLIT stopwords.txt -o stopwords.txt
```

## Nori 형태소 분석기를 사용한 Index 생성 예제
```shell
curl -k -XPUT 'http://elastic-01:9200/search-nori-sample1_v1' -H 'Content-Type: application/json' -d '{
  "settings": {
    "number_of_shards": 5, <== shard 수 : 내용이 없으면 경고가 나타남
    "number_of_replicas": 1, <== 복제 수 : 내용이 없으면 경고가 나타남
    "index": {
      "analysis": {
        "analyzer": {
          "korean": { <== analyzer 명칭 : 사용자 설정
            "filter": [
              "npos_filter", <== 아래 filter에 설정된 분석 설정
              "nori_readingform", <== 한자를 한글로 변환하여 분석
              "lowercase", <== 영어는 전부 소문자로 분석
              "custom_synonym_filter", <== 동의어 필터
              "custom_stopwords_filter" <== 불용어 필터
            ],
            "tokenizer": "nori_user_dict" <== 아래 tokenizer에 설정된 tokenizer
          },
          "customNGram": { <== edge ngram 분석기 추가
            "filter": [
              "lowercase", <== 영어는 전부 소문자로 분석
              "trim", <== 문자의 앞뒤 공백제거
              "custom_stopwords_filter" <== 불용어 필터
            ],
            "tokenizer": "custom_ngram_tokenizer"
          }
        },
        "tokenizer": {
          "nori_user_dict": { <== tokenizer 명칭 : 사용자 설정
            "decompound_mode": "mixed", <== Elasticsearch 메뉴얼에서 제공된 내용 : mixed 단어를 분석 및 분해한 후 원 단어도 포함(예: 가곡역 => 가곡역, 가곡, 역)
            "type": "nori_tokenizer", <== 고정값
            "user_dictionary": "userdic_ko.txt" <== 사용자 정의 사전 위치 : <Elasticsearch Installed Directory>/config/userdic_ko.txt
          },
          "custom_ngram_tokenizer": { <== Edge NGram 설정
            "type": "edge_ngram",
            "min_gram": 1,
            "max_gram": 50, <== 영문을 위해 50자로 설정
            "token_chars": [ "letter", "digit", "punctuation", "symbol" ] <== 글자, 숫자, 기호($, &), 공백(whitespace, enter) 제외
          }
        },
        "filter": {
          "npos_filter": { <== filter 명칭 : 사용자 설정
            "type": "nori_part_of_speech", <== 형태소 분석에서 제외
            "stoptags": [
              "E", <== 구두문장
              "IC", <== 감탄사
              "J", <== Ending Particle : 조사(?), 어미(?)
              "MAG", <== 일반 부사
              "MAJ", <== 부역 부사
              "MM", <== Determiner : 한정사
              "SP", <== Space : 띄어쓰기
              "SSC", <== 닫는 괄호
              "SSO", <== 여는 괄호
              "SC", <== 구분 기호 (* / :)
              "SE", <== Ellipsis : 생략(...), 말줄임
              "XPN", <== 접두사
              "XSA", <== 형용사 접미사
              "XSN", <== 명사 접미사
              "XSV", <== 동사 접미사
              "UNA", <== 알수없는
              "NA", <== 알수없는
              "VSV" <== 알수없는
            ]
          },
          "custom_synonym_filter": { <== 동의어 필터 추가
            "type": "synonym_graph", <== 동의어 필터 type
            "synonyms_path": "synonym.txt" <== 동의어 사전 위치 : <Elasticsearch Installed Directory>/config/synonym.txt
          },
          "custom_stopwords_filter": { <== 불용어 필터 추가
            "type": "stop", <== 불용어 필터 type
            "stopwords_path": "stopwords.txt" <== 불용어 사전 위치 : <Elasticsearch Installed Directory>/config/stopwords.txt
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
          "analyzer": "korean", <== index 에서 설정한 analyzer 설정 적용
          "fields": {
            "keyword": {
              "type": "keyword", <== 전자 메일 주소, 호스트 이름, 상태 코드, 우편 번호 또는 태그와 같은 구조화 된 콘텐츠를 인덱싱하는 필드입니다.
              "ignore_above": 256 <== keyword 에서 색인할 최대 문자열 수 256 문자까지만 색인에 사용(권장값)
            }
          }
        },
        "content": {
          "type": "text",
          "analyzer": "korean", <== index 에서 설정한 analyzer 설정 적용
          "fields": {
            "keyword": {
              "type": "keyword",
              "ignore_above": 256
            },
            "ngram_txt": { <== NGram 필드 추가
              "type": "text",
              "analyzer": "customNGram"
            }
          }
        },
        "description": {
          "type": "text",
          "analyzer": "korean", <== index 에서 설정한 analyzer 설정 적용
          "fields": {
            "keyword": {
              "type": "keyword",
              "ignore_above": 256
            }
          }
        },
        "grade_avg": {
          "type": "float"
        },
        "grade_avg_custom": {
          "type": "float"
        }
      }
    }
  }
}'
```

## 사용자정의 사전 내용 갱신 후 적용 방법
- 단어가 추가된 이후 시점부터 저장되는 데이터에만 적용
- 사용자 정의 사전에 단어가 추가된 경우 모든 Node 서버에 동일한 단어가 추가 되어 있어야함
- 특정노드에 단어가 없는 경우 오동작함
```shell
# index 닫기
curl -k -XPOST 'https://elastic-01:9200/search-nori-sample1_v1/_close'
 
# index 열기
curl -k -XPOST 'https://elastic-01:9200/search-nori-sample1_v1/_open'

# 이미 저장된 모든 데이터에 대해 재색인
curl -k -XPOST 'https://elastic-01:9200/search-nori-sample1_v1/_update_by_query'
```

## Nori 형태소 분석기를 사용한 Template 생성 예제
```shell
curl -k -XPUT 'http://elastic-01:9200/_template/search-nori-sample1_v1-template' -H 'Content-Type: application/json' -d '{
  "index_patterns": [ <== 설정된 pattern 에 해당하는 index 생성 요청이 발생하면 현재의 Template 을 적용하여 index를 생성한다.
    "search-nori-sample1_v1-*"
  ],
  "aliases": { <== index 생성 시 설정된 alias 를 적용하여 생성한다.
    "search-nori-sample1_v1": {}
  },
  "settings": {
    "index": {
      "number_of_shards": "5",
      "number_of_replicas": "1",
      "analysis": {
        "analyzer": {
          "korean": {
            "filter": [
              "npos_filter",
              "nori_readingform",
              "lowercase",
              "custom_synonym_filter",
              "custom_stopwords_filter"
            ],
            "tokenizer": "nori_user_dict"
          },
          "customNGram": {
            "filter": [
              "lowercase",
              "trim",
              "custom_stopwords_filter"
            ],
            "tokenizer": "custom_ngram_tokenizer"
          }
        },
        "tokenizer": {
          "nori_user_dict": {
            "decompound_mode": "discard",
            "type": "nori_tokenizer",
            "user_dictionary": "userdic_ko.txt"
          },
          "custom_ngram_tokenizer": {
            "type": "edge_ngram",
            "min_gram": 1,
            "max_gram": 50,
            "token_chars": [ "letter", "digit", "punctuation", "symbol" ]
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
          "custom_stopwords_filter": {
            "type": "stop",
            "stopwords_path": "stopwords.txt"
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
          "analyzer": "korean", <== index 에서 설정한 analyzer 설정 적용
          "fields": {
            "keyword": {
              "type": "keyword", <== 전자 메일 주소, 호스트 이름, 상태 코드, 우편 번호 또는 태그와 같은 구조화 된 콘텐츠를 인덱싱하는 필드입니다.
              "ignore_above": 256 <== keyword 에서 색인할 최대 문자열 수 256 문자까지만 색인에 사용(권장값)
            },
            "ngram_txt": { <== NGram 필드 추가
              "type": "text",
              "analyzer": "customNGram"
            }
          }
        },
        "content": {
          "type": "text",
          "analyzer": "korean", <== index 에서 설정한 analyzer 설정 적용
          "fields": {
            "keyword": {
              "type": "keyword",
              "ignore_above": 256
            },
            "ngram_txt": { <== NGram 필드 추가
              "type": "text",
              "analyzer": "customNGram"
            }
          }
        },
        "description": {
          "type": "text",
          "analyzer": "korean", <== index 에서 설정한 analyzer 설정 적용
          "fields": {
            "keyword": {
              "type": "keyword",
              "ignore_above": 256
            },
            "ngram_txt": { <== NGram 필드 추가
              "type": "text",
              "analyzer": "customNGram"
            }
          }
        },
        "grade_avg": {
          "type": "float"
        },
        "grade_avg_custom": {
          "type": "float"
        }
      }
    }
  }
}'
 
# Enroll List 기본 템플릿 조회 - Hunet(2162)
curl -k -XGET 'http://elastic-01:9200/_template/search-nori-sample1_v1-template?pretty'
 
# Enroll List 기본 템플릿 삭제 - Hunet(2162)
curl -k -XDELETE 'http://elastic-01:9200/_template/search-nori-sample1_v1-template?pretty'
```

## 사용자 정의사전과 동의어 사전 테스트
### decompound_mode 가 mixed 인경우
- 동의어 설정 시 형태소 분석이 되지 않는 단어만 동의어 사전에 등록 가능함
- 오류 발생 예제
  - 동의어 사전 설정
  ```
  플립러닝, 플립 러닝, flipped learning
  ```
  - 사용자정의 사전 설정
  ```
  플립러닝 플립 러닝
  ```
  - 위와 같이 설정한 경우 Index 생성 시 아래의 오류가 발생함
  ```
  term: 플립러닝 analyzed to a token (플립) with position increment != 1 (get: 0)
  ```
- 오류 해결 예제
  - 동의어 사전 설정
  ```
  플립러닝, 플립 러닝, flipped learning
  ```
  - 사용자정의 사전 설정
  ```
  플립러닝
  ```
  - 위와 같이 설정 해야 오류가 발생하지 않음
  - mixed 모드로 사용할 경우 위와 같은 형태로 설정해야 하므로 신규 단어의 경우 형태소 분석이 되지 않을 수 있음

### decompound_mode 가 discard 인경우
- 동의어 설정과 사용자정의 단어 설정이 둘 다 동작함
- mixed 모드에서 발생하던 오류가 발생하지 않음
- 아래의 설정이 가능함
  - 동의어 사전 설정
  ```
  플립러닝, 플립 러닝, flipped learning
  ```
  - 사용자정의 사전 설정
  ```
  플립러닝 플립 러닝
  ```

## NGram 테스트
- Kibana DevTools 에서 실행
- 분석기 테스트
```shell
# Nori
GET search-nori-sample1_v1/_analyze
{
  "analyzer": "korean",
  "text": ["[테스트] Fundamental Sales Course FSC"]
}

# Edge NGram
GET search-nori-sample1_v1/_analyze
{
  "analyzer": "customNGram",
  "text": ["[테스트] Fundamental Sales Course FSC"]
}
```

- Elasticsearch SQL 테스트
```shell
# NGram
GET search-nori-sample1_v1/_search
{
  "query": {
    "match_phrase": {
      "goodsname_nm.ngram_txt": "테스"
    }
  },
  "_source": ["id", "process_nm"]
}

# Nori
POST _xpack/sql?format=txt
{
  "query": "SELECT goodsname_nm FROM \"search-nori-sample1_v1\" WHERE 1 = 1 AND MATCH('goodsname_nm', '테스') ORDER BY SCORE() asc"
}
```

- NGram 과 Edge NGram 테스트 결과
  - 단어의 입력 순서에 따라 조회된 결과는 NGram 방식 보다 Edge NGram 방식이 더 나은 결과를 보여줌
  - “전문가 과”라고 검색 시
    - NGram 은 “전문가”라는 단어가 있는 모든 데이터가 같이 조회됨
    - Edge NGram 은 “전문가 과”라는 단어가 있는 데이터만 조회됨
  - “token_chars” 설정 시 “whitespace” 가 포함되면 “term” 생성 시 전체 text에 대해 “term”이 생성되어 검색이 정상적으로 되지 않음
  - NGram(Edge 포함) 분석기의 경우 “match_phrase”, “match_phrase_prefix” 조회의 결과가 거의 차이가 없음

## 불용어 테스트
- Kibana DevTools 에서 실행
- 분석기 테스트
```shell
GET hunet-enroll-list-2162-2019-02-26-stopwords-1/_analyze
{
  "analyzer": "customNGram",
  "text": ["아시아의 허브, 싱가포르 정복을 위한 양상민 과장의 사투기!"]
}

GET hunet-enroll-list-2162-2019-02-26-stopwords-1/_analyze
{
  "analyzer": "korean",
  "text": ["아시아의 허브, 싱가포르 정복을 위한 양상민 과장의 사투기!"]
}
```

- 불용어 등록 : stopwords.txt
```shell
양상민
양
양상
```

- “양상민” 만 등록한 경우
  - korean 분석기에서는 해당 단어가 token 으로 분해되지 않음
  - customNGram 분석기에서는 “양”, “양상” 의 단어가 token 으로 분해되고, “양상민”은 token 으로 분해되지 않음
  - match 로 조회하는 경우 korean 분석기의 결과에서는 조회 되지 않으나, customNGram 분석기에서는 조회됨
    - “양”, “양상” 의 단어가 term 으로 저장되어 있어 조회가 가능함
- “양상민, 양, 양상” 으로 등록한 경우
  - korean 분석기에서는 여전히 해당 단어가 token 으로 분해되지 않음
  - customNGram 분석기에서는 “양”, “양상” 의 단어도 token 으로 분해되지 않음
  - match 로 조회하는 경우 korean, customNGram 분석기 모두에서 조회 되지 않음

# 참고
- https://www.elastic.co/guide/en/elasticsearch/plugins/current/analysis-nori-tokenizer.html
- https://www.elastic.co/guide/en/elasticsearch/plugins/current/analysis-nori-speech.html
- http://lucene.apache.org/core/7_4_0/analyzers-nori/org/apache/lucene/analysis/ko/POS.Tag.html
- https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-types.html
- https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-templates.html
- https://www.elastic.co/guide/en/elasticsearch/plugins/current/analysis-nori-tokenizer.html
- https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis-edgengram-tokenizer.html
- https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis-stop-tokenfilter.html
