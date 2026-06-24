---
name: accessibility
description: >
  HTML 마크업과 Figma 디자인 양쪽의 접근성을 검수하고 개선하는 Skill.
  WCAG 2.2 AA + 바이널씨 컨벤션 기준으로 점검한다.
  트리거 예시: "접근성 검수해줘", "웹 접근성 확인해줘", "WCAG 체크해줘",
  "스크린리더 대응해줘", "키보드 접근성 확인해줘", "색상 대비 확인해줘",
  "aria 써줘", "접근성 개선해줘", "figma 접근성 확인해줘", "디자인 접근성 검수".
---

# Accessibility Review Skill

WCAG 2.2 AA + 바이널씨 컨벤션 기준의 접근성 검수 및 개선 Skill.  
HTML 마크업과 Figma 디자인 모두 커버한다.

---

## Step 0: 검수 범위 파악

| 요청 유형 | 접근 방법 |
|-----------|-----------|
| HTML 전체 페이지 검수 | 영역 1–5 순서대로 전체 점검 |
| Figma 디자인 검수 | 영역 6 (디자인 접근성) 점검 |
| 특정 컴포넌트 | 해당 영역만 집중 점검 |
| 개선 방법 요청 | 문제 파악 → 수정 코드/가이드 제시 |
| 납품 전 체크리스트 | 섹션 E 빠른 체크리스트 사용 |

---

## 영역 1: 인지 가능성 (Perceivable)

### 1-1. 이미지 대체 텍스트

```html
<!-- ✅ 의미 있는 이미지 -->
<img src="chart.png" alt="2024년 4분기 매출이 전년 대비 30% 증가한 막대 그래프">

<!-- ✅ 장식용 이미지 (스크린리더 무시) -->
<img src="deco.webp" alt="" role="presentation">

<!-- ✅ 복잡한 정보를 담은 이미지 -->
<figure>
  <img src="infographic.png" alt="인포그래픽 요약" aria-describedby="infographic-desc">
  <figcaption id="infographic-desc">상세 설명...</figcaption>
</figure>

<!-- ❌ alt 누락 -->
<img src="product.jpg">

<!-- ❌ 파일명을 alt로 사용 -->
<img src="img_01.jpg" alt="img_01.jpg">
```

### 1-2. 색상 대비

WCAG AA 기준:
- 일반 텍스트 (18px 미만 / bold 14px 미만): **4.5:1 이상**
- 대형 텍스트 (18px 이상 / bold 14px 이상): **3:1 이상**
- UI 컴포넌트 경계선, 아이콘: **3:1 이상**

```css
/* ✅ 충분한 대비 */
color: var(--color-text-primary);       /* #171717 on white = 16.1:1 */
color: var(--color-text-secondary);     /* #525252 on white = 7.0:1 */

/* ⚠️ 위험 — 반드시 확인 필요 */
color: var(--color-text-disabled);     /* #a3a3a3 on white = 2.3:1 → 텍스트에 사용 금지 */
```

**확인 도구:** https://webaim.org/resources/contrastchecker/

### 1-3. 색상만으로 정보 전달 금지

```html
<!-- ❌ 색상만으로 에러 표시 -->
<input style="border-color: red">

<!-- ✅ 아이콘 + 텍스트 + 색상 병행 -->
<div class="form_group form_group--error">
  <input aria-invalid="true" aria-describedby="err">
  <span id="err" role="alert">⚠ 이메일을 올바르게 입력해주세요.</span>
</div>
```

---

## 영역 2: 운용 가능성 (Operable)

### 2-1. 키보드 접근성

모든 인터랙션은 키보드만으로 가능해야 한다.

```html
<!-- ✅ 네이티브 요소 사용 (자동으로 키보드 접근 지원) -->
<button type="button">클릭</button>
<a href="/page">이동</a>

<!-- ❌ div/span 클릭 이벤트 — 키보드 불가 -->
<div onclick="doSomething()">클릭</div>

<!-- 불가피하게 div 사용 시 role + tabindex 필수 -->
<div
  role="button"
  tabindex="0"
  onclick="doSomething()"
  onkeydown="if(event.key==='Enter'||event.key===' ')doSomething()"
>
  클릭
</div>
```

### 2-2. 포커스 표시

**[2.4.11 AA — WCAG 2.2 신규]** 포커스 인디케이터 최소 요건:
- 포커스 링의 둘레 면적이 컴포넌트 둘레 × 2px 이상
- 포커스 링과 인접 색상 간 대비 **3:1 이상**

