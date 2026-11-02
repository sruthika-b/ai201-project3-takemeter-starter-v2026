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

**Moment 3**
- *What I asked for:* Can you explain what each criteria means in depth and how can we improve each criteria and the list of debugging techniques for each.
- *What came back:* Detailed explanation of all the criteria and techniques to improve them.
- *What I changed:* This helped me in deciding the best move to take forward for the model improvement.

<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

| Measure | Baseline | Trained | Difference |
|---|---|---|---|
| Overall accuracy | 0.267 | 0.533 | +0.267 |
| Macro F1 | 0.193 | 0.319 | +0.126 |
| F1 — `advice` | 0.000 | 0.611 | +0.611 |
| F1 — `analysis` | 0.345 | 0.000 | -0.345 |
| F1 — `hot_take` | 0.000 | 0.000 | +0.000 |
| F1 — `reaction` | 0.429 | 0.667 | +0.238 |

**What I predicted before I looked:**
I expect the trained model to beat the baseline on accuracy, especially on advice and reaction, my two largest labels, because it learns from my verified 200 labelled Fitness Stack Exchange comments, while the baseline only sees my four label definitions.

**What the gap actually means:**
Fine-tuning helped, but only a little. The trained model got 16 of 30 posts right against the baseline's 8, but it predicted advice for 25 of the 30 posts and reaction for the other 5, and never predicted analysis or hot_take. Always guessing advice would already score 0.367, so fine-tuning added only 5 correct posts beyond that, all of them reaction comments.

The baseline failed the opposite way: it never predicted advice and called 23 of the 30 posts analysis. That's why it's the only model with any analysis F1 (0.345).

So my prediction was half right. The trained model beat the baseline on accuracy, advice and reaction, but mostly by learning that those are my two biggest labels, not by learning to tell all four apart. Neither model learned analysis or hot_take, which have only 6 and 3 test posts.

---

## Run Log — Before

| Measure | Seed 42 | Seed 7 | Seed 2024 | Spread |
|---|---|---|---|---|
| Overall accuracy | 0.533 | 0.433 | 0.500 | 0.100 |
| Macro F1 | 0.319 | 0.239 | 0.299 | 0.081 |
| F1 — `analysis` | 0.000 | 0.000 | 0.000 | 0.000 |
| F1 — `hot_take` | 0.000 | 0.000 | 0.000 | 0.000 |
| F1 — `reaction` | 0.667 | 0.400 | 0.625 | 0.267 |
| F1 — `advice` | 0.611 | 0.556 | 0.571 | 0.056 |

### Confusion matrix

| true \ predicted | analysis | hot_take | reaction | advice |
|---|---|---|---|---|
| **analysis** | 0 | 0 | 0 | 6 |
| **hot_take** | 0 | 0 | 0 | 3 |
| **reaction** | 0 | 0 | 5 | 5 |
| **advice** | 0 | 0 | 0 | 11 |

**My biggest off-diagonal number, and what it means:**
<!-- Not "the model made mistakes" — WHICH boundary it didn't learn, and which
     direction. "7 real analysis posts were called hot_take and only 3 went the
     other way" is a direction, not just an error rate. -->
All 6 real analysis posts were called advice, and none went the other way. The same 6 → advice appears on all three seeds. This is the boundary I named as my hardest, analysis vs advice, and the model didn't learn it at all: it treats advice as the default for anything that isn't clearly a reaction. The cause is probably partly the data, because many of my analysis comments also contain recommendation wording ("you should", "focus on"), and partly the imbalance, because advice is my largest label, so guessing it is the cheapest way for the model to lower its loss. The same thing happens to hot_take (3 of 3 → advice) and to half of reaction (5 of 10 → advice).

---

## Verdicts and Diagnoses

<!-- MET or MISSED against LAST UNIT's target. The target has to hold across
     all three seeds, not turn up sometimes. -->

| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 | Overall accuracy on the held-out test split | ≥ 0.55 on every seed | MISSED | 0.533 / 0.433 / 0.500 across seeds 42 / 7 / 2024 which is below 0.55 on all three |
| 2 | `advice` and `reaction` F1 | ≥ 0.60 each, on every seed | MISSED | `advice` 0.611 / 0.556 / 0.571 (under 0.60 on seeds 7 and 2024); `reaction` 0.667 / 0.400 / 0.625 (under 0.60 on seed 7) |
| 3 | Macro F1 | ≥ 0.45 on every seed | MISSED | 0.319 / 0.239 / 0.299; `analysis` and `hot_take` scored 0.000 on every seed, which caps macro F1 |
| 4 | No single label gets more than 50% of test predictions | ≤ 50% on every seed | MISSED | `advice` received 25 / 25 / 24 of 30 predictions (83% / 83% / 80%), from the confusion-matrix column totals |
| 5 | Most-confident third beats least-confident third by at least 0.15 accuracy | Gap ≥ 0.15 on every seed | MET | 0.70 − 0.30 = 0.40, 0.50 − 0.20 = 0.30, 0.70 − 0.30 = 0.40; but mean confidence is only about 0.35, barely above the 0.25 of an even four-way guess |

