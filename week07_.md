<!--
author:  Sabbir
email:   a.rifat@st.ovgu.de
version: 1.0.0
language: en
narrator: US English Female

comment:  Group Comparison in Quantitative Research Using R (T-test)

-->

# Group Comparison in Quantitative Research Using R (T-test)

<span style="font-size: 22px;">__Teaching Team__</span>
 

> **Responsible Teacher:** M.A. Hannes Tegelbeckers  
> **Tutors:** A E M Sabbir Rifat, Masub Makhdoom, Mahwish Kanwal

# Objectives of the Lecture

- Understand different group comparison techniques  
- Learn when to apply each method  
- Apply methods using R  
- Interpret results meaningfully  

# Group Comparison

Group comparison in quantitative research refers to the statistical methods used to compare differences between groups in terms of their central tendencies (e.g., means, medians), variability or other statistical properties. It helps researchers determine whether the observed differences between groups are statistically significant or likely due to random chance.

## Key Measures of Variability

- Range  
- Variance  
- Standard Deviation  
- Interquartile Range (IQR)  

## Assumptions in Group Comparisons

- **Normality**  
  - *Checking tools:* Histograms, Q-Q plots, Density plots, Shapiro-Wilk test, Kolmogorov-Smirnov test  

- **Homogeneity of Variance**  
  - *Checking tools:* Boxplots to compare variances, Levene's Test, Bartlett's Test  

- **Independence of Observations**  
  - *Checking tools:* Random sampling, No overlap between groups  

- **Sample Size Considerations**  
  - *Checking tools:* Power analysis, Sample size guidelines  

### 1. Normality test

__Please follow the instructions given through R__


### 2. Homogeneity of variance text

__Please follow the instructions given through R__

## Key Elements in Test Output (Interpreting Results)

- **Test Statistic**  
  - A numerical value that summarizes the difference between groups.  

- **Degrees of Freedom**  
  - The number of independent pieces of information in the data used for estimating parameters.  

- **p-value**  
  - The probability of obtaining test results at least as extreme as the observed results, assuming the null hypothesis is true.  

- **Significance Level (usually α = 0.05)**  
  - The threshold for determining statistical significance; if p-value < α, reject the null hypothesis.  

- **Rejecting or Failing to Reject the Null Hypothesis**  
  - If p-value < α, reject H₀ (null hypothesis); otherwise, fail to reject H₀.  

- **Effect Size**  
  - A measure of the magnitude of the difference between groups, independent of sample size.  


# Overview of Group Comparison Methods

## Types of Comparison Methods

<div style="text-align: center;">
  <img src="https://raw.githubusercontent.com/AEMSabbirRifat/QuRM-Sabbir-/97cf7d5d07315d817559561f306ead3e6dcfa1a7/media/Group%20Comparison%20in%20Quantitative%20Research%20Using%20R%20(T-test)/Picture1.jpg" alt="Group Comparison Image" width="50%">
</div>


# Parametric Test

--{{0}}--
!?[](https://github.com/OVGU-VET-TechEd/Quantitative_Methods_with_R/blob/dd3cf8ddcc70db81b69716447c4a0457f38f044c/Media_week7/Parametric%20Test.mp4?raw=true)



## Parametric Tests´ Data Set Criteria

- **Normal Distribution**  
  - Data should come from a normal distribution (bell-shaped curve).

- **Homogeneity of Variance**  
  - Groups should have similar variances (spread of data is similar).

- **Interval or Ratio Scale**  
  - Data should be measured on an interval or ratio scale (e.g., height, weight, income).


# T-tests

T-tests are used to **compare means between groups** to determine if differences are **statistically significant**.


 __When to Use:__

 - The **dependent variable** is **continuous**  
  - *(e.g., height, test scores)*  

- The **independent variable** is **categorical with up to two levels**  
  - *(Binary Grouping Variable, e.g., male vs. female)*


## Types of T-tests

<div style="text-align: center;">
  <img src="https://raw.githubusercontent.com/AEMSabbirRifat/QuRM-Sabbir-/97cf7d5d07315d817559561f306ead3e6dcfa1a7/media/Group%20Comparison%20in%20Quantitative%20Research%20Using%20R%20(T-test)/Picture2.jpg" alt="Group Comparison Image" width="50%">
</div>




## Types of T-tests (contn.)

| Type                                  | Purpose                                              | Example |
|---------------------------------------|------------------------------------------------------|-----------------------------------------------------------|
| **One-Sample T-test**                 | Compare the mean of one group to a known value.      | Test if average working hours differ from 40 hours.       |
| **Independent T-test (Two-Sample T-test)** | Compare means of two independent groups.        | Test if male and female students have different math scores. |
| **Paired T-test**                      | Compare means of two related groups (same individuals). | Test if an exercise program improves weightlifting performance before and after participation. |


## One-Sample T-test in R
__Please follow the instructions given through R__

## Two-Sample T-test (Independent T-test) in R
__Please follow the instructions given through R__

## Paired T-test (Dependent T-test) in R
__Please follow the instructions given through R__

# Practical Exercise

**Formulate and test a hypothesis using either an Independent T-test or Dependent T-test.**

- Based on your dataset, create a hypothesis suitable for either an **Independent or Dependent T-test**.  
- If necessary, modify the dataset to ensure it fits the hypothesis.  
  - Justify these changes logically in your hand-in document.  
- Perform the **T-test in R**, following the procedure discussed in class.  
- **Interpret the output and check the p-value.**  
- If the result is **significant**, explain why.  
- If **not significant**, explain why.  

<span style="background-color: yellow; font-weight: bold;">Use AI platforms for this step-by-step procedure, check feedback, rationalize and improve accordingly</span>


