이펙티브 자바
-------------

---

### 9장 일반적인 프로그래밍 원칙

-	자바 언어의 핵심요소에 집중한다.
-	지역변수, 제어구조, 라이브러리, 데이터 타입, 리플렉션, 네이티브 메서드, 최적화, 명명 규칙에 대해 살펴본다.

#### 아이템 57 지역변수의 범위를 최소화하라

-	아이템15 - '클래스와 멤버의 접근 권한을 최소화하라' 와 취지가 비슷하다.

-	지역변수의 유효범위를 최소로 줄이면 코드 가독성과 유지보수성이 높아지고 오류 가능성은 낮아진다.

-	지역변수의 범위를 줄이는 가장 강력한 기법은 역시 '가장 처음 쓰일 때 선언하기'다.

	-	미리 선언해두면 코드가 어수선해지고, 실제로 사용하는 시점에 초깃값이 기억나지 않을 수 있음

-	거의 모든 지역변수는 선언과 동시에 초기화해야 한다.

	-	초기화에 필요한 정보가 필요하지 않다면 선언을 미뤄야 함.
	-	변수를 초기화하는 표현식에서 검사 예외를 던질 가능성이 있다면 try 블록 안에서 초기화해야 한다.

-	반복문은 독특한 방식으로 변수 범위를 최소화해준다.

	-	반복문에서는 반복 변수의 범위가 반복문의 몸체, 그리고 for 키워드와 몸체 사이의 괄호 안으로 제한된다.
	-	반복 변수의 값을 반복문이 종료된 뒤에도 써야 하는 상황이 아니라면 while 문보다 for문을 쓰는 것이 낫다.

	```java
	// 컬렉션이나 배열을 순회하는 권장 관용구
	for(Element e : c){
	    ...
	}


	// 반복자가 필요할 때의 관용구
	for(Iterator<Element> i = c.iterator(); i.hasNext(); ){
	    Element e = i.next();
	    ...
	}
	```

	-	while 문의 경우 오타로 인한 실수가 발생할 수 있음.
	-	for 문은 이런 문제를 방지할 수 있다. (변수 유효범위로 같은 변수를 써도 영향을 주지 않음)

-	메서드를 작게 유지하고 한 가지 기능에 집중해야 한다.

	-	한 메서드에서 여러가지 기능을 처리한다면 한 기능과만 관련된 지역변수라도 다른 기능을 수행하는 코드에서 접근할 수 있을 것임.

#### 아이템 58 전통적인 for문 보다는 for-each문을 사용하라

-	반복자와 인덱스 변수는 코드를 지저분하게 할 뿐 우리에게 진짜 필요한 건 원소들 뿐이다.

	-	요소 종류가 늘어나면 오류가 생길 가능성이 높아진다.

		```java
		// 컬렉션 순회
		for(Iterator<Element> i = c.iterator(); i.hasNext(); ){
		    Element e = i.next();
		    ...
		}


		// 배열 순회
		for (int i = 0; i < a.length; i++){
		    ...
		}
		```

-	for-each문을 사용하면 해결된다. (enhanced for statement)

	-	반복자와 인덱스 변수를 사용하지 않으니 코드가 깔끔해지고 오류가 날 일도 없다.
	-	하나의 관용구로 컬레션과 배열 모두처리할 수 있어서 어떤 컨테이너를 다루는지 신경 쓰지 않아도 된다.

		```java
		for (Element e : element){
		    ...
		}
		```

-	for-each문을 사용할 수 없는 상황 (일반적인 for문을 사용할 것)

	-	파괴적인 필터링 : 컬렉션을 순회하면서 선택된 원소를 제거해야 한다면 반복자의 remove 메서드를 호출해야한다. (자바8부터는 Collection의 removeIf 메서드를 사용해 컬렉션을 명시적으로 순회하는 일을 피할 수 있다.)
	-	변경 : 리스트나 배열을 순회하면서 그 원소의 값 일부 혹은 전체를 교체해야 한다면 리스트의 반복자나 배열의 인덱스를 사용해야 한다.
	-	병렬 반복 : 여러 컬렉션을 병렬로 순회해야 한다면 각각의 반복자와 인덱스 변수를 사용해 엄격하고 명시적으로 제어해야 한다.

