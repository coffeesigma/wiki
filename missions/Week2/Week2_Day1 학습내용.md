---
title: 250714 2주차 월요일 수업

---

# 250714 2주차 월요일 수업 

## 1. String
### char와 String의 메모리 사용 차이
- String Constant Pool에서 String을 어떻게 찾아서 참조하는가?
    → 해시기반으로 관리한다 
> 해시기반으로 관리한다 는 말...해시 기반...? 이게 무슨 소리일까요? 해시 테이블 구조이다..?  
> 갑자기 들이닥치는 JVM 구조ㅋㅋㅋㅋㅋㅋ 수업 
- char[] ch = {'a', 'b', 'c'};
String str = "abc"; 
두 경우, 각각 사용하는 메모리의 크기는 어떻게 되나요?
> char[] 배열의 메모리 사용량 = char 객체(헤더 + 배열 길이 + char 데이터 + 패딩 ) 
> String 객체의 메모리 사용량 = String 객체(헤더 + hash필드 / value) +  char[] 배열
> 오 메모리 사용량에서 char[] 이 유리하네? 
> 앞으로 char[] 만 쓰시면 됩니다. 
> 


### String 객체가 immutable인 이유와 장단점
 - 보안
     - `String`에 파일 경로, 비밀번호 같은 민감한 정보를 저장해 사용한다고 해보자, `String`이 non-immutable이라면 무결성 확인 후 해당 `String`을 수정하는 공격이 가능할 수 있다. `String`이 immutable이기 때문에 보안상 이점이 존재한다.
 - 캐싱
     - immutable 객체는 해시값도 변하지 않기 때문에 해시값을 한 번 구해두고 재사용할 수 있다.
- 메모리 절약
    - 같은 문자열 리터럴을 참조하는 경우, 같은 SCP의 객체를 참조하기 때문에 메모리가 절약된다. 
 - String Constant pool은 GC에 의해 정리되나요??
    - 기본은 정리되지 않음
    - 1. String a = "abc" ← GC에 의해 정리되지 않음
    - 2.  String b = new String("abc") ← GC에 의해 정리 됨 (SCP에 등록되지 않기 때문)
    +) b.intern() 메서드를 사용하면 SCP에 등록이 됨
    
    > 이게 왜 이런 답변이 달렸을까 생각해보니 JAVA 8 이전 이야기인 것 같습니다. (그게 아니라면 그냥 잘못 알고 계신 것. Java 8은 2014년 3월 18일에 최초로 출시되었다고 하네요ㅋㅋㅋ)
    > 그 이후로는 JVM에서 heap 에 위치하기 때문에 당연히 GC 가 돕니다..
    > new String("alice").intern()에 대해서 설명해주실 분 구합니다. 


### String concatenation(문자열 연결)의 다양한 방법들

```java
// 1. + 연산자
String s1 = "Hello" + " World";
// '+ 연산자' vs concat
// + 연산자는 Java 내부에서 자동으로 StringBuilder로 연결하고 최종적으로 toString() 해주기 때문에 메모리 관리 측면에서 concat 보다 좋다.

// 2. concat
String s2 = "Hello".concat(" World");

// 3. StringBuilder
StringBuilder sb = new StringBuilder();
sb.append("Hello").append(" World");
String s3 = sb.toString();

// 4. join
String s4 = String.join(", ", "A", "B", "C");

// 5. String.format
String s5 = String.format("Score: %d", 100);

// 6. Stream.joining
List<String> list = List.of("A", "B", "C");
String s6 = list.stream().collect(Collectors.joining("-"));

```

### StringBuilder와 StringBuffer의 차이

- StringBuffer 의 경우 내부에서 어떻게 구현이 되고 있는가?
    - 메소드에 synchronized 키워드가 붙어 있음
        - synchronized vs lock 어떤 차이가 있는지? : 자동 unlock / 수동 unlock

## 2. Map, Set, List, Array
### Generic 이란? 
### "Map, Set, List, Array는 각각 어떤 특징을 가지고 있나요? (예: 중복 데이터 허용 여부, 순서 보장, 키-값 쌍 저장 등의 관점)

- TreeSet은 순서가 보장 되는가?
    - TreeSet 은 이진검색트리를 기반으로 하기 때문에 데이터 정렬 상태를 유지한다.
- HashMap에서 해시 충돌이 발생하는 경우 어떤 방식으로 처리하나요?
- HashMap은 충돌이 너무 많이 나게 되면 TreeMap으로 변경된다고 합니다.. 
  -> 그 자세하게 찾아보니, 충돌되는 노드의 개수가 임계값을 넘어서면 chaining 되는 구조가 linked-list에서 Red-Balck Tree로 바뀌는 것 같아요. 다음 코드는 HashMap 안에서 구현된 코드입니다. 
```java
/**
     * Replaces all linked nodes in bin at index for given hash unless
     * table is too small, in which case resizes instead.
     */
    final void treeifyBin(Node<K,V>[] tab, int hash) {
        int n, index; Node<K,V> e;
        if (tab == null || (n = tab.length) < MIN_TREEIFY_CAPACITY)
            resize();
        else if ((e = tab[index = (n - 1) & hash]) != null) {
            TreeNode<K,V> hd = null, tl = null;
            do {
                TreeNode<K,V> p = replacementTreeNode(e, null);
                if (tl == null)
                    hd = p;
                else {
                    p.prev = tl;
                    tl.next = p;
                }
                tl = p;
            } while ((e = e.next) != null);
            if ((tab[index] = hd) != null)
                hd.treeify(tab);
        }
    }
```

- HashMap VS HashSet 어떤게 검색 성능이 더 좋을까요?
    - 답변: 두 자료구조가 사용 목적이 다르긴 합니다. <br> HashMap의 경우 key를 기반으로 value를 찾기 위해 사용하고, value를 조회하는 과정에서 linked-list 혹은 tree를 탐색해야 할 수 있습니다. <br> HashSet의 경우, HashMap과 다르게, 값이 존재하는지만 확인하면 됩니다.

### 대량의 데이터를 다룰 때 Map, Set, List, Array 중에서 검색 속도가 가장 빠른 것은 무엇이고 이유를 설명

## 3. Exception 

### Exception 계층 구조와 분류
	- Checked Exception과 Unchecked Exception의 차이점
	- Error와 Exception의 차이점
    

### 언제 사용자 정의 예외(Custom Exception)를 만들어야 하는지?(적절한 예시 설계)
### 예외처리 로직- 예외를 catch해서 처리하는 것 vs throws를 사용해서 상위 메소드로 전파하는 것 


