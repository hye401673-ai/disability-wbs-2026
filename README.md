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

## 🔄 협업 워크플로 (하이브리드 + 능동 폴링)

페이지가 데이터를 읽어오는 **우선순위**:

```
1. localStorage (본인 작업분, 즉시)
2. data.json (repo의 baseline, fetch)
3. Firestore (능동 폴링, 살아있으면)
```

세 군데 중 **가장 최신** (`updated` 타임스탬프 비교) 데이터를 사용합니다.

### 동기화 방식 (v17부터 — onSnapshot 구독 X)

| 동작 | 시점 | 빈도 |
|---|---|---|
| **읽기 (Read)** | 페이지 로드 시 1회 | + 60초마다 (조건부) |
| | 조건: 탭이 **활성** + **5분 이내 활동** | |
| | 조건 미충족 시 폴링 자동 중단 (quota 절약) | |
| | 사용자 🔄 새로고침 버튼 클릭 시 | 즉시 |
| **저장 (Write)** | 사용자 수정 후 | 2초 debounce |
| | 변경 hash 체크로 중복 방지 | |

**예상 quota 소비** (10명 동시 사용 기준):
- Reads ~2,400/일 (무료 50K 한도의 5%)
- Writes ~500/일 (무료 20K 한도의 2.5%)

### 헤더 상태 표시

| 표시 | 의미 |
|---|---|
| `🟢 실시간 · 최종 …` | 최근 읽기·저장 모두 성공 |
| `⏳ 연결 중 · 최종 …` | 읽기 또는 저장 실패 (재시도 중) |
| `⚠ 로컬 전용 · 최종 …` | Quota 초과 — 로컬에만 저장 (자정 UTC 리셋)|

### Firestore quota 초과 시 (`⚠ 로컬 전용` 표시될 때)

Firestore 무료 플랜은 **하루 20K writes** 제한이 있고, 초과 시 자정 UTC(한국 오전 9시) 리셋됩니다.

이 상태에서는 본인 변경분이 다른 사람에게 안 보입니다. 해결책 두 가지:

**A. 그냥 기다리기 (가장 편함)** — 다음날 오전 9시 이후 본인이 한 번 저장만 트리거하면 자동 동기화됩니다.

**B. data.json으로 즉시 배포 (메인 편집자)**

1. 페이지에서 **📥 JSON 저장** 버튼 클릭 → `wbs-data-YYYY-MM-DD.json` 다운로드
2. 다운로드한 파일을 `data.json`으로 **이름 변경**
3. repo의 `data.json`을 **덮어쓰기** commit·push:
   ```bash
   git add data.json
   git commit -m "data update YYYY-MM-DD"
   git push
   ```
4. 1~2분 후 다른 팀원이 새로고침하면 자동으로 새 `data.json` 받아감

### 보기 전용 (디자이너·개발자·발주처)

- URL만 접속하면 됨
- 편집하면 본인 PC localStorage에 저장 (+ Firestore 살아있으면 다른 사람에게도)
- 메인 편집자가 새 데이터 push하면 새로고침으로 최신 받음

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
- v7 (2026-05-19) · 청각 분과 차시 전면 개편 (OT 회의 반영) · 일반 이슈 등록 기능 (차시 무관) · 이슈 인라인 편집 · 정렬 (차시ID/Phase/우선순위)
- v13 (2026-05-19) · **하이브리드 데이터 소스** · `data.json` 분리 + Firestore 백업 fallback · localStorage 우선 + 변경 hash + quota 보호
- v14~v16 (2026-05-19) · 📊 간트 차트 (5단계 색깔 막대 + 드래그앤드롭 일정 조정 + 양끝 리사이즈)
- v17 (2026-05-20) · onSnapshot → **능동 폴링** (Visibility + Idle 60초) · 🔄 수동 새로고침 버튼 · read/write 분리 상태 표시 (🟢 실시간 / ⏳ 연결 중 / ⚠ 로컬 전용)
