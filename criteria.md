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

**Why this target:** `advice` is my largest label at 35.5% of the 200 posts, so a model that always guesses `advice` already scores about 0.36, and a random guess across four labels scores 0.25. 0.55 means about 17 of the roughly 30 test posts are correct, which is about 20 points better than always guessing `advice`. I didn't set it higher because one test post is worth 3.3 points of accuracy, and my `analysis`/`advice` and `hot_take`/`reaction` boundaries were hard even for me to apply.

---

## 2. `advice` and `reaction` each have an F1 of at least 0.60, on every seed

Measured from `per_label` in `results.json`.

**Why this target:** These are my two largest labels (71 and 69 posts), so each should have about 10 to 11 posts in the test split, which is enough for a fairly stable F1. They also have visible wording cues: `advice` uses phrases like "you should", "I'd suggest" and "see a doctor", and `reaction` comments are often questions, "+1", jokes or ":)". If the model can't reach 0.60 on the labels with the most data and the clearest cues, it hasn't learned my taxonomy.

---

## 3. Macro F1 of at least 0.45, on every seed

Measured as `f1_macro` in `results.json`.

**Why this target:** Macro F1 weights all four labels equally, so it can't hide the two small ones. `hot_take` has only 17 posts, so the test split will have about 3, and its F1 can only take a handful of values (0, 0.4, 0.67, 1.0). `analysis` has 43 posts, so about 6 to 7 in test. If the model gets 0.65 on `advice` and `reaction`, about 0.45 on `analysis` and never gets a `hot_take` right, macro F1 comes out at about 0.44. So 0.45 means the model has to get at least some `hot_take` posts right, or do clearly better on the other three labels. I didn't set a separate target for `hot_take`, because an F1 based on 3 posts would mostly be measuring which posts the seed happened to put in the test split.

---

## 4. No single label receives more than 50% of the model's predictions on the test split, on every seed

Measured from the column totals of the confusion matrix in `results.json`.

**Why this target:** My data is lopsided toward `advice` and `reaction` (together 70% of the posts), while `hot_take` is only 8.5%. With about 140 training posts and 3 epochs, the most likely failure is that the model mostly guesses the two big labels. The test split itself contains about 35% `advice`, so a model that puts more than half its predictions in one label is over-predicting that label by at least 15 points, which shows the imbalance is driving its guesses. This checks balance in the model's output, which is where the imbalance would actually do damage.

---

## 5. On every seed, the most-confident third of predictions is at least 0.15 more accurate than the least-confident third

Measured as `accuracy_most_confident_third` minus `accuracy_least_confident_third` in the `confidence` block of `results.json`.

**Why this target:** Many posts in this dataset mix two purposes, for example an explanation followed by a recommendation, or a joke that also makes a claim. I'd want to use the model's confidence to flag those posts for a human to check, which only works if confidence actually means something. Each third has about 10 posts, so a gap of 0.15 means at least two more correct predictions in the confident third. That's a small but real difference, and it guards against confidence that is high everywhere and therefore tells me nothing.

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
