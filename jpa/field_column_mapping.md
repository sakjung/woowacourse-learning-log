# mapping

| Annotation | description |
|:----------:|:-----------:|
|@Column | 컬럼 매핑|
|@Temporal | 날짜 타입 매핑|
|@Enumerated | enum 타입 매핑|
|@Lob | BLOB, CLOB 매핑|
|@Transient | 특정 필드를 컬럼에 매핑하지 않음(매핑 무시)|

```
# 객체에는 username 이라는 이름의 필드를 가지고 싶고 table column 이름은 name이라고 하고 싶을 때
@Column(name = "name")
private String username;

# enum을 db에 저장하고 싶을 때
@Enumerated(EnumType.STRING)
private RoleType roleType;

# 날짜 타입을 db에 저장하고 싶을 때
@Temporal(TemporalType.TIMESTAMP)
private Date createdDate;

# varchar를 넘어서는 큰 컨텐츠를 db에 저장하고 싶을 때
# String이면 clob으로 생성됨
@Lob
private String description;
```

## @Column

| attribute | description | default |
|:----:|:---:|:---:|
|name| 필드와 매핑할 테이블의 컬럼 이름 |객체의 필드 이름|
|insertable, updatable |등록, 변경 가능 여부| TRUE|
|nullable(DDL) |null 값의 허용 여부를 설정한다. false로 설정하면 DDL 생성 시에 not null 제약조건이 붙는다.| |
|unique(DDL) |@Table의 uniqueConstraints와 같지만 한 컬럼에 간단히 유니크 제 약조건을 걸 때 사용한다.| |
|columnDefinition (DDL)| 데이터베이스 컬럼 정보를 직접 줄 수 있다. ex) varchar(100) default ‘EMPTY' |필드의 자바 타입과 방언 정보를 사용해|
|length(DDL)| 문자 길이 제약조건, String 타입에만 사용한다 | 255 |
|precision, scale(DDL) | BigDecimal 타입에서 사용한다(BigInteger도 사용할 수 있다). precision은 소수점을 포함한 전체 자 릿수를, scale은 소수의 자릿수 다. 참고로 double, float 타입에는 적용되지 않는다. 아주 큰 숫자나 정 밀한 소수를 다루어야 할 때만 사용한다.| precision=19, scale=2|

## @Enumerated

- Java enum 타입을 매핑할 때 사용

