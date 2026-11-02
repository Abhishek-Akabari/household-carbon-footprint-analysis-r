# Household Carbon Footprint Analysis (R)

A statistical analysis of what drives household carbon footprint, plus a model of which households recycle regularly. Written in R Markdown for the Quantitative Data Analysis module of my MSc in Artificial Intelligence at Brunel University London.

## What I did

### 1. Data quality: plan before cleaning
I checked every variable against the metadata before changing anything. That meant data types, ranges, logical consistency, missing values, duplicates and category labels. Three real problems turned up:

| Issue | What I found | What I did and why |
|---|---|---|
| Impossible household size | Households with **0 adults** | Removed them. Making up an adult count would have meant inventing data. |
| Impossible energy use | **Negative** annual energy consumption | Removed them. The size of the error was unknown, so imputing would have been a guess. |
| Messy category labels | `detatched`, `Terrrace`, mixed capitals, so 8 home-type labels instead of 4 | Standardised to the 4 real categories instead of dropping rows. |

The 22 missing income values were noted but not treated as an error: income isn't the outcome being modelled, and it's often left blank in self-reported surveys. After cleaning, 217 of the 240 households in my subset remained.

### 2. Exploratory analysis

| Carbon footprint | Energy use vs footprint | By home type |
|---|---|---|
| ![](images/carbon_footprint_hist.png) | ![](images/energy_vs_carbon.png) | ![](images/carbon_by_home_type.png) |

- Carbon footprint is right-skewed: most households sit low to mid-range, with a tail of high emitters.
- Annual energy use has a strong positive relationship with footprint (correlation about 0.77).
- Home types differ a little, but their distributions overlap a lot.

### 3. Modelling carbon footprint: multiple linear regression
- **Initial model:** energy consumption + number of adults + home type.
- **Final model:** number of adults was dropped because it added nothing (p = 0.54).
- **Result:** the model explains about **60% of the variation** in carbon footprint (R² = 0.60, adjusted 0.59). Each extra unit of annual energy use adds about **1.79** to the footprint (p < 0.001). Home type was not significant once energy use was accounted for.
- **Diagnostics:** residuals are centred on zero and roughly normal, with no highly influential points, but there is some heteroscedasticity at higher fitted values.

![Model diagnostics](images/model_diagnostics.png)

### 4. Modelling recycling: logistic regression
I modelled whether a household recycles regularly from energy use, number of adults, home type and diet. None of these were significant predictors. Vegetarian households had the highest odds of recycling (odds ratio about 1.86, p = 0.10), but the uncertainty is large. The honest conclusion is that recycling behaviour depends on things this dataset doesn't capture, such as local facilities and attitudes.

## Files

- `analysis.Rmd`: the main write-up (data quality, cleaning, EDA, both models)
- `supporting_checks.Rmd`: the extra checks behind each claim (summaries, correlations, confusion matrix, odds-ratio confidence intervals)
- `images/`: figures from the analysis

## How to run

The dataset (`carbon-footprint.Rda`) was provided by the module and isn't included in this repository. If you have it, put it in this folder and knit `analysis.Rmd` in RStudio. The analysis uses a fixed subset of every 50th row, chosen by an ID-based offset, so the results are reproducible.

```r
install.packages(c("dplyr", "stringr", "ggplot2", "rmarkdown"))
rmarkdown::render("analysis.Rmd")
```

## Tools

R, tidyverse (dplyr, stringr, ggplot2), base R graphics, `lm` and `glm`, R Markdown.

---
Abhishek Akabari · [LinkedIn](https://www.linkedin.com/in/abhishek-akabari-1ba63626b) · [GitHub](https://github.com/Abhishek-Akabari)
