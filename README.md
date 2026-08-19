# claude-setup

새 노트북에서 스킬 세 개를 설치하는 마켓플레이스.

## 선행 조건 (기계당 한 번)

```bash
gh auth login
```

그다음 **반드시** 이 한 줄. 플러그인 설치기가 `git@github.com:`(SSH)로 클론하는데,
SSH 키가 없으면 공개 레포조차 `Permission denied (publickey)` 로 실패한다.
키를 만들어 계정에 올리는 대신 https로 바꿔치기하면 osxkeychain의 gh 토큰이 인증을 처리한다.

```bash
git config --global url."https://github.com/".insteadOf "git@github.com:"
```

## 설치

```bash
claude plugin marketplace add risky-dice/claude-setup && claude plugin install jeongsan@gosu && claude plugin install live-editor@gosu && claude plugin install ponytail@gosu
```

| 플러그인 | 출처 | 비고 |
|---|---|---|
| `jeongsan` | risky-dice/jeongsan | 내가 만듦 |
| `live-editor` | risky-dice/live-editor | 내가 만듦. 첫 사용 시 Claude가 `npm install` + venv 로 pymupdf 자동 설치(40~70초). Node·파이썬 런타임은 미리 있어야 함 |
| `ponytail` | DietrichGebert/ponytail | 남의 스킬. 복사본이 아니라 상류를 가리키므로 업데이트가 따라온다 |

확인:

```bash
claude plugin list
```

## 주의

**ponytail을 이미 수동 설치한 기계에는 플러그인본을 넣지 말 것.** 스킬 6개가 두 번,
SessionStart 훅이 두 번 발화한다. 수동본은 `~/.claude/skills/ponytail*` 과
`~/.claude/settings.json` 훅으로 들어가 있다.

**Node.js 나 파이썬이 아예 없는 기계**(런타임 설치에 관리자 권한이 필요한 학교 노트북 등)
에서는 live-editor 가 실행되지 않는다. 의존성 설치 자체는 권한이 필요 없다 —
npm 은 `~/live-editor-work/node_modules` 에, 파이썬 쪽은 venv 에 깔면 된다.
jeongsan 은 파이썬만 있으면 릴리스의 단일 파일로 우회한다:

```bash
gh release download v0.1.0 -R risky-dice/jeongsan
```
