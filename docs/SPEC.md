## **Show What You Know: TakeMeter**

Online communities run on opinions. Some posts are worth reading. Some are noise. Regulars can usually tell within a sentence, but almost nobody can say exactly how.

In this unit you'll build **TakeMeter**, a classifier that sorts posts from a community you pick. You choose the labels, gather and label 200 real posts, and train a small model on them.

The training is the easy part. It takes about ten minutes. The hard part is deciding what you're measuring, and labeling 200 posts the same way twice.

**This project runs for two units.** In this unit you build it and write down what "working" would mean. In the next unit you test it — against a baseline, and against labels someone on staff wrote for the same posts. Same repository both units.

**You write all five acceptance criteria this time.** None are given.

### **Goals**

By the end of this unit you'll be able to:

* Write label definitions two people would apply the same way  
* Label real text consistently and record the cases that were hard  
* Run a fine-tuning pipeline and get a model out of it  
* Write acceptance criteria for something that has never been trained yet

### **What proves you did the work**

Three things. If all three are in your repo, your submission is complete.

1. **criteria.md** — five numbered acceptance criteria, each naming a target. All five yours.  
2. **labels.csv** — at least 200 labeled examples, one file, not pre-split.  
3. **README.md** — the five sections listed at the bottom of this page.

Plus at least four new commits, in the repo you'll submit again in the next unit.

### **Features**

#### **Required Features**

* **Two to four labels.** Each has a one-sentence definition and two example posts. A post should fit exactly one label almost every time.  
* **A written decision rule** for the hardest boundary between two of your labels. Every taxonomy has one. Find yours before you label 200 things.  
* **At least 200 labeled examples** from one public community, in a single CSV with text, label, and note columns.  
* **No label above 70% of your data.** If one label dominates, the model learns to guess it and nothing else.  
* **A completed training run** on your data, with the resulting metrics saved.  
* **Five acceptance criteria**, written by you, before you see any results.

#### **Stretch Features**

Optional, for extra credit. Say what you're adding in your README before you start.

* **A fourth label**, if your community genuinely has one your three don't cover.  
* **A second training run** with one hyperparameter changed, and a note on what moved.  
* **50 extra examples** for whichever label is thinnest.

### **Milestone 1: Pick a community and read it**

Pick where your posts come from, then read there for twenty minutes before deciding anything.

Don't design labels from memory. You'll invent distinctions that sound good and don't appear in the actual text.

* Pick one public community with lots of text and real variation in quality. Sports forums, music discussion boards, film communities, and gaming subreddits all work. You need to be able to collect 200 public posts from it.  
  Two rules. Public only — nothing behind a login you had to be invited to. And avoid communities built around one show or one release, since spoilers make your examples unreadable for anyone who isn't caught up.  
* Read 30 to 40 posts. Actually read them. As you go, note what makes some feel more substantial than others.  
* Write down three or four candidate distinctions you saw in the text. Not "good and bad" — something you could point at. *"Some posts cite a number. Most don't."*

**Before you move on**

You've named your community and written three candidate distinctions that came from posts you read. Commit.

### **Milestone 2: Design your labels**

This is the decision the whole project rests on. Vague labels produce a model that learned something vague, and you won't find out until the next unit.

**What a weak taxonomy looks like**

* good — a quality post  
* bad — a low-quality post

This fails because quality is in the eye of the reader. Two people apply it differently, so the model gets an inconsistent signal. You also can't say where the line is.

**What a strong one looks like**

* analysis — makes an argument backed by a specific, checkable fact. A stat, a date, a quote.  
* hot\_take — a confident claim with no support offered. Might be true. Isn't argued.  
* reaction — an immediate response to something that just happened. Feeling, not argument.

These work because you can state each boundary in a sentence, two people would agree on most posts, and the distinctions match how the community itself talks.

* Write two to four labels. For each: a one-sentence definition, and two real posts from your reading that clearly belong to it.  
* Find one post that sits between two of your labels. Write down which two, and then write the **decision rule** you'll use every time you hit that case. *"If the post names a specific checkable fact, it's analysis, even if the tone is heated."*  
  This one paragraph will save you an hour in Milestone 4\. Ambiguous posts are where consistency dies.  
* Stress-test the labels with an AI tool. Give it your definitions and ask it to write eight posts that sit on your boundaries. If you can't classify its posts cleanly, tighten the definitions now — not after you've labeled 200 things.

### **Milestone 3: Write your acceptance criteria**

An acceptance criterion says what "working" means, specifically enough that someone else could check it. Not *"the model is accurate"* — that's an opinion. *"Every label has an F1 of at least 0.60 on the held-out set"* — that's a criterion. It names a number.

They are written **before you train anything**. In the next unit you find out how you did against a standard you set when you couldn't know the answer.

Missing your own targets in the next unit does not cost you points. Setting a target so easy you can't miss it does.

* Create criteria.md and write five numbered criteria. Cover at least three of these areas:

| Area | A question it could answer |
| ----- | ----- |
| Overall accuracy | How often does it need to be right to be worth using? |
| Per-label performance | Is one label allowed to be much worse than the others? |
| Balance | How lopsided can your label counts get before it's a problem? |
| Consistency | If someone else labeled the same posts, how often should you agree? |
| Confidence | Should a confident prediction be right more often than an unsure one? |

*   
* Give every criterion a number. "Accuracy is good" is not a criterion. "Accuracy is at least 0.65" is.  
  Pick numbers you can defend, not numbers that sound impressive. Three labels means a coin-flip guesser gets about 33%. Your target should sit somewhere you'd honestly call useful.  