**Diagnoses**

<!-- For each miss: the cause, and how you know. The four common causes are:
     too few examples for a label, a boundary you applied inconsistently, a
     genuinely hard label pair, and a task the model can't reach from this
     much data.

     ⚠️ Use your agreement report as evidence. It is the only instrument you
     have that can tell a LABELLING problem from a MODEL problem, and this
     section is graded on whether you used it that way. -->

All four misses come from the same pattern on my labels.csv model: it predicts advice for 24–25 of the 30 test posts on every seed, sometimes reaction, and never analysis or hot_take.

1. Accuracy (missed on all seeds): a task the model can't reach from this much data, made worse by imbalance. My 200 posts split into about 140 for training. With batch size 16 and 3 epochs, that's only about 27 weight updates in total, and the model's mean confidence is about 0.35, barely above the 0.25 of an even four-way guess. It barely moved from its starting point and fell back on my most common label (advice, 35.5% of labels.csv). Always guessing advice would score 0.367, and the model only beats that by picking up some reaction posts.

2. advice and reaction F1 (missed on seeds 7 and 2024): a side effect of the same collapse. advice has perfect recall but low precision (about 0.44 on seed 42), because analysis, hot_take and half of reaction all get sent there. reaction precision is high (1.0 on seed 42) but recall depends on the split (3 to 5 of 10 found), which is why its F1 moved 27 points between seeds. These are my two largest labels (71 and 69 posts) with the clearest wording cues, so the issue isn't too few examples. The model hasn't been trained enough to use those cues for anything but the most obvious reaction posts.

3. Macro F1 (missed on all seeds): too few examples, plus a genuinely hard label pair. analysis has about 30 training posts and hot_take only about 12, and both scored 0.000 F1 on every seed. All 6 analysis test posts went to advice on every seed, which is the analysis/advice boundary I named as my hardest. In labels.csv, 10 of my 43 analysis posts (23%) contain recommendation wording such as "should", "try" or "focus on", against 47 of 71 advice posts (66%). So the wording does overlap, but most analysis posts don't look like advice on the surface. That points more to too few analysis examples than to the pair being impossible to separate. My agreement report (29/30 with staff) suggests I apply a written rule consistently, so I don't think inconsistent labelling is the main cause. Two caveats: the staff set has no advice label, so it can't test this boundary, and rows 16–200 of labels.csv were AI-pre-labelled and then reviewed.

4. Prediction balance (missed on all seeds): lopsided data and an undertrained model. 70% of my posts are advice or reaction, and only 8.5% are hot_take. advice received 80–83% of predictions on every seed. Fixing this needs more training so the small labels actually get learned, or class weighting, so that mistakes on analysis and hot_take cost more during training.
---

## Agreement Report

<!-- Your rate against the staff set, and every disagreement adjudicated.

     Remember you labelled these 30 under the STAFF taxonomy in
     data/staff_taxonomy.md, not your own — so every argument below is made
     from those definitions and those decision rules. -->

**Agreement rate:** 29 / 30 = 97%

<!-- Nobody grades this number. A 60% who argues every disagreement from the
     stated rules beats a 95% who wrote "staff was right" nine times. Several
     of the 30 were chosen because they're genuinely ambiguous — you should be
     winning some of these. -->

**Disagreements**

**1.**|
> *The post:* I tracked sleep against session quality for eleven weeks. Under seven hours the next day's intervals were 6-8 seconds per mile slower, every time bar one. Small sample, one runner, but it's consistent enough that I've stopped scheduling hard days after late shifts.
>
> *Staff said / I said:* `analysis`/`hot_take`
>
> *My call, and why:* This post was based on personal analysis and not based on any established facts or analysis, and the author stopped scheduling hard days after late shifts, which is a personal behvioral change for his own analysis and findings, therefore I felt that, this post is a hot take rather than an analysis.
>
> *Which pile:* (c)

**What the pattern in my disagreements tells me:**
This is a borderline case where the statement can fit in both analysis and hot_take category, but I feel like I could rate personal takes based on thorough analysis could also be considered as 'analysis' category.

---

## The Improvement

**What I changed:** I raised EPOCHS from 3 to 10 in the notebook's settings cell. Nothing else changed: learning rate 2e-5, batch size 16, max length 128, the same labels.csv, and the same three seeds (42, 7, 2024), so the before and after runs compare directly.

