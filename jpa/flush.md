# Flush

영속성 컨텍스트의 변경내용을 데이터베이스에 반영

## flush 발생

- 변경 감지 (최초 버전의 entity 스냅샷과 1차캐시의 상태를 비교)
- 변경사항이 있으면 update query를 만들어서 쓰기지연 SQL 저장소에 저장
- 쓰기 지연 SQL 저장소의 쿼리를 데이터베이스에 전송 (등록, 수정, 삭제)

## Flush mode option

```
entityManager.setFlushMode(FlushModeType.COMMIT)
```

- FlushModeType.AUTO
  커밋이나 쿼리를 실행할 때 flush (default)

- FlushModeType.COMMIT
  커밋할 때만 flush

- 웬만하면 손 대지 말고 default로 놓고 쓰는 것이 좋음

## Flush는

- 영속성 컨텍스트를 비우지 ***않음***
- 영속성 컨텍스트의 변경내용을 데이터베이스에 동기화
- 트랜잭션이라는 작업 단위가 중요 -> 커밋 직전에만 동기화 하면 됨


