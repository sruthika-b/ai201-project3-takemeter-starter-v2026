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

**How I labelled them:** I labelled the first 15 comments cold and wrote brief reasons for those decisions. I then used AI assistance to label the remaining 185 comments, applying the distinctions in my first 15 labels and explanations. During that review, 85 of the initial draft labels changed; the other 100 were kept. The assisted labels and rationales are recorded in the CSV notes, and they should still be treated as AI-assisted judgments rather than independent human labels.

**Counts per label:**

| Label | Count | Share |
|---|---|---|
| `analysis` | 43 | 21.5% |
| `hot_take` | 24 | 12% |
| `reaction` | 65 | 32.5% |
| `advice` | 68 | 34% |
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

**Base model:**

**Settings:** <!-- epochs, learning rate, batch size, seed -->

**Anything I changed from the defaults, and why:**

**Split sizes:** <!-- train / val / test, and per-label counts in the test
split. If a label had fewer than about 8 in test, say so — it explains a lot
of next unit's variance. -->



---

## How I Used AI

<!-- Two specific moments — what you asked, what came back, what you changed.

     ⚠️ Plus disclosure of any pre-labelling. If you had a model pre-label a
     batch and then read and corrected every one, say that. It's an allowed
     workflow and disclosing it costs you nothing. Not disclosing it is the
     problem. -->

**Moment 1**

- *What I asked for:*
- *What came back:*
- *What I changed:*

**Moment 2**

- *What I asked for:*
- *What came back:*
- *What I changed:*

**Pre-labelling disclosure:**

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
