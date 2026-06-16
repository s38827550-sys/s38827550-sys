# Hi, I'm TaeGeuk Kim 👋

**Python 기반 데이터 파이프라인 · 백엔드 시스템을 설계하는 개발자입니다.**

설계 의사결정과 그 근거를 남기는 것을 중요하게 생각합니다.  
"왜 이 방법인가"를 항상 먼저 질문합니다.

[![Mail Badge](https://img.shields.io/badge/-Gmail-c14438?style=flat&logo=Gmail&logoColor=white)](mailto:s38827550@gmail.com)
[![GitHub followers](https://img.shields.io/github/followers/s38827550-sys?color=27da6b&logo=Handshake)](https://github.com/s38827550-sys?tab=followers)

---

## Tech Stack

**Languages & Frameworks**

[![Python](https://img.shields.io/badge/Python-3776AB?style=flat&logo=python&logoColor=white)](https://www.python.org)
[![FastAPI](https://img.shields.io/badge/FastAPI-009688?style=flat&logo=fastapi&logoColor=white)](https://fastapi.tiangolo.com)

**Database**

[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-336791?style=flat&logo=postgresql&logoColor=white)](https://www.postgresql.org)
[![SQLite](https://img.shields.io/badge/SQLite-003B57?style=flat&logo=sqlite&logoColor=white)](https://www.sqlite.org)

**Infra & Tools**

[![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat&logo=docker&logoColor=white)](https://www.docker.com)
[![GitHub Actions](https://img.shields.io/badge/GitHub_Actions-2088FF?style=flat&logo=githubactions&logoColor=white)](https://github.com/features/actions)

---

## Projects

---

### 🔄 공공데이터 ETL 파이프라인 (서울시 대기질)

> FastAPI · APScheduler · PostgreSQL · Docker | Render + Supabase 배포 · 개인 프로젝트

[![Repo](https://img.shields.io/badge/Repository-181717?style=flat&logo=github)](https://github.com/s38827550-sys/Mini-Guestbook-Data-Pipeline)

**Problem**

Render 무료 플랜은 서버가 비정기적으로 재시작  
매시 정각 수집 구조에서 재시작 발생 시 APScheduler Job이 중복 등록되고,  
동일 시각 데이터가 2건 이상 삽입되어 일별 통계가 오염되는 문제가 있었음.

**Analyze**

| 선택지 | 문제점 |
|---|---|
| DB 레벨 중복 방지만 적용 | Job 자체는 N개 실행 → 외부 API 호출 N배, 부하 증가 |
| 스케줄러 레벨만 적용 | 동시 요청 Race Condition 시 DB 중복 가능성 잔존 |
| **두 레벨 동시 적용** | Job 실행 자체를 1회로 보장 + DB 레벨에서 최후 방어 |

Connection Pool도 같은 맥락으로 검토했음.  
`ThreadedConnectionPool`은 스레드 경합 비용이 있고, Render 단일 인스턴스에서는 과도한 복잡성이라 판단해 `SimpleConnectionPool`을 선택했음.

**Action**

- APScheduler `replace_existing=True`로 재시작 시 Job 중복 등록 차단
- PostgreSQL `ON CONFLICT (station_name, measure_time) DO UPDATE`로 멱등성 보장
- Supabase Connection Pooler(port 6543) 전환으로 IPv6 비호환 문제 해결
- `AT TIME ZONE 'Asia/Seoul'` 적용으로 UTC 저장 → KST 집계 시 날짜 경계 오류 수정

**Result**

<!-- ✏️ TODO: DBeaver에서 아래 쿼리 실행 후 실제 수치로 교체 -->
<!-- SELECT COUNT(*), MIN(measure_time), MAX(measure_time) FROM air_quality; -->
- 운영 **7일간 4400건** 수집, 중복 삽입 **0건** 확인 (ON CONFLICT 트리거 횟수 대비)
- UptimeRobot 기준 가동률 **100%**
- `/health` 응답시간 평균 **[N]ms**

---

### 🚦 AI 기반 스마트 횡단보도 위험도 분석 시스템

> YOLOv11 · PostgreSQL · Docker · GitHub Actions CI | 팀 프로젝트 (3인) · **DB 설계 및 파이프라인 담당**

![CI](https://github.com/s38827550-sys/smart-crosswalk-risk-analyzer/actions/workflows/ci.yml/badge.svg)
[![Repo](https://img.shields.io/badge/Repository-181717?style=flat&logo=github)](https://github.com/s38827550-sys/smart-crosswalk-risk-analyzer)

**Problem**

YOLOv11은 30fps로 추론함.  
모든 프레임의 위험도를 저장하면 1시간 기준 **약 108,000건**이 누적됨.  
단일 테이블로 관리하면 집계 쿼리가 Full Scan에 의존해 선형적으로 느려지고,  
실시간 알림·이력 조회·통계 분석이 서로 다른 쿼리 패턴을 요구한다는 점도 문제였음.

**Analyze**

| 전략 | 장점 | 단점 |
|---|---|---|
| 단일 테이블 | 구현 단순 | 집계 시 Full Scan, 쿼리 패턴 혼재 |
| **3단계 분리 (`risk_log` / `risk_summary` / `risk_event`)** | 쿼리 패턴별 최적화, 인덱스 분리 가능 | 스토리지 소폭 증가, 삽입 로직 복잡 |

데이터 특성을 보면 위험도 점수는 연속적으로 변하지 않음.  
±10 이상 변화가 발생할 때만 `risk_log`에 기록하는 방식으로 볼륨을 줄일 수 있다고 판단함.

**Action**

- `risk_log`: 위험도 점수 변화(±10 이상)가 있을 때만 삽입 → 불필요한 쓰기 차단
- `risk_summary`: 1초 단위 집계 레코드 → 통계 조회용
- `risk_event`: 임계치 초과 이벤트만 저장 → 알림·리포트용
- 모듈 import 시점 환경변수 평가 → `connect()` 호출 시점으로 지연 초기화 (테스트 환경 TypeError 해결)
- `ObjectTracker` 클래스 캡슐화로 전역 상태 제거 → 단위 테스트 43개 작성 가능하게 됨

**Result**

- 30fps 기준 시간당 108,000건 → 저장 대상 약 **3,600건**으로 **97% 볼륨 감소**
- 단위 테스트 **43개** · GitHub Actions CI 통과율 **100%** · 현재 커버리지 27% (개선 중)

---

### 🐸 웹 게임 플랫폼 — Frog Jump

> FastAPI · PostgreSQL · JWT · Pygame · Vercel 배포 | 개인 프로젝트

[![Game](https://img.shields.io/badge/Game_Client-181717?style=flat&logo=github)](https://github.com/s38827550-sys/FrogJumpGame)
[![Leaderboard API](https://img.shields.io/badge/Leaderboard_API-181717?style=flat&logo=github)](https://github.com/s38827550-sys/frogjump-leaderboard-remake)
[![Live](https://img.shields.io/badge/Live_Site-009688?style=flat)](https://frogjump-web.vercel.app/)

**Problem (클라이언트)**

서버 HTTP 요청이 Pygame 게임 루프(60fps 틱)를 블로킹해 입력 지연이 발생  
asyncio는 Pygame 이벤트 루프와 충돌해 적용 불가

**Action (클라이언트)**

Thread를 분리해 게임 루프와 네트워크 I/O를 독립 실행  
공유 점수 변수는 GIL 보호 단순 타입으로 한정해 Race Condition을 회피  
→ 동기/비동기 경계와 스레드 안전성을 직접 체감한 계기가 됨.

**Problem (API 서버 v1 → v2 마이그레이션)**

SQLite 단일 파일 DB 사용 시 다중 접속에서 write lock 충돌,  
동일 유저 점수 중복 저장 문제가 있었음.

**Analyze (API 서버)**

| 인증 방식 | 이유 |
|---|---|
| 세션 방식 | 서버 메모리에 상태 저장 → 단일 인스턴스 의존 |
| **JWT (선택)** | stateless → 수평 확장 가능, 서버 재시작 후에도 토큰 유효 |

**Result (API 서버)**

- SQLite → PostgreSQL 마이그레이션으로 write lock 충돌 제거
- JWT 기반 stateless 인증 구현

<!-- ✏️ TODO: locust로 부하테스트 후 응답시간 추가 -->
<!-- pip install locust → /scores/ranking 100 동시 요청 기준 평균 응답시간 -->

---

## 🏆 Certificate

| 자격증 | 취득일 |
|---|---|
| SQLD (SQL 개발자) | 2026. 03. 27 |
