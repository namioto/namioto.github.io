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

위 코드는 Windows에 맞춰서 경로가 잡혀있지만 맥 또는 리눅스라면 폰트가 저장된 경로를 확인해서 `font_url` 변수를 수정 해 주면 된다.
