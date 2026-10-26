# TakeMeter

> ### 👋 Start here
>
> **New to this repo? Read [RUNNING.md](RUNNING.md) first** — setup, the
> notebook, the baseline, and what to do when something breaks.
>
> Once `python test.py` passes:
>
> ```bash
> head -5 data/practice_labels.csv     # the shape your labels.csv needs
> ```
>
> Then open `takemeter.ipynb` **in this folder** — in VS Code, or with
> `jupyter notebook` if you prefer. Pick the kernel: the `.venv` inside this
> project. Run section 1, which reports the hardware you'll be training on.
> Everything else waits until you have data.
>
> Nothing to upload, nothing to connect, no accounts and no keys. The notebook
> runs on your machine and writes next to your code.
>
> **The rest of this file is your submission.** Fill it in as you go.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     HOW TO USE THIS FILE

     Unit 5 asks for the first five sections. Unit 6 adds the five below them.

     Everything is pasted as TEXT. No screenshots, no images.

     ⚠️ The confusion matrix especially. The notebook prints one as a markdown
     table, ready to copy. A screenshot of a matrix earns nothing. Paste the
     table.
     ───────────────────────────────────────────────────────────────────────── -->

<!-- ═══════════════════════ UNIT 5 — THE BUILD ═══════════════════════ -->

## What This Does

<!-- Your community, and what your classifier sorts posts into. Three or four
     sentences. -->

This project uses 200 public comments from the Fitness Stack Exchange community. The classifier sorts each comment by its main purpose: evidence-backed `analysis`, a confident unsupported `hot_take`, an immediate `reaction`, or actionable `advice`. The examples come from fitness discussions, where short replies can mix personal experience, opinions, explanations, and recommendations. Each row keeps its source link and license information for traceability.

---

## Label Taxonomy

<!-- Each label: a one-sentence definition and two real examples from your
     reading. Then your decision rule for the hardest boundary.

     The decision rule is worth a point on its own and it's the thing most
     people leave out. Every taxonomy has a hardest boundary. Name yours. -->

### `analysis`

**Definition:** A comment that explains or evaluates a fitness claim using a reason, comparison, measurement, or cited evidence.

**Example 1:**
> “@Samuel: there is no exact way to tell for yourself, but various studies and anecdotal evidence suggest ~48 hours is good enough for the average trainee/workout.”

**Example 2:**
> “82 year old guy who has been hill running for decades will generally have more training than someone who never jogged”

### `hot_take`

**Definition:** A confident, broad opinion about fitness that is presented without specific evidence or reasoning to support it.

**Example 1:**
> “For future readers, don't sacrifice quality for speed. Natural bodybuilding is a hobby of patience.”

**Example 2:**
> “If sweating made you thin, the American South would not have an obesity epidemic.”

### `reaction`

**Definition:** A brief personal response, feeling, or expression of approval, surprise, humor, or encouragement in the conversation.

**Example 1:**
> “BTW congrats on the progress. That first 50 pounds, 8-mile walks, and quitting smoking are already tremendous achievements!”

**Example 2:**
> “i actually enjoy treadmills because they don't let you slow down!”

### `advice`

**Definition:** A comment whose main purpose is to recommend a specific action, choice, or next step.

**Example 1:**
> “may I suggest focusing more on nutrition for losing weight; and simply enjoy your bike riding.”

**Example 2:**
> “@Andrew: the whole point of the % of MHR is to work at a specific level. If you are working at less than 55% of your MHR, you should work harder, not put on a hoodie.”

### The hardest boundary

**Which two labels:** `analysis` and `advice`

**The decision rule I used every time:** If the comment mainly explains why a fitness claim is true or false, label it `analysis`, even if it mentions an action. If its main point tells someone what to do next, label it `advice`, even if it includes a reason or evidence.

---

## The Dataset

<!-- Where you collected from, how you labelled, your counts, and three hard
     cases. -->

