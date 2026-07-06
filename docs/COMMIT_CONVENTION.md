# Commit Convention

Conventional Commits를 기반으로 프로젝트 유형에 맞는 `data`와 `analysis` 타입을
추가해 사용합니다.

## Format

```text
<type>(<scope>): <summary>
```

예시:

```text
feat(ui): add missingness heatmap controls
fix(ml): prevent feature-selection leakage
analysis(shortage): calculate department demand index
data(prep): normalize administrative district codes
docs(readme): summarize key findings and limitations
ci(validation): run build and tests on pull requests
```

## Types

| Type | 사용 시점 |
| --- | --- |
| `feat` | 사용자 기능 또는 새로운 처리 기능 추가 |
| `fix` | 오류, 잘못된 계산, 데이터 누수 수정 |
| `data` | 수집, 정제, 스키마, 샘플 데이터 변경 |
| `analysis` | 지표, 통계, 모델, 해석 로직 변경 |
| `refactor` | 동작 변화 없는 구조 개선 |
| `test` | 테스트 추가 또는 수정 |
| `docs` | README, 보고서, 주석 변경 |
| `perf` | 속도 또는 메모리 개선 |
| `build` | 의존성, 빌드 설정 변경 |
| `ci` | GitHub Actions 등 자동 검증 변경 |
| `chore` | 위 타입에 속하지 않는 유지보수 |

## Scopes

프로젝트에서 의미가 드러나는 짧은 소문자 scope를 사용합니다.

```text
ui, io, ml, qc, ppi, pipeline, spark, hive, prep, map, clustering,
visualization, readme, validation
```

## Rules

- summary는 영문 명령형으로 작성하고 마침표를 붙이지 않습니다.
- 한 커밋에는 하나의 논리적 변경만 포함합니다.
- summary는 가능하면 72자 이내로 유지합니다.
- `update`, `main`, `final`, `수정`처럼 변경 내용을 알 수 없는 메시지는 사용하지 않습니다.
- 계산식, 데이터 정의, 동작이 바뀌면 본문에 이유와 검증 방법을 남깁니다.

```text
analysis(demand): revise age preference weighting

Why: Reduce over-representation of sparse age groups.
What: Normalize booking shares within each age group.
Validation: Rebuilt sample outputs and compared Top 3 rankings.
```
