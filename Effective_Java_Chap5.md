이펙티브 자바
-------------

---

### 5장 제네릭

-	제네릭을 사용하면 컬렉션이 담을 수 있는 타입을 컴파일러에 알려주게 된다.
	-	컴파일러는 알아서 형변환 코드를 추가해줌
	-	엉뚱한 타입의 객체를 넣으려는 시도를 컴파일 과정에서 차단하여 더 안전하고 명확한 프로그램을 만들어줌

#### 아이템 26 로 타입은 사용하지 말라

-	List< String>은 원소의 타입이 String인 리스트를 뜻하는 매개변수화 타입이다.

-	로 타입 : 제네릭 타입에서 타입 매개변수를 전혀 사용하지 않을 때 ( List< E> )

	> 오류는 가능한 한 발생 즉시, 이상적으로는 컴파일할 때 발견하는 것이 좋다.

-	로 타입을 쓰면 제네릭이 안겨주는 안전성과 표현력을 모두 잃게된다.

	```java
	// 로 타입으로 인해 런타입 오류가 발생할 때
	private final Collection stamps = ....;


	stamps.add(new Coin(...));


	for(Iterator i = stamps.iterator(); i.hasNext(); ){
	    Stamp stamp = (Stamp) i.next(); // ClassCastException !!
	    stamp.cancle();
	}


	------------------------------------------------------


	// 타입의 안정성 확보 : 이 경우 컴파일 오류가 발생한다.
	private final Collection<Stamp> stamps = ...;


	stamps.add(new Coin(...));


	for(Iterator i = stamps.iterator(); i.hasNext(); ){
	    Stamp stamp = (Stamp) i.next(); // incompatible types : Coin cannot be converted to Stamp
	    stamp.cancle();
	}


	```

-	List 와 List< Ojbect> 차이

	-	List는 제네릭이 발을 뺀 것임 (타입 안전성을 잃게됨!!)
	-	List< Object> 는 모든 타입을 허용한다는 의사를 컴파일러에 전달

-	원소의 타입을 몰라도 되는 로 타입을 쓰고 싶다면 와일드 카드 타입을 쓸 것! ( Set< ?> )

-	로 타입 예외

	-	class 리터럴에는 로 타입을 써야한다. (매개변수화 타입을 사용하지 못한다)
	-	instanceof : 런타입에는 제네릭 타입정보가 지워진다. 로 타입이든 비한정적 와일드카드 타입이든 instanceof는 똑같이 동작한다.

-	책에 있는 핵심 정리

	-	로 타입을 사용하면 런타임에 예외가 일어날 수 있으니 사용하면 안된다.
	-	로 타입은 제네릭이 도입되지 전 이전 코드와 호환성을 위해 제공될 뿐이다.
	-	Set< Object>는 어떤 타입의 객체도 저장할 수 있는 매개변수화 타입이다.
	-	Set<?>는 모종의 타입 객체만 저장할 수 있는 와일드카드 타입이다.

#### 아이템 27 비검사 경고를 제거하라

-	할 수 있는 한 모든 비검사 경고를 제거하라! 모두 제거한다면 그 코드는 타입 안전성이 보장된다. (런타입에 ClassCastException이 발생할 일이 없다.)

-	자바 7에서는 다이아몬드 연산자를 제공하는데, 이를 통해 컴파일러가 올바른 실제 타입 매개변수를 추론해준다.

	```java
	Set<Lark> exaltation = new HashSet<>();
	```

-	경고를 제거할 수는 없지만 타입 안전하다고 확신할 수 있다면 @SuppressWarnings("unchecked") 애노테이션을 달아 경고를 숨기자

	-	이 애노테이션은 개별 지역변수 선언부터 클래스 전체까지 어떤 선언에도 달 수 있다. (심각한 경고를 놓칠 수 있으니 클래스전체에는 달지 말 것 !)
	-	애노테이션을 사용할 때 그 경고를 무시해도 안전한 이유를 항상 주석으로 남길 것!

-	책에 있는 핵심 정리

	-	비검사 경고는 중요하니 무시하지 말자. 런터임에 ClassCastException을 일으킬 수 있다.
	-	경고를 없앨 방법을 찾지못한다면, 그 코드가 타입 안전함을 증명하고 가능한 범위를 좁혀 @SuppressWarnings("unchecked") 애너테이션으로 경고를 숨겨라. (숨긴 이유 주석으로 남길 것)
