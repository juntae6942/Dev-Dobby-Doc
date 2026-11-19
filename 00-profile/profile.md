---
category: profile
name: 박준태
role: Backend Software Engineer
contact:
  github: https://github.com/juntae6942
  blog: https://parkjunbackend.tistory.com
  linkedin: https://www.linkedin.com/in/juntae--park
keywords: [Java, Spring Boot, 백엔드아키텍처, 성능최적화, 데이터기반의사결정, 오픈소스기여, 분산시스템]
---

# 기본 프로필 및 엔지니어링 정체성

## 1. 기본 인적사항 및 학력/교육
* **이름:** 박준태
* **희망 직무:** 백엔드 엔지니어 (Backend / Server Developer)
* **학력:** 충남대학교 (Computer Vision 연구실 DIPLAB 학부연구생)
* **주요 교육 및 수료:**
    * 우아한테크코스 8기 백엔드 과정 수료
    * UCSD Qualcomm AI Camp 수료
* **인턴십:** 가온플랫폼 소프트웨어 엔지니어링 인턴 (2024) — 모놀리식 산업 공정 서비스의 마이크로서비스 아키텍처(MSA) 리팩토링

## 2. 한 줄 소개 (Elevator Pitch)
> **"추측이 아닌 데이터와 벤치마크로 증명하는 백엔드 엔지니어"**  
> 기술 선택과 아키텍처 설계 시 직관에 의존하지 않고, `k6` 부하 테스트와 모니터링 지표(Prometheus/Grafana) 등 실증적 근거를 바탕으로 최적의 해답을 찾아냅니다. 프레임워크의 내부 동작 원리를 깊이 파고들어 오픈소스 생태계에 직접 기여할 만큼 본질적인 문제 해결을 지향합니다.

## 3. 핵심 역량 (Core Competencies)
1. **실증적 성능 최적화 및 트래픽 대응 역량**
    * 데이터베이스(PostGIS GIST, MongoDB, Elasticsearch) 및 인프라 선택 시 동일 조건 부하 테스트(`k6`)와 JVM 힙/GC 모니터링을 통해 정량적 근거로 의사결정
2. **프레임워크 내부 동작 원리에 대한 깊은 이해와 오픈소스 기여**
    * Spring Framework, Swagger Core, Redis Lettuce 등 핵심 라이브러리의 내부 소스를 분석하여 버그를 수정하고 공식 리포지토리에 PR 머지
    * Tomcat/Coyote HTTP/1.1 및 Spring MVC 핵심 컴포넌트 직접 구현 경험 보유
3. **도메인 특화 데이터 처리 및 시스템 관측 가능성(Observability) 구축**
    * 공간 데이터(PostGIS), 한국어 형태소 검색(Elasticsearch `nori`), 실시간 채팅(MongoDB/STOMP) 등 도메인에 최적화된 저장소 설계 및 운영
    * Prometheus, Grafana, Exporter 기반의 메트릭 수집 파이프라인 구축으로 병목 사전 탐지

## 4. 기술 스택 (Tech Stack Matrix)
JD의 요구사항에 맞춰 강조할 스택만 골라 쓸 수 있도록 숙련도/활용 경험 기준으로 분류해 둡니다.

* **Language & Framework:** Java 21, Spring Boot (4.x / 3.x), Spring MVC, Spring Data JPA, QueryDSL
* **Database & Search:** PostgreSQL 17 (PostGIS), MySQL, MongoDB, Redis (Redisson, Lettuce), Elasticsearch (`nori`), Flyway
* **Infra & DevOps:** AWS (EC2), Docker, Docker Compose, Nginx, GitHub Actions (CI/CD), Linux (`systemctl`)
* **Monitoring & Testing:** Prometheus, Grafana (`node-exporter`, `postgres-exporter`), `k6` (부하 테스트), JUnit 5
* **Etc / Data:** Python, GeoPandas, LiveKit (WebRTC)

## 5. 자격증 및 주요 수상/학술 요약
*(상세 내용은 `03-opensource-research/` 참조)*
* **오픈소스 기여:** Spring Framework, Swagger Core, Spring AI, Redis Lettuce
* **자격증:** SQLD (2024), AWS Cloud Practitioner / Technical Essentials (2025)
* **수상:** 2025 국토교통부 서비스 아이디어 공모전 한국교통연구원장상(우수상), 2024 트렌디톤 최우수상, 제4회 KOPIS 빅데이터 공모전 진흥상 등
* **학술:** 한국통신학회(2026), 한국ITS학회(2025), 한국컴퓨터종합학술대회 KCC(2024) 논문 발표
