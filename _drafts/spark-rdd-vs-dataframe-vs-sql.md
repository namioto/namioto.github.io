---
layout: "post"
title: "spark rdd vs dataframe vs sql"
date: "2019-01-22 18:59"
author: namioto
tags: []
image: "/files/covers/image"
cover:
  title:
  link:
---

https://dadk.tistory.com/74 - Dataframe이 짱임
> RDD가 DataFrames 및 SparkSQL보다 우수한 성능을 발휘했다고 나오는데 DataFrame 및 SparkSQL 테스트를 위해 RDD 에서 데이터 프레임으로 변환하는 과정때문이라고 Comment 로 지적. databricks 에서 SparkSQL csv reader 를 사용했다면 비교가 다를 것이라고 지적.


https://118k.tistory.com/669 - DataSet을 주목하라
> RDD는 Spark 1.0에서 소개
> Dataframe는 Spark 1.3에서 프로젝트 텅스텐의 일부로 소개
> Catalyst Optimizer에 의해 효율적으로 동작함

> DataSet 은 Spark 1.6에서 소개.
> RDD와 DataFrame의 장점을 모두 지원. 마찬가지로 Catalyst Optimizer을 지원.

https://www.popit.kr/spark2-0-new-features1-dataset/
> DataFrame 내부적인 동작 방식에는 Catalyst Optimizer를 통해 실행 시점에 최적화된 코드를 제공하고 있어 RDD 프로그래밍과 달리 언어(Scala, Java, Pytho, R)에 상관없이 동일한 성능을 보장한다.


https://databricks.com/blog/2015/02/17/introducing-dataframes-in-spark-for-large-scale-data-science.html

![](https://databricks.com/wp-content/uploads/2015/02/Screen-Shot-2015-02-16-at-9.46.39-AM-1024x457.png)


https://twowinsh87.github.io/data/2018/08/02/data-fcdes-spark-1/
### RDD(Resilient Distributed Datasets)
- RDD공식문서
- 클러스터 전체에서 공유되는 리스트, 메모리상에 올라가있음. (메모리 부족한 경우, 디스크에 spill)
- interface는 List와 거의 동일함
- map, reduce, count, filter, join 등 다양한 작업 가능
- 작업을 병렬적으로 처리
- 여러 작업을 설정해두고, 결과를 얻을 때 lazy하게 계산
- 만약에 20TB 이상의 데이터가 있다. load 등 작업 하나 하는데만 시간이 큼
- 실제 작업정보만 가지고 있다가. 미룰 수 없는 작업이 왔을 때 수행 = Actions
### List와 RDD차이?
- PC한대의 메모리로 처리할 수 있는 만큼만 처리
- RDD는 PC메모리를 넘어서는 양이라도 처리가 가능 -> 영속화 캐싱기능도 있음 (메모리를 넘는 자료 처리가 가능하다는 것은 정보만 가지고 있다가 실제 처리할 때 던짐)
### RDD 특징
- Transformation
- Actions
- Lineage : 클러스터 중 일부의 고장으로 실패하더라도, Lineage를 통해 데이터 복구
- Lazy Execution: Transformation에서는 계산을 수행하지않고, Action이 수행되는 시점에 데이터를 읽어들여서 계산을 시작함.
### Lineage
- 여기서 하둡, 맵리듀스와 차이: c1, c2, c3 에서 c3에서 문제가 생기면 다시 계산
- computing 비용때문에 재계산 문제시에 특정 작업단계에서 임시파일로 저장하는 것을 고려하기도 함.

***

df와 sql은 완전 동일
내부 최적화 과정이 동일하여 차이가 없음

그럼 어떤 차이가 있을까
다음의 두 코드의 퍼포먼스는 동일

```Python
geoip_df.selec('ip', 'contry')\
.where('contry="russia"')\
.show(3)
```
```sql
spark_session.sql("""
  selec ip, country
  from geoip
  where country='russia'
""")
```

하지만 위 코드엔 에러가 존재. `select`를 써야하는데 `selec` 로 썼음.
Dataframe API에선 코드를 실행하보지 않고서도 에러를 잡아낼 수 있다면,
SparkSQL에선 실행해봐야 에러를 확인 할 수 있음.