**Where the posts came from:** 200 public comments from the Fitness Stack Exchange community, collected through the Stack Exchange API from fitness discussions. Each row preserves the comment's source URL, author display name, and the CC BY-SA license version supplied by the API.

**How I labelled them:** I labelled the first 15 comments cold and wrote brief reasons for those decisions. I then used AI assistance to label the remaining 185 comments, applying the distinctions in my first 15 labels and explanations. I proof read the categorization done by AI and have changed 13 labels which were labelled wrong by AI.

**Counts per label:**

| Label | Count | Share |
|---|---|---|
| `analysis` | 43 | 21.5% |
| `hot_take` | 17 | 8.5% |
| `reaction` | 69 | 34.5% |
| `advice` | 71 | 35.5% |
| **Total** | **200** | **100%** |

**Three hard cases**

<!-- Any post that made you pause: what it was, which two labels it could have
     been, and what you chose. These are worth more than the easy 190. -->

**1.**
> *The post:* “@Samuel: there is no exact way to tell for yourself, but various studies and anecdotal evidence suggest ~48 hours is good enough for the average trainee/workout. In other words, unless you are moving HUGE weights/volume, working out the same muscles/motions 2-3 days per week on non-consecutive days tends to be ideal.”
>
> *Could have been:* `analysis` or `advice`
>
> *I chose:* `analysis`, because the comment explains its recommendation with study references and personal evidence. The training schedule is advice, but the explanation is the main point.

**2.**
> *The post:* “For future readers, don't sacrifice quality for speed. Natural bodybuilding is a hobby of patience.”
>
> *Could have been:* `hot_take` or `advice`
>
> *I chose:* `advice`, because “don't sacrifice quality” directly recommends what readers should do. I changed the original `hot_take` draft label.

**3.**
> *The post:* “Sorry, this is simply wrong. Human metabolism is too complex for the nutritional value of food to be reduced to a simple linear scale like calories. This is strictly anecdotal, but I personally lost more weight on a higher calorie low-carb diet in a couple of months than I did on a lower calorie high-carb diet for over a year.”
>
> *Could have been:* `reaction` or `analysis`
>
> *I chose:* `analysis`, because after the emotional opening, the comment explains its disagreement and supports it with a personal comparison. I changed the original `reaction` draft label.

---

## The Training Run

<!-- Your starting model, your settings, and anything you changed and why. -->

**Base model:** `distilbert-base-uncased`

**Settings:** 3 epochs, learning rate 2e-5, batch size 16, seed 42, max length 128 tokens. Trained on an Apple Silicon GPU (MPS), torch 2.14.1.

**Anything I changed from the defaults, and why:** Nothing in the training settings. The only change was `LABELS`, which I extended to `["analysis", "hot_take", "reaction", "advice"]` because my taxonomy has a fourth label, `advice`, for comments whose main purpose is recommending an action.

**Split sizes:** 140 train / 30 validation / 30 test (70/15/15, stratified). Test split per label: `advice` 11, `reaction` 10, `analysis` 6, `hot_take` 3. `analysis` and `hot_take` both have fewer than 8 test posts. `hot_take` has only 3, so a single post changes its F1 a lot, and I expect those two labels to swing the most between seeds.

---

## How I Used AI

<!-- Two specific moments — what you asked, what came back, what you changed.

     ⚠️ Plus disclosure of any pre-labelling. If you had a model pre-label a
     batch and then read and corrected every one, say that. It's an allowed
     workflow and disclosing it costs you nothing. Not disclosing it is the
     problem. -->

**Moment 1**

