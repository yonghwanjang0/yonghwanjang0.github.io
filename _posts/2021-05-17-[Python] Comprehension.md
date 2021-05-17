---
layout: post
title: Python - Comprehension
categories: [Python]
excerpt_separator:  <!--more-->
---

## 개요
컴프리헨션(Comprehension)은 python에서 Iterable(반복 가능)한 객체를 생성하기 위한 python의 기능 중 하나이다. for 문을 통해 직접 반복하며 append를 하는 것에 비해 처리 속도가 빠르며, 코드가 간결해지는 부수 효과를 얻게 된다. 단, 축약해서 코드를 적기 때문에 가독성 있는 코드를 작성하기 위해 신경을 써야 할 필요가 있는 방법이다. 잘못 사용할 경우 직관적이지 않은 코드가 될 수 있다.

Comprehension은 List, Set, Dict, Generator 등 Iterable한 객체에는 모두 사용이 가능하다.    
일단 대표적으로 많이 쓰이는 List Comprehension부터 알아보도록 하자.
- - -
### List Comprehension
List Comprehension은 list 자료형을 만들기 위한 방법이다.
```python
# 1부터 10까지 원소로 담은 list를 만들기 위해서는 
# 일반적으로 이런 방법을 사용할 것이다.
number_list = []
for number in range(1, 11):
    number_list.append(number)

>>> print(number_list)
[1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

# 하지만 List Comprehension을 사용하면 이렇게 표현된다.
number_list = [number for number in range(1, 11)]
>>> print(number_list)
[1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```

빈 리스트를 선언하고, for 문을 통해 값을 append 하는 것에 비해 간결해진 것을 볼 수 있다. 
- - -
#### Comprehension의 기본 문법
Comprehension의 기본 문법은 다음과 같다.

```python
# [변수를 이용해서 리스트에 넣고 싶은 값 for 사용할 변수 이름 in 순회 방법]
sample = [number * 2 for number in range(1, 11)]
>>> sample
[2, 4, 6, 8, 10, 12, 14, 16, 18, 20]
```

* 변수를 이용해서 리스트에 넣고 싶은 값
 * for 문의 append 함수에 인자로 넣는 값을 이곳에 적는다. (ex : 2를 곱한 값 (number * 2), 제곱을 한 값 (number ** 2)), 실제 리스트에 담고 싶은 것을 for 앞에 적어준다.

* 사용할 변수 이름
 * 위의 예시로 든 for 문에서는 for와 in 사이의 'number'에 해당한다. 숫자를 다루는 상황이기에 number를 사용했지만, 이해하기 편한 문자라면 어느 것을 사용해도 상관없다. 주의할 점이 있다면, for의 앞에 적히는 실제 리스트에 넣고 싶은 값에 사용하는 변수명과 일치시켜야 정상적으로 작동한다.

* 순회 방법
 * 위의 예시로 든 for 문의 range에 해당하는 부분이다. range 외에도 iterable이 가능한 어떤 객체든 넣어도 상관없다.
- - -
#### if 조건문 사용
for 문에서도 반복과정에서 if 조건문을 달아 list에 담을 자료를 걸러 낼 수 있는 것처럼, Comprehension에서도 if 조건문을 사용할 수 있다.    
사용 방법은 다음과 같다.

```python
# 기본 문법 뒤에 if 조건문을 추가해준다.
sample = [i * 3 for i in range(50) if i % 3 == 0]

>>> sample
[0, 9, 18, 27, 36, 45, 54, 63, 72, 81, 
90, 99, 108, 117, 126, 135, 144]

# 위의 코드를 풀어서 for 문으로 작성하면 다음과 같다.
sample = []
for i in range(50):
    if i % 3 == 0:
        sample.append(i * 3)

>>> sample
[0, 9, 18, 27, 36, 45, 54, 63, 72, 81, 
90, 99, 108, 117, 126, 135, 144]
```
Comprehension을 작성하고, 원하는 if 조건문을 뒤에 넣어주면 된다.
- - -
#### 다중 if 조건문 사용
for 문에서도 다중 if 조건문을 사용할 수 있듯이, Comprehension에서도 마찬가지다.

