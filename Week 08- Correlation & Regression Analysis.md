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

# Correlation & Regression Analysis in Quantitative Research

**Responsible Teacher:** M.A. Hannes Tegelbeckers  
**Tutors:** A E M Sabbir Rifat, Masub Makhdoom, Mahwish Kanwal

## Objectives of the Lecture

- Understand the concept of correlation and its applications.
- Learn how to compute and interpret correlation coefficients.
- Explore the basics of regression analysis and its assumptions.
- Understand how to build and interpret regression models.
- Discuss the limitations and practical considerations of these methods.

--{{0}}--
!?[Video: Correlation Explained (Noor)](https://github.com/OVGU-VET-TechEd/Quantitative_Methods_with_R/blob/7e14b672028401d1610f1ffe5df3f13de567e0b3/Media_week8/Noor.mp4?raw=true)


## Correlation

Correlation analysis is a statistical method used to assess the strength and direction of the relationship between two variables. It indicates how closely the variables with a linear relationship are associated.(RJ Janse,2021)


🔹**For a better insight, please check out the following video**


<iframe width="450" height="350" src="https://www.youtube.com/embed/qo1FVrlvW1Y?si=LGg5KHDB96qJeN0S" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>


### Key Points:

- Ranges from -1 to +1
- Positive values indicate a positive relationship
- Negative values indicate a negative relationship
- Values closer to ±1 indicate stronger relationships
- Value of 0 indicates no linear relationship

## When to Use Correlation Statistical Analysis

Correlation analysis is appropriate in the following scenarios:

- To assess the strength and direction of a relationship between two quantitative variables.
- Before conducting regression analysis to identify potential predictors.
- In exploratory data analysis to uncover patterns.

## Conditions for Correlation Statistical Analysis

For valid correlation analysis, these conditions should be met:

1. **Quantitative Variables**: Both variables must be numeric.
2. **Linearity**: The relationship between variables should be approximately linear.
3. **Normality**: Data should follow a normal distribution (for Pearson correlation).
4. **No Outliers**: Outliers can distort the correlation coefficient.
5. **Independent Observations**: Data points should not be influenced by each other.

<div class="ct-chart ct-golden-section">
<!-- Add an r-plot here showing these conditions -->
</div>

## Types of Correlation

🔹**For a better insight, please check out the following video.**

  <tr>
    <td>
      <img src="https://raw.githubusercontent.com/AEMSabbirRifat/QuRM-Sabbir-/5c0824f35eba6bafaacfcb7be99a2323fc2d4eb3/media/Correlation%20%26%20Regression/Picture1.jpg" alt="Correlation and Regression Image" width="460" height="10"/>
    </td>



    <td>
      <iframe width="400" height="220" src="https://www.youtube.com/embed/XWFMypQkZ7Y?si=wKW0Ly5FDiG7wKWi" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>
    </td>
  </tr>

## Correlation Coefficient

**A correlation coefficient (r)** is a statistical measure that quantifies the linear association between variables. It can indicate positive correlation, where both variables increase together or negative correlation, where one variable increases while the other decreases.

🔹**For a better insight, please check out the following video.**

<iframe width="450" height="350" src="https://www.youtube.com/embed/11c9cs6WpJU?si=0aqKeohk9KZVRNKt" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>


### Interpretation Guidelines:

- **r = +1**: Perfect positive correlation
- **r = 0**: No linear correlation
- **r = -1**: Perfect negative correlation
- **0 < |r| < 0.3**: Weak correlation
- **0.3 ≤ |r| < 0.7**: Moderate correlation
- **0.7 ≤ |r| < 1**: Strong correlation

## Types of Correlation Coefficient

![Regression Output Image](https://raw.githubusercontent.com/AEMSabbirRifat/QuRM-Sabbir-/5c0824f35eba6bafaacfcb7be99a2323fc2d4eb3/media/Correlation%20%26%20Regression/Picture3.jpg) 


## Correlation vs Correlation Coefficient

| **Aspect** | **Correlation** | **Correlation Coefficient** |
|:-----------|:----------------|:----------------------------|
| **Definition** | Describes the relationship between variables. | Quantifies the strength and direction of the relationship. |
| **Nature** | Conceptual and descriptive. | Numerical and measurable. |
| **Range** | Not applicable. | Always between −1 and +1. |
| **Types** | Positive, Negative, None. | Pearson's r, Spearman's ρ, Kendall's τ. |
| **Causality** | Explains association but doesn't measure it. | Measures linear or monotonic association. |
| **Examples** | "As X increases, Y increases." | "The correlation coefficient between X and Y is +0.85." |


## Correlation Analysis Through R

**Null Hypothesis (H₀)**:
- There is **no significant correlation** between the variables Ozone and Temp (significance level 5%).

**Hypothesis (H₁)**:
- There is a **significant correlation** between the variables Ozone and Temp.

🔹**For statistical analysis, please follow the instructions given through R. Please try to aligh your codes according to your dataset with the help of AI.**

```r
# Load the airquality dataset
data("airquality")

# Check for missing values
colSums(is.na(airquality)) 

# Remove rows with missing values (optional)
airquality_clean <- na.omit(airquality)

# Select numerical columns for correlation analysis
numeric_vars <- airquality_clean[, c("Ozone", "Solar.R", "Wind", "Temp")]

# Calculate the correlation matrix
cor_matrix <- cor(numeric_vars)

# Print the correlation matrix
print(cor_matrix)

# Visualize the correlation matrix using the corrplot package
if (!require("corrplot")) install.packages("corrplot")  # Install the package if not already installed
library(corrplot)

corrplot(cor_matrix, method = "circle", type = "upper", tl.col = "black", tl.srt = 45)


# Scatter Plot
# Simple scatterplot (Scatterplot of Ozone vs Temperature)
plot(airquality_clean$Temp, airquality_clean$Ozone,
     xlab = "Temperature (F)", 
     ylab = "Ozone (ppb)",
     main = "Scatterplot of Ozone vs Temperature",
     col = "blue", pch = 16)

# Add a regression line
abline(lm(Ozone ~ Temp, data = airquality_clean), col = "red", lwd = 2)


# Scatterplot (Scatterplot of Ozone vs Wind)
plot(airquality_clean$Wind, airquality_clean$Ozone,
     xlab = "Wind Speed (mph)", 
     ylab = "Ozone (ppb)",
     main = "Scatterplot of Ozone vs Wind",
     col = "blue", pch = 16)

# Add a regression line
abline(lm(Ozone ~ Wind, data = airquality_clean), col = "red", lwd = 2)


# Pairwise scatterplot matrix
# Install and load the psych package (if not already installed)
if (!require("psych")) install.packages("psych")
library(psych)

# Pairwise scatterplot matrix with regression lines
pairs.panels(
  airquality_clean[, c("Ozone", "Solar.R", "Wind", "Temp")], # Select relevant variables
  method = "pearson",    # Correlation method (Pearson by default)
  hist.col = "lightblue", # Color for histogram
  lm = TRUE,             # Add regression lines
  main = "Scatterplot Matrix with Regression Lines"
)
```


## Self assessment

1. What does a correlation coefficient of **+0.85** indicate?

[[X]] A strong positive linear relationship
[[ ]] A weak positive linear relationship
[[ ]] No relationship
[[ ]] A strong negative linear relationship
[[?]] Think about what values close to +1 represent in correlation analysis.
********************************************************************************

**Explanation:**

- **A strong positive linear relationship** ✅ **Correct!** A correlation close to +1 indicates a strong positive linear relationship—when one variable increases, the other tends to increase too.

- **A weak positive linear relationship** ❌ **Incorrect.** A weak positive correlation is closer to 0 (e.g., +0.1 to +0.3), not +0.85.

- **No relationship** ❌ **Incorrect.** A coefficient of +0.85 clearly shows a strong relationship, not absence of one.

- **A strong negative linear relationship** ❌ **Incorrect.** A strong **negative** relationship would have a coefficient close to -1.

********************************************************************************

2. Which of the following correlation values indicates the **weakest** relationship?

[[ ]] -0.76
[[X]] +0.02
[[ ]] +0.89
[[ ]] -0.67
[[?]] Consider which value is closest to zero.
********************************************************************************

**Explanation:**

- **-0.76** ❌ **Incorrect.** -0.76 shows a strong negative relationship.

- **+0.02** ✅ **Correct!** A value near 0 means very weak or no linear relationship.

- **+0.89** ❌ **Incorrect.** +0.89 shows a strong positive correlation.

- **-0.67** ❌ **Incorrect.** -0.67 is a moderately strong negative correlation.

********************************************************************************


3. If two variables move in **opposite directions**, what type of correlation do they have?

[[ ]] Positive correlation
[[X]] Negative correlation
[[ ]] No correlation
[[ ]] Spurious correlation
[[?]] Think about what happens when one variable goes up while the other goes down.
********************************************************************************

**Explanation:**

- **Positive correlation** ❌ **Incorrect.** In a positive correlation, both variables increase or decrease together.

- **Negative correlation** ✅ **Correct!** In a negative correlation, one variable increases as the other decreases.

- **No correlation** ❌ **Incorrect.** No correlation means no consistent relationship.

- **Spurious correlation** ❌ **Incorrect.** A spurious correlation refers to a misleading or coincidental association.

********************************************************************************


4. What does a correlation coefficient of **0** mean?

[[ ]] Strong positive relationship
[[ ]] Moderate negative relationship
[[ ]] Linear relationship exists
[[X]] No linear relationship
[[?]] Consider what zero represents in the context of correlation coefficients.
********************************************************************************

**Explanation:**

- **Strong positive relationship** ❌ **Incorrect.** A strong positive relationship would have a value closer to +1.

- **Moderate negative relationship** ❌ **Incorrect.** A moderate negative relationship would have a negative value between -0.3 and -0.7.

- **Linear relationship exists** ❌ **Incorrect.** A coefficient of 0 means **no linear** relationship, though a non-linear one could exist.

- **No linear relationship** ✅ **Correct!** A correlation of 0 means the variables are not linearly related.

********************************************************************************

5. Which method is commonly used in R to **visualize** a correlation matrix?

[[ ]] hist()
[[ ]] t.test()
[[X]] corrplot()
[[ ]] lm()
[[?]] Think about which function is specifically designed for correlation visualization.
********************************************************************************

**Explanation:**

- **hist()** ❌ **Incorrect.** `hist()` creates a histogram, not a correlation matrix.

- **t.test()** ❌ **Incorrect.** `t.test()` is used for hypothesis testing, not visualization.

- **corrplot()** ✅ **Correct!** `corrplot()` from the `corrplot` package is widely used to visualize correlation matrices in R.

- **lm()** ❌ **Incorrect.** `lm()` fits a linear model, but does not visualize correlation matrices.

********************************************************************************

# Regression Analysis

Regression analysis is a statistical method used to analyze experimental data, representing the nature of relationships between studied features. It provides a graphical description and helps identify significant correlations, while also addressing potential issues like false predictions.

🔹**For a better insight, please check out the following video.**


<div align="center">
  <iframe width="450" height="300" src="https://www.youtube.com/embed/-JTKf-a1JpU?si=aPx0kwTpvPtZX_uX" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
</div>


## Correlation vs Regression

| **Feature** | **Correlation** | **Regression** |
|:------------|:----------------|:---------------|
| **Purpose** | Measures the strength and direction of a relationship | Predicts the value of one variable based on another |
| **Output** | A single correlation coefficient (r) | An equation/line to predict outcomes (Y = β₀ + β₁X) |
| **Causality** | No causality, just a relationship | Can imply causality, but only under certain conditions (e.g., experimental design) |
| **Type of Relationship** | Linear relationship between two variables | Can be linear or non-linear (e.g., polynomial regression) |
| **Number of Variables** | Typically between two variables | Can involve one or more independent variables (multiple regression) |



## When to Use Regression Analysis

- To predict outcomes based on known data.
- To evaluate the strength of relationships between variables.
- To identify which variables significantly influence an outcome.

## Conditions for Regression Analysis

- **Quantitative Variables**: Dependent variable must be numeric.
- **Linearity**: The relationship between variables should be linear (for linear regression).
- **Independence**: Observations should be independent.
- **Homoscedasticity**: Equal variance of residuals across all levels of the independent variable.
- **Normality**: Residuals should be normally distributed.
- **Goodness-of-Fit**: R².



## Types of Regression Analysis

<div style="display: flex; gap: 20px; align-items: flex-start; flex-wrap: wrap;">

<div style="flex: 1;">
  <img src="https://raw.githubusercontent.com/AEMSabbirRifat/QuRM-Sabbir-/5c0824f35eba6bafaacfcb7be99a2323fc2d4eb3/media/Correlation%20%26%20Regression/Picture2.jpg" alt="Correlation Matrix Visualization" width="450" />
</div>

<div style="flex: 1; font-size: 16px;">

**For a better insight, please check out the following videos.**


### 🔹 [Simple Linear Regression](https://www.youtube.com/watch?v=gPfgB4ew3RY)

### 🔹 [Multiple Linear Regression Analysis](https://www.youtube.com/watch?v=i3IadpjctWg)

### 🔹 [Non-linear Regression](https://www.youtube.com/watch?v=4hYGiG4qVf4)

### 🔹 [Logistic Regression Analysis](https://www.youtube.com/watch?v=C5268D9t9Ak)

</div>
</div>


## Self assessment


1. In linear regression, what does the **R-squared** value represent?

[[X]] The proportion of variance in the dependent variable explained by the independent variable(s)
[[ ]] The correlation coefficient between variables
[[ ]] The slope of the regression line
[[ ]] The p-value of the regression model
[[?]] Think about what R-squared tells us about how well our model fits the data.
********************************************************************************

**Explanation:**

- **The proportion of variance in the dependent variable explained by the independent variable(s)** ✅ **Correct!** R-squared (coefficient of determination) represents the percentage of variance in the dependent variable that can be explained by the independent variable(s) in the model.

- **The correlation coefficient between variables** ❌ **Incorrect.** The correlation coefficient is a separate measure (r), though R-squared is the square of the correlation coefficient in simple linear regression.

- **The slope of the regression line** ❌ **Incorrect.** The slope is represented by the regression coefficient (β), not R-squared.

- **The p-value of the regression model** ❌ **Incorrect.** The p-value indicates statistical significance, while R-squared measures explained variance.

********************************************************************************

2. What does a **negative slope** in a regression line indicate?

[[ ]] As X increases, Y increases
[[X]] As X increases, Y decreases
[[ ]] There is no relationship between X and Y
[[ ]] The regression model is invalid
[[?]] Consider what happens to Y when X increases if the slope is negative.
********************************************************************************

**Explanation:**

- **As X increases, Y increases** ❌ **Incorrect.** This describes a positive slope, not a negative one.

- **As X increases, Y decreases** ✅ **Correct!** A negative slope means there is an inverse relationship - as the independent variable (X) increases, the dependent variable (Y) tends to decrease.

- **There is no relationship between X and Y** ❌ **Incorrect.** No relationship would be indicated by a slope of zero (horizontal line), not a negative slope.

- **The regression model is invalid** ❌ **Incorrect.** A negative slope is perfectly valid and simply indicates the direction of the relationship.

********************************************************************************

3. Which assumption of linear regression is violated when the **residuals show a clear pattern** when plotted against fitted values?

[[ ]] Normality of residuals
[[ ]] Independence of observations
[[X]] Homoscedasticity (constant variance)
[[ ]] Linearity
[[?]] Think about what patterns in residual plots typically indicate about the variance.
********************************************************************************

**Explanation:**

- **Normality of residuals** ❌ **Incorrect.** Normality assumption is checked using Q-Q plots or normality tests, not residuals vs. fitted values plots.

- **Independence of observations** ❌ **Incorrect.** Independence is typically assessed through the study design and temporal patterns, not through residual patterns against fitted values.

- **Homoscedasticity (constant variance)** ✅ **Correct!** When residuals show patterns (like fanning out or forming curves) against fitted values, it indicates heteroscedasticity - meaning the variance of residuals is not constant across all levels of the fitted values.

- **Linearity** ❌ **Incorrect.** While patterns can sometimes indicate non-linearity, the most common interpretation of patterns in residuals vs. fitted plots is violation of the constant variance assumption.

********************************************************************************




## Simple Linear Regression Analysis Through R

**Null Hypothesis (H₀)**:
- There is **no significant relationship** between **Wind** and **Ozone** (significance level 5%).

**Hypothesis (H₁)**:
- There is a **significant relationship** between **Wind** and **Ozone**.

🔹**For statistical analysis, please follow the instructions given through R. Please try to aligh your codes according to your dataset with the help of AI.**

```r
# Load the dataset
data("airquality")

# Remove rows with missing values
airquality_clean <- na.omit(airquality)

# Perform simple linear regression
simple_lm <- lm(Ozone ~ Wind, data = airquality_clean)

# Print the regression summary
summary(simple_lm)

# Visualization: Scatterplot with regression line
library(ggplot2)
ggplot(airquality_clean, aes(x = Wind, y = Ozone)) +
  geom_point(color = "blue") +
  geom_smooth(method = "lm", se = TRUE, color = "red") +
  labs(title = "Simple Linear Regression: Ozone vs Wind",
       x = "Wind (mph)",
       y = "Ozone (ppb)") +
  theme_minimal()
```

## Multiple Linear Regression Analysis Through R

**Null Hypothesis (H₀)**:
- There is **no significant relationship** between Wind, Solar.R & Temp (Independent variables) and Ozone (dependent variable) (significance level 5%).

**Hypothesis (H₁)**:
- There is a **significant relationship** between at least one of the independent variables (Wind, Solar.R or Temp) and Ozone (Dependent variable).

🔹**For statistical analysis, please follow the instructions given through R. Please try to aligh your codes according to your dataset with the help of AI.**


```r
# Load airquality dataset
data("airquality")

# Clean the dataset by removing rows with missing values
airquality_clean <- na.omit(airquality)

# Perform multiple linear regression
multiple_lm <- lm(Ozone ~ Wind + Solar.R + Temp, data = airquality_clean)

# Display summary of the regression model
summary(multiple_lm)

# Visualize the residuals
par(mfrow = c(2, 2))  # Set plotting layout to 2x2
plot(multiple_lm)     # Diagnostic plots


# Combined Diagnostic Plots
# Install required packages if not already installed
if (!require("ggfortify")) install.packages("ggfortify")

# Load the library
library(ggfortify)

# Perform multiple linear regression
multiple_lm <- lm(Ozone ~ Wind + Solar.R + Temp, data = airquality_clean)

# Create combined diagnostic plots
autoplot(multiple_lm, label.size = 3)

```

## Practical Exercise

**Formulate and test a hypothesis using either an Independent T-test or Dependent T-test**

- Based on your dataset, create 3 hypotheses suitable for correlation and regression analysis.
- If necessary, modify the dataset to ensure it fits the hypothesis. Justify these changes logically in your hand-in document.
- Perform the statistical analysis via R, following the procedure discussed in class.
- Input the visualizations
- Interpret the output and check the p-value.

  - If the result is significant, explain why.
  - If not significant, explain why.


**Use AI platforms for this step-by-step procedure, check feedback, rationalize and improve accordingly**
