---
layout: post
title: Python - Garbage Collection
categories: [Python]
excerpt_separator:  <!--more-->
---

## 개요
C언어나 C++ 같은 unmanaged language는 메모리를 할당하고 해제하는 것을 프로그래머가 직접 해야 했다. 이러한 방식은 퍼포먼스적 이득, 포인터의 사용 등 프로그래머에게 편의를 주는 부분도 많았으나 메모리 누수, 해제된 메모리 참조 등 많은 문제점을 드러내게 되었고 프로그래머들은 대안을 찾기 시작했다. 그 결과 언어 자체에서 메모리 할당과 해제를 알아서 하도록 하고 프로그래머는 이에 관여하지 않는 managed language들이 속속 만들어지기 시작했다. 

Java, C#, Javascript 등의 언어들이 managed 언어이며, Python 역시 managed 언어의 한 종류이다. 이러한 managed 언어에는 사용이 완료된 메모리의 해제를 위한 Garbage Collector (쓰레기 수집기)라는 기능이 도입되어 있다. 

이 포스트에서는 Python의 Garbage Collector가 어떻게 작동하는지 알아보도록 할 것이다.
- - -
### 왜 사용하는가?
컴퓨터의 메모리는 현실의 주소처럼 분할되어 각각의 고유 주소를 부여받게 된다. 프로그래밍 언어는 객체들을 생성할 때, 이 메모리의 주솟값을 할당받아 객체를 저장하며 이 객체들을 사용하여 프로그램은 작업을 수행하게 된다. 

초기의 프로그래밍 언어인 C언어나 C++ 같은 언어들은 이 작업을 전부 프로그래머들이 진행해야 했다. 객체를 담기 위한 메모리를 직접 할당받고, 할당받은 주소를 포인터로 사용한 다음, 다 사용하고 난 다음에는 사용을 마쳤으니 할당을 해제하여 자유롭게 만드는 것까지 모두 신경을 써 줘야 했던 것이다. 이 방식은 2가지의 문제점을 발생시키게 되었다.

1. 할당된 메모리를 사용 완료한 후에도 해제하지 않음
 * 사용이 끝나서 더 이상 참조하지 않는 메모리 구역을 할당 해제하지 않으면 메모리의 누수가 생기게 되어 점점 프로그램이 점유하는 메모리의 영역이 늘어났고, 오래 작동시켜야 하는 프로그램의 경우에는 치명적인 문제를 발생시킬 수 있다.
2. 이미 할당이 해제된 메모리를 참조하려고 한다
 * 이미 해제되어 아무 값도 없거나, 다른 값이 할당되어 있거나, 혹은 타 프로그램에게 할당되어 있는 등 참조하지 않아야 하는 메모리 구역을 참조해버리면, 데이터가 손상되거나 프로그램이 에러를 발생시켜버리는 등 문제를 일으킬 수 있다.

프로그래머도 결국은 사람이기 때문에, 이런 메모리 할당 및 해제 문제는 늘 문제가 되는 사항이었고 실제 버그가 발생하면 디버그 하기도 쉽지 않다는 문제점이 있었다. 그래서 프로그래머들은 이런 메모리 할당 문제를 자동으로 처리해 주는 방식을 고안하게 되었고, 사용이 끝나고 방치된 메모리 영역, 즉 쓰레기가 된 메모리를 자동으로 조사하여 할당을 해제하게 하는 Garbage Collection (쓰레기 수집)이라는 기능을 개발하여 프로그래밍 언어에 내장하게 되었다.
- - -
### 자동 메모리 관리의 방법
처음 쓰레기 수집이 도입될 때는 다소 무식한 방법을 썼다. 프로그램에 할당된 메모리를 주기적으로 전수조사해서, 접근이 되지 않는 메모리는 전부 쓰레기로 간주하고 할당을 해제시키는 방식이었는데 이 방법을 사용하기 위해서는 프로그램을 아예 멈춰놓고 작업을 진행시켜야 했으므로 프로그램이 뚝뚝 끊기는 문제점이 발생하게 되었다.

따라서 이러한 문제점을 개선하기 위해 조금씩 조금씩 구역을 나누어 진행하는 점진적 방법이 도입되었고, 프로그램의 메모리 사용에 대한 패턴을 조사해보니 대부분의 객체는 생성되어 한두 번 사용된 뒤 바로 해제가 되며 오래 남아 사용되는 객체는 무척 적다는 경향성을 발견하게 되었다.

