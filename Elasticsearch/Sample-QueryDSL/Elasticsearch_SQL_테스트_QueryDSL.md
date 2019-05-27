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

POST _xpack/sql?format=txt
{
  "query": """
  select admin_rating,
         cate1_etc, cate1_nm, cate1_no,
         cate2_etc, cate2_nm, cate2_no,
         cate3_etc, cate3_nm, cate3_no,
         detailimage_file, goods_id,
         goodsname_nm, intro_nm,
         listimage_file,
         listimage_file_no_text,
         reg_date,
         series_goods_id,
         series_goodsname_nm,
         study_time,
         user_rating,
         COUNT(user_id) as view_cnt
  from "sample-index"
  where insert_date = '2019-05-27'
  group by admin_rating,
         cate1_etc, cate1_nm, cate1_no,
         cate2_etc, cate2_nm, cate2_no,
         cate3_etc, cate3_nm, cate3_no,
         detailimage_file, goods_id,
         goodsname_nm, intro_nm,
         listimage_file,
         listimage_file_no_text,
         reg_date,
         series_goods_id,
         series_goodsname_nm,
         study_time,
         user_rating
  order by COUNT(user_id)
  """
}

POST _xpack/sql/translate
{
  "query": """
  select admin_rating,
         cate1_etc, cate1_nm, cate1_no,
         cate2_etc, cate2_nm, cate2_no,
         cate3_etc, cate3_nm, cate3_no,
         detailimage_file, goods_id,
         goodsname_nm, intro_nm,
         listimage_file,
         listimage_file_no_text,
         reg_date,
         series_goods_id,
         series_goodsname_nm,
         study_time,
         user_rating,
         COUNT(user_id) as view_cnt
  from "sample-index"
  where insert_date = '2019-05-27'
  group by admin_rating,
         cate1_etc, cate1_nm, cate1_no,
         cate2_etc, cate2_nm, cate2_no,
         cate3_etc, cate3_nm, cate3_no,
         detailimage_file, goods_id,
         goodsname_nm, intro_nm,
         listimage_file,
         listimage_file_no_text,
         reg_date,
         series_goods_id,
         series_goodsname_nm,
         study_time,
         user_rating
  """
}
