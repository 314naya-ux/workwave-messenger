# [PRD] 사내 실시간 메신저 웹 애플리케이션 (Proto / Single-File Web App)

## 1. 프로젝트 개요 (Overview)
- **목적**: 백엔드 서버 없이 단일 HTML 파일(`index.html`)로 동작하는 사내 실시간 메신저 웹 인터페이스 시제품 개발.
- **핵심 목표**: 개발 지식이 없는 사용자도 쉽게 이해하고 테스트할 수 있도록 직관적인 UI/UX를 제공하며, 향후 Supabase 및 Google OAuth 연동을 고려한 클라이언트 상태 및 스텁(Stub) 구조 설계.
- **대상 AI 에이전트**: Antigravity 2.0 AI 에이전트가 본 문서를 읽고 추가 질의 없이 즉시 동작 가능한 단일 `index.html` 전체 코드를 생성해야 함.

---

## 2. 기술 스택 및 제약 조건 (Tech Stack & Constraints)
1. **파일 구조**: 단일 `index.html` 내에 HTML5, CSS3(`<style>`), Vanilla JS(`<script>`)를 모두 포함. (외부 빌드 도구, 모듈 번들러, React/Vue 등 프레임워크 사용 금지)
2. **외부 라이브러리**: FontAwesome CDN 또는 Lucide Icons CDN (아이콘용), Google Fonts (Inter 또는 Noto Sans KR).
3. **서버 및 인증**: 
   - 서버 연결 없음 (Pure Client-side JS).
   - 모든 로그인/로그아웃/데이터 저장은 메모리 기반(JS 변수)으로 흉내.
   - 코드 내 Supabase 및 OAuth 연동이 필요한 핵심 지점에는 반드시 `// TODO: Supabase 연동` 주석 표기.

---

## 3. 디자인 시스템 (Design System)

### 3.1 색상 팔레트 (Color Palette)
- **Primary / Sidebar Background**: `#1E293B` (Slate 800 - 어두운 남색)
- **Sidebar Active / Hover**: `#334155` (Slate 700)
- **Accent / Point Color**: `#2563EB` (Blue 600 - 포인트 파란색)
- **Accent Hover Color**: `#1D4ED8` (Blue 700)
- **Main Background**: `#F8FAFC` (Slate 50 - 밝은 회색/흰색 톤)
- **Card & Header Background**: `#FFFFFF` (White)
- **Border Color**: `#E2E8F0` (Slate 200)
- **Text Main**: `#0F172A` (Slate 900)
- **Text Sub / Muted**: `#64748B` (Slate 500)
- **My Message Bubble (내 말풍선)**: `#2563EB` (텍스트: `#FFFFFF`)
- **Other Message Bubble (상대 말풍선)**: `#F1F5F9` (텍스트: `#0F172A`)

### 3.2 타이포그래피 & 레이아웃 (Typography & Layout)
- **폰트**: `'Noto Sans KR', sans-serif`
- **사이드바 너비**: Fixed `240px`
- **상단 헤더 높이**: Fixed `60px`
- **로그인 카드 너비**: Fixed `380px`

---

## 4. 화면 및 상태 요구사항 (Screen & State Specifications)

### 4.1 로그인 전 화면 (Pre-Login Screen)
- **화면 구조**:
  - 화면 중앙 정렬 (`display: flex; align-items: center; justify-content: center; min-height: 100vh; background-color: #F8FAFC;`)
  - 흰색 로그인 카드 (`width: 380px; padding: 40px; border-radius: 12px; box-shadow: 0 10px 25px rgba(0,0,0,0.05); text-align: center;`)
- **구성 요소**:
  - 브랜드 로고/아이콘 및 앱 이름 ("WorkWave Messenger")
  - 안내 문구 ("사내 계정으로 로그인하여 업무 대화를 시작하세요.")
  - `Google 계정으로 로그인` 버튼:
    - 파란색 배경 (`#2563EB`), 흰색 글씨, 호버 시 `#1D4ED8`
    - Google 로고 아이콘 포함
