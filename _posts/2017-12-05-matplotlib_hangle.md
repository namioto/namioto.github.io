---
layout: post
title: '[Python] Matplotlib에 한글 출력하기'
author: namioto
date: 2017-12-05 14:13
tags: ['Python', 'matplotlib', '한글']
image: /files/covers/matplotlib.png
---
# matplotlib에 한글 출력 하기

matplotlib에 한글을 출력하는 경우, 폰트 문제로 한글이 깨져서 출력될 수 있다.
이런 경우 다음의 코드를 넣어주면 한글이 정상적으로 출력된다.

```python
from matplotlib import font_manager, rc
font_url = "c:/Windows/Fonts/malgun.ttf"

font_name = font_manager.FontProperties(fname=font_url).get_name()
rc('font', family=font_name)
```

위 코드는 Windows에 맞춰서 경로가 잡혀있지만 맥 또는 리눅스라면 폰트가 저장된 경로를 확인해서 `font_url` 변수를 수정 해 주면 된다.
