# 다형적 하위 타입 스키마의 캐시 충돌 수정

| 항목 | 내용 |
| --- | --- |
| 프로젝트 | `swagger-api/swagger-core` |
| 작성자 | `juntae6942` |
| PR 개설일 | 2025-10-24 (한국 시간) |
| 상태 | 병합 (2025-10-31) |
| 근거 | [PR #5005](https://github.com/swagger-api/swagger-core/pull/5005), [관련 이슈 #5003](https://github.com/swagger-api/swagger-core/issues/5003), [변경 파일](https://github.com/swagger-api/swagger-core/pull/5005/files) |

## 문제와 원인

캐시 키 동등성을 정규화한 뒤, 같은 Java 클래스가 일반 스키마 속성으로 쓰일 때와 `@JsonSubTypes`에서 다형적 하위 타입으로 해석될 때 캐시 항목을 잘못 공유할 수 있었다. 먼저 해석된 스키마가 재사용되면서 하위 타입 고유 속성이 누락될 수 있었다. [선행 PR #4975](https://github.com/swagger-api/swagger-core/pull/4975)

## 구현과 검증

- 최종 변경에서는 `AnnotatedType`에 `isSubtype`을 추가하고, `equals()`와 `hashCode()`에 `schemaProperty`, `isSubtype`, 스키마 속성인 경우의 `propertyName`을 일관되게 반영했다.
- `ModelResolver.resolveSubtypes()`에서 하위 타입으로 발견된 클래스에 `subtype(true)`를 설정했다. 해당 플래그를 속성과 JSON ID 관련 해석 경로에도 전달했다.
- 키 동등성 단위 테스트와 다형성 회귀 테스트를 추가했다. 속성 맥락을 먼저 해석한 뒤에도 다형적 하위 타입의 결과가 독립 해석 결과와 일치하고, 하위 타입 고유 필드가 남는지 확인하도록 했다.

## 시도와 기여에서 배운 점

첫 병합 회고에 정리한 #4975의 테스트는 서로 다른 필드에 등장한 같은 타입의 중복 처리를 확인했다. 후속 이슈에서는 반대 방향의 조건도 검증해야 했다. 같은 타입이라도 스키마가 달라져야 하는 맥락에서는 서로 다른 항목으로 처리해야 했다. [선행 PR 회고](https://parkjunbackend.tistory.com/193)

이슈에 첨부된 재현 프로젝트에서 보고된 실패를 추적하고, 일반 속성으로 먼저 해석한 결과가 다형적 해석에 영향을 주는 경우를 프로젝트 자체의 회귀 테스트에 옮겼다. 하위 타입을 독립적으로 해석했을 때와 비교하고, 하위 타입 고유 속성이 실제로 출력되는지 확인했다. 프리코스 회고에서는 오픈소스 기여를 통해 자동화된 회귀 테스트가 변경의 파급 효과를 빠르게 확인하는 협업 수단이라는 점을 체감했다고 정리했다. 이 과정에서 캐시 동등성의 기준은 Java 타입 자체와 함께 최종 스키마를 바꾸는 해석 맥락까지 고려해야 한다는 점을 배웠다. [테스트와 협업에 대한 회고](https://parkjunbackend.tistory.com/196), [회귀 보고](https://github.com/swagger-api/swagger-core/issues/5003), [후속 수정과 검증](https://github.com/swagger-api/swagger-core/pull/5005)

## 결과

PR이 병합됐다. 일반 속성과 다형적 하위 타입의 해석 맥락을 구분하면서, 같은 맥락에서는 캐시를 재사용할 수 있게 했다.
