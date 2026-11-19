---
category: experience
type: team-project
title: 가치가챠 (GachaGacha)
organization: 우아한테크코스 8기 팀 프로젝트
role: Backend Developer (B2C 매장·가챠 API와 인증, B2B 데이터 수집, 검색·색인 동기화, 초기 모니터링, 추천 알림·채팅 성능 개선)
period: 2026.07 - 2026.12
team_size: 5명 (백엔드 3, 프론트엔드 2)
tech_stack:
  - Java 21
  - Spring Boot 4.1.0
  - PostgreSQL 17 (PostGIS)
  - MongoDB
  - Elasticsearch (nori)
  - AWS S3
  - AWS EC2 / Docker / Nginx / GitHub Actions
  - Prometheus / Grafana / k6
keywords:
  - 위치기반서비스
  - 공간쿼리
  - 검색엔진
  - 실시간채팅
  - 추천알림
  - 모니터링
  - 부하테스트
  - OAuth
  - B2B
impact: "매장 Instagram 게시물 기반 가챠 수집 환경과 PostGIS 매장 조회, Elasticsearch 검색·색인 동기화를 구현하고, 채팅방 갱신의 참여자당 조회를 7회에서 2회로 줄임"
---

# 가치가챠 (GachaGacha) — 가챠샵 위치 탐색 및 굿즈 교환 플랫폼

## 1. 프로젝트 개요 (What & Why)
* **서비스 소개:** 홍대 가챠(캡슐토이) 매장과 보유 기기 정보를 탐색하고, 중복 뽑기 굿즈를 유저 간 1:1 채팅으로 교환할 수 있는 위치 기반 커뮤니티 및 B2B 매장 관리 포털
* **서비스 범위:** 매장 데이터 수집의 한계로 현재는 홍대 매장으로 범위를 한정해 운영
* **핵심 문제 해결:** 파편화된 가챠샵 정보 부재 해소, 오프라인 직거래 중심의 굿즈 교환 비효율 개선, 매장 점주 대상 신작 알림 및 매장 관리 기능 제공

## 2. 내 역할

백엔드 개발자 벨로(박준태, `juntae6942`)로 매장·가챠 도메인 API, 공간 조회, 검색과 색인 관리, 회원 인증, 초기 모니터링을 구현했다. 이후 관심 기반 추천 알림과 채팅방 목록 갱신 성능 개선을 개발했다. B2B에서는 매장 게시물 기반 가챠 데이터 수집 초기 환경을 구축하고, 가챠 검색과 색인·동의어 관리 기능을 개발했다.

