---
layout: "post"
title: "and 와 &의 차이"
date: "2018-01-08 14:07"
author: namioto
tags: []
images: "/files/covers/image"
---

http://hashcode.co.kr/questions/4967/a-a-and-b%EB%8A%94-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80%EC%9A%94

파이썬의 and와 &는 우선 같다고 보시면 됩니다.

True and True = True
False and True = False
True and False = False
False and False = False
1 and 1 = 1
0 and 0 = 0
0 and 1 = 0
1 and 0 = 0
& 도 동일합니다.

True & True = True
False & True = False
True & False = False
False & False = False
1 & 1 = 1
0 & 0 = 0
0 & 1 = 0
1 & 0 = 0
그럼 and와 &의 차이점은 무엇일까요? 바로 &는 type까지 체크하는 것이 다릅니다.

1 and 1.0 = 1
1 & 1.0 = Type Error
version and int(version)는 왜 저렇게 했는지 이유를 알 수가 없군요.
