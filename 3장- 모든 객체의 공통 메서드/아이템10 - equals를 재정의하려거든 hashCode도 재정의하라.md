# 아이템 11 -equals를 재정의하려거든 hashCode도 재정의하라

## Object명세에 나와있는 규약


모든 객체의 최상위 객체은 Object에서 제공하는 equals와 hashCode에 대해서 아래와같은 규칙이 있다.


- equals 비교에 사용되는 `정보가 변경되지 않으면` 애플리케이션이 실행되는 동안 `hashCode메서드는 항상 동일한 값`을 반환
- `equals가 두 객체를 같다고 판단`했다면, 두 객체의 `hashCode는 똑같은 값`을 반환해야한다.
- `equals가 두 객체를 다르다고 판단했더라도`, 두 객체의 hashCode가 서로 다른값을 반환할 필요는 없다.



## 규악을 지키지 않으면 어떤상황이 발생하는건가?

아래와 같은 코드를 생각해보면 키값으로 PhoneNumber라는 객체를 생성해서 사용하고 있고 이를 key로 value를 꺼내오려는 코드이다. 분명 map에 추가할때 사용한 객체와 map에서 값을 꺼내올때 상요한 객체는 논리적으로 동치이지만 코드를 실행하면 그렇지 않다.

```java
Map<PhoneNumber,String> map = new HashMap<>();
map.put(new PhoneNumber(707,867,5309),"제니");
String user = map.get(new PhoneNumber(707,867,5309)); // null반환
```

## 왜 이런일이 생기는걸까?
각각의 인스턴스는 서로 다른 hashCode를 반환한다. 즉 ,Object의 두번째규약을 지키지 못하기때문에 이런일이 발생하고 HashMap은 hashCode가 다른 엔트리끼리는 동치성 비교를 시도하지 않도록 최적화 되어있다.



## 어떻게 해결해야할까?
PhoneNumber객체에서 hashCode를 적절하게 오버라이딩해주면 끝이다. 

아래처럼 오버라이딩하면 모든 객체가 똑같은 hashCode를 반환하므로 HashMap의 버킷 하나에만 연결리스트로 연결되어서 Map이지만 Map의 성능을 얻지못한다. (충돌이 발생하면서 항상 같은 리스트에 들어간다)
```java

@Overide
public int hashCode() {
 	return 42;
}

```


## 이상적인 해시함수를 구현하는 방법

주어진 인스턴스들을 32비트 정수범위에 균일하게 분배해야 한다.  Object가 제공하는 hash메서드를 사용해도되지만 속도는 더 느리다. hash값을 균등하게 분포되도록 만드는게 중요한데 이런건 우리가 임의로 판단해서 구현하는것 보다는 이미 잘 나와있는 레퍼런스를 활용해서 구현하는 게좋다. ide에서 자동으로 생성해주기도하니 적극활용하자.



## 그럼 진짜 무조건 재정의 해야할까?

모든 객체의 equals를 재정의할필요가 없으므로 당연히 hashCode도 무조건 재정의할 필요는 없다. 스프링을 사용할때 모든 빈에 hashCode를 재정의하는 일이 없는것처럼 객체자체를 값저럼 사용할때 정의하는것이 좋다.

 Value Object(VO)객체라면 객체가 동등한지를 확인해야하므로 equals를 재정의해야하므로 hashCode를 재정의해야한다. 

Collection프레임워크를 사용하지 않는다면 사실 hashCode를 재정의해도 문제되지 않는다. 그렇다고 Collection프레임워크 사용여부로 hashCode 재정의여부를 결정하기에는 애매하므로 VO라면 무조건 equals,hashCode를 재정의하고 그렇지 않다면 재정의할 필요가 없다 정도의 기준으로 생각하는게 좋을것 같다.