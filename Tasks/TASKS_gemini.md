# 개발 TASK 명세서 (SRS V1.0 기반)

본 태스크 리스트는 **[Task Type (수행 성격) ➔ Epic (도메인)]**의 계층 구조로 재분류되었습니다. 바이브코딩 파이프라인의 진행 순서와 일치하도록 구성하였으며, 앞서 누락되었던 **Phase 2 (정식 출시/고도화)** 항목들을 백로그 목록에 함께 식별하여 향후 마이그레이션 일정까지 조망할 수 있도록 반영했습니다.

---

## 1. 🏗️ Infra & Environment (개발 및 배포 환경)
**바이브코딩 핵심:** 로컬에서 발생할 수 있는 환경 차이의 원천 차단 및 CI/CD 구축.

| Task ID | Epic (도메인) | Feature (태스크명) | Phase (우선순위) | Dependencies |
|---|---|---|---|---|
| TSK-001 | 환경 구축 | [Infra] FastAPI, Streamlit, PostgreSQL, Redis 연결 로컬 Docker Compose 구성 | Phase 1 (Must) | None |
| TSK-002 | 환경 구축 | [Infra] Railway/Render 기반 CI/CD 자동 배포 파이프라인 연동 | Phase 1 (Must) | TSK-001 |
| TSK-034 | 인프라/NFR | [Infra] Datadog/AWS CloudWatch 기반 백엔드 APM 에러 로그 수집망 연동 | Phase 1 (Must) | TSK-002 |
| **TSK-035**| **Phase 2 (마이그레이션)**| [Infra] AWS ECS 인프라 및 RDS PostgreSQL 기반 배포 환경 마이그레이션 | **Phase 2 (Future)** | TSK-002 |

---

## 2. 🗄️ DB & Data (데이터베이스 스키마 및 마이그레이션)
**바이브코딩 핵심:** AI 에이전트가 로직을 구성하기 전에 참조할 단일 스키마 공급원(SSOT) 마련.

| Task ID | Epic (도메인) | Feature (태스크명) | Phase (우선순위) | Dependencies |
|---|---|---|---|---|
| TSK-003 | DB & Data | [DB] TENANT, SHOP 중심의 OAuth 및 인증 상태 관리 스키마 명세 (SQLModel) | Phase 1 (Must) | TSK-001 |
| TSK-004 | DB & Data | [DB] ORDER, PRODUCT, INVENTORY 핵심 물류 데이터 구조 작성 | Phase 1 (Must) | TSK-003 |
| TSK-005 | DB & Data | [DB] WEATHER_DATA, FORECAST, FORECAST_FACTOR 연계 예측 데이터 구조 작성 | Phase 1 (Must) | TSK-004 |
| TSK-006 | DB & Data | [DB] WORKFORCE_PLAN, AUDIT_LOG 로깅 및 역산 결과 구조 작성 | Phase 1 (Must) | TSK-005 |
| **TSK-036**| **Phase 2 (마이그레이션)**| [DB] 멀티테넌트 Row-Level 격리에서 Schema Isolation 논리적 격리로 구조 전환 | **Phase 2 (Future)** | TSK-003, TSK-011 |

---

## 3. 🔌 API & Mock (계약 규약 및 DTO 정의)
**바이브코딩 핵심:** 프론트와 백엔드 간 의존성을 분리하기 위한 통신 규약과 Mock 서버 마련.

| Task ID | Epic (도메인) | Feature (태스크명) | Phase (우선순위) | Dependencies |
|---|---|---|---|---|
| TSK-007 | 내부 통신 (Backend)| [API] 서버 내부 API (/forecast, /report 등) Request/Response DTO 정의 | Phase 1 (Must) | None |
| TSK-008 | 외부 통신 (Integration)| [API] 외부 연결 스펙(단기 데이터, 카페24, 알림톡) Adapter Interface 뼈대 작성 | Phase 1 (Must) | None |
| TSK-009 | 목업 데이터 (Mock) | [Mock] Streamlit 프론트 개발을 위한 임시 JSON 데이터 통신용 API 구성 | Phase 1 (Must) | TSK-007 |
| **TSK-037**| **Phase 2 (마이그레이션)**| [API] 중기예보 API 데이터 수집 규약(Contract) 및 Adapter DTO 생성 추가 | **Phase 2 (Future)** | TSK-008 |

