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

TakeMeter sorts posts from 4forums.com, a public debate forum where people
argued about gun control, abortion, and gay marriage. Each post gets one of
three labels: `evidence` (it states a specific, checkable fact), `argument`
(it makes a claim about the topic but states no checkable fact), or `reaction`
(it makes no claim about the topic, like an insult, agreement, or off-topic
chat). The classifier is `distilbert-base-uncased`, fine-tuned on 271 posts
that I labelled or checked myself. The goal is to separate posts that bring
checkable facts from bare opinion and from posts that just go after the other
person.

---

## Label Taxonomy

<!-- Each label: a one-sentence definition and two real examples from your
     reading. Then your decision rule for the hardest boundary.

     The decision rule is worth a point on its own and it's the thing most
     people leave out. Every taxonomy has a hardest boundary. Name yours. -->

### `evidence`

**Definition:** States at least one specific, checkable fact, such as a
statistic, a link or named source, a quote from an outside source, or a named
real event.

**Example 1:**
> While this is true, the number of abortions at or after 21 weeks is not insignificant. According to the Guttemacher Institute, 1.2% of abortions occur at or after 21 weeks. That translates into about 15,480 abortions. This thread, of course, is talking specifically about those 15,480 abortions.

**Example 2:**
> Yea. They would look like XXXXXX and waste a lot of ammo. Even the article at the end states they have a range of 1100-1500 yards, so not even close to a mile. The articles title is pure hyperbole. Those fire a .338 lapua (8.59mm).

### `argument`

**Definition:** States, defends, or challenges a claim about the issue, with or
without reasons, but states no specific checkable fact.

**Example 1:**
> What good is that when most cases involving firearms offenses never make it to the full court? Anytime you have the potential to put somebody away for a great many years because they have a felony record and were arrested and in possession of a gun, the possession charges always get dropped and a plea deal is offered.

**Example 2:**
> There are all kinds of social services, religious and otherwise available that will provide assistance to any woman who wants to carry her baby to term........ When pro abortion people use that as and argument it is just a red herring and smoke screen........

### `reaction`

**Definition:** Makes no claim about the issue, such as insults, ridicule,
praise, or agreement aimed at the other poster or their side, or off-topic
chat.

**Example 1:**
> Then get your sick, satanic mitts off everyone else.

**Example 2:**
> The same God that created a troll like you.

### The hardest boundary

**Which two labels:** `argument` and `reaction`

This is the hardest line because sarcastic posts and rhetorical questions often
attack the other poster and make a point about the issue at the same time.

**The decision rule I used every time:**

"The issue" means the thread's debate topic (guns, abortion, gay marriage, and
so on). I check the labels in order: `evidence`, then `argument`, then
`reaction`.

1. If the post states a specific, checkable fact, it's `evidence`, even if the
   tone is rude and even if the fact isn't used to support anything.
   - Test: would the sentence be false if the fact were false? Naming the topic
     isn't stating a fact. "I don't think Massachusetts allowing same-sex
     marriage was a bad thing" is an opinion. "We still have gay marriages in
     Massachusetts" is a fact.
   - Paraphrasing what a named person said counts, and so does a headline that
     names its source.
   - A historical event counts when it's stated as a fact, not when it's used
     as an analogy or sarcasm (like the Nazis as a rhetorical device).
   - Vague claims like "studies show" and personal stories don't count.
   - A quote counts only if it clearly comes from an outside source. If I can't
     tell whether it's from a document or the other poster, it doesn't count.
2. If not, and the post makes **any** claim about the issue, it's `argument`.
   That includes claims made through sarcasm, through a rhetorical question, by
   demanding proof for the other side's claim, or with no reasons at all.
3. Otherwise, it's `reaction`. That covers posts about the other poster or
   their side, claims about people that aren't about the topic, and off-topic
   chat.


---

## The Dataset

<!-- Where you collected from, how you labelled, your counts, and three hard
     cases. -->

**Where the posts came from:** 4forums.com, a public debate forum where people
argued about gun control, abortion, gay marriage, and other political issues.
The forum no longer exists, so I collected the posts from the Internet Argument
Corpus, a research dataset of 4forums.com threads. I started with 250 posts and
later added 21 from a reserve pool of 500 more posts from the same corpus
(`data/extra_posts.csv`) to balance the labels.

