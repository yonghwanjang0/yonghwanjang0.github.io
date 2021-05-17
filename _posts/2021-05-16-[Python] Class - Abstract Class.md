---
layout: post
title: Python - 추상 클래스 (Abstract Class)
categories: [Python]
excerpt_separator:  <!--more-->
---

## 개요
추상 클래스(Abstract Class)는 말 그대로 추상적으로 클래스를 먼저 선언해 놓는 것을 말한다. 
'무슨 선문답 같은 소리인가?'라고 생각할 수도 있지만, 객체 지향 프로그래밍을 함에 있어 공통된 부분을 미리 정의해 놓을 수 있기에 효율적인 코드 작성에 도움이 된다.

예를 들어보자.    
당신은 5:5로 서로 대전하는 AOS 게임을 개발하는 개발자의 입장이 되었다. 그래서 챔피언들을 구현하기 위해 코드를 작성하려고 하는데
각 챔피언 별 Class를 구상 중에 생각해 보니 챔피언들은 공통적으로 갖는 요소들이 많다는 결론에 도달하였다.

챔피언들은 체력(HP), 경험치(Exp)와 레벨(Level), 공격력(AD)과 방어력(Armor), 마법 공격력(AP)과 마법 방어력(MR), 이동속도(Movement Speed), 공격 속도(Attack Speed), 사정거리(Range), 치명타(Critical)등의 스탯을 가지고, 기본 공격과 3가지의 일반 스킬(Q/W/E), 1가지의 궁극기 스킬(R)을 갖는 등 공통적인 부분이 많다는 점이다. 

추상 클래스는 이러한 공통적인 부분을 미리 설계해서 선언해 놓음으로써 구현의 편의성과 함께 변화에 유연하게 대응할 수 있도록 만들어준다.
OOP의 다형성(Polymorphism) 특성을 살리는 방식이다.

쉽게 풀어서 쓰자면, 여기서도 쓰이고 저기서도 쓰이는 것들은 세부 구현은 각기 다를지언정 어차피 반복되는 거니까 묶어서 규격화 해놓자는 것이다. 
클래스가 인스턴스들을 만들기 위한 쿠키 틀 같은 것이라면, 추상 클래스는 상속받는 자식 클래스들의 쿠키 틀 같은 존재가 되는 셈이다.
- - -
### 추상 클래스의 특징
* 추상 클래스가 되면, 인스턴스(객체)를 만들 수 없게 된다.
* 다른 자식 클래스에게 상속만 가능해진다.
* @abstractmethod를 이용하여 자식 클래스가 오버라이딩(Overriding) 을 통해 필수로 구현해야 하는 메서드를 지정한다.

- - -
### 추상 클래스를 사용하여 얻는 이득
1. 공통된 변수명과 메서드명으로 통일
* 만약 위의 챔피언들을 여러 명의 개발자들에게 각각 분배해서 구현한다고 가정해보자. 각 개발자들은 자신만의 방법으로 변수와 메서드를 정의하여 챔피언들을 구현할 것이다. 이렇게 개발할 경우 일단 구현 당시에 돌아가게는 만들 수 있을 것이다. 
* 하지만 '추후에 업데이트 시 챔피언들 이동 관련 버그가 발생한다면?' '특정 챔피언을 추가해야 하는데 기존 챔피언과 연계가 필요하다면?' 등의 이슈가 발생 시, 대응이 힘들어진다. 기존의 코드를 파악할 때도 오래 걸릴 것이고, 아예 기존 코드를 모두 삭제하고 새로 개발하는 것이 더 좋은 상황이 올 가능성도 있다. 
* 추상 클래스는 이러한 점을 최대한 막아준다. 추상 클래스에서 미리 정의해놓은 틀대로 세부 구현만 각 챔피언 클래스에서 해 놓으면, 유지 보수성이 높아지고 코드의 가독성이 올라간다.

