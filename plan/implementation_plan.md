# TASK 추출 프롬프트 검토 및 TASK 리스트 생성

## 1. 프롬프트 검토 결과

SRS V1.0 문서의 구조와 내용을 기반으로, 제시된 TASK 추출 프롬프트를 **5가지 요건**에 대해 검토합니다.

---

### 요건 1: SRS 계획 전체를 커버할 것 — ⚠️ 부분 미흡

**양호한 점:**
- Step 1~4의 4단계 프로세싱 순서가 Data → Logic → Test → NFR로 체계적
- CQRS 패턴을 적용한 Read/Write 분리 전략이 적절
- AC → Test Task 변환 전략이 우수

**보완 필요:**

| 누락 영역 | SRS 해당 섹션 | 설명 |
|---|---|---|
| **Sprint 0 / 개발환경 부트스트랩** | §1.5.3 C-TEC-013, 014 | Docker + docker-compose 환경 표준화, AI context injection 방법론 등 개발 착수 전 인프라 Task 미언급 |
| **외부 시스템 어댑터 계층** | §3.1 EXT-01~07, §1.5.3 C-TEC 전체 | 기상청/카페24/네이버/카카오 각 外部 API 어댑터 구현이 독립 Task로 분리되어야 함 |
| **Fallback 전략 구현** | §3.1.1 전체 | 각 외부 시스템 장애 시 우회 전략이 6개 명시되어 있으나, Step 2에서 자동으로 포착되기 어려움 |
| **모니터링 & 알림 인프라** | §4.2.5 REQ-NF-021~024 | CloudWatch/Datadog 연동, Airflow DAG 모니터링, 모델 드리프트 감지 등 |
| **Validation Plan / 실험 인프라** | §6.4 EXP-01, EXP-02 | 파일럿 PoC 실험 설계 및 데이터 수집 파이프라인 Task |
| **보안 인프라** | §4.2.3 REQ-NF-015~019 | TLS 1.3, AES-256, RBAC 설정 등 보안 전용 Task |

> [!IMPORTANT]
> 프롬프트의 Step 1~4가 **기능 개발 중심**으로 구성되어 있어, **인프라/DevOps/보안/모니터링** 영역에 대한 별도 Step이 부재합니다. `Step 0: 개발 환경 및 인프라 부트스트랩` 또는 `Step 5: 운영/모니터링 Task 추출`을 추가할 것을 권장합니다.

---

### 요건 2: SRS를 넘어서는 임의 내용이 없을 것 — ✅ 양호

- Constraints 섹션에 "SRS 문서에 명시되지 않은 기능을 임의로 상상하여 추가하지 마십시오"가 명확히 기술됨
- 다만, 예시(이메일 기반 회원가입, JWT 로그인 등)가 **본 SRS의 인증 체계(OAuth 2.0 + JWT + RBAC)**와 다소 괴리 → 예시를 SRS 맥락에 맞게 교체하면 더 정확

---

### 요건 3: SRS에서 추구하는 서술 방침을 따를 것 — ⚠️ 부분 미흡

**보완 필요:**

| 항목 | 현재 | 권장 |
|---|---|---|
| **REQ ID 참조** | 관련 SRS 섹션(예: "2.1.1 회원 관리") | SRS의 `REQ-FUNC-xxx`, `REQ-NF-xxx` ID를 직접 매핑 |
| **기능 명명** | 일반적 도메인명 | SRS의 F1/F2/F3 피처 구분을 Epic 단위에 반영 |
| **기술 스택 참조** | 미언급 | C-TEC-xxx 제약사항을 Task 설명에 포함 |
| **PRD 추적** | 미언급 | Traceability Matrix(§5)와의 교차 확인 기준 명시 |

---

### 요건 4: 깃허브 프로젝트로 관리될 수 있는 포맷 — ⚠️ 개선 가능

**현재 출력 포맷:**
```
| Task ID | Epic | Feature | 관련 SRS 섹션 | Dependencies | 복잡도 |
```

**권장 확장 포맷:**
```
| Task ID | Phase | Epic | Type | Feature | 관련 REQ ID | Dependencies | 우선순위 | 복잡도 |
```

