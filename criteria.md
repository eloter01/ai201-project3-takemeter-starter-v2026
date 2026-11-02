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

## 1. Balance

In `labels.csv`, no label has fewer than 55 rows.

**Why this target:** The assignment already caps any label at 70%, so restating
that wouldn't be a criterion. From reading my posts, I expect `argument` to be
the biggest label by a lot and `evidence` to be the thinnest, since only a
handful of posts contain a link or a number. 55 rows gives the smallest label
about 8 posts in the 15% test split, which is the notebook's floor for a label
being measurable at all. If `evidence` comes in under 55, this tells me to
collect more before I train.

---

## 2. Confidence

On every seed (42, 7, 2024), the model's accuracy on its most-confident third
of test posts is at least 0.15 higher than its accuracy on its least-confident
third.

**Why this target:** My hardest boundary is `argument` vs. `reaction`, and this
community is full of sarcasm and rhetorical questions that attack the poster
and make a point at the same time. Those posts should be the ones the model is
least sure about, so its unsure third should hold most of its mistakes. With
about 38 test posts, each third is about 12 posts, so 0.15 means the confident
third gets at least 2 more posts right. If confidence meant nothing, a gap like
that would only show up by luck, not on all three seeds. I compare thirds
instead of using a fixed cutoff like 0.80, because a small model trained on
about 175 posts may give low scores, and a cutoff could leave no posts above
it.

---

## 3. Overall accuracy

On every seed (42, 7, 2024), the model's accuracy on the held-out test split is
at least 0.70.

**Why this target:** The coin-flip baseline for three labels is 0.33, but that
isn't the right baseline for my data. If `argument` is around 60% of the file,
a model that always guesses `argument` scores 0.60, so 0.65 would barely beat
doing nothing. 0.70 is where I'd call it useful. If `argument` ends up even
bigger, 0.70 gets close to free, but criterion 4 catches that, because always
guessing `argument` gives an F1 of 0 on the other two labels.

---

## 4. Per-label performance

On every seed (42, 7, 2024), every label has an F1 of at least 0.50 on the test
split.

**Why this target:** With about 38 posts in the test split and at least 55 rows
per label (criterion 1), my smallest label will have about 8 test examples, so
a single miss moves its F1 by around 0.1. I can't hold it to 0.70 with that few
examples. But below 0.50 the model hasn't really learned the label and is just
folding it into `argument`. `evidence` has obvious surface cues (links,
percentages), so the label most likely to miss this is `reaction`.

---

## 5. Stability

Across the three training seeds (42, 7, 2024), the highest and lowest test
accuracy differ by no more than 0.08.

**Why this target:** With about 38 test posts, each post is worth about 2.6
points of accuracy, so 0.08 allows a gap of about 3 posts between the best and
worst seed. The notebook re-splits the data for each seed, so each run is
tested on different posts, and the spread includes which posts land in the
test split, not just training noise. That makes 0.08 harder than it sounds. If
the spread is wider than that, no single run's accuracy means much. This is the
one I'd be most embarrassed to miss, because it would mean the numbers I
report are mostly noise.

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