```css
/* ❌ 절대 금지 */
:focus { outline: none; }
*:focus { outline: 0; }

/* ✅ 커스텀 포커스 스타일 — WCAG 2.4.11 AA 충족 */
:focus-visible {
  outline: 2px solid var(--color-border-focus); /* 인접 색 대비 3:1 이상 확보 */
  outline-offset: 2px;
  border-radius: 2px;
}
```

### 2-3. 건너뛰기 링크 (바이널씨 컨벤션 필수)

바이널씨 컨벤션에서 `#skipnavi`는 예약된 레이아웃 ID다. 반드시 포함해야 한다.

```html
<!-- body 최상단에 위치, 포커스 시에만 보이게 -->
<a href="#contents" class="skip_navi" id="skipnavi">본문 바로가기</a>

<style>
.skip_navi {
  position: absolute;
  top: -9999px;
  left: -9999px;
}
.skip_navi:focus {
  top: 0;
  left: 0;
  z-index: var(--z-index-tooltip);
  padding: var(--spacing-2) var(--spacing-4);
  background: var(--color-primary);
  color: #fff;
}
</style>
```

### 2-4. 충분한 클릭 영역

| 기준 | 크기 | 레벨 |
|------|------|-------|
| **[2.5.8 WCAG 2.2 신규]** 타깃 크기 최소 | **24×24px** (또는 인접 타깃과의 간격 포함 24px) | AA |
| **[2.5.5]** 타깃 크기 권장 | **44×44px** | AAA |
| 바이널씨 컨벤션 | **44×44px** (AAA 수준 권장 유지) | — |

```css
/* ✅ AA 최소 충족 (24px) + 바이널씨 권장 (44px) */
.icon_btn {
  padding: var(--spacing-2);
  min-width: 44px;
  min-height: 44px;
}

/* ✅ 타깃 자체가 작을 때 — 히트 영역 확장으로 2.5.8 AA 충족 */
.icon_btn--small::after {
  content: '';
  position: absolute;
  inset: -10px; /* 시각 크기가 24px 미만이어도 히트 영역 24px 이상 확보 */
}
```

---

## 영역 3: 이해 가능성 (Understandable)

### 3-1. 언어 설정

```html
<html lang="ko">

<!-- 본문 내 외국어 구간 -->
<span lang="en">Hello World</span>
```

### 3-2. 폼 레이블

```html
<!-- ✅ label + for/id 연결 (label 안에 input 중첩 금지) -->
<label for="userId">아이디</label>
<input type="text" id="userId">

<!-- ✅ label이 없을 때 aria-label -->
<input type="search" aria-label="사이트 검색" placeholder="검색어 입력">

<!-- ✅ 힌트 텍스트 연결 -->
<input id="pw" aria-describedby="pw-hint">
<p id="pw-hint">영문, 숫자 포함 8자 이상</p>
```

### 3-3. 에러 메시지

```html
<!-- ✅ 에러 내용 + 수정 방법 명시 -->
<span role="alert" id="emailErr">
  이메일 형식이 올바르지 않습니다. (예: name@example.com)
</span>

<!-- ✅ 동적 에러: aria-live -->
<div aria-live="polite" aria-atomic="true" class="live_region">
  <!-- JS로 에러 텍스트 삽입 시 스크린리더가 읽음 -->
</div>
```

---

## 영역 4: 견고성 (Robust)

### 4-1. ARIA 사용 원칙

```
규칙 1: 네이티브 HTML 요소로 해결 가능하면 ARIA 불필요
규칙 2: 네이티브 의미(semantics)를 ARIA로 덮어쓰지 않는다
규칙 3: 모든 인터랙티브 요소는 키보드 접근 가능해야 한다
```

```html
<!-- ✅ ARIA 올바른 사용 예 -->

<!-- 탭 컴포넌트 -->
<div role="tablist" aria-label="상품 정보">
  <button role="tab" aria-selected="true" aria-controls="panel-1" id="tab-1">상세정보</button>
  <button role="tab" aria-selected="false" aria-controls="panel-2" id="tab-2">리뷰</button>
</div>
<div role="tabpanel" id="panel-1" aria-labelledby="tab-1">...</div>

<!-- 모달 -->
<dialog role="dialog" aria-modal="true" aria-labelledby="modal-title">
  <h2 id="modal-title">모달 제목</h2>
  <button aria-label="닫기">×</button>
</dialog>

<!-- 토글 버튼 -->
<button aria-pressed="false">알림 켜기</button>

<!-- 확장/축소 -->
<button aria-expanded="false" aria-controls="submenu">메뉴</button>
<ul id="submenu" hidden>...</ul>
```

