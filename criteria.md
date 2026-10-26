# Acceptance criteria — TakeMeter

Five criteria that say what "working" means for this classifier, written in
unit 5 **before** anything was trained.

**All five are yours this time.** None are given. You've had two projects of
practice.

An acceptance criterion names a number. *"The model is accurate"* is an
opinion. *"Every label has an F1 of at least 0.60 on the held-out set"* is a
criterion.

Under each, write a sentence or two on **why that number**. A reason that says
something about your data or your taxonomy earns credit — *"I picked 0.60 F1
for `reaction` because it's my smallest label and I only have about 50
examples of it"*. A reason that could be attached to any project does not.

> Missing your own targets next unit costs you nothing. Setting a target so
> easy you can't miss it does.

---

## Pick numbers you can defend

Not numbers that sound impressive. Three labels means a coin-flip guesser gets
about 33%, so a target of 0.40 is barely a target. Your number should sit
somewhere you'd honestly call useful.

**Cover at least three of these five areas.** They're here as prompts, not as a
form to fill in — a criterion that fits none of them is fine if it names a
number.

| Area | A question it could answer |
|---|---|
| Overall accuracy | How often does it need to be right to be worth using? |
| Per-label performance | Is one label allowed to be much worse than the others? |
| Balance | How lopsided can your label counts get before it's a problem? |
| Consistency | If someone else labelled the same posts, how often should you agree? |
| Confidence | Should a confident prediction be right more often than an unsure one? |

Two things worth knowing before you pick numbers, because both will affect
whether you hit them:

- **Your smallest label will have the jumpiest score.** If a label has 50
  examples, about 8 land in the test split. An F1 computed on 8 examples moves
  a lot between seeds. A target for that label should be looser than one for
  your biggest label, and saying so is a good reason.
- **Unit 6 tests across three seeds, and the target has to hold across all
  three.** A target of 0.65 against results of 0.71, 0.62, 0.68 is a **miss**.
  Pick with that in mind — it is stricter than it first sounds.

---

## 1. Overall accuracy of at least 0.55 on the held-out test split, on every seed

Measured as `accuracy` in `results.json`, and it must hold for all three seeds.

**Why this target:** `advice` is my largest label at 35.5%, so always guessing `advice` already scores about 0.36, and 0.55 (about 17 of the roughly 30 test posts) is a clear step above that without demanding more than my blurry `analysis`/`advice` boundary allows. One test post moves accuracy by 3.3 points, so I left room for seed-to-seed noise.

---

## 2. `advice` and `reaction` each have an F1 of at least 0.60, on every seed

Measured from `per_label` in `results.json`.

**Why this target:** These are my two largest labels (71 and 69 posts, about 10 to 11 each in test), and both have visible wording cues like "you should" for `advice` and questions, "+1" or ":)" for `reaction`. If the model can't reach 0.60 on the labels with the most data and the clearest cues, it hasn't learned my taxonomy.

---

## 3. Macro F1 of at least 0.45, on every seed

Measured as `f1_macro` in `results.json`.

**Why this target:** `hot_take` is my smallest label with only 17 posts (about 3 in test), so I didn't give it its own F1 target; macro F1 counts it equally instead. A model that never gets a `hot_take` right lands at about 0.44 even with 0.65 on `advice` and `reaction` and 0.45 on `analysis`, so 0.45 means it has to get some of them right.

---

## 4. No single label receives more than 50% of the model's predictions on the test split, on every seed

Measured from the column totals of the confusion matrix in `results.json`.

**Why this target:** 70% of my posts are `advice` or `reaction` and only 8.5% are `hot_take`, so the most likely failure with about 140 training posts is a model that keeps guessing the big labels. The test split is only about 35% `advice`, so a single label taking more than half the predictions means the imbalance, not the posts, is driving the guesses.

---

## 5. On every seed, the most-confident third of predictions is at least 0.15 more accurate than the least-confident third

Measured as `accuracy_most_confident_third` minus `accuracy_least_confident_third` in the `confidence` block of `results.json`.

**Why this target:** Many of my posts mix two purposes, such as an explanation followed by a recommendation, and I'd want confidence to flag those for a human to check. With about 10 posts per third, 0.15 means at least two more correct predictions in the confident third, a small but real gap.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     UNIT 6 — read this before you change anything above.

     If a criterion turns out to be BROKEN rather than merely unmet, you can
     revise it, and that earns credit. But never delete or edit the original
     line. Add the revision underneath, like this:

         ## 2. Every label performs acceptably

         The model performs well on all labels.

         **Why this target:** ...

         > **Revised in unit 6:** Every label has an F1 of at least 0.60 on
         > the held-out set.
         >
         > **Why revised:** "performs well" gave me nothing to check. I
         > couldn't produce a verdict from it at all.

     That's a revision because the criterion couldn't be MEASURED.

     Lowering a target because you missed it is not a revision, and it costs
     you the point:

         ✗ "Overall accuracy of at least 0.65" → "at least 0.55", because
            0.65 turned out to be optimistic for 200 examples.

     A number you missed stays where it is, gets diagnosed, and gets a fix
     attempted. That's where the points are.
     ───────────────────────────────────────────────────────────────────────── -->