- **클릭 동작**:
  - 버튼 클릭 시 실제 외부 인증 없이 즉시 `isLoggedIn = true` 상태 전환.
  - 사용자 프로필을 `게스트` (프로필 이미지 dummy URL 포함)로 설정.
  - 로그인 카드 화면(`div#login-screen`) 숨김(`display: none`), 메인 앱 화면(`div#app-screen`) 표시(`display: flex`).
  - JS 이벤트 핸들러 내부에 `// TODO: Supabase Google OAuth 연동 (supabase.auth.signInWithOAuth)` 주석 명시.

---

### 4.2 로그인 후 메인 화면 (Post-Login App Screen)
로그인 후 전체 화면은 Flexbox/Grid 기반의 대시보드 레이아웃 형태입니다.

#### A. 왼쪽 사이드바 (Left Sidebar - 240px)
- **배경색**: `#1E293B`, 글자색: `#F8FAFC`
- **상단 브랜드 영역 (높이 60px)**:
  - 앱 이름 "WorkWave" + 실시간 상태 점 (초록색 `#10B981`)
- **메뉴 목록 (`ul.menu-list`)**:
  1. `💬 사내 채팅방` (Default Active)
  2. `📚 게시판`
- **메뉴 클릭 동작**:
  - 클릭된 메뉴 항목에 active 클래스 적용 (`background-color: #334155; font-weight: bold; border-left: 4px solid #2563EB;`)
  - 클릭 시 오른쪽 메인 컨텐츠 영역의 View를 전환.
  - `💬 사내 채팅방` 선택 시: 채팅 뷰(`div#chat-view`) 표시.
  - `📚 게시판` 선택 시: 빈 화면에 "📚 게시판 기능은 준비 중입니다." 안내 문구 카드 표시.

#### B. 상단 헤더 (Top Header - 높이 60px)
- **배경색**: `#FFFFFF`, 하단 테두리: `1px solid #E2E8F0`
- **구성 요소**:
  - **좌측**: 현재 선택된 메뉴 타이틀 (예: "💬 사내 채팅방")
  - **우측**: 
    - 프로필 영역: 게스트 아바타(원형 아이콘) + 사용자 이름 "게스트"
    - `로그아웃` 버튼: 회색/레드 톤의 간결한 버튼 (`border: 1px solid #E2E8F0; background: #FFFFFF; color: #64748B;`)
- **로그아웃 클릭 동작**:
  - 클릭 시 `isLoggedIn = false` 전환.
  - 메인 앱 화면 숨김, 로그인 전 화면(로그인 카드)으로 즉시 복귀.
  - JS 핸들러 내 `// TODO: Supabase 로그아웃 연동 (supabase.auth.signOut)` 주석 명시.

#### C. 본문 - 채팅창 뷰 (Chat View)
- **메시지 출력 영역 (`div#message-container`)**:
  - `flex-1`, `overflow-y: auto`, `padding: 20px`
  - **초기 더미 데이터 (2~3개)**:
    - [상대방 / 김철수 팀장]: "안녕하세요! 오늘 프로젝트 진행 상황 공유 부탁드립니다." (좌측, 회색 말풍선 `#F1F5F9`, 시각 포함)
    - [내 메시지 / 게스트]: "네 팀장님, UI 시안 및 요구사항 정의서 작성 완료되었습니다!" (우측, 파란 말풍선 `#2563EB`, 흰색 글씨, 시각 포함)
    - [상대방 / 김철수 팀장]: "좋습니다. 확인 후 피드백 드리겠습니다." (좌측, 회색 말풍선 `#F1F5F9`)
- **말풍선 스타일**:
  - 상대방 메시지: 프로필 이름 + 작성 시간 표시 + 좌측 정렬 + 회색 배경 (`#F1F5F9`)
  - 내 메시지: 작성 시간 표시 + 우측 정렬 + 파란색 배경 (`#2563EB`)
  - 모서리 라운딩: `border-radius: 16px`, 말풍선 꼬리 부분 미세 조절 (`max-width: 60%`).

