# 정육 영상 매뉴얼 작업 요약

**작업일**: 2026-04-20
**작업 공간**: `D:/meat_manual_workspace/`
**스크립트**: `C:/Users/speci/OneDrive/Desktop/_Manuals_Operations/meat_video_workspace/`

---

## ✅ 완료된 작업 (Phase 1~3)

### 1. 자동 인덱싱
- 원본 141개 영상 (44GB) 스캔 → **중복 제거 후 104개 유니크 영상 / 3.2시간 / 18.4GB**
- 썸네일 3장/영상 자동 생성 (312장, 긴 변 360px)
- `index.json`, `index_unique.json`, `index.csv`, `index.html`

### 2. 매뉴얼 구조 파싱
- `MEAT_OPS_MANUAL_KOR/ENG/ESP.docx` 3종 전문 추출
- 8개 챕터 발견: **CH1 오픈 / CH2 일일 생산 60품목 / CH3 프리미엄 12컷 / CH4 진열 / CH5 운영안전 / CH6 온도 / CH7 KPI / CH8 마감**
- `catalog.json`: 72개 품목 마스터 리스트 (24 베스트셀러 ⭐, 12 프리미엄 ★)
- `manual_KOR.md` / `manual_ENG.md` / `manual_ESP.md`

### 3. 시간 클러스터링
- 30분 gap 기준 → **11개 세션** (`S01~S11`)
- 메인 촬영일: **S10 2026-03-31 (37 clips, 67.7분)** + **S08 2025-12-11 (33 clips, 71분)** = 전체의 67%

### 4. Vision 태깅 100% (104/104)
- Claude가 썸네일 전수 스캔 → 각 영상 **부위/작업/카메라/품질/설명** 태깅
- 품질: **A=46, B=40, C=18** (A+B 83% 사용 가능)
- 종: 돼지 52, 소 32, 기타 20
- `tags.jsonl`

### 5. 카탈로그 매핑
- **Ch2 일일 생산: 60품목 중 56 커버 (93%)**
- **Ch3 프리미엄: 12품목 중 2 커버 (16%)**
- 베스트셀러 24품목 **전부 A급 클립 확보**
- `final_map.json` — 각 품목당 최적 클립 + 전체 클립 리스트

### 6. 웹 최적화 재인코딩 86/86 완료
- 원본 1080x1920 → **720x1280 H.264 CRF 24/27** + AAC 96k
- 포스터 이미지 86장 (각 영상 1초 시점)
- 18.4GB → **1.8GB (90% 압축)**
- `clips_web/` + `clips_web/posters/`

### 7. 웹 매뉴얼 HTML
- `manual.html` (170KB) — 카탈로그 부위별 UI
- 필터: 베스트셀러/프리미엄/돼지/소/영상있음/재촬영필요
- 검색: 파일명·한글명·PLU·태그
- 영상 모달: 각 품목 클릭 → 클립 8개 탭 + 품질 레이블 + 상세 설명
- 3개 언어 구조 대비 (현재 한글 기본)

### 8. L3 재촬영 가이드
- `reshoot_guide_KOR.md`
- 14개 커버리지 갭 부위 (Ch2 뼈 4 + Ch3 프리미엄 10)
- 2카메라 세팅 / 조명 / 음향 권장사양
- 부위별 샷 리스트 + 대본 템플릿 (한/영/스페인어)
- 12~15시간 촬영 일정

### 9. Whisper 자막 추출 기술 검증
- faster-whisper small 모델 / CPU int8
- **RTF 1.12x** (9분 영상 처리에 10분) — 86개 전체 처리 ~14시간
- 한국어 감지 prob 1.00, SRT/TXT 출력 OK

---

## ⚠️ 중요한 발견 — 검토 필요

### 기존 영상의 오디오가 교육용 자막으로 부적합 가능성

Whisper 테스트 결과 `20251210_165036.mp4` (삼겹살 A급 9분 26초)의 **오디오 전사 내용이 실제 작업 설명이 아닌 음식점/요리 리뷰성 대화**로 판명:

> "270도 높은 온도와 두꺼운 무쇠팬에다가 구워야지...", "마케팅적 요소가 크지 않을까...", "인테리어도 너무 잘 해놨고..."

**의심 원인:**
1. 작업 중 TV를 틀어놓고 녹화 (배경음)
2. 작업자가 현장에서 음식점 이야기를 곁들여 대화
3. 일부 영상이 TV 프로그램 녹화 (`S01 20251130_011949`처럼 자막 있는 TV 화면)