* Under each, write a sentence or two on why that number. *"I picked 0.60 F1 for reaction because it's my smallest label and I only have about 50 examples of it"* is a real answer.

**Before you move on**

criteria.md has five numbered criteria. Each names a number. Each has a reason underneath. Commit.

### **Milestone 4: Collect and label 200 examples**

This is the longest block in the unit and the one that decides your model's ceiling.

The model can only learn what your labels actually captured. Careless labeling produces a bad model no matter what the training run does.

* **Collect first, label second (about 45 minutes).** Gather at least 200 public posts into labels.csv in your repo. labels\_template.csv shows the shape — text, label, note. Keep whole posts; don't trim them to fit. Copy and paste is fine and keeps you close to the data. If you write a scraper, timebox it — this is not a scraping project.  
  **One file, unsplit.** Don't split it yourself — the notebook does a 70/15/15 split, and splitting twice is how examples leak between training and test.  
* **Label 20 posts cold (about 15 minutes).** No help, no tools. Just you and your definitions. This is your honesty check and the next unit's agreement report depends on it, so do it first and mark these rows cold in the note column.  
* **Label the rest (about 40 minutes).** You may use an AI tool to pre-label a batch, and most people should — give it your definitions and a set of posts, then **read and correct every single one**. Skimming produces noisy data and a model that learned your skimming.  
  Mark pre-labeled rows in the note column. You'll disclose the workflow in your README.  
* **Keep a list of hard cases as you go.** Any post that made you pause: what it was, which two labels it could be, what you chose. You need at least three, and they're worth more than the easy 190\.  
* **Count your labels (about 5 minutes).** If any label is above 70% of the file, go collect more of the thin ones. A lopsided file trains a model that guesses. The notebook's check cell flags this for you in Milestone 5, along with duplicate posts and any label spelled differently in your CSV than in your label map — but finding it now is cheaper than finding it then.

**Before you move on**

labels.csv has at least 200 rows with text, label, and note, or the number the stop rule above left you with, written up in your README. No label is above 70%. Three hard cases are written down. Commit it — the notebook reads labels.csv off your disk, so it doesn't have to be pushed to work, but this is the file your whole unit rests on and it belongs in your history now.

### **Milestone 5: Run the training**

Run the notebook, get a model. Most of this is waiting.

* **Commit labels.csv first.** Nothing depends on it being pushed — the notebook reads it off your disk — but this is the file your whole unit rests on and it should be in your history before you start changing things.  
* Open takemeter.ipynb from your repo and **check the kernel is this project's .venv.**  
* Run **section 1**. It prints what you're training on and confirms it can see your files. Fill in your name and email in the second cell — they only label your results. If you'd rather see the whole notebook work before your own data is ready, data/practice\_labels.csv is 60 already-labeled posts from a fictional running forum, there for exactly that.  
* Run **section 2**: your label map, the settings, and the seed. Its last cell checks your data and prints anything that would break the run or quietly ruin it — a label spelled differently in your CSV than in LABELS, a label over the 70% cap, duplicate posts that would leak across splits, a label with too few examples to measure. Fix the \[FIX ME\] lines before going on.  
* Run **section 3** and read the printed split sizes and label counts. If a label has fewer than about 8 examples in the test split, note it — that number will make the next unit's metrics jumpy, and knowing why beats being surprised.  
* Run **section 4**, the training. A few minutes on a laptop CPU, well under one on a GPU. The defaults are three epochs, learning rate 2e-5, batch size 16, and 128 tokens per post. If you change any of them, write down what and why.  
* Run **section 5**. It writes results.json and test\_split.csv. **Commit them both** — the next unit's baseline reads test\_split.csv. Don't read the numbers closely yet; that's the next unit's work, and your criteria are already filed.  
  Sections 6 and 7 are the next unit's. Nothing in this unit needs them.

**Write down what you trained on**

results.json records your device, and you should note it in your README too.

Two people running this notebook on the same data with the same seed can get different numbers, because different hardware does the arithmetic in a slightly different order. That is not a bug and it is not one of you being wrong — it is a real property of the thing you're building, and the next unit asks you to report it beside your three seeds.

It is also the first time in this course that *where you ran something* is part of what you found.

**If you're stuck**

Notebook failing after many modifications? The three usual causes, in order: the kernel isn't this project's .venv (a ModuleNotFoundError for something you know you installed), a label in your CSV doesn't match your label map exactly — including capitals and spaces — or your machine ran out of memory, which looks like everything crawling. RUNNING.md has the full table.

If none of those fix it, commit your CSV and criteria and write down the error. You have a labeled dataset and a filed standard, which is most of this unit's grade.

**Before you move on**

Training finished, and results.json and test\_split.csv are in your repo. Commit them both — and note the device results.json recorded.

### **Milestone 6: Write it up and submit**

Short, because you wrote most of it as you went.

* Fill in **What This Does** and **How I Used AI**. For AI use, describe two specific moments and disclose any pre-labeling.  
* Check the repo: criteria.md, labels.csv, results.json, test\_split.csv, the README's five sections, four new commits or more. Push everything, then submit **your fork's URL** — not the CodePath starter's.

**Before you move on**

Your repository URL is submitted. **Write it down somewhere you'll find it in the next unit.** A repo you delete and recreate loses the commit history that protects you in a dispute.