이러한 경향성을 반영하여 제안된 것이 세대별 쓰레기 수집 (Generational Garbage Collection)이다. 객체가 처음 만들어질 때는 New Object로 분류해놓다가, 해당 객체가 오래 살아남으면 Old 쪽으로 분류를 넘겨버리는 방식을 사용하기 시작했던 것이다. 이렇게 분류를 하면 New 영역 위주로 쓰레기 수집을 작동시키면 되므로 좀 더 효율적인 메모리 관리를 할 수 있었다.

자동 메모리 관리의 또 다른 방법은 객체의 참조된 횟수를 카운트하는 방식이다. (Reference Counting)    

객체를 생성할 때, integer 변수를 0으로 하여 같이 할당한 다음, 해당 객체가 참조 될 때마다 +1, 참조가 해제될 때마다 -1을 하도록 한 뒤 0이 되는 순간 객체를 사용하지 않는 쓰레기로 취급하고 해체하는 방식이 Reference Counting이다. 
- - -
#### 자동 메모리 관리의 한계
물론 어느 방법을 선택하던지 쓰레기 수집을 진행할 때 프로그램의 성능 하락은 피할 수 없다. 참조 횟수를 카운팅 하기 위해서는 작업이 진행될 때마다 객체에 접근하여 기록을 해줘야 한다는 문제점이 있고, 세대별 쓰레기 수집도 기존 방식에 비해 성능 하락이 일어나는 간격이나 길이를 조절할 수 있을 뿐, 성능 하락 자체는 막을 수 없다. 

하지만 이러한 단점이 있음에도 자동 메모리 관리를 도입함으로써 얻어지는 생산성의 이득이 더 컸고, 떨어지는 퍼포먼스는 하드웨어의 퍼포먼스 상승으로 극복한다는 개념이 프로그래머들 사이에 자리 잡게 되었고, Garbage Collection이 계속 사용되는 이유가 되었다.
- - -
### Python의 Garbage Collection
Python은 위의 쓰레기 수집 방식 2가지(참조 횟수, 세대별 수집)를 같이 사용한다.
- - -
#### Reference Counting (참조 횟수 이용)
Python의 객체에는 참조가 얼마나 되고 있는지를 기록하는 변수가 같이 할당되어 있는데 sys 모듈의 getrefcount() 함수를 사용하면 이를 확인할 수 있다.
```python
import sys


# a, b, c에 같은 문자열을 할당
>>> a = b = c = "test-string"
# 'test-string'이라는 문자열 객체를 변수 a, b, c가 참조하고 있고, 
# sys.getrefcount() 함수가 참조했으므로 참조 횟수는 4
>>> sys.getrefcount(a)
4

# 변수를 하나씩 삭제할 때마다 참조 횟수가 줄어드는 것을 확인할 수 있다.
>>> del c
>>> sys.getrefcount(a)
3
>>> del b
>>> sys.getrefcount(a)
2

# 변수의 참조가 늘어나면 참조 횟수도 같이 늘어난다.
>>> a_list = []
>>> a_list.append(a)
>>> sys.getrefcount(a)
3
>>> a_dict = {}
>>> a_dict[a] = "reference_test"
>>> sys.getrefcount(a)
4
```
이와 같이 작동하다가 참조 횟수가 0이 되면, 해당 객체를 할당 해제하는 것이다.
- - -
#### Generational Garbage Collection (세대별 쓰레기 수집)
Python은 세대별 쓰레기 수집도 같이 사용하고 있는데, 참조 횟수를 카운팅 하는 것만으로는 쓰레기 수집을 완전하게 할 수 없기 때문이다. 객체가 자기 자신을 참조하거나, 두 객체가 서로를 각자 참조하도록 하면 이미 사용이 완료된 메모리 영역이지만 참조 횟수가 0이 아니기 때문에 해제가 불가능하기 때문이다.
```python
class A:
    def reference(self, obj):
        self.b = obj

class B:
    def reference(self, obj):
        self.a = obj

>>> a = A()
>>> b = B()
>>> a.reference(b)
>>> b.reference(a)
>>> del a, b
```
이런 참조가 진행되면 a 객체에서는 b를, b 객체에서는 a를 참조하므로 이후 이 두 객체가 삭제되더라도 참조 횟수만으로는 해제를 할 수 없어진다.

따라서 세대별 쓰레기 수집을 같이 사용하게 되는데, 이는 Python의 gc 모듈로 확인이 가능하다.
- - -
##### 세대별 쓰레기 수집의 개념
세대별 쓰레기 수집을 이해하기 위해서는 두 가지의 주요 개념을 이해하여야 한다. 
1. 세대 (Generation)
2. 임곗값 (Threshold Value)