2. 실제 클래스를 작성함에 있어서 개발 시간 절약
* 아무것도 없는 백지상태에서 챔피언을 개발하려면, 일단 설계부터 해야 할 것이다. "챔피언에는 체력이 있고, 스킬이 3개가 있고, 공격이 있고, 여러 가지 스탯이 있고, 궁극기가 있고..." 
* 위에서 열거했던 각종 요소들을 하나하나 생각해 보면서, 클래스에 들어갈 변수와 메서드가 무엇일까에 대한 시간 소요가 필요해진다. 
* 추상 클래스는 이러한 과정을 대폭 단축시켜준다. 미리 설계해둔 챔피언 추상 클래스를 상속받으면, 기본적으로 구현해야 될 틀이 일단 잡히게 되므로, 개발자들은 여기에 세부 구현과 해당 챔피언만의 고유 요소를 추가 구현하기만 하면 되기 때문에 시간을 단축할 수 있다.

3. 규격에 맞는 클래스 작성
* 다른 개발자와의 협업이 일반적이 된 현대의 개발 환경에서, 추상 클래스는 일종의 약속이 된다. 유지 보수와 가독성이 올라가고, 예측 가능한 범위 내에서 개발이 진행되므로 협업에 큰 도움이 된다.

- - -
### 추상 클래스의 선언 과정
1. abc 모듈을 import 한다.
```python
from abc import *
```

2. 추상 클래스로 선언하고 싶은 클래스가 ABCMeta를 metaclass로 받도록 한다.
```python
class Champion(metaclass=ABCMeta):
```

3. 자식 클래스에서 구현해야 할 메서드를 선언하고, @abstractmethod를 Decorator로 장식한다.
```python
class Champion(metaclass=ABCMeta):
    @abstractmethod
    def attack(self):
        pass
    
    @abstractmethod
    def passive_skill(self):
        pass
```

- - -
#### 추상 클래스 선언시 주의 사항
* @abstractmethod를 한 개도 넣지 않을 경우, 추상 클래스가 되지 않는다.
* @abstractmethod가 장식된 메서드는 자식 클래스에서 필수로 구현해야 한다.
* 자식 클래스에서 @abstractmethod로 지정된 모든 메서드들을 구현하지 않으면, 객체 생성 시 에러가 발생한다.

