# DONGHANGON
자바스크립트 기말고사
# 🏥 동행온 (DonghangOn)
> 어르신 병원 방문 동행 서비스 — 안전하고 투명하게 자녀의 마음을 전달합니다.

---

## 📌 서비스 소개

**동행온**은 혼자 병원에 가기 어려운 어르신을 위해, 자격 검증된 동행인 파트너가 자택 현관문 앞부터 귀가까지 전 과정을 함께하는 **병원 동행 연결 플랫폼**입니다.

바쁜 자녀가 직접 동행하지 못하더라도, 실시간 카카오톡 알림과 진료 요약 카드를 통해 부모님의 병원 방문을 안심하고 위임할 수 있도록 설계했습니다.

### 주요 사용자
- 부모님 병원 동행이 어려운 직장인 자녀
- 혼자 병원 가기 불안한 고령 어르신
- 빠르고 신뢰할 수 있는 동행인을 찾는 보호자

---

## 🖥️ 화면 구성 (총 11개 화면 — JS 동적 전환)

| 화면 ID | 화면 이름 | 진입 경로 |
|---|---|---|
| `home-view` | 메인 홈 | 기본 진입 |
| `booking-view` | 예약 신청 폼 | 예약 버튼 클릭 |
| `dashboard-view` | 예약 완료 대시보드 | 폼 제출 완료 |
| `companions-view` | 전체 동행인 목록 | 전체 동행인 보기 클릭 |
| `review-view` | 이용 후기 상세 | 후기 카드 클릭 |
| `pricing-view` | 이용 요금 | 푸터 > 이용 요금 |
| `inquiry-view` | 1:1 문의 | 푸터 > 1:1 문의 |
| `partner-view` | 동행인 파트너 지원 | 푸터 > 파트너 지원 |
| `terms-view` | 이용 약관 | 푸터 > 이용 약관 |
| `privacy-view` | 개인정보 처리방침 | 푸터 > 개인정보 |
| `liability-view` | 배상책임 한도 안내 | 푸터 > 배상책임 |

---

## ⚙️ 핵심 기능

### 1. SPA 방식 화면 전환
물리적 HTML 파일 이동 없이 JavaScript의 `style.display` 제어로 11개 화면을 전환합니다.

```javascript
// 모든 뷰를 숨긴 뒤 대상 뷰만 표시
allViews.forEach(v => { if (v) v.style.display = 'none'; });
const target = viewMap[viewName];
if (target) target.style.display = 'block';
```

### 2. 동행인 실시간 검색 필터
`addEventListener('input')`으로 검색창 입력을 감지하고, 배열 메서드 `filter()` · `some()` · `map()`으로 일치하는 카드만 표시합니다.

```javascript
const matched = companionList.filter(c =>
  c.name.includes(kw) ||
  c.tags.some(tag => tag.includes(kw)) ||
  c.cert.includes(kw) ||
  c.area.includes(kw)
);
```

### 3. 예약 폼 처리 및 대시보드 바인딩
폼 입력값을 수집 → 유효성 검사 → 예약번호 난수 생성 → 대시보드 DOM 바인딩 → 화면 전환

```javascript
const randomRsvId = 'DH-2026-06-' + Math.floor(1000 + Math.random() * 9000);
document.getElementById('dispRsvId').innerText = randomRsvId;
```

### 4. async/await 기반 커스텀 모달
브라우저 기본 `alert` / `confirm` 대신 Promise 기반 커스텀 팝업을 구현했습니다.

```javascript
// 취소 여부를 2단계 모달로 확인
const confirmCancel = await openCustomModal('예약 취소 신청', '...', 'danger');
if (confirmCancel) { /* 취소 처리 */ }
```

### 5. 이용 후기 상세 페이지
후기 카드 클릭 시 `reviewData.find()`로 해당 인덱스의 후기 객체를 탐색하고, `split().map().join()` 체이닝으로 본문 단락을 `<p>` 태그로 렌더링합니다.

### 6. 접근성 폰트 크기 조절
`body` 클래스를 교체(`fs-normal` / `fs-large` / `fs-xlarge`)하여 CSS 변수 `--fs-*`가 전역 적용되는 방식으로 글자 크기를 변경합니다.

---

## 🧩 JavaScript 핵심 구조

