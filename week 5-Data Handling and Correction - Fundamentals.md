<!--
author:   Hannes Tegelbckers & Course Team
email:    LiaScript@web.de
version:  1.0.0
language: en
narrator: US English Female

comment:  This course covers fundamental concepts and practical implementations of data handling and correction in R, with interactive examples and visualizations.

script:   https://api.observablehq.com/@observablehq/plot.js?v=3
          https://cdn.jsdelivr.net/pyodide/v0.24.1/full/pyodide.js

link:     https://cdn.jsdelivr.net/npm/animate.css@4.1.1/animate.min.css

import: https://raw.githubusercontent.com/LiaScript/CodeRunner/master/README.md
         https://raw.githubusercontent.com/LiaTemplates/TextTyper/master/README.md
-->

# Data Handling and Correction - Fundamentals

> 📊 Learn how to **UNDERSTAND** your **DATASET** and properly handle, clean, and prepare data for analysis using R. 

[Preview-link](https://liascript.github.io/)

<!-- style="background-color: #fff0f0; padding: 15px; border-radius: 5px;" -->
**IMPORTANT**: All exercises named **TASK** should be handled with the help of AI and the prompts and outputs should be uploaded in **ONE DOCUMENT** into your **PROMPT FOLDER**

**CHOOSE**: Do the **Task** with the given data OR your **OWN DATA**

<!-- style="background-color: #f0f7f0; padding: 15px; border-radius: 5px;" -->
**CODE HANDLING**
The code you see should be **EXECUTABLE** in this presentation in your browser. Please click on the execute button below the lower left corner of every the code box. See example below which should open a black terminal with the following output:
'#' Example code 
R script
**Error: unexpected symbol in "R script"**


```r
# Example code

R script
```
@LIA.r_withShell


## Course Overview

                               --{{0}}--
This course introduces fundamental concepts and practical techniques for handling and correcting data in R. Whether you're working with small datasets or large databases, these skills are essential for any data analyst or researcher.

    {{0-1}}
What you'll learn:

* Data entry error detection and correction
* Missing data handling strategies 
* Outlier detection and treatment
* Basic data transformations
* Quality assurance in data handling

    {{1}}
Prerequisites:

* Basic knowledge of R programming
* Understanding of basic statistical concepts
* RStudio or similar R environment installed

## 1. Reading and Basic Data Inspection

    --{{1}}--
Let's start with the basics of reading data into R and performing initial inspections (example only, please use your own data for trial).

<!-- style="background-color: #f0f7f0; padding: 15px; border-radius: 5px;" -->
**Good to know**: the # symbol allows you to comment data without interfering once the code (script) is executed

```r
library(tibble)  # for as_tibble and rownames_to_column
library(dplyr)   # for the pipe operator %>%

# Now create our dataset
demo_data <- as_tibble(mtcars) %>% 
  rownames_to_column("car_name")  

# Basic inspection commands
head(demo_data)
glimpse(demo_data)          # Quick structure overview
summary(demo_data)          # Summary statistics

# Check for missing values
colSums(is.na(demo_data))
```
@LIA.r_withShell

<!-- style="background-color: #fff0f0; padding: 15px; border-radius: 5px;" -->
**Task**:
What do you know about the mtcars dataset after the head, glimpse and summary inspection (Handin: Prompt + 200 words output)


### Interactive Data Inspection

                               --{{0}}--
Let's try an interactive example where we create and inspect a sample dataset (copy code into your R studio):


```r
# Generate sample data with known issues

sample_data <- data.frame(
  ID = 1:10,
  Value = c(1, 2, NA, 4, 500, 6, 7, NA, 9, 10),
  Category = c("A", "B", "A", "B", "B", "C", "C", "C", "D", "D")
)

# Basic inspection
str(sample_data)
summary(sample_data)
```
@LIA.r_withShell

<!-- style="background-color: #fff0f0; padding: 15px; border-radius: 5px;" -->
**Task**:
Compare the Script and the output of the **str** command (under #Basic Inspection). Desribe what you see (Handin: Prompt + 100 words output).

<!-- style="background-color: #f0f7f0; padding: 15px; border-radius: 5px;" -->
**Good to know**: You can easily use your own dataframe outline (from your questionnaires) to generate dummy data via this process and add some properties which you want to analyse later (e.g. add random N.A data or generate data which has covariate properties so you are able to analyse it for this exercise)



### 🔍 Quick Exercise

Check the generated sample data and identify:

1. How many missing values are there?
2. What's the mean value (excluding missing values)?
3. How many unique categories exist?

<!-- style="background-color: #f0f7f0; padding: 15px; border-radius: 5px;" -->
**Suggestion**: Answer the following question with the help of a quick prompt and check the answer later. The answer code is not executable because it lacks the data input.


    {{2}}
<details>
<summary>💡 Possible Solution</summary>

```r
# Count missing values
sum(is.na(sample_data$Value))

# Calculate mean (excluding NA)
mean(sample_data$Value, na.rm = TRUE)

# Count unique categories
length(unique(sample_data$Category))
```

</details>

## 2. Data Entry Errors

                               --{{0}}--
Data entry errors can significantly impact your analysis. Let's learn how to identify and correct them.

### Types of Data Entry Errors

    {{1}}

1. **Typographical Errors**

   * Mistyped numbers or text
   * Transposed digits
   * Extra spaces or characters

    {{2}}

2. **Format Inconsistencies**

   * Inconsistent date formats
   * Mixed number formats
   * Inconsistent text case

    {{3}}

3. **Measurement Errors**

   * Wrong units
   * Decimal point errors
   * Scale errors

### Detecting Data Entry Errors

```r
# Create data with common errors
error_data <- data.frame(
  ID = 1:5,
  Temperature = c("23.5", "25,4", "2.35e2", "25.7", "257"),
  Date = c("2024-01-01", "01/01/2024", "2024.01.01", "2024-01-01", "01-01-24")
)

# Function to standardize numeric values
standardize_numbers <- function(x) {
  x <- gsub(",", ".", x)  # Replace commas with decimals
  as.numeric(x)           # Convert to numeric
}

# Function to standardize dates
standardize_dates <- function(x) {
  # Add your date standardization logic here
  as.Date(x, format = "%Y-%m-%d")
}

# Apply standardization
clean_data <- error_data
clean_data$Temperature <- standardize_numbers(error_data$Temperature)
clean_data$Date <- standardize_dates(error_data$Date)

# Print original and cleaned data
cat("Original Data:\n")
print(error_data)
cat("\nCleaned Data:\n")
print(clean_data)

```
@LIA.r_withShell

<!-- style="background-color: #fff0f0; padding: 15px; border-radius: 5px;" -->
**Task**:
Study the script and descripe what the different sections do. If you have NA Data in your clean_data output (command: print(clean_data)) then try to address the issue and fix the output so all "Date" values are correctly presented (Handin: Prompt + 250 words output).


### 🛠 Exercise: Error Detection

Try to identify and fix the following issues in this dataset (No Handin required):

```r
messy_data <- data.frame(
  Value = c("1.5", "1,6", "1.7e1", "1.8", "19,000"),
  Category = c("cat1", "Cat1", "CAT1", "cat_1", "category1")
)
```
@LIA.r_withShell

    {{2}}
<details>
<summary>💡 Possible Solution</summary>

```r
messy_data <- data.frame(
  Value = c("1.5", "1,6", "1.7e1", "1.8", "19,000"),
  Category = c("cat1", "Cat1", "CAT1", "cat_1", "category1")
)

# Clean numeric values
clean_values <- standardize_numbers(messy_data$Value)

# Standardize categories
clean_categories <- tolower(gsub("_", "", messy_data$Category))

# Create clean dataset
clean_data <- data.frame(
  Value = clean_values,
  Category = clean_categories
)

print(clean_data)
```
@LIA.r_withShell