### 4-2. 아이콘 접근성

```html
<!-- 텍스트 없는 아이콘 버튼 -->
<button type="button" aria-label="검색">
  <svg aria-hidden="true" focusable="false">
    <use href="#ico_search"></use>
  </svg>
</button>

<!-- 아이콘 + 텍스트 (아이콘은 숨김) -->
<button type="button">
  <svg aria-hidden="true" focusable="false">...</svg>
  <span>검색</span>
</button>
```

---

## 영역 5: 모달 / 팝업 포커스 관리

```javascript
// 모달 열 때: 포커스 이동
modal.addEventListener('open', () => {
  firstFocusableElement.focus()
})

// 모달 내 포커스 트랩 (Tab/Shift+Tab)
modal.addEventListener('keydown', (e) => {
  if (e.key !== 'Tab') return
  if (e.shiftKey) {
    if (document.activeElement === firstEl) {
      e.preventDefault()
      lastEl.focus()
    }
  } else {
    if (document.activeElement === lastEl) {
      e.preventDefault()
      firstEl.focus()
    }
  }
})

// 모달 닫을 때: 트리거 요소로 포커스 복귀
modal.addEventListener('close', () => {
  triggerButton.focus()
})
```

---

## 영역 5-B: WCAG 2.2 신규 기준

### 5B-1. 드래그 대체 수단 [2.5.7 AA]

드래그로만 동작하는 기능에는 반드시 클릭/탭 기반 대체 수단을 제공해야 한다.

```html
<!-- ❌ 드래그만 지원 -->
<ul id="sortable">...</ul>

<!-- ✅ 드래그 + 버튼 대체 수단 병행 -->
<ul id="sortable">
  <li>
    항목 A
    <button aria-label="항목 A를 위로 이동" onclick="moveUp(this)">▲</button>
    <button aria-label="항목 A를 아래로 이동" onclick="moveDown(this)">▼</button>
  </li>
</ul>
```

### 5B-2. 일관된 도움 [3.2.6 A]

고객센터 링크, 챗봇, 전화번호 등 도움 메커니즘이 여러 페이지에 걸쳐 있을 경우 **동일한 위치**에 배치해야 한다.

```
✅ 푸터 고객센터 링크가 모든 페이지에서 동일한 순서/위치로 노출
❌ 어떤 페이지는 푸터, 어떤 페이지는 사이드바에 위치
```

### 5B-3. 중복 입력 방지 [3.3.7 A]

동일한 세션 내에서 이미 입력한 정보를 다시 요구하지 않는다.

```html
<!-- ✅ 배송지와 청구지가 같을 때 자동 채우기 -->
<label>
  <input type="checkbox" id="sameAddr" onchange="copyAddress()">
  배송지와 동일
</label>

<!-- ✅ 이전 단계 입력값 자동 유지 (다단계 폼) -->
<input id="email" value="이전 단계에서 입력한 값 자동 채움">
```

### 5B-4. 인증 접근성 [3.3.8 AA]

로그인 등 인증 과정에서 인지 기능 테스트(암호 외우기, 수수께끼, CAPTCHA)를 **유일한 수단**으로 요구하지 않는다.

```
✅ 허용: 비밀번호 관리자 사용 가능한 일반 비밀번호 입력
✅ 허용: 이메일 OTP / 소셜 로그인 대체 수단 제공
✅ 허용: CAPTCHA가 있어도 이메일 인증 등 대체 수단 존재
❌ 금지: 텍스트 CAPTCHA만 제공하고 다른 인증 수단 없음
❌ 금지: 특정 이미지 기억 후 선택을 유일한 인증 수단으로 사용
```

---

## 영역 6: Figma 디자인 접근성

디자인 단계에서 확인해야 할 항목. figma-mcp-go가 연결된 경우 변수·스타일을 직접 조회해 검수한다.

### 6-1. 색상 대비

- 텍스트 색상과 배경색의 대비 확인 → https://webaim.org/resources/contrastchecker/
- Figma 변수에서 추출한 hex 값으로 직접 계산
- 비활성(disabled) 상태는 예외이나, 비활성 텍스트를 일반 텍스트와 혼용하는 경우 주의

