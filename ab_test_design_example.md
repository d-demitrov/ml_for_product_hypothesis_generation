# A/B Test Design Document: Boosting Loan Conversion via BNPL Engagement

### 1. Objective

The objective of this experiment is to increase **`loan_conversion_next30d`** (loan conversion within the next 30 days) by strategically influencing user behavior. Our causal analysis reveals that `bnpl_3_month_orders_share_last30d` has the highest detectable positive causal effect (`point = 0.392689`, `p_value = 0.00101467`) on `loan_conversion_next30d`. Additionally, `bnpl_3_month_orders_share_last30d` is a top-performing feature in predicting loan conversion (importance `0.179572`), second only to `ages`. By driving up the share of e-commerce orders paid via BNPL, we aim to leverage this strong causal link to improve overall loan conversion, which is crucial for business growth and financial product adoption.

### 2. Hypothesis

**Directional Hypothesis:** By increasing the `bnpl_3_month_orders_share_last30d` (share of e-commerce orders paid via BNPL), we will observe a statistically significant increase in `loan_conversion_next30d`.

### 3. Experiment Design (Control vs Treatment)

*   **Control Group:** Users will experience the current product interface and payment options without any specific changes.
*   **Treatment Group:** Users will be exposed to enhanced promotion and visibility of BNPL payment options during their e-commerce journey, particularly at the point of sale or on product detail pages. This could include prominent BNPL messaging, personalized offers, or streamlined BNPL application flows within the e-commerce checkout.
*   **Why this change:** The treatment directly targets `bnpl_3_month_orders_share_last30d`, defined as "share of e-commerce orders paid via BNPL in the last 30 days". Our causal analysis shows a strong positive effect of this feature on `loan_conversion_next30d`.
*   **User Exposure:** Users will be exposed to the treatment during their e-commerce browsing and purchasing flows.
*   **Expected Behavioral Mechanism:** Increased awareness and ease of using BNPL for e-commerce purchases will lead to a higher proportion of orders being paid via BNPL, subsequently increasing the `bnpl_3_month_orders_share_last30d` feature value for these users, which our model predicts will drive higher `loan_conversion_next30d`.

### 4. Target Audience / Eligibility Rules

The experiment will target active users who are eligible for BNPL services and are engaging in e-commerce activity, specifically those making "e-commerce orders" as implied by the `bnpl_3_month_orders_share_last30d` definition. This ensures the audience is relevant to the feature we are attempting to influence and allows for clear measurement of the treatment's effect on BNPL usage in e-commerce.

### 5. Metrics

*   **Primary Metric:**
    *   **`loan_conversion_next30d`** (target metric from causal analysis)
    *   **Expected Direction:** ↑ (Increase)
    *   **Business Interpretation:** Direct measure of success, indicating improved loan product adoption.

*   **Secondary Metrics:**
    *   **`bnpl_3_month_orders_share_last30d`** ("share of e-commerce orders paid via BNPL in the last 30 days")
        *   **Expected Direction:** ↑ (Increase)
        *   **Business Interpretation:** Confirms the treatment is effectively driving BNPL adoption for e-commerce orders, serving as the direct lever for the primary metric.
    *   **`transaction_count_last30d`** ("total number of fintech transactions made by the user in the last 30 days")
        *   **Expected Direction:** Monitor (ideally ↑)
        *   **Business Interpretation:** Serves as a diagnostic for overall user engagement and activity. While not causally significant in our analysis, it has high feature importance (`0.046`).
    *   **`avg_check_ecom_last30d`** ("average check e-com last 30 days")
        *   **Expected Direction:** Monitor (ideally no ↓)
        *   **Business Interpretation:** Ensures that increased BNPL usage does not negatively impact the average value of e-commerce orders.
    *   **`ages`** ("customer age")
        *   **Expected Direction:** Monitor (no significant shift)
        *   **Business Interpretation:** Important feature (`0.38` importance) with a significant causal effect (`0.00732793`, `p_value = 3.85e-12`). Monitoring helps detect unintended demographic shifts in the user base engaging with BNPL, which could have downstream implications for credit risk.

