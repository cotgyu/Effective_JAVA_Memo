이펙티브 자바
-------------

---

### 12장 직렬화

-	객체 직렬화란 자바가 객체를 바이트 스트림으로 인코딩하고(직렬화) 그 바이트 스트릠으로부터 다시 객체를 재구성하는(역직렬화) 메커니즘이다.
	-	직렬화된 객체는 다른 VM에 전송하거나 디스크에 저장한 후 나중에 역직렬화할 수 있다.

#### 아이템 85 자바 직렬화의 대안을 찾으라

-	직렬화의 근본적인 문제는 공격 범위가 너무 넓고 지속적으로 더 넓어져 방어하기 어렵다는 점이다.

	-	ObjectInputStream의 readObject 메서드를 호출하면서 객체 그래프가 역직렬화되기 때문
	-	readObject 메서드는 Serializable 인터페이스를 구현했다면 클래스패스 안의 거의 모든 타입의 객체를 만들어낼 수 있는 생성자
	-	바이트 스트림을 역직렬화하는 과정에서 이 메서드는 그 타입들 안의 모든 코드를 모두 수행할 수 있다. (그 타입들의 모드 전체가 공격범위에 들어감)

-	역직렬화에 시간이 오래 걸리는 짧은 스트림을 역직렬화하는 것만으로도 서비스 거부 공격에 쉽게 노출될 수 있음 (역직렬화 폭탄)

> 직렬화 위험을 회피하는 가장 좋은 방법은 아무것도 역직렬화하지 않는 것이다.

-	객체와 바이트 시퀀스를 변환해주는 다른 메커니즘이 많이 있다.

	-	이 방식들은 자바 직렬화의 여러 위험을 회피하면서 다양한 플랫폼 지원, 우수한 성능, 풍부한 지원 도구, 활발한 커뮤니티와 전문가 집단 등 수많은 이점까지 제공한다.
	-	이러한 메커니즘도 직렬화 시스템이라 불리기도 하지만, 책에선 구분하고자 크로스-플랫폼 구조화된 데이터 표현이라 함
	-	이런 표현들의 공통점은 자바 직렬화보다 훨씬 간단함.
		-	임의 객체 그래프를 자동으로 직렬화/역직렬화하지 않는다.
		-	대신 속성 값-쌍의 집합으로 구성된 간단하고 구조화된 데이터 객체를 사용한다.
	-	크로스-플랫폼 구조화된 데이터 포현의 선두주자는 JSON과 프로토콜 버퍼임

-	레거시 시스템 떄문에 자바 직렬화를 완전히 배제할 수 없을 때의 차선책은 신뢰할 수 없는 데이터는 절대 역직렬화하지 않는 것이다.

-	직렬화를 피할 수 없고 역직렬화된 데이터가 안전한지 완전히 확신할 수 없다면 객체 역직렬화 필러링을 사용하자 (자바 9)

	-	데이터 스트림이 역직렬화되기 전에 필터를 설치하는 기능
	-	클래스 단위로, 특정 클래스를 받아들이거나 거부할 수 있음

-	책에 있는 핵심 정리

	-	직렬화는 위험하니 피해야 한다.
	-	시스템을 밑바닥부터 설계한다면 JSON이나 프로톸콜 버퍼 같은 대안을 사용하자.
	-	신뢰할 수 없는 데이터는 역직렬화하지 말자.
	-	꼭 해야한다면 객체 역직렬화 필터링을 사용하되, 이마저도 모든 공격을 막아줄 수는 없음을 기억하자.
	-	클래스가 직렬화를 지원하도록 만들지 말고, 꼭 그렇게 만들어야 한다면 정말 신경써서 작성해야 한다.

#### 아이템 86 Serializable을 구현할지는 신중히 결정하라

-	클래스의 인스턴스를 직렬화할 수 있게 하려면 클래스 선언에 implements Serializable 만 덧붙이면 된다.

	-	쉽게 적용할 수 있지만 신경쓸게 많다.

-	Serializable을 구현하면 릴리스한 뒤에는 수정하기 어렵다.

	-	클래스가 Serializable을 구현하면 직렬화된 바이트 스트림 인코딩(직렬화 형태)도 하나의 공개 API가 된다.
	-	그래서 이 클래스가 널리 퍼진다면 그 직렬화 형태도 영원히 지원해야 한다.
	-	커스텀 직렬화 형태를 설계하지 않고 자바의 기본 방식을 사용하면 직렬화 형태는 최소 적용 당시 클래스의 내부 구현 방식에 영원히 묶여버린다.
		-	기본 직렬화 형태에서는 클래스의 private 과 package-private 인스턴스 필드마저 API로 공개된다

-	직렬화 가능 클래스를 만들고자 한다면, 길게 보고 감당할 수 있을 만큼 고품질의 직렬화 형태도 주의해서 함께 설계해야 한다.