-	for-each문은 컬렉션과 배열은 물론 Iterable 인터페이스를 구현한 객체라면 순회할 수 있다.

	-	Iterable 을 처음부터 구현하기는 까다롭지만, 원소들의 묶음을 표현하는 타입을 작성해야 한다면 Iterable을 구현하는 쪽으로 고민하자

-	책에 있는 핵심 정리

	-	전통적인 for 문과 비교했을 때 for-each문은 명료하고, 유연하고, 버그를 예방해준다.
	-	성능저하도 없음
	-	가능한 모든 곳에서 for문이 아닌 for-each문을 사용할 것!!

#### 아이템 59 라이브러리를 익히고 사용하라

-	**표준 라이브러리를 사용하면 그 코드를 작성한 전문가의 지식과 여러분보다 앞서 사용한 다른 프로그래머들의 경험을 활용할 수 있다.**

	-	핵심적인 일과 크게 관련 없는 문제를 해결하느라 시간을 허비하지 않아도 된다.
	-	라이브러리는 노력하지 않아도 성능이 지속해서 개선된다.
	-	기능이 점점 많아진다.
	-	작성한 코드가 많은 사람에게 낯익은 코드가 된다. (다른 개발자들에게 유지보수하기 좋아짐.)

-	표준 라이브러리의 기능을 사용하는 것이 좋지만, 많은 프로그래머들은 직접 구현해서 쓰고있다.

	-	라이브러리에 그런 기능이 있는지 모르기 때문
	-	자바 메이저 릴리즈마다 새로운 기능이 추가되니 한번 쯤 확인해볼 것

-	모든 API 문서를 공부하기는 벅차겠지만 자바 프로그래머라면 java.lang, java.util, java.io와 그 하위 패키지들에는 익숙해져야한다.

	-	컬렉션 프레임워크, 스트림 라이브러리, java.util.concurrent 의 동시성 기능 등 알아두면 좋음!

-	라이브러리가 필요한 기능을 충분히 제공하지 못할 수 있다.

	-	우선 라이브러리를 사용하려 시도하자.
	-	그 다음 선택지는 고품질의 서드파티 라이브러리
	-	아니면 직접 구현하자.

#### 아이템 60 정확한 답이 필요하다면 float와 double은 피해라

-	float와 double 타입은 이진 부동 소수점 연산에 쓰이며, 넓은 범위의 수를 빠르게 정밀한 근사치로 계산하도록 설계되었다.

	-	따라서 정확한 결과를 사용할 떄는 사용하면 안된다.
	-	**금융 관련 계산과는 맞지 않는다.**
	-	0.1 혹은 10의 음의 거듭제곱 수를 표현할 수 없음.

-	금융계산에는 BigDecimal, int 혹은 long을 사용해야 한다.

-	BigDecimal의 단점

	-	기본 타입 보다 쓰기가 불편하다.
	-	느리다

-	책에 있는 핵심 정리

	-	정홛한 답이 필요한 계산에는 float나 double 을 피할 것
	-	소수점 추적은 시스템에 맡기고, 코딩 시의 불편함이나 성능 저하를 신경 쓰지 않겠다면 BigDecimal을 사용하라
		-	BigDecimal이 제공하는 여덟가지 반올림 모드를 이용하면 반올림을 완벽히 제어할 수 있음.
	-	성능이 중요하고 소수점을 직접 추적할 수 있고 숫자가 너무 크지 않다면 int나 long 사용할 것.
		-	아홉자리 십진수로 표현 가능하면 int
		-	열여덟자리 십진수로 표현 가능하면 long
		-	넘어가면 BigDecimal 사용

#### 아이템 61 박싱된 기본 타입보다는 기본 타입을 사용하라

-	자바의 데이터 타입은 크게 두 가지로 나눌 수 있다.

	-	int, double, boolean 같은 기본 타입
	-	String, List 같은 참조 타입

-	그리고 각각의 기본 타입에 대응하는 참조타입이 하나씩 있으며 이를 박싱된 기본 타입이라고 함. (Integer, Double, Boolean)

-	오토박싱과 오토언박싱으로 두 타입을 크게 구분하지 않고 사용할 수는 있지만, 그렇다고 차이가 사라지는 것은 아님.

	-	둘 사이에는 분명한 차이가 있으니 어떤 타입을 사용하는지는 상당히 중요하다. (주의해서 선택해야함.)

