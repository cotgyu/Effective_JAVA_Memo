이펙티브 자바
-------------

---

### 4장 클래스와 인터페이스

#### 아이템 15 : 클래스와 멤버의 접근 권한을 최소화하라

-	잘 설계된 컴포넌트는 모든 내부 구현을 완벽히 숨겨, 구현과 API를 깔끔히 분리한다.

	-	오직 API를 통해서만 다른 컴포넌트와 소통하며 서로의 내부 동작방식에는 전혀 개의치 않는다.

-	정보 은닉의 장점

	-	시스템 개발 속도를 높인다. 여러 컴포넌트를 병렬로 개발할 수 있다.
	-	시스템 관리 비용을 낮춘다. 각 컴포넌트를 빨리 파악하여 디버깅할 수 있고, 다른 컴포넌트로 교체하는 부담도 적다.
	-	정보 은닉 자체가 성능을 높여주진 않지만, 성능 최적화에 도움을 준다. 다른 컴포넌트에 영향을 주지 않고 해당 컴포넌트만 최적화할 수 있기 때문
	-	소프트웨어 재사용을 높인다. 외부에 거의 의존하지 않고 독자적으로 동작하는 컴포넌트라면 낯선환경에서도 유용하게 쓰일 가능성이 크다.
	-	시스템 전체가 완성되지 않은 상태에서도 개발 컴포넌트의 동작을 검증할 수 있다.

-	자바의 각 요소의 접근성은 그 요소가 선언된 위치와 접근 제한자로 정해진다.

	-	이 접근제한자를 제대로 활용하는 것이 정보 은닉의 핵심

	> 모든 클래스와 멤버의 접근성을 가능한 한 좁혀야 한다.

	-	private : 멤버를 선언한 톱 레벨 클래스에서만 접근할 수 있다.
	-	package-private : 멤버가 소속된 패키지 안의 모든 클래스에서 접근할 수 있다. (접근제한자를 명시하지 않았을 때 적용되는 패키지 접근 수준. 단, 인터페이스의 멤버는 기본적으로 public)
	-	protected : package-private의 접근범위를 포함하며, 이 멤버를 선언한 클래스의 하위클래스에서도 접근할 수 있다.
	-	public : 모든 곳에서 접근가능

-	클래스의 공개 API를 세심히 설계한 후, 그 외의 모든 멤버는 private 으로 만들자.

-	public 클래스의 인스턴스 필드는 되도록 public이 아니어야 한다.

	-	그 필드에 담을 수 있는 값을 제한할 힘을 잃게 된다. (그 필드와 관련된 모든 것은 불변식을 보장할 수 없게 됨)

-	클래스에서 public static final 배열 필드를 두거나 이 필드를 반환하는 접근자 메서드를 제공해서는 안 된다.

	-	길이가 0이 아닌 배열은 모두 변경이 가능함
	-	이런 필드나 접근자를 제공한다면 클라이언트에서 그 배열의 내용을 수정할 수 있게 된다.
	-	해결책

		```java
		private static final Thing[] PRIVATE_VALUES = {...};
		// 방어적 복사
		public static final Thing[] values(){
		    return PRIVATE_VALUES.clone();
		}
		```

-	자바 9에서 모듈 시스템이라는 개념이 도입되면서 암묵적인 접근 수준이 추가됨.

	-	모듈은 자신이 속하는 패키지 중 공개할 것들을 선언한다. (관례상 module-info.java)
		-	protected 혹은 public 멤버라도 해당 패키지를 공개하지 않았다면 모듈 외부에서는 접근할 수 없다.

-	책에 있는 핵심정리

	-	프로그램 요소의 접근성은 가능한 한 최소한으로 하라.
	-	꼭 필요한 것만 최소한의 public API를 설계하자.
	-	public 클래스는 상수용 public static final 필드 외에는 어떠한 public 필드도 가져서는 안된다. public static final 필드가 참조하는 객체가 불변인지 확인하라.

