# AnnotatedType 동등성 개선으로 중복 스키마 해석 방지

| 항목 | 내용 |
| --- | --- |
| 프로젝트 | `swagger-api/swagger-core` |
| 작성자 | `juntae6942` |
| PR 개설일 | 2025-09-19 (한국 시간) |
| 상태 | 병합 (2025-10-13) |
| 근거 | [PR #4975](https://github.com/swagger-api/swagger-core/pull/4975), [관련 이슈 #4965](https://github.com/swagger-api/swagger-core/issues/4965), [변경 파일](https://github.com/swagger-api/swagger-core/pull/4975/files) |

## 기여를 시작한 계기

2025년 8월 오픈소스 기여 모임에서 이슈를 찾고 해결안을 제안하는 방법을 배웠다. Swagger Core에서는 이슈 작성자가 짚어 준 `equals()`와 `hashCode()`를 출발점으로 삼았다. 메인테이너가 이슈를 배정한 뒤 캐시 키를 비교하는 조건부터 살펴봤다. [첫 PR 병합 회고](https://parkjunbackend.tistory.com/193)

## 문제와 원인

`ModelConverterContext`는 `AnnotatedType`을 기준으로 이미 해석한 타입을 추적한다. 기존 `equals()`는 컨텍스트 애너테이션 배열을 순서까지 비교했고, `hashCode()`는 일부 애너테이션을 별도로 걸러냈다. 같은 스키마를 뜻하는 타입이 불필요하게 여러 키로 처리돼 스키마 해석이 반복될 수 있었다.

## 구현과 검증

- 두 메서드가 동일하게 정규화한 애너테이션 목록을 사용하도록 수정했다. Java·JDK·Sun 패키지의 애너테이션을 제외하고 애너테이션 타입 이름으로 정렬했다. 타입, JSON View 애너테이션, 미지정 속성 포함 여부도 양쪽에 동일하게 반영했다.
- 애너테이션 배열의 입력과 반환에 방어적 복사를 적용해 외부 배열 변경이 동등성 판단에 영향을 주지 않도록 했다.
- `DummyModelConverter`로 재귀적인 스키마 해석을 구성해, 같은 `String` 타입이 처리된 타입 집합에 한 번만 들어가는지 검증했다. `AnnotatedType` 동등성 테스트도 추가했다.

## 시도와 기여에서 배운 점

처음에는 플래그와 이름을 넓게 비교하고 `getClass()`로 타입을 확인하는 방향을 시도했다. 메인테이너는 애너테이션 선언 순서, 내부 애너테이션 필터링 불일치, 배열의 가변성, 하위 클래스 호환성을 짚었다. 피드백을 바탕으로 `instanceof`를 유지하고, 비교에 필요한 필드를 추린 뒤 양쪽 메서드가 같은 애너테이션 정규화 결과를 쓰도록 수정했다. [초기 시도와 리뷰 반영](https://parkjunbackend.tistory.com/193)

표준 `ModelResolver`로 테스트하려 했을 때는 `String` 같은 단순 타입에 대한 내부 최적화 때문에 캐시를 거치는 재귀 경로를 관찰하기 어려웠다. 테스트용 변환기로 재귀 호출을 직접 구성해, 같은 타입이 처리 집합에 몇 번 들어가는지를 검증했다. 이 과정에서 호출 경로를 통제하면 성능 문제의 원인인 중복 작업을 외부 응답 시간과 독립적으로 확인할 수 있다는 점을 배웠다. 후속 회귀는 캐시 키를 정규화할 때 출력 스키마에 영향을 주는 맥락을 함께 검증해야 한다는 사례가 됐다. [테스트 설계 설명](https://github.com/swagger-api/swagger-core/pull/4975)

## 결과

처음으로 병합된 오픈소스 PR이다. 이슈 탐색부터 리뷰 반영과 회귀 테스트까지 경험하면서, 이후에는 직접 기여할 이슈를 찾아 시도할 자신감을 얻었다. 테스트에서는 여러 경로에 등장한 `String` 타입이 처리 집합에 한 번만 남는 것을 확인했다. [첫 PR 병합 회고](https://parkjunbackend.tistory.com/193) 이후 다형적 하위 타입을 처리할 때 필요한 키 구분은 후속 PR에서 보완했다. [후속 PR #5005](https://github.com/swagger-api/swagger-core/pull/5005)
