---
layout: post
title: '[Python] Subprocessing - 외부 프로그램 실행하기'
author: namioto
date: '2018-03-27 13:28'
tags:
  - python
---

# Python Subprocess
파이썬을 쓰다보면 외부 프로그램을 실행 시켜야 하는 경우가 종종 있는데, 이 때 Subprocess 모듈을 쓰게 된다. API 문서나 예제들을 보면 조금 어렵게 쓰여 있지만 자세히 들여다보면 엄청 간단한 모듈이다.

> 내부에 `Popen` 클래스가 있으나, 이건 로우레벨의 클래스라고 생각하면 좋을 듯 하다.
>
> 특별한 경우가 아닌 이상 사용할 일이 적으므로 이 글에선 다루지 않겠다.

Subprocess 모듈의 기능은 크게 다음 두가지로 나눠 기억하면 된다.
- [**call**](#call)
- [**output**](#output)


 ## Call<a name='call'></a>
먼저 `Call`은 외부 프로그램을 호출해서 그 프로그램의 종료코드(Exit Code) 값을 받아온다고 생각하면 된다.
왠만한 프로그램은 프로그램 실행 결과에 따라 종료코드를 반환한다.

UNIX 시스템의 일반적인 종료코드는 정상 종료 일때 0을 반환하고 오류가 발생 했을 시, 0이 아닌 값을 반환한다.

보편적인 종료코드의 대한 정의는 https://wiki.kldp.org/HOWTO/html/Adv-Bash-Scr-HOWTO/exitcodes.html 를 참고하면 될 듯 하다.

어쨌거나 내가 실행 시킨 스크립트나 외부 프로그램이 정상 수행 되었는지를 판단하는데 이 종료코드를 이용할 수 있다.

Call 방식의 메소드는 다음과 같이 2가지가 존재한다.
- .call()
- .check_call()

`call()` 메소드는 종료코드를 리턴 값으로 가지며, `check_call()` 메소드는 종료코드가 정상이 아니면 `CalledProcessError` 예외를 발생 시키는 차이 밖에 없다.

즉, call()은 프로그램 수행 결과에 대해 직접 결과코드를 체크해야하는 것이고, check_call()은 비정상 종료시 예외가 발생하므로 예외 처리에 대한 로직을 작성 해 줘야 한다는 것이다.

call()의 인자는 다음과 같다. check_call() 또한 이와 동일하게 써도 무방하다.

```python
subprocess.call(args, *,
  stdin=None, stdout=None, stderr=None,
  shell=False, timeout=None)
```

- args : 명령행. String or List 타입을 인자로 받는다.
  ```python
    ##  파일 목록이 출력된다.
    subprocess.call('ls')

    ### 이 명령도 위와 동일
    subprocess.call(['ls'])

    ### 단 문자열로 아래와 같이 인자를 넣는 것은 안된다.
    #subprocess.call('ls -alh')

    ### 인자 전달 시엔 리스트 형태로 전달 해야한다.
    subprocess.call(['ls', '-alh'])
  ```
- shell : 별도의 서브 쉘을 실행 하고 그 쉘 위에서 명령을 실행 시키도록 함.
  > 위의 예제 중 `subprocess.call('ls -alh')` 명령행에 매개변수를 삽입하는 것이 안되는 이유는 subprocess 모듈이 해당 명령 구문을 해석하지 않고 바로 실행하기 때문이다.
  >
  > 이 명령 구문을 해석하는 건 쉘이 담당하며, `shell=True` 옵션을 활성화 시키면 쉘 기반 위에서 실행되므로 리스트 형태로 전달하지 않아도 된다.
  > ```python
  > subprocess.call('ls -alh', shell=True)
  > ```
- stdin, stdout, stderr : 표준 입출력에 대한 리다이렉션을 정의할 수 있다. 보통은 생략한다.
- timeout : 외부 프로그램을 호출하고, 해당 프로그램이 종료될 때까지 기다리게 되는데(단위:초), 이 값을 설정하면 해당 시간 만큼 대기 후 `TimeoutExpired` 예외를 내 뱉는다. (외부 프로그램이 행걸리는 상황에 대해 대비할 수 있다.)

## Output<a name='output'></a>
call은 종료코드를 리턴 값으로 받아왔다면 output은 프로그램의 출력 결과를 가져온다.

output은 `check_output()` 메소드만 존재한다.
위 내용을 정독했다면 이 메소드도 결과에 따라 예외를 발생시킨다는 것을 짐작 할 수 있을 것이다.

프로그램 종료코드 결과에 따라 `check_call()`과 동일한 `CalledProcessError` 예외를 발생시킨다.


```python
subprocess.check_output(args, *,
  stdin=None, stdout=None, stderr=None,
  shell=False, timeout=None, universal_newlines=False, ...)
```
기본적으로 `call()`, `check_call()` 과 동일한 파라미터인데, `check_output()`에만 있는 `universal_newlines`이라는 특별한 파라미터가 보인다.
- universal_newlines : (기본값 False) 표준입출력 파이프라인은 파일 형태로 되어있어, 프로그램 실행 결과가 바이너리 모드로 바이트 스트림 형태로 전달받게 된다. 이 파라미터가 True인 경우, [io.TextIOWrapper](https://docs.python.org/3/library/io.html#io.TextIOWrapper)을 통해 String 형태로 형변환된 결과를 받을 수 있도록 해 준다. 일반적인 경우라면 나는 이 파라미터를 항상 True에 두고 사용한다.
