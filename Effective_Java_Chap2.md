이펙티브 자바
-------------

---

### 2장 객체 생성과 파괴

#### 아이템1 : 생성자 대신 정적 팩터리 메서드를 고려하라.

-	정적 팩토리 메소드로 쉽게 생성자를 사용하지 않고, 로직을 수행할 수 있다

	```java
	  // 생성자
	  Test test = new Test();
	  test.testPrint("test");


	  // 정적 팩토리 메소드
	  testStaticPrint("test");  
	```

-	장점

	-	이름을 가질 수 있다.

		```
		각 생성자가 어떤 역할을 하는지 정확히 기억하기 어려워 엉뚱한 것을 호출하는 실수를 할 수 있다.
		이름을 가질 수 있는 정적 팩토리 메소드에는 이런 제약이 없다.
		한 클래스에 시그니처가 같은 생성자가 여러 개 필요할 것 같으면, 생성자를 정적 팩토리 메소드로 바꾸고 각각의 차이를 잘 드러내는 이름을 지어주자.
		```

	-	호출될 때 마다 인스턴스를 새로 생성하지 않아도 된다.

	-	정적 팩토리 메소드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도된다. ?

-	단점

	-	정적 팩토리 메서드만 제공하면 하위 클래스를 만들 수 없다.
	-	프로그래머가 찾기 힘들다.

-	책에 있는 핵심정리

	-	정적 팩토리 메소드와 public 생성자는 각자의 쓰임새가 있으니 상대적인 장단점을 이해하고 사용하는 것이 좋다.<br> 그렇다고 하더라도 정적 팩토리를 사용하는 게 유리한 경우가 더 많으므로 무작정 public 생성자를 제공하던 습관이 있으면 고치자.

#### 아이템 2: 생성자에 매개변수가 많다면 빌더를 고려하라.

-	점층적 생성자 패턴: 필수매개변수만 받는 생성자, 필수 매개변수와 선택 매개변수 1개 받는 생성자, 필수 매개변수와 선택 매개변수 2개 받는 생성자, ..., 모든 매개변수가 있는 생성자 형태로 생성자를 늘려가는 방식

	```java
	  public class NutritionFacts {
	    private final int servingSize; // 필수
	    private final int servings; // 필수


	    private final int calories; // 선택
	    private final int fat; // 선택
	    private final int sodium; // 선택
	    private final int carbohydrate; // 선택


	    // 필수 매개변수
	    public NutritionFacts(int servingSize, int servings){
	      this(servingSize, servings, 0);
	    }


	    // 필수 매개변수 + 선택 매개변수 1개  
	    public NutritionFacts(int servingSize, int servings, int calories){
	      this(servingSize, servings, calories, 0);
	    }


	    // 필수 매개변수 + 선택 매개변수 2개  
	    public NutritionFacts(int servingSize, int servings, int calories, int fat){
	      this(servingSize, servings, fat, 0);
	    }


	    ...


	    // 필수 매개변수 + 선택 매개변수 모두
	    public NutritionFacts(int servingSize, int servings, int calories, int fat, int sodium, int carbohydrate){
	      this.servingSize = servingSize;
	      this.servings = servings;
	      this.calories = calroies;
	      this.fat = fat;
	      this.sodium = sodium;
	      this.carbohydrate = carbohydrate;  
	    }
	  }
	```

	> 매개변수 개수가 많아지면 점층적 생성자패턴은 코드를 작성하거나 읽기 어려워진다.

<br>

