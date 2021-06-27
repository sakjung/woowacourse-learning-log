# @Entity

- @Entity가 붙은 클래스는 JPA가 관리, 엔티티라 한다
- JPA를 사용해서 테이블과 매핑할 클래스는 @Entity 필수

**주의**
- 기본 생성자 필수
- final class, enum, interface, inner class 사용 불가
- final filed 사용 불가

## name

- JPA에서 사용할 엔티티 이름을 지정
- JPA가 내부적으로 구분하는 이름이라고 보면 됨
- default로 클래스 이름을 그대로 사용
- 같은 클래스 이름이 없으면 가급적 default 사용할 것

# @Table

- 엔티티와 매핑할 테이블 지정

## name
- 매핑할 테이블 이름 (default: 엔티티 이름)

## catalog
- 데이터베이스 catalog mapping

## schema
- 데이터베이스 schema mapping

## uniqueConstraints (DDL)
- DDL 생성 시 유니크 제약 조건 생성

# 데이터베이스 스키마 자동 생성

- DDL을 애플리케이션 로딩(실행)시점에 자동 생성
- 테이블 중심 -> 객체 중심
- 데이터 베이스 방언을 활용해서 데이터베이스에 맞는 적절한 DDL 알아서 생성해줌
- 꼭! 개발장비에서만 사용할 것! production 환경에서는 쓰면안됨
- 생성된 DDL은 운영서버에서는 사용하지 않거나, 적절히 다듬은 후에 사용할 것
- Primitive type field (e.g. int): 자동 not null 설정
- Wrapper class type (e.g. Integer): not null 설정 안붙음 (nullable)

```
e.g. 

hibernate.hbm2ddl.auto = create
```

- create: 기존 테이블 삭제 후 다시 생성 (DROP + CREATE)
- create-drop: create와 같으나 종료시점에 테이블 DROP
- update: 변경분만 반영 (운영 DB에는 사용하면 안됨)
  field 추가시 alter table add column
  field 삭제시에는 아무 작동 안함
- validate: 엔티티와 테이블이 정상 매핑 되었는지만 확인
- none: 사용하지 않음

## 주의사항
- 운영장비에는 절대 create, create-drop, update 사용하면 안된다
- 개발 초기: create or update
- 테스트 서버: update or validate
- 스테이징 & 운영 서버: validate or none

# DDL 생성 기능
- 제약조건 추가
```
# 회원 이름은 필수, 10자 초과 x
@Column(nullable = false, length = 10)
private String name;

# 회원 이름은 유니크
# unique 제약 조건 이름이 랜덤으로 생성되기 때문에 잘 안씀!
@Column(unique = true)
private String name;
```

- 유니크 제약조건 추가
```
@Table(uniqueConstraints = {@UniqueConstraints(name = “NAME_AGE_UNIQUE”, columnNames = {“NAME”, “AGE”} )})
public class Member {
...
}
```

- DDL 생성 기능은 DDL을 자동 생성할 때만 사용되고 JPA의 실행 로직에는 영향을 주지 않음