# MeetRank — Group Preference-Aware Venue Recommendation

> **모임 참여자의 취향 투표와 eat.pl·BC카드 기반 장소 데이터를 결합해, 알레르기 위험과 다수 선호를 반영하는 설명 가능한 장소 추천 서비스를 개발·통합했습니다.**

`Python` `SQLite` `JavaScript` `K-Skill / eat.pl` `Rule-Based Ranking` `GitHub Actions` `AWS Lightsail` `Vercel`

**팀 프로젝트 (4인)** · 사용자용 서비스명: **지금어디가**  
[서비스 데모](https://jj2ya.vercel.app/) · [팀 소스 코드](https://github.com/BDAI9/2026-project-demo) · [시스템 아키텍처](https://github.com/BDAI9/2026-project-demo/blob/main/docs/architecture.md) · [추천 알고리즘](https://github.com/BDAI9/2026-project-demo/blob/main/docs/recommendation.md)

## 프로젝트 개요

| 구분 | 내용 |
| --- | --- |
| **개발 배경** | 여러 사람이 식사나 모임 장소를 정할 때 음식 취향·예산·분위기·알레르기 조건이 충돌해 메시지로 조율하는 과정이 반복됩니다. |
| **해결 과제** | 회원가입 없이 링크로 개별 조건을 수집하고, 그룹 합의에 가까운 우선순위와 추천 근거를 함께 보여주는 서비스가 필요했습니다. |
| **담당 업무** | 팀 GitHub 통합·PR 관리, Vercel/AWS 연동 검증, 그룹 음식 득표 우선 랭커 개선, 5/6·6/6 자동 갱신 UI 안정화, 회귀 테스트 및 배포 문서 정리를 수행했습니다. |
| **기능 범위** | 지역/업종 선택 → 모임방 생성·초대 → 구성원 취향 수집 → 집계 → 먹고 싶은 음식 우선 장소 추천 → eat.pl 상세정보 |
| **핵심 산출물** | [그룹 선호 기반 랭커](https://github.com/BDAI9/2026-project-demo/blob/main/moim/recommend.py), [모임 API](https://github.com/BDAI9/2026-project-demo/blob/main/moim/server.py), [6단계 UI](https://github.com/BDAI9/2026-project-demo/blob/main/web/v2.js), [CI 테스트](https://github.com/BDAI9/2026-project-demo/blob/main/.github/workflows/verify-integration.yml) |
| **검증 근거** | [PR #30](https://github.com/BDAI9/2026-project-demo/pull/30)에서 **Python 테스트 248개 통과** 및 Playwright UI 스모크 테스트 성공. 이 값은 테스트 개수이지 추천 정확도나 사용자 만족도 점수가 아닙니다. |

## 1. 개발 배경 및 문제 정의

모임 장소를 정할 때는 참석자의 희망 음식과 예산이 제각각입니다. 단순히 매출이 높은 가게나 점수 합계가 높은 가게를 보여주면 **실제 다수의 음식 선호를 무시**할 수 있습니다.

이 프로젝트의 질문은 다음 세 가지였습니다.

1. 각 참여자의 조건을 링크 기반으로 어떻게 구조화해 모을 것인가?
2. 중식 2명·한식 1명처럼 다수 의견이 뚜렷할 때 어떻게 우선순위에 반영할 것인가?
3. 정보가 불완전한 결제 데이터와 지연되는 AI 보강 결과를 사용자에게 어떻게 정직하고 안정적으로 보여줄 것인가?

새로운 머신러닝 모델을 학습하기보다, **그룹 선호를 반영하는 규칙 기반 랭킹과 실서비스 통합**에 초점을 맞췄습니다.

## 2. 데이터 및 시스템 흐름

```text
[사용자 A/B/C: 음식·예산·분위기·알레르기]
          |
     모임 초대 링크
          |
  Python API + SQLite
          |
   그룹별 선택 수 집계
          |
 K-Skill → eat.pl 후보 조회
          |
 업종 규칙·알레르기 위험 확인
          |
 음식 종류 득표 우선 정렬
          |
 예산·분위기·방문 특성 세부 점수
          |
  추천 후보 + 근거 + 상세 링크
          |
 (선택) 상세 메뉴/후기 및 OpenRouter 추정 → 부분 갱신
```

운영 화면은 Vercel, Python 백엔드는 AWS Lightsail에 분리되어 있습니다. 백엔드는 상태 저장에 SQLite를 사용하고, 일부 민감한 사용자 조건이 외부 AI 모델로 전달되지 않도록 규칙 기반 위험 판단을 유지합니다.

## 3. 담당 업무 및 기여 근거

본 프로젝트는 팀 개발입니다. 아래는 **내 계정으로 관리·수정한 PR을 중심으로** 역할을 구분한 기록입니다. 초기 모임 API와 장소 규칙을 비롯한 팀원들의 작업을 모두 단독 구현했다고 주장하지 않습니다.

| 담당 영역 | 구체적인 기여 | 근거 |
| --- | --- | --- |
| **통합 및 배포 검증** | 팀 작업 통합, Vercel–AWS 연결·읽기 전용 점검, 운영 릴리스 절차 정리 | [PR #14](https://github.com/BDAI9/2026-project-demo/pull/14), [#20](https://github.com/BDAI9/2026-project-demo/pull/20), [#28](https://github.com/BDAI9/2026-project-demo/pull/28) |
| **그룹 랭킹 개선** | 음식 종류의 투표 수를 먼저 비교하고 동률에서 예산·분위기·결제 신호를 적용하도록 정렬 수정 | [PR #30](https://github.com/BDAI9/2026-project-demo/pull/30) |
| **화면 갱신 안정화** | 같은 방·추천 응답에 대한 DOM 전체 교체를 줄이고, 추천 결과에서 변경 영역만 갱신 | [PR #30](https://github.com/BDAI9/2026-project-demo/pull/30) |
| **UI 품질 및 테스트** | 모바일 가독성·포커스·터치 영역 정리, Playwright 시나리오 및 스냅샷 점검 | [PR #29](https://github.com/BDAI9/2026-project-demo/pull/29) |
| **프로젝트 문서화** | README, 추천 기준, 시스템 구조, 배포·복구 가이드 정리 | [PR #31](https://github.com/BDAI9/2026-project-demo/pull/31) |

## 4. 핵심 기능

| 단계 | 기능 | 출력 |
| --- | --- | --- |
| 1 | 지역·지하철역 및 업종 선택 | 장소 검색 범위 |
| 2 | 혼자/함께 결정, 모임 정원 지정 | 모임 설정 |
| 3 | 음식 종류, 예산, 분위기, 방문 특성, 알레르기 | 참여자별 선호 데이터 |
| 4 | 공유 링크를 통한 참여자 입력·수정 | 집계된 선호 조건 |
| 5 | 그룹 투표·안전 제외·근거 기반 랭킹 | 정렬된 장소 후보 |
| 6 | eat.pl 상세 링크 및 선택적 정보 보강 | 추천 이유·주의사항·추가 정보 |

**실제 구현 범위는 한 장소 추천입니다.** 여러 장소를 이어주는 데이트 코스 추천과 예약·결제는 현재 범위에 포함되지 않습니다.

## 5. 기술적 의사결정

| 결정 | 이유 | Trade-off |
| --- | --- | --- |
| 음식 최다 득표 우선 정렬 | 중식 2명·한식 1명인데 부가 점수로 한식이 앞서는 현상 보정 | 소수 취향·예산보다 음식 다수 의견을 우선하는 정책적 선택 |
| 고위험 알레르기 선제 제외 | 선호도가 높아도 위험 신호가 있는 후보를 추천에서 배제 | 실제 재료·교차접촉을 전부 검증할 수 없어 완전한 안전 보장은 불가 |
| 규칙 기반 초기 추천 + 선택적 AI 보강 | 무료 AI 호출 지연·실패 상황에서도 우선 후보를 제공 | 추정 정보의 불확실성과 최종 목록 변동 가능성 |
| 동일 응답 DOM 보존·부분 갱신 | 5/6 및 6/6 폴링 중 화면 깜빡임과 펼침 상태 초기화 완화 | 화면 상태·비동기 요청 시점을 신중히 관리해야 함 |
| SQLite + Python API | 작은 팀의 MVP에서 단순한 배포·데이터 모델 | 단일 worker/서버를 넘어서는 확장성·동시성은 별도 검증 필요 |

## 6. 데이터·개인정보 처리 범위

- **eat.pl/K-Skill:** 후보 가게·업종·주소와 제공된 결제 기반 지표 사용.
- **OpenRouter:** 선택적 추정·상세 정보 보강. 기본 랭커를 대체하지 않습니다.
- **알레르기:** 사용자 입력을 위험 규칙으로 판단하고, 실제 식재료가 불명확하면 경고합니다. **알레르기 안전을 보장하지 않습니다.**
- **모임방:** 로그인 대신 공유 링크와 수정/방장 토큰을 사용합니다. 링크를 아는 이용자는 모임 참여 조건을 볼 수 있습니다. 공개 운영 전 보관·삭제·권한 정책 검토가 필요합니다.
- **운영 보안:** SQLite DB와 API 비밀키는 Git 저장소 밖에서 관리합니다.

## 7. 주요 산출물 및 구현 근거

| 확인 목적 | 팀 저장소 |
| --- | --- |
| 사용자 UI 및 비동기 갱신 | [`web/v2.js`](https://github.com/BDAI9/2026-project-demo/blob/main/web/v2.js) |
| 집계·추천 점수·정렬 | [`moim/recommend.py`](https://github.com/BDAI9/2026-project-demo/blob/main/moim/recommend.py) |
| 업종·알레르기 규칙과 AI 추정 | [`moim/estimate.py`](https://github.com/BDAI9/2026-project-demo/blob/main/moim/estimate.py) |
| 실제 API 및 모임 라우팅 | [`moim/server.py`](https://github.com/BDAI9/2026-project-demo/blob/main/moim/server.py) |
| 데이터 원천 검증 | [`moim/eatpl.py`](https://github.com/BDAI9/2026-project-demo/blob/main/moim/eatpl.py) |
| 자동 UI 테스트 | [`tests/ui_smoke.mjs`](https://github.com/BDAI9/2026-project-demo/blob/main/tests/ui_smoke.mjs) |
| 추천 회귀 테스트 | [`tests/test_moim.py`](https://github.com/BDAI9/2026-project-demo/blob/main/tests/test_moim.py) |
| 아키텍처 및 추천 기준 | [`docs/architecture.md`](https://github.com/BDAI9/2026-project-demo/blob/main/docs/architecture.md), [`docs/recommendation.md`](https://github.com/BDAI9/2026-project-demo/blob/main/docs/recommendation.md) |

## 8. 활용 기술

| 구분 | 기술 |
| --- | --- |
| Frontend | HTML, CSS, JavaScript, Vercel |
| Backend / Storage | Python, Gunicorn, SQLite |
| Data / Ranking | K-Skill API, eat.pl/BC카드 기반 지표, 투표 집계, 규칙 기반 선호 랭킹 |
| Optional Enrichment | OpenRouter, eat.pl 상세 메뉴·후기 정보 |
| Engineering | GitHub PR, GitHub Actions, pytest, Playwright, AWS Lightsail |

## 9. 저장소 구조

개인 계정에는 팀 소스 전체를 복제하지 않고, 팀 원본을 참조합니다.

```text
BDAI9/2026-project-demo/
├── web/                 # 6단계 사용자 UI
├── moim/                # 방 API, SQLite, eat.pl 연동, 추천
├── tests/               # 단위·브라우저 테스트
├── deploy/              # 배포·상태 확인
├── docs/                # 제품·아키텍처·추천·개발·운영
└── README.md

lthofficial01/lthofficial01/
└── projects/
    └── meetrank.md       # 개인 기여·성과 기록 (이 문서)
```

## 10. 실행 및 검증 방법

로컬에서 프로젝트 전체를 재현하려면 팀 저장소를 clone한 뒤 Python 3.10 이상 환경을 사용합니다.

```bash
git clone https://github.com/BDAI9/2026-project-demo.git
cd 2026-project-demo
cp .env.example .env
# .env: EATPL_SOURCE=sample  (가상 매장 데이터)
python -m moim

python -m pip install pytest
python -m pytest -q
```

실제 데이터 연동은 `EATPL_SOURCE=kskill`을 설정해야 하며, API 키는 저장소에 저장하지 않습니다. [운영 배포 안내](https://github.com/BDAI9/2026-project-demo/blob/main/docs/deployment.md)는 GitHub 병합과 AWS 반영을 별도로 구분합니다.

## 11. 성과·한계 및 다음 단계

**코드로 확인 가능한 결과**

- [PR #30](https://github.com/BDAI9/2026-project-demo/pull/30): 중식 2명 > 한식 1명인 경우 다수 선호 우선 정렬 테스트, 고위험 알레르기 제외 회귀 테스트, 동률 처리 검증.
- [PR #30 자동 검증](https://github.com/BDAI9/2026-project-demo/actions/runs/44922000294): Python **248 tests passed**, 브라우저 E2E 및 정적/연동 점검 완료.
- [PR #29](https://github.com/BDAI9/2026-project-demo/pull/29): 모바일 화면 가독성·선택 상태·디자인 정리 및 UI 테스트.
- [PR #31](https://github.com/BDAI9/2026-project-demo/pull/31): 프로젝트 구조/알고리즘/운영 문서화.

**현재 한계**

- 사용자 선호 학습 모델이나 개인화 추천 실험이 아닌, **설명 가능한 규칙 기반 랭킹**입니다.
- 제공처의 후보 목록·업종 분류·결제 데이터가 불완전하면 원하는 음식을 모두 찾지 못할 수 있습니다.
- 가격·운영시간·분위기·알레르기 안전은 실측·보증 정보가 아닙니다.
- 248개 통과는 테스트 결과이며 사용자 만족도, 추천 정확도, 대규모 동시접속 성능을 뜻하지 않습니다.
- 다중 가게 이동 코스, 예약·결제, 지속적인 사용자 평가(A/B)는 미구현 또는 미검증 범위입니다.

**다음 단계:** 실사용 시나리오별 추천 평가셋, 결과 설명 품질, 제공처 후보 편향, 접근·보관 정책과 서비스 운영 안정성 검증.

---

**Team attribution:** This is a collaborative BDAI9 project. The service's full feature set belongs to the team; the contribution section above separates the work evidenced by my merged PRs from other members' contributions.
