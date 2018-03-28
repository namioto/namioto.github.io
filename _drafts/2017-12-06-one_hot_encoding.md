---
layout: post
title: 'One Hot Encoding'
author: namioto
date: 2017-12-05 14:13
tags: ['python', 'matplotlib', '한글']
image: /files/covers/matplotlib.png
---
# OneHot Encoding


```python
from matplotlib import font_manager, rc
font_url = "c:/Windows/Fonts/malgun.ttf"

font_name = font_manager.FontProperties(fname=font_url).get_name()
rc('font', family=font_name)
```
폰트가 저장된 경로를 확인해서 `font_url` 변수를 수정 해 주면 된다.
