# DataCo Supply Chain: Late-Delivery Analysis (Python / pandas)

**Question.** How much of DataCo's order volume ships late, what is the financial value exposed to
that risk, and where does the problem actually come from?

**Dataset.** DataCo Smart Supply Chain, 180,519 orders, 53 fields.
**Tools.** Python, pandas, Jupyter.
**Companion.** The same finding built as a Power BI dashboard (`../DataCo_LateDelivery_PowerBI`).

---

## Headline findings
- 54.8% of orders (98,977) arrived late. I confirmed this from the raw shipping dates myself
  instead of trusting the vendor's `Delivery Status` label.
- USD 2.14M of profit and USD 20.1M of revenue sit on those late orders.
- First Class, the premium tier, is the worst performer against its promise at 95% late, even
  though it ships in two days, faster than the reliable Standard Class at four.
- Standard Class produces the largest group of late orders, around 41,000, purely on volume,
  despite having the best rate at 38%.
- The late rate holds near 38% across every region and every year, so the cause is systemic.

## Root cause
This is a promise problem, not a speed problem. The shipping SLAs sit at or below what the
operation actually delivers, so orders miss targets they were never able to meet.

## Recommendations
1. **Recalibrate the shipping promises.** Reset each SLA to a level the operation reliably hits,
   around the 90th percentile of real delivery time (First Class one to two days, Standard four to
   five). This moves tens of thousands of orders back inside their promise at almost no cost.
2. **Reduce delivery-time variance.** Tighten the spread on Standard Class through more consistent
   fulfilment. This is an operational investment that pays back over time. Start with Standard
   Class for its volume and review the third-party versus in-house shipping legs.

Lead with the recalibration for a fast win, then fund the variance work for the lasting fix.

---

## Method (the investigation loop)
1. Load and confirm the row count against a figure I already trusted.
2. Survey the columns and locate the delivery-timing fields.
3. Derive "late" from first principles (`real days > scheduled days`) rather than trusting the
   label, then cross-check the three candidate definitions. The raw rule counts 4,423 more orders
   than the label. All of them are cancelled shipments that were behind schedule when they were
   cancelled, which I checked rather than assumed.
4. Quantify the money at risk, separating revenue from profit.
5. Break the late rate down by shipping mode and weight it by volume, because a rate is not a count.
6. Eliminate suspects: region and year both come back flat, which points to a systemic cause.
7. Read the distribution with `describe`. The median sits on the promise and the 1.4-day spread
   drives the late tail.
8. Recommend from the driver, not from the headline.

A reusable version of this loop, the analytical questions mapped to the pandas tools that answer
them, is in [`pandas_investigation_playbook.md`](pandas_investigation_playbook.md).

## Files
- `DataCo_late_delivery_pandas.ipynb` is the analysis notebook.
- `pandas_investigation_playbook.md` is the reusable Question to pandas-tool playbook.

## Reproduce
```bash
pip install pandas jupyterlab
jupyter lab
```
Open the notebook and run all cells.

**Data.** The dataset is not committed here because it is a 92MB public file. Download the *DataCo
Smart Supply Chain Dataset* from Kaggle (search "DataCo Smart Supply Chain"), place
`DataCoSupplyChainDataset.csv` next to the notebook, and update the path in the first cell. It
loads with `encoding="latin-1"` because product names contain non-UTF-8 characters.
