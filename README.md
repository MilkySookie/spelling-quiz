# 맞춤공책

메신저와 메일에서 자주 흔들리는 한국어 맞춤법을 빈칸으로 익히는 개인 연습 공책입니다. 서버가 없고, 점수·오답·약점은 이 브라우저 안에만 남습니다.

공개 주소: https://milkysookie.github.io/spelling-quiz/

A personal Korean spelling quiz for the words that slip in chat and email. There is no server. Scores, wrong answers, and weak spots stay in this browser.

Live site: https://milkysookie.github.io/spelling-quiz/

## 한국어

### 무엇인가

맞춤공책은 국립국어원 규범을 기준으로 한 맞춤법 퀴즈입니다. 문제 은행 226문항이 `index.html` 한 파일 안에 들어 있고, CSS와 스크립트도 그 파일에 함께 있습니다. 빌드 과정이 없습니다. 파일을 브라우저로 바로 열어도 동작합니다.

한 회차는 고른 범위에서 문제를 섞어 최대 20개를 냅니다. 보기를 고르면 바로 채점합니다. 정답은 초록 동그라미, 고른 오답은 빨간 줄입니다. 이어서 해설과 기억법이 나옵니다.

### 기능

- **한 회차 20문제.** 연습, 오답 복습, 약점 집중 모두 최대 20문항입니다. 범위에 문제가 더 적으면 있는 만큼만 냅니다.
- **범주 필터.** 홈에서 범주를 고르면 그 범주만 출제됩니다. 아무것도 고르지 않으면 226문항 전체입니다.
- **바로 채점.** 보기를 누르거나 숫자 키 1–4를 누르면 그 자리에서 맞음/틀림이 갈립니다. 정답은 초록(`#157a3c`) 동그라미, 오답은 빨강(`#d3122a`) 취소선입니다. 해설(`explain`)과 기억법(`tip`)이 함께 나옵니다.
- **오답노트.** 틀린 문항이 이 기기에 쌓입니다. 복습에서 맞히면 그 문항은 노트에서 빠집니다.
- **내 약점 분석.** 범주별 맞힌 비율과 자주 틀린 문항을 보여 줍니다. 막대는 80% 이상이면 초록, 60% 미만이면 빨강입니다.
- **약점 집중.** 오답노트에 남은 문항, 자주 틀린 문항, 정답률이 낮은 범주를 더 뽑아 최대 20문제를 냅니다. 풀이 기록이 8회 미만이면 아직 약점을 가리지 않고 무작위로 냅니다.
- **기록.** 최근 회차의 점수와 시간이 남습니다. 기록 지우기는 점수와 약점 통계만 지우고, 오답노트는 그대로 둡니다.
- **데이터는 브라우저에만.** 아래 키로 `localStorage`에 저장합니다. 계정, 서버, 외부 전송은 없습니다.
  - `matchum-note-v1` — 오답노트
  - `matchum-history-v1` — 회차 기록 (최근 40개)
  - `matchum-stats-v1` — 문항별 맞힘/틀림

### 로컬에서 실행

의존성을 설치하지 않습니다.

1. 이 저장소를 받습니다.
2. `index.html`을 브라우저로 엽니다. 주소가 `file://`로 시작해도 됩니다.
3. 정적 서버를 쓰고 싶으면 저장소 루트에서 예를 들어 `python3 -m http.server 8080`을 실행한 뒤 http://localhost:8080/ 을 엽니다.

GitHub Pages는 `main`에 푸시되면 `.github/workflows/pages.yml`이 저장소 루트를 https://milkysookie.github.io/spelling-quiz/ 에 배포합니다.

### 문제 추가와 수정

문제 은행은 `index.html`의 `<script>` 안에 있는 `const QUESTIONS = [ ... ];` 한 줄입니다. 다른 파일로 나뉘어 있지 않습니다. 문항을 고친 뒤에도 이 파일 하나만으로 `file://`에서 열려야 합니다.

각 문항은 객체이고 필드는 다음뿐입니다.

| 필드 | 형식 | 의미 |
| --- | --- | --- |
| `id` | 문자열 | 문항 고유 아이디. 오답노트와 통계가 이 값으로 문항을 찾습니다. 이미 있는 아이디는 바꾸지 마세요. |
| `category` | 문자열 | 범주. 홈 필터와 약점 분석에 쓰입니다. 새 범주라면 같은 스크립트의 `CATEGORY_ORDER`에도 넣으세요. |
| `scene` | 문자열 | 문장이 놓인 상황. 현재 값은 `메신저`, `메일`, `메모`, `공지`, `댓글`입니다. |
| `sentence` | 문자열 | 빈칸이 `____` 하나인 문장입니다. |
| `choices` | 문자열 배열 | 보기. 대부분 2개, 일부 3개입니다. 순서는 출제할 때 다시 섞입니다. |
| `answer` | 문자열 | 정답. `choices` 안의 값과 글자 그대로 같아야 합니다. |
| `explain` | 문자열 | 맞힌 뒤 보여 주는 해설입니다. |
| `tip` | 문자열 | 짧은 기억법입니다. |

예시:

```js
{
  "id": "doe-01",
  "category": "되/돼",
  "scene": "메신저",
  "sentence": "지금 나가면 안 ____. 비 오기 시작했어.",
  "choices": ["돼", "되"],
  "answer": "돼",
  "explain": "‘돼’는 ‘되어’를 줄인 말입니다. ‘안 돼’는 ‘안 되어’이므로 ‘돼’로 끝냅니다.",
  "tip": "자리에 ‘하’를 넣어 보세요. ‘안 해’가 자연스러우면 ‘돼’, ‘하면·하고·하지’처럼 ‘하’가 살면 ‘되’입니다."
}
```

