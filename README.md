# 2026 장애유형별 평생교육 디지털 콘텐츠 개발 · WBS Tracker

국립특수교육원 발주 · 60차시 디지털 콘텐츠 개발 일정·이슈 추적 페이지

🔗 **배포 URL**: https://hye401673-ai.github.io/disability-wbs-2026/

---

## 📋 주요 기능

- **대시보드** · 60차시 카드 + 진행도 6단계 progress bar
- **테이블** · 정렬·필터 가능한 차시 일람
- **칸반** · 현재 진행 단계별 그룹화 (기획 / 개발 / 검수 / 완료)
- **이슈 트래커** · 차시별 이슈 추가·해결·삭제 (High/Medium/Low 우선순위)
- **마일스톤 4건** · 7월 초 시연 · 8월 초 현장 검토 · 11월 말 최종 검수 · 12월 초 납품
- **CSV / JSON Export** · Excel·다른 도구 연동
- **JSON Import** · 다른 사람이 작업한 데이터 받기
- **localStorage 자동 저장** · 편집 즉시 브라우저에 저장

---

## 🚀 배포 (GitHub Pages)

### 1) Public repo 전환 (필수)

GitHub Pages 무료 플랜은 **public repo만** 지원합니다.

1. Repo Settings → 맨 아래 **Danger Zone**
2. **Change repository visibility** → **Public** 선택
3. 확인

> 💡 WBS는 차시명·일정·담당자 정도만 들어가 있어 외부 노출되어도 무방합니다. URL을 모르면 검색되지 않으니 사실상 비공개와 비슷합니다.

### 2) GitHub Pages 활성화

1. Repo Settings → **Pages** (좌측 메뉴)
2. **Source**: `Deploy from a branch`
3. **Branch**: `main` / **Folder**: `/ (root)`
4. **Save**
5. 1~2분 후 https://hye401673-ai.github.io/disability-wbs-2026/ 접속

### 3) `index.html` 파일 push

이 폴더의 `index.html`을 repo 루트에 commit & push:

```bash
git add index.html
git commit -m "Initial WBS tracker"
git push origin main
```

---

## 🔄 협업 워크플로

이 페이지는 **로컬 편집 + JSON 동기화** 방식입니다 (실시간 협업 X).

### 메인 편집자 (기획팀)

1. URL 접속 → 차시 클릭 → 단계 상태·일정·이슈 편집
2. 변경사항은 **브라우저 localStorage**에 자동 저장
3. 주요 변경 후 **📥 JSON 저장** 버튼 → `wbs-data-YYYY-MM-DD.json` 다운로드
4. 다운로드한 JSON을 repo의 `data.json`으로 commit·push
5. (또는 HTML 안의 `SEED` 객체를 새 JSON으로 교체 후 commit·push)

### 보기 전용 (디자이너·개발자·발주처)

- URL만 접속하면 됨
- 편집해도 본인 PC localStorage에만 저장 (다른 사람에게 안 보임)
- 메인 편집자가 새 데이터 push하면 새로고침으로 최신 받음
  - 단, 본인이 편집한 게 있으면 **🔄 초기화** 버튼으로 시드 데이터 복귀

---

## 🛠 데이터 구조

```json
{
  "version": 1,
  "updated": "2026-05-15",
  "milestones": [...],
  "lessons": [
    {
      "id": "뇌병변 1-3",
      "name": "상황에 맞는 위톡 앱 대화",
      "type": "뇌병변",
      "area": "디지털",
      "expert": "최진영",
      "phase": "Phase 1",
      "priority": "1차 (6/19 공유)",
      "videoCount": 3,
      "stages": {
        "plan":  { "status": "in-progress", "start": "2026-05-18", "end": "2026-05-22", "owner": ["최문선"], "notes": "" },
        "ui":    { ... },
        "art":   { ... },
        "dev":   { ... },
        "video": { ... },
        "qa":    { ... }
      },
      "issues": [
        { "id": "I-abc", "title": "...", "description": "...", "status": "open", "priority": "high", "created": "2026-05-15", "resolved": null }
      ],
      "notes": ""
    }
  ]
}
```

### 단계 상태값

| 값 | 뜻 |
|---|---|
| `todo` | 시작 전 |
| `in-progress` | 진행 중 |
| `done` | 완료 |
| `blocked` | 블락 (의존성 또는 이슈로 막힘) |

---

## 📅 마일스톤 (고정)

| 시기 | 마일스톤 | 비고 |
|---|---|---|
| 7월 초 (7/10~11) | 프로토타입 시연 | 1박2일 · 9차시 알파 시연 |
| 8월 초 (8/3~14) | 현장 적합성 검토 | 9차시로 장애 학습자 테스트 |
| 11월 말 (11/24~30) | 최종 검수 | 60차시 통합 검토회 |
| 12월 초 (12/4) | 납품 / 설명회 | 최종 납품 + 설명회 |

---

## 📝 변경 이력

- v1 (2026-05-15) · 초기 빌드 · 60차시 시드 + 시드 이슈 3건
- v2 (2026-05-19) · Firestore 실시간 동기화 · 칸반 7컬럼(시작 전 추가) · 모달 단계 표 형식 · **계획 vs 실제 일정 비교** · 진척 배지(정상/지연/완료) · 지연 시 자동 색깔 경고