- - -
### 예시 코드
```python
from abc import *

"""
먼저 챔피언 클래스를 추상 클래스로 선언한다.
상속받아서 구현해야 될 공통 변수와 메서드를 먼저 선언해놓는다. 
"""
class Champion(metaclass=ABCMeta):
    # 기본 변수값을 선언한다.
    def __init__(self):
        self.current_health = 500
        self.total_health = 500
        self.attack_damage = 50
        self.ability_power = 0
        self.armor = 0
        self.magic_resistance = 0
        self.exp = 0
        self.level = 1
    
    # 마찬가지로 구현해야 할 메서드들을 선언한다. 
    # 공격, 패시브, 스킬, 궁극기등을 들 수 있겠다.
    @abstractmethod
    def attack(self):
        pass
    
    @abstractmethod
    def passive_skill(self):
        pass

    @abstractmethod
    def first_skill(self):
        pass

    @abstractmethod
    def second_skill(self):
        pass

    @abstractmethod
    def third_skill(self):
        pass

    @abstractmethod
    def ultimate_skill(self):
        pass


# 추상 클래스를 상속받아 검사를 구현한다.
class Swordsman(Champion):
    def __init__(self):
        # super().__init__()을 먼저 입력하여 
        # 부모 클래스인 챔피언 클래스의 변수로 정의하고, 세부 항목을 구현한다.
        super().__init__()
        self.current_health = 600
        self.total_health = 600
        self.attack_damage = 55
        self.ability_power = 0
        self.armor = 10
        self.magic_resistance = 0
        self.exp = 0
        self.level = 1
    
    # 챔피언 클래스에 선언만 되어있는 추상 메서드들을 모두 다 구현해 준다.
    def attack(self):
        print("검으로 공격한다!")
    
    def passive_skill(self):
        print("체력이 20% 이하가 되면 방어력이 20% 상승한다.")

    def first_skill(self):
        print("검을 강하게 휘둘러 150%의 공격력으로 공격한다.")

    def second_skill(self):
        print("검을 들어 방어 태세를 취한다. 1회 공격을 막는다.")

    def third_skill(self):
        print("""세 번째 기본 공격은 추가 데미지를 입히고, 
        적을 0.5초간 기절시킨다.""")

    def ultimate_skill(self):
        print("""일직선으로 빠르게 이동해서 
        경로상의 모든 적에게 피해를 입힌다.""")

    # 그 외 해당 클래스에만 적용되는 세부 사항이 있을 시 구현한다.
    def special(self):
        print("검사 챔피언에게 존재하는 특별한 것")


# 이와 같은 방법으로 Archer, Healer, Magician 등의 챔피언 클래스를 구현한다.
class Archer(Champion):
    def __init__(self):
        super().__init__()
        # 궁수는 마나가 있다고 가정하고 변수에 추가하였다.
        self.mana = 400
        # 세부 스탯 생략

    def attack(self):
        print("활로 공격한다!")
    # 이하 생략

class Healer(Champion):
    # 이하 생략

class Magician(Champion):
    # 이하 생략

# 필요한 만큼 상속받아 챔피언들을 세부 구현한다.

"""
실제 게임에서는, 유저가 챔피언을 선택하게 되면 
구현된 챔피언 클래스의 객체를 생성해 
플레이어 객체의 챔피언 변수에 할당하게 된다.
"""
>>> blue_team.player1.champion = Swordsman()
# 블루팀의 1번 유저에게 검사가 할당이 되었다.

# 이제 실제 게임에서 유저 입력이 들어오게 되면, 
# 챔피언 객체의 구현된 메서드를 호출하게 되는 것이다.
>>> blue_team.player1.champion.attack()
'검으로 공격한다!'
>>> blue_team.player1.champion.ultimate_skill()
'일직선으로 빠르게 이동해서 경로상의 모든 적에게 피해를 입힌다.'

# 만약 추상 클래스의 객체를 생성하려고 하면, 
# 객체를 만들 수 없다는 타입 에러가 발생한다.
>>> champ = Champion()
Traceback (most recent call last):
  File "<pyshell#1>", line 1, in <module>
    champ = Champion()
TypeError: Cant instantiate abstract class Champion with 
abstract methods attack, first_skill, passive_skill, second_skill, 
third_skill, ultimate_skill

# 또한 자식 클래스에서 @abstractmethod로 지정된 
# 모든 메서드들을 구현하지 않으면, 객체 생성에서 에러가 발생한다.

# Swordsman 클래스의 ultimate_skill 메서드를 각주화 시켜보았다.
    # def ultimate_skill(self):
    #     print("일직선으로 빠르게 이동해서 경로상의 모든 적에게 피해를 입힌다.")

>>> blue_team.player1.champion = Swordsman()
Traceback (most recent call last):
  File "<pyshell#1>", line 1, in <module>
    blue_team.player1.champion = Swordsman()
TypeError: Cant instantiate abstract class Swordsman with 
abstract methods ultimate_skill
# 궁극기에 해당하는 ultimate_skill 메서드를 생략하고 
# 객체를 생성하려 시도해보니 에러가 나는 것을 확인할 수 있다.
```

이 예시코드를 보면, Swordsman이나 Archer클래스등은 Champion의 한 부류이기 때문에(IS-A 관계), Champion 클래스를 상속받고 있는 것을 알 수 있다.

또한 Swordsman이나 Archer등은 비교적 명확하게 객체로서의 모습이 상상되지만, Champion이라고 하면 명확한 모습이 떠오르지 않는 큰 범주로서의 의미를 가지게 된다.     

이러한 큰 의미를 가진 클래스의 객체 생성을 막는것이 추상화이며, 추상화된 클래스는 자식 클래스에게 메서드와 변수를 상속시켜 구현을 하도록 만드는 것이다.

- - -
## 정리
추상 클래스는 공통된 변수와 메서드들을 묶어서 미리 선언해 놓음으로써 구현의 편의와 유지 보수성, 가독성, 협업 편의성 등을 높이기 위한 방법이다.     
자식 클래스는 추상 클래스를 상속받은 후 메서드 오버라이딩을 통해 세부 구현을 다르게 가져가서 클래스 별로 같은 메서드명임에도 각기 다른 행동을 할 수 있게 만들 수 있다.    
또한 예상 가능한 자식 클래스의 정형화된 변수와 메서드를 통해 유지 보수에도 도움을 준다.
