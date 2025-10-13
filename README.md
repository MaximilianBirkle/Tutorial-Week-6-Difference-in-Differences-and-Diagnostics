**Assignment.**  Please do in groups of 2-3. You will have two weeks.  Each member of the group should hand in one assignment, but please put everyone’s name on it.

Please look over the paper sufficiently to understand the data and nature of the design.

**Based on:** *Angrist & Kugler (2008). “Rural Windfall or a New Resource Curse? Coca, Income, and Civil Conflict in Colombia.”*

**Dataset:** `data00_AngristKugler.tab`

**Variables:**

| **Variable name** | **Description** | **Type / Coding** | **Notes** |
| --- | --- | --- | --- |
| **dep_ocu** | Department or region of Colombia | Categorical / string or numeric region ID | Identifies geographic units used in the analysis |
| **year** | Year of observation | Integer | Two time periods: pre- and post-bridge disruption |
| **sex** | Sex of the subgroup | 1 = Male, 2 = Female | Use to create gender-specific estimates |
| **age** | Age category | Integer (7 – 23) | Higher values represent older age groups |
| **violent** | Number of violent deaths | Count | Used to compute violence rate |
| **populati** | Total population of the subgroup | Count | Denominator for per-capita measures |
| **grow (constructed below)** | Indicator for coca-growing region | 1 = Growing region, 0 = Non-growing region | Treatment indicator in DiD design |

**Outcome variable:**

$outcome=log\left(\frac{populati+1}{violent+1}\right)$

(logged per-capita violent death rate)

**Generated variable:**

Please create a variable, `grow`, which takes a value of 1 if its department or region (`dep_ocu`) is in the following list, or a 0 otherwise.  Attach it to your data.

```python
13, 18, 19, 50, 52, 86, 95, 97, 99
```

---

### **Q1. Setup and Data Construction**

1. Read in the dataset and subset it to the years 1991, 1992, 1993 and 1996, 1997, 1998.
2. Then create:
    - `after` = 1 if year ∈ {1996, 1997, 1998}, 0 if year ∈ {1991, 1992, 1993}.
    - `growafter = grow × after`.
3. Confirm that:
    - `grow` = 1 in coca-growing regions.
    - `after` = 1 post bridge-disruption.

---

### **Q2. Visualizing Violence Before and After**

1. Produce two density plots:
    - **Left:** Non-growing regions — before vs. after.
    - **Right:** Growing regions — before vs. after.
2. Extend to a **2×2 grid:**
    
    Top = men (`sex = 1`), bottom = women (`sex = 2`).
    
3. Interpretation:
    - Do you see evidence of shifts in violence after the disruption?
    - Are patterns different by gender?

---

### **Q3. Age-Specific Effects**

For coca-growing regions only, plot:

$\Delta_{\text{age}} = \overline{Y}_{\text{after}} - \overline{Y}_{\text{before}}$

by age group.

Does the effect of the air-bridge disruption vary across age categories?

---

### **Q4. Testing the Parallel Trends Assumption (Pre-Treatment)**

Using to **pre-treatment years (1990-1993)**  estimate,:

$\texttt{outcome}_{it}
  = \alpha + \beta\,\texttt{year}_t
  + \gamma\,\texttt{grow}_i
  + \delta\,(\texttt{grow}_i\times \texttt{year}_t)
  + u_{it}$

1. Test whether the interaction terms (`grow × year`) are jointly zero (treat year as both linear and categorical).
    - Report the p-value(s).
    - What does this imply about the validity of parallel trends?
2. Create a **graph of average outcome by year and group** to visually inspect pre-treatment trends.

---

### **Q5. Placebo DiD Test 🧠**

Implement a **placebo difference-in-differences** by pretending that the bridge was disrupted in 1993 instead of 1996:

1. Define a false indicator `placebo_after = 1` if year = 1992 or 1993, and before for 1990-1991.
2. Estimate
    
    $\texttt{outcome}
    = \beta_0 + \beta_1\,\texttt{placebo\_after}
    + \beta_2\,\texttt{grow}
    + \beta_3\,(\texttt{placebo\_after} \times \texttt{grow})
    + u.$
    
3. Interpret the coefficient on `placebo_after × grow`.
    - Should it be statistically significant?
    - What does finding a “significant” placebo effect suggest?
    - Why are placebo tests important for establishing credibility in DiD studies?

---

### **Q6. Covariate Balance at Time 0**

1. Using only **pre-treatment data** (`after = 0`), compare treatment and control regions on:
    - `age`, `sex`, and `populati`.
        
        Present either a **balance table** or a **standardized-difference plot**.
        
2. Discuss:
    - Why is covariate balance before treatment critical for DiD validity?
    - What would imbalance imply about selection into coca-growing regions?

---

### **Q7. Why Covariate Balance Matters**

1. If covariates are balanced at time 0, what does this imply about confounding?
2. What role do these variables play after assignment?
3. If violence trends already differ before treatment, how might this bias your DiD?

---

### **Q8. Covariate Timing and Post-Treatment Bias**

1. Should we include covariates from time 0, time 1, or both?
2. What happens if you include a covariate measured after treatment (e.g., population)?
    - Explain why this may introduce **post-treatment bias**.
    - When, if ever, might adjusting for such variables be appropriate?

---

### **Q9. Computing the DiD Estimate**

1. Compute:
    - Mean difference (after − before) for `grow = 1`
    - Mean difference (after − before) for `grow = 0`
    - Subtract the two to obtain the **DiD estimate**.
2. Why is this estimate preferable to a simple before–after difference in growing regions?

---

### **Q10. Regression Form of DiD**

Estimate:

$\texttt{outcome}
= \beta_0 + \beta_1\,\texttt{after}
+ \beta_2\,\texttt{grow}
+ \beta_3\,(\texttt{after}\times\texttt{grow})
+ u$

- Report $\hat\beta_3$ and its p-value.
- Interpret: what does it tell us about the causal effect of the bridge disruption?
- Show analytically that $\hat\beta_3$ equals

$(\bar{Y}_{1,1}-\bar{Y}_{1,0})-(\bar{Y}_{0,1}-\bar{Y}_{0,0})$

---

### **Q11. Adding Covariates**

Estimate and compare:

1. `outcome ~ grow + after + growafter`
2. Add `age` and `sex`
3. Add `age`, `sex`, and `populati`

Then discuss:

- Does $\hat\beta_3$ change in size or significance?
- Do the covariates have meaningful effects?  Should they?  If the treatment and control are balanced on covariates, what are the covariates for?
- Which specification is most credible, and why?

---

### **Q12. Interpretation and Reflection**

1. Summarize the evidence: did violence increase or decrease after the air-bridge disruption?
2. Does the evidence support a “resource-curse” interpretation?
3. Discuss any remaining identification threats.
