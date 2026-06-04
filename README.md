# Hi, I'm TaeGeuk Kim 👋

> 데이터가 흐르는 시스템을 설계합니다.

2025년 12월에 개발을 시작하였습니다.  
처음에는 게임 하나를 만들어보려 했으나, 어느새 서버를 설계하고 있었고,  
그 다음에는 웹 플랫폼을 직접 배포하고 운영하고 있었습니다.  
이후 데이터베이스에 관심을 갖게 되어 SQLD를 취득하고,  
현재는 **데이터 엔지니어링**을 목표로 학습과 프로젝트를 이어가고 있습니다.

---

## About Me

[![Visitor Badge](https://visitor-badge.laobi.icu/badge?page_id=s38827550-sys.s38827550-sys)](https://github.com/s38827550-sys)
[![Mail Badge](https://img.shields.io/badge/-Gmail-c14438?style=flat&logo=Gmail&logoColor=white)](mailto:s38827550@gmail.com)
[![GitHub followers](https://img.shields.io/github/followers/s38827550-sys?color=27da6b&logo=Handshake)](https://github.com/s38827550-sys?tab=followers)

- 🔭 신입 **데이터 엔지니어 / 백엔드 개발자** 취업 준비 중
- 🌱 현재 학습 중: Apache Airflow · Kafka
- 🏆 SQLD 취득 (2026.03)
- 💬 관심 분야: Data Pipeline · ETL · AI 연동 시스템

---

## Tech Stack

**Languages & Frameworks**

[![Python](https://img.shields.io/badge/Python-3776AB?style=flat&logo=python&logoColor=white)](https://www.python.org)
[![FastAPI](https://img.shields.io/badge/FastAPI-009688?style=flat&logo=fastapi&logoColor=white)](https://fastapi.tiangolo.com)
[![Uvicorn](https://img.shields.io/badge/Uvicorn-499848?style=flat&logo=gunicorn&logoColor=white)](https://www.uvicorn.org)
[![Pygame](https://img.shields.io/badge/Pygame-3776AB?style=flat&logo=python&logoColor=white)](https://www.pygame.org)
[![React](https://img.shields.io/badge/React-61DAFB?style=flat&logo=react&logoColor=black)](https://react.dev)
[![React Router](https://img.shields.io/badge/React_Router-CA4245?style=flat&logo=reactrouter&logoColor=white)](https://reactrouter.com)
[![HTML](https://img.shields.io/badge/HTML-E34F26?style=flat&logo=html5&logoColor=white)](https://developer.mozilla.org/en-US/docs/Web/HTML)
[![CSS](https://img.shields.io/badge/CSS-1572B6?style=flat&logo=css3&logoColor=white)](https://developer.mozilla.org/en-US/docs/Web/CSS)
[![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?style=flat&logo=javascript&logoColor=black)](https://developer.mozilla.org/en-US/docs/Web/JavaScript)

**AI / Vision**

[![YOLOv8](https://img.shields.io/badge/YOLOv8-Ultralytics-8A2BE2?style=flat)](https://github.com/ultralytics/ultralytics)
[![OpenCV](https://img.shields.io/badge/OpenCV-5C3EE8?style=flat&logo=opencv&logoColor=white)](https://opencv.org)

**Database**

[![SQLite](https://img.shields.io/badge/SQLite-003B57?style=flat&logo=sqlite&logoColor=white)](https://www.sqlite.org)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-336791?style=flat&logo=postgresql&logoColor=white)](https://www.postgresql.org)

**Platforms & Tools**

[![Git](https://img.shields.io/badge/Git-F05032?style=flat&logo=git&logoColor=white)](https://git-scm.com)
[![GitHub](https://img.shields.io/badge/GitHub-181717?style=flat&logo=github&logoColor=white)](https://github.com)
[![GitHub Actions](https://img.shields.io/badge/GitHub_Actions-2088FF?style=flat&logo=githubactions&logoColor=white)](https://github.com/features/actions)
[![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat&logo=docker&logoColor=white)](https://www.docker.com)
[![Render](https://img.shields.io/badge/Render-000000?style=flat&logo=render&logoColor=white)](https://render.com)
[![Vercel](https://img.shields.io/badge/Vercel-000000?style=flat&logo=vercel&logoColor=white)](https://vercel.com)
[![Supabase](https://img.shields.io/badge/Supabase-3ECF8E?style=flat&logo=supabase&logoColor=white)](https://supabase.com)

---

## 🚦 AI 기반 스마트 횡단보도 위험도 분석 시스템

> YOLOv8 기반 실시간 차량-보행자 위험도 분석 파이프라인 · **팀 프로젝트 (3인)**

![CI](https://github.com/s38827550-sys/smart-crosswalk-risk-analyzer/actions/workflows/ci.yml/badge.svg)

횡단보도 영상을 실시간으로 분석하여 차량·보행자 간 위험도를 정량적 점수로 산출하고,  
PostgreSQL에 저장하는 End-to-End 데이터 파이프라인을 구축하였습니다.

**핵심 구현 내용**

- 🔍 YOLOv8 객체 탐지 → ROI 위험영역 필터링 → Object Tracking → 위험도 점수 산출
- 🗄️ `risk_log` / `risk_summary` / `risk_event` 3단계 PostgreSQL 저장 전략 설계 **(본인 담당)**
- ✅ 단위 테스트 43개 작성 · GitHub Actions CI 자동화 구축

**개발하면서 해결한 문제들**

- 🐛 모듈 import 시점에 환경변수를 평가하면 테스트 환경에서 TypeError 발생 → 지연 초기화(connect() 호출 시점)로 해결
- 🐛 전역 딕셔너리로 관리하던 트래킹 상태를 `ObjectTracker` 클래스로 캡슐화하여 테스트 가능성 확보

[![Repo](https://img.shields.io/badge/🚦_Repository-181717?style=flat&logo=github)](https://github.com/s38827550-sys/smart-crosswalk-risk-analyzer)

---

## 🔄 공공데이터 ETL 파이프라인

> Python · PostgreSQL · Docker 기반 데이터 수집·적재 파이프라인

서울시 실시간 대기질 현황 데이터를 수집하여 정제·검증 후 PostgreSQL에 적재하는  
End-to-End ETL 파이프라인을 구축하였습니다.  
Docker · docker-compose로 실행 환경을 컨테이너화하여 재현 가능한 환경을 구성하였습니다.

[![Repo](https://img.shields.io/badge/🔄_Repository-181717?style=flat&logo=github)](https://github.com/s38827550-sys/Mini-Guestbook-Data-Pipeline)

---

## 🐸 웹 게임 플랫폼 — Frog Jump

> Python을 학습하기 위해 게임을 제작하기 시작하였으나, 점수 저장이 필요해지면서 서버를 설계하게 되었습니다.

서버를 구축하다가 랭킹 페이지가 필요해졌고,  
최종적으로 회원가입·로그인·게시판·포인트 시스템을 갖춘 **공식 웹 플랫폼**을 직접 설계하고 배포하였습니다.

**주요 기능**

- 🎨 메달 시스템이 포함된 랭킹 보드
- ⏱️ 시간 10초 이하 시 HUD 애니메이션
- 🧩 `core/` 패키지로 모듈화

**개발하면서 해결한 문제들**

- 🐛 서버 요청이 게임 루프를 차단하여 입력 지연 발생 → Threading으로 해결, 동기/비동기 개념을 실감하게 된 계기
- 🐛 클라이언트 점수와 서버 저장값 불일치 → 데이터 처리 순서를 명시적으로 보장하여 해결

[![Game](https://img.shields.io/badge/🎮_Game_Client-181717?style=flat&logo=github)](https://github.com/s38827550-sys/FrogJumpGame)
[![Web Platform](https://img.shields.io/badge/🚀_Web_Platform-181717?style=flat&logo=github)](https://github.com/s38827550-sys/frogjump-web)
[![Leaderboard](https://img.shields.io/badge/🏆_Leaderboard-181717?style=flat&logo=github)](https://github.com/s38827550-sys/frogjump-leaderboard-remake)
[![Live](https://img.shields.io/badge/🔴_Live_Site-009688?style=flat)](https://frogjump-web.vercel.app/)

---

## 🏆 Certificate

| 자격증 | 취득일 |
|---|---|
| SQLD (SQL 개발자) | 2026. 03. 27 |

---

## 📊 Stats

[![GitHub stats](https://github-readme-stats.vercel.app/api?username=s38827550-sys&hide_title=false&hide_border=true&show_icons=true&include_all_commits=true&line_height=20&theme=default)](https://github.com/s38827550-sys)

[![GitHub streak](https://github-readme-streak-stats.herokuapp.com/?user=s38827550-sys&hide_border=true&theme=default)](https://github.com/s38827550-sys)

[![Top Langs](https://github-readme-stats.vercel.app/api/top-langs/?username=s38827550-sys&layout=compact&hide_border=true&theme=default)](https://github.com/s38827550-sys)

---

## ⏱ This Week I Spent My Time On

<!--START_SECTION:waka-->
<!--END_SECTION:waka-->
