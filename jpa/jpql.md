# JPQL

테이블이 아닌 객체를 대상으로 검색하는 객체 지향 쿼리
SQL을 추상화 -> 특정 데이터베이스 SQL에 의존하지 않음
객체 지향 SQL 이라고 할 수 있음

## JPQL query 실행시 flush가 자동으로 호출되는 이유

```
em.persist(memberA);
em.persist(memberB);

// 중간에 JPQL 실행
query = em.createQuery(“select m from Member m”, Member.class);
List<Member> members = query.getResultList();
```
만약 flush가 자동실행이 안되면 JPQL위의 두 쿼리가 반영이 안된 상태로 JPQL이 날라가는 셈
members는 비어있을 것