---
layout: post
title: Python - getattr, hasattr, setattr, delattr
categories: [Python]
excerpt_separator:  <!--more-->
---

## 개요
getattr, hasattr, setattr, delattr.    
이상 4종류의 함수는 python의 내장함수로, 객체가 가지고 있는 속성값을 직접 다룰 수 있는 함수이다. 

"object.attribute" 형식의 호출이 아닌, 문자열을 이용해서 직접 속성값에 접근하는 것으로, 속성값의 변수명을 문자열 값으로 얻은 상태에서 이 문자열 값을 통해 객체의 속성을 직접 호출해서 사용하고 싶을 때 같은 상황에서 유용하게 사용되는 함수들이다.

- - -
### getattr
getattr의 문법은 다음과 같다.
* getattr(object, name[, default])

getattr을 사용하면 객체의 attribute 값을 문자열을 통해서 얻을 수 있다.
```python
class Example:
    def __init__(self):
        self.name = 'Oliver'

>>> a = Example()
>>> a.name
'Oliver'

>>> getattr(a, 'name')
'Oliver'

>>> getattr(a, 'age')
Traceback (most recent call last):
  File "<pyshell#1>", line 1, in <module>
    getattr(a, 'age')
AttributeError: 'Example' object has no attribute 'age'
```
getattr 함수는 특정 문자열을 받아, 객체에 해당 문자열과 일치하는 attribute 명이 있으면 해당 값을 반환하고, 그렇지 않으면 AttributeError를 반환하는 함수이다.

일반적으로 객체의 attribute 값에 접근하기 위해서는 "object.attribute"의 형식으로 접근해서 사용하는 것이 일반적이다. 하지만 getattr을 이용하면 문자열을 통해 직접 해당 attribute에 접근해서 값을 얻어 올 수 있게 된다. 'Oliver'라는 문자열을 'name'이라는 문자열을 통해 attribute의 호출 없이 얻게 되었다.

'age'라는 문자열은 a 객체의 attribute에 존재하지 않으므로 getattr 함수 사용시 AttributeError를 반환한다.

또한 3번째 인자로 받는 default 값에 특정 값을 지정하면, 일치하는 attribute 명이 없는 경우 AttributeError 대신 지정한 값을 반환하게 만들 수도 있다. 

```python
>>> a.age
Traceback (most recent call last):
  File "<pyshell#1>", line 1, in <module>
    a.age
AttributeError: 'Example' object has no attribute 'age'

>>> getattr(a, 'age', 25)
25
```
a라는 객체에 age라는 이름의 attribute 명은 없지만, 25라는 값을 반환하도록 지정하니 AttributeError 대신 25를 반환하는 것을 확인할 수 있다.

- - -
### hasattr
hasattr의 문법은 다음과 같다.
* hasattr(object, name)

hasattr 함수는 문자열을 받아, 객체에 해당 문자열과 일치하는 attribute 명이 있으면 True를, 그렇지 않다면 False를 반환하는 함수이다. 말 그대로 객체에 해당 attribute 명이 있는지 확인할 수 있는 내장함수이다.

```python
>>> hasattr(a, 'name')
True
>>> hasattr(a, 'age')
False
```
a.name은 선언된 attribute이므로 True를, a.age는 선언되지 않은 attribute이므로 False를 반환한다.

- - -
### setattr
setattr의 문법은 다음과 같다.
* setattr(object, name, value)

setattr 함수는 2번째 인자로 문자열을 받아, 문자열에 해당하는 attribute에 3번째 인자로 받은 값을 할당하는 함수이다. 

```python
>>> setattr(a, 'name', 'Ella')
>>> a.name
'Ella'
```
a 객체의 name이 'Ella'로 변경된 것을 확인할 수 있다.

setattr 함수는 해당 객체에 문자열로 받은 attribute가 없더라도 값을 할당할 수 있다. 이렇게 사용할 경우 객체에 문자열을 attribute 명으로 삼아 새로운 속성을 선언하게 된다.

```python
>>> setattr(a, 'age', 23)
>>> a.age
23
```
a 객체에는 age가 없었지만 setattr 함수를 이용하여 23이란 값을 할당하였다.

또한 setattr 함수는 객체 내의 method, 심지어는 인스턴스를 만드는 Class 자체에도 접근하여 값을 재정의 할 수 있다. 이는 python의 모든 것이 객체로 이루어져 있기 때문에 가능한 것이다. 객체 내의 속성도 객체이고, 객체 내의 메서드도 객체이고, 선언된 Class도 type으로 만든 객체이기 때문에 문자열만 옳게 인자로 넣어주면 setattr 함수를 통해 접근하여 값을 수정할 수 있는 것이다.

