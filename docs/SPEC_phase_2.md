## **TakeMeter, Part 2 — Testing**

We’ve labeled 200 posts, trained a model, and wrote down what "working" would mean then filed those five criteria before we saw a single number.

In this unit you find out.

Three things test your model, and only one of them is your own. You'll compare it against a **baseline** — a general model doing the same job with no training. You'll read a **confusion matrix** to see which labels it mixes up. And you'll check your own labels against a set written by someone on staff, on the same 30 posts, under the same written definitions.

Until now, the only thing that could tell you your system was wrong was you. In this unit someone else's answers can disagree with yours.

**Missing your own targets does not cost you points.** A model that misses every criterion, with a clear explanation of each miss, earns full marks. Disagreeing with the staff labels doesn't cost you points either — what's graded is whether you can say *why* you disagreed.

### **Goals**

* Compare a trained model against an untrained one and say what the gap means  
* Read a confusion matrix and name the boundary a model didn't learn  
* Compare your labels against someone else's and argue a disagreement  
* Test a model with repeated trials rather than one split

### **What proves you did the work**

Three things, added to the repo you submitted in the previous unit.

1. **A run log** — your five criteria, three trials each, before your fix and after it.  
2. **A verdict, a diagnosis, and your confusion matrix** — met or missed per criterion, what caused every miss, and the matrix typed out as a table.  
3. **An agreement report** — your labels against the staff set on 30 posts, with every disagreement adjudicated.

### **Features**

#### **Required Features**

* **A baseline run.** A general model classifies your held-out posts using only your label definitions. No training.  
* **Three trials per criterion.** Retrain on three different random splits, using three different seeds. One split is one lucky draw, not a measurement.  
* **A confusion matrix, typed as a markdown table.** An image of one earns nothing, because the grader can't read it.  
* **An agreement check against the staff set**, with every disagreement adjudicated. 30 posts, labeled by staff and by you. Report the rate, then say who you think is right on each split and why. "Staff is right" every time is not adjudication.  
* **A verdict and a diagnosis for each of your five criteria.** Met or missed against the previous unit's target, and for every miss, which label and what caused it.  
* **One improvement, made and measured.** Change one thing, retrain, and put both result sets side by side.

**One rule about changing your system.** Your one improvement is the only change in this unit. Your labels, your taxonomy, and your criteria stay where they are. 

### **Setup**

The baseline runs a general model **on your own machine**, not in the notebook. No accounts, no keys. It's slower than the trained model — a few seconds per post, so a 30-post test takes a couple of minutes. That slowness is itself part of the finding: it's what skipping fine-tuning costs you at runtime.

The training is the same notebook as in the previous unit, on your own machine. Sections 6 and 7 are this unit's; run section 1 first so the device gets recorded.

RUNNING.md in your repo has every command and the full troubleshooting table.

### **Milestone 1: Run the baseline**

Before judging your model, find out how hard the task is without one.

A general model reading your label definitions is a fair opponent. If it matches your trained model, your fine-tuning added nothing — and that's a finding worth having, not a failure.

* **Put your label definitions in label\_definitions.txt**, one per line as label: definition, copied from your README. **The definitions are the prompt** — they're the entire input the model gets, so the baseline is only as fair as they are.  
* Run it:

```
python baseline.py
```

*   
  It scores test\_split.csv — the same held-out posts your trained model was measured on, committed by the notebook in the previous unit. Two numbers from two different sets of posts aren't a comparison, which is why it reads that file rather than picking its own sample. If the file isn't there, baseline.py says so and falls back to a random sample; note that in your README if it happens.  
* Write one sentence predicting where your trained model will beat it, **before you look**. Then produce the comparison table:

```
python baseline.py --trained results.json
```

*   
  That adds a Trained column and prints the **Baseline vs. Trained** table ready to paste into your README. Commit baseline\_results.json.  
* Worth doing once on purpose: run it with label\_definitions.txt and again without it, when it falls back to bare label names. The gap between the two accuracies is what your definitions were worth — measured instead of assumed. If you report the bare-names run as your baseline, say so in your README.

**Before you move on**

You have baseline accuracy and per-label F1 on the same posts your model will be tested on. Your prediction is written down. Commit.

### **Milestone 2: Retrain three times and read the matrix**