-	선택 매개변수가 많을 때 대안으로 자바빈즈패턴을 사용할 수 있음. <br> 자바빈즈 패턴 : 매개변수가 없는 생성자로 객체를 만든 후, setter 메서드들을 호출해 원하는 매개변수의 값을 설정하는 방식.

	```java
	public class NutritionFacts {
	    // 매개변수 초기화
	    private int servingSize = -1;
	    private int servings = -1;
	    private int calories = 0;
	    private int fat = 0;        
	    ...


	    public NutritionFacts() {}


	    // setter
	    public void setServingSize(int val) {
	        servingSize = val;
	    }


	    public void setServings(int val){
	        servings = val;
	    }


	    public void setCalories(int val){
	        calories = val;
	    }


	    public void setFat(int val){
	        fat = val;
	    }
	    ...


	}
	```

	> 객체 하나를 만들려면 메서드를 여러 개 호출해야 하고, 객체가 완전히 생성되지 전까지는 일관성이 무너진 상태에 놓여지게 됨.

<br>

-	점층적 생성자 패턴의 안정성과 자바빈즈 패턴의 가독성을 겸비한 빌더패턴. <br> 빌더 패턴: 필요한 객체를 직접 만드는 대신, 필수 매개변수만으로 생성자를 호출해 빅더 객체를 얻는다. 그 후 빌더객체가 제공하는 일종의 세터 메서드들로 원하는 선택 매개변수들을 설정한다.

	```java
	public class NutritionFacts{
	    private final int servingSize;
	    private final int servings;
	    private final int calories;
	    private final int fat;
	    ...


	    public static class Builder{
	        // 필수 매개변수
	        private final int servingSize;
	        private final int servings;


	        // 선택 매개변수 - 기본값을 초기화
	        private int calories = 0;
	        private int fat = 0;
	        ...


	        public Builder(int servingSize, int servings){
	            this.servingSize = servingSize;
	            this.servings = servings;
	        }


	        public Builder calories(int val){
	            calories = val;
	            return this;
	        }


	        public Builder fat(int val){
	            fat = val;
	            return this;
	        }


	        ...


	        public NutritionFacts build(){
	            return new NutritionFacts(this);
	        }


	    }


	        private NutritionFacts(Builder builder){
	            servingSize = builder.servingSize;
	            servings = builder.servings;
	            calories = builder.calories;
	            fat = builder.fat;


	            ...
	        }
	}


	// builder 사용예시
	NutritionFacts cocaCola = new NutritionFacts.Builder(240,8).calories(100).fat(200).carbohydrate(280).builder();
	```

	> 매개변수 유효성검사는 빌더의 생성자와 메서드에서 검사하면 된다. 또한 빌더패턴은 계층적으로 설계된 클래스와 함께 쓰기도 좋음.
	>
	> 빌더 생성 비용이 크지는 않지만, 성능이 민감한 상황에서는 문제가 될 수 도 있음. 점층적 생성자 패턴보다는 코드가 장황해서 매개변수 4개 이상일 경우 효율적임.

#### 아이템 3 : private 생성자나 열거 타입으로 싱글턴임을 보증하라.

-	싱글턴 이란?(singleton) : 인스턴스를 오직 하나만 생성할 수 있는 클래스

	> 클래스를 싱글턴으로 만들게 되면 싱글턴 인스턴스를 mock 구현으로 대체할 수 없기때문에 이 클래스를 사용하는 클라이언트를 테스트하기가 어려워질 수 있음.<br> 책에선 싱글턴을 만드는 방식을 소개하고 있음.

-	public static 멤버가 final 필드인 방식

	```java
	public class Elvis{
	    public static final Elvis INSTANCE = new Elvis();
	    private Elvis(){ ... }


	    public void leaveTheBuildings(){ ... }
	}
	```

	> private 생성자는 public static final 필드인 Elvis.INSTANCE를 초기화할 때 딱 한번 호출됨. (public 이나 protected 생성자 없음.)
	>
	> AccessibleObject.setAccessible 을 사용해 private 생성자를 호출할 수 있지만, 두번째 생성자가 생성되려 할 때 예외를 던지게 하여 방어하면 됨.

	-	장점1 : 해당 클래스가 싱글컨임을 API에 드러남.
	-	장점2: 간결함