-	차이점

	-	기본 타입은 값만 가지고 있으나, 박싱된 기본 타입은 값에 더해 식별성이란 속성을 갖는다. (박싱된 기본 타입의 두 인스턴스는 값이 같아도 서로 다르다고 식별될 수 있다.)
	-	기본 타입의 값은 언제나 유효하나, 박싱된 기본 타입은 유효하지 않은 값 null을 가질 수 있다.
	-	기본 타입이 박싱된 기본타입보다 시간과 메모리 사용면에서 더 효율적이다.

-	박싱된 기본타입이 쓰이는 경우

	-	컬렉션의 원소, 키, 값으로 쓰임. 컬렉션은 기본 타입을 담을 수 없으므로 어쩔 수 없이 박싱된 기본 타입을 써야함.
	-	매개변수화 타입이나 매개변수화 메서드의 타입 매개변수 (자바가 타입 매개변수로 기본 타입을 지원하지 않기 때문)
	-	리플렉션을 통해 메서드를 호출할 때

-	책에 있는 핵심정리

	-	기본 타입과 박싱된 기본 타입 중 하나를 선택해야 한다면 가능하면 기본 타입을 사용하라. (간단하고 빠르다)
	-	박싱된 기본 타입을 써야 한다면 주의를 기울이자.
		-	오토 박싱된 기본 타입을 사용할 때의 번거로움을 줄여주지만, 그 위험까지 없애주지는 않는다.
		-	두 박싱된 기본 타입을 == 연산자로 비교한다면 식별성 비교가 이뤄지는데, 이는 원하는 결과가 아닐 가능성이 크다.
		-	같은 연산에서 기본 타입과 박싱된 기본 타입을 혼용하면 언박싱이 이뤄지며, 언박싱 과정에서 NullPointerException을 던질 수 있음.
		-	기본타입을 박싱하는 작업은 필요없는 객체를 생성하는 부작용이 생길 수 있음.

#### 아이템 62 다른 타입이 적잘하다면 문자열 사용을 피하라

-	문자열은 다른 값 타입을 대신하기에 적합하지 않다.

	-	입력받을 데이터가 진짜 문자열일 때만 그렇게 하는 좋음.

-	문자열은 열거 타입을 대신하기에 적합하지 않음.

-	문자열은 혼합타입을 대신하기에 적합하지 않음

	-	두 요소를 문자로 구분하여 사용하는 등으로 사용한다면 각 개별 요소 접근 시 문자일 파싱으로 인해 느리고, 오류가능성이 커짐.
	-	차라리 전용 클래스를 만드는 편이 낫다

-	문자열로 권한을 표현하기에 적합하지 않음.

#### 아이템 63 문자열 연결은 느리지 주의하라

-	문자열 연결 연산자로 문자열 n개를 잇는 시간은 n*n 에 비례한다.

	-	문자열은 불변 아이팀이라서 두 문자열을 연결할 경우 양쪽의 내용을 모두 복사해야하므로 성능 저하는 피할 수 없는 결과다.

-	성능을 포기하고 싶지 않다면 String 대신 StringBuilder를 사용하자

	```java
	// 문자열 연결을 잘못 사용예시
	public String statement(){
	    String result= "";
	    for(int = 0; i < numItems(); i++){
	        result += lineForItem(i);
	    }
	    return result;
	}


	// StringBuilder를 사용예시
	public String statement2(){
	    StringBuilder b = new StringBuilder(numItem() * LINE_WIDTH);


	    for(int i = -; i < numItems(); i++){
	        b.append(lineForItem(i));
	    }


	    return b.toString();
	}
	```

-	책에 있는 핵심정리

	-	성능에 신경써야한다면 많은 문자열을 연결할 때는 문자열 연결 연산자(+)를 피하자.
	-	대신 StringBuilder의 append 메서드를 사요하라.
	-	문자 배열을 사용하거나, 문자열을 연결하지 않고 하나씩 처리하는 방법도 있다.

#### 아이템 64 객체는 인터페이스를 사용해 참조하라

-	아이템 51의 '매개변수 타입으로 클래스가 아니라 인터페이스를 사용하라' 를 '객체는 클래스가 아닌 인터페이스로 참조하라' 까지 확장할 수 있다.

