#### Kotlin in Action 09. Generics
* where
* reified
* in, out
* star projection
-> 용도가 리스트같은 경우 런타임 때 타입이 erase되기 때문에 자료형을 체크하고 싶을 때 별을 사용.

inline 과 reified 를 함께 써주면 자료형 보존이 된다.
컴파일 타임에 실제화 시키는 원리.

reified는 inline fuction에서만 사용할 수 있다.

inline reified는 자바에서 call할 수 없다.
koin의 핵심은 inline reified이기 때문에 자바와 섞여쓰면 사용 불가능해진다.

Variance(공변/반공변)
String은 Any의 subtype이다.
A는 A?의 subtype이다.

invariant : 자바의 경우 모든 제네릭 클래스는 invariant.

covariant : 상속관계 속이면 covariant이다.
List<B>는 List<A>의 subtype 이다. (List말고도..기타 등...)

<out>키워드를 써줘야 covariant.
out을 써주지 않으면 invariant. 내부적으로 사용하면 안되고 리턴타입으로만 T를 사용해야한다.
(T로 뱉을 수는 있지만 comsume소비할 수는 없다.)
1. 내부에서만 사용되었다.
2. 서브타입이 보존되었다.

내부 클래스 까보면
List -> covariant 
MutableList -> invariant

생성자에서 private or vararg면 T쓸 수 있다.
* Contravariant
B는 A의 서브타입인데 Consumer<B>는 Consumer<A>의 서브타입일 경우.
in을 쓸 경우
1. T는 인포지션에서만 사용하였다.
2. 서브타이핑이 뒤바뀌었다.


(A)->B
: 이뜻은 즉 in A, out B

star : 특정타입을 받는데 뭔지 모르겠다. Any는 다 받겠다.
set은 안되지만 get은 Any?로 받을 수 있다.
Mutable<*> 은 Mutable<out Any?>로 protected 되었다.