**영향:**
- 기존 오디오 직접 자막화 → **교육용 가치 낮음** + 저작권 이슈 가능
- **권장: 별도 내레이션 대본 작성 → TTS 합성** (MEAT_OPS_MANUAL docx 기반)
- 기존 오디오는 **삭제 또는 음소거 처리** 후 TTS 오버더빙

### 사용자 확인 필요
- 각 영상의 오디오 성격 (현장 설명? TV? 잡담?)
- 86개 중 **교육용 설명이 명확한 영상** 선별 (Whisper 추출할 가치 있는 것)
- 나머지는 **TTS 내레이션으로 대체**

---

## 📂 결과물 파일 구조

```
D:/meat_manual_workspace/
├── manual.html               ← 웹 매뉴얼 (170KB, 메인 뷰어)
├── README.md                 ← 이 파일
├── reshoot_guide_KOR.md      ← L3 재촬영 가이드
│
├── index.json                 ← 영상 원본 메타 (141)
├── index_unique.json          ← 중복 제거 (104)
├── index.csv / index.html     ← 인덱스 대시보드
├── catalog.json               ← 72 품목 마스터
├── chapters.json              ← 매뉴얼 챕터 구조
├── manual_text.json           ← 전문 JSON
├── manual_KOR.md / ENG.md / ESP.md  ← 3국어 평문 매뉴얼
├── sessions.json              ← 시간 클러스터 (11세션)
├── tags.jsonl                 ← 태그 DB (104개)
├── final_map.json             ← 품목↔클립 매핑 완성본
├── stats.json                 ← 통계
│
├── thumbs/                    ← 원본 썸네일 312장 (360px)
├── clips_web/                 ← 웹 최적화 영상 86개 (1.8GB)
│   └── posters/               ← 포스터 이미지 86장
└── audio/                     ← Whisper 샘플 (1개)
    ├── 20251210_165036.wav
    ├── 20251210_165036.srt
    └── 20251210_165036.txt
```

### 스크립트 (재실행 가능)
```
C:/Users/speci/OneDrive/Desktop/_Manuals_Operations/meat_video_workspace/
├── index_videos.py          ← 1단계 썸네일 생성
├── analyze.py               ← 2단계 통계·챕터·클러스터
├── extract_manual_text.py   ← docx 전문 추출
├── dump_manual_md.py        ← docx → markdown
├── build_catalog.py         ← catalog.json
├── dedup_and_recluster.py   ← 중복 제거
├── tag_append.py            ← 태그 추가 헬퍼
├── build_final_map.py       ← 5단계 최종 매핑
├── optimize_clips.py        ← 6단계 웹 재인코딩
├── build_web_manual.py      ← 7단계 HTML 생성
└── transcribe_test.py       ← Whisper 테스트
```

---

## 🚀 로컬에서 매뉴얼 열기

```bash
cd /d/meat_manual_workspace
py -m http.server 8765
# 브라우저: http://localhost:8765/manual.html
```

또는 Claude Code preview:
```
/preview meat-manual  (.claude/launch.json에 등록됨)
```

---

## 🎯 다음 단계 (대기)

1. **오디오 성격 결정** — 사용자 검토 후:
   - (옵션 A) 기존 오디오 유지 + 자막 추가 → Whisper 전체 처리
   - (옵션 B) 오디오 제거 + TTS 내레이션 재생성 (권장)
2. **L2 AI 파이프라인** — MEAT_OPS_MANUAL docx 대본 → Claude 3국어 재작성 → ElevenLabs/Edge TTS → FFmpeg 믹싱
3. **정보 오버레이** — 부위명/PLU/두께/칼각도 모션 그래픽 (FFmpeg drawtext)
4. **L3 재촬영** — `reshoot_guide_KOR.md` 기반 14부위 촬영
5. **배포** — `apps.html` 런처에 카드 추가 + QR 코드 매장 벽 부착

---

## 📊 최종 통계

| 지표 | 값 |
|---|---|
| 원본 영상 | 141개 (44GB, 중복 37) |
| 유니크 영상 | 104개 (18.4GB, 3.2시간) |
| 태깅 완료 | 104/104 (100%) |
| A급 클립 | 46개 (44%) |
| Ch2 품목 커버 | 56/60 (93%) |
| Ch3 프리미엄 커버 | 2/12 (16%) — 재촬영 필요 |
| 웹 최적화 완료 | 86개 (1.8GB) |
| 압축률 | 90% |
| 세션 수 | 11개 (주 2세션 = 67%) |
| 매뉴얼 언어 | KOR + ENG + ESP |

**전체 프로젝트 상태**: Level 1 MVP **완성**, Level 2 AI 파이프라인 **준비 완료**, Level 3 재촬영 **가이드 완성**.