**Which diagnosis pointed at it:** My diagnosis for criterion 1 found that the model was undertrained, not mislabelled. With about 140 training posts and batch size 16, 3 epochs gave only about 27 weight updates. Mean confidence was about 0.35, barely above a four-way guess, and the model predicted advice for 80–83% of posts while never predicting analysis or hot_take. My 29/30 agreement with staff gave no evidence of a boundary I'd applied two ways, so relabelling wasn't justified. Even analysis, with about 30 training posts, scored 0, so adding more examples wasn't the first fix either. The model needed more training on the data it already had.

### Run Log — After

| Measure | Seed 42 | Seed 7 | Seed 2024 | Spread |
|---|---|---|---|---|
| Overall accuracy | 0.800 | 0.600 | 0.700 | 0.200 |
| Macro F1 | 0.630 | 0.452 | 0.509 | 0.178 |
| F1 — `analysis` | 0.833 | 0.500 | 0.444 | 0.389 |
| F1 — `hot_take` | 0.000 | 0.000 | 0.000 | 0.000 |
| F1 — `reaction` | 0.870 | 0.667 | 0.870 | 0.203 |
| F1 — `advice` | 0.818 | 0.643 | 0.720 | 0.175 |

### Criterion table - after
| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 | Overall accuracy on the held-out test split | ≥ 0.55 on every seed | MET | 0.800 / 0.600 / 0.700 across seeds 42 / 7 / 2024 which is above 0.55 on all three |
| 2 | `advice` and `reaction` F1 | ≥ 0.60 each, on every seed | MET | `advice` 0.818 / 0.643 / 0.720, `reaction` 0.870 / 0.667 / 0.870 (all the seeds are above 0.60) |
| 3 | Macro F1 | ≥ 0.45 on every seed | MET | 0.630 / 0.452 / 0.509, (seed 7 is only 0.002 above the target) |
| 4 | No single label gets more than 50% of test predictions | ≤ 50% on every seed | MISSED | `advice` received 13 / 17 / 14 of 30 predictions (43% / 57% / 47%), from the confusion-matrix column totals - missed on seed 7 |
| 5 | Most-confident third beats least-confident third by at least 0.15 accuracy | Gap ≥ 0.15 on every seed | MISSED | 1.00 − 0.80 = 0.20, 0.60 − 0.50 = 0.10, 1.00 − 0.50 = 0.50, missed on seed 7 |

**Did it help, and how do I know:**

Yes, on three of the five criteria, and on all three seeds rather than just a lucky one. Raising EPOCHS from 3 to 10 took accuracy from 0.533 / 0.433 / 0.500 to 0.800 / 0.600 / 0.700, and macro F1 from 0.319 / 0.239 / 0.299 to 0.630 / 0.452 / 0.509. Criteria 1, 2 and 3 went from MISSED to MET. The clearest sign the model actually learned rather than just guessing differently is analysis: it scored 0.000 F1 on every seed before and 0.833 / 0.500 / 0.444 after, because the model now predicts analysis at all. It also stopped defaulting to advice, which fell from 80–83% of predictions to 37–57%.

| true \ predicted | analysis | hot_take | reaction | advice |
|---|---|---|---|---|
| **analysis** | 5 | 0 | 1 | 0 |
| **hot_take** | 0 | 0 | 1 | 2 |
| **reaction** | 0 | 0 | 10 | 0 |
| **advice** | 1 | 0 | 1 | 9 |

---

## What's Still Broken

- hot_take is still 0.000 on every seed, and the model never predicts it. With about 12 training posts, more epochs wasn't enough. That supports the diagnosis that this label has too few examples, which is the fix to try next.
- Seed 7 is the weak split each time. It caused both misses and the near-miss on macro F1.
- Accuracy now swings 20 points across seeds, double the 10-point warning line. - That's the small test set showing up: 30 posts at 3.3 points each.
- Confidence went up but is still modest: about 0.58 when the model is right against 0.51 when it's wrong. It was about 0.35 before.

**The gap between what I meant my labels to capture and what the model
learned:**
I meant my labels to capture what a comment is trying to do and whether it supports it. The model mostly learned wording instead: "you should" means advice, questions and jokes mean reaction, and explanations mean analysis. hot_take shows the gap most clearly. It is defined by evidence that is missing, which isn't visible in the words, so all 3 hot_take posts were misread (2 as advice, 1 as reaction), and the model never predicted hot_take once. That 2 (hot_take → advice) is the biggest off-diagonal number. Elsewhere the model does well: reaction 10 of 10, advice 9 of 11, analysis 5 of 6. My hardest boundary, analysis/advice, now has only 1 error in each direction, so on this split it learned that boundary, but not the difference between a supported claim and an unsupported one.

| true \ predicted | analysis | hot_take | reaction | advice |
|---|---|---|---|---|
| **analysis** | 5 | 0 | 1 | 0 |
| **hot_take** | 0 | 0 | 1 | 2 |
| **reaction** | 0 | 0 | 10 | 0 |
| **advice** | 1 | 0 | 1 | 9 |


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