**How I labelled them:**

1. **Cold (20 rows).** I labelled the first 20 posts myself using only my
   definitions and decision rule, with no AI help. These are marked `cold`.
   They were labelled under the first version of my rules, and I didn't change
   them after the rules were tightened.
2. **AI pre-labelling (230 rows).** Claude pre-labelled the other 230 posts
   using my definitions and decision rule. I read every one and changed 6.
3. **Tightening the rules.** The review showed gaps in my rules: whether a fact
   has to support a claim, what "the issue" means, and where off-topic posts
   go. I fixed the definitions and decision rule above, rechecked the rows the
   new rules could affect, and changed 4 more. All 10 changed rows are marked
   `ai-prelabel, corrected`. The rest are marked `ai-prelabel`.
4. **Balancing (21 rows).** After the review, `evidence` had 44 rows and
   `reaction` had 54, both under the 55 my first criterion asks for. Claude
   read the first 120 posts of the reserve pool in order and pre-labelled the
   `evidence` and `reaction` ones. It picked different kinds of evidence (court
   cases, named sources, quotes, historical events) instead of just posts with
   numbers, so the model doesn't learn "has a number = `evidence`". I reviewed
   all 21 and kept them. They're marked `ai-prelabel, added for balance`.

In total: 20 rows labelled by me alone, and 251 pre-labelled by AI and checked
by me, 10 of which I corrected.

**Counts per label:**

| Label | Count | Share |
|---|---|---|
| `argument` | 148 | 54.6% |
| `reaction` | 62 | 22.9% |
| `evidence` | 61 | 22.5% |
| **Total** | 271 | 100% |

**Three hard cases**

<!-- Any post that made you pause: what it was, which two labels it could have
     been, and what you chose. These are worth more than the easy 190. -->

**1.** (line 56)
> *The post:* I believe my wording was "very rare". Please do not exaggerate my statements by mis-quoting me. But as you have mentioned it, care to produce some figures on the frequency of partial birth abortions? Its just you have a habit of making over-inflated statements to support your arguements.
>
> *Could have been:* `reaction` or `argument`
>
> *I chose, because:* `argument`. Most of the post is about being misquoted,
> which on its own would be `reaction`. But it restates a claim about the issue
> ("very rare") and asks the other poster for figures on partial-birth
> abortions. Under my rule, demanding proof for the other side's claim is
> `argument`.

**2.** (line 144)
> *The post:* Prove your assertion that he has called himself a conservative Christian rather than a conservative Republican OP. Support your clai if you can or stop trying to lump everyone on the right together.
>
> *Could have been:* `argument` or `reaction`
>
> *I chose, because:* `reaction`. Like the post above, it demands proof, but
> for a claim about a person (whether someone called himself a conservative
> Christian), not about the thread's topic. This is the post that made me write
> down that "the issue" means the thread's debate topic.

**3.** (line 122)
> *The post:* What part of.. 'On the other hand, not one acceptable reason for individuals owning guns is mentioned.' are you having trouble with? Only a gullible fool, such as yourself, would lend blind allegiance, and obedience, to a 'world' body that seeks to usurp the sovereignty of individual nations. That's why you are indeed a LIBERAL.
>
> *Could have been:* `evidence` or `argument`
>
> *I chose, because:* `argument`. It's only `evidence` if the quoted sentence
> comes from an outside document, and from this post alone I can't tell
> whether it's from a document or the other poster's earlier words. My rule is
> that if I can't tell, the quote doesn't count. Line 263 has the same quote,
> but that post points to the UN website as the source, so there it counts and
> the post is `evidence`.

---

## The Training Run

<!-- Your starting model, your settings, and anything you changed and why. -->

**Base model:** `distilbert-base-uncased`

**Settings:** 3 epochs, learning rate 2e-5, batch size 16, max length 128
tokens, seed 42. Trained on a CPU (`results.json` records `device: cpu`,
torch 2.14.1+cpu).

**Anything I changed from the defaults, and why:** Nothing. I kept every
default and only set `LABELS` to my three labels. 128 tokens was enough: only
3 of my 271 posts are longer than that (the longest is 177 tokens and the
median is 48), so almost nothing gets cut off.

