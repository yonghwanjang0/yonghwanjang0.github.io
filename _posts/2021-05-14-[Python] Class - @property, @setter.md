---
layout: post
title: Python의 접근자와 설정자, @property / @name.setter
categories: [Python]
excerpt_separator:  <!--more-->
---

## 설명
접근자(getter) : 인스턴스 내부의 값에 접근을 할 수 있도록 해주는 메서드   
@property

설정자(setter) : 인스턴스 내부의 값을 수정 할 수 있도록 해주는 메서드   
@name.setter

Python의 Decorator 기능을 이용해서 접근자와 설정자의 구현을 편리하게 할 수 있다.

@property 로 메서드를 꾸며주게되면 그 메서드는 접근자가 된다.   
@name.setter 로 메서드를 꾸며주게되면 그 메서드는 설정자가 된다.    
(단, name에는 생성자에서 선언한 인스턴스 변수명을 넣게 된다.)

이 @property와 @name.setter 는 인스턴스 메서드에만 사용가능하다.   
즉 클래스 메서드에는 사용이 불가능하다.

- - -
## 예시코드
특정 인물의 이름과 전화번호를 입력받는 class를 만든다 가정하고. 예시코드를 작성한다.

```{.python}
class Info:
    # 생성자에서 인스턴스 생성시 이름과 전화번호 값을 입력받는다.
    def __init__(self, name, phone_number):
        self._name = name
        self._phone_number = phone_number
    
    # name의 접근자
    @property
    def name(self):
        return self._name
    
    # name의 설정자
    @name.setter
    def name(self, name):
        self._name = name

    # phone_number의 접근자
    @property
    def phone_number(self):
        return self._phone_number

    # phone_number의 설정자
    @phone_number.setter
    def phone_number(self, phone_number):
        self._phone_number = phone_number
```
- - -
## 적용과정
get_, set_ 등을 메소드 접두사로 사용하지 않고, 인스턴스 변수명과 메소드명을 통일시킨다. 
위의 예시코드에서는 name과 phone_number를 메소드명으로 사용하였다.

@property를 접근자 메소드 Decorator로,   
@변수명.setter를 설정자 메소드 Decorator로 장식한다.

이후 인스턴스를 생성하고, 생성자로 초기값을 입력받는 과정까지는 차이점이 없다.

```{.python}    
john = Info("John", "010-0000-0000")
jane = Info("Jane", "010-1000-1000")
```
하지만 인스턴스 생성 후 메소드 사용법이 달라진다.   
마치 인스턴스 변수에 직접 접근하여 값을 받아오고 할당하는 것 처럼 보이게 된다.

인스턴스 변수의 값을 받아본다.   
마치 변수 자체에 직접 접근하는 것 처럼 보이지만 접근자 메소드를 통해서 값을 받고있다.

```{.python}
>>> john.name
'John'
>>> john.phone_number
'010-0000-0000'
>>> jane.name
'Jane'
>>> jane.phone_number
'010-1000-1000'
```

인스턴스 변수의 값을 수정해본다.   
이것도 변수 자체에 직접 새 값을 할당하는 것 처럼 보이지만, 설정자 메소드를 통해서 값이 수정된다.

```{.python}
>>> john.name = "John Doe"
>>> john.phone_number = "010-1234-1234"
>>> john.name
'John Doe'
>>> john.phone_number
'010-1234-1234'
```

새 값을 할당한 이후 값을 받아보면 정상적으로 수정이 되었다는 사실을 알 수 있다.

또한 이렇게 접근자와 설정자를 구현할 경우, 메소드를 직접 호출하여 사용하는 방법은 불가능해진다.

```{.python}
>>> jane.name("Jane Roe")
Traceback (most recent call last):
  File "<pyshell#1>", line 1, in <module>
    jane.name("Jane Roe")
TypeError: 'str' object is not callable
```

메소드를 직접 사용하게 되면 문자열 객체는 호출 할 수 없다라는 타입에러를 발생시키는 것을 볼 수 있다.


## 정리
Decorator로 @property, @name.setter를 쓰면 접근자와 설정자 메서드를 통하여 인스턴스 변수에 접근하는 것과 동일한 효과를 낼 수있다.

get_, set_등의 메소드명을 사용할 필요가 없어 코드의 가독성이 좋아지며, 변수처럼 사용가능하다.   
변수에 직접 접근하는 것과 같은 직관성, 간결성 을 가질 수 있고, 코드 작성시 편의성이 증가한다.

단, 메소드 호출방식의 사용은 불가능해진다. 