- *What I asked for:* After labelling the first 15 comments cold, I asked Claude Code to check my labelling criteria (the README definitions, `label_definitions.txt` and my 15 cold labels) and label the remaining 185 comments in `labels.csv`, which already had AI draft labels from an earlier pass.
- *What came back:* It summarised the rules my cold labels follow (for example, personal experience used as evidence counts as `analysis`, and comments about the question itself count as `reaction`) and changed 13 of the 185 draft labels, writing the old label, the new label and the reason into each row's `note`. It also pointed out that `hot_take` had dropped to 17 posts (8.5%), so it would have only about 3 test posts, and it found places where my README examples didn't match my own cold labels.
- *What I changed:* I kept the 13 changes and had the README counts table updated to 43 / 17 / 69 / 71. I didn't let it change any of my 15 cold labels; those stayed my own calls. Its warning about how small `hot_take` is shaped my criteria: I gave `hot_take` no F1 target of its own and covered it through macro F1 instead.

**Moment 2**

- *What I asked for:* Five numbered criteria for `criteria.md`, covering at least three of the five areas (accuracy, per-label performance, balance, consistency, confidence).
- *What came back:* Five targets covering accuracy, per-label F1, macro F1, prediction balance and confidence, each with a reason of three to five sentences tied to my label counts and the roughly 30-post test split.
- *What I changed:* I checked the draft against the brief, which asks for "a sentence or two" on why each number, and had every reason cut to two sentences. I also had the macro F1 reason rewritten to show its arithmetic (0.65 on `advice` and `reaction`, 0.45 on `analysis` and nothing right on `hot_take` gives about 0.44), so the 0.45 target can be checked rather than taken on trust.

**Pre-labelling disclosure:** Yes. I labelled rows 1 to 15 myself without AI help (`note` = `cold`). Rows 16 to 200 were pre-labelled by AI. In a first review pass, guided by my 15 cold labels, 85 of those draft labels changed. In a second pass, Claude Code checked them again against my cold-label rules and changed 13 more, and I accepted those changes. Every AI-assisted row says so in its `note`, along with the old and new label wherever one changed and the comment's source link and license. These 185 labels should be read as AI-assisted judgments that I reviewed, not as independent human labels.

<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

<!-- Both models on the same posts. `python baseline.py --trained results.json`
     prints this table for you. -->

| Measure | Baseline | Trained | Difference |
|---|---|---|---|
| Overall accuracy |  |  |  |
| Macro F1 |  |  |  |
| F1 — `label_one` |  |  |  |
| F1 — `label_two` |  |  |  |

**What I predicted before I looked:**
<!-- Milestone 1 asks you to write this BEFORE seeing the trained numbers. A
     prediction made afterwards isn't one. -->

**What the gap actually means:**
<!-- If the baseline matched your trained model, your fine-tuning added
     nothing — and that is a real finding, not a failure. Say it plainly. -->



---

## Run Log — Before

<!-- Five criteria across three seeds. The notebook's section 6 prints the
     spread table; the Target and Verdict columns are yours. -->

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1.  |  |  |  |  |  |
| 2.  |  |  |  |  |  |
| 3.  |  |  |  |  |  |
| 4.  |  |  |  |  |  |
| 5.  |  |  |  |  |  |

### Confusion matrix

<!-- ⚠️ TYPED AS A MARKDOWN TABLE. The notebook prints one ready to paste.
     An image of a matrix earns nothing. -->

| true \ predicted |  |  |  |
|---|---|---|---|
| **** |  |  |  |
| **** |  |  |  |
| **** |  |  |  |

**My biggest off-diagonal number, and what it means:**
<!-- Not "the model made mistakes" — WHICH boundary it didn't learn, and which
     direction. "7 real analysis posts were called hot_take and only 3 went the
     other way" is a direction, not just an error rate. -->



---

## Verdicts and Diagnoses

<!-- MET or MISSED against LAST UNIT's target. The target has to hold across
     all three seeds, not turn up sometimes. -->

| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 |  |  |  |  |
| 2 |  |  |  |  |
| 3 |  |  |  |  |
| 4 |  |  |  |  |
| 5 |  |  |  |  |

**Diagnoses**

