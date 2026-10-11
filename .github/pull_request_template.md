<!-- 리뷰(Reviewed)가 끝난 브랜치만 PR로 연다. 구현자의 "구현 요약"을 그대로 옮긴다. -->

## 구현 요약
- 스펙: app/docs/specs/<file>.md · 보고서: app/docs/reports/<file>.md
- 방식: 핵심 접근 2~3줄 (고른 이유, 버린 대안)
- 바뀐 파일: `path` — 무엇을 왜 (파일당 한 줄)
- 검증: `명령` → 결과
- 스펙과 달라진 점: 없음 | 내용

## 리뷰
- 리뷰어: <claude | codex> (새 세션) · 라운드: N
- 결과: must-fix 0 · suggestion N (수락 N / 거절 N — 이유는 보고서)

## 머지 전 체크 (사람)
- [ ] CI ✅
- [ ] 스펙의 Human check 실행 → 스펙 `Verified`