Your model's score depends on which posts landed in the test split. Change the split, change the score.

So run it three times with three different seeds. Three numbers tell you whether your result is real or whether you got a friendly draw.

* Open the notebook and run **section 1** — every session starts there, and it's what records the device. **Section 6** retrains three times across three seeds in one cell and writes results\_three\_seeds\_before.json. A few minutes per seed on a laptop CPU. Write down all three sets of numbers.  
  The cell has a RUN\_LABEL at the top, set to "before". **You change it to "after" in Milestone 5** — leave it alone and the improvement run overwrites the numbers you're about to compare it against.  
* Look at the spread. If accuracy swings more than about ten points across three seeds, your dataset is too small or too lopsided for a stable measure. Note that — it's a genuine diagnosis, not a mistake.  
* Run **section 7**. It prints the confusion matrix as a markdown table you paste straight into your README — rows are true labels, columns are the model's guesses. Then find your biggest off-diagonal number and say what it means in one sentence.

```
| true \ predicted | analysis | hot_take | reaction |
|---|---|---|---|
| **analysis** | 14 | 7 | 1 |
| **hot_take** | 3 | 18 | 2 |
| **reaction** | 0 | 2 | 15 |
```

*   
  In the table above, 7 real analysis posts were called hot\_take — and only 3 went the other way. That's a direction, not just an error rate.

**Before you move on**

Three sets of numbers from three seeds, and the device they came from. A typed confusion matrix in your README. One sentence on your biggest confusion. Commit results\_three\_seeds\_before.json.

### **Milestone 3: Check yourself against the staff labels**

Staff labeled 30 posts under a taxonomy staff wrote. We’ll label the same 30 under **those** definitions — not the ones you designed in unit 5 — and then compare.

That swap is the point. Your own taxonomy was yours to invent, so nobody could ever tell you that you'd applied it wrong. This one is somebody else's to apply, which makes "did I apply the rule the way it's written" a question with an answer. It is also the only evidence you have about whether your own 200 labels were consistent, and Milestone 4 asks you to use it as exactly that.

This is not a test of whether you're right. Some of these posts are genuinely ambiguous, and staff picked them for that reason. What's being asked is whether you can look at a disagreement and say something useful about it.

* 