#### 아이템 16 : public 클래스에서는 public 필드가 아닌 접근자 메서드를 사용하라

-	이런 클래스는 데이터 필드에 직접 접근할 수 있으니 캡슐화의 이점을 제공하지 못한다. (불변식을 보장할 수 없다. 외부에서 필드에 접근할 때 부수작업을 수 없다.)

	```java
	class Point{
	    public double x;
	    public double y;
	}
	```

-	필드를 모두 private 으로 바꾸고 public 접근자를 통해 데이터를 캡슐화한다.

	```java
	class Point{
	    private double x;
	    private double y;


	    public Point(double x, double y){
	        this.x = x;
	        this.y = y;
	    }


	    public double getX(){
	        return x;
	    }
	    public double getY(){
	        return y;
	    }


	    public void setX(double x){
	        this.x = x;
	    }
	    public void setY(double y){
	        this.y = y;
	    }
	}
	```

> 패키지 바깥에서 접근할 수 있는 클래스라면 접근자를 제공함으로써 클래스 내부 표현 방식을 언제든 바꿀 수 있는 유연성을 얻을 수 있다.

-	책에 있는 핵심 정리
	-	public 클래스는 절대 가변 필드를 직접 노출해서는 안된다. 불변 필드라면 노출해도 덜 위험하지만 완전히 안심할 수는 없다.
	-	package-private 클래스나 private 중첩 클래스에서는 종종 필드를 노출하는 편이 나을때도 있다.

#### 아이템 17 : 변경 가능성을 최소화하라

-	불변 클래스는 인스턴스의 내부 값을 수정할 수 없는 클래스다. 객체가 파괴되는 순간까지 달라지지 않는다.

-	클래스를 불변으로 만들려면 다섯 가지 규칙을 따르면 된다.

	-	객체의 상태를 변경하는 메서드를 제공하지 않는다.
	-	클래스를 확장할 수 없도록 한다 : 하위클래스에서 부주의하게 혹은 나쁜 의도로 객체의 상태를 변하게 만드는 상태를 막아준다.
	-	모든 필드를 final로 선언한다
	-	모든 필드를 private으로 선언한다 : 필드가 참조하는 가변 객체를 클라이언트에서 직접 접근해 수정하는 일을 막아준다.
	-	자신 외에는 내부의 가변 컴포넌트에 접근할 수 없도록 한다.

-	불변 객체는 근본적으로 스레드 안전하여 따로 동기화할 필요 없다. 불변 객체에 대해서는 그 어떤 스레드도 다른 스레드에 영향을 줄 수 없으니 불변 객체는 안심하고 공유할 수 있다.

-	불변 클래스는 자주 사용되는 인스턴스를 캐싱하여 같은 인스턴스를 중복으로 생성하지 않게 해주는 정적팩터리를 제공할 수 있다.

	-	정적 팩터리를 통해 여러 클라이언트가 인스턴스를 공유하여 메모리 사용량과 가비지 컬렉션 비용을 줄일 수 있음.

-	불변 클래스의 단점 : 값이 다르면 반드시 독립된 객체로 만들어야한다. (성능 부하 문제)

-	자신을 상속하지 못하게 하는 쉬운 방법은 final 클래스로 선언하는 것이지만, 모든 생성자를 private 혹은 package-private으로 만들고 public 정적 팩터리를 제공하는 것이 더 유연한 방법이다.

	-	바깥에서 볼 수 없는 package-private 구현 클래스를 원하는 만큼 만들어 활용할 수 있다.
	-	다수의 구현 클래스를 활용한 유연성을 제공하고, 이에 더해 다음 릴리스에서 객체 캐싱 기능을 추가해 성능을 끌어올릴 수도 있다.

	```java
	public class Complex{
	    private final double re;
	    private final double im;


	    private Complex(double re, double im){
	        this.re = re;
	        this.im = im;
	    }


	    public static Complex valueOf(double re, double im){
	        return new Complex(re, im);
	    }
	}


	```