---

## 4. ⚙️ Command (상태 변이 로직 / Write 및 외부 연동)
**바이브코딩 핵심:** 외부 사이드 이펙트를 발생하는 주요 비즈니스 액션 로직 구현의 집결처.

| Task ID | Epic (도메인) | Feature (태스크명) | Phase (우선순위) | Dependencies |
|---|---|---|---|---|
| TSK-010 | Common | [Command] JWT 발급 및 OAuth 인가 권한 공통 미들웨어 구현 | Phase 1 (Must) | TSK-003, TSK-007 |
| TSK-011 | Common | [Command] 멀티테넌트(Row-Level) 데이터 접근 제어 인젝터(Dependency) 적용 | Phase 1 (Must) | TSK-010 |
| TSK-013 | 연동 (F2) | [Command] 카페24 1-Click OAuth 토큰 발급, 갱신 및 상태 관리 로직 구현 | Phase 1 (Must) | TSK-003, TSK-008 |
| TSK-014 | 연동 (F2) | [Command] 스마트스토어 1-Click OAuth 인증 플로우 추가 연동 구현 | Phase 1.5 (Should)| TSK-013 |
| TSK-016 | 파이프라인 | [Command] APScheduler 기반 배치 시스템 및 PostgreSQL Jobstore 커넥션 영속화 | Phase 1 (Must) | TSK-001 |
| TSK-017 | 파이프라인 | [Command] 기상청 단기예보 및 DataLab 키워드 트렌드 ETL 수집 비즈니스 파이프라인 | Phase 1 (Must) | TSK-016, TSK-008 |
| TSK-018 | 파이프라인 | [Command] 카페24 통신 결과 API 수집 (Redis Rate Limit 대기열 시스템 구성 포함) | Phase 1 (Must) | TSK-013, TSK-016 |
| TSK-020 | 예측 (F1) | [Command] LightGBM 수요 엔진 통합 예측 파이프라인(추론 API / Confidence 검증 로직) | Phase 1 (Must) | TSK-005, TSK-007 |
| TSK-021 | 예측 (F1) | [Command] SHAP 기여도 산출 및 Gemini API 연동 통한 Top 3 자연어 요약 로직 생성 | Phase 1 (Must) | TSK-020 |
| TSK-024 | 리포트 (F1) | [Command] WeasyPrint 적용 HTML/CSS 바인딩 및 파일 변환 로직 적용 | Phase 1 (Must) | TSK-021 |
| TSK-025 | 리포트 (F1) | [Command] 생성된 PDF의 Supabase Storage 적재 및 Signed 다운로드 URL 추출 로직 | Phase 1 (Must) | TSK-024 |
| TSK-027 | 역산 (F3) | [Command] 출고량 기반 센터 인력수 산출(Capacity 기반) 및 프로모션 보정 적용 함수 | Phase 1 (Must) | TSK-020 |
| TSK-028 | 역산 (F3) | [Command] 카카오 알림톡 API 발송 연동 트리거 및 16시 정규 자동 발송 스케줄링 적용 | Phase 1 (Must) | TSK-027, TSK-016 |
| TSK-029 | 역산 (F3) | [Command] 카카오 시스템 응답 지연/에러 시 즉각 SMS Fallback 발송 플로우 구현 | Phase 1 (Must) | TSK-028 |
| TSK-033 | 인프라/NFR | [Command] 액션/상태별 AUDIT_LOG 테이블 비동기 삽입을 위한 공통 데코레이터 적용 | Phase 1 (Must) | TSK-006 |
| **TSK-038**| **Phase 2 (마이그레이션)**| [Command] 예측 파이프라인을 LightGBM 단일에서 TFT/N-BEATS 앙상블로 업그레이드 교체 | **Phase 2 (Future)** | TSK-020 |
| **TSK-039**| **Phase 2 (마이그레이션)**| [Command] APScheduler 기반 배치를 Apache Airflow 워크플로우로 전환 마이그레이션 | **Phase 2 (Future)** | TSK-016 |
| **TSK-040**| **Phase 2 (마이그레이션)**| [Command] PDF 생성 모듈을 WeasyPrint에서 Puppeteer 기반 렌더러로 교체 | **Phase 2 (Future)** | TSK-024 |