* Read **data/staff\_taxonomy.md** — it ships in the starter and holds the three definitions and the decision rules staff applied. Then label all 30 posts in data/staff\_posts.csv yourself, **under those definitions**, and save them as my\_staff\_labels.csv with text and label columns.  
*   
* Only then, get data/staff\_labels.csv. In class, your TF sends it. **Do not open it until your own 30 are saved** — there is no way to unsee an answer key.  
  * download [staff\_labels.csv](https://courses.codepath.org/course_files/ai201/fa26-01b4358f/staff_labels.csv) and save it in your repo's data/ folder.  
*   
* **Paste each post unchanged.** The two files are matched on their text, so a retyped or trimmed post won't match — the script tells you which ones didn't.  
* Now compare:

```
python agreement.py
```

*   
  It works out your agreement rate and lists every disagreement, then **stops there on purpose**. The rate is arithmetic; the adjudication is the graded part. For each disagreement, write three lines: the post, both labels, and who you think is right and why. Commit agreement\_results.json.  
  **Not adjudication:**  
  "Staff said hot\_take, I said analysis. I was probably wrong."  
  **Adjudication:**  
  "Staff said hot\_take, I said analysis. The post cites one stat, but the stat is picked to support a conclusion already stated. Rule 1 says a fact has to carry the argument, not decorate it — so staff is right, and the rule already covered this. I applied it loosely."  
* Sort your disagreements into three piles: (a) the rule covered it and you applied it loosely, (b) the rule genuinely doesn't say, (c) the rule is ambiguous here and your reading is defensible. Piles (b) and (c) are the interesting ones, and (c) is a legitimate win — some of these you should be arguing and winning.

**Before you move on**

Your agreement rate is written down. Every disagreement has a written call with a reason. Commit.

### **Milestone 4: Call each criterion and diagnose every miss**

Say met or missed against the previous unit's targets. Then work out why the misses happened.

The verdict sounds like the easy part. It's the part people get wrong, because "met" is more comfortable and there's always a generous reading of three numbers.

* Fill in your run log. Every test unit in this course uses the same format — one row for **each of your five criteria**, a column per seed, and a verdict. A table with fewer than five rows isn't a complete run log:

```
| Criterion | Target | Seed 1 | Seed 2 | Seed 3 | Verdict |
|---|---|---|---|---|---|
| 1. Overall accuracy | ≥ 0.65 | 0.71 | 0.62 | 0.68 | MISSED |
| 2. Every label F1 | ≥ 0.60 | 0.58 | 0.55 | 0.61 | MISSED |
| 3. Beats the baseline on accuracy | yes | yes | yes | yes | MET |
| 4. Precision on `analysis` | ≥ 0.70 | 0.74 | 0.72 | 0.76 | MET |
| 5. Recall on `hot_take` | ≥ 0.60 | 0.63 | 0.59 | 0.66 | MISSED |
```

*   
* Write MET or MISSED for each. If your target said 0.65 and your seeds came out 0.71, 0.62, 0.68 — that's a miss. The target has to hold across the trials, not turn up sometimes.  
* For each miss, name the **cause** and how you know. Your model has four common ones: too few examples for a label, a boundary your own labels applied inconsistently, a label pair that's genuinely hard, or a task the model can't reach from 200 examples.  
  **Not a diagnosis:**  
  "The reaction label did badly."  
  **A diagnosis:**  
  "reaction has an F1 of 0.41 across all three seeds. My matrix shows most misses going to hot\_take. My agreement check found 4 of my 6 disagreements on exactly that pair. So this is my labeling, not the model — I applied the boundary two different ways in the training data."  
* Use your agreement report as evidence. It is the only thing you have that can tell a labeling problem from a model problem, and that's what it's for.

**A note on changing a criterion**

Sometimes a criterion turns out to be broken rather than unmet. That's a real finding and it earns credit. Only one kind of change counts.

**This earns points.** The criterion couldn't be measured:

*Original: "The model performs well on all labels."* *Revised: "Every label has an F1 of at least 0.60 on the held-out set."* *Why: "performs well" gave me nothing to check. I couldn't produce a verdict from it.*

**This does not.** The criterion was fine; you missed it:

*Original: "Overall accuracy of at least 0.65."* *Revised: "Overall accuracy of at least 0.55."* *Why: 0.65 turned out to be optimistic for 200 examples.*

* If you revise, leave the original line and put the new one underneath with your reason. Never delete the original.

**Before you move on**

Every criterion has a verdict and a sentence on how you decided. Every miss has a cause and evidence. Commit.

### **Milestone 5: Fix one thing and re-run**

Pick one thing your diagnosis pointed at, change it, and retrain.

One change, measured properly, beats four changes you can't tell apart.

* Pick your improvement. These are the three most likely to move the numbers:  
  **Relabel one boundary.** If your agreement check found you applied a rule two ways, go back and apply it once. Fix only that boundary and say how many rows changed.  
  **Add examples to your thinnest label.** Thirty more posts of the label with the worst F1, labeled with the same care as the first batch.  
  **Change one training setting.** More epochs, or a different learning rate. Only pick this if your diagnosis actually pointed at training, which it usually doesn't.  
* Make the change, **set RUN\_LABEL \= "after" in section 6**, then retrain across the same three seeds and fill in a second run log in the same format. One change only. If you're fixing three things, pick the one your diagnosis named.  
* Put both logs in your README, before and after, so the difference is visible side by side.  
* Say whether it helped. **If it didn't, say that.** A change that made things worse, honestly reported, earns full credit — and relabeling that didn't help is a genuinely interesting result.

**Before you move on**

Both run logs are in your README. You can say in one sentence whether the change helped. Commit.

### **Milestone 6: Say what's still broken, and submit**

Finish the write-up and make the call on what you didn't fix.

* Write **What's Still Broken**. For each criterion still missed: what you'd do, and why you stopped. "I ran out of time" is fine if it's true.  
* Add two sentences on the gap between what you meant your labels to capture and what the model actually learned. The confusion matrix is your evidence.  
* Update **How I Used AI**, especially if a model helped you spot patterns in your errors. Then submit the **same URL you submitted in the previous unit**.

 **Before you move on**

Both run logs, verdicts, diagnoses, your matrix, and your agreement report. Same repository URL. Commit.