### 주의! ORDINAL 사용X
- enum이 배치된 순서를 저장하기 때문에[ Intger로 저장함 (index 0, 1, 2 ...)
- 나중에 enum들의 순서가 서로 변경된다면 데이터가 꼬이게 된다 -> 장애 유발

| attribute | description | default |
|:----:|:---|:---:|
|value| EnumType.ORDINAL: enum 순서를 데이터베이스에 저장 <br> EnumType.STRING: enum 이름을 데이터베이스에 저장 | EnumType.ORDINAL|

## @Temporal

- 날짜 타입(java.util.Date, java.util.Calendar)을 매핑할 때 사용

### 참고: LocalDate, LocalDateTime을 사용할 때는 생략 가능(최신 하이버네이트 지원)

| attribute | description | default |
|:----:|:---|:---:|
|value | TemporalType.DATE: 날짜, 데이터베이스 date 타입과 매핑 (예: 2013–10–11)<br> TemporalType.TIME: 시간, 데이터베이스 time 타입과 매핑 (예: 11:11:11) <br> TemporalType.TIMESTAMP: 날짜와 시간, 데이터베이스 timestamp 타입과 매핑(예: 2013–10–11 11:11:11)| |

## @Lob

- 데이터베이스 BLOB, CLOB 타입과 매핑
- @Lob에는 지정할 수 있는 속성이 없다.
  
- 매핑하는 필드 타입이 문자면 CLOB 매핑, 나머지는 BLOB 매핑
    - CLOB: String, char[], java.sql.CLOB 
    - BLOB: byte[], java.sql. BLOB
    
## @Transient

- 필드 매핑X
- 데이터베이스에 저장X, 조회X
- 주로 메모리상에서만 임시로 어떤 값을 보관하고 싶을 때 사용

```
@Transient
private Integer temp;
```


# 기본 키 매핑

## @Id & @GeneratedValue

- 직접 할당: @Id만 사용
- 자동 생성: @Id + @GeneratedValue
  
```
@Id
@GeneratedValue(strategy = GenerationType.IDENTITY)
private Long id;
```

### IDENTITY
- 기본 키 생성을 데이터베이스에 위임
- 주로 MySQL, PostgreSQL, SQL Server, DB2에서 사용 (예: MySQL의 AUTO_ INCREMENT)
- JPA는 보통 트랜잭션 커밋 시점에 INSERT SQL 실행
- AUTO_ INCREMENT는 데이터베이스에 INSERT SQL을 실행한 이후에 ID 값을 알 수 있음
- IDENTITY 전략은 em.persist() 시점에 **즉시** INSERT SQL 실행 하고 DB에서 식별자를 조회 (commit 시점 아님!)
- JDBC driver에 insert 후 generated key를 가져오는 구현이 존재함
- persist -> 즉시 insert -> key 가져옴 -> 객체 key setting
- 모아서 insert 하는 것 불가


### SEQUENCE: 데이터베이스 시퀀스 오브젝트 사용, ORACLE
- 데이터베이스 시퀀스는 유일한 값을 순서대로 생성하는 특별한 데이터베이스 오브젝트(예: 오라클 시퀀스)
- 오라클, PostgreSQL, DB2, H2 데이터베이스에서 사용
```
@Entity
@SequenceGenerator(
name = “MEMBER_SEQ_GENERATOR",
sequenceName = “MEMBER_SEQ", //매핑할 데이터베이스 시퀀스 이름
initialValue = 1, allocationSize = 1)
public class Member {
@Id
@GeneratedValue(strategy = GenerationType.SEQUENCE,
private Long id;
```

- @SequenceGenerator

***주의: allocationSize 기본값 = 50***

| attribute | description | default |
|:----:|:---|:---:|
|name |식별자 생성기 이름| 필수
|sequenceName |데이터베이스에 등록되어 있는 시퀀스 이름 |hibernate_sequence|
|initialValue |DDL 생성 시에만 사용됨, 시퀀스 DDL을 생성할 때 처음 1 시작하는 수를 지정한다.| 1|
|allocationSize |시퀀스 한 번 호출에 증가하는 수(성능 최적화에 사용됨 데이터베이스 시퀀스 값이 하나씩 증가하도록 설정되어 있으면 이 값 을 반드시 1로 설정해야 한다 |50|
|catalog, schema |데이터베이스 catalog, schema |이름|

- allocationSize: next call 쿼리를 매번 호출하면 network 비용이 비쌈 
  -> allocationSize를 통해서 한번에 생성할 sequence양을 정해줘서 id를 위해서 매번 next call 쿼리를 날리지 않아도 되게끔. 
  다음 사이즈가 필요할 때 까지는 db를 호출하지 않고 메모리에서 호출

### TABLE: 키 생성용 테이블 사용, 모든 DB에서 사용
- @TableGenerator 필요
- 키 생성 전용 테이블을 하나 만들어서 데이터베이스 시퀀스를 흉 내내는 전략
- 장점: 모든 데이터베이스에 적용 가능
- 단점: 성능

```
create table MY_SEQUENCES (
sequence_name varchar(255) not null,
next_val bigint,
primary key ( sequence_name )
)

@Entity
@TableGenerator(
name = "MEMBER_SEQ_GENERATOR",
table = "MY_SEQUENCES",
pkColumnValue = “MEMBER_SEQ", allocationSize = 1)
public class Member {
@Id
@GeneratedValue(strategy = GenerationType.TABLE,
generator = "MEMBER_SEQ_GENERATOR")
private Long id;
```

### AUTO:방언에 따라 자동 지정, 기본값

## 권장하는 식별자 전략
- 기본 키 제약 조건: null 아님, 유일, 변하면 안된다.
- 미래까지 이 조건을 만족하는 자연키는 찾기 어렵다. 대리키(대체키)를 사용하자.
- 예를 들어 주민등록번호도 기본 키로 적절하기 않다
- 권장: `Long형 + 대체키 + 키 생성전략` 사용

