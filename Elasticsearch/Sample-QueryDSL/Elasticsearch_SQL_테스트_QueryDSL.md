```
POST _xpack/sql?format=txt
{
  "query": """
  select cate1_nm.keyword, count(*)
  from "sample-index"
  where insert_date = '2019-05-21'
  group by cate1_no.keyword, cate1_nm.keyword
  """
}

POST _xpack/sql/translate
{
  "query": """
  select cate1_nm.keyword, count(*)
  from "sample-index"
  where insert_date = '2019-05-21'
  group by cate1_no.keyword, cate1_nm.keyword"""
}
```
