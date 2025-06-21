<!--
author:  Sabbir
email:   a.rifat@ovgu.de
version:  0.1.0
language: en
comment:  A presentation on Correlation & Regression Analysis in Quantitative Research Using R
license: CC-BY-4.0

link:     https://cdn.jsdelivr.net/chartist.js/latest/chartist.min.css
script:   https://cdn.jsdelivr.net/chartist.js/latest/chartist.min.js
-->

# Group Comparison in Quantitative Research Using R (ANOVA)

**Responsible Teacher:** M.A. Hannes Tegelbeckers  
**Tutors:** A E M Sabbir Rifat, Masub Makhdoom, Mahwish Kanwal


# Objectives of the Lecture

- Explore and analyze the following techniques:

  - **ANOVA (Analysis of Variance)**
  - **Non-Parametric Tests**:
    - Chi-Squared Test
    - Mann-Whitney U
    - Kruskal-Wallis
    - Fisher’s Exact Test
- Criteria for selecting the appropriate method
- Understand and evaluate assumptions for each statistical method.
- Gain practical skills in performing these analyses using **R programming**.


--{{0}}--
!?[Video: Correlation Explained (Noor)](https://github.com/OVGU-VET-TechEd/Quantitative_Methods_with_R/blob/abfdcc59c2277c9b61034ba538c3c66ac1fc6007/Media_week9/Noor%202.mp4?raw=true)


# Group Comparison

Group comparison in quantitative research refers to the statistical methods used to compare differences between groups in terms of their central tendencies (e.g., means, medians), variability or other statistical properties. It helps researchers determine whether the observed differences between groups are statistically significant or likely due to random chance.



## Overview of Group Comparison Methods

<div style="text-align: center;">
  <img src="https://github.com/AEMSabbirRifat/QuRM-Sabbir-/raw/e90eec1f9b8a39b135be76f5e965a4de4f2ab9e2/media/Group%20Comparison%20in%20Quantitative%20Research%20Using%20R%20(Part%202)/Picture1.jpg" alt="Group Comparison Image" width="42%">
</div>

**For a better insight, please check out the following videos.**

🔹 [Parametric and Nonparametric Tests (Part 1)](https://www.youtube.com/watch?v=ftnOBcXtBEQ)

🔹 [Parametric and Nonparametric Tests (Part 2)](https://www.youtube.com/watch?v=welIUs2boQ8)

# Parametric Tests

 __Data Set Criteria__

- Data should come from a **normal distribution** (bell-shaped curve).
- Groups should have **similar variances** (spread of data is similar).
- The data should be measured on an **interval or ratio scale** (e.g., height, weight, income).



## Analysis of Variance (ANOVA)

__ANOVA__ (Analysis of Variance) is a statistical method used to compare the means of three or more groups to determine if there are significant differences among them. Instead of performing multiple T-tests (which increases the risk of errors), ANOVA provides a single statistical test to assess whether at least one group mean is different from the others.

🔹**For a better insight, please check out the following videos.**
<div align="center">
  <iframe width="450" height="300" src="https://www.youtube.com/embed/0NwA9xxxtHw?si=tzFunIJHUHcw_vFw" 
    title="YouTube video player" frameborder="0" 
    allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" 
    referrerpolicy="strict-origin-when-cross-origin" allowfullscreen>
  </iframe>
</div>


## When to Use ANOVA

- **Continuous dependent variable**
- **Categorical grouping variable**
- **Multiple groups (3 or more)**

## Types of Analysis of Variance (ANOVA)

<div style="text-align: center;">
  <img src="https://github.com/AEMSabbirRifat/QuRM-Sabbir-/raw/a97504babab47c887b4cb66c8c6cb36a06620703/media/Group%20Comparison%20in%20Quantitative%20Research%20Using%20R%20(Part%202)/Picture2.jpg" alt="Group Comparison Image" width="40%">
</div>

**For a better insight, please check out the following videos.**

🔹 [One Way ANOVA](https://www.youtube.com/watch?v=mOdYddj5IG8)

🔹 [Two Way ANOVA](https://www.youtube.com/watch?v=7OY0DdFyJBg)

🔹 [Repeated Measures ANOVA](https://www.youtube.com/watch?v=SqWeHRvWQZ0)



## Types of Analysis of Variance (ANOVA) (contn.)

__One-way ANOVA__

- **Hypothesis (Ha):** At least one month's mean ozone level is different from the others.  
- **Null Hypothesis (H0):** The mean ozone levels are the same across all months.  


🔹**For statistical analysis, please follow the instructions given through R. Please try to aligh your codes according to your dataset with the help of AI.**

```r
# Load necessary libraries
if (!requireNamespace("dplyr", quietly = TRUE)) install.packages("dplyr")
if (!requireNamespace("ggplot2", quietly = TRUE)) install.packages("ggplot2")

library(dplyr)
library(ggplot2)

# Load the dataset
data("airquality")

# Remove rows with missing values
cleaned_data <- na.omit(airquality)

# Perform one-way ANOVA
anova_result <- aov(Ozone ~ as.factor(Month), data = cleaned_data)
summary(anova_result)

# Visual Representation 1: Boxplot
boxplot(Ozone ~ as.factor(Month), 
        data = cleaned_data, 
        main = "Boxplot of Ozone Levels Across Months", 
        xlab = "Month", 
        ylab = "Ozone Levels", 
        col = rainbow(5), 
        border = "darkblue")

# Add jittered points to the boxplot
points(jitter(as.numeric(as.factor(cleaned_data$Month))), 
       cleaned_data$Ozone, 
       col = "red", 
       pch = 16)

# Visual Representation 2: Bar Plot with Error Bars
# Calculate means and standard errors for Ozone by Month
summary_stats <- cleaned_data %>%
  group_by(Month) %>%
  summarise(
    Mean_Ozone = mean(Ozone, na.rm = TRUE),
    SE_Ozone = sd(Ozone, na.rm = TRUE) / sqrt(n())
  )

# Create a bar plot with error bars
ggplot(summary_stats, aes(x = as.factor(Month), y = Mean_Ozone)) +
  geom_bar(stat = "identity", fill = "skyblue", color = "black", width = 0.7) +
  geom_errorbar(aes(ymin = Mean_Ozone - SE_Ozone, ymax = Mean_Ozone + SE_Ozone), 
                width = 0.2, color = "red") +
  labs(
    title = "Mean Ozone Levels Across Months",
    x = "Month",
    y = "Mean Ozone Levels"
  ) +
  theme_minimal()
```

## Types of Analysis of Variance (ANOVA) (contn.)

__Two-way ANOVA__

- **Hypothesis (Ha):** There is an interaction effect between the factors (dose and supp) on tooth length.
- **Null Hypothesis (H0):** There is no interaction effect between the factors (dose and supp) on tooth length.

🔹**For statistical analysis, please follow the instructions given through R. Please try to aligh your codes according to your dataset with the help of AI.**

```r
# Load the ToothGrowth dataset
data("ToothGrowth")

# Check for missing values and remove them (if any)
ToothGrowth <- na.omit(ToothGrowth)

# Perform Two-Way ANOVA
anova_result <- aov(len ~ dose * supp, data = ToothGrowth)

# Summary of the ANOVA result
summary(anova_result)

# Load required library
library(ggplot2)

# Create a boxplot to visualize the effects of dose and supp on tooth length
ggplot(ToothGrowth, aes(x = interaction(dose, supp), y = len, fill = supp)) +
  geom_boxplot() +
  labs(x = "Dose and Supplement Combination", y = "Tooth Length") +
  theme_minimal() +
  theme(axis.text.x = element_text(angle = 45, hjust = 1))

# Load required library
library(ggplot2)

# Calculate the mean tooth length for each combination of dose and supplement
means <- aggregate(len ~ dose + supp, data = ToothGrowth, FUN = mean)

# Create the bar chart
ggplot(means, aes(x = interaction(dose, supp), y = len, fill = supp)) +
  geom_bar(stat = "identity", position = "dodge") +
  labs(x = "Dose and Supplement Combination", y = "Mean Tooth Length") +
  theme_minimal() +
  theme(axis.text.x = element_text(angle = 45, hjust = 1))
```


## Types of Analysis of Variance (ANOVA) (contn.)

__Repeated Measures ANOVA__

- **Alternative Hypothesis (Ha):** There is a significant difference in the mean extra hours of sleep between the drug and placebo groups (i.e., the drug has an effect).
- **Null Hypothesis (H0):** There is no difference in the mean extra hours of sleep between the drug and placebo groups (i.e., the drug does not have an effect).

🔹**For statistical analysis, please follow the instructions given through R. Please try to aligh your codes according to your dataset with the help of AI.**

```r

# Load the necessary libraries
library(ggplot2)
library(car)

# Load the dataset
data(sleep)

# Check for missing values
sum(is.na(sleep))

# Perform repeated measures ANOVA
anova_result <- aov(extra ~ factor(group) + Error(factor(ID)/factor(group)), data = sleep)

# Show the summary of the ANOVA result
summary(anova_result)

# Create a boxplot to visualize the data
ggplot(sleep, aes(x = factor(group), y = extra, fill = factor(group))) +
  geom_boxplot() +
  labs(title = "Effect of Drug vs Placebo on Extra Sleep", 
       x = "Group (1 = Drug, 2 = Placebo)", 
       y = "Extra Sleep (hours)") +
  theme_minimal() +
  scale_fill_manual(values = c("skyblue", "orange"))


```


## Post-Hoc Tests for ANOVA

A post-hoc test is used after ANOVA to perform pairwise comparisons between the group means and determine where the differences lie. 
One commonly used post-hoc test is **Tukey’s Honest Significant Difference (HSD) test**.

__When to Use Tukey’s HSD__

- There are three or more groups.
- You need to compare every pair of groups to determine which specific groups differ from each other.


## Introduction to MANOVA

- **Extension of ANOVA** for multiple dependent variables.
- Compares means across groups on a combination of dependent variables.

__When to Use MANOVA__

- a. Multiple related dependent variables.
- b. One or more independent variables (factors).

🔹**For a better insight, please check out the following videos.**

<div align="center">
  <iframe width="450" height="225" src="https://www.youtube.com/embed/CBXYxs9pLW8?si=LukltCq9UPFidZgX" 
    title="YouTube video player" frameborder="0" 
    allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" 
    referrerpolicy="strict-origin-when-cross-origin" allowfullscreen>
  </iframe>
</div>


## Self assessment

1. What is the primary purpose of a **One-Way ANOVA**?

[[X]] To compare the means of **three or more groups** based on **one independent variable**
[[ ]] To compare two dependent variables simultaneously
[[ ]] To examine interaction effects between two independent variables
[[ ]] To analyze repeated measures over time
[[?]] Think about the number of groups and how many independent variables are involved.
********************************************************************************

**Explanation:**

- ✅ **Correct!** One-Way ANOVA is used when comparing **three or more group means** based on **a single independent variable**.

- ❌ **To compare two dependent variables simultaneously** is a task for **MANOVA**, not One-Way ANOVA.

- ❌ **To examine interaction effects between two independent variables** refers to **Two-Way ANOVA**.

- ❌ **To analyze repeated measures over time** refers to **Repeated Measures ANOVA**.

********************************************************************************

2. What makes **Two-Way ANOVA** different from One-Way ANOVA?

[[ ]] It compares only two groups
[[X]] It evaluates the effect of **two independent variables** and their interaction on a dependent variable
[[ ]] It uses multiple dependent variables
[[ ]] It is only used for time-series data
[[?]] Think about the number of independent variables and interactions.
********************************************************************************

**Explanation:**

- ✅ **Correct!** Two-Way ANOVA involves **two independent variables**, allowing you to assess **individual effects and interactions**.

- ❌ **It compares only two groups** — incorrect; it can compare multiple group combinations.

- ❌ **It uses multiple dependent variables** — that’s what **MANOVA** does.

- ❌ **It is only used for time-series data** — that’s a misconception; time-series is not a requirement here.

********************************************************************************

3. When should you use a **Repeated Measures ANOVA**?

[[ ]] When you compare multiple groups that are unrelated
[[ ]] When your dependent variable is categorical
[[X]] When you measure the **same subjects at multiple time points or under different conditions**
[[ ]] When analyzing the interaction between two independent variables
[[?]] Consider situations where measurements are repeated on the same individuals.
********************************************************************************

**Explanation:**

- ✅ **Correct!** Repeated Measures ANOVA is used when **the same subjects** are tested under **different conditions or over time**.

- ❌ **When you compare unrelated groups** — One-Way or Two-Way ANOVA is more appropriate.

- ❌ **When your dependent variable is categorical** — ANOVA assumes the dependent variable is continuous.

- ❌ **Analyzing interaction between two variables** — refers to **Two-Way ANOVA**.

********************************************************************************


4. What is the key feature of **MANOVA**?

[[ ]] It analyzes the mean difference of one dependent variable across multiple groups
[[ ]] It is limited to only one independent variable
[[X]] It tests for differences in **multiple dependent variables** simultaneously
[[ ]] It can only be used with repeated measures
[[?]] Focus on how many dependent variables are involved.
********************************************************************************

**Explanation:**

- ✅ **Correct!** MANOVA (Multivariate ANOVA) analyzes **two or more dependent variables** at the same time.

- ❌ **It analyzes one dependent variable** — that would be **ANOVA**, not MANOVA.

- ❌ **Limited to one independent variable** — MANOVA can handle multiple independent variables too.

- ❌ **Only for repeated measures** — not necessarily; repeated measures MANOVA exists, but it's not a requirement.

********************************************************************************

# Non-Parametric Tests

__Non-parametric tests__ are statistical methods that do not assume a specific distribution for the data. They are particularly useful for analyzing ordinal data or when sample sizes are small, as they rely on ranks rather than raw data values.


## **Non-Parametric Tests (Data set criteria)**

- Data can be **ordinal** (ranked) or **nominal** (categorical).  
- Some tests can be applied to **interval** or **ratio** data that do not meet parametric assumptions.  
- **Small sample sizes** where parametric tests might lack power or reliability.  
- **No assumption** of normality in the data distribution.  
- Non-parametric tests are **robust to outliers** and skewed distributions.  
- **Independence of observations** is still a key requirement for most tests.  
- **Does not rely** on equal variances between groups assumption.  
- Use when **assumptions of parametric tests are violated**.  


## **Chi-Squared Test**

- Used for **categorical variables**  
- Compares **observed frequencies** with **expected frequencies**  
- It does not require assumptions of **normality** or **homogeneity of variance**  
- Data is arranged in a **table (2x2 or larger)**  
- Shows the **frequency counts** for combinations of categories.  

🔹**For a better insight, please check out the following videos.**

<div align="center">
  <iframe width="450" height="300" src="https://www.youtube.com/embed/rpKzq64GA9Y?si=Xe3-ufnlTO4l1UYR" 
    title="YouTube video player" frameborder="0" 
    allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" 
    referrerpolicy="strict-origin-when-cross-origin" allowfullscreen>
  </iframe>
</div>


## **Chi-Squared Test (contn.)**

- **Null Hypothesis (H₀):** There is no association between car efficiency (efficiency) and the number of cylinders (cyl_cat).  
- **Alternative Hypothesis (H₁):** There is an association between car efficiency (efficiency) and the number of cylinders (cyl_cat).  

🔹**For statistical analysis, please follow the instructions given through R. Please try to aligh your codes according to your dataset with the help of AI.**

```r
# Load the mtcars dataset
data(mtcars)

# Categorize mpg into "Low" and "High" efficiency
mtcars$efficiency <- ifelse(mtcars$mpg > 20, "High", "Low")

# Categorize the number of cylinders
mtcars$cyl_cat <- as.factor(mtcars$cyl)

# Create a contingency table
contingency_table <- table(mtcars$efficiency, mtcars$cyl_cat)

# Perform the Chi-Square test
chi_result <- chisq.test(contingency_table)

# Display the contingency table and test results
print("Contingency Table:")
print(contingency_table)
print("Chi-Square Test Results:")
print(chi_result)

# Visualization using ggplot2
if (!requireNamespace("ggplot2", quietly = TRUE)) install.packages("ggplot2")
library(ggplot2)

# Convert the table to a data frame for plotting
contingency_df <- as.data.frame(contingency_table)
colnames(contingency_df) <- c("Efficiency", "Cylinders", "Frequency")

# Create a bar plot to visualize the relationship
ggplot(contingency_df, aes(x = Cylinders, y = Frequency, fill = Efficiency)) +
  geom_bar(stat = "identity", position = "dodge") +
  labs(title = "Relationship Between Efficiency and Cylinders",
       x = "Number of Cylinders",
       y = "Frequency",
       fill = "Efficiency") +
  theme_minimal()


here

# Perform Fisher's Exact Test
fisher_result <- fisher.test(contingency_table)

# Display the results
print("Fisher's Exact Test Results:")
print(fisher_result)
# Perform Chi-Square Test with simulated p-values
chi_result_simulated <- chisq.test(contingency_table, simulate.p.value = TRUE, B = 10000)

# Display the results
print("Chi-Square Test with Simulated p-Value:")
print(chi_result_simulated)
# Create a grouped bar plot
library(ggplot2)

# Convert contingency table to a data frame
contingency_df <- as.data.frame(as.table(contingency_table))
colnames(contingency_df) <- c("Efficiency", "Cylinders", "Count")

# Plot
ggplot(contingency_df, aes(x = Cylinders, y = Count, fill = Efficiency)) +
  geom_bar(stat = "identity", position = "dodge") +
  labs(title = "Car Efficiency vs. Cylinders",
       x = "Number of Cylinders",
       y = "Count") +
  theme_minimal()
# Create a mosaic plot
mosaicplot(contingency_table,
           main = "Mosaic Plot: Car Efficiency vs. Cylinders",
           color = TRUE,
           shade = TRUE)


```

## **Types of Chi-Squared Tests**  

🔹 [**Goodness of Fit Test**](https://www.youtube.com/watch?v=y24q6BhRiDc)

🔹 [**Test of Independence**](https://www.youtube.com/watch?v=AuYRQVmpSaE)


## **Mann-Whitney U Test / Wilcoxon Rank-Sum Test**  

- An **alternative** to the Independent Samples t-test  
- It **compares differences** between two independent groups on a **continuous or ordinal** dependent variable.  
- It tests whether the **distribution** of the two groups is the same or if one group tends to have **higher values** than the other.  


## **Kruskal-Wallis Test**  

- **Compare 3+ independent groups**  
- It is an **extension** of the Mann-Whitney U Test to more than two groups  
- The test evaluates whether the **distributions** of the groups are the same  
- Checks if one or more groups have **systematically higher or lower values** than the others.  


## **Fisher’s Exact Test**  

- **Alternative** to Chi-squared for **small sample sizes**  
- Determines if there is a **significant association** between two categorical variables in a **contingency table**  
- Calculates the **exact probability** of observing the data under the **null hypothesis**  
- **Ideal** for small datasets  


# 🎉 Semester Wrap-Up!

🌟 **You've made it to the end!** No more tasks this week; just a moment to pause, breathe and feel proud of how far you've come.

> “Success is the sum of small efforts, repeated day in and day out.” — Robert Collier

![Relax, Recharge, Reflect](https://media.giphy.com/media/3o6Zt481isNVuQI1l6/giphy.gif)

🙌 Thank you for staying engaged. You’re awesome!