-	적합한 인터페이스만 있다면 매개변수뿐 아니라 반환값, 변수, 필드를 전부 인터페이스 타입으로 선언하라.

	-	객체의 실제 클래스를 사용해야 할 상황은 오직 생성자로 생성할 때 뿐이다.

```java
// 좋은 예
Set<Son> sonSet = new LinkedHashSet<>();

// 나쁜 예
LinkedHashSet<Son> sonSet = new LinkedHashSet<>();
```

-	위와 같이 인터페이스를 타입으로 사용하는 습관을 길러두면 프로그램이 훨씬 유연해질 것이다.

	-	나중에 구현 클래스를 교체하고자 한다면 새 클래스의 생성자(또는 다른 정적팩터리)를 호출해주기만 하면 된다.
	-	**단, 원래의 클래스가 인터페이스의 일반 규약 이외의 특별한 기능을 제공하며, 주변 코드가 이 기능에 기대어 동작한담녀 새로운 클래스도 반드시 같은 기능을 제공해야 한다.**

-	적합한 인터페이스가 없다면 당연히 클래스로 참조해야 한다.

	-	String 이나 BigInteger 같은 값 클래스 (값 클래스를 여러가지로 구현될 수 있다고 생각하고 설계하는 일은 거의 없음)
	-	클래스 기반으로 작성된 프레임워크가 제공하는 객체들 (OutputStream 등 java.io 패키지의 여러 클래스)
	-	인터페이스에는 없는 특별한 메서드를 제공하는 클래스 (PriorityQueue 클래스-comparator 메서드 제공함 등)

> 적합한 인터페이스가 없다면 필요한기능을 맍고하는 가장 덜 구체적인 클래스 타입으로 사용하자.

#### 아이템 65 리플렉션보다는 인터페이스를 사용하라

-	리플렉션 기능을 이용하면 프로그램에서 임의의 클래스에 접근할 수 있다.

	-	Class 객체가 주어지면 그 클래스의 생성자, 메서드, 필드에 해당하는 Constructor, Method, Field 인스턴스를 가져올 수 있고, 이 인스턴스들로 그 클래스의 멤버 이름, 필드 타입, 메서드 시그니처 등을 가져올 수 있다.
	-	Constructor, Method, Field 인스턴스를 이용해 각각에 연결된 실제 생성자, 메서드, 필드를 조작할 수도 있다. (이 인스턴스들을 통해 해당 클래스의 인스턴스를 생성하거나, 메서드를 호출하거나, 필드에 접근할 수 있음)
	-	예를 들어 Method.invoke는 어떤 클래스의 어떤 객체가 가진 어떤 메서드라도 호출할 수 있게 해줌.
	-	리플렉션을 이용하면 컴파일 당시에 존재하지 않던 클래스도 이용할 수 있다.

-	리플렉션 단점

	-	컴파일타임 타입 검사가 주는 이점을 하나도 누릴 수 없다
	-	리플렉션을 이용하면 코드가 지저분해지고 장황해짐
	-	성능이 떨어진다

-	리플렉션을 써야하는 복잡한 애플리케이션들도 사용을 점차 줄이고 있음

-	리플렉션은 아주 제한된 형태로만 사용해야 그 단점을 피하고 이점을 취할 수 있다.

	-	리플렉션은 인스턴스 생성에서만 쓰고, 이렇게 만든 인스턴스느 인터페이스나 상위 클래스로 참조해 사용하자

-	책에 있는 핵심 정리

	-	리플렉션은 복잡한 특수 시스템을 개발할 때 필요한 강력한 기능이지만, 단점도 많다.
	-	컴파일타임에는 알 수 없는 클래스를 사용하는 프로그램을 작성한다면 리플렉션을 사용해야 할 것이다.
	-	단, 되도록 객체 생성에만 사용하고, 생성한 객체를 이용할 때는 적절한 인터페이스나 컴파일 타임에 알 수 있는 상위 클래스로 형변환해서 사용해야 한다.

#### 아이템 66 네이티브 메서드는 신중히 사용해라

-	자바 네이티브 인터피에스(Java Native Interface, JNI)는 자바 프로그램이 네이티브 메서드를 호출하는 기술이다. (C나 C++ 같은 네이티브 프로그래밍 언어로 작성한 메서드)