#### D. 본문 - 메시지 입력창 (Input Area)
- **위치**: 채팅창 최하단 고정 (`height: 70px; border-top: 1px solid #E2E8F0; padding: 12px 20px; background: #FFFFFF;`)
- **구성 요소**:
  - 텍스트 입력 필드 (`input[type="text"]`): `placeholder="메시지를 입력하세요..."`, `flex-1`, 라운드 테두리.
  - 전송 버튼 (`button`): 파란색 배경 (`#2563EB`), "전송" 또는 비행기 아이콘.
- **전송 동작**:
  - 전송 버튼 클릭 또는 입력창에서 `Enter` 키 입력 시 실행.
  - 입력 값이 공백이 아닌 경우:
    1. 메모리 데이터 배열(`messages` array)에 객체 추가 `{ sender: '게스트', text: inputVal, time: '14:25', isMe: true }`
    2. DOM에 즉시 말풍선 element 생성 및 추가.
    3. 메시지 영역 최하단으로 자동 스크롤 (`scrollTop = scrollHeight`).
    4. 입력 필드 초기화 및 포커스 유효화.
  - JS 함수 내부에 `// TODO: Supabase 실시간 DB 저장 및 브로드캐스트 (supabase.from('messages').insert)` 주석 명시.

---

## 5. JavaScript 상태 구조 및 데이터 명세 (Data & State Specification)

```javascript
// ==========================================
// 애플리케이션 상태 관리 (State Management)
// ==========================================
let state = {
  currentUser: null, // { name: '게스트', avatar: '...' }
  activeMenu: 'chat', // 'chat' | 'board'
  messages: [
    { id: 1, sender: '김철수 팀장', text: '안녕하세요! 오늘 프로젝트 진행 상황 공유 부탁드립니다.', time: '10:00 AM', isMe: false },
    { id: 2, sender: '게스트', text: '네 팀장님, UI 시안 및 요구사항 정의서 작성 완료되었습니다!', time: '10:02 AM', isMe: true },
    { id: 3, sender: '김철수 팀장', text: '좋습니다. 확인 후 피드백 드리겠습니다.', time: '10:05 AM', isMe: false }
  ]
};

// ==========================================
// 핵심 동작 함수 및 TODO 주석 위치
// ==========================================

// 1. Google 로그인 (임시)
function handleGoogleLogin() {
  // // TODO: Supabase Google OAuth 연동
  // const { data, error } = await supabase.auth.signInWithOAuth({ provider: 'google' });

  state.currentUser = { name: '게스트' };
  renderApp();
}

// 2. 로그아웃 (임시)
function handleLogout() {
  // // TODO: Supabase 로그아웃 연동
  // await supabase.auth.signOut();

  state.currentUser = null;
  renderApp();
}

// 3. 메시지 전송 (임시)
function sendMessage(text) {
  if (!text.trim()) return;

  const newMessage = {
    id: Date.now(),
    sender: state.currentUser.name,
    text: text,
    time: new Date().toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' }),
    isMe: true
  };

  // // TODO: Supabase DB 저장 및 실시간 구독 (Realtime subscription)
  // await supabase.from('messages').insert([newMessage]);

  state.messages.push(newMessage);
  renderMessages();
  scrollToBottom();
}
```

---

## 6. 안티그래비티 2.0 AI 에이전트 지시사항 (Prompt for Antigravity)

> **안티그래비티 에이전트에게:**
> 1. 위 PRD 요구사항을 100% 반영하여 실행 가능한 단 하나의 `index.html` 파일을 생성하라.
> 2. CSS는 Modern Flexbox/Grid를 사용하고 패딩, 마진, 그림자, 라운딩을 깔끔하게 적용하여 실제 SaaS 메신저(예: Slack, Channel Talk 톤앤매너) 느낌이 나도록 스타일링하라.
> 3. 코드 내 주석으로 지정된 `// TODO: Supabase 연동` 포인트를 빠짐없이 작성하라.
> 4. 새로고침 없이 로그인 ↔ 메인 화면 switching 및 채팅 입력 ↔ 즉시 화면 반영이 부드럽게 동작하도록 DOM 조작 코드를 완료하라.
