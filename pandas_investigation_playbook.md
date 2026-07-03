# Pandas Investigation Playbook
*How I turn an analytical question into pandas. Built from my DataCo late-delivery case study.*

## The one idea
I do **not** memorize pandas. I memorize the **questions an analyst asks**. Each question
has one tool. Learn the questions — the tool follows. The whole DataCo analysis used ~8 moves.

---

## The investigation loop (the ORDER is the method)
This is the sequence I followed on DataCo. It works on almost any dataset.

1. **Did it all load? How big is it?** → confirm before trusting anything
2. **What are my columns?** → find the ones that matter
3. **What's actually inside this column?** → look before analysing
4. **Derive the metric MYSELF — don't trust the label** → a label is a hypothesis
5. **Do the definitions agree?** → cross-check; a gap is a finding
6. **Decide & write it down** → a number I can defend in writing
7. **Put money on it** → what's the £/$ exposed?
8. **WHERE does the problem live?** → break the metric down by a dimension
9. **Worst rate, or biggest pile?** → a rate is meaningless without its count
10. **Eliminate suspects** → flat across a dimension = NOT the driver (that's progress)
11. **Read the distribution** → when "on average it's fine" but cases still fail
12. **Recommend** → the fix comes from the DRIVER, not the headline

---

## Question → Tool map (the whole toolkit)

| When I want to know... | I reach for... | My DataCo example |
|---|---|---|
| Did it load? How many rows/cols? | `df.shape` | `(180519, 53)` — matched my known count |
| What are all the columns + types + nulls? | `df.info()` | found the 6 delivery columns |
| What values are in a category, and how many? | `df["col"].value_counts()` | Late delivery = 98,977 |
| How many rows meet a condition? | make a mask, then `.sum()` | `(real > scheduled).sum()` = 103,400 |
| Save a condition to reuse | `df["flag"] = condition` | `df["derived_late"] = real > scheduled` |
| Of the rows where X, show me Y | `df.loc[mask, "col"]` | status of my 103,400 late orders |
| Total / money over a subset | `df.loc[mask, "col"].sum()` | profit on late orders = $2.14M |
| Rate or total PER group | `df.groupby("dim")["col"].mean()` | late rate by shipping mode |
| Rate AND volume per group | `df.groupby("dim")["col"].agg(["mean","count"])` | First Class 95% on 27,814 |
| The spread of a number (mean, std, %iles) | `df["col"].describe()` | Standard: mean 4, std 1.4, 75%=5 |
| Pull a subset out to work on | `sub = df[df["col"] == "X"]` | `std = df[Shipping Mode == Standard]` |
| Get year/month from a date | `pd.to_datetime(col).dt.year` | late rate by order year |
| Sort to put the worst on top | `.sort_values("col", ascending=False)` | worst shipping mode first |
| Cross two dimensions in a grid | `pd.crosstab(rows, cols)` | derived_late vs Delivery Status |

**Two syntax facts that unlock half the table:**
- A **comparison makes a mask** (True/False per row); **`.sum()` counts the Trues** (True = 1).
- **Mean of a 0/1 column = the rate** (proportion of 1s). This is why `groupby.mean()` gives a rate.

---

## The judgment (this is NOT syntax — it's what makes me an analyst)
- **Rate, not count.** Weight every rate by its volume. Worst-behaved ≠ biggest problem.
- **A label is a hypothesis.** Derive the number myself; if it disagrees with the label, that gap is the finding.
- **Test my own story.** A plausible mechanism ("cancelled in the warehouse") is a guess until the data confirms it.
- **Flatness eliminates a suspect.** All groups ≈ same rate → the cause is systemic, shared by all. That's progress.
- **The mean is the promise; the std is whether I can keep it.** Reach for `describe()` the moment "on average it's fine" is doing suspicious work.
- **The fix comes from the driver.** Headline says there's a problem; the breakdown says where; the distribution says why; only then do I recommend.

---

## How to make this stick (reproduce COLD)
Reading this ≠ knowing it. To own it:
1. **Blank notebook, same dataset.** Rebuild the DataCo analysis from memory. Use this map only when stuck.
2. Do it again in a few days — this map closed, then open only to check.
3. Then a **different** dataset (Superstore, Vrinda), same loop. The loop transfers; only the column names change.
4. When I can run the loop on a new dataset without looking here, it's mine.

The goal is not to remember pandas. It's that when I think *"what's the rate per group?"*, my
hands already know it's `groupby`. Questions first; syntax becomes reflex.