-	네이티브 메서드 주요 쓰임

	-	레지스트리 같은 플랫폼 특화 기능 사용
	-	네이티브 코드로 작성된 기존 라이브러리를 사용(레거시 데이터를 사용하는 레거시 라이브러리)
	-	성능 개선을 목적으로 성능에 결정적인 영향을 주는 영역만 따로 네이티브 언어로 작성

-	플랫폼 특화 기능을 활용하려면 네이티브 메서드를 사용해야하지만 자바가 성숙해가면서 하부 플랫폼의 기능들을 점차 흡수하고 있다.

	-	네이티브 메서드를 사용할 필요가 계속 줄어들고 있음 (자바9의 procss API )

-	성능을 개선할 목적으로 네이티브 메서드를 사용하는 것은 거의 권장하지 않는다.

	-	JVM이 많이 발전함. 대부분 작업에서 자바는 다른 플랫폼에 견줄만한 성능을 보임.
	-	고성능의 다중 정밀 연산이 필요한 자바 프로그래머라면 네이티브 메서드를 통해 GMP를 사용하는 것을 고려해도 좋다

-	네이티브 언어가 안전하지 않으므로 네이티브 메서드를 사용하는 애플리케이션도 메모리 훼손 오류로부터 더 이상 안전하지 않다.

	-	네이티브 언어는 자바보다 플랫폼을 많이 타서 이식성이 낮다.
	-	디버깅도 더 어려움
	-	주의하지 않으면 속도가 더 느려짐
	-	가비지 컬렉터가 네이티브 메모리는 자동 회수하지 못하고, 심지어 추적조차 할 수 없음
	-	자바 코드와 네이티브 코드의 경계를 넘나들 때마다 비용도 추가됨
	-	네이티브 코드와 자바코드 사이의 접착 코드를 작성해야하는데 이는 귀찮고 가독성이 떨어짐.

-	책에 있는 핵심 정리

	-	네이티브 메서드를 사용하려거든 한번 더 생각하라.
	-	네이티브 메서드가 성능을 개선해 주는 일은 많지 않다.
	-	저수준 자원이나 네이티브 라이브러리를 사용해야만 해서 어쩔 수 없더라도 네이티브 코드는 최소환만 사용하고 철저히 테스트하라.
	-	네이티브 코드 안에 숨은 단 하나의 버그가 애플리케이션 전체를 훼손할 수도 있다.

#### 아이템 67 최적화 신중히 하라

-	최적화 격언

	-	자그마한 효율성은 모두 잊자. 섣부른 최적화가 만악의 근원이다.
	-	최적화를 할 때는 다음 두 규칙을 따르라.

		-	1) 하지마라
		-	2) 아직 하지마라. 다시 말해, 완전히 명백한 해법을 찾을 때까지는 하지마라.

	-	각각의 최적화 시도 전후로 성능을 측정하라

-	최적화는 좋은 결과보다는 해로운 결과로 이어지기 쉽거, 섣불리 진행하면 특히 더 그렇다.

> 빠른 프로그램보다는 좋은 프로그램을 작성하라.

-	좋은 프로그램은 정보 은닉 원칙을 따르므로 개별 구성요소의 내부를 독립적으로 설계할 수 있다.

	-	따라서 시스템의 나머지에 영향을 주지 않고도 각 요소를 다시 설계할 수 있다.

-	프로그램을 완성할 때까지 성능 문제를 무시하라는 뜻이 아님.

	-	완성된 설계의 기본 틀을 변경하려다보면 유지보수하거나 개선하기 어려운 꼬인 구조의 시스템이 만들어지기 쉽기 때문

-	성능을 제한하는 설계를 피히라.

	-	완성 후 변경하기가 가장 어려운 설계 요소는 바로 컴포넌트끼리 혹은 외부 시스템과의 소통방식임 (API, 네트워크 프로토콜, 영구 저장용 데이터 포맷 등)
	-	이런 설계요소들은 완성 후에는 변경하기 어렵거나 불가능할 수 있으며 동시에 시스템 성능을 심각하게 제한할 수 있음