현재 범주와 문항 수입니다.

| 범주 | 문항 |
| --- | --- |
| 되/돼 | 12 |
| 안/않 | 12 |
| 로서/로써 | 12 |
| 웬/왠 | 12 |
| 든/던 | 12 |
| 어떻게/어떡해 | 12 |
| 사이시옷 | 20 |
| 띄어쓰기 | 25 |
| 바라다 | 12 |
| 맞히다/맞추다 | 12 |
| 두음법칙 | 12 |
| 어미 | 12 |
| 부사 | 12 |
| 어휘 | 37 |
| 외래어 | 12 |
| **합계** | **226** |

### 표기 기준

정답은 국립국어원 규범을 따릅니다. 띄어쓰기, 사이시옷, 두음 법칙, 어미, 표준 어휘는 **한글 맞춤법**을 보고, 단어의 뜻과 표준 표기는 **표준국어대사전**을 봅니다. 해설에 조항이 적힌 문항은 그 조항이 근거입니다. 새 문항을 넣을 때도 같은 기준으로 정답을 확인한 뒤에 넣으세요.

## English

### What it is

맞춤공책 is a Korean spelling quiz grounded in National Institute of Korean Language norms. All 226 questions, the CSS, and the script live in one file, `index.html`. There is no build. Opening that file in a browser is enough, including from a `file://` URL.

Each round shuffles the selected range and asks up to 20 questions. A choice is graded immediately: a correct answer is circled in green, a wrong choice is struck through in red, and the explanation and memory tip appear with the grade.

### Features

- **20 questions per round.** Practice, wrong-answer review, and weak-spot practice each draw at most 20 questions. A smaller pool draws every question it has.
- **Category filter.** Selecting categories on the home screen limits the draw to those categories. Selecting none uses all 226 questions.
- **Instant green/red grading.** Click a choice, or press 1–4. Correct is a green circle (`#157a3c`). The choice you got wrong is a red strikethrough (`#d3122a`). The `explain` text and the `tip` appear at once.
- **오답노트 (wrong-answer notebook).** Missed questions accumulate on this device. Getting one right during review removes it from the notebook.
- **내 약점 분석 (my weak spots).** Category accuracy and the questions you miss most. A bar is green at 80% or above and red below 60%.
- **약점 집중 (focus on weak spots).** The round prefers notebook items, questions you miss, and categories under 80% once a category has at least 3 attempts, still capped at 20. With fewer than 8 recorded attempts the round stays random and says so.
- **Score history.** Recent rounds keep score and time. Clearing history removes scores and weak-spot stats only. The wrong-answer notebook stays.
- **Everything stays in the browser.** Three `localStorage` keys, no account and no server:
  - `matchum-note-v1` — wrong-answer notebook
  - `matchum-history-v1` — round history (latest 40)
  - `matchum-stats-v1` — per-question correct/wrong counts

### Run it locally

Nothing to install.

1. Clone this repository.
2. Open `index.html` in a browser. A `file://` URL is fine.
3. Or serve the repo root, for example `python3 -m http.server 8080`, then open http://localhost:8080/ .

A push to `main` runs `.github/workflows/pages.yml`, which publishes the repository root to https://milkysookie.github.io/spelling-quiz/ .

### Add or edit questions

The bank is the single statement `const QUESTIONS = [ ... ];` inside the `<script>` in `index.html`. It is not a separate file. After an edit, that one file must still work from `file://`.

Every question is an object with these fields:

| Field | Type | Meaning |
| --- | --- | --- |
| `id` | string | Stable id. The notebook and stats look questions up by this value. Do not change an id that already exists. |
| `category` | string | Category used by the filter and the weak-spot view. A new category also needs an entry in `CATEGORY_ORDER` in the same script. |
| `scene` | string | Setting of the sentence. Current values are `메신저`, `메일`, `메모`, `공지`, and `댓글`. |
| `sentence` | string | Sentence with one `____` blank. |
| `choices` | string array | Options. Most questions have two; some have three. Order is shuffled when the round starts. |
| `answer` | string | Correct option. Must equal one entry in `choices` exactly. |
| `explain` | string | Explanation shown after the answer. |
| `tip` | string | Short way to remember the rule. |

```js
{
  "id": "doe-01",
  "category": "되/돼",
  "scene": "메신저",
  "sentence": "지금 나가면 안 ____. 비 오기 시작했어.",
  "choices": ["돼", "되"],
  "answer": "돼",
  "explain": "‘돼’는 ‘되어’를 줄인 말입니다. ‘안 돼’는 ‘안 되어’이므로 ‘돼’로 끝냅니다.",
  "tip": "자리에 ‘하’를 넣어 보세요. ‘안 해’가 자연스러우면 ‘돼’, ‘하면·하고·하지’처럼 ‘하’가 살면 ‘되’입니다."
}
```

Counts: 되/돼 12, 안/않 12, 로서/로써 12, 웬/왠 12, 든/던 12, 어떻게/어떡해 12, 사이시옷 20, 띄어쓰기 25, 바라다 12, 맞히다/맞추다 12, 두음법칙 12, 어미 12, 부사 12, 어휘 37, 외래어 12. Total 226.

### What the answers follow

Answers follow National Institute of Korean Language standards. Spacing, 사이시옷, the initial-sound rule, endings, and standard vocabulary follow **한글 맞춤법**. Word meaning and standard spelling follow **표준국어대사전**. Where an explanation names a clause, that clause is the source. Check a new question against the same sources before adding it.