<!-- For each miss: the cause, and how you know. The four common causes are:
     too few examples for a label, a boundary you applied inconsistently, a
     genuinely hard label pair, and a task the model can't reach from this
     much data.

     ⚠️ Use your agreement report as evidence. It is the only instrument you
     have that can tell a LABELLING problem from a MODEL problem, and this
     section is graded on whether you used it that way. -->



---

## Agreement Report

<!-- Your rate against the staff set, and every disagreement adjudicated.

     Remember you labelled these 30 under the STAFF taxonomy in
     data/staff_taxonomy.md, not your own — so every argument below is made
     from those definitions and those decision rules. -->

**Agreement rate:** ___ / 30 = ___%

<!-- Nobody grades this number. A 60% who argues every disagreement from the
     stated rules beats a 95% who wrote "staff was right" nine times. Several
     of the 30 were chosen because they're genuinely ambiguous — you should be
     winning some of these. -->

**Disagreements**

<!-- Three lines each: the post, both labels, and who you think is right and
     why — grounded in the staff definitions you were both applying.

     Then sort each into one of three piles:
       (a) the rule covered it and I applied it loosely → a consistency problem
       (b) the rule genuinely doesn't say               → a gap in the definitions
       (c) the rule is ambiguous here and my reading is defensible → argue it.
           This is a legitimate win.

     Pile (a) is the one that matters most for your diagnosis: if you applied a
     written rule two different ways on 30 posts, that is direct evidence about
     what you did across your own 200. -->

**1.**
> *The post:*
>
> *Staff said / I said:*
>
> *My call, and why:*
>
> *Which pile:*

**2.**
> *The post:*
>
> *Staff said / I said:*
>
> *My call, and why:*
>
> *Which pile:*

**What the pattern in my disagreements tells me:**



---

## The Improvement

**What I changed:**

**Which diagnosis pointed at it:**

### Run Log — After

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1.  |  |  |  |  |  |
| 2.  |  |  |  |  |  |
| 3.  |  |  |  |  |  |
| 4.  |  |  |  |  |  |
| 5.  |  |  |  |  |  |

**Did it help, and how do I know:**

<!-- If it didn't, say so. Relabelling that didn't help is a genuinely
     interesting result and earns full credit. -->



---

## What's Still Broken

<!-- For each criterion still missed: what you'd do, and why you stopped. -->



**The gap between what I meant my labels to capture and what the model
learned:**
<!-- Two sentences. Your confusion matrix is the evidence. -->



<!-- ═════════════════════════════════════════════════════════════════════

     SUBMISSION CHECKLIST — unit 5

       [ ] criteria.md has five numbered criteria, each naming a NUMBER
       [ ] Each has a reason underneath tied to your data or taxonomy
       [ ] labels.csv: at least 150 rows, text/label/note, ONE file not split
       [ ] No label above 70%
       [ ] All five unit 5 sections have real content
       [ ] Label Taxonomy includes the decision rule for your hardest boundary
       [ ] The Dataset includes three hard cases
       [ ] results.json and test_split.csv committed (the notebook does this)
       [ ] At least four new commits
       [ ] Repository URL submitted — WRITE IT DOWN

     SUBMISSION CHECKLIST — unit 6

       [ ] Baseline vs. Trained table, with your prediction written beforehand
       [ ] Run Log — Before, five criteria across three seeds
       [ ] Confusion matrix TYPED AS A MARKDOWN TABLE
       [ ] A verdict on every criterion
       [ ] A diagnosis for every miss, using the agreement report as evidence
       [ ] Agreement Report with every disagreement adjudicated
       [ ] One improvement, with Run Log — After
       [ ] What's Still Broken
       [ ] results_three_seeds_before.json, results_three_seeds_after.json,
           baseline_results.json and
           agreement_results.json committed
       [ ] At least four new commits
       [ ] The SAME repository URL as last unit

     Do not delete and recreate this repository.
     ═════════════════════════════════════════════════════════════════════ -->

---

📖 **How to run this project: [RUNNING.md](RUNNING.md)**