---

## 5. 🔍 Query (상태 읽기 및 조회 로직)
**바이브코딩 핵심:** DB 데이터의 단순 참조 및 필터링을 목적으로 하는 조회 기능 관리.

| Task ID | Epic (도메인) | Feature (태스크명) | Phase (우선순위) | Dependencies |
|---|---|---|---|---|
| TSK-022 | 예측 (F1) | [Query] 결측 또는 API 오류로 인한 24시간 캐시 폴백 적용 상태 조회 및 엠블럼 플래그 반환 | Phase 1 (Must) | TSK-020 |
| TSK-031 | 대시보드 | [Query] 페이지 렌더링에 요구되는 KPI, 매출 통계, 1인당 인원 집계 쿼리 구현 | Phase 1 (Must) | TSK-017, TSK-027 |

---

## 6. 🎨 Feature (프론트/UI/화면 구성)
**바이브코딩 핵심:** API 구조나 데이터 상관없이 '화면 계층의 렌더링' 목적에만 집중된 태스크 할당.

| Task ID | Epic (도메인) | Feature (태스크명) | Phase (우선순위) | Dependencies |
|---|---|---|---|---|
| TSK-032 | 대시보드 | [Feature] Streamlit 메인 대시보드 (XAI 차트, 경고 배너, 인원 확인 표 등) 컴포넌트 렌더링 | Phase 1 (Must) | TSK-009, TSK-031 |
| **TSK-041**| **Phase 2 (마이그레이션)**| [Feature] Streamlit 대시보드를 React + Recharts 기반 독립 자바스크립트 프로젝트로 분할 | **Phase 2 (Future)** | TSK-032 |

---

## 7. 🧪 Test (사용자/시스템 인수 조건 검증)
**바이브코딩 핵심:** 각 AC(인수 조건) 및 NFR(비기능 요구사항)을 AI 자동 수정 루프의 종착점으로 안내하기 위한 TC.

| Task ID | Epic (도메인) | Feature (태스크명) | Phase (우선순위) | Dependencies |
|---|---|---|---|---|
| TSK-012 | Common | [Test] 로그인/인가 검증 실패 시 조치로그 및 Row-Level 기반 테넌트 간 교차 노출 차단 단위 검증 | Phase 1 (Must) | TSK-011 |
| TSK-015 | 연동 (F2) | [Test] 카페24/스마트스토어 토큰 갱신 시나리오 및 만료로 인한 401 예외 처리 복구 플로우 점검 | Phase 1 (Must) | TSK-013 |
| TSK-019 | 파이프라인 | [Test] 기상/화주 API 통신 타임아웃 강제 3회 유발 시나리오에 응답하는 자동 Retry 시퀀스 오류 검사 | Phase 1 (Must) | TSK-017, TSK-018|
| TSK-023 | 예측 (F1) | [Test] 추론된 신뢰도 70% 이하 기준 시 대시보드 출력 상태/플래그 동작 단위 검증(AC 1.3 결함 방지) | Phase 1 (Must) | TSK-022 |
| TSK-026 | 리포트 (F1) | [Test] 가공된 PDF 파일 출력 시 p95 로드 한계 목표 (≤20초) 통과 성능 측정 테스트 자동화 | Phase 1 (Must) | TSK-025 |
| TSK-030 | 역산 (F3) | [Test] 알림톡 Mocking 통신 강제 실패유발 후 SMS 폴백 전환기기가 1분 내 작동하는지 확인하는 통합 테스트 | Phase 1 (Must) | TSK-029 |