-	직렬화가 클래스 개선을 방해하는 예

	-	모든 직렬화 클래스는 고유 식별 번호를 부여받는다. serialVersionUID

		-	명시하지 않으면 시스템이 런타임에 암호해시 함수를 적용해 자동으로 넣음
		-	이 값은 클래스이름, 구현한 인터페이스들 등이 고려되는데 나중에 편의 메서드를 추가하는 식으로 이들 중 하나라도 수정되면 직렬 버전 UID도 변경함
		-	쉽게 호환성이 깨져 런타임에 InvaildClassException이 발생할 수 있음

	-	버그와 보안 구멍이 생길 위험이 높아짐

		-	직렬화는 언어의 기본 메커니즘을 우회하는 객체 생성 기법임
		-	기본 역직렬화를 사용하면 불변식 깨짐과 허가되지 않은 접근에 쉽게 노출됨

	-	해당 클래스의 신버전을 릴리스할 때 테스트할 것이 늘어남

		-	직렬화 가능 클래스가 수정되면 신버전 인스턴스를 직렬화한 후 구버전으로 역직렬화할 수 있는지 그리고, 그 반대도 가능한지를 검사해야 한다.
		-	양방향 직렬화/역직렬화가 모두 성공하고 원래의 객체를 충실히 복제해내는지를 반드시 확인해야함

-	상속용으로 설계된 클래스는 대부분 Serializable을 구현하면 안 되며, 인터페이스도 ㅂ대부분 Serializable을 확장해서는 안된다.

-	클래스의 인스턴스 필드가 직렬화와 확장이 모두 가능하다면 주의해야할 점이 몇가지 있음

	-	인스턴스 필드 값 중 불변식을 보장해야 할 게 있다면 반드시 하위 클래스에서 finalize 메서드를 재정의하지 못하게 해야 한다.

		-	finalize 메서드를 자신이 재정의하면서 final 로 선언하면 됨
		-	이렇게 해두지 않으면 finalizer 공격을 당할 수 있음

	-	인스턴스 필드 중 기본 값으로 초기화되면 위배되는 불변식이 있다면 클래스에 다음의 readObjectNodata 메서드를 반드시 추가해야한다.

		```java
		private void readObjectNoData() throws InvalidObjectException {
		    throw new InvaildObjectException("스트림 데이터가 필요합니다.");
		}
		```

-	내부 클래스는 직렬화를 구현하지 말아야 한다.

	-	내부 클래스에 대한 기본 직렬화 형태는 분명하지가 않다.

-	책에 있는 핵심 정리

	-	Serializable은 구현한다고 선언하기는 아주 쉽지만, 그것은 눈속임일 뿐이다.
	-	한 클래스의 여러 버전이 상호작용할 일이 없고 서버가 신뢰할 수 없는 데이터에 노출될 가능성이 없는 등, 보호된 환경에서만 쓰일 클래스가 아니라면 Serializable 구현은 아주 신중하게 이뤄져야 한다.
	-	상속할 수 있는 클래스라면 주의사항이 더욱 많아진다.

#### 87 아이템 커스텀 직렬화 형태를 고려해보라

-	먼저 고민해보고 괜찮다고 판단될 때만 기본 직렬화 형태를 사용하라.

	-	객체의 물리적 표현과 논리적 내용이 같다면 기본 직렬화 형태라도 무방하다.

	-	기본 직렬화 형태가 적합하다고 결졍했더라도 불변식 보장과 보안을 위해 readObject 메서드를 제공해야 할 때가 많다.

-	객체의 물리젹 표현과 논리적 표현의 차이가 클 때 기본 직렬화 형태를 사용하면 크게 네 가지 면에서 문제가 생긴다.

	-	공개 API가 현재의 내부 표현 방식에 영구히 묶인다
	-	너무 많은 공간을 차지할 수 있다.
	-	시간이 너무 많이 걸릴 수 있다.
	-	스택 오버플로를 일으킬 수 있다.

-	어떤 직렬화 형태를 택하든 직렬화 가능 클래스 모두에 직렬 버전 UID를 명시적으로 부여하자.

	-	직렬 버전 UID가 일으키는 잠재적인 호환성 문제가 사라진다.
	-	성능도 조금 빨라짐 (없으면 런타임 시 생성해야하기 때문)

-	구버전으로 직렬화된 인스턴스들과의 호환성을 끊으려는 경우를 제외하고 직렬 버전 UID를 절대 수정하지 말자.

-	책에 있는 핵심 정리

	-	클래스를 직렬화하기로 했다면 어떤 직렬화 형태를 사용할지 심사숙고하기 바란다.
	-	자바의 기본 직렬화 형태는 객체를 직렬화한 결과가 해당 객체의 논리적 표현에 부합할 때만 사용하고, 그렇지 않으면 객체를 적절히 설명하는 커스텀 직렬화 형태를 고안하라.

		-	직렬화 형태도 공개 메서드를 설계할 때 준하는 시간을 들여 설계해야 한다.

		-	한 번 공개된 메서드는 향후 릴리스에서 제거할 수 없듯이, 직렬화 형태에 포함된 필드도 마음대로 제거할 수 없다.

		-	잘못된 직렬화 형태를 선택하면 해당 클래스의 복잡성과 성능에 영구히 부정적인 영향을 남긴다.

