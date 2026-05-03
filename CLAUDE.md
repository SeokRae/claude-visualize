# claude-visualize

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

> Claude Code 플러그인 — 문서·데이터를 self-contained HTML 시각화로 변환

## 구조

```
.claude-plugin/
  plugin.json       # 스킬 목록, 버전 (설치 시 사용)
  marketplace.json  # 마켓플레이스 메타데이터 + 버전
skills/
  visualize/
    SKILL.md        # 시각화 스킬
examples/           # 출력 예시
```

## 스킬 추가 체크리스트

새 스킬을 추가할 때 **반드시** 같은 PR에 포함:

- [ ] `skills/{name}/SKILL.md` 생성
- [ ] `plugin.json` → `skills` 배열에 `"./skills/{name}"` 추가 (없으면 배열 신규 추가)
- [ ] `plugin.json` → `version` MINOR 올리기
- [ ] `marketplace.json` → 버전 동일하게 올리기
- [ ] `README.md` / `README.ko.md` 스킬 표에 추가

버전 bump 누락 시 설치본과 소스가 불일치한다 — 버전 bump는 별도 PR 금지.

## 버전 규칙 (semver)

| 변경 | 올릴 자리 |
|------|----------|
| 스킬 추가 / 시각화 유형 추가 | MINOR |
| 버그 픽스 / 문서 수정 | PATCH |
| 스킬 제거 / 인터페이스 변경 | MAJOR |

## 릴리즈 절차 (PR 머지 후)

```bash
git checkout main && git pull origin main
git tag -a v{version} -m "v{version}: {한 줄 요약}"
git push origin v{version}
gh release create v{version} --title "v{version}" --generate-notes
```

## 작업 흐름

```
gh issue create → git checkout -b feature/{N}-{desc} → 구현 + 버전 bump
→ git commit -m "... (#N)" → gh pr create (Closes #N) → gh pr merge → 릴리즈 태그
```

커밋 prefix: `feat:` (스킬 추가), `fix:` (버그), `chore:` (설정·버전 bump), `docs:` (문서)
