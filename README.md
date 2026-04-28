# node

정적 랜딩 페이지([GitHub Pages](https://nodeboxlab-eng.github.io/node/)) 소스입니다.

배포 전에 **특정 UI를 빼거나**, 했던 변경을 **되돌리거나**, 노출하지 않게 하고 싶으면 **채팅으로 요청**하면 됨. 배너뿐 아니라 **그때 진행 중인 작업 전체**(해당 블록·스타일·스크립트 등)를 숨김·제거·원복하는 방향으로 처리하면 됨.

## 배포 URL에서만 보이는 UI (임베드 vs 직접 접속)

같은 `index.html`이 **아트머그 글 안 iframe**과 **GitHub Pages 직접 주소** 두 맥락에서 열립니다. 브라우저는 보안상 **부모 페이지 도메인을 항상 알려 주지 않으므로**, “artmug에서만” 같은 조건은 **URL·iframe·리퍼러를 조합**해 구현합니다.

**어린이날 이벤트 블록**(`#childrens-day-root`)은 위 규칙으로 **artmug 임베드일 때만** 보이도록 되어 있음. 직접 [node 페이지](https://nodeboxlab-eng.github.io/node/)를 열면 해당 구간은 숨김.

### 1) [artmug.kr](https://artmug.kr/) 글·에디터처럼 **iframe 안에서만** 보이게 할 때

**의도**: [게시글 보기](https://artmug.kr/index.php?channel=view&uid=56867) 등 **다른 사이트에 삽입된 iframe**으로 열었을 때만 표시. [nodeboxlab-eng.github.io/node/](https://nodeboxlab-eng.github.io/node/)를 **주소창으로 직접 연 경우에는 숨김**.

**구현 패턴** (예: `artmug-only-notice`)

1. 먼저 최상위 창이면 중단: `window.self === window.top` 이면 표시하지 않음.
2. iframe이면 다음 중 하나로 판별:
   - iframe `src`에 쿼리 붙이기 (가장 확실):  
     `https://nodeboxlab-eng.github.io/node/?embed=artmug`  
     스크립트에서 `embed=artmug` 또는 `from=artmug` 확인.
   - `document.referrer`에 `artmug.kr` 포함 여부 (부모·리퍼러 정책에 따라 **비어 있을 수 있음**).

**에디터에 넣을 iframe 예시**

```html
<iframe
  src="https://nodeboxlab-eng.github.io/node/?embed=artmug"
  style="width:100%;height:100vh;border:0;display:block;border-radius:20px;"
  scrolling="yes"
></iframe>
```

### 2) **GitHub Pages URL 직접 접속**에서만 보이게 할 때

**의도**: 사용자가 **주소창으로** [https://nodeboxlab-eng.github.io/node/](https://nodeboxlab-eng.github.io/node/)를 열었을 때만 표시. 아트머그 등 **어디에 iframe으로 넣어도 숨김**.

**구현 패턴** (예: `direct-only-test`)

- 표시 조건: `window.self === window.top` 일 때만 `hidden` 제거.
- iframe 안에서는 최상위 창이 아니므로 자동으로 숨겨짐.

### 요약 표

| 목적 | 조건 (개념) |
|------|-------------|
| artmug iframe 등 **임베드에서만** | `top`이 아님 + (`?embed=artmug` 등 또는 `referrer`에 artmug) |
| **직접 접속만** | `window.self === window.top` |

### 주의

- **직접 접속 전용**과 **임베드 전용** 배너는 동시에 켜지면 안 되는 UX가 많음 → 역할별로 요건을 명확히 둘 것.
- 리퍼러·iframe 설정은 호스트(아트머그) 쪽 정책에 따라 달라질 수 있어, **중요한 분기는 `?embed=…` 같은 명시적 쿼리**를 권장함.
