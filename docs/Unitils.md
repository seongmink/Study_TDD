# Unitils

오픈소스 중 하나인 Unitils(유닛틸즈)는 단위 테스트를 좀 더 쉽게 만들고 더 유연하게 유지할 수 있게 도와주는 일종의 단위 테스트 지원 라이브러리다. DbUnit과 마찬가지로, 독립적으로 사용된다기보다는 주로 다른 테스트 프레임워크와 함께 사용된다.

## Unitils를 사용하기 위한 환경 준비

실제 예제를 살펴보기 전에, Unitils를 사용할 수 있는 환경을 먼저 구축해보자. Unitils 를 사용하기 위해 Unitils 라이브러리를 사이트(https://mvnrepository.com/artifact/org.unitils/unitils-core)에서 내려받는다. 다음 실습을 따라 하려면, unitils-core 폴더와 unitils-core/lib 폴더 안의 라이브러리를 클래스패스에 포함시켜 줘야 한다.

그리고 소스에서는 JUnit 4와 마찬가지로 static import를 주로 사용한다. 이를테면 다음과 같은 식으로 말이다.

```java
import static org.unitils.reflectionassert.ReflectionAssert.*;
```

JUnit 4의 static 메소드들을 자동완성 기능으로 사용하기 위해 favorite으로 등록하는 방법을 2장에 정리해놓았다. Unitils의 static 메소드들도 같은식으로 등록해놓으면 편리하게 사용할 수 있다.

## Unitils의 단위 테스트 지원 기능들

### 객체 동치성 비교

TDD를 위해 테스트 케이스를 작성하다 보면, 객체끼리 동치성 비교를 해야 하는 경 우가 흔히 발생한다. 그런데, 이때 assertEquals만으로는 원하는 답이 나오지 않는 경우가 많다. 다음은 Book이라는 클래스와 그 클래스를 기반으로 만들어진 두 객체에 대한 동치성 비교 문장이다.

##### Book 클래스

```java
public class Book {
    private String name;
    private String author;
    private int price;
    
    public Book(String name, String author, int price) {
        this.name = name;
        this.author = author;
        this.price = price;
    }
    ...
}
```

##### 두 Book 객체를 비교하는 테스트 케이스

```java
@Test
public void testBook() throws Exception {
	Book aBook = new Book("사람은 무엇으로 사는가?","톨스토이", 9000);
    Book otherBook = new Book("사람은 무엇으로 사는가?","톨스토이", 9000);
    
	assertEquals(aBook, otherBook);
}
```

##### 실행 결과

```java
Java.lang.AssertionError: expected:<main.Book@16f0472> but was:<main.Book@18d107f>
	at org.junit.Assert.fail(Assert.Java:91)
```

이를테면 이런 종류의 객체 비교는 단순히 참조 비교를 하기 때문에 두 aBook과 otherBook은 의도된 객체의 상태라는 측면에서는 동일하지만, JUnit의 assertEquals 결과는 두 객체가 서로 다르다고 판정한다.

```java
@Test
public void testBook() throws Exception {
    Book aBook = new Book("사람은 무엇으로 사는가?","톨스토이", 9000);
    Book otherBook = new Book("사람은 무엇으로 사는가?","톨스토이", 9000);
    
    assertEquals(aBook.getName(), otherBook.getName());
    assertEquals(aBook.getAuthor(), otherBook.getAuthor());
    assertEquals(aBook.getPrice(), otherBook.getPrice());
}
```

그래서 위와 같은 식으로 각 필드를 비교해야 한다. 하지만 필드가 많아지면 여간 불편한 게 아니다. Unitils는 이런 불편함을 해소하기 위해, 객체의 필드에 저장되어 있는 값을 알아서 비교해주는 리플렉션 단정문(reflection assertion)이라는 기능을 제공한다.

### 리플렉션 단정문(Reflection Assertion)

```java
assertReflectionEquals(예상 객체, 실제 객체);
assertReflectionEquals([메시지], 예상 객체, 실제 객체);
```

형식은 일반적으로 사용되는 assertEquals와 동일하며, 마찬가지로 첫 번째 인자로 '메시지'를 적을 수도 있다. 실제 적용 시에는 가급적이면 의도를 확실히 하기 위해 assert의 의도를 설명해주는 '메시지'를 적는 걸 권장한다.

```java
import static org.unitils.reflectionassert.ReflectionAssert.*;

public class BookTest {
    @Test
    public void testBook() throws Exception {
        Book aBook = new Book("사람은 무엇으로 사는가?","톨스토이", 9000);
        Book otherBook = new Book("사람은 무엇으로 사는가?","톨스토이", 9000);

        assertReflectionEquals("Book 객체 필드 비교", aBook, otherBook);
    }
    ...
```

리플렉션 단정문을 사용하면 테스트가 깔끔하게 성공한다.

Unitils의 assertReflectionEquals를 사용하면 이런 식으로 객체의 동치성을 증명해준다. 이걸 Unitils에서는 리플렉션을 이용한 단정(reflection assertion)이라고 부른다. Unitils의 이런 리플렉션 단정문에 몇 가지 옵션을 지정할 수 있는데, 대부분은 소스코드가 리팩토링이 일어나면서 발생하는 테스트 케이스의 깨짐(fragility of test)을 보완하기 위해 사용된다. 이 방식을 너그러운 단정문(lenient assertions) 적용이라고 부른다.