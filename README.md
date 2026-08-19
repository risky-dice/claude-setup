# claude-setup

새 노트북에서 스킬 세 개를 한 줄로 설치하는 마켓플레이스.

```bash
claude plugin marketplace add risky-dice/claude-setup && claude plugin install jeongsan@gosu && claude plugin install live-editor@gosu && claude plugin install ponytail@gosu
```

| 플러그인 | 출처 | 비고 |
|---|---|---|
| `jeongsan` | risky-dice/jeongsan | 내가 만듦 |
| `live-editor` | risky-dice/live-editor | 내가 만듦. 첫 사용 시 Claude가 `npm install` + `pip install pymupdf`를 자동 실행(40~70초) |
| `ponytail` | DietrichGebert/ponytail | 남의 스킬. 복사본이 아니라 상류를 가리키므로 업데이트가 따라온다 |

설치 후 확인:

```bash
claude plugin list
```