-	정적 팩토리 방식의 싱글턴

	```java
	public class Elvis{
	    private static final Elvis INSTANCE = new Elvis();
	    private Elvis(){ ... }


	    public static Elvis getInstance(){
	        return INSTANCE;
	    }


	    public void leaveTheBuildings(){ ... }
	}
	```

	> Elvis.getInstance는 항상 같은 객체의 참조를 반환하므로, 제 2의 Elvis 인스턴스란 결코 만들어지지 않는다.

	-	장점1 : API를 바꾸지 않고 싱글턴이 아니게 변경가능. (팩터리 메서드를 호출하는 스레드별로 다른 인스턴스 넘겨줄 수 있음.)
	-	장점2 : 정적 팩터리를 제네릭 싱글턴 팩터리로 만들 수 있다.
	-	장점3 : 정적 팩터리의 메서드참조를 공급자(supplier)로 사용할 수 있다.
		-	(Evlics::getInstance 를 Supplier<Elvis> 로 사용 가능)

-	위의 두 방식의 싱글턴을 직렬화할 때는 Serializable을 구현한다고 선언하는 것으로는 부족함 (직렬화된 인스턴스를 역직렬화할 때 마다 새로운 인스턴스가 생성될 수 있음)

	-	모든 인스턴스 필드를 일시적(transient)이라고 선언
	-	readResolve메서드를 제공해야함

		```java
		private Object readResolve(){  
		    // 진짜 Elvis를 반환
		    return INSTANCE;
		}
		```

-	열거 타입 방식의 싱글턴 - 바람직한 방법

	```java
	public enum Elvis{
	    INSTANCE;
	    public void leaveTheBuildings(){ ... }
	}
	```

	> 간결하고, 추가 노력 없이 직렬화 가능. 복잡한 직렬화 상황이나, 리플렉션 공격에서도 제 2의 인스턴스가 생기는 일을 막아줌.

-	대부분 상황에서는 원소가 하나뿐인 열거 타입이 싱글턴을 만드는 가장 좋은 방법임. (만드려는 싱글턴이 Enum 외의 클래스를 상속해야하는 경우 제외)

#### 아이템 4 : 인스턴스화를 막으려거든 private 생성자를 사용하라.

-	정적 멤버만 담은 유틸리티 클래스는 인스턴스로 만들어 쓰려고 설계한 게 아니다. 하지만, 생성자를 명시하지 않으면 컴파일러가 자동으로 생성함

	> 추상 클래스로 만드는 거승로는 인스턴스화를 막을 수 없다. private 생성자를 추가하면 클래스의 인스턴스화를 막을 수 있다.

