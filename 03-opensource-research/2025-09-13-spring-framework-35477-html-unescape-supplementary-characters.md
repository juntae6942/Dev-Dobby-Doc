# HTML 숫자 참조의 유니코드 보충 문자 처리

| 항목 | 내용 |
| --- | --- |
| 프로젝트 | `spring-projects/spring-framework` |
| 작성자 | `juntae6942` |
| PR 개설일 | 2025-09-13 (한국 시간) |
| 상태 | 메인테이너 후속 커밋으로 반영 (2025-11-17) · 7.0.1 반영 · 6.2.14 백포트 |
| 근거 | [PR #35477](https://github.com/spring-projects/spring-framework/pull/35477), [관련 이슈 #35426](https://github.com/spring-projects/spring-framework/issues/35426), [변경 파일](https://github.com/spring-projects/spring-framework/pull/35477/files) |

## 문제와 원인

`HtmlUtils.htmlUnescape()`가 U+FFFF를 넘는 코드 포인트의 숫자 참조를 잘못 변환했다. `HtmlCharacterEntityDecoder`가 파싱한 정숫값을 UTF-16 코드 유닛 하나인 `char`로 캐스팅하면서 상위 비트가 잘렸다. 예를 들어 U+1F600을 나타내는 숫자 참조가 U+F600으로 변환됐다.

## 제안한 변경과 검증

- 유효한 코드 포인트를 `StringBuilder.appendCodePoint()`로 추가해 보충 문자를 UTF-16 서로게이트 쌍으로 출력하도록 변경했다. Unicode 최대 코드 포인트를 초과하는 값은 디코딩 실패로 처리했다.
- 같은 문자의 10진수·16진수 참조가 모두 올바르게 변환되는지 테스트를 추가했다. 이름이 있는 HTML 엔티티와 기본 숫자 참조를 함께 포함한 입력으로 기존 디코딩도 확인하도록 했다.

## 시도와 기여에서 배운 점

10진수와 16진수 숫자 참조에 같은 이모지를 넣어 두 입력이 같은 방식으로 깨지는지 확인했다. 문제가 숫자 파싱 방식이 아닌 파싱 결과를 `char` 하나에 담는 과정에서 생긴다는 점을 짚고, 수정 전후를 비교할 수 있는 테스트와 함께 제안했다. 이 사례에서 코드 포인트와 UTF-16 코드 유닛의 차이를 디코더 동작으로 확인했다. 수용 여부를 파악할 때도 PR 상태와 함께 메인테이너의 반영 커밋과 백포트 이력을 추적할 필요가 있었다. [재현 및 수정 제안](https://github.com/spring-projects/spring-framework/pull/35477)

## 결과

원인 분석, 수정안, 회귀 테스트를 PR로 제출했다. 메인테이너가 기여 내용을 다듬은 후속 커밋으로 반영하면서 2025-11-17에 PR을 닫았다. PR 타임라인에는 7.0.1 마일스톤과 6.2.x 백포트 완료가 기록되어 있으며, 백포트 이슈는 6.2.14 마일스톤에서 종료됐다. [후속 반영 커밋](https://github.com/spring-projects/spring-framework/commit/87d95dc30a6a2034ffca86c3f40250cbbacb2a55), [백포트 이슈 #35832](https://github.com/spring-projects/spring-framework/issues/35832)
