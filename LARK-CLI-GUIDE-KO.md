# lark-cli 분석 · 활용 · 수익화 가이드 (한국어)

> 이 문서는 `lark-cli` 저장소를 직접 분석하고 정리한 한국어 가이드입니다.
> 설치법, 사용법, 사업 아이디어, 기술 스택 선택까지 한 번에 담았습니다.

## 관련 GitHub 주소

| 구분 | 주소 |
|---|---|
| 이 저장소 (작업용 포크) | https://github.com/bmshin94/cli |
| 원본 공식 저장소 | https://github.com/larksuite/cli |
| npm 패키지 | https://www.npmjs.com/package/@larksuite/cli |
| 이슈 등록 | https://github.com/larksuite/cli/issues |
| Meegle CLI (프로젝트 관리, 별도 설치) | https://github.com/larksuite/meegle-cli |

---

## 목차

1. [lark-cli란?](#1-lark-cli란)
2. [저장소 구조 분석](#2-저장소-구조-분석)
3. [설치 가이드](#3-설치-가이드)
4. [사용법](#4-사용법)
5. [문제 해결](#5-문제-해결)
6. [보안 주의사항](#6-보안-주의사항)
7. [수익화 아이디어](#7-수익화-아이디어)
8. [기술 스택 선택: React / PHP로 가능한가?](#8-기술-스택-선택-react--php로-가능한가)
9. [참고 링크](#9-참고-링크)

---

## 1. lark-cli란?

**Lark(라크) / 飞书(페이슈)의 공식 CLI 도구**입니다. Lark 팀(ByteDance 계열)이 직접 만들고 관리하는 오픈소스입니다.

- **언어**: Go (`module github.com/larksuite/cli`, Go 1.23+)
- **라이선스**: MIT (상업적 이용 자유)
- **배포**: npm `@larksuite/cli` (문서 작성 시점 v1.0.93)
- **규모**: 18개 업무 도메인, 200+ 커맨드, 28개 AI Agent Skill

### 한 줄 요약

> **Lark의 메신저·문서·표·캘린더·메일을 마우스 클릭 대신 명령어 한 줄로 조작하는 도구**

Lark는 메신저(카톡) + 문서(노션) + 스프레드시트(구글시트) + 캘린더 + 메일이 하나로 합쳐진 올인원 업무 앱입니다. 중국·동남아·일본 기업에서 주로 사용합니다.

### 진짜 핵심: "AI를 위해 만든 CLI"

사람용이 아니라 **AI 에이전트용으로 설계**된 것이 이 도구의 정체성입니다.

- AI는 마우스 클릭을 못 하지만 **명령어는 칠 수 있습니다**
- 이 CLI를 설치하면 AI에게 자연어로 지시할 수 있습니다
  - "오늘 회의 정리해서 팀 단톡방에 올려줘"
  - "이 CSV 데이터로 Base 표 만들어줘"
- 즉, **AI에게 손발을 달아주는 도구**입니다

### 커버하는 업무 도메인

| 도메인 | 기능 |
|---|---|
| Calendar | 일정 조회/생성/수정, 참석자 초대, 회의실 찾기, 참석 응답 |
| Messenger (IM) | 메시지 송수신, 그룹채팅 관리, 검색, 미디어 업/다운로드 |
| Docs | 문서 생성/조회/수정/검색 |
| Drive | 파일 업/다운로드, 권한·댓글 관리 |
| Markdown | Drive 네이티브 `.md` 파일 관리 |
| Base | 다차원 표, 필드, 레코드, 뷰, 대시보드, 워크플로 |
| Sheets | 스프레드시트 읽기/쓰기/추가/내보내기 |
| Slides | 프레젠테이션 생성·관리 |
| Tasks | 할일, 목록, 서브태스크, 알림 |
| Wiki | 지식 공간, 노드, 문서 |
| Contact | 이름/이메일/전화로 사용자 검색 |
| Mail | 메일 조회/검색/발송/전달/초안 |
| Meetings | 회의 검색, 참가자, 녹취 분석, Minutes 관리 |
| Attendance | 출퇴근 기록 조회 |
| Approval | 승인 업무 조회, 승인/반려/이관 |
| OKR | OKR 조회/생성/수정, 정렬, 지표 |
| Apps | Spark/Miaoda 앱 생성, HTML 사이트 배포 |

---

## 2. 저장소 구조 분석

| 폴더 / 파일 | 역할 |
|---|---|
| `cmd/` | CLI 명령어 진입점 (auth, api, config, skill, schema, doctor 등) |
| `shortcuts/` | 21개 도메인별 단축 명령 (사람·AI 친화) |
| `skills/` | **28개 AI Agent Skill** — 이 저장소의 최대 자산 |
| `internal/` | 인증, 키체인, 보안, 페이지네이션, 리스크컨트롤 등 내부 엔진 (54개 패키지) |
| `events/` | WebSocket 실시간 이벤트 구독 |
| `extension/` | 기업/ISV가 자사 플랫폼에 임베드할 때 쓰는 확장 포인트 |
| `sidecar/` | HMAC 기반 인증 사이드카 (멀티테넌트 데모 포함) |
| `affordance/` | 도메인별 AI용 사용법 가이드 문서 |
| `isolated-skills/` | 격리 실행용 스킬 |
| `skill-template/` | 커스텀 스킬 제작 템플릿 |
| `scripts/` | 설치·빌드·릴리즈·CI 스크립트 (`install.js`, `fetch_meta.py` 등) |
| `AGENTS.md` | AI가 이 저장소에 기여할 때 지켜야 할 규칙서 |

### Skill 목록 (28개)

```
lark-shared          (모든 스킬이 자동 로드하는 공통 베이스)
lark-calendar        lark-im            lark-doc           lark-drive
lark-markdown        lark-sheets        lark-slides        lark-base
lark-task            lark-mail          lark-contact       lark-wiki
lark-event           lark-meeting       lark-minutes       lark-vc
lark-vc-agent        lark-whiteboard    lark-note          lark-okr
lark-apps            lark-attendance    lark-approval
lark-openapi-explorer     lark-skill-maker
lark-workflow-meeting-summary    lark-workflow-standup-report
```

### 배포 방식이 영리한 지점

`scripts/install.js`를 보면, **Go로 빌드한 단일 실행 파일을 npm으로 배달**합니다.

- OS(darwin/linux/windows) × CPU(amd64/arm64/riscv64)별 바이너리를 GitHub Releases에서 다운로드
- 체크섬으로 무결성 검증
- 호스트 allowlist(`github.com`, `objects.githubusercontent.com`, `registry.npmmirror.com`)로 방어

→ **사용자는 Go가 뭔지 몰라도 `npx` 한 줄로 설치 완료**

---

## 3. 설치 가이드

### 준비물

| 필요한 것 | 설명 |
|---|---|
| Node.js (npm) | 필수. `node -v`로 확인 |
| Go 1.23+ / Python 3 | 소스 빌드할 때만 필요 |
| Lark 계정 | 필수 (https://www.larksuite.com) |

### 방법 A — npm 설치 (권장)

```bash
npx @larksuite/cli@latest install
```

### 방법 B — 소스 빌드

```bash
git clone https://github.com/larksuite/cli.git
cd cli
make install

# AI 스킬 설치 (필수)
npx skills add larksuite/cli -y -g
```

> 첫 빌드 시 `scripts/fetch_meta.py`가 `open.feishu.cn`에서 API 메타데이터를 받아옵니다. **인터넷 연결 필수**이며, `LARKSUITE_CLI_REMOTE_META=off`로도 이 빌드타임 fetch는 비활성화되지 않습니다.

### 설치 확인

```bash
lark-cli --help
lark-cli --version
```

### 앱 설정 (최초 1회)

```bash
lark-cli config init --new
```

1. 터미널에 인증 URL 출력
2. 브라우저에서 링크 열기
3. Lark 개발자 콘솔에서 앱 자동 생성 + 권한 승인
4. 완료되면 명령이 자동 종료

App ID / App Secret은 **OS 네이티브 키체인**(맥 키체인, 윈도 자격증명 관리자)에 저장됩니다. 평문 파일로 남지 않습니다.

```bash
lark-cli config init     # 기존 App ID/Secret 직접 입력
lark-cli config show     # 현재 설정 확인
```

### 로그인

```bash
lark-cli auth login --recommend            # 권장: 자주 쓰는 권한 자동 선택
lark-cli auth login                        # 대화형 TUI
lark-cli auth login --domain calendar,task # 특정 도메인만
lark-cli auth login --scope "calendar:calendar:read"  # 개별 scope
lark-cli auth login --no-wait              # 링크만 반환 후 종료 (Agent용)
lark-cli auth login --device-code <CODE>   # 폴링 재개
```

### 확인

```bash
lark-cli auth status    # 현재 로그인 상태 + 권한 목록
lark-cli auth list      # 로그인된 계정 전체
lark-cli auth scopes    # 앱이 사용 가능한 전체 scope
lark-cli auth check <scope>   # 특정 scope 검증 (exit 0 = ok)
```

---

## 4. 사용법

### 3단 계층 명령 시스템

#### 1층 — Shortcuts (`+` 접두사) : 가장 쉬움

스마트 기본값, 표 출력, dry-run 지원. **평소엔 이것만으로 충분**합니다.

```bash
lark-cli calendar +agenda
lark-cli im +messages-send --chat-id "oc_xxxx" --text "안녕하세요!"
lark-cli docs +create --doc-format markdown \
  --content $'<title>주간 보고</title>\n# 진행상황\n- 기능 A 완료'
lark-cli drive +search --doc-types bitable --mine
lark-cli drive +search --sort open_time --page-size 20
```

전체 목록은 `lark-cli <도메인> --help`로 확인합니다.

**도메인 21개**: `calendar` `im` `doc` `drive` `markdown` `sheets` `slides` `base` `task` `mail` `wiki` `contact` `vc` `minutes` `okr` `note` `whiteboard` `apps` `application` `event` `common`

#### 2층 — API Commands : Lark 공식 API와 1:1 매핑 (100+)

```bash
lark-cli calendar calendars list
lark-cli calendar events instance_view \
  --params '{"calendar_id":"primary","start_time":"1700000000","end_time":"1700086400"}'
```

#### 3층 — Raw API : 2500+ API 전부 호출 가능

```bash
lark-cli api GET /open-apis/calendar/v4/calendars
lark-cli api POST /open-apis/im/v1/messages \
  --params '{"receive_id_type":"chat_id"}' \
  --data '{"receive_id":"oc_xxx","msg_type":"text","content":"{\"text\":\"Hello\"}"}'
```

### 주요 옵션

#### 출력 형식

```bash
--format json      # 전체 JSON (기본값)
--format pretty    # 사람이 보기 좋게
--format table     # 표 형태
--format ndjson    # 줄바꿈 구분 JSON (파이프용)
--format csv       # CSV
```

#### dry-run (안전장치)

```bash
lark-cli im +messages-send --chat-id oc_xxx --text "hi" --dry-run
```

실제로 실행하지 않고 요청 내용만 미리 보여줍니다. **위험한 작업 전 필수**입니다.

#### 페이지네이션

```bash
--page-all           # 끝까지 전부 조회
--page-limit 5       # 최대 5페이지
--page-delay 500     # 페이지 사이 500ms 대기
```

#### 신분 선택

```bash
--as user    # 사용자 본인 자격 → 개인 캘린더·클라우드 파일 접근 가능
--as bot     # 애플리케이션 자격 → 앱 소유 리소스만
```

> **함정 주의**: `--as bot`으로 사용자 개인 리소스를 조회하면 에러가 아니라 **"빈 결과 + 성공"**이 반환됩니다. 결과가 비어 있으면 먼저 신분을 의심하세요.

#### 스키마 / 스킬 조회

```bash
lark-cli schema                                # 전체 목록
lark-cli schema calendar.events.instance_view  # 파라미터·응답 구조 조회
lark-cli skills list                           # 스킬 28개 목록
lark-cli skills read lark-doc                  # 특정 스킬의 SKILL.md 출력
```

### AI 에이전트와 연결

```bash
npx skills add larksuite/cli -y -g
```

Claude Code 등 AI 툴에 스킬 28개가 설치됩니다. 이후 자연어로 지시하면 됩니다.

---

## 5. 문제 해결

```bash
lark-cli doctor          # 설정/네트워크/권한 자동 진단
lark-cli auth status     # 로그인 상태
lark-cli config show     # 설정 확인
```

| 증상 | 원인 | 해결 |
|---|---|---|
| 권한 에러 | scope 부족 | `lark-cli auth login --domain <도메인>` 재로그인 |
| 결과가 계속 빈 배열 | 신분 오선택 | `--as user` 추가 |
| `unsafe file path` | 절대경로 사용 | **상대경로만 허용** (cwd 기준) |

### JSON 출력 계약 (스크립트 작성 시 필수)

```
성공 → stdout, 종료코드 0    : { "ok": true,  "identity": "...", "data": {...}, "meta": {...} }
실패 → stderr, 종료코드 != 0 : { "ok": false, "identity": "...", "error": { "type", "code", "message", "hint" } }
```

> **중요**: 성공 여부는 반드시 **`ok == true`** (또는 종료코드)로 판단하세요.
> 원본 OpenAPI 형식인 `code == 0`으로 판단하면 **모든 성공 호출을 실패로 오판**합니다.
> 성공 응답에는 최상위 `code` / `msg` 필드가 없으며, `code`는 에러 응답의 `error` 안에만 존재합니다.
> 자세한 내용: `errs/ERROR_CONTRACT.md`

### 종료코드 10 = 에러가 아님

고위험 쓰기 작업(`risk: "high-risk-write"`)에 대한 **확인 게이트**입니다.

1. 실행을 멈추고
2. 사용자에게 `action`, `risk`, 주요 파라미터를 보여준 뒤
3. 명시적 동의를 받고
4. `hint`가 지정한 확인 플래그를 원래 명령 뒤에 붙여 재실행

**확인 플래그를 조용히 붙여 우회하면 절대 안 됩니다.**

---

## 6. 보안 주의사항

공식 README가 강조하는 내용입니다.

1. **기본 보안 설정을 변경하지 마세요.** 완화하면 위험이 크게 증가하며 책임은 사용자에게 있습니다.
2. **봇을 그룹채팅에 넣지 마세요.** 개인 대화형 비서로만 사용하는 것이 권장됩니다.
3. **쓰기/삭제 전 `--dry-run`**으로 미리 확인하세요.
4. **App Secret / Access Token을 터미널에 평문 출력하지 마세요.**
5. AI 에이전트는 **사용자 권한으로 동작**합니다. 모델 환각·프롬프트 인젝션으로 인한 데이터 유출·무단 조작 위험이 실재합니다.

### 리스크 컨트롤

CLI는 공식 Lark/Feishu HTTPS 도메인 요청에 최소한의 리스크 컨트롤 신호를 함께 전송합니다 (전송 정보: OS 종류, 하드웨어 모델). 기본 활성화 상태입니다.

```bash
lark-cli config risk-control on        # 켜기
lark-cli config risk-control off       # 끄기 (권장하지 않음)
lark-cli config risk-control default   # 기본 정책 복원
```

---

## 7. 수익화 아이디어

### 냉정한 전제 3가지

| 사실 | 의미 |
|---|---|
| MIT 라이선스 | 상업적 이용은 자유지만, **CLI 자체는 판매 불가** (누구나 무료 사용) |
| ByteDance가 직접 관리 | 만든 기능을 **본사가 흡수할 플랫폼 리스크** 존재 |
| 한국 내 Lark 사용자 극소수 | 국내 타겟이면 시장이 없음 (본진은 중국·동남아·일본) |

> 결론: CLI를 파는 것이 아니라 **그 위에 얹는 레이어**를 팔아야 합니다.

### A. Lark 생태계 안에서

#### A-1. Lark 자동화 구축 대행 (ISV / 컨설팅)
**난이도 하 · 수익성 상 · 가장 현실적**

동남아·중국에 진출한 한국 기업들이 Lark를 쓰지만 자동화는 못 하고 있습니다.

- "회의록 → 자동 요약 → Base 표 적재 → 담당자 태스크 배정" 파이프라인 구축
- 프로젝트 단위 + 유지보수 월 구독

근거: `extension/` 폴더가 **정확히 이 용도**로 설계되어 있습니다. CLI 소스를 포크하지 않고 래퍼 `main`으로 확장 가능합니다.

#### A-2. 버티컬 AI 에이전트 SaaS
**난이도 상 · 수익성 최상 · 개발 기간 김**

특정 업종 하나를 깊게 파는 방식입니다.

- 제조·무역: 발주 메일 파싱 → Base 재고표 갱신 → 승인 요청
- HR: 근태(`attendance`) + 승인(`approval`) 자동 리포트
- 영업: 회의 녹취(`minutes`) → 고객 인사이트 → CRM 자동 입력

근거: `sidecar/`에 **멀티테넌트 데모**가 이미 존재합니다. 여러 고객사 자격증명 중앙 관리 구조가 준비되어 있습니다.

#### A-3. 스킬 템플릿 마켓
**난이도 하 · 수익성 하 · 리드 생성용**

`lark-skill-maker`로 업종별 스킬팩 제작 후 판매. 큰 수익은 어렵고 마케팅 수단으로 적합합니다.

### B. 패턴만 가져와 한국 시장에 적용 (추천)

이 저장소의 진짜 가치는 Lark가 아니라 **설계 패턴**입니다.

#### B-1. 국내 SaaS용 Agent-Native 도구 (최우선 추천)
**난이도 중 · 수익성 상 · 타이밍 최적**

카카오워크, 네이버웍스, 잔디, 두레이, 플로우 등은 API는 있지만 **AI 에이전트가 쓰기 어렵습니다.**

lark-cli를 참고서로 삼아 동일 패턴을 이식합니다.

- 3단 계층 구조 (Shortcut → API → Raw)
- `{"ok": true}` JSON 출력 계약
- `--dry-run` 안전장치
- SKILL.md 작성 패턴

수익 모델: 오픈소스로 인지도 확보 → 기업용 유료 버전(SSO, 감사 로그, 멀티테넌트) 판매

#### B-2. 사내 레거시 시스템 AI 래퍼
**난이도 중 · 수익성 최상 · B2B 수요 큼**

오래된 사내 그룹웨어·ERP 앞에 AI가 조작 가능한 인터페이스를 씌우는 사업입니다. 진입장벽이 "도메인 지식"이라 특정 업계를 잘 알면 유리합니다.

### C. 콘텐츠 / 교육

"AI가 쓸 수 있는 도구 설계법" 강의·뉴스레터. 이 저장소 자체가 교보재입니다. 직접 수익보다 **B-1, B-2의 고객 유입 통로**로서 가치가 있습니다.

### 우선순위

| 순위 | 아이디어 | 이유 |
|---|---|---|
| 1 | B-1 국내 SaaS용 Agent 도구 | 시장 공백 + 타이밍 + 참고 자료 확보 |
| 2 | A-1 Lark 자동화 대행 | 즉시 매출, 낮은 리스크 (단 해외 네트워크 필요) |
| 3 | B-2 레거시 AI 래퍼 | 최대 매출, 영업력·도메인 지식 필수 |

### 검증 방법 (1~2주)

```
1주차: 국내 툴 하나 선정 → Shortcut 5개짜리 최소 버전 제작
2주차: AI Skill 연결 → 데모 영상 제작 → 커뮤니티 반응 측정
반응 있음 → 확장 / 반응 없음 → 다음 아이디어
```

### 리스크

- A안은 플랫폼 정책 변경에 취약합니다.
- AI가 회사 데이터를 다루는 서비스는 사고 시 책임 문제가 발생합니다. 계약서·보험을 반드시 검토하세요.
- 기술보다 **"누가 돈을 낼 것인가"가 핵심**입니다. 코딩보다 고객 인터뷰가 먼저입니다.

---

## 8. 기술 스택 선택: React / PHP로 가능한가?

### 결론 요약

| 질문 | 답 |
|---|---|
| React로 만들 수 있나? | CLI로는 비권장. **대시보드는 React가 정답** |
| PHP로 만들 수 있나? | CLI는 배포가 어려움. **백엔드 / MCP 서버는 완벽 가능** |
| 그럼 무엇으로? | **CLI 대신 MCP 서버로 만들면 React + PHP로 전부 커버 가능** |

### React로 CLI 만들기

가능은 합니다. **Ink**(터미널용 React)를 쓰면 됩니다. 실제로 Claude Code, Gemini CLI, GitHub Copilot CLI가 Ink 기반입니다.

```jsx
import {Text, Box} from 'ink';

const App = () => (
  <Box borderStyle="round">
    <Text color="green">전송 완료</Text>
  </Box>
);
```

다만 Ink는 **사람이 보는 대화형 화면**에 적합합니다. AI 에이전트에게 필요한 것은 화려한 UI가 아니라 **깔끔한 JSON 출력**이므로, AI용 명령에는 React가 불필요합니다.

### PHP로 CLI 만들기

가능합니다. Symfony Console을 쓰면 코드는 깔끔합니다. 문제는 **배포**입니다.

| | Go (lark-cli) | PHP |
|---|---|---|
| 배포물 | 실행 파일 1개 | PHP 소스 + composer 패키지 다수 |
| 사용자 준비물 | 없음 | PHP 8.x 설치 + 버전·확장모듈 맞추기 |
| 설치 | `npx` 한 줄 | 런타임 사전 설치 필요 |

lark-cli가 Go를 선택한 이유가 여기 있습니다. PHP CLI는 **자체 서버 내부용**으로는 훌륭하지만 **외부 배포용**으로는 불리합니다.

### 대안: CLI 대신 MCP 서버

AI에게 도구를 제공하는 방법은 CLI만이 아닙니다.

```
방법 1) CLI     : AI가 터미널 명령 실행    → 사용자 PC에 설치 필요
방법 2) MCP 서버 : AI가 HTTP로 서버에 요청 → 사용자 설치 불필요
```

MCP(Model Context Protocol)는 AI가 외부 도구와 통신하는 표준 규격이며, Claude·ChatGPT·Cursor 등이 지원합니다.

**PHP 공식 MCP SDK가 정식 제공됩니다.** (2025년 9월 발표, PHP Foundation + Symfony 팀 공동 유지보수)

```bash
composer require mcp/sdk
```

초기 릴리즈는 **MCP 서버 구축**(tools / prompts / resources 노출)을 지원하며, PHP 애플리케이션이 MCP 클라이언트로 동작하는 기능은 후속 예정입니다. 프레임워크 비종속 API를 제공합니다.

> **주의**: 첫 메이저 릴리즈 전까지는 experimental 단계입니다. 프로덕션에서는 **버전을 고정(pin)**하고 로드맵을 추적하세요.

### 권장 아키텍처

```
┌─────────────────────────────────────────┐
│  Claude / ChatGPT / Cursor              │
└──────────────┬──────────────────────────┘
               │ MCP (HTTP)
               ▼
┌─────────────────────────────────────────┐
│  PHP (Laravel/Symfony) — MCP 서버 + 백엔드 │
│   - AI가 사용할 tool 정의                 │
│   - 멀티테넌트 인증 / 과금                │
│   - 감사 로그                             │
└──────────────┬──────────────────────────┘
               │ REST API
               ▼
┌─────────────────────────────────────────┐
│  React — 관리자 대시보드                  │
│   - AI 작업 로그 조회                     │
│   - 권한 / 요금제 설정                    │
│   - 팀원 초대                             │
└─────────────────────────────────────────┘
```

React의 자리는 CLI가 아니라 **대시보드**입니다. 그리고 유료화 포인트(로그, 권한 관리, 과금)가 바로 이 대시보드에 있습니다.

### lark-cli에서 이식할 설계

| 원본 설계 | PHP/MCP로 이식 |
|---|---|
| 3단 계층 (Shortcut → API → Raw) | MCP tool을 "간편형 / 상세형"으로 분리 |
| `{"ok": true, "data": {}}` 계약 | 응답 포맷 통일 |
| `--dry-run` | `dryRun: true` 파라미터 |
| `--as user` / `--as bot` | 신분 분리 |
| **SKILL.md 작성법** | **가장 값진 자산** — AI용 설명서 작성 노하우 |

`skills/` 폴더의 28개 SKILL.md가 사실상 교과서입니다.

### 로드맵

```
1주차: Laravel + mcp/sdk 설치 → tool 3개(조회/생성/검색) → Claude Desktop 연결 테스트
2주차: React 대시보드 최소 버전 → "AI 실행 작업 목록" 화면
3~4주차: 데모 영상 제작 → 커뮤니티 피드백 수집
```

Go를 새로 배울 필요는 없습니다.

---

## 9. 참고 링크

### 저장소

- 이 저장소: https://github.com/bmshin94/cli
- 원본 공식 저장소: https://github.com/larksuite/cli
- npm 패키지: https://www.npmjs.com/package/@larksuite/cli
- 이슈: https://github.com/larksuite/cli/issues
- meegle-cli: https://github.com/larksuite/meegle-cli

### 공식 문서

- Lark Open Platform: https://open.larksuite.com/
- Agent 임베드 가이드: https://open.larksuite.com/document/mcp_open_tools/feishu-cli/embed-feishu-cli-in-agent
- Lark 서비스 약관: https://www.larksuite.com/user-terms-of-service
- Lark 개인정보 처리방침: https://www.larksuite.com/privacy-policy

> 팁: Open Platform 문서 URL 뒤에 `.md`를 붙이면 원시 Markdown으로 받을 수 있습니다.

### MCP (PHP 스택 참고)

- 공식 PHP SDK 발표: https://blog.modelcontextprotocol.io/posts/2025-09-05-php-sdk/
- PHP SDK 저장소: https://github.com/modelcontextprotocol/php-sdk
- PHP SDK 문서: https://php.sdk.modelcontextprotocol.io/
- Packagist: https://packagist.org/packages/mcp/sdk
- The PHP Foundation 발표: https://thephp.foundation/blog/2025/09/05/php-mcp-sdk/

### React CLI 참고

- Ink (터미널용 React): https://github.com/vadimdemedes/ink

### 저장소 내부 문서

- `README.md` / `README.zh.md` — 공식 안내
- `AGENTS.md` — 빌드·테스트·PR 체크리스트
- `errs/ERROR_CONTRACT.md` — 에러 분류 체계
- `skills/lark-shared/SKILL.md` — 모든 스킬의 공통 규칙
- `extension/README.md` — 확장 포인트 안내

---

*이 문서는 저장소 내용을 직접 분석하여 작성되었습니다. CLI 버전 업데이트에 따라 명령어가 달라질 수 있으니 `lark-cli --help`로 최신 정보를 확인하세요.*
