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

# @Column

| attribute | description | default |
|:----:|:---:|:---:|
|name| 필드와 매핑할 테이블의 컬럼 이름 |객체의 필드 이름|
|insertable, updatable |등록, 변경 가능 여부| TRUE|
|nullable(DDL) |null 값의 허용 여부를 설정한다. false로 설정하면 DDL 생성 시에 not null 제약조건이 붙는다.| |
|unique(DDL) |@Table의 uniqueConstraints와 같지만 한 컬럼에 간단히 유니크 제 약조건을 걸 때 사용한다.| |
|columnDefinition (DDL)| 데이터베이스 컬럼 정보를 직접 줄 수 있다. ex) varchar(100) default ‘EMPTY' |필드의 자바 타입과 방언 정보를 사용해|
|length(DDL)| 문자 길이 제약조건, String 타입에만 사용한다 | 255 |
|precision, scale(DDL) | BigDecimal 타입에서 사용한다(BigInteger도 사용할 수 있다). precision은 소수점을 포함한 전체 자 릿수를, scale은 소수의 자릿수 다. 참고로 double, float 타입에는 적용되지 않는다. 아주 큰 숫자나 정 밀한 소수를 다루어야 할 때만 사용한다.| precision=19, scale=2|

# @Enumerated

- Java enum 타입을 매핑할 때 사용

### 주의! ORDINAL 사용X
- enum이 배치된 순서를 저장하기 때문에[ Intger로 저장함 (index 0, 1, 2 ...)
- 나중에 enum들의 순서가 서로 변경된다면 데이터가 꼬이게 된다 -> 장애 유발

| attribute | description | default |
|:----:|:---|:---:|
|value| EnumType.ORDINAL: enum 순서를 데이터베이스에 저장 <br> EnumType.STRING: enum 이름을 데이터베이스에 저장 | EnumType.ORDINAL|

# @Temporal

- 날짜 타입(java.util.Date, java.util.Calendar)을 매핑할 때 사용

### 참고: LocalDate, LocalDateTime을 사용할 때는 생략 가능(최신 하이버네이트 지원)

| attribute | description | default |
|:----:|:---|:---:|
|value | TemporalType.DATE: 날짜, 데이터베이스 date 타입과 매핑 (예: 2013–10–11)<br> TemporalType.TIME: 시간, 데이터베이스 time 타입과 매핑 (예: 11:11:11) <br> TemporalType.TIMESTAMP: 날짜와 시간, 데이터베이스 timestamp 타입과 매핑(예: 2013–10–11 11:11:11)| |

# @Lob

- 데이터베이스 BLOB, CLOB 타입과 매핑
- @Lob에는 지정할 수 있는 속성이 없다.
  
- 매핑하는 필드 타입이 문자면 CLOB 매핑, 나머지는 BLOB 매핑
    - CLOB: String, char[], java.sql.CLOB 
    - BLOB: byte[], java.sql. BLOB
    
# @Transient

- 필드 매핑X
- 데이터베이스에 저장X, 조회X
- 주로 메모리상에서만 임시로 어떤 값을 보관하고 싶을 때 사용

```
@Transient
private Integer temp;
```