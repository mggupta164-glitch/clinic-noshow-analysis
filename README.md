**GitHub Repository:** https://github.com/mggupta164-glitch/clinic-noshow-analysis

## 4-Line Summary (Parts 1–4)
- **Part 1 (Data Cleaning):** Cleaned the raw clinic dataset from 1,020 rows to **990** valid appointments with an overall no-show rate of **22.0%** (218 no-shows) after removing duplicates, invalid dates/statuses, and capping outliers.
- **Part 2 (Exploratory Analysis):** Appointments without reminders (`reminder_sent = No`) showed a **30.8%** no-show rate compared to **15.3%** when a reminder was sent (`reminder_sent = Yes`).
- **Part 3 (Predictive Modeling):** Evaluated Logistic Regression, Decision Tree, and Random Forest models, selecting **Logistic Regression (Pipeline)** with a test ROC-AUC of **0.7889** and capturing **57.1%** recall (28 of 49 no-shows) at threshold **0.35**.
- **Part 4 (LLM Note Classification):** Structured Gemini classification on 10 follow-up notes achieved **9/9 (100%)** schema-valid outputs on non-blank notes (with **1** blank note safely skipped) and showed that both `forgot_or_no_reminder` cases occurred when `reminder_sent == No` (2/2).

## Recommendation
To reduce missed appointments without overloading front-desk staff, the clinic should enforce automated SMS/call reminders for all bookings and prioritize manual outreach for appointments scoring at or above **0.35** risk. Across **990** appointments (overall no-show rate **22.0%**), missing reminders was strongly associated with higher missed visits (**30.8%** without a reminder vs. **15.3%** with a reminder), and in our LLM note audit, **2 of the 9** recorded follow-up notes (both unreminded) cited forgetting or receiving no reminder. Targeting high-risk patients using our tuned Logistic Regression pipeline (test ROC-AUC **0.7889**) captures **57.1%** of actual no-shows while flagging only **49 of 198** test appointments (24.7%), keeping daily follow-up calls manageable.

## Model Choice and Why
I selected **Logistic Regression (Pipeline with StandardScaler)** because it achieved the highest 5-fold cross-validated ROC-AUC (**0.7737**) and test ROC-AUC (**0.7889**) without the severe overfitting seen in an unpruned Decision Tree (train accuracy `1.00` vs. test accuracy `0.67`). Lowering the decision threshold from `0.50` to **0.35** increased out-of-fold F1 from `0.3471` to `0.4892` and raised test recall from `34.7%` to **57.1%**, which is operationally preferable because the cost of a missed clinic slot outweighs the low cost of a reminder call.

## Limitations
1. **Illustrative sample size for LLM notes:** The 10 analysed follow-up notes are a small illustration and cannot represent clinic-wide root causes statistically.
2. **Observational data (association, not causation):** Because historical data is observational, observed links (such as `reminder_sent` and no-show rates) represent associations rather than proven causal effects.
3. **Random train/test split and capacity-dependent threshold:** Using a single random split (where the same patient can appear in both train and test, and cleaning statistics were computed before splitting) may overestimate real-world performance, and the optimal probability threshold depends on daily front-desk call capacity.
