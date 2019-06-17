---
layout: "post"
title: "Apache Zeppelin json/csv 처리시 common.lang3 에러에 대해"
date: "2019-04-16 14:50"
author: namioto
tags: []
image: "/files/covers/image"
cover:
  title:
  link:
---

```sh
/usr/hdp/current/spark2-client/python/lib/py4j-0.10.7-src.zip

export PYSPARK_PYTHON=/usr/local/bin/python3;${SPARK_HOME}/python/lib/py4j-0.10.7-src.zip;${SPARK_HOME}/python/lib/pyspark.zip
export PYSPARK_DRIVER_PYTHON=python3

export PYTHONPATH="${SPARK_HOME}/python:${SPARK_HOME}/python/lib/py4j-0.10.7-src.zip:${SPARK_HOME}/python/lib/pyspark.zip"
export SPARK_YARN_USER_ENV="PYTHONPATH=${PYTHONPATH}"
```

https://arclab.tistory.com/128