```python
sample = [i * 3 for i in range(50) if i % 3 == 0 if i % 2 == 0]

>>> sample
[0, 18, 36, 54, 72, 90, 108, 126, 144]
```
결과물에서 알 수 있듯 2개의 if 조건문이 and 연산으로 적용되어 있는 것을 확인할 수 있다.
- - -
#### 다중 for문 사용
또한 for 문을 사용할 때, 다중 for 문을 사용할 수 있는 것처럼 Comprehension에서도 다중 for 문을 적용할 수 있다.
```python
sample = [i * 2 for i in range(10) for j in range(3)]

>>> sample
[0, 0, 0, 2, 2, 2, 4, 4, 4, 6, 6, 6, 8, 8, 8, 10, 10, 10, 
12, 12, 12, 14, 14, 14, 16, 16, 16, 18, 18, 18]
```
앞의 for 문이 하나씩 적용될 때, 각각의 순번에 뒤의 for 문이 적용되어 원소가 3번씩 추가된 것을 확인할 수 있다.

물론 조건문과의 혼용도 가능하다.
```python
>>> sample = [i * 2 for i in range(10) if i % 2 == 0 for j in range(3)]

>>> sample
[0, 0, 0, 4, 4, 4, 8, 8, 8, 12, 12, 12, 16, 16, 16]
```
다중 for 문의 사용과 동시에 if 조건문이 같이 적용된 것을 확인할 수 있다.
- - -
#### else 사용
if 조건문에 함께 사용되는 else를 사용하기 위해서는 문법을 숙지해야 할 필요성이 있다.

```python
fail = [i for i in range(30) if i % 2 == 0 else]
SyntaxError: invalid syntax
fail = [i for i in range(30) if i % 2 == 0 else 'odd']
SyntaxError: invalid syntax
```
기존의 if 사용법처럼 if를 뒤에 넣고 else를 사용하면 맞지 않는 문법을 사용하고 있다고 에러를 표시한다. 사실 if 조건문의 사용법을 생각해 봤을 때 논리적으로 올바르지 않다는 사실을 바로 알 수 있다. if 조건문을 통과하지 못 했을 때 분기하여 다른 행동을 취하라는 것이 else 문법인데, 따라서 결과에 관여하는 곳에 조건문을 넣어줘야 자연스러운 문법이 될 것이다.

```python
sample = [i * 2 if i % 2 == 0 else 'odd' for i in range(30)]

>>> sample
[0, 'odd', 4, 'odd', 8, 'odd', 12, 'odd', 16, 'odd', 
20, 'odd', 24, 'odd', 28, 'odd', 32, 'odd', 36, 'odd', 
40, 'odd', 44, 'odd', 48, 'odd', 52, 'odd', 56, 'odd']
```

복잡해 보이지만 하나씩 살펴보도록 하자, 일단 맨 앞의 (i * 2)가 조건문을 통과했을 때 들어가는 값에 해당한다. 그리고 이 결과값을 결정하는 조건문인 if~else 문을 바로 뒤에 붙여서 어떤 조건일 때 값을 추가할지 제시하고 있다. i 값이 짝수이면 통과하고, 홀수이면 else로 분기하여 'odd'라는 원소를 넣으라는 뜻이 된다. 그러고 나서 이 조건문을 순회하는 조건을 for 문을 통해서 넣어준다.

위의 예시를 풀어서 코드로 작성하면 다음과 같을 것이다.

```python
sample = []
for i in range(30):
    if i % 2 == 0:
        sample.append(i * 2)
    else:
        sample.append('odd')

>>> sample
[0, 'odd', 4, 'odd', 8, 'odd', 12, 'odd', 16, 'odd', 
20, 'odd', 24, 'odd', 28, 'odd', 32, 'odd', 36, 'odd', 
40, 'odd', 44, 'odd', 48, 'odd', 52, 'odd', 56, 'odd']
```
- - -
### Set Comprehension
set 자료형에도 Comprehension을 사용할 수 있다. '[]' 대신 '{}'을 사용해서 코드를 작성하면 Set Comprehension이 된다.
단, 집합 자료형으로 중복을 허용하지 않는 Set의 특성을 이해하고 사용하여야 한다.
```python
set_sample = {s for s in range(20)}

>>> set_sample
{0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 
11, 12, 13, 14, 15, 16, 17, 18, 19}
>>> type(set_sample)
<class 'set'>

# 이중 for 문의 사용
duplicate = {s for s in range(20) for t in range(3)}

>>> duplicate
{0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 
11, 12, 13, 14, 15, 16, 17, 18, 19}
```
이중 for 문으로 강제로 중복을 만들었지만 적용이 되지 않았다는 것을 확인할 수 있다.
- - -
### Dictionary Comprehension
list, set 자료형 말고 dict 자료형에도 Comprehension을 적용할 수 있다. key-value의 쌍을 갖는 dict 자료형의 특성상 2개의 iterable 한 data가 필요하다.
```python
students = ['Ella', 'John', 'Mia', 'James', 'Oliver']
scores = [98, 80, 95, 92, 100]
score_dict = {key: value for key, value in zip(subjects, scores)}

>>> score_dict
{'Ella': 98, 'John': 80, 'Mia': 95, 'James': 92, 'Oliver': 100}
```
학생들 명단인 students, 점수표인 scores를 각각 list 자료형으로 받아서, 각 list의 iterable 한 원소를 반복자(iterator)로 반환해 주는 zip 함수에 넣은 뒤 key-value의 형태로 넣어준 모습이다. 또한 '{}'로 감싸서 dict 자료형임을 python에 알려주고 있다.

