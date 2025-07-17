## Java 메모리 구조
Java 프로그램 실행 시 JVM Run-time Data Area에서 메모리가 관리됨.

### 구성 요소
#### PC (Program Counter)
**현재 실행 중이 스레드의 '바이트코드 줄 번호 표시기'**
멀티스레딩이 CPU 코어를 여러 스레드가 교대로 사용하는 방식으로 동작하며 각 스레드마다 Program Counter가 필요하다.

#### Java Stack
**Java 메서드를 실행하는 스레드의 메모리**
메서드가 호출될 때마다 JVM이 스택 프레임을 만들어 지역 변수 테이블, 피연산자 스택, 동적 링크, 메서드 반환값 등의 정보를 저장.

#### Native Method Stack
**Java Stack과 다르게 C/C++등의 네이티브 메서드를 실행할 때 사용**

#### Heap
**객체 인스턴스를 저장**
모든 스레드가 공유, GC가 관리하는 메모리 영역

#### Method Area
**타입 정보, 상수, 정적 변수 등을 저장**
.class 파일의 바이트 코드도 저장되며 String Constant Pool도 포함됨.

---
## Garbage Collector
PC, Java Stack, Native Method Stack은 스레드와 함께 생성되고 소멸되며 메서드가 끝나도 스택에 할당된 메모리가 회수되기 때문에 GC의 대상이 아님.
--> **Heap, Method Area는 GC의 관리 대상이다.**

### GC의 참조 종류
GC는 기본적으로 GC가 참조하고 있는 모든 객체를 표시하고, 참조되지 않는 객체에서 사용하는 메모리를 식별하고 회수한다 (Mark and Sweep 방식). 그러나 참조된 메모리 중에서도 종류를 나누어 관리할 수 있다.
#### 1. Strong Reference
```java
String s = new String("hello");
```
- 평소에 쓰는 참조
- 참조 중이라면 GC가 수거해 가지 않음
#### 2. Soft Reference
```java
SoftReference<MyObject> ref = new SoftReference<>(new MyObject());
```
- 메모리가 부족하다면 GC가 수거
- 캐시 구현에 적합
#### 3. Weak Reference
```java
WeakReference<MyObject> ref = new WeakReference<>(new MyObject());
```
- GC가 한 번이라도 돌면 즉시 수거됨
#### 4. Phantom Reference
```java
PhantomReference<MyObject> ref = new PhantomReference<>(obj, refQueue);
```
- 객체가 사라지기 직전 감지
- 직접 접근 불가, `ReferenceQueue`와 같이 사용

### Generational GC
**객체의 생존 기간에 따라 다른 영역에 저장하고, 다르게 관리**
#### Young Generation
대부분의 객체가 생성됨. 짧은 생명주기를 가지고 있고 처리가 빠른 Minor GC 이벤트 발생.
#### Old Generation
오래 살아남은 객체 저장, Major GC 또는 Full GC 발생
#### Permanent
클래스 메타데이터 등 저장

GC가 동작하는 도중에는 프로그램이 정지되므로 적절한 GC이벤트를 발생시켜야 한다.

---
## JIT 컴파일러
전통적인 컴파일 언어는 실행 전 이미 코드가 기계어로 변환되어 있기 때문에 JIT 컴파일러가 필요 없다.
그러나 바이트 코드로 컴파일하고 JVM이 바이트 코드를 인터프리팅 하는 Java의 특수성 때문에 JIT 컴파일러가 실행 중 바이트 코드를 기계어로 컴파일하여 성능을 향상시킬 수 있음.

#### 최적화 예시
|최적화 기법|설명|
|---|---|
|**인라인(Inlining)**|자주 호출되는 메서드를 **코드에 직접 삽입**하여 호출 비용 제거|
|**루프 언롤링**|반복문을 풀어서 반복 횟수 줄이기|
|**죽은 코드 제거**|실행되지 않는 분기문, 변수 제거|
|**Escape Analysis**|객체가 스레드 내부에서만 사용되면 **스택에 할당**하여 GC 부담 감소|
|**배치 연산 최적화**|연속된 연산들을 병합하거나 순서를 바꿔 더 빠르게 처리|