**Split sizes:** 189 train / 41 val / 41 test.

| Label | Train | Val | Test |
|---|---|---|---|
| `evidence` | 43 | 9 | 9 |
| `argument` | 103 | 22 | 23 |
| `reaction` | 43 | 10 | 9 |

Every label has at least 8 posts in the test split, because I topped up
`evidence` and `reaction` to 55+ rows before training (see The Dataset). With
only 9 test posts each, their F1 scores will still move a lot between seeds.


---

## How I Used AI

<!-- Two specific moments — what you asked, what came back, what you changed.

     ⚠️ Plus disclosure of any pre-labelling. If you had a model pre-label a
     batch and then read and corrected every one, say that. It's an allowed
     workflow and disclosing it costs you nothing. Not disclosing it is the
     problem. -->

**Moment 1: stress-testing my labels**

- *What I asked for:* I gave Claude my three label definitions and asked for
  eight posts that sit on the boundaries between them. Then I labelled them
  myself without its help.
- *What came back:* Eight made-up posts. Seven of my answers followed my
  rules, but one ("Agreed, well said. The government has no business deciding
  who can get married.") fit no label. It makes a claim with no reason, and my
  `argument` definition required reasons while `reaction` couldn't contain a
  claim.
- *What I changed:* I rewrote `argument` to cover claims "with or without
  reasons", and took "sarcasm" out of `reaction`, because another test post
  showed sarcasm can carry a claim.

**Moment 2: reviewing the pre-labels**

- *What I asked for:* I had Claude pre-label 230 posts and flag the ones it
  wasn't sure about, plus any places where my rules didn't decide the case.
- *What came back:* 43 borderline posts and five rule questions, like whether
  a fact has to support a claim to count as `evidence`, and where off-topic
  posts go.
- *What I changed:* I made the rule calls myself (any stated checkable fact
  counts, "the issue" means the thread's topic, and step 3 became "otherwise,
  `reaction`"), updated the rules in this README, and changed 10 labels. Some
  were posts where Claude didn't follow its own calls, like line 96 (a
  historical fact it called `argument`) and line 122 (a quote it counted as
  outside evidence when the source wasn't clear).

**Pre-labelling disclosure:** Claude pre-labelled 251 of my 271 posts: the 230
that weren't in my cold set, plus 21 it picked from a reserve pool to balance
the labels. I read and checked every one and changed 10 (marked
`ai-prelabel, corrected` in the note column). The other 20 posts are my cold
set, which I labelled alone with no AI help (marked `cold`). Claude also
checked my criteria to make sure each one could be measured, and helped turn
my notes into the README text. The final labels, rules, and target numbers are
my decisions.

<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

<!-- Both models on the same posts. `python baseline.py --trained results.json`
     prints this table for you. -->

| Measure | Baseline | Trained | Difference |
|---|---|---|---|
| Overall accuracy | 0.220 | 0.561 | +0.341 |
| Macro F1 | 0.120 | 0.240 | +0.120 |
| F1 — `argument` | 0.000 | 0.719 | +0.719 |
| F1 — `evidence` | 0.360 | 0.000 | -0.360 |
| F1 — `reaction` | 0.000 | 0.000 | +0.000 |

**What I predicted before I looked:**
<!-- Milestone 1 asks you to write this BEFORE seeing the trained numbers. A
     prediction made afterwards isn't one. -->

The definitions I created make logical sense to me and captured several edge
cases that I believe to be most common; therefore I believe my trained model
will beat the baseline on F1 for all three labels.

**What the gap actually means:**
<!-- If the baseline matched your trained model, your fine-tuning added
     nothing — and that is a real finding, not a failure. Say it plainly. -->

The baseline, a general model with no training, labeled every post as
`evidence` when given my label definitions. My trained model labeled every
post as `argument` instead. The gap comes from my test split having more
`argument` posts than `evidence` posts: always guessing `argument` scores
23/41 = 0.561, while always guessing `evidence` scores 9/41 = 0.220.
`argument` also makes up over half of my training split (103 of 189 posts),
while `evidence` and `reaction` have 43 each, so it appears the fine-tuned
model learned to heavily bias toward `argument` rather than learning to tell
the labels apart.



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