```
검수 기준
  일반 텍스트:  4.5:1 이상
  대형 텍스트:  3:1 이상 (18px↑ 또는 bold 14px↑)
  아이콘/테두리: 3:1 이상
```

### 6-2. 터치 타깃 크기

- **[2.5.8 AA]** 터치 타깃 최소 **24×24px** (WCAG 2.2 신규)
- **[2.5.5 AAA / 바이널씨 권장]** **44×44px** 이상 권장
- 아이콘이 작아도 히트 영역(터치 영역)은 24px 이상으로 설계
- Figma에서 컴포넌트의 실제 frame 크기 기준으로 확인

### 6-3. 포커스 인디케이터 디자인

- 포커스 상태(focused state)가 컴포넌트에 정의되어 있는지 확인
- 포커스 링의 색상 대비: 배경 대비 **3:1 이상**
- 포커스 링 두께: **2px 이상** 권장

### 6-4. 텍스트 크기 및 줄간격

- 본문 텍스트 최소 **14px** 이상 (모바일 12px까지 허용하나 권장하지 않음)
- 줄간격(line-height) **1.5 이상** 권장 (한글 본문 기준)
- 자간(letter-spacing) 과도하게 늘리지 않음

### 6-5. 상태 표현

색상만으로 상태를 구분하지 않는다. 아이콘, 텍스트, 패턴을 병행 사용한다.

```
❌ 에러 상태를 빨간 테두리 색상만으로 표현
✅ 에러 아이콘 + 에러 텍스트 + 빨간 테두리 병행
```

### 6-6. 애니메이션 / 모션

- 자동 재생 애니메이션은 **3초 이내** 또는 정지 수단 제공
- `prefers-reduced-motion` 대응 여부를 디자인 스펙에 명시

---

## 섹션 E: 납품 전 빠른 체크리스트

```
[ ] HTML lang 속성 설정 (ko)
[ ] 모든 img에 alt 속성 (장식용은 alt="")
[ ] 색상 대비 4.5:1 이상 (텍스트), 3:1 이상 (UI 요소)
[ ] 색상만으로 정보 전달 없음
[ ] 모든 인터랙션 키보드 가능
[ ] focus outline 제거 없음
[ ] skip 링크 (#skipnavi) 포함 — 바이널씨 컨벤션 필수
[ ] 모든 form input에 label 연결
[ ] 에러 메시지에 role="alert" 또는 aria-live
[ ] 아이콘 전용 버튼에 aria-label
[ ] 모달/팝업 포커스 트랩 + 복귀
[ ] 터치 타깃 24×24px 이상 (AA), 44×44px 권장
[ ] 움직이는 요소 prefers-reduced-motion 대응
[ ] 제목(heading) 계층 순서 올바름
[ ] 테이블에 caption + scope 속성
--- WCAG 2.2 신규 ---
[ ] [2.4.11] 포커스 링 — 인접 색 대비 3:1 이상, 두께 2px 이상
[ ] [2.5.7] 드래그 기능에 클릭/탭 대체 수단 존재
[ ] [3.2.6] 도움 메커니즘(고객센터 등)이 모든 페이지에서 동일 위치
[ ] [3.3.7] 동일 세션 내 이미 입력한 정보 재요구 없음
[ ] [3.3.8] 인증 과정에 인지 테스트만 강제하지 않음

[디자인]
[ ] Figma 컴포넌트에 focused state 정의됨
[ ] 비활성 제외 모든 텍스트 색상 대비 통과
[ ] 에러/경고 상태를 아이콘+텍스트로 병행 표현
[ ] 터치 영역 44px 이상으로 설계
[ ] 자동 재생 모션 3초 이내 또는 정지 수단 있음
```

---

## 출력 형식

검수 결과는 아래 형식으로 제공한다:

```
## 접근성 검수 결과: [파일명/컴포넌트명]

❌ Critical (즉시 수정 — WCAG 2.2 위반)
- [1.1.1] Line N: 이미지에 alt 속성 없음 → alt="[설명]" 추가

⚠️ Warning (수정 권장)
- [1.4.3] Line N: 텍스트 색상 대비 부족 (2.3:1) → 최소 4.5:1 필요

💡 Suggestion (개선 권장)
- [2.4.7] 포커스 스타일을 더 명확하게 개선 권장

✅ 통과: N개 항목
총 이슈: Critical N / Warning N / Suggestion N
```
