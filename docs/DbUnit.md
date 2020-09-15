# 데이터베이스 테스트: DbUnit

## DbUnit의 장점

DB를 사용하는 부분은 프로그래밍 언어 외적인 부분이 상당 부분 포함되기 때문에 TDD를 적용하기가 종종 쉽지 않다. 특히 데이터베이스의 상태를 일정하게 유지하면서 테스트를 지속적으로 수행 가능하게 만드는 데는 고통스러운 작업이 필요하다. 또한 테스트 전후의 데이터베이스 상태를 비교하는 것도 손쉬운 일은 아니다.

이럴 때 도움을 받을 수 있는 대표적인 유틸리티로 DbUnit(www.dbunit.org)이 있다. DbUnit은 다음과 같은 기능을 제공함으로써 DB를 사용하는 테스트 케이스 작성 시에 도움을 준다.

- #### 독립적인 데이터베이스 연결을 지원한다.

   JDBC, DataSource, JNDI 방식 지원

- ####  데이터베이스의 특정 시점 상태를 쉽게 내보내거나(export) 읽어들일(import) 수 있다.

  xml 파일이나 csv 파일 형식을 지원한다. import 계열 동작일 경우에는 엑셀 파일도 지원한다.

- ####  테이블이나 데이터셋을 서로 쉽게 비교할 수 있다.

보통 DbUnit은 독립적으로 사용하기보다는 JUnit 등의 테스트 프레임워크 등과 함께 사용한다. 그래서 DbUnit은 테스트 프레임워크라기보다는 테스트 지원 라이브러리에 더 가깝다. DbUnit을 알면 테스트 케이스 작성 시 DB 관리가 매우 편리해진다. 하지만 그러기 위해서는 제일 먼저 알아야 하는 DbUnit의 개념이 하나 있다. 바로 데이터셋(Dataset)이다.

## 데이터셋

DbUnit에서 이야기하는 데이터셋(DataSet)은 데이터베이스나 그 안에 존재하는 테이블 혹은 그 일부를 xml이나 csv(comma separated value) 파일로 나타낸 모습이다. 이를테면 다음과 같은 판매자(SELLER) 테이블이 있다고 하자.

![](../images/5-1.jpg)

##### 판매자 테이블에 들어 있는 데이터

| ID        | NAME   | EMAIL              |
| --------- | ------ | ------------------ |
| seongmink | 김성민 | sminggo5@naver.com |
| wooyoung  | 장우영 | tmlu48@naver.com   |
| kidong    | 강기동 | ki0050@naver.com   |

위 내용을 DbUnit의 데이터셋으로 만들면 다음과 같이 표현된다.

##### seller.xml 파일

```xml
<?xml version='1.0' encoding='UTF-8'?>
<dataset>
    <seller ID="seongmink" NAME="김성민" EMAIL="sminggo5@naver.com"/>
    <seller ID="wooyoung" NAME="장우영" EMAIL="tmlu48@naver.com"/>
    <seller ID="kidong" NAME="강기동" EMAIL="ki0050@naver.comt"/>
</dataset>
```

위 XML은 DbUnit에서 제공하는 여러 데이터셋 중 가장 대표적인 형식인 FlatXmlDataSet 형식의 데이터셋이다. XML에서 seller라고 표시된 요소(element)는 DB 테이블 이름에 해당하고 ID, NAME, EMAIL 등의 속성(attribute)명은 해당 테이블의 컬럼 이름을 나타낸다. DbUnit은 이런 데이터셋이라 불리는 형식을 이용해서 DB의 상태를 저장하거나 변경, 유지한다. 그리고 데이터베이스 전체나 특정 테이블 등을 비교할 때도 이 데이터셋을 사용한다. 

### 데이터베이스 연결과 테이블 초기화

앞서 살펴본 판매자 테이블을 이용해서 판매자 정보를 DB에 저장하는 DatabaseRepository 클래스를 만들어보자. 이제, DatabaseRepository 클래스를 구현 하기 위해 테스트 케이스를 작성해야 한다. 그래서 DatabaseRepository 클래스가 가져야 하는 기능들을 떠올리다 보니, 문득 향후에는 DB가 아닌 파일이나 다른 매체에 저장될 수 있다는 생각이 들었다. 그렇다면, 인터페이스를 먼저 만들면 좋겠다. 그리고 이 인터페이스를 이용해서 DatabaseRepository를 만들면 좀 더 유연한 구조가 될 것 같다. 다음은 저장소를 대표하는 Repository 인터페이스다.

##### 저장소(Repository) 인터페이스

```java
public interface Repository {
    public Seller findById(String id);
    public void add(Seller seller);
    public void update(Seller seller);
    public void update(Seller selller);
}
```

이제 Repository 인터페이스를 이용해 DatabaseRepository를 만들어보자. DatabaseRepository는 Repository 스펙에 맞춘 구현체 중 하나로 판매자들을 관리하는 기능을 제공한다. 우선, Respository 인터페이스에 정의되어 있는 여러 기능 중, 판매자의 ID를 기준으로 판매자 정보를 가져오는 findById 기능을 구현해보자. 다음은 findById 기능을 구현하기 위해 만든 테스트 클래스와 테스트 메소드다.

```java
public class DatabaseRepositoryTest {
    @Test
    public void testFindById() throws Exception {
        Seller expectedSeller = new Seller("seongmink","김성민", "smingog5@naver.com");
        Repository repository = new DatabaseRepository();
        Seller actualSeller = repository.findById("seongmink");
        
        assertEquals(expectedSeller.getId(),actualSeller.getId());
        assertEquals(expectedSeller.getName(),actualSeller.getName());
        assertEquals(expectedSeller.getEmail(),actualSeller.getEmail());
	}
    ...
}
```

위 테스트 케이스는 단순히 ID 값으로 조회해서 나온 결과를 예상 결과와 비교하는 로직이기 때문에, 이 상태에서 곧바로 DatabaseRepository 구현에 들어가도 별 무리는 없다. 하지만 현재 가정한 테이터베이스 내의 데이터 내용이 변경된다면, 기능 구현에 문제가 없음에도 불구하고 테스트 케이스가 실패할 수 있다. 따라서 **현재 가정되어 있는 DB 안의 데이터 상태가 테스트를 수행하기 전에 가정했던 모습으로 한결같이 유지됐으면 좋겠다**고 생각하여 다음과 같은 전략을 세웠다.

![](../images/5-2.jpg)