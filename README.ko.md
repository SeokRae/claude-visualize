# claude-visualize

문서와 데이터를 읽기 좋은 단일 HTML 시각화로 변환하는 Claude Code 플러그인입니다.

[English](README.md)

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
