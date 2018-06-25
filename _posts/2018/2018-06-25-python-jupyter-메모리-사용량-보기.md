---
layout: post
title: '[Python] Jupyter 메모리 사용량 보기'
date: '2018-06-25 13:10'
author: namioto
tags:
  - python
images: /files/covers/image
cover:
  title: null
  link: null
categories: []
---

# Jupyter Notebook 메모리 사용량 보기
1. `memory_profiler` 을 설치한다.

**SHELL**
```shell
pip install memory_profiler
```

OR

**Jupyter**
```Python
!pip install memory_profiler
```

2. `Jupyter`에서 외장 모듈을 불러온다
```Python
%load_ext memory_profiler
```

3. `Jupyter`에서 메모리 사용량 확인
```Python
%memit
```
> peak memory: 75.84 MiB, increment: 0.07 MiB
