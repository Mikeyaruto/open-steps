[English](README.md) · [Español](README.es.md) · [Français](README.fr.md) · [Русский](README.ru.md) · [Українська](README.uk.md) · **한국어** · [中文](README.zh.md)

# Open Steps

*2026년 9월 12일 기준 영어 README를 옮긴 것입니다. 측정 수치, 내부 구조, 기여자 안내는 [영어 README](README.md)에 있습니다. 그쪽은 매주 바뀌고, 이 페이지에는 잘 바뀌지 않는 내용만 담았습니다. AI의 도움을 받아 옮겼고, 아직 한국어 원어민이 검토하지 않았습니다. 틀린 곳이 보이면 pull request로 고쳐 주세요. Codex, Cursor, Gemini CLI 설치 방법은 [영어 README](README.md#other-agents-codex-cursor-gemini-cli)의 Quick start에 있습니다.*

**개발을 이끄는 사람이 개발 과정을 계속 볼 수 있게 해 주는 스킬 모음입니다. 세션, 결정, 다음 단계, 전체 그림, 모두 쉬운 말로.**

만든 사람: [Pavlo Kharmanskyi](https://github.com/kharmanskyi).

## 왜 만들었나

저는 엔지니어가 아닙니다. 20년 동안 제품 쪽에서 제품을 만들어 왔고, 지금 제 회사에는 개발자가 50명이 넘습니다. 그와 별개로 에이전트만 데리고, 엔지니어 없이 혼자 제품을 만들기 시작했습니다. 곧 벽에 부딪혔습니다. 에이전트는 일을 잘 해 놓고, 그 결과를 커밋 해시와 전문 용어로 설명합니다. 그래서 우리가 끝난 건지 아닌지 알 수가 없습니다. 일 자체는 문제가 없습니다. 코드를 읽지 않는 사람과 이야기하는 법을 아무도 에이전트에게 가르치지 않았을 뿐입니다.

이 팩이 그것을 가르칩니다. 에이전트 대신 코드를 쓰거나 코드를 검토하지는 않습니다. 몇 가지 중요한 순간에 에이전트가 당신에게 말하는 내용을 바꾸고, 전에는 "끝났습니다" 한마디로 넘어가던 자리에 증거를 요구합니다.

## 스킬이 하는 일

| 스킬 | 하는 일 | 켜지는 때 |
|---|---|---|
| `os-done-or-not` | 한 화면짜리 보고서와 판정: 끝났는지, 당신이 해야 할 일이 있는지, 새로 남은 빚이 있는지, 닫아도 되는지. "예"에는 반드시 근거가 붙습니다 | 작업이 끝났을 때, 또는 어떻게 됐는지 물을 때 |
| `os-step-by-step` | 기술을 모르는 사람도 따라갈 수 있는 번호 붙은 단계. 에이전트는 먼저 스스로 다 해 보고, 정말 당신만 할 수 있는 것만 요청합니다 | 에이전트가 당신에게 실행, 붙여넣기, 클릭, 승인, 테스트를 요청해야 할 때 |
| `os-ask-simple` | 쉬운 말로 된 질문, 나중에 드는 비용, 그리고 표시된 추천 하나 | 에이전트가 당신에게 질문이나 선택지를 낼 때 |
| `os-what-could-go-wrong` | 결정이 이미 실패했다고 가정하고 그 이유를 거꾸로 찾습니다. 결정에 관여하지 않은 새 에이전트가 맡고, 판정은 하나로 끝냅니다 | 되돌리기 어려운 일이 합의되기 직전: 계약, 구매, 마이그레이션, 출시 |
| `os-whats-next` | 검증되어 준비된 것을 먼저 마무리하고, 다음 작업 하나를 추천하며 이유를 쉬운 말로 설명합니다 | 무엇이 남았는지, 다음에 무엇을 할지 물을 때 |
| `os-check-work` | 다른 세션의 보고서를 그대로 믿지 않습니다. 주장 하나하나를 실제로 일어난 일과 대조하고, 어떻게 할지 말합니다 | 다른 세션이 끝났다고 말할 때 |
| `os-say-simple` | 어떤 글이든 사실과 나쁜 소식을 빼지 않고 쉬운 말로 다시 씁니다. 숫자를 말하면 정확히 그 개수의 요점을 줍니다 | 보고서, 댓글, 오류 메시지, 에이전트 자신의 답변 등 글이 엔지니어 말투일 때 |
| `os-big-picture` | `BIG-PICTURE.md` 파일 하나를 관리합니다: 이 제품이 무엇인지, 기능마다 어디까지 왔는지, 오래 손대지 않은 부분은 무엇인지, 대기 중인 일은 무엇인지. 이미 쓰는 이슈 트래커가 있으면 대기 목록을 티켓으로 여는 것을 제안합니다 | 프로젝트가 지금 어디쯤인지 물을 때, 또는 세션 보고서가 막 작성됐을 때 |

에이전트는 당신이 말하는 언어로 답합니다. 코드, 파일 이름, 명령은 영어 그대로입니다.

## 설치

Claude Code에서는 플러그인이 명령 한 번으로 스킬과 두 훅을 연결합니다. 스킬은 Codex, Cursor, Gemini CLI에도 설치됩니다. 복사 명령 하나로 스킬이 설치되고, 훅은 도구마다 설정 몇 줄이 필요합니다. [다른 에이전트](docs/other-agents.md)를 보세요 (영어).

저장소를 내려받습니다:

```bash
git clone https://github.com/kharmanskyi/open-steps.git
```

### Claude Code

아래 두 명령은 내려받은 폴더, 즉 이제 `open-steps/`가 들어 있는 폴더에서 실행합니다. 그 안에서 실행하지 않습니다. 플러그인으로 설치합니다:

```bash
claude plugin marketplace add ./open-steps && claude plugin install open-steps@open-steps
```

끝입니다. 스킬과 두 훅이 연결됐습니다. 무엇이 설치됐는지 확인:

```bash
claude plugin details open-steps
```

한 가지는 손으로 추가해야 하고, 어떤 설치 프로그램도 대신해 주지 못합니다: 당신의 `~/.claude/CLAUDE.md`에 짧은 블록 하나. 스킬은 모델이 쓰기로 선택하는 것입니다. 훅은 그것을 상기시키고, 이 블록은 그것을 규칙으로 만들어 긴 대화에서도 살아남게 합니다. 같은 폴더에서 명령 하나, 여러 번 실행해도 안전합니다:

```bash
grep -q 'os-done-or-not' ~/.claude/CLAUDE.md 2>/dev/null || cat open-steps/docs/routing-block.md >> ~/.claude/CLAUDE.md
```

나중에 플러그인만이 아니라 설치 전체를 점검하려면 에이전트 안에서 `/open-steps:os-install-check`를 실행합니다. 무엇이 연결됐고 무엇이 안 됐는지 말하고, 확인할 수 없던 곳은 "확인 안 함"이라고 씁니다.

## 업데이트와 제거

**Claude Code.** 업데이트: `open-steps/` 안에서 `git pull`, 그다음 `claude plugin update open-steps@open-steps`. 둘 다 필요합니다. 플러그인은 GitHub가 아니라 당신의 폴더에서 업데이트되고, 파일은 버전 번호가 바뀔 때만 설치된 복사본으로 옮겨집니다. 제거: `claude plugin uninstall open-steps`, 그다음 `CLAUDE.md`에서 블록을 지웁니다.

## 라이선스

MIT. 공개 팩이고 기여를 환영합니다. 규칙은 [CONTRIBUTING.md](CONTRIBUTING.md)에 있습니다 (영어).

Open Steps is an independent open-source project, not affiliated with or endorsed by the makers of the tools it runs on. Claude and Claude Code are trademarks of Anthropic. All other trademarks, including Codex, Cursor and Gemini, are the property of their respective owners.