추가 권장 컬럼:
- **Phase**: PRD 롤아웃 로드맵(Phase 1/2/3)과 매핑
- **Type**: `[DB]` / `[API]` / `[Mock]` / `[Feature/Query]` / `[Feature/Command]` / `[Test]` / `[Infra]` / `[Sec]` / `[UI]` / `[Monitor]`
- **우선순위**: MoSCoW 우선순위 반영 (Must / Should / Could)
- **관련 REQ ID**: SRS의 REQ-FUNC/REQ-NF ID 직접 매핑

---

### 요건 5: 순차적-병렬적 계획의 근거자료로 충분할 것 — ⚠️ 보완 필요

**양호한 점:**
- Dependencies 컬럼으로 선행 관계 추적 가능

**보완 필요:**
- **Phase/Sprint 배정 컬럼** 추가 → 시간축 기반 그룹핑
- **병렬 실행 그룹** 식별 → 동시 착수 가능 Task 명시
- **크리티컬 패스** 표기 → 전체 일정의 제약 경로 식별
- SRS §1.5.2 CON-04 `F3 → F2 선행 완료 필수` 같은 핵심 의존성이 자동 포착되는 가이드라인 부재

---

## 2. 종합 평가

| 요건 | 평가 | 비고 |
|---|---|---|
| 1. SRS 전체 커버 | ⚠️ 70% | 인프라/모니터링/보안/Fallback/실험 영역 Step 보강 필요 |
| 2. 임의 내용 배제 | ✅ 95% | 제약 조건 명확. 예시만 SRS 맥락으로 교체 권장 |
| 3. SRS 서술 방침 | ⚠️ 60% | REQ ID, C-TEC, F1/F2/F3 명명 체계 미반영 |
| 4. GitHub 포맷 | ⚠️ 75% | Phase, Type, 우선순위 컬럼 추가 권장 |
| 5. 순차적·병렬적 계획 | ⚠️ 65% | Phase/Sprint 배정, 병렬 그룹, 크리티컬 패스 보강 필요 |

> [!TIP]
> 프롬프트의 **핵심 전략(Data-first, CQRS, AC→Test 변환)**은 매우 우수합니다. 위 보완 사항을 반영하면 본 SRS에 최적화된 완성도 높은 TASK 리스트 생성이 가능합니다.

---

## 3. 실행 계획

위 검토 결과를 **반영하여** 다음 순서로 TASK 리스트를 추출합니다:

1. **Step 0**: 개발 환경 및 인프라 부트스트랩 (C-TEC 기반)
2. **Step 1**: 데이터 스키마 + API 계약 + Mock (§6.1, §6.2 기반)
3. **Step 2**: 기능별 Read/Write 분리 (§4.1 REQ-FUNC 기반)
4. **Step 3**: AC → 테스트 Task 변환 (각 REQ의 AC 기반)
5. **Step 4**: NFR/보안/모니터링/Fallback 인프라 (§4.2 REQ-NF 기반)
6. **Step 5**: 의존성 매핑 + Phase 배정 + 크리티컬 패스 식별

**출력 포맷** (확장):
```
| Task ID | Phase | Epic | Type | Feature | 관련 REQ ID | Dependencies | 우선순위 | 복잡도 |
```

> [!IMPORTANT]
> 프롬프트 검토 결과 보완 사항을 반영하여 TASK 추출을 진행합니다. 원본 프롬프트를 그대로 사용할지, 보완 반영 버전으로 진행할지 확인 부탁드립니다.

## Open Questions

1. **TASK 추출 시 보완 사항 반영 여부**: 위에서 식별한 5가지 보완 사항(인프라 Step 추가, REQ ID 매핑, Phase 컬럼 등)을 모두 반영하여 진행할까요, 아니면 원본 프롬프트 그대로 진행할까요?
2. **Phase 기준**: PRD §8-1의 3-Phase 로드맵(Phase 1: 데이터 파이프라인 4주, Phase 2: 코어 엔진 4주, Phase 3: UI+PoC 4주)을 TASK의 Phase 배정 기준으로 사용해도 될까요?
3. **기존 TASKS_gemini.md**: Tasks 폴더에 이미 존재하는 `TASKS_gemini.md`와의 관계는? 별도 파일로 생성할까요?