```python
class ModifyTest:
    def __init__(self):
        self.a = 'a'
    def test_method(self):
        print('this is test')

# first 인스턴스 객체의 메서드를 재정의했다.
>>> first = ModifyTest()
>>> setattr(first, 'test_method', 'b')
>>> first.test_method
'b'

# first 인스턴스 객체를 수정해도 
# 새롭게 만든 second 인스턴스 객체에는 변화가 없다.
>>> second = ModifyTest()
>>> second.test_method()
'this is test'

# 하지만 ModifyTest 클래스 자체를 수정하면 
# second 인스턴스 객체에도 영향을 끼친다.
>>> setattr(ModifyTest, 'test_method', 'change')
>>> second.test_method
'change'

# 메서드가 아닌 클래스 변수가 되었으므로 호출하면 문자열을 반환한다.
>>> ModifyTest.test_method
'change'

"""
하지만 first 인스턴스 객체는 변화가 없는데, 
이는 first 인스턴스 객체는 만들어지고 난 뒤 
test_method 메서드가 새로운 객체로 재정의가 되어 있으므로
first 인스턴스 객체의 test_method attribute는 
ModifyTest 클래스의 변화와 상관없이 유지가 된다.
"""
>>> first.test_method
'b'

>>> id(first.test_method)
34601088
>>> id(second.test_method)
45151824
>>> id(ModifyTest.test_method)
45151824
```
모든 수정이 끝난 후 객체의 주소값을 알 수 있는 id 함수에 attribute들을 넣어서 확인해보면, second 인스턴스와 ModifyTest 클래스의 test_method 객체는 같은 객체를 가리키고 있다는 것을 확인할 수 있다. 

이는 bound method로 선언되었던 test_method 메서드가 재정의되는 과정에서 덮어씌워지면서 class variable로 변했고, 클래스 변수의 특성상 인스턴스 객체에서도 클래스 변수를 참조하게 만들기 때문이다. 

왜 이렇게 변하는지 이해가 가지 않는다면, 클래스와 인스턴스 간의 관계를 살펴보아야 한다. 

코드로 살펴보면 다음과 같다.
```python
class BoundCheck:
    def __init__(self):
        self.var = 'var'
    def test(self):
        self.var = 'change var'      
        print("this is test")

>>> bound = BoundCheck()
```
이와 같이 선언이 된 경우, 'bound' 인스턴스 객체에서 test 메서드를 호출하면,

```python
>>> bound.test()
# 위의 메서드를 호출하면 -> 실제로는 아래와 같이 작동한다.
>>> BoundCheck.test(bound)
'this is test'
>>> bound.var
'change var'
```
이와 같은 변환을 거치게 되는 것이다. 'self' 인자를 받는 것이 인스턴스 객체 자신을 클래스에 선언된 메서드의 1번째 인자로 넣어주겠다는 의미이기 때문이다.

따라서 클래스의 메서드를 재정의했으니 그에 영향을 받는 인스턴스들도 변화가 생기게 되고, 그런 변화 이전에 먼저 재정의 과정을 거친 인스턴스 객체에는 영향을 끼치지 않게 되는 것이다.

이런 이유로 생성자(init)를 통해서 만들어지는 인스턴스 객체의 attribute는 클래스를 통해서 재정의가 불가능하다. 클래스를 통해 접근하여 재정의가 가능한 attribute는 클래스 변수나 메서드뿐이다.
```python
>>> setattr(BoundCheck, 'var', 'new_var')
>>> BoundCheck.var
'new_var'
>>> bound.var
'change var'
```
setattr 함수가 정상적으로 작동하기 때문에 '수정이 가능한 것이 아닌가?' 하고 착각하기 쉽다. 클래스에 쓰인 setattr 함수는 클래스 변수를 생성한 것이 되며, 실제로 인스턴스 객체의 attribute를 호출해보면 변화가 없다는 것을 확인할 수 있다.

- - -
### delattr
delattr의 문법은 다음과 같다.
* delattr(object, name)

object에 존재하는 name과 일치하는 attribute를 삭제한다. 일치하는 attribute가 없다면 AttributeError를 발생시킨다.

```python
>>> a. age
23

# attribute를 삭제한다.
>>> delattr(a, 'age')
>>> a.age
Traceback (most recent call last):
  File "<pyshell#1>", line 1, in <module>
    a.age
AttributeError: 'Example' object has no attribute 'age'

# 삭제된 attribute를 호출시 AttributeError가 발생한다.
>>> delattr(a, 'age')
Traceback (most recent call last):
  File "<pyshell#1>", line 1, in <module>
    delattr(a, 'age')
AttributeError: age
```
삭제가 잘 완료되어 호출하려고 할 시 AttributeError를 발생시키는 것을 볼 수 있다. 또한 없는 attribute를 삭제하려고 하면 AttributeError를 발생시킨다.