-	API를 설계할 때 성능에 주는 영향을 고려하라.

	-	public 타입을 가변으로 만들면, 즉 내부 데이터를 변경할 수 있게 만들면 불필요한 방어적 복사를 수없이 유발할 수 있다.
	-	컴포지션으로 해결할 수 있음에도 상속 방식으로 설계한 public 클래스는 상위 클래스에 영원히 종속되며 그 성능 제약까지 물려받는다.
	-	인터페이스가 있는데 굳이 구현타입을 사용하는 것 역시 좋지 않음 (특정 구현체에 종속되어 다른 좋은 구현체가 나오더라도 사용못함)

-	성능을 위해 이미 설계된 API를 왜곡하는 건 매우 안좋은 생각이다.

	-	왜곡된 API이나 이를 지원하는 데 따르는 고통은 영원하다

-	프로파일링 도구는 최적화 노력을 어디에 집중해야 할지 찾는 데 도움을 준다.

	-	이런 도구는 개별 메서드의 소비 시간과 호출 횟수 같은 런타임 정보를 제공하여, 집중할 곳은 물론 알고리즘을 변경해야 한다는 사실을 알려주기도 한다.

#### 아이템 68 일반적으로 통용되는 명명 규칙을 따르라

-	자바의 명명규칙은 철자와, 문법 두 범주로 나뉘는데 이 규칙을 어긴 API는 사용하기 어렵고, 유지보수하기 어렵다.

-	철자 규칙

	-	패키지와 모듈 이름은 각 요소를 점(.)으로 구분하여 계층으로 짓는다.

		-	조직 바깥에서 사용될 패키지라면 도메인 이름을 역순으로 사용한다. (com.google)

		-	예외적으로 표준 라이브러리와 선택된 패키지들은 각각 java와 javax로 시작한다.

		-	패키지 이름의 나머지는 해당 패키지를 설명하는 하나 이상의 이뤄짐. 각 요소는 8자 이하의 짧은단어로 한다.

	-	클래스와 인터페이스의 이름은 하나 이상의 단어로 이뤄지며, 각 단어는 대문자로 시작한다.

		-	널리 통용된 줄임말을 제외하고 줄여쓰지 않도록 한다.

	-	메서드와 필드 이름은 첫글자를 소문자로 쓴다는 점만 빼고 클래스 명명규칙과 같다.

		-	단 상수 필드는 예외. (모두 대문자, 단어사이는 밑줄로 NEGATIVE_INFINITY 등) (static final 필드 )

	-	타입 매개변수 이름은 보통 한 문자로 표현

		-	임의의 타입엔 T / 컬렉션 원소의 타입은 E / 맵의 키와 값에는 K, Y / 예외에는 X / 메서드의 반환 타입에는 R

-	문법 규칙 (더 유연하고 논란도 많음)

	-	객체를 생성할 수 있는 클래스의 이름은 보통 단수 명사나 명사구 (Thread, PriorityQueue 등)
	-	객체를 생성할 수 없는 클래스의 이름은 보통 복수형 명사 (Collectors, Collections 등 )
	-	인터페이스 이름은 클래스와 똑같이 짓거나 able 혹은 ible로 끝나는 형용사로 짓는다 (Runnable, Iterable 등)
	-	애너테이션은 지배적인 규칙없이 명사,동사,전치사,형용사 두루 사용
	-	어떤 동작을 수행하는 메서드의 이름은 동사나 동사구 (append)
	-	boolean 값을 반환하는 메서드라면 보통 is나 has 로 시작하고 명사나 명사구 로 끝나게 짓는다 (isDigit, 등)
	-	해당 인스턴스의 속성을 반환하는 메서드의 이름은 보통 명사, 혹은 get으로 시작하는 동사구로 짓는다 (size, getTime 등)
	-	객체의 타입을 바꿔서 다른 타입의 또 다른 객체를 반환하는 인스턴스의 메서드의 이름은 보통 toType으로 짓는다 (toString, toArray 등)
	-	객체의 내용을 다른 뷰로 보여주는 메서드의 이름은 보통 asType 형태로 짓는다 (asList 등)
	-	객체의 값을 기본 타입 값으로 반환하는 메서드이름은 보통 typeValue 형태 (intValue 등)

-	책에 있는 핵심 정리

	-	표준 명명 규칙을 체화하여 자연스럽게 베어 나오도록 하자.
