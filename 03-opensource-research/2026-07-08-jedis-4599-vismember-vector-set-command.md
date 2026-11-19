# Redis 벡터 집합의 VISMEMBER 명령 지원

| 항목 | 내용 |
| --- | --- |
| 프로젝트 | `redis/jedis` |
| 작성자 | `juntae6942` |
| PR 개설일 | 2026-07-08 (한국 시간) |
| 상태 | 병합 (2026-07-10) |
| 근거 | [PR #4599](https://github.com/redis/jedis/pull/4599), [관련 이슈 #4594](https://github.com/redis/jedis/issues/4594), [변경 파일](https://github.com/redis/jedis/pull/4599/files) |

## 문제

Redis 8.0의 `VISMEMBER`는 벡터 집합에 이름으로 식별되는 요소가 있는지 확인하는 명령이다. Jedis의 벡터 집합 API에 이 명령을 추가해 기존 Java 클라이언트 인터페이스에서 사용할 수 있도록 했다.

## 구현과 검증

- 프로토콜 명령과 `CommandObjects`에 `VISMEMBER`를 연결하고, `BuilderFactory.BOOLEAN`으로 응답을 Java의 boolean 결과로 변환했다.
- `Jedis`, `UnifiedJedis`, 파이프라인에 `vismember`를 추가하고 기존 벡터 집합 API와 동일하게 `String`과 `byte[]`를 지원했다.
- Jedis와 UnifiedJedis 테스트에 Redis 8.0 이상에서 실행되는 통합 테스트를 추가했다. 집합이 없는 경우, 요소를 추가하거나 삭제한 뒤, 집합에 다른 요소가 남아 있는 경우의 멤버십을 검사하고 문자열·바이너리 호출을 검증했다.

## 기여를 시작한 계기와 배운 점

Redis에 관심을 두고 여러 기능을 직접 테스트하다가 Jedis 이슈 목록에서 메인테이너가 제안한 `VISMEMBER` 지원 작업을 발견했다. 관심 있던 기술을 실습하는 과정에서 실제 프로젝트에 필요한 기능을 찾고, 기존 벡터 집합 API의 패턴을 따라 구현해 기여까지 이어갔다. [LinkedIn 기여 글이 표시된 공개 활동](https://kr.linkedin.com/in/daemin-kim), [기능 요청 #4594](https://github.com/redis/jedis/issues/4594)

이 작업으로 Redis 서버 명령이 Jedis의 프로토콜 정의, 응답 변환, 동기·파이프라인 API에 어떻게 연결되는지 살펴봤다. 문자열과 바이너리 호출을 모두 구현하고, 요소 추가·삭제 전후를 통합 테스트에서 확인하면서 서버 기능을 클라이언트 API로 노출할 때 검증해야 할 범위를 익혔다. [구현 및 통합 테스트](https://github.com/redis/jedis/pull/4599/files)

## 결과

PR이 병합됐다. 동기 호출과 파이프라인에서 벡터 집합의 요소 존재 여부를 확인할 수 있게 됐다.
