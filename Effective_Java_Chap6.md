이펙티브 자바
-------------

---

### 6장 열거타입과 애너테이션

-	열거타입과 애너테이션들을 올바르게 사용하는 방법을 알아보자

#### 아아템 34 int 상수 대신 열거 타입을 사용하라

-	열거 타입은 일정 개수의 상수 값을 정의한 다음, 그 외의 값은 허용하지 않는 타입이다.

-	정수 열거 패턴, 문자열 열거 패턴에는 단점들이 많다.

-	자바는 위의 패턴의 단점을 없애고 장점을 주는 열거 타입을 제시했다.

	-	열거 타입 자체는 클래스이며, 상수 하나당 자신의 인스턴스를 만들어 public static final 필드로 공개한다.
	-	열거 타입은 밖에서 접근할 수 있는 생성자를 제공하지 않으므로 사실상 final이다.
	-	클라이언트가 인스턴스를 직접 생성하거나 확장할 수 없으니 열거타입 선언으로 만들어진 인스턴스는 딱 하나만 존재함이 보장된다.
	-	컴파일 타입 안전성을 제공한다.
	-	열거타입은 임의의 메서드나 필드를 추가할 수 있고 임의의 인터페이스를 구현하게 할 수도 있다. (Object 메서드, Comparable, Serializable 구현)
	-	추상메서드를 통해 상수별 메서드 구현도 가능하다.
	-	상수별 메서드 구현에는 열거 타입 상수끼리 코드를 공유하기 어려운데, 전략 열거 타입패턴을 통해 안전하고 유연하게 사용할 수 있다.

	```java
	public enum Apple { FUJI, PIPPIN, GRANNY_SMITH }
	public enum Orange { NAVEL, TEMPLE, BLOOD }
	```

-	필요한 원소를 컴파일타임에 다 알 수 있는 상수 집합이라면 항상 열거타입을 사용하자.

-	책에 있는 핵심 정리

	-	열거 타입은 정수 상수보다 더 읽기 쉽고 안전하고 강력하다.
	-	각 상수를 특정 데이터와 연결짓거나 상수마다 다르게 동작하게 할 때는 생성자나 메서드가 필요하다.
	-	하나의 메서드가 상수별로 다르게 동작해야할 때는 switch 대신 상수별 메서드 구현을 사용하자.
	-	열거 타입 상수 일부가 같은 동작을 공유한다면 전략 열거 타입 패턴을 사용하자.

#### 아이템 35 ordinal 메서드 대신 인스턴스 필드를 사용하라

-	모든 열거 타입은 해당 상수가 그 열거 타입에서 몇 번째 위치인지를 반환하는 ordinal 메서드를 제공한다.

	-	ordinal을 잘못 사용하면 유지보수 끔찍해진다.
	-	ordinal은 EnumSet과 EnumMap 같이 열거 타입 기반의 범용 자료구조에 쓸 목적으로 설계되었다. 이런 용도가 아니라면 사용하지 말자.

-	열거 타입 상수에 연결된 값은 ordinal 메서드로 얻지 말고, 인스턴스 필드에 저장하자.

```java
public enum Ensemble{
  SOLE(1), DEUT(2), TRIO(3), QUARTET(4), QUINTET(5);

  private final int numberOfMusicians;
  Ensemble(int size) {
    this.numberOfMusicians = size;
  }
  public int numberOfMusicians(){
    return numberOfMusicians;
  }
}

```

#### 아이템 36 비트 필드 대신 EnumSet을 사용하라

-	열거한 값들이 주로 집합으로 사용될 경우, 열거 패턴을 사용해왔다.

-	비트 필드를 사용하면 비트별 연산을 사용해 합집합과 교집합 같은 집합 연산을 효율적으로 수행할 수 있으나, 비트필드는 정수 열거 상수의 단점을 그대로 지닌다. (또한 해석, 적잘한 타입 예상필요 등의 단점있음)

> java.util 패키지의 EnumSet 클래스는 열거타입 상수의 값으로 구성된 집합을 효과적으로 표현해준다.

-	EnumSet은 Set인터페이스를 완벽히 구현하며, 타입 안전하고 다른 Set 구현체와도 함께 사용할 수 있다.

```java
public class Text{
	public enum Style{ BOLD, ITALIC, UNDERLINE, STRIKETHROUGH }

	// 모든 클라이언트가 EnumSet으로 넘긴다고 짐작되는 상황이라도 인터페이스로 받는게 좋다. (Set<Style>)
	public void applyStyles(Set<Style> styles){...}
}


...

text.applyStyles(EnumSet.of(Style.BOLD, Style.ITALIC));
```

-	책에 있는 핵심 정리

	-	열거할 수 있는 타입을 한데 모아 집합 형태로 사용한다고 해도 비트 필드를 사용할 이유는 없다.
	-	EnumSet 클래스가 비트 필드 수준의 명료함과 성능을 제공한다.

#### 아이템 37 ordinal 인덱싱 대신 EnumMap을 사용하라

-	배열이나 리스트에서 원소를 꺼낼 때 ordinal 메서드로 인덱스를 얻는 코드가 있다.

	-	이 경우 비검사 형변환, 인덱스 의미 명시, 정수 사용으로 인한 보증필요(타입불안전) 등의 문제가 있다.

-	열거 타입을 키로 사용하도록 설계한 EnumMap 을 통해 해결할 수 있다.

	-	Map의 타입 안전성과 배열의 성능을 모두 얻어낼 수 있다.

```java
Map<Plant.LifeCycle, Set<Plant>> plantByLifeCycle = new EnumMap<>(Plant.LifeCycle.class);

for (Plant.LifeCycle lc : Plant.LifeCycle.vlaues())
	plantByLifeCycle.put(lc, new HashSet<>());

for (Plant p : garden)
	plantsByLifeCycle.get(p.lifeCycle).add(p);


// 스트림을 사용하여 코드를 더 줄일 수 있음
System.out.println(Arrays.stream(garden).collect(groupingBy(p -> p.lifeCycle, () -> new EnumMap<>(LifeCycle.class), toSet()));
```

-	책에 있는 핵심 정리
	-	배열의 인덱스를 얻기 위해 ordinal을 쓰는 것은 일반적으로 좋지 않으니, EnumMap을 사용하라.
	-	다차원 관계는 EnumMap<..., EnumMap<...>> 으로 표현해라.
