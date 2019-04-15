---
layout: "post"
title: "pyspark_file_read"
date: "2019-01-22 14:57"
author: namioto
tags: []
image: "/files/covers/image"
cover:
  title:
  link:
---

zeppelin
```sh
%#!/bin/sh
hdfs dfs -ls /EVENT_LOG_TEXT/NHN_ENT_ADID/
```
> Found 303 items
-rw-r--r--   3 shwhite   hdfs     299151 2018-03-19 13:05 /EVENT_LOG_TEXT/NHN_ENT_ADID/thinkware_adid_demo_20180227.txt
-rw-r--r--   3 shwhite   hdfs    4713058 2018-03-19 13:05 /EVENT_LOG_TEXT/NHN_ENT_ADID/thinkware_adid_demo_20180311.txt
-rw-r--r--   3 shwhite   hdfs    4476042 2018-03-19 13:05 /EVENT_LOG_TEXT/NHN_ENT_ADID/thinkware_adid_demo_20180312.txt
-rw-r--r--   3 shwhite   hdfs    4575587 2018-03-19 13:05 /EVENT_LOG_TEXT/NHN_ENT_ADID/thinkware_adid_demo_20180313.txt
-rw-r--r--   3 shwhite   hdfs    4640689 2018-03-19 13:05 /EVENT_LOG_TEXT/NHN_ENT_ADID/thinkware_adid_demo_20180314.txt
-rw-r--r--   3 shwhite   hdfs    4394643 2018-03-19 13:05 /EVENT_LOG_TEXT/NHN_ENT_ADID/thinkware_adid_demo_20180315.txt

### 로드 방법
```scala
val rdd = sc.textFile("test.json")
```
위 방법도 존재하나, `spark_session.read.load()` 또는 `spark_session.read.text()`로 읽자.
sparkContext.textFile()은 `RDD` 객체로 리턴하기 때문에 경우에 따라 `.toDF()`로 DataFrame로 변환해야 할 수도 있다.

해외 벤치마크 자료를 보면 이 오버헤드로 인해 RDD가 퍼포먼스가 좋은것처럼 평가되는 경우도 있음.
> https://community.hortonworks.com/articles/42027/rdd-vs-dataframe-vs-sparksql.html

하지만 바로 DataFrame으로 읽는다면 DataFrame의 성능이 더 우수하다는 이야기도 존재.
> https://dadk.tistory.com/74

이렇게 불러온다.
```python
df = sqlContext.read.text("/EVENT_LOG_TEXT/NHN_ENT_ADID/thinkware_adid_demo_20180227.txt")
df.show(truncate=False)
```

구분자가 존재하는 csv 형식의 데이터라면 csv 로 읽는 것이 맞다.
`zeppelin` 0.7 버전에서 csv 관련 오류가 발생해서 이런 편법을 쓰는 것.
csv 로 읽는 방법은 다음 3가지가 존재.
#### method1
```Python
df = sqlContext.read.format("com.databricks.spark.csv")
  .option('header', 'true').load("some.csv")
```
#### method2
format을 축약된 형태로 사용한다.
```Python
df = sqlContext.read.format(“csv”)
  .option(“header”,”true”)
  .option(“mode”,”DROPMALFORMED”)
  .option("delimeter", "\t")
  .load(“C:/Users/Ajay/Desktop/test/new_sample.csv”)
```

#### method3
`load()` 대신 `csv()`를 사용하면서 format을 아얘 생략
```Python
df = sqlContext.read
  .option(“header”,”true”)
  .option(“mode”,”DROPMALFORMED”)
  .option("delimeter", "\t")
  .csv(“C:/Users/Ajay/Desktop/test/new_sample.csv”)
```


### Split Column
zeppelin 0.7에서 csv parser가 에러를 내뿜으니(버전 miss match 인데 서버를 고치기 귀찮을때..)
text()로 읽어서 컬럼을 split 하는 방법을 사용.

이 것도 방법이 많다.

### method1
임시테이블에 등록하는 해서 SQL로 처리하는 방법.
pyspark 에 능숙하지 않아도 쉽게 구현이 가능한 장점.
```Python
df.createOrReplaceTempView('temp_view')

split_df = sqlContext.sql("""
  SELECT arr[0] as foo,
    arr[1] as bar
  FROM (
    SELECT SPLIT(value, ',') as arr FROM temp_view
  ) A
""")
split_df.write.saveAsTable("foo_bar")
```

### method2
rdd.map() 을 통해 split() 하는 방법
dataframe.map()안의 lambda 함수는 list 또는 set 객체를 반환해야한다.

여기서 rdd는 dataframe 처럼 컬럼으로 구성되어 있지 않기 때문에 index로 접근해야한다.
```Python
# x[0]은 value 컬럼을 지칭.
conv_df = df.rdd.
  map( lambda x: x[0].split('\t') )
  .toDF(['foo','bar'])
conv_df.show(20, False)
```

### method3
expr 을 import 해서 사용
```python
from pyspark.sql.functions import expr
df.select(expr("split(value, '\t')")).show(truncate=False)
```