물론 이중 list나, tuple 쌍으로 표현되고 있는 형태의 자료도 dict로 변경할 수 있다.
```python
score_tuples = [('Ella', 98), ('John', 80), ('Mia', 95), ('James', 92), ('Oliver', 100)]
score_dict = {t[0]: t[1] for t in score_tuples}

>>> score_dict
{'Ella': 98, 'John': 80, 'Mia': 95, 'James': 92, 'Oliver': 100}
```
score_tuples의 각 원소를 받아 tuple의 index 0, index 1을 각각 key-value로 넣어서 dict 자료형을 만든 것을 확인할 수 있다.
- - -
### Generator Expression
Generator Expression은 Generator를 반환하는 Comprehension이다. Generator란 모든 원소를 한 번에 반환하지 않고, 한 번에 하나의 원소만 반환하는 자료형으로, 원소를 꺼내기 위해서는 next 함수를 이용하여 하나씩 꺼내어 사용하게 된다. 

Generator Expression을 사용하기 위해서는 '()'을 사용해서 문법을 감싸준다.
```python
generator = (g for g in range(5))

>>> generator
<generator object <genexpr> at 0x0000000002C0E150>
>>> type(generator)
<class 'generator'>

# 원소를 사용하기 위해서는 next 함수를 사용한다.
>>> next(generator)
0
>>> next(generator)
1
>>> next(generator)
2
>>> next(generator)
3
>>> next(generator)
4

# 모든 원소를 다 꺼냈으면 에러가 발생한다.
>>> next(generator)
Traceback (most recent call last):
  File "<pyshell#1>", line 1, in <module>
    next(generator)
StopIteration
```
- - -
 ### Comprehension은 정말 처리 속도가 빠를까?
Comprehension 관련해서 공부를 하다 보면 Comprehension을 사용하는 것이 퍼포먼스 면에서도 이득이라는 내용을 보게 된다. 정말로 그런지 테스트해 보았다.
```python
import time


def append_test():
    append_list = []
    start_time = time.time()
    for number in range(10000000):
        append_list.append(number)
    end_time = time.time()
    elapsed_time = end_time - start_time

    print("총 소요시간 (초) : {}".format(str(elapsed_time)))

    return append_list


def comprehension_test():
    start_time = time.time()
    comprehension_list = [number for number in range(10000000)]
    end_time = time.time()
    elapsed_time = end_time - start_time

    print("총 소요시간 (초) : {}".format(str(elapsed_time)))

    return comprehension_list


>>> append_list = append_test()
총 소요시간 (초) : 0.9219999313354492

>>> comprehension_list = comprehension_test()
총 소요시간 (초) : 0.5320000648498535

>>> len(append_list)
10000000

>>> len(comprehension_list)
10000000
```
똑같이 천만 개의 원소를 포함한 list를 만드는 함수를 선언하고 얼마나 걸리는지 시간을 재서 속도 차이를 비교해보았다.
결과는 거의 2배에 가까운 속도 차이가 나고, 꽤나 유의미한 차이를 보이는 것을 확인했다.

흥미로워 이유를 찾아보니, [List Comprehension이 빠른 이유를 찾아보자](https://jeongukjae.github.io/posts/inspecting-list-comprehension) 라는 포스트에서 Function Call 수를 줄여 속도 면에서 이득을 보았다는 내용을 확인할 수 있었다.

- - -
## 정리
Comprehension은 Iterable이 가능한 자료의 생성에 사용하는 문법으로, list, set, dict, generator 등에 사용 가능하다.
if~else 조건문, 다중 for 문 등의 사용도 물론 가능하며, 속도 면에서도 일반 loop-append에 비해 이득을 볼 수 있다는 특징이 있다.
가독성을 잃지 않게 주의해야 한다는 점만 조심한다면, 강력하고 생산성에 도움을 줄 수 있는 문법이다.