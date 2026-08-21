# claude-setup

Claude Code에 학교 문서 작업용 스킬을 설치하는 마켓플레이스.

| 플러그인 | 하는 일 |
|---|---|
| `live-editor` | 한/글(HWP·HWPX)과 텍스트 PDF를 대화창에서 고친다. 바뀐 자리에 형광펜이 칠해진 미리보기가 즉시 갱신되고, 편집된 파일을 그대로 받는다. 외부 API 없이 로컬에서 처리한다 |
| `jeongsan` | 학교회계 목적사업비 정산 검증. 16개 룰로 기안·견적서를 검산하고 집행내역서를 만든다 |

코딩용 스킬 `ponytail`도 이 마켓플레이스에 등록돼 있지만 기본 설치에는 넣지 않는다. 아래 "코딩 모드" 절 참고.

---

## 0. 미리 필요한 것

`live-editor`는 **Node.js 18 이상**과 **파이썬 3.10 이상**이 있어야 돕니다. 스킬 정의는 설치로
따라오지만 **엔진은 안 따라옵니다** — 첫 사용 때 Claude가 알아서 깝니다(40~70초).

둘 다 없으면 먼저 넣으세요. **관리자 권한 없이도 됩니다:**

- **Node.js** — 공식 **zip** 배포본을 풀고 그 폴더를 `PATH`에 추가
- **파이썬** — python.org 설치 관리자에서 **"Install for me only"**(사용자 전용) 선택

의존성 설치 자체는 권한이 필요 없습니다. npm은 `~/live-editor-work/node_modules`에, 파이썬 쪽은
venv에 들어갑니다. 다만 **학교 정책이 실행 파일 자체를 막으면 우회로가 없습니다.**

`jeongsan`은 파이썬만 있으면 릴리스의 단일 파일로 우회할 수 있습니다:

```bash
gh release download v0.1.0 -R risky-dice/jeongsan
```

---

## 1. 선행 조건 (기계당 한 번)

```bash
gh auth login
```

그다음 이 한 줄을 권합니다. 플러그인 설치기가 `git@github.com:`(SSH)로 클론하는데, SSH 키가
없으면 공개 저장소인데도 `Permission denied (publickey)`로 실패할 수 있습니다. https로 바꿔치기하면
gh 토큰이 인증을 대신합니다.

```bash
git config --global url."https://github.com/".insteadOf "git@github.com:"
```

---

## 2. 설치

```bash
claude plugin marketplace add risky-dice/claude-setup
```

```bash
claude plugin install live-editor@gosu && claude plugin install jeongsan@gosu
```

터미널 Claude Code 안에서 `/plugin`으로 골라도 결과는 같습니다. 확인:

```bash
claude plugin list
```

### `claude` 명령이 없다고 나오면

데스크톱 앱만 깔린 기계에는 CLI가 `PATH`에 없을 수 있습니다. 번들된 실행 파일을 직접 부르세요.

- **윈도우** — `%APPDATA%\Claude\claude-code\<버전>\claude.exe`
- **맥** — `~/Library/Application Support/Claude/claude-code/<버전>/claude`

버전 폴더명은 앱이 업데이트되면 바뀌니 그때그때 확인하세요.

---

## 3. 처음 쓸 때 알아둘 것

**첫 실행이 느립니다.** `live-editor`를 처음 부르면 Claude가 엔진(node 패키지 + pymupdf)을 세션당
한 번 깝니다. 40~70초 걸립니다. 멈춘 게 아닙니다.

**설치가 됐는지 한 줄로 확인할 수 있습니다.** 이게 환경 문제와 문서 문제를 갈라 줍니다.

```bash
cd ~/live-editor-work && node hwpedit.mjs test
```

**25/25 통과면 준비 완료.** 하나라도 실패하면 그 출력을 그대로 들고 문의하세요.

**표가 있는 문서는 한/글에서 한 번 저장하고 보세요.** hwpx에는 한/글이 계산해 둔 줄 배치
기록(`linesegarray`)이 들어 있는데, 편집 방식에 따라 이게 지워집니다. 그러면 미리보기가 한/글
화면과 어긋납니다(셀 넘침, 줄바꿈 차이). 미리보기가 이상하면:

1. 그 파일을 **한/글로 연다**
2. **파일 → 다른 이름으로 저장** (`Ctrl+S`는 안 됩니다 — 고친 게 없으면 한/글이 아무것도 다시 안 씁니다)
3. 형식은 **`*.hwpx` 유지**

이러면 기록이 복원돼 미리보기가 한/글과 같아집니다.

---

## 4. 코딩 모드 (선택)

`ponytail`은 가장 게으른 해법을 강제하는 코딩 모드입니다. **문서 작업만 할 거면 필요 없습니다** —
스킬 6개와 SessionStart 훅이 붙어 Claude의 코딩 동작 자체를 바꿉니다. 코딩에 쓸 때만 넣으세요.

```bash
claude plugin install ponytail@gosu
```

**남의 스킬입니다**(`DietrichGebert/ponytail`). 복사본이 아니라 상류를 가리키므로 업데이트가
따라옵니다 — 뒤집으면 원작자가 푸시하는 것이 그대로 들어온다는 뜻이기도 합니다.

**이미 손으로 깔아 둔 기계에는 플러그인본을 넣지 마세요.** 스킬 6개가 두 번, SessionStart 훅이
두 번 발화합니다. 수동본은 `~/.claude/skills/ponytail*`과 `~/.claude/settings.json` 훅으로
들어가 있습니다.

---

## 5. 주의

**Claude 앱에 번들된 `anthropic-skills:live-editor`는 이것과 다른 구버전입니다.** 이름이 비슷해
헷갈리기 쉬운데, 스킬 목록에서 `anthropic-skills:` 접두사가 붙은 쪽이 구버전입니다.

---

## 6. 막히면

이 세 가지를 함께 보내면 원격에서 판정할 수 있습니다.

1. `claude plugin list` 출력
2. `node hwpedit.mjs test` 출력 (JSON 한 줄)
3. 오류 메시지 전문