### 6. Success Criteria

The experiment will be considered successful if:
1.  The **Primary Metric (`loan_conversion_next30d`)** shows a statistically significant positive lift (p-value < 0.05).
2.  The key **Secondary Metric (`bnpl_3_month_orders_share_last30d`)** shows a statistically significant positive lift, validating the treatment's intended mechanism.
3.  No other secondary metrics, particularly `avg_check_ecom_last30d`, show a statistically significant negative impact.

### 7. Duration & Sample Size

Given the `loan_conversion_next30d` target metric has a 30-day look-forward window and the `bnpl_3_month_orders_share_last30d` causal effect is substantial (`0.392689` with a small `stderr = 0.119488`), we anticipate a relatively efficient detection of effects.
*   **Minimal Detectable Effect (MDE) Assumption:** We aim to detect a minimal lift in `loan_conversion_next30d` directly proportional to the causal effect observed from `bnpl_3_month_orders_share_last30d`. A relatively small MDE for `loan_conversion_next30d` could still be meaningful given the large causal coefficient. For `bnpl_3_month_orders_share_last30d`, we aim for an MDE that is realistically achievable through the proposed intervention.
*   **Expected Runtime:** We estimate a runtime of **3-4 weeks**. This allows sufficient time for users to be exposed to the treatment, engage with BNPL, and for the `loan_conversion_next30d` window to fully mature for a representative cohort.

### 8. Guardrails

1.  **`avg_check_ecom_last30d` stability:** Monitor this metric to ensure that promoting BNPL doesn't lead to a significant decrease in the average value of e-commerce orders, which could signal a degradation of purchase quality or user behavior for higher-value items.
2.  **`transaction_count_last30d` stability:** Ensure overall user activity in fintech transactions does not significantly decline, indicating no negative impact on broader platform engagement beyond BNPL.
3.  **`ages` distribution:** Closely monitor the distribution of `ages` within the treatment group compared to control. A significant shift could indicate unintended consequences in attracting specific demographics for loan products, which could have risk implications.

### 9. Risks & Mitigations

1.  **Risk:** The treatment fails to increase `bnpl_3_month_orders_share_last30d`.
    *   **Mitigation:** Analyze user interaction data (e.g., clicks, impressions on BNPL promotions) to understand why engagement is low. Iterate on the promotion strategy or placement.
2.  **Risk:** Increased BNPL usage leads to a decrease in `avg_check_ecom_last30d` or shifts purchases to lower-margin items.
    *   **Mitigation:** Monitor `avg_check_ecom_last30d` and other relevant e-commerce metrics closely. If a negative trend is observed, consider adjusting BNPL eligibility criteria or minimum order values for promotional offers.
3.  **Risk:** The observed causal link does not fully translate into the live experiment, leading to no significant lift in `loan_conversion_next30d` despite increased `bnpl_3_month_orders_share_last30d`.
    *   **Mitigation:** This could indicate unmeasured confounding factors or a difference between observational causal analysis and experimental intervention. Post-experiment, a deeper dive into segmented user behavior and potentially additional qualitative research would be conducted.
4.  **Risk:** Increased exposure to BNPL leads to unintended negative consequences on user trust or increased customer support inquiries related to BNPL.
    *   **Mitigation:** Monitor customer feedback channels, support ticket volumes, and relevant sentiment analysis. Be prepared to pause or roll back the experiment if user sentiment becomes significantly negative.
5.  **Risk:** The experiment causes a disproportionate shift in the `ages` distribution for loan conversion, attracting younger or potentially higher-risk segments to loans.
    *   **Mitigation:** Proactively monitor `ages` as a guardrail. If significant shifts are detected, conduct further analysis on the credit risk profiles of the newly converted users. This could lead to adjustments in the BNPL promotion targeting or the associated loan product's risk assessment.