```
script
 ├── 1.  showSection()         화면 전환 라우터 (sectionId → viewName 변환)
 ├── 2.  showView()            실제 DOM show/hide 처리 (SPA 핵심)
 ├── 3.  companionList[]       동행인 데이터 배열 (filter/find 원본)
 ├── 4.  filterCompanions()    실시간 검색 필터 (filter · some · map 활용)
 ├──     addEventListener('input')    검색창 실시간 이벤트 등록
 ├──     addEventListener('keydown')  Tab 키 포커스 이동 이벤트 등록
 ├── 5.  reviewData[]          후기 데이터 배열
 ├── 6.  showReview()          후기 상세 렌더링 (find · split · map · join)
 ├── 7.  scrollToSection()     홈 섹션 스크롤 이동
 ├── 8.  reserveWithCompanion() 동행인 카드 → 예약폼 연결
 ├── 9.  openCustomModal()     Promise 기반 커스텀 모달 열기
 ├── 10. closeModal()          모달 닫기 및 Promise resolve
 ├── 11. handleBookingSubmit() 예약 폼 제출 처리 (핵심 비즈니스 로직)
 ├── 12. copyReservationId()   예약번호 클립보드 복사
 ├── 13. confirmReservation()  예약 확인 완료 및 폼 초기화
 ├── 14. triggerCancelProcess() async 2단계 취소 프로세스
 ├── 15. adjustFontSize()      접근성 폰트 크기 조절
 ├── 16. submitInquiry()       1:1 문의 폼 제출
 ├── 17. submitPartner()       파트너 지원서 폼 제출
 └── 18. loadSampleData()      시연용 샘플 데이터 자동 입력
```

---

## 🤖 AI 활용 내역

본 프로젝트는 **Claude (Anthropic)**를 활용하여 개발했습니다.

### AI를 활용한 부분
| 영역 | 활용 내용 |
|---|---|
| 전체 HTML 구조 | 서비스 기획을 바탕으로 시맨틱 마크업 초안 생성 |
| CSS 디자인 시스템 | CSS 변수(`--c-*`, `--fs-*`, `--r-*`) 기반 토큰 설계 및 컴포넌트 스타일 |
| JS 화면 전환 로직 | SPA 방식의 `showSection` / `showView` 라우터 구조 |
| 커스텀 모달 | Promise 기반 `openCustomModal` 설계 및 구현 |
| 동행인 SVG 아바타 | 각 동행인별 인라인 SVG 일러스트 생성 |
| 법적 고지 페이지 | 이용약관·개인정보처리방침·배상책임 한도 문서 초안 작성 |

### 직접 수정·보완한 부분
| 영역 | 수정 내용 |
|---|---|
| 동행인 검색 필터 | `addEventListener('input')` 이벤트 + `filter()` · `some()` 로직 직접 설계 및 추가 |
| Tab 키 포커스 이동 | `addEventListener('keydown')` 이벤트로 UX 개선 직접 추가 |
| `companionList` 배열 설계 | 9명 데이터 객체 구조(name, cert, area, tags, rating 등) 직접 설계 |
| `handleBookingSubmit` 확장 | `companionList.find()`로 나머지 동행인 대시보드 처리 로직 직접 추가 |
| JS 전체 주석 | 모든 함수 JSDoc 및 줄 단위 `//` 주석 직접 작성 |
| 화면 구성 기획 | 11개 뷰의 User Flow 및 각 페이지 콘텐츠 기획 |

---

## 🛠️ 기술 스택

- **HTML5** — 시맨틱 마크업, 단일 파일 SPA 구조
- **CSS3** — CSS 변수(Custom Properties), Flexbox, Grid, 반응형 미디어쿼리
- **Vanilla JavaScript** — DOM 조작, 이벤트 리스너, 배열 메서드, async/await, Promise

> 외부 라이브러리·프레임워크 미사용 (순수 HTML/CSS/JS만으로 구현)

---

## 📁 파일 구조

```
동행온_최종.html      ← 전체 서비스가 담긴 단일 HTML 파일
README.md            ← 프로젝트 설명 문서 (현재 파일)
```

---

## 🚀 실행 방법

별도 설치 없이 `동행온_최종.html` 파일을 브라우저에서 열면 바로 실행됩니다.

```bash
# 파일을 더블클릭하거나 브라우저에 드래그 앤 드롭
open 동행온_최종.html
```

---

## 👤 제작자

- 과목: 웹 프론트엔드 개발 실습
- 제출일: 2026년 6월
- 서비스명: **동행온** — 어르신 병원 방문 동행 플랫폼
