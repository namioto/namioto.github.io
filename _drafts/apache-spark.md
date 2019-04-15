---
layout: "post"
title: "Apache Spark"
date: "2018-09-17 13:52"
author: namioto
tags: []
image: "/files/covers/image"
cover:
  title:
  link:
---

# Apache Spark 정리

- Map-Reduce & Hive는 중간 결과를 Disk 에 저장하는 데이터 프로세싱
  (요샌 Hive LLAP 등으로 메모리 캐싱...)
- Piccolo는 메모리 기반의 데이터 프로세싱
  - Fault-tolerance 때문에 성능이 저하되는 부분 발생
    - 메모리의 데이터가 망가짐을 대비하여 Replication 기능
    - Replication 으로 부족해서 Disk에 checkpointing.
    - Replication & checkpointing 하는 동안 연산 중단.

Spark는 위 둘의 장점을 합친 것.
- Spark 는 고속이면서 Fault-tolerance가 상대적으로 쉽고 성능에 영향을 안준다고 함..
- 왜 인지 이유 확인 해 볼것.
![spark1](/files/images/2018/09/spark1.png)


- Spark Application 개발은 아래의 3가지에 대한 이해를 바탕으로 시작
  - RDD
  - RDD 변환 API (Scala / Python / Java / R)
  - RDD 변환 API 로 만들어진 SQL/ML/Grpah/Stream Library (Scala / Python / Java / R)
위 내용 찾아 볼 것

![rdd](/files/images/2018/09/rdd.png)
`DAG`와 `Lineage`(계보) 확실하게 이해 할 것.

- 하나의 RDD는 여러 파티션으로 나누어짐. ????

### Lineage
- Lineage는 Fault-Tolerance 확보
  - Lineage만 기록해두면 동일한 RDD 생성가능
  - RDD의 복제본을 보관하기보다 lineage만 보관해도 복구가 가능.
  - 일부 계산 코스트가 큰 RDD는 디스크에 `Check Pointing` 을 한다.
  - 신뢰성(Reliability) 확보

- Lazy-execution 이 가능해짐
  - interpreter 상에서 transformation 명령을 읽을 땐 단순히 lineage만 생성
  - Action 명령이 읽히면 쌓여있던 lineage를 실행

- Job Scheduling 에 활용 가능
  - Lineage 가 그려진 상태에선 현재 자원(Source Data)이 배치된 상황과 앞으로 배치될 상황 및 의존성(Dependency) 등을 미리 계산해서 작업 분산이 가능.
  - 확장성(Scalability) 확보


### RDD와 DataFrame
Spark = RDD + interface

Spark 의 데이터를 저장하는 형태는 RDD와 DataFrame 형태 두가지로 존재한다.
- RDD (Resilient Distributed Datasets)
  ```python
  rdd.filter(line -> isString(..)).map(line => ...)
  ```
- DataFrame
  ```python
  df.sort().select().createOrReplaceTempView("blah blah")
  ```

RDD는 안정성 및 유연성이 높다고 함.
함수형 프로그래밍이 가능

DataFrame & SQL 은 성능이 높다고 함.
(Tungsten 엔진 사용)
![rdd_vs_dataframe](/files/images/2018/09/rdd-vs-dataframe.png)


위 두가지의 장점을 합친것이 Dataset
![dataset](/files/images/2018/09/dataset.png)
```python
ds.map(p->p.setAge(p.getAge+10))
ds.createOrReplaceTempView("blah")
```

장단점을 확실하게 알아 둘것.

###Spark 2.0
- 에서부터 Dataset, DataFrame 지원.
- SQL 2003 서포트
- R UDF 지원