앞에서도 설명했듯 객체가 생성될 시 거의 대부분의 객체는 사용 후 버려지며 오래 사용되는 객체는 많지 않다는 가설을 기반으로 작동되는 알고리즘이다.

Python에서 새롭게 객체를 만들게 되면, 일단 Garbage Collector의 0세대 영역에 할당이 된다. 또한 이 쓰레기 수집기에는 세대별 임곗값이 존재하는데, 0세대의 임곗값에 객체 카운트가 도달하게 되면 자동으로 쓰레기 수집을 진행하고, 여기서 살아남은 객체는 1세대로 옮겨지며 1세대의 객체 카운트가 +1이 된다. 이와 같은 방법으로 0~2세대의 3단계 방법으로 객체를 관리하게 된다.

```python
import gc


# 쓰레기 수집기의 임곗값을 얻는 함수
>>> gc.get_threshold()
(700, 10, 10)
```
기본적으로 설정되어 있는 임곗값은 (700, 10, 10)이다.

```python
# 현재 객체의 카운트를 얻는 함수
>>> gc.get_count()
(581, 2, 1)
# 수동 쓰레기 수집
>>> gc.collect()
563
>>> gc.get_count()
(18, 0, 0)
```
이 예제 코드에는 0세대에 581, 1세대에 2, 2세대에 1개의 객체가 존재한다. 이를 "gc.collect()" 함수를 사용하여 수동으로 수집하면 위와 같이 쓰레기 수집이 완료되고 객체 수가 (18, 0, 0)으로 변하게 된다. 563개의 객체가 0~2세대에서 정리가 되고, 3개의 객체가 0세대에 추가되었다.

한편 "gc.set_threshold()" 함수를 사용하여 쓰레기 수집기의 임곗값을 변경할 수도 있다.
```python
>>> gc.get_threshold()
(700, 10, 10)
>>> gc.set_threshold(900, 20, 20)
>>> gc.get_threshold()
(900, 20, 20)
```
쓰레기 수집기의 객체 임곗값을 늘려주었는데, 이와 같이 임곗값을 늘리면 쓰레기 수집기의 특성상 쓰레기가 된 객체가 좀 더 오래 살아남게 되지만 쓰레기 수집기의 작동 빈도는 줄어들어서 성능 하락의 빈도는 줄어들게 될 것이다.
- - -
### Python을 쓰면서 쓰레기 수집 설정을 수정, 또는 해제해야 하는가?
결론부터 말하자면, Python의 쓰레기 수집 설정은 건드리지 않는 것이 일반적이다. 고급 언어인 Python의 경우 막강한 생산성이 강점인데, 수동 쓰레기 수집기 설정을 사용하는 것보다 성능 하락을 일으키는 병목현상이 발생하는 구간을 C, C++ 등의 언어로 따로 짜서 붙이거나, 시스템 성능을 올리는 편이 생산성에 더 도움이 되기 때문이다.

Python의 프레임워크인 Django를 쓰는 Instagram이 쓰레기 수집기를 완전히 비활성화 시킨 것 같은 예외 케이스도 분명히 존재하나, Instagram의 경우에는 전 세계적으로 수백만 명의 사용자들이 이용하는 웹 어플리케이션이며 쓰레기 수집기 설정까지 건드릴 정도로 성능을 끌어내기 위한 동기가 분명하다는 점이 있다. 일반적인 개발과정에서는 Python에서 제공하는 기본적인 쓰레기 수집만으로 충분할 것이다.
- - -
## 정리
메모리 할당 및 해제 문제를 해결하기 위해 쓰레기 수집기 개념이 고안되었고, 쓰레기 수집기는 객체를 주기적으로 추적하거나, 작업이 진행될 때마다 참조 횟수를 카운트하여 메모리 해제를 자동으로 수행하게 만들었다. 이로 인한 성능 하락은 피할 수 없으나, Trade-Off 개념으로 그 반대급부인 생산성의 향상과 메모리 관련 버그 발생을 미연에 방지한다는 점에서 쓰레기 수집기는 완전히 정착되었다. 

Python은 추적 방식의 일종인 세대별 쓰레기 수집과 참조 횟수 카운팅 방식을 쓰레기 수집기에서 사용하며, 이것 때문에 객체별로 참조 횟수 변수가 존재하며, 쓰레기 수집기에 0~2세대의 3단계로 세대가 분류되어 객체를 관리한다.