#### 아이템 88 readObject 메서드는 방어적으로 작성하라

-	보통의 생성자처럼 readObject 메서드에서도 인수가 유효한지 검사해야 하고 필요하다면 매개변수를 방어적으로 복사해야 한다.

	-	이 작업을 제대로 수행하지 못하면 공격자느 손쉽게 해당 클래스의 불변식을 깨뜨릴 수 있다.

-	객체를 역직렬화할 때는 클라이언트가 소유해서는 안 되는 객체 참조를 갖는 필드를 모두 반드시 방어적으로 복사해야 한다.

	-	readObject 에서는 불변 클래스 안의 모든 private 가변 요소를 방어적으로 복사해야 한다.

-	책에 있는 핵심 정리

	-	readObject 메서드를 작성할 때는 언제나 public 생성자를 작성하는 자세로 임해야 한다.

		-	readObject 는 어떤 바이트 스트림이 넘어오더라도 유효한 인스턴스를 만들어내야 한다.
		-	바이트 스트림이 진짜 직렬화된 인스턴스라고 가정해서는 안된다.

	-	readObject 메서드를 작성하는 지침

		-	private이어야 하는 객체 참조 필드는 각 필드가 가리키는 객체를 방어적으로 복사하라.
			-	불변 클래스 내의 가변 요소가 여기 속한다.
		-	모든 불변식을 검사하여 어긋나는 게 발견되면 InvaildObjectException 을 던진다.
			-	방어적 복사 다음에는 반드시 불변식 검사가 뒤따라야 한다.
		-	역직렬화 후 객체 그래프 전체의 유효성을 검사해야 한다면 ObjectInputValidation 인터페이스를 사용하라
		-	직접적이든, 간접적이든, 재정의할 수 있는 메서드는 호출하지 말자.

#### 아이템 89 인스턴스 수를 통제해야 한다면 readResolve 보다는 열거 타입을 사용하라

-	readResolve 기능을 이용하려면 readObject가 만들어낸 인스턴스를 다른 것으로 대체할 수 있다.

	-	역직렬화한 객체의 클래스가 readResolve 메서드를 적절히 정의해뒀다면, 역직렬화 후 새로 생성된 객체를 인수로 이 메서드가 호출되고, 이 메서드가 반환한 객체 참조가 새로 생성된 객체를 대신해 반환된다.
	-	대부분의 경우 이때 새로 생성된 객체의 참조는 유지하지 않으므로 바로 가비지 컬렉션 대상이 된다.

-	readResolve 를 인스턴스 통제 목적으로 사용한다면 객체 참조 타입 인스턴스 필드는 모두 transient로 선언해야 한다.

	-	그렇지 않으면 readResolve 메서드가 수행되기 전에 역직렬화된 객체의 참조를 공격할 여지가 남는다.

-	책에 있는 핵심 정리

	-	불변식을 지키기 위해 인스턴스를 통제하야 한다면 가능한 한 열거타입을 사용하자.
	-	여의치 않은 상황에서 직렬화와 인스턴스 통제가 모두 필요하다면 readResolve 메서드를 작성해 넣어야하고, 그 클래스에서 모든 참조 타입 인스턴스 필드를 transient로 선언해야 한다.

#### 아이템 90 직렬화된 인스턴스 대신 직렬화 프록시 사용을 검토하라

-	Serializable을 구현하기로 결정한 순간 언어의 정상 메커니즘인 생성자 이외의 방법으로 인스턴스를 생성할 수 있게 된다.

	-	버그와 보안 문제가 일어날 가능성이 커진다.
	-	직렬화 프록시를 통해 위험을 줄여줄 수 있다.

-	직렬화 프록시 패턴

	-	바깥 클래스의 논리적 상태를 정밀하게 표현하는 중첩 클래스를 설계해 private static으로 선언
	-	중첩 클래스의 생성자는 단 하나여야 하며, 바깥 클래스를 매개변수로 받아야 함.
	-	이 생성자는 단순히 인수로 넘어온 인스턴스의 데이터를 복사함 (일관성 검사, 방어적 복사도 필요 없음)
	-	설계상, 직렬화 프록시의 기본 직렬화 형태는 바깥 클래스의 직렬화 형태로 쓰이게 이상적임

-	한계

	-	클라이언트가 멋대로 확장할 수 있는 클래스에는 적용할 수 없다.
	-	객체 그래프에 순환이 있는 클래스에 적용할 수 없다.
		-	이런 객체의 메서드를 직렬화 프록시의 readResolve 안에서 호출하려면 ClassCastException이 발생할 것임

-	책에 있는 핵심 정리

	-	제3자가 확장할 수 없는 클래스라면 가능한 한 직렬화 프록시 패턴을 사용하자.
	-	이 패턴은 중요한 불변식을 안정적으로 직렬화해주는 가장 쉬운 방법일 것이다.