-	하지만 생성자가 존재하긴 하니 적절한 주석이 필요하다. (ex_ // 기본 생성자가 만들어지는 것을 막는다 (인스턴스화 방지용))

-	상속을 불가능하게 하는 효과도 존재함. (하위 클래스가 상위 클래스의 생성자에 접근할 방법이 없어진다)

#### 아이템 5 : 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라.

-	많은 클래스가 하나 이상의 자원에 의존하지만, 정적 유틸리티 클래스로 구현하거나 싱클턴으로 구현하는 방식은 훌륭하지 않다. (유연하지 않고, 테스트하기 어려움)

	> 사용하는 자원에 따라 동작이 달라지는 클래스에는 정적 유틸리티 클래스나 싱글턴 방식이 적합하지 않음

-	인스턴스를 생성할 때 생성자에 필요한 자원을 넘겨주는 방식이 적합하다 (의존 객체 주입은 생성자, 정적팩터리, 빌더에 응용할 수 있다.)

	> 불변을 보장하여 같은 자원을 사용하려는 여러 클라이언트가 의존 객체들을 안심하고 공유할 수 있다.

	```java
	public class SpellChecker{
	    private final Lexicon dictionary;


	    public SpellChecker(Lexicon dictionary){
	        this.dictionary = Objects.requireNonNull(dictionary);
	    }


	    ...
	}
	```

-	의존 객체 주입이 유연성과 테스트 용이성을 개선해주긴 하지만, 의존성이 많은 큰 프로젝트에서는 코드를 어지럽게 만들기도 함.

	> 스프링과 같은 의존 객체 주입 프레임워크를 사용하면 해소 가능

-	책에 있는 핵심정리

	-	클래스가 내부적으로 하나 이상의 자원에 의존하고, 그 자원이 클래스 동작에 영향을 준다면 싱글턴과 정적 유틸리티 클래스는 사용하지 않는 것이 좋다. 이 자원들을 클래스가 직접 만들게 해서도 안된다. 대신 필요한 자원을(혹은 그 자원을 만들어주는 팩터리를) 셍상자에(혹은 정적팩터리나 빌더에) 넘겨주자. 의존 객체 주입이라는 하는 이 기법은 클래스의 유연성, 재사용성, 테스트용이성을 기막히게 개선해준다.

#### 아이템 6 : 불필요한 객체 생성을 피하라.

-	생성자 대신 정적 팩터리 메서드를 제공하는 불변 클래스에서는 정적 팩터리 메서드를 사용해 불필요한 객체 생성을 피할 수 있다.

	> 생성자는 호출할 때 마다 새로운 객체를 만들지만, 팩터리 메서드는 전혀 그렇지 않다. (재사용 가능)

	```java
	// 이 메서드가 내부에서 만드는 정규표현식용 Patterns 인스턴스는 한번 쓰고 버려져서 곧바로 가비지 컬렉션 대상이 됨
	static boolean isRomanNumeral(String s){
	    return s.matches("^(?=.)M*(C[MD]|D?C{0,3})" + "(X[CL]|L?X{0,3})(I[XV]|V?I{0,3}$)");
	}


	//성능 개선 - 정규표현식을 표현하는 Pattern 인스턴스를 클래스 초기화 과정에서 직접 생성해서 캐싱
	public class RomanNumerals{
	    private static final Pattern ROMAN = Pattern.compile("^(?=.)M*(C[MD]|D?C{0,3})" + "(X[CL]|L?X{0,3})(I[XV]|V?I{0,3}$)");


	    static boolean isRomanNumeral(String s){
	        return ROMAN.matcher(s).matches();
	    }
	}
	```

-	불필요한 객체를 만들어내는 또 다른 예는 오토박싱이다.

	> 박싱된 기본타입보다는 기본 타입을 사용하고, 의도치 않은 오토박싱이 숨어들지 않도록 주의하자.

-	"객체 생성은 비싸니 피하자!"로 오해하지말 것!

	> 요즘 JVM은 작은 객체를 생성하고 회수하는 것은 크게 부담되지 않는다. 프로그램의 명확성, 간결성, 기능을 위해서 객체를 추가로 생성하는 것은 일반적으로 좋은 일이다.

-	아주 무거운 객체가 아닌 다음에야 단순히 객체 생성을 피하고자 자신만의 객체 풀을 만들지는 말자. (데이터베이스 연결 같은 경우 생성비용이 워낙 비싸니 재사용하는 편이 낫다.)

	> 일반적으로 자체 객체 풀은 코드를 헷갈리게 만들고 메모리 사용량을 늘리고 성능을 떨어뜨린다.

	-	객체 풀이란?

		-	Object Pool Pattern : 객체를 필요로 할 때 풀에 요청, 반환 일의 작업을 수행하는 패턴

			-	객체를 매번 할당,해제하지 않고 고정 크기 풀에 들어 있는 객체를 재사용함으로써 메모리 사용 성능 개선 가능
			-	많은 수의 인스턴스를 생성할 때 또는 무거운 오브젝트를 인스턴스화할 때 성능향상가능
			-	데이터베이스 연결, 네트워크 연결처럼 접근비용이 비싸면서 재사용 가능한 자원을 객체가 캡슐화할 때 사용

#### 아이템 7 : 다 쓴 객체 참조를 해제하라

-	일반적으로 자기 메모리를 직접 관리하는 클래스라면 프로그래머는 항시 메모리 누수에 주의해야 한다.

-	캐시 역시 메모리 누수를 일으키는 주범이다.

-	리스너 혹은 콜백 또한 메모리 누수의 주범이다.

-	책에 있는 핵심정리

	-	메모리 누수는 겉으로 잘 드러나지 않아 시스템에 수년간 잠복하는 사례도 있다. 이런 누수는 철저한 코드 리뷰나 힙 프로파일러 같은 디버깅 도구를 동원해야만 발견되기도 한다. 그래서 이런 종류의 문제는 예방법을 익혀두는 것이 매우 중요하다.

#### 아이템 8 : finalizer 와 cleaner 사용을 피하라

-	자바는 두 가지 객체 소멸자를 제공하는데 finalizer 는 오동작, 낮은성능, 이식성 문제의 원인이 되기도하다. 일반적으로 불필요

	-	자바 9에서는 finalizer를 사용자제(deprecated) API로 지정하고 cleaner 를 대안으로 소개한다.
	-	하지만 cleaner 또한 여전히 예측할 수 없고 느리고 일반적으로 불필요
	-	클래스에 finalizer를 달아주면 그 인스턴스의 자원회수가 제 멋대로 지연될 수 있다고함

		> finalizer 스레드가의 우선순위가 늦을 경우 / cleaner 는 자신을 수행할 스레드를 제어할 수 있지만, 여전히 백그라운드에서 수행되며 가비지 컬렉터의 통제하에 있어 즉시 수행의 보장이 없다.

-	상태를 영구적으로 수정하는 작업에서는 절대 finalizer나 cleaner에 의존해서는 안된다. (ex 데이터베이스의 영구 락 해제)

-	finalizer 공격에 보안문제를 일으킬 수 있음

	-	생성자나 직렬화 과정에서 예외가 발생하면, 이 생성되다 만 객체에서 악의적인 하위 클래스의 finalizer가 수행될 수 있게 된다. 성능저하 유발가능성 있음
		-	방어하러면? final로 선언하여 하위클래스를 만들지 못하게 하라

-	파일이나 스레드 등 종료해야 할 자원을 담고 있는 객체의 클래스에서 finalizer나 cleaner를 대신할 묘안은?

	-	AutoCloseable 구현해주고, 인스턴스를 다 쓰고나면 close 메서드를 호출 (예외가 발생해도 제대로 종료되도록 try-with-resources 사용해야 함.)
	-	각 인스턴스는 자신이 닫혔는 지 추적하는 것이 좋음

-	cleaner 와 finalizer 를 쓰는 곳

	-	자원의 소유자가 close 메서드를 호출하지 않았을 경우를 대비한 안전망

		-	즉시 호출되리라는 보장은 없지만, 자원회수를 늦게라도 해주는 것이 낫다 (가치가 있는 지 판단 후에 해줄 것!)
		-	FileInputStream, FileOutputStream, ThreadPoolExcutor 와 같이 자바 라이브러리의 일부 클래스는 안전망 역할의 finalizer를 제공함

	-	네이티브 피어와 연결된 객체에서

		-	네이티브 피어 : 일반 자바 객체가 네이티브 메서드를 통해 기능을 위임한 네이티브 객체
		-	일반 자바 객체 회수 시 네이티브객체까지 회수하지 못하니 cleaner나 finalizer가 처리
		-	성능저하를 감당할 수 없거나, 즉시 회수가 필요할 땐 close 사용할 것

-	책에 있는 핵심정리

	-	cleaner(자바 8까지는 finalizer)는 안전망 역할이니 중요하지 않은 네이티브 자원 회수용으로만 사용하자. 이 때도 불확실성과 성능 저하에 주의할 것