삭제는 delattr 외에도 "del object.attribute"의 형태로도 삭제가 가능하다.
```python
>>> a.name
'Ella'

>>> del a.name
>>> a.name
Traceback (most recent call last):
  File "<pyshell#1>", line 1, in <module>
    a.name
AttributeError: 'Example' object has no attribute 'name'
```
name이 잘 삭제된 것을 확인할 수 있다.

물론 delattr의 경우에도 객체에 직접 접근하는 것이므로 클래스 attribute에 직접 접근하여 삭제가 가능한 것은 setattr과 마찬가지이다. 단, 인스턴스에 있는 bound method는 클래스에 의존하기 때문에 setattr로 재정의는 가능하나 삭제는 불가능하다. 

또한 setattr 함수와 마찬가지로 생성자(init)를 통해서 만들어지는 인스턴스 객체의 attribute는 클래스를 통해서 삭제가 불가능하다. 클래스에 접근하여 삭제가 가능한 attribute는 클래스 변수나 메서드뿐이다.

```python
class DeleteTest:
    def __init__(self):
        self.var = 'var'
    def delete_method(self):
        print('this method will be delete.')

>>> d = DeleteTest()
>>> d.delete_method()
'this method will be delete.'

# 인스턴스의 메서드를 직접 삭제하려고 하면 
# AttributeError가 발생한다.
>>> delattr(d, 'delete_method')
Traceback (most recent call last):
  File "<pyshell#1>", line 1, in <module>
    delattr(d, 'delete_method')
AttributeError: delete_method

# 삭제가 되지 않은 것을 확인할 수 있다.
>>> d.delete_method()
'this method will be delete.'

# 클래스에서 메서드를 삭제하면, 의존하고 있는 객체에서도 메서드가 삭제된다.
>>> delattr(DeleteTest, 'delete_method')
>>> d.delete_method()
Traceback (most recent call last):
  File "<pyshell#1>", line 1, in <module>
    d.delete_method()
AttributeError: 'DeleteTest' object has no attribute 'delete_method'

# 이미 클래스에서 메서드가 삭제되었으므로, 
# 다른 객체를 만들어도 메서드는 존재하지 않는다.
>>> e = DeleteTest()
>>> e.delete_method()
Traceback (most recent call last):
  File "<pyshell#1>", line 1, in <module>
    e.delete_method()
AttributeError: 'DeleteTest' object has no attribute 'delete_method'

# 생성자를 통해 인스턴스 객체에만 존재하는 
# 인스턴스 attribute들은 클래스를 통해 삭제가 불가능하다.
>>> delattr(DeleteTest, 'var')
Traceback (most recent call last):
  File "<pyshell#1>", line 1, in <module>
    delattr(DeleteTest, 'var')
AttributeError: var

# 인스턴스에 있는 attribute들은 호출이 잘 되는 것을 확인할 수 있다.
>>> d.var
'var'
>>> e.var
'var'
```
- - -
### getattr를 사용한 코드 축약
이와 같은 코드가 있다고 가정해보자.
```python
# 작성한 CNN 모듈을 import
import cnn_model as Model

# 구현된 모델을 주어진 이름에 맞춰서 사용하는 함수
def build_cnn(model_name):
    if model_name == 'mobilenetv2':
        model = Model.mobilenetv2
    elif model_name == 'resnet18':
        model = Model.resnet18
    elif model_name == 'resnet34':
        model = Model.resnet34
    elif model_name == 'resnet50':
        model = Model.resnet50
    # elif를 연속적으로 넣는다

    return model

```
분명히 잘 작동은 하겠지만, if~elif~else 분기문이 쓸데없이 길어지기 때문에 가독성이 떨어지며 유지 보수하기 힘들다는 단점이 생길 것이다.

이를 getattr()로 수정해보자.
```python
import cnn_model as Model

def build_cnn(model_name):
    return getattr(Model, model_name)
```
문자열을 attribute로 인식시켜 사용할 수 있다는 것만으로 코드를 많이 간결하게 만들 수 있다. 코드를 작성하다 보면 문자열 data는 얻을 수 있으나 이를 호출로 옮기는 과정에서 변환이 어려운 상황이 종종 발생하게 된다. 이럴 때 getattr() 함수를 사용하면 많은 코드 양을 줄이게 해준다.

## 정리
* getattr : 객체의 attribute를 얻어온다.
* hasattr : 객체의 attribute가 존재하는지 확인한다.
* setattr : 객체의 attribute 값을 재정의한다.
* delattr : 객체의 attribute를 삭제한다.

이상 4종류의 python 내장함수는 문자열을 통해 직접 객체의 attribute를 제어할 수 있는 방법이다. 강력한 기능을 갖춘 내장함수들로 문자열 data와 실제 객체의 Function call 변환 과정에서 많은 편의성을 제공하는 함수들이다.