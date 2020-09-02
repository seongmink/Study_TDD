# JUnit과 Hamcrest

## JUnit

 JUnit은 현재 전 세계적으로 가장 널리 사용되는 Java 단위 테스트 프레임워크다. TDD의 근간이 되는 프레임워크이며, 소위 xUnit 시리즈라고 불리는 다양한 단위 테스트 프레임워크들의 기원이 되는 프레임워크다. 전체 소스가 공개되어 있는 오픈소스 프로젝트방식으로 개발되며 지금도 꾸준히 버전업을 하고 있다. JUnit은 제공하는 기능도 기능이지만, 만들어진 방식이나 구조 자체에서도 배울 점이 매우 많은 소프트웨어다. JUnit은 단위 테스트를 수행하는 데 있어 기본적으 로 다음과 같은 기능을 제공한다. 

##### 테스트 결과가 예상과 같은지를 판별해주는 단정문(assertions) 

##### 여러 테스트에서 공용으로 사용할 수 있는 테스트 픽스처(test fixture)

##### 테스트 작업을 수행할 수 있게 해주는 테스트 러너(test runner)

JUnit이 널리 퍼진 이유 중 하나는 위에서 보는 것과 같이 목표가 단순하고, 하고자 하는 일이 명료했기 때문이다. 거기에 쉬운 사용 방법도 크게 한몫 거들었다. JUnit을 비롯하여 단위 테스트 케이스를 만드는 데 있어 꼭 알아둘 개념이 하나 있는 데, **테스트 픽스처**(테스트 기반환경 또는 테스트를 위한 구조물)라는 개념이다.

#### 텍스트 픽스처

일반적으로 소프트웨어 테스트에서 이야기하는 '테스트 픽스처'란 테스트를 반복적으로 수행할 수 있게 도와주고 매번 동일한 결과를 얻을 수 있게 도와주는 **'기반이 되는 상태나 환경'**을 의미한다. **일관된 테스트 실행환경**이라고도 하며, 때로는 테스트 컨텍스트(test context)라 부르기도 한다. 테스트 케이스에서 사용할 객체의 인스턴스를 만든다든가, 데이터베이스와 연동할 수 있는 참조를 선언한다든가, 파일이나 네트워크 등의 자원을 만들어 지정한다든가 하는 등의 작업 혹은 그 작업의 결과로 만들어진 대상을 통칭한다.

#### 테스트 케이스와 테스트 메소드

테스트 케이스(test case)와 테스트 메소드(test method)라는 용어는 흔히 혼용되어 사용된다. 정확히는 단위 테스트 케이스와 단위 테스트 메소드가 제대로 된 명칭이다. 테스트 케이스는 테스트 작업에 대한 시나리오적인 의미가 더 강하고 테스트 메소드는 JUnit의 메소드를 지칭한다. 그러나 테스트 케이스가 곧 테스트 메소드인 경우가 많기 때문에 두 단어는 종종 혼용되어 사용된다

- ### JUnit 3

  지금은 JUnit 4가 주로 사용되고 있지만, 아직 JUnit 3을 사용하고 있는 프로젝 트들도 상당수 남아 있다. JUnit은 junit.org 사이트에서 파일을 내려받아서 클래스 경 로 내에 junit.jar 파일을 포함시켜 놓으면 바로 사용할 수 있다.

  #### JUnit 3 규칙

  ```
  1. TestCase를 상속받는다.
     예) AccountTest extends TestCase
  2. 테스트 메소드의 이름은 반드시 test로 시작해야 한다.
     에) testGetAddress(), testCalculateValue() 등
  ```

  ##### 예

  ```java
  public class CalculatorTest extends TestCase {
  	
      public void testCalculateSum() {
          ...
      }
  }
  ```

  #### 구성요소

  - ##### 테스트 픽스처 메소드(Test Fixture Method)

    ```
    - setUP()
    - tearDown()
    ```

    JUnit 3에서는 setUp과 tearDown이라는 두 개의 테스트 픽스처 메소드를 지원하고 있다. setUp은 각각의 테스트 메소드가 실행되기 전에 공통으로 호출되는 메소드다. 보통 테스트 환경 준비에 해당하는, 자원 할당, 객체 생성, DB 연결이나 네트워크 연 결 등의 작업이 이뤄진다. tearDown은 테스트 메소드가 수행된 다음 수행되는 메소드다. setUp과는 반대로 자원 해체, 연결 해제, 객체 초기화 등의 뒷정리 작업을 하게 된다.  **오버라이드된 형태로 실행되기 때문에 setUp과 tearDown을 사용할 때는 대소문자에 유의해야 한다.**

    실행 순서 : setUp() -> 메소드 -> tearDown()

  - ##### 단정문(Assertions)

    ```
    대표적인 단정문    			*[message]는 선택사항
     - assertEquals([message], expected, actual)
     - assertSame([message], expected, actual / assertNotSame([message], expected, actual)
     - assertTrue([message], expected) / assertFalse([message], expected)
     - assertNull([messaeg], expected) / assertNotNull([message], expected)
     - fail([message])
    ```

    - ##### assertEquals([messsage], expected, actual)

      두 값이 같은지 비교하는 단정문으로, 예상에 해당하는 expected와 실제 테스트 수행 결과에 해당하는 actual이 서로 일치하는지 비교 판단한다. expect와 actual은 Java 언어의 기본 타입(primitive type) 전체에 대해 중첩구현(overloading)되어 있기 때문에 다양한 값을 서로 비교할 수 있다. 만일 비교 값이 기본 타입이 아닐 경우에는 equals 메소드 비교를 하게 되어 있다. 따라서 equals를 구현해놓은 클래스라면, 해당 equals 메소드를 호출해서 비교에 사용한다.

    - ##### assertSame([message], expected, actual)

      ##### assertNotSame([message], expected, actual)

      assertSame은 두 객체가 정말 동일한 객체인지 주소값으로 비교하는 단정문이다. 따라서 객체를 비교할 때 equals 메소드를 사용하지 않고 바로 등가비교(==)를 한다. assertNotSame도 마찬가지로 두 객체를 주소로 비교한다. 다만 이 경우 주소 값이 다르면 무조건 true가 된다.

      assertSame은 객체의 주소를 비교하는 것이다 보니, 주로 동일 객체임을 증명하는 데 쓰인다. 이를테면 캐시(cache) 기능을 만들었는데, 해당 캐시가 제대로 동작하는지 판단할 필요가 있다고 가정해보자. 이럴 때에 assertSame을 사용해서 특정 객체가 캐시에서 가져온 객체와 동일한지 여부를 판단할 수 있다.

      ```java
      cache.add(someObject, KEY);
      assertSame("캐시처리 실패", someObject, cache.lookup(KEY));
      ```

      비슷한 방식으로, **싱글톤**(**Singleton**)**으로 만들어진 객체를 비교할 때 쓰이기도 한다.**

    - ##### assertTrue([message], expected)

      ##### assertFalse([message], expected)

      예상값의 참/거짓을 판별하는 단정문이다. boolean 타입 메소드를 이용할 경우나, 부등호 비교, 범위 비교 등을 판단할 때 사용한다. 단정문이 실패 할 경우 값을 바로 알 수 없어 불편한 점이 있다. 등호 비교는 가급적 assertEquals(0, account.getBalance()) 형식을 사용하도록 권장한다.

    - ##### assertNull[message], expected)

      ##### assertNotNull([message], expected)

      대상 값의 null 여부를 판단하는 단정문이다. assertNull이면 대상 값이 null인지 확인 하며, assertNotNull 이면 null이 아닌지 판별하는 문장이다.

      assert 시리즈는 예상값이 assert 문장 다음에 이어지는 글자와 상태가 일치한다는 걸 확인하는 문장이다. assert를 ‘~이어야 함’으로 해석하면 헷갈리지 않을 수 있다.

    - #####  fail([message])

      이 메소드가 호출되면 해당 테스트 케이스는 그 즉시 실패한다. 현재 작성 중인 메소드의 경우 단정문을 쓰지 않았으면 예외가 발생하지 않는 이상 무조건 성공하는 테스트 케이스가 된다. 만일 아직 테스트 케이스를 작성 중인데 완료하지 못한 채 구현을 중단해야 하는 경우라면 끝 부분에 **fail**()**을 추가해놓으면 도움이 된다.** 나중에 다시 돌아와야 할 때 어디부터 해야 할지를 알려주는 잣대가 되기 때문이다.

      예외처리를 테스트하기 위한 트릭으로 fail()을 사용하기도 한다.

      ```java
      public void testWithdraw_현재잔고이하_인출요구시() throws Exception {
          Account account = new Account(10000);
      	account.withdraw(20000); // OverWithdrawRequestException이 발생해야 함
      }
      ```

      위와 같은 경우처럼 특정 조건에서 예외가 발생해야 정상인 경우를 테스트 케이스로 작성하려면 다음과 같이 작성하면 된다.

      ```java
      public void testWithdraw_현재잔고이하_인출요구시() throws Exception {
          Account account = new Account(10000);
          try {
              account.withdraw(20000); // (1)
              fail(); // (2)
          } catch (OverWithdrawRequestException e){
              assertTrue(true); // (3)
          }
      }
      ```

      (1) : OverWithdrawRequestException이 발생해야 함

      (2) : 만일 위에서 예외가 발생하지 않아서 이 부분까지 실행되면 실패함

      (3) : 빈 줄로 남겨둬도 무방하나 명시적으로 표시해놓음
    
  - ##### 테스트 러너(Test runner)

    ```java
    - junit.swingui.TestRunner.run(테스트클래스.class);
    - junit.textui.TestRunner.run(테스트클래스.class);
    - junit.awtui.TestRunner.run(테스트클래스.class);
    ```

    대부분의 Java 통합개발환경(IDE)은 JUnit 프레임워크를 내장 지원하고 있다. 그래서 종종 JUnit이 독립적인 프레임워크라기보다는 하나의 기능처럼 느껴질 수도 있다. 하지만 JUnit 프레임워크는 엄연히 독립적인 소프트웨어이고, 애초부터 그렇게 만들어졌다. 때문에 명령행 프롬프트에서 실행하거나 셸 스크립트 등을 이용해 실행할 수도 있다. 이를 위해 JUnit은 테스트 러너라는 테스트 실행 클래스를 제공한다. 기본적으로 세 가지 실행 방법을 제공하는데, Swing UI, 텍스트 그리고 Java AWT UI이다. 다음은 DisplayTest라는 테스트 클래스를 세 가지 방식으로 모두 실행하는 예제이다.

    ```java
    import junit.framework.TestCase;
    
    public class DisplayTest extends TestCase {
    	public void testGetString() {
    		assertEquals("Happy", Display.getString());
    	}
    }
    
    public static void main(String [] args){
    	junit.swingui.TestRunner.run(DisplayTest.class);
    	junit.textui.TestRunner.run(DisplayTest.class);
    	junit.awtui.TestRunner.run(DisplayTest.class);
    }
    ```

  - ##### 테스트 스위트(Test suite)

    ```
    - 여러 개의 테스트 케이스를 한꺼번에 수행하고자 할 때
    - 테스트 스위트는 테스트 케이스와 다른 테스트 스위트를 포함시킬 수 있다.
    - 메소드는 반드시 public static Test suite()여야 한다.
    - 테스트 추가는 suite.addTestSuite(테스트 클래스.class) 형식을 갖는다.
    ```

    ```java
    Class DisplaySuiteTest {
    	public static void main(String [] args){
    		junit.swingui.TestRunner.run(DisplaySuiteTest.class);
    	}
        
    	public static Test suite() {
            TestSuite suite = new TestSuite();
            suite.addTestSuite(DisplayTest.class); // 테스트 케이스 추가
            suite.addTestSuite(DisplayReceiverTest.class);
            suite.addTest(AnotherSuiteTest.suite()); // 다른 테스트 스위트를 포함할 경우
            return suite;
        }
    }
    ```

    테스트 스위트는 여러 개의 테스트 케이스를 함께 수행할 때 사용한다. **예전에는 이런 식으로 테스트 스위트를 작성했으나 현재는 잘 사용하지 않는다.** 테스트 클래스가 추가되거나 변경될 때마다 소스를 직접 수정해야 하기 때문이다. IDE에서 제공하는 기능을 이용하거나 Ant나 Maven 등의 빌드 도구를 사용해 다수의 테스트를 수행한다. (이클립스에서는 Run As -> JUnit Test)

