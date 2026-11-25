# AGENTS.md

Instructions for AI coding tools working in this repository.

맞춤공책 is a personal Korean spelling quiz. The published site is the repository root on GitHub Pages: https://milkysookie.github.io/spelling-quiz/

## Structure

There is no build, no package manager, and no framework.

- `index.html` — the whole site. CSS is in the `<style>` in `<head>`. Behavior and the question bank are in the single `<script>` at the end of `<body>`. The page renders into `<div id="app">`.
- `.nojekyll` — empty file so a Jekyll build cannot rewrite the site. Leave it in place.
- `.github/workflows/pages.yml` — on every push to `main`, checks out the repo, runs `actions/configure-pages`, uploads the repository root with `actions/upload-pages-artifact` (`path: "."`), and publishes it with `actions/deploy-pages`.
- `README.md` — Korean and English documentation for people.

Do not add a bundler, a second HTML entry, or assets that `index.html` must fetch. The page has to keep working when opened directly via `file://`.

## Where the question bank lives

Inside `index.html`, in the `<script>`, one statement:

```js
const QUESTIONS = [ /* 226 objects, currently a single line */ ];
```

That line is the bank. Search for `const QUESTIONS`. It currently sits at line 466, immediately after the script opens. Category display order is the `CATEGORY_ORDER` array a few lines below it. A new category must be added there or the home screen will not list it in the intended order.

Each object has only these fields:

| Field | Required content |
| --- | --- |
| `id` | Unique string. Notebook and stats address the question by this id. Never rename an existing id. |
| `category` | One category name. Current set: `되/돼`, `안/않`, `로서/로써`, `웬/왠`, `든/던`, `어떻게/어떡해`, `사이시옷`, `띄어쓰기`, `바라다`, `맞히다/맞추다`, `두음법칙`, `어미`, `부사`, `어휘`, `외래어`. |
| `scene` | Short setting label. Current values: `메신저`, `메일`, `메모`, `공지`, `댓글`. |
| `sentence` | Korean sentence containing exactly one blank written as `____`. |
| `choices` | Array of distinct strings. Two choices is normal; three is used for a few items. |
| `answer` | Must be identical to one element of `choices`. Grading is `item.choice === q.answer`. |
| `explain` | Why that spelling is standard. |
| `tip` | A short memory aid shown with the explanation. |

Rounds call `shuffle` and take `Math.min(20, pool.length)` questions (`startRound`). Keep that cap.

## localStorage compatibility

All persistence goes through `storageGet` / `storageSet` in the same script. Keys and JSON shapes are part of the public data on the owner's browser. Do not rename them, do not wrap them in another object, and do not change field names.

| Key | Shape |
| --- | --- |
| `matchum-note-v1` | Object map keyed by question `id`. Value: `{ id, wrongCount, lastChoice, updatedAt }`. `loadNote` discards a stored array and returns `{}`. A wrong answer increments `wrongCount`. A later correct answer deletes that id. |
| `matchum-history-v1` | Array, newest first, capped at 40. Item: `{ at, mode, fallback, score, total, elapsed, cats, items }`. Each `items` entry is `{ id, choice, correct }`. `mode` is `"normal"` (practice), `"wrong"` (notebook review), or `"weak"` (weak-spot round). |
| `matchum-stats-v1` | Object map keyed by question `id`. Value: `{ correct, wrong, lastAt, lastChoice, recent }`. `recent` is `{ at, ok, choice }` and is capped at 12. If this key is missing, `loadStats` rebuilds it from history. |

`기록 지우기` writes `matchum-history-v1` to `[]` and `matchum-stats-v1` to `{}`. It does not clear `matchum-note-v1`. Keep that split.

`storageGet` accepts only a JSON object or array. A failed read sets `state.storageError` and the quiz still runs without saving. Keep that fallback.

## Rules

1. **One self-contained `index.html` that works via `file://`.** Keep CSS, script, and `QUESTIONS` inline. No network request is required to quiz, grade, or read saved progress. Do not introduce module imports, external stylesheets, fonts that the layout depends on, or a generated bundle.
2. **Verify every answer against 한글 맞춤법 and 표준국어대사전.** These are the National Institute of Korean Language standards. Spacing, 사이시옷, 두음 법칙, endings, and standard vocabulary come from 한글 맞춤법. Meaning and the standard headword come from 표준국어대사전. Do not ship a question whose `answer` you have not checked. When a clause number is the authority, name it in `explain` or `tip`. If two spellings are both standard, do not mark one of them wrong.
3. **Green means correct. Red means wrong.** Do not swap them. Correct uses `--good` (`#157a3c`): class `choice.is-right` draws the green circle, `verdict.good` / `explain.good` / `pick-good` / `wfill.good` use the same green. Wrong uses `--pen` (`#d3122a`): class `choice.is-wrong` draws the red strikethrough, `verdict.bad` / `explain.bad` / `pick-bad` / `wfill.bad` use the same red. The filled blank of the correct sentence stays green.
4. **Do not break the localStorage keys or their compatibility.** Existing notebooks, scores, and weak-spot stats must still load after a change. Adding an optional field is fine. Renaming a key, an `id`, or a field listed above is not.
5. **Leave Pages deploy pointed at the repo root.** `pages.yml` must keep `actions/configure-pages`, `actions/upload-pages-artifact` with `path: "."`, and `actions/deploy-pages`, and `.nojekyll` must remain at the root.