-	정리

	-	getter가 있다고해서 무조건 setter를 만들지는 말자. (클래스는 꼭 필요한 경우가 아니라면 불변이어야 한다.)
	-	불변으로 만들 수 없는 클래스라도 변경할 수 있는 부분을 최소한으로 줄이자.
	-	다른 합당한 이유가 없다면 모든 필드는 private final 이어야 한다.
	-	생성자는 불변식 설정이 모두 완료된, 초기화가 완벽히 끝난 상태의 객체를 생성해야 한다.
	-	확실한 이유가 없다면 생성자와 정적 팩터리 외에는 그 어떤 초기화 메서드도 public으로 제공해서는 안된다.
	-	java.util.concurent 패키지의 CountDownLatch 클래스를 참고해 볼 것

#### 아이템 18 : 상속보다는 컴포지션을 사용하라

-	상속은 코드를 재사용하는 강력한 수단이지만 잘못 사용하면 오류를 내기 쉬운 소프트웨어를 만들게 된다.

-	확장할 목적으로 설계되었고 문서화가 잘 된 클래스라면 안전하지만 일반적인 구체 클래스를 패키지 경계를 넘어, 즉 다른 패키지의 구체클래스를 상속하는 것인 위험하다.

	-	메서드 호출과 달리 상속은 캡슐화를 깨뜨린다.
	-	상위 클래스가 어떻게 구현되느냐에 따라 하위 클래스의 동작에 이상이 생길 수 있다. (상위 클래스는 릴리스마다 내부 구현이 달라질 수 있으므로

		-	상위클래스의 메서드 재정의 시 문제발생 우려
		-	기존 클래스의 내부 구현방식에 영향

-	피해가는 방법

	-	기존 클래스를 확장하는 대신 새로운 클래스를 만들고 private 필드로 기존 클래스의 인스턴스를 참조하는 방법
	-	기존 클래스가 새로운 클래스의 구성요소로 쓰인다고 해서 컴포지션 이라 함
	-	새로운 클래스는 기존 클래스의 내부 구현 방식의 영향에서 벗어나게 됨
	-	래퍼클래스는 콜백 프레임워크와 어울리지 않는다. (콜백 때 래퍼가 아닌 내부 객체를 호출하게 됨)

	```java
	// 새로운 클래스 (래퍼클래스)
	public class InstrumentedSet<E> extends ForwardingSet<E>{
	    private int addCount = 0;


	    private InstrumentedSet(Set<E> s){
	        super(s);
	    }


	    @Override
	    public boolean add(E e){
	        addCount++;
	        return super.add(e)
	    }
	    ...
	}


	// 전달 : 기존 클래스의 대응하는 메서드를 호출해 그 결과를 반환.
	public class ForwardingSet<E> implements Set<E> {
	    private final Set<E> s;


	    public ForwardingSet(Set<E> s) {
	        this.s = s;
	    }


	    public boolean add(E e){
	        return s.add(e);
	    }


	    @Override
	    public boolean equals(Object o){
	        return s.equals(o);
	    }


	     ...
	}


	```

-	책에 있는 핵심정리

	-	상속은 강력하지만 캡슐화를 해친다는 문제가 있다. 상위 클래스와 하위 클래스가 순수한 is-a 관계일 때만 써야한다. (상위 클래스가 확장을 고려하지 않았다면 문제가 생길 수 있음)
	-	상속의 취약점을 피하려면 상속 대신 컴포지션과 전달을 사용할 것. 래퍼 클래스는 하위 클래스보다 견고하고 강력하다.

#### 아이템 19 : 상속을 고려해 설계하고 문서화하라. 그러지 않았다면 상속을 금지하라.

-	상속용 클래스는 재정의할 수 있는 메서드들을 내부적으로 어떻게 이용하는지 문서로 남겨야 한다.

	-	클래스를 안전하게 상속할 수 있도록 하려면 내부 구현 방식을 설명해야만 한다.

	-	널리 쓰일 클래스를 상속용으로 설계한다면 문서화한 내부 사용 패턴과 protected 메서드와 필드를 구현하면서 선택한 결정에 영환히 책임져야 함을 잘 인식해야 한다.

-	상속용 클래스의 생성자는 직접적으로든 간접적으로든 재정의 가능 메서드를 호출해서는 안된다.

	-	상위 클래스의 생성자가 하위 클래스의 생성자보다 먼저 실행되므로 하위 클래스에서 재정의한 메서드가 하위 클래스의 생성자보다 먼저 호출된다.
	-	이 때, 재정의한 메서드가 하위클래스의 생성자에서 초기화하는 값에 의존한다면 의도대로 동작하지 않을 것임.

-	책에 있는 핵심정리

	-	상속용 클래스를 설계하려면 클래스 내부에서 스스로 어떻게 사용하는지 모두 문서로 남겨야하며, 일단 문서화한 것은 그 크랠스가 쓰이는 한 반드시 지켜야한다. (그렇지 않으면 하위 클래스를 오동작하게 만들 수 있음)
	-	클래스를 확장해야할 명백한 이유가 떠오르지 않는다면 상속을 금지하는 편이 나음 (클래스를 final로 선언하거나 생성자 모두를 외부에서 접근할 수 없도록 만들면 됨)

#### 아이템 20 : 추상 클래스보다는 인터페이스를 우선하라

-	자바가 제공하는 다중 구현 메커니즘은 인터페이스와 추상 클래스이다.

	-	추상클래스는 정의한 타입을 구현하는 클래스는 반드시 추상 클래스의 하위 클래스가 되어야 한다. (새로운 타입을 정의하는 데 제약이 생긴다.)
	-	반면 인터페이스가 선언한 메서드는 모두 정의하고 그 일반 규약을 잘 지킨 클래스라면 다른 어떤 클래스를 상속했던 같은 타입으로 취급된다.

-	기존 클래스 위에 새로운 추상 클래스를 끼워넣기는 어렵지만, 새로운 인터페이스를 구현해 넣는 것 쉽다.

-	인터페이스는 믹스인 정의에 알맞다.

	-	믹스인 : 클래스가 구현할 수 있는 타입으로, 믹스인을 구현한 클래스에 원래의 주된 타입 외에도 특정 선택적 행위를 제공한다고 선언하는 효과를 가진다.
	-	추상클래스는 정의할 수 없다. : 클래스는 두 부모를 섬길 수 없고, 클래스 계층구조에는 믹스인을 삽입하기에 합리적인 위치가 없음

-	인터페이스로는 계층 구조가 없는 타입 프레임워크를 만들 수 있다.

-	인터페이스의 메서드 중 구현 방법이 명백한 것이 있다면, 그 구현을 디폴트 메서드로 제공해 편의를 제공해줄 수 있다.

	-	디폴트 메서드를 제공할 때는 상속하려는 사람을 위한 설명을 @implSepc 자바독 태그를 붙여 문서화해야 한다.

-	템플릿 메서드 패턴 : 인터페이스와 추상 골격 구현 클래스를 함께 제공하는 식으로 인터페이스와 추상 클래스의 장점을 모두 취하는 방법

	-	인터페이스로는 타입을 정의하괴, 필요하면 디폴트 메서드 몇개도 함께 제공. 골격 구현 클래스는 나머지 메서드들까지 구현.
	-	골격구현 클래스의 이름은 AbstractInterface (ex_ AbstractCollection, AbstractSet, AbstractList 등)
	-	추상 클래스처럼 구현을 도와주는 동시에, 추상 클래스로 타입을 정의할 때 따라오는 심각한 제약에서 자유룝다.

-	책에 있는 핵심정리

	-	일반적으로 다중 구현용 타입으로는 인터페이스가 가장 정합하다.
	-	복잡한 인터페이스라면 구현하는 수고를 덜어주는 골격 구현을 함께 제공하는 방법을 꼭 고려해보자.

#### 아이템 21 : 인터페이스는 구현하는 쪽을 생각해 설계하라

-	자바 8 전에는 기존 구현체를 깨뜨리지 않고는 인터페이스에 메서드를 추가할 방법이 없었다.

	-	자바 8 이후엔 기존 인터페이스에 메서드를 추가할 수 있도록 디폴트 메서드를 제공하지만 위함이 완전히 사라진 것은 아님.
	-	디폴트 메서드를 선언하면, 그 인터페이스를 구현한 후 디폴트 메서드를 재정의히지 않은 모든 클래스에서 디폴트 구현이 쓰이게 된다. (모든 구현체들과 매끄럽게 연동되리란 보장은 없다.)

	> 생각할 수 있는 모든 상황에서 불변식을 해치지 않는 디폴트 메서드를 작성하기란 어려운 법이다.

-	디폴트 메서드는 컴파일에 성공하더라도 기존 구현체에 런타임 오류를 일으킬 수 있다.

-	기존 인터페이스에 디폴트 메서드로 새 메서드를 추가하는 일은 꼭 필요한 경우가 아니면 피해야 한다.

#### 아이템 22 : 인터페이스는 타입을 정의하는 용도로만 사용하라

-	인터페이스는 자신을 구현할 클래스의 인스턴스를 참조할 수 있는 타입 역할을 한다.

	-	클래스가 어떤 인터페이스를 구현한다는 것은 자신의 인스턴스로 무엇을 할 수 있는지를 클라이언트에 얘기해주는 것이다.

-	나쁜 인터페이스 예 (상수 인터페이스) : 메서드 없이 상수를 뜻하는 static final 필드로만 가득 찬 인터페이스

	-	클래스가 어떤 상수 인터페이스를 사용하든 사용자에게 아무 의미없다. (오히려 혼란을 줌)
	-	클라이언트 코드가 내부 구현에 해당하는 이 상수들에 종속된다.

		```java
		public interface TestConstants{
		    static final double AVOGADROS_NUMBER = 6.022_140_857e23;


		    static final double BOLT_CONSTANTS = 1.30780;


		    static final double ELE_MASS = 9.109_2;
		}
		```

	-	상수를 공개하려는 목적이라면, 열거 타입으로 나타내거나 인스턴스화할 수 없는 유틸리티 클래스에 담아 공개하자.

		```java
		public class TestConstants2{
		    // 인스턴스화 방지
		    private TestConstants2() {}


		    static final double AVOGADROS_NUMBER = 6.022_140_857e23;


		    static final double BOLT_CONSTANTS = 1.30780;


		    static final double ELE_MASS = 9.109_2;
		}


		// 정적 임포트를 사용해 상수 이름만 사용한다.
		import static effective.chap4.TestConstants2.*;


		public class Test{
		    double atoms(double mols){
		        return AVOGADROS_NUMBER * mols;
		    }
		    ...
		}
		```

#### 아이템 23 : 태그 달린 클래스보다 클래스 계층구조를 활용하라

-	태그 달린 클래스에는 단점이 많다.

	-	열거 타입선언, 태그 필드, switch문 등
	-	여러 구현이 한 클래스에 혼합돼 있어 가독성이 나쁨
	-	또 다른 의미를 추가하려면 코드를 수정해야하는데, 개발자 실수로 인해 문제가 발생할 수 있음

		```java
		class Figure{
		    enum Shape {
		        RECTANGLE, CIRCLE
		    };


		    final Shape = shape;


		    double length;
		    double width;
		    double radius;


		    Figure(double radius){
		        shape = Shape.CIRCLE;
		        this.radius = radius;
		    }


		    Figure(double length, double width){
		        shape = Shape.RECTANGLE;
		        this.length = length;
		        this.width = width;
		    }


		    double area(){
		        swith(shape){
		            case RECTANGLE:
		                return length * width;
		            case CIRCLE:
		                return Math.PI * (radius * radius);
		            default:
		                throw new AssertionError(shape);
		        }
		    }
		}
		```

-	클래스 계층 구조를 통해 태그 달린 클래스의 단점을 모두 없앨 수 있음.

	-	실수로 빼먹은 case 문 때문에 런타임 오류가 발생할 일 없음
	-	다른 프로그래머들이 독립적으로 계층구조를 확장하고 함께 사용할 수 있음
	-	타입 사이의 자연스러운 계층 관계를 반영할 수 있어서 유연성은 물론 컴파일 타입 검사능력을 높여줌

		```java
		abstract class Figure{
		    abstract double area();
		}


		class Circle extends Figure{
		    final double radius;


		    Circle(double radius){
		        this.radius = radius;
		    }


		    @Override double area() {
		        return Math.PI * (radius * radius);
		    }
		}


		class Rectangle extends Figure{
		    final double length;
		    final double width;


		    Rectangle(double length, double width){
		        this.length = length;
		        this.width = width;
		    }


		    @Override double area(){
		        return length * width;
		    }
		}


		```

-	책에 있는 핵심정리

	-	태그 달린 클래스를 써야하는 상황은 거의 없다.
	-	기존 클래스가 태그 필드를 사용하고 있다면 계층 구조로 리팩터링하는 걸 고민해보자.

#### 아이템 24 : 멤버 클래스는 되도록 static 으로 만들라

-	중첩 클래스는 다른 클래스 안에 정의된 클래스를 말한다.

-	멤버 클래스에서 바깥 인스턴스에 접근할 일이 없다면 무조건 static을 붙여서 정적 멤버 클래스로 만들자.

	-	staic을 생략하면 바깥 인스턴스의 숨은 외부 참조를 갖게 된다. : 시간과 공간이 소비됨.
	-	가비지 컬렉션이 바깥 클래스의 인스턴스를 수거하지 못하는 메모리 누수가 생길 수 있음

-	책에 있는 핵심정리

	-	메서드 밖에서 사용해야하거나 안에 정의하기엔 너무 길다면 멤버 클래스로 만든다.
	-	멤버 클래스의 인스턴스 각각이 바깥이 인스턴스를 참조한다면 비정적으로, 그렇지 않으면 정적으로 만들자.
	-	중첩 클래스가 한 메서드 안에서만 쓰이면서 그 인스턴스를 생성하느 지점이 단 한 곳이고 해당 타입으로 쓰기에 적합한 클래스나 인터페이스가 이미 있다면 익명 클래스로 만들고, 그렇지 않으면 지역클래스로 만들자.

#### 아이템 25 : 톱 레벨 클래스는 한 파일에 하나만 담으라

-	소스파일에 톱레벨 클래스를 여러개 선언하더라도 자바 컴파일러는 불편하지 않는다.

	-	하지만 아무런 득이 없고 심각한 위험을 감수해야하는 행위다.
	-	어느 소스를 먼저 컴파일하냐에 따라 달라질 수 있다.

	```java
	// Utensil.java
	class Utensil{
	    static final String NAME = "pan";
	}


	class Dessert{
	    static final String NAME  = "cake";
	}


	```

-	해결책

	-	톱레벨 클래스들을 서로 다른 소스파일로 분리
	-	한 파일에 담고싶다면 정적 멤버 클래스를 사용한다.

	```java
	// Test.java
	public class Test{
	    public static void main(String[] agrs){
	        System.out.println(Utensil.NAME + Dessert.NAME);
	    }


	    private static class Utensil{
	        static final String NAME = "pan";
	    }


	    private static class Dessert{
	        static final String NAME = "cake";
	    }
	}


	```

-	책에 있는 핵심정리

	-	소스 파일 하나에는 반드시 톱레벨 클래스를 하나만 담자.
	-	소스파일을 어떤 순서로 컴파일하든 바이너리 파일이나 프로그램의 동작이 달리지는 일은 일어나지 않을 것임.
