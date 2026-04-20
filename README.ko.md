# claude-visualize

문서와 데이터를 읽기 좋은 단일 HTML 시각화로 변환하는 Claude Code 플러그인입니다.

[English](README.md) · [Live Demo](https://seokrae.github.io/claude-visualize/examples/)

## 무엇을 하나요

어떤 문서, 데이터셋, 분석 내용이든 단일 `.html` 파일로 변환합니다. 출력 결과는 외부 의존성 없이 브라우저에서 바로 열리는 완전한 자립형 파일입니다.

## 지원 타입

| 타입 | 적합한 경우 |
|------|-----------|
| `report` | 긴 분석 문서, 기술 리뷰, step 문서 |
| `flow` | 프로세스 흐름, 상태 전이, 시스템 구조 |
| `comparison` | before/after, 옵션 비교, 기존/신규 구조 비교 |
| `timeline` | 순차 단계, 롤아웃 일정, 인시던트 진행 |
| `dashboard` | KPI, 운영 상태, 스냅샷 요약 |
| `deck` | 발표 슬라이드, 스토리텔링 |

## 설치

### 마켓플레이스 등록

```
/plugins marketplace add SeokRae/claude-visualize
```

### 플러그인 설치

```
/plugins install claude-visualize@claude-visualize
```

## 샘플

각 시각화 타입별 실제 예시 파일 — 브라우저에서 바로 열 수 있습니다.

| 파일 | 타입 | 내용 |
|------|------|------|
| [`examples/index.html`](examples/index.html) · [↗ Live](https://seokrae.github.io/claude-visualize/examples/) | — | 갤러리 인덱스 — 6종 샘플 한 페이지에서 탐색 |
| [`examples/report.html`](examples/report.html) | `report` | API 마이그레이션 분석 리포트 (요약, 배경, 리스크, 다음 단계) |
| [`examples/flow.html`](examples/flow.html) | `flow` | 결제 프로세스 흐름도 (체크아웃 → 정산) |
| [`examples/comparison.html`](examples/comparison.html) | `comparison` | 세션 쿠키 vs JWT 인증 아키텍처 비교 |
| [`examples/timeline.html`](examples/timeline.html) | `timeline` | 스테이블코인 서비스 6단계 런치 타임라인 |
| [`examples/dashboard.html`](examples/dashboard.html) | `dashboard` | 결제 시스템 실시간 KPI 대시보드 |
| [`examples/deck.html`](examples/deck.html) | `deck` | Q2 엔지니어링 전략 발표 슬라이드 (6장) |

## 사용 예시

```
이 문서를 흐름도로 시각화해줘
이 단계들을 타임라인으로 만들어줘
이 분석을 HTML 리포트로 변환해줘
이 지표들을 대시보드로 정리해줘
```

## 디자인 원칙

- 화려함보다 읽기 쉬움
- 카드 수보다 정보 계층
- 장식보다 핵심 메시지
- 기본값은 바닐라 HTML/CSS/JS — 외부 라이브러리는 꼭 필요할 때만

## 라이선스

MIT
