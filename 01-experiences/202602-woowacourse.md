---
category: experience
type: education
title: 우아한테크코스 8기 백엔드
organization: 우아한테크코스
role: 백엔드 크루 벨로 (박준태)
period: "2026.02–"
tech_stack:
  - Java
  - JUnit
  - Spring Boot
  - JDBC / H2
  - HTTP
keywords:
  - 객체지향 설계
  - 페어 프로그래밍
  - 코드 리뷰
  - 트랜잭션
  - 프레임워크 내부 구조
  - 테크니컬 라이팅
impact: "예약·결제 미션에서 트랜잭션 경계를 다루고, HTTP 서버·MVC·JDBC 라이브러리를 직접 구현한 뒤 팀 서비스에 백엔드 설계와 성능 계측을 적용"
---

# 우아한테크코스 8기 백엔드

백엔드 크루 벨로로 참여했다. Java 도메인 모델링과 테스트에서 시작해 Spring 기반 예약 서비스, 팀 프로젝트, HTTP 서버·MVC·JDBC 라이브러리 구현으로 학습 범위를 넓혔다. 미션별로 페어와 설계를 논의하고 PR에 구현 의도와 리뷰받을 지점을 남겼다. [공개 미션 PR 목록](https://github.com/search?q=org%3Awoowacourse+is%3Apr+author%3Ajuntae6942&type=pullrequests)

## 레벨별 경험

| 단계 | 구현과 학습 |
| --- | --- |
| 레벨 1 · Java와 객체지향 | 블랙잭 게임에 베팅 기능을 확장하면서 도메인 책임과 테스트 범위를 검토했다. 장기 미션에서는 기물별 이동 규칙을 구현하고, H2에 저장한 게임 상태를 다시 불러오는 기능을 추가했다. [블랙잭 PR](https://github.com/woowacourse/java-blackjack/pull/1054), [장기 PR](https://github.com/woowacourse/java-janggi/pull/270) |
| 레벨 2 · Spring 기반 웹 백엔드 | 방탈출 예약 관리에서 시작해 사용자 예약과 테마 조회, 예약 변경·취소, 예약 대기·승인, 외부 결제 API 연동으로 기능을 확장했다. [예약 관리 PR](https://github.com/woowacourse/spring-roomescape-admin/pull/365), [예약 변경·취소 PR](https://github.com/woowacourse/spring-roomescape-member/pull/422), [대기 승인 PR](https://github.com/woowacourse/spring-roomescape-waiting/pull/428), [외부 API 연동 PR](https://github.com/woowacourse/spring-roomescape-waiting/pull/559) |
| 레벨 3 · 가치가챠 팀 프로젝트 | 백엔드 개발자로 매장·가챠 API와 공간 조회, 검색·색인 동기화, 소셜 로그인, 초기 모니터링을 구현했다. B2B 데이터 수집, 관심 기반 추천 알림과 채팅 성능 개선까지 이어서 작업했다. 각 작업의 PR·커밋과 채팅 부하 테스트 조건은 프로젝트 문서에 기록했다. [가치가챠 경험 문서](202607-gachigacha.md), [B2C 저장소](https://github.com/woowacourse-teams/2026-gachi-gacha), [B2B 저장소](https://github.com/woowacourse-teams/2026-gachi-gacha-b2b) |
| 레벨 4 · 백엔드 프레임워크 내부 구현 | 소켓 기반 HTTP 서버에서 로그인 흐름을 만들고 스레드 풀로 동시 요청을 처리했다. MVC 미션에서는 애너테이션 기반 핸들러 매핑과 JSON View를 구현했다. JDBC 미션에서는 `TransactionSynchronizationManager`와 `DataSourceUtils`를 구현해 스레드에 바인딩된 커넥션을 재사용하고, 정리 시 `ThreadLocal`을 제거했다. [HTTP 서버 PR](https://github.com/woowacourse/java-http/pull/1289), [MVC PR](https://github.com/woowacourse/java-mvc/pull/1172), [JDBC PR](https://github.com/woowacourse/java-jdbc/pull/1375) |

## 블로그에 기록한 기술적 판단과 검증

### 레벨 1 · 객체의 책임과 협력부터 설계

블랙잭 리뷰에서 Service와 Controller의 역할과 필요성을 질문받았다. 이후 『객체지향의 사실과 오해』를 읽고, 클래스부터 나누던 방식에서 도메인 객체가 협력하려면 어떤 메시지를 주고받아야 하는지 먼저 정하는 방식으로 설계 순서를 바꿨다. 객체를 재사용하는 경량 패턴도 생성 비용만 보고 선택하기보다 JVM의 객체 할당과 GC까지 고려해 판단해야 할 대상으로 다뤘다. [레벨 1 전체 회고](https://parkjunbackend.tistory.com/204)

### 레벨 2 · 예약 정합성과 결제 실패 처리

- **예약 변경의 트랜잭션 경계** — 확정 예약 변경 중 충돌이 나면 예외를 잡아 대기 예약으로 전환하려 했다. 내부 작업이 같은 물리 트랜잭션에 합류한 상태에서 롤백 전용으로 표시돼, 외부에서 예외를 처리한 뒤에도 커밋 시 `UnexpectedRollbackException`이 발생했다. 충돌 가능성이 있는 변경을 `REQUIRES_NEW`로 격리했다. 별도의 통합 테스트에서는 같은 슬롯에 20개 요청을 동시에 보내 1건 확정·19건 대기를 확인했고, 대기자 승격 저장을 강제로 실패시켜 대기자 데이터가 롤백으로 보존되는지도 확인했다. [레벨 2 전체 회고](https://parkjunbackend.tistory.com/206)
- **토스페이먼츠 연동** — 결제사 DTO와 오류 응답을 어댑터에서 도메인 모델과 예외로 변환했다. 승인 요청의 읽기 타임아웃은 결제 결과를 모르는 상태로 보고, 재시도 시 같은 멱등 키를 유지하는 이유를 정리했다. 토큰 버킷을 인바운드와 아웃바운드에 적용해 서버 요청과 결제사 호출을 각각 제한했다. 인터셉터와 `@Retryable`의 재시도가 중첩될 수 있다는 점도 검토했다. [토스페이먼츠 연동 회고](https://parkjunbackend.tistory.com/207)

### 레벨 3 · 배포 제약에 맞춘 CI/CD 구성

EC2의 SSH 접근 제약으로 GitHub Actions에서 SSH로 배포하는 초기 구상을 바꿨다. EC2 내부에서 Jenkins와 GitHub Webhook을 연결하는 방식을 시도한 뒤 GitHub Actions Self-hosted Runner를 도입했다. 이 과정에서 프리티어 EC2에서 Spring Boot를 빌드할 때의 메모리 제약과 Runner 서비스 계정의 실행 권한 문제도 기록했다. [Self-hosted Runner 도입 회고](https://parkjunbackend.tistory.com/208)

### 레벨 4 · 교환 기능을 위한 OIDC 로그인

지도 탐색에 사용자 간 교환을 추가하면서 회원 인증이 필요해졌다. 제공자별 프로필 응답을 처리하던 기존 소셜 로그인 경험을 바탕으로 OIDC의 ID 토큰을 활용하는 방식을 학습하고 적용했다. 제공자별 구현체를 두고 공통 인증 흐름에서 적절한 구현체를 선택하도록 구성했다. 이후 기술 글에서는 서버 내부와 브라우저 전체를 나눠 로그인 시간을 계측했다. [OIDC 기반 로그인 구현기](https://parkjunbackend.tistory.com/209)

## 가치가챠에서 팀과 문제를 해결한 경험

- **인프라 설계와 사용자 조사** — 개발·운영 환경에 모니터링, 로그, 대시보드를 어떻게 연결할지 칠판에 그려 팀과 검토했다. 팀원은 그 설계를 바탕으로 후속 설정을 진행할 수 있었다고 피드백했다. 홍대 가챠샵 인근에서는 팀원들과 스티커 조사판으로 의견을 모았고, 팀은 조사 결과를 교환 기능을 추가하는 근거로 활용했다. [LMS+ 스프린트 1 팀원 피드백](https://techcourse-lms-plus-web.woowahan.com/feedbacks/me)
- **Apple 로그인에서 프론트엔드와 API 계약 조율** — Apple의 `form_post` 콜백을 기존 로그인 흐름에 연결하는 과정에서 프론트엔드 복귀와 응답 처리 문제가 생겼다. 프론트엔드 팀원과 로그인 시작 경로, 콜백 처리, 탈퇴 시 재인증 흐름을 단계별로 맞췄다. 탈퇴 재인증에는 코드 리뷰 의견을 반영해 별도 콜백을 적용했다. [LMS+ 스프린트 3·4 팀원 피드백](https://techcourse-lms-plus-web.woowahan.com/feedbacks/me), [Apple 로그인 PR](https://github.com/woowacourse-teams/2026-gachi-gacha/pull/362)
- **운영과 테스트 기준 정리** — Elasticsearch를 도입한 뒤 교환글 설명의 키워드와 DB–색인 정합성을 챙겼고, 조건 구독형 알림을 개발했다. 스프린트 4에서는 테스트 기준을 정리했고, 팀원은 이를 통해 공통 기준으로 테스트를 논의할 수 있었다고 평가했다. [LMS+ 스프린트 4 팀원 피드백](https://techcourse-lms-plus-web.woowahan.com/feedbacks/me), [검색 색인 개선 PR](https://github.com/woowacourse-teams/2026-gachi-gacha/pull/571), [조건 구독형 알림 PR](https://github.com/woowacourse-teams/2026-gachi-gacha/pull/639)

## 우아한 전시회 · 가치가챠 피칭과 부스 운영

1부 팀별 피칭에서는 가치가챠를 소개했고, 2부 부스에서는 현업 개발자들에게 서비스를 만든 이유와 기술적 선택의 근거를 설명하며 질문과 피드백을 받았다. 매장 인스타그램에서 수집한 가챠 정보가 부족하다는 단서를 세션 리플레이에서 확인해 홍대 매장을 다시 조사한 과정과, 현장 설문 참여자 161명 중 87.6%가 교환 의향을 보인 결과를 바탕으로 교환글과 채팅을 추가한 과정을 소개했다.

첫 부스 소개에서는 긴장해 서비스의 핵심과 팀이 내린 결정을 순서대로 설명하는 데 어려움을 겪었다. 설명을 끝까지 듣고 후속 질문을 해 준 개발자와 대화하면서 구현 과정에서 고민한 이유를 더 구체적으로 전하고 싶다고 느꼈다. 다음 발표를 위해 해결하려는 문제와 선택의 근거를 짧게 설명하는 연습을 하기로 했다. [우아한 전시회 및 팀 프로젝트 회고](https://parkjunbackend.tistory.com/211)

## 피드백과 설문에서 돌아본 점

- 팀원들은 기술적 선택의 근거를 설명하고 PR을 꼼꼼히 검토·반영하는 점을 강점으로 꼽았다. 개선점으로는 진행 중인 업무를 더 일찍 공유하고, 인증·검색·알림처럼 담당자에게 지식이 집중되는 기능의 설계 의도와 검증 결과를 팀에 남겨 달라는 의견이 있었다. [LMS+ 팀원 상호 피드백](https://techcourse-lms-plus-web.woowahan.com/feedbacks/me)
- 프레임워크 미션에서 동료 코드를 리뷰하며 `JdbcTemplate`의 `update`와 `query`에 반복된 커넥션 획득·반납을 짚고 공통 처리 예시를 제안했다. 동료는 제안을 바탕으로 실제로 공통 메서드를 추출했다고 피드백했다. [LMS+ JDBC 미션 리뷰어 피드백](https://techcourse-lms-plus-web.woowahan.com/feedbacks/me)
- 레벨 4 학습 경험 설문에는 독학 중 정리하기 어려웠던 대규모 시스템 설계의 접근 순서를 강의로 정리할 수 있었다고 답했다. 프로젝트 운영에 대해서는 팀 구성과 아이디어 확정에 걸리는 시간을 줄이고, 스프린트 산출물이 실제 의사결정에 쓰이도록 다듬자는 의견을 냈다. 심리적 안전감 설문에서는 데모데이 질문을 통해 서비스의 부족한 점을 발견했고, 원온원에서 기술적 도움을 받았다고 돌아봤다. [LMS+ 설문 응답](https://techcourse-lms-plus-web.woowahan.com/crew/surveys)
- 소프트스킬 성장 리포트에서 자기응답 기준 문제 해결 태도 항목은 레벨 2 후반 9.5점에서 레벨 3 10.0점으로 변했다. [LMS+ 소프트스킬 리포트](https://techcourse-lms-plus-web.woowahan.com/crew/soft-skill)

## 레벨별 제출 회고

- **레벨 1 · 페어와 설계 과정부터 합의하기** — 블랙잭 미션에서는 예시 코드를 놓고 계층별 책임을 비교하는 토론을 했다. 장기 미션에서는 구조와 다이어그램까지 준비해 갔고, 페어가 제 설계를 이해하는 데 시간을 쓰면서 자기 설계를 제안할 기회가 줄었다. 구현 중에는 페어의 의견을 반영하려고 했고, 다음에는 각자 어디까지 준비하고 어떤 결정을 함께 내릴지 먼저 합의하겠다고 정리했다. [레벨 1 회고](https://github.com/woowacourse/woowa-writing/blob/012501f3670bf428359ad3aa6e2da7ba33ec7a62/Level1_%EB%B2%A8%EB%A1%9C_%ED%9A%8C%EA%B3%A0.md)
- **레벨 2 · 트랜잭션 경계를 코드로 검증하기** — 요청이 Controller → Manager → Service를 거치는 구조에서 Manager에 `@Transactional`을 선언했다. 리뷰를 통해 독립적으로 수행돼야 할 하위 작업까지 상위 트랜잭션에 묶인 점을 확인했다. 그 경험을 바탕으로 트랜잭션 진입점과 각 작업의 실패·롤백 범위를 먼저 정의하고, 새 기술을 익힐 때는 작은 코드로 실제 동작을 확인하겠다고 정리했다. [레벨 2 회고](https://github.com/woowacourse/woowa-writing/blob/012501f3670bf428359ad3aa6e2da7ba33ec7a62/Level2_%EB%B2%A8%EB%A1%9C_%ED%9A%8C%EA%B3%A0.md)
- **레벨 3 · 업무 분담과 리뷰에서 동료의 접근 배우기** — 가치가챠 초기 API를 분담할 때 팀원들이 원하는 일을 먼저 고르도록 했다. 이미지 업로드를 맡은 팀원은 그동안 제가 재사용해 온 AWS SDK v1의 `AmazonS3` 대신 SDK v2의 `S3Client`로 구현했다. 코드를 리뷰하며 익숙한 기능에서도 기존 구현을 다시 살펴볼 계기가 생긴다는 점을 구체적인 사례로 남겼다. [레벨 3 회고](https://github.com/woowacourse/woowa-writing/blob/012501f3670bf428359ad3aa6e2da7ba33ec7a62/Level3_%EB%B2%A8%EB%A1%9C_%ED%9A%8C%EA%B3%A0.md)

## 기술 글쓰기와 기타 제출물

- 기술 토론 미션에서는 실제 대화에 개념을 적용하고 자신의 기준을 유지한 점을 평가받았다. 성찰 글에는 당시 상황과 행동을 더 구체적으로 담으라는 피드백을 받았다.
- 레벨 4 테크니컬 라이팅에서 소셜 로그인에 OIDC를 적용하고, OAuth 2.0과 OIDC를 방식별 네 번 측정했다. 앱 처리 시간 합계는 각각 6.36ms와 6.01ms, 브라우저 전체 평균은 245ms와 189ms였다. 실행 순서가 고정돼 있었고 캐시 상태와 측정 구간이 결과에 영향을 줄 수 있다는 점을 글에 명시했다. [OIDC 기술 글 최종본](https://github.com/woowacourse/woowa-writing/blob/012501f3670bf428359ad3aa6e2da7ba33ec7a62/technical-writing-%EB%B2%A8%EB%A1%9C.md)
- Gemini Canvas 미션에서는 REST API 네이밍 도구를 포함한 웹앱 4개와 AI 사용 일지를 제출했다. HTML·CSS 실습으로 Today I Learn 페이지를 제작해 GitHub Pages에 배포했다. [Gemini Canvas PR](https://github.com/woowacourse/gemini-canvas-mission/pull/1), [Today I Learn PR](https://github.com/woowacourse/web-today-i-learn/pull/2)