2026년 11월 19일 GitHub 조회 기준으로 B2C 집계 대상 PR은 32건이다. 병합 27건, 열려 있는 PR 2건, 미병합으로 닫힌 PR 3건이다. 대상 브랜치를 `main`으로 지정했다가 미병합 상태로 닫은 PR 4건을 집계에서 제외했다. [#3](https://github.com/woowacourse-teams/2026-gachi-gacha/pull/3), [#23](https://github.com/woowacourse-teams/2026-gachi-gacha/pull/23), [#43](https://github.com/woowacourse-teams/2026-gachi-gacha/pull/43), [#109](https://github.com/woowacourse-teams/2026-gachi-gacha/pull/109)

B2B 집계 대상 PR은 1건이며 열려 있다. 같은 검색 브랜치에서 `main`을 대상으로 열었다가 미병합 종료된 PR은 제외했다. 두 저장소 합계는 33건으로, 병합 27건, 열려 있는 PR 3건, 미병합으로 닫힌 PR 3건이다. [B2B PR #30](https://github.com/woowacourse-teams/2026-gachi-gacha-b2b/pull/30), [제외한 B2B PR #29](https://github.com/woowacourse-teams/2026-gachi-gacha-b2b/pull/29)

## 3. 주요 기여

### 매장·가챠 도메인과 공간 조회

- 가챠·매장 도메인 모델, 공통 API 응답과 예외 처리, 가챠 CRUD, 매장 보유 가챠 등록·조회·삭제 및 테스트를 작성했다. [도메인 정의 PR](https://github.com/woowacourse-teams/2026-gachi-gacha/pull/4), [PR #10](https://github.com/woowacourse-teams/2026-gachi-gacha/pull/10), [PR #12](https://github.com/woowacourse-teams/2026-gachi-gacha/pull/12), [PR #24](https://github.com/woowacourse-teams/2026-gachi-gacha/pull/24)
- 인근 매장 조회를 애플리케이션 라이브러리에서 PostgreSQL/PostGIS 쿼리로 옮겼다. `ST_DWithin`으로 반경을 필터링하고 `ST_DistanceSphere`로 거리를 산출했다. Flyway에 공간 확장과 GiST 인덱스를 추가하고, DB 변경에 맞춰 CI를 수정했다. [PR #44](https://github.com/woowacourse-teams/2026-gachi-gacha/pull/44), [커밋 `811de02b`](https://github.com/woowacourse-teams/2026-gachi-gacha/commit/811de02b), [커밋 `0b0572a9`](https://github.com/woowacourse-teams/2026-gachi-gacha/commit/0b0572a9)
- 특정 가챠를 보유한 인근 매장 조회, 카테고리별 가챠 목록, 가챠별 보유 매장 수를 구현했다. B2C 조회 구조 변경에 맞춰 기존 API와 클래스를 정리했다. [PR #110](https://github.com/woowacourse-teams/2026-gachi-gacha/pull/110), [PR #155](https://github.com/woowacourse-teams/2026-gachi-gacha/pull/155), [PR #166](https://github.com/woowacourse-teams/2026-gachi-gacha/pull/166), [PR #86](https://github.com/woowacourse-teams/2026-gachi-gacha/pull/86)

### Elasticsearch 검색과 색인 정합성

- 가챠·교환글 검색을 Elasticsearch로 구현했다. 가챠 색인에 한국어 Nori 분석기와 동의어 설정을 적용하고, 교환글 검색에는 설명 필드 가중치를 추가했다. [PR #243](https://github.com/woowacourse-teams/2026-gachi-gacha/pull/243), [커밋 `756fa916`](https://github.com/woowacourse-teams/2026-gachi-gacha/commit/756fa916), [PR #571](https://github.com/woowacourse-teams/2026-gachi-gacha/pull/571)
- 도메인 이벤트를 받아 색인을 비동기로 갱신하고, DB와 Elasticsearch 사이에 누락되거나 어긋난 색인을 주기적으로 확인·갱신하는 스케줄러를 구현했다. Elasticsearch 문서 순회에는 커서 방식을 사용했다. [커밋 `24bf47c2`](https://github.com/woowacourse-teams/2026-gachi-gacha/commit/24bf47c2), [PR #571](https://github.com/woowacourse-teams/2026-gachi-gacha/pull/571), [커밋 `ceb80f0d`](https://github.com/woowacourse-teams/2026-gachi-gacha/commit/ceb80f0d)
- B2B에서는 가챠 검색에 Nori와 동의어를 적용하고, 동의어 관리 API와 Elasticsearch 동의어 동기화를 구현했다. 가챠 등록·수정·삭제와 게시물 수집에서 색인 갱신 이벤트를 발행하고, 매장 변경 시 연관 가챠도 다시 색인하도록 연결했다. 색인 초기화는 비동기로 처리했다. 11월 19일 기준 B2B 검색 PR은 열려 있다. [B2B PR #30](https://github.com/woowacourse-teams/2026-gachi-gacha-b2b/pull/30), [동의어 관리](https://github.com/woowacourse-teams/2026-gachi-gacha-b2b/commit/c918b69), [색인 초기화](https://github.com/woowacourse-teams/2026-gachi-gacha-b2b/commit/35466f7), [매장·수집 변경 연동](https://github.com/woowacourse-teams/2026-gachi-gacha-b2b/commit/993b91f)

### 소셜 로그인과 회원 세션 관리

- Kakao·Naver 소셜 로그인과 JWT 인증을 구현했다. 마이페이지 회원 정보 조회·수정 및 상태별 본인 교환글 조회를 추가했다. [PR #96](https://github.com/woowacourse-teams/2026-gachi-gacha/pull/96), [PR #99](https://github.com/woowacourse-teams/2026-gachi-gacha/pull/99)
- Refresh Token 발급·해시 저장·갱신과 로그아웃·회원 탈퇴 시 세션 정리, 만료 토큰 삭제 스케줄러를 구현했다. [PR #141](https://github.com/woowacourse-teams/2026-gachi-gacha/pull/141), [커밋 `d15b75a5`](https://github.com/woowacourse-teams/2026-gachi-gacha/commit/d15b75a5), [커밋 `e0f25c`](https://github.com/woowacourse-teams/2026-gachi-gacha/commit/e0f25c), [커밋 `ebf2e067`](https://github.com/woowacourse-teams/2026-gachi-gacha/commit/ebf2e067)
- Apple 로그인에서 ID 토큰을 검증하고 회원 탈퇴 시 Apple Refresh Token을 폐기하도록 구현했다. 모바일 로그인용 nonce 발급·저장과 API 명세 수정에도 참여했다. [PR #362](https://github.com/woowacourse-teams/2026-gachi-gacha/pull/362), [PR #520](https://github.com/woowacourse-teams/2026-gachi-gacha/pull/520), [커밋 `6e7fbbd8`](https://github.com/woowacourse-teams/2026-gachi-gacha/commit/6e7fbbd8)

### 초기 인프라와 모니터링

- 초기 서버 설정과 Jenkins 웹훅·GitHub Actions 러너 설정 작업을 했다. [커밋 `5224a494`](https://github.com/woowacourse-teams/2026-gachi-gacha/commit/5224a494), [커밋 `04223e44`](https://github.com/woowacourse-teams/2026-gachi-gacha/commit/04223e44), [커밋 `be70110f`](https://github.com/woowacourse-teams/2026-gachi-gacha/commit/be70110f)
- Spring Boot Actuator와 Micrometer Prometheus를 연결하고 Docker Compose에 Node Exporter와 PostgreSQL Exporter를 추가해 애플리케이션·호스트·DB 지표를 수집할 수 있게 했다. 이후 대시보드·구성 수정은 팀원이 이어서 담당했다. [PR #74](https://github.com/woowacourse-teams/2026-gachi-gacha/pull/74), [커밋 `6ab9a477`](https://github.com/woowacourse-teams/2026-gachi-gacha/commit/6ab9a477)

### 관심 기반 교환글 추천 알림

- 관심 매장·가챠·교환글 등록과 비동기 관심 신호 처리를 구현했다. 새 교환글에서 가챠와 키워드를 추론하고, 최근 관심에 더 높은 점수를 주는 시간 감쇠 방식으로 알림 대상을 선정했다. 일일 조건형 푸시 알림 횟수 제한을 추가했다. [PR #639](https://github.com/woowacourse-teams/2026-gachi-gacha/pull/639), [커밋 `0c00f4bc`](https://github.com/woowacourse-teams/2026-gachi-gacha/commit/0c00f4bc)
- 관심 등록·해제 시 현재 상태를 기준으로 신호를 재구축하고, 같은 회원·게시글의 신호 작업이 겹치지 않도록 PostgreSQL advisory lock을 적용했다. 신호 재구축·시간 감쇠·동시성 테스트를 작성했다. 11월 19일 기준 PR은 열려 있다. [PR #639](https://github.com/woowacourse-teams/2026-gachi-gacha/pull/639), [커밋 `6cc8a232`](https://github.com/woowacourse-teams/2026-gachi-gacha/commit/6cc8a232), [커밋 `9285994a`](https://github.com/woowacourse-teams/2026-gachi-gacha/commit/9285994a), [커밋 `41a8be35`](https://github.com/woowacourse-teams/2026-gachi-gacha/commit/41a8be35)

### STOMP 채팅방 목록 갱신 성능 개선

- k6로 dev 서버에서 WebSocket 500개 연결을 확인했다. 로컬에서는 구간별 Timer와 STOMP 채널 큐 대기를 계측하고, 메시지 전송마다 반복되는 채팅방 목록 갱신 조회를 projection으로 통합했다. 코드 경로 기준 참여자당 조회가 7회에서 2회로 줄었고, 참여자 ID 조회까지 포함한 정상 1:1 채팅방 갱신 경로는 메시지당 15회에서 5회로 줄었다. [PR #731](https://github.com/woowacourse-teams/2026-gachi-gacha/pull/731), [채팅 성능 개선 실험 보고서](../02-trobleshooting/chat-performance-improvement.md)
- 로컬 275소켓에서 소켓당 초당 1건을 60초 동안 전송했다. 변경 직전 1회에서 p95는 80ms였고, 변경 후 3회에서 18ms, 66ms, 21ms였다. 변경 후 3회 모두 전달률 100%, STOMP 오류 0건이었다. 11월 19일 기준 PR은 열려 있으며, 측정 수치는 해당 로컬 조건에 한정된다. [PR #731](https://github.com/woowacourse-teams/2026-gachi-gacha/pull/731)

## 4. 프로젝트 기록과 추가 확인 항목

- 기존 프로젝트 기록에 따르면 초기 지도 탐색 요구사항을 기준으로 MySQL Spatial, MongoDB `2dsphere`, PostgreSQL/PostGIS를 k6로 비교했다. 저장소에서 PostGIS 쿼리와 인덱스 구현을 확인했으며, 세 엔진의 비교 조건과 결과는 원본 실험 자료를 확인해야 한다. 운영 범위가 홍대로 좁아지면서 반경 조회의 필요성이 줄었다는 내용도 기존 기록에 따른다.
- 홍대 현장 답사, Kakao Map API 호출 한도 검증과 캐싱 논의, Grafana 초기 대시보드, B2B 점주 아웃리치와 데모데이 발표는 기존 문서에 기록되어 있다. 개인 담당 범위나 성과 수치를 구체적으로 기재하려면 당시 자료 확인이 필요하다.
