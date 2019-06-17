---
layout: "post"
title: "Python Asyncio 코루틴 공부"
date: "2019-05-17 11:46"
author: namioto
tags: []
image: "/files/covers/image"
cover:
  title:
  link:
---

# Asyncio
Python 3.4에 새로 추가된 라이브러리
비동기 IO를 통해 코드를 동시에 돌릴 수 있음.

Generator & Coroutine에 대해 이해해야 함.
파이썬엔 `yield` 키워드 존재.

yield를 만나면 해당 함수와 함수의 Context를 포함한 `Generator` 객체를 반환함.

`Generator` 객체는 iterable 한 객체.

```
for i in range(5):
    print(i, end=' ')
# => 0 1 2 3 4

def custom_range(end):
    i = 0
    while i < end:
        yield i
        i += 1

gen = custom_range(5)  # => <generator object custom_range>
for i in gen:
    print(i, end=' ')
# => 0 1 2 3 4

list(custom_range(5))  # => [0, 1, 2, 3, 4]

# >>> gen = custom_range(2)
# >>> next(gen)
# 0
# >>> next(gen)
# 1
# >>> next(gen)
# Traceback (most recent call last):
#  File "<stdin>", line 1, in <module>
# StopIteration
```

yield 키워드에 도달하면 함수를 일시 중단하고 next를 호출해 줄때 다시 해당 라인으로 진입함.
이를 코루틴, 진입점이 여러개인 함수라고 부름.

일반적으로 순서대로 처리하다 return 코드를 만나고 결과를 리턴하는 방식을 서브루틴(subroutine)이라고 함.

> 하나의 함수에 대하여 기본 출입문과 별도로 개구멍이 하나 더 있는게 코루틴.
> 그러다보니 함수 실행 중간에 중간결과 값을 반환할 수 있고 새로운 인자를 넘길수도 있음.
>
