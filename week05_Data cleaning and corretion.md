
<!--
author:   Hannes Tegelbeckers
email:    hannes.tegelbeckers@gmail.com
version:  1.0.0
language: en
narrator: US English Female

comment:  This presentation covers fundamental concepts and practical techniques for reading and manipulating data in R.

script:   https://cdn.jsdelivr.net/pyodide/v0.24.1/full/pyodide.js
import:   https://raw.githubusercontent.com/LiaScript/CodeRunner/master/README.md
-->

# Reading Data in R

> 📊 Learn how to **IMPORT** data from various sources and perform initial inspections.

---

# Course Contents

<!-- style="width: 100%; font-size: 0.9em;" -->
| Section | Content |
|:--------|:--------|
| [Reading Data in R](#Reading-Data-in-R) | • [Data Sources](#1.-Data-Sources)<br>• [File Import Functions](#2.-File-Import-Functions)<br>• [Practical Tips for Importing Data](#3.-Practical-Tips-for-Importing-Data) |
| [Checking for Data Integrity](#Checking-for-Data-Integrity) | • [Identifying Missing Values](#1.-Identifying-Missing-Values)<br>• [Handling Missing Values](#2.-Handling-Missing-Values)<br>• [Detecting Duplicate Entries](#3.-Detecting-Duplicate-Entries)<br>• [Validating Data Types](#4.-Validating-Data-Types) |
| [Dealing with Data Entry Errors](#Dealing-with-Data-Entry-Errors) | • [Common Types of Data Entry Errors](#1.-Common-Types-of-Data-Entry-Errors)<br>• [Detecting Errors in R](#2.-Detecting-Errors-in-R)<br>• [Correcting Errors in R](#3.-Correcting-Errors-in-R) |
| [Outlier Detection](#Outlier-Detection) | • [What Are Outliers?](#1.-What-Are-Outliers%3F)<br>• [Visual Methods for Detecting Outliers](#2.-Visual-Methods-for-Detecting-Outliers)<br>• [Statistical Methods for Detecting Outliers](#3.-Statistical-Methods-for-Detecting-Outliers)<br>• [Handling Outliers](#4.-Handling-Outliers) |
| [Handling Missing Data](#Handling-Missing-Data) | •  [Detecting Missing Data](#1.-Detecting-Missing-Data)<br>• [Handling Missing Data](#2.-Handling-Missing-Data) |
| [Data Transformation](#Data-Transformation) | • [Overview](#Overview)<br>• [Feature Engineering](#1.-Feature-Engineering)<br>• [Reshaping Data](#2.-Reshaping-Data)<br>• [Scaling and Normalization](#3.-Scaling-and-Normalization) |
| [Encoding Categorical Variables](#Encoding-Categorical-Variables) | • [Why Encode Categorical Variables?](#1.-Why-Encode-Categorical-Variables%3F)<br>• [One-Hot Encoding](#2.-One-Hot-Encoding)<br>• [Label Encoding](#3.-Label-Encoding)<br>• [Practical Considerations](#4.-Practical-Considerations) |
| [Combining Datasets](#Combining-Datasets) | • [Why Combine Datasets?](#1.-Why-Combine-Datasets%3F)<br>• [Merging Datasets by Key](#2.-Merging-Datasets-by-Key)<br>• [Practical Considerations](#3.-Practical-Considerations) |
| [Validating Prepared Data](#Validating-Prepared-Data) | • [Why Validate Data?](#1.-Why-Validate-Data%3F)<br>• [Validating Consistency](#2.-Validating-Consistency)<br>• [Validating Summary Statistics](#3.-Validating-Summary-Statistics)<br>• [Validating Relationships](#4.-Validating-Relationships) |

<!-- style="font-size: 0.8em;" -->
> 📚 **Navigation Tips:**
> * Click on any topic to jump to that section
> * Use the LiaScript navigation panel on the left to move between sections

## Overview

This presentation introduces the basics of reading data into R, including:

1. Understanding different data sources.
2. Using appropriate import functions.
3. Addressing common issues during data import.


What you'll learn:

* Key R functions for reading data.
* Handling common issues like delimiters and missing headers.
* Initial inspection of imported datasets.

---

## 1. Data Sources

R provides tools to import data from a variety of sources, including:

* **CSV Files**: Standard comma-separated text files.
* **Excel Spreadsheets**: Workbooks with multiple sheets.
* **Databases**: SQL-based or NoSQL-based data storage systems.
* **Web Data**: APIs or web scraping.

---

### Practical Example: Importing CSV Data

```r
library(readr)

# Import CSV data
data <- read_csv("data.csv")

# View the first few rows
head(data)
```

---

### Exercise: Importing Your Own Data

1. Save a small dataset as `data.csv`.
2. Use `read_csv()` to load it in R.
3. Run `head()` to display the first few rows.

---

## 2. File Import Functions


Here are key R functions to import data:

| **File Type**      | **Function**             | **Package**    |
|--------------------|--------------------------|----------------|
| CSV Files          | `read_csv()`            | `readr`        |
| Excel Files        | `read_excel()`          | `readxl`       |
| Databases          | `dbReadTable()`         | `DBI`, `RSQLite` |
| Web Data (HTML)    | `read_html()`           | `rvest`        |
| JSON Files         | `fromJSON()`            | `jsonlite`     |

---

### Example: Reading Excel Files

```r
library(readxl)

# Import Excel data
excel_data <- read_excel("data.xlsx", sheet = 1)

# View summary
summary(excel_data)
```


---

## 3. Practical Tips for Importing Data


When reading data, you may encounter issues such as:

1. **Incorrect Delimiters**: Specify the correct separator using the `delim` argument.
2. **Missing Headers**: Handle using the `col_names` argument.
3. **Encoding Problems**: Address with the `locale` parameter in functions like `read_csv()`.

---

### Handling Missing Values During Import

```r
library(readr)

# Import CSV data with NA handling
data <- read_csv("data.csv", na = c("", "NA", "-"))

# Count missing values
sum(is.na(data))
```

---

## Task: Initial Data Inspection


Use the following R commands to inspect your imported dataset:

1. **`head()`**: Display the first few rows.
2. **`summary()`**: Show summary statistics.
3. **`str()`**: Display the structure of the dataset.

---

**Example: Inspecting Data****

```r
library(dplyr)

# Load sample data
data <- data.frame(
  ID = 1:10,
  Value = c(1, 2, NA, 4, 500, 6, 7, NA, 9, 10),
  Category = c("A", "B", "A", "B", "B", "C", "C", "C", "D", "D")
)

# Inspect data
head(data)
summary(data)
str(data)
```

### Quick Exercise

For the sample dataset provided:

1. How many missing values are there?
2. What is the mean of the `Value` column (excluding missing values)?
3. How many unique categories exist?

### Possible Solution
<summary>💡 Possible Solution</summary>

```r
# Count missing values
sum(is.na(data$Value))

# Calculate mean (excluding missing values)
mean(data$Value, na.rm = TRUE)

# Count unique categories
length(unique(data$Category))
```


# Checking for Data Integrity

> 🔍 Learn how to ensure your dataset is clean, consistent, and ready for analysis.


This presentation covers methods to ensure data integrity by addressing:

1. Missing values
2. Duplicate entries
3. Data type validation


What you'll learn:

* How to identify and handle missing data.
* Techniques for detecting duplicates.
* Ensuring proper data types for analysis.

---

## 1. Identifying Missing Values


Missing values can cause issues during analysis. Let's learn how to detect them:

**Detect Missing Values**

```r
# Sample dataset
data <- data.frame(
  ID = 1:5,
  Value = c(10, NA, 30, NA, 50)
)

# Identify missing values
is.na(data)

# Count missing values per column
colSums(is.na(data))
```

**Visualizing Missing Data**

```r
# Visualize missing data
library(naniar)

# Generate missing value plot
vis_miss(data)
```

## 2. Handling Missing Values


Once missing values are identified, decide on a strategy to handle them:

**Remove Missing Values**


```r
# Remove rows with missing values
data_clean <- na.omit(data)
print(data_clean)
```

**Impute Missing Values**

```r
# Replace missing values with column mean
data$Value[is.na(data$Value)] <- mean(data$Value, na.rm = TRUE)
print(data)
```

---

## 3. Detecting Duplicate Entries

Duplicates can skew your analysis. Identify and remove them:

**Find and Remove Duplicates**

```r
# Sample data with duplicates
data <- data.frame(
  ID = c(1, 2, 2, 4, 5),
  Value = c(10, 20, 20, 40, 50)
)

# Identify duplicates
duplicated(data)

# Remove duplicates
data_unique <- data[!duplicated(data), ]
print(data_unique)
```


---

## 4. Validating Data Types


Ensure all variables have appropriate data types for analysis:

* Check Data Types

```r
# Check column types
sapply(data, class)
```

* Convert Data Types

```r
# Convert ID to factor
data$ID <- as.factor(data$ID)
print(data)
```

---

## Task: Integrity Check


Perform an integrity check on the dataset:

1. Identify missing values.
2. Detect duplicates.
3. Validate and, if necessary, convert data types.

---

### Solution

```r
# Sample data
data <- data.frame(
  ID = c(1, 2, 2, 4, NA),
  Value = c(10, NA, 30, 40, 50)
)

# Handle missing values
data <- na.omit(data)

# Remove duplicates
data <- data[!duplicated(data), ]

# Convert ID to factor
data$ID <- as.factor(data$ID)
print(data)
```

---


# Dealing with Data Entry Errors

> 🛠 Learn how to detect and correct common data entry errors using R.

---

**Overview**


Data entry errors can significantly impact your analysis. This presentation covers:

1. Common types of errors (typographical, formatting, measurement).
2. Strategies to detect and fix errors in R.


What you'll learn:

* Tools to identify data inconsistencies.
* Practical techniques to clean erroneous data.

---

## 1. Common Types of Data Entry Errors


1. **Typographical Errors**:

-Mistyped values or misplaced characters.
-Example: `1234` instead of `123.4`.

2. **Format Inconsistencies**:

-Mixed date formats (e.g., `2023/01/01` vs. `01-01-2023`).
-Inconsistent numeric representation (e.g., `1,234` vs. `1234`).

3. **Measurement Errors**:

-Incorrect units or scales.
-Example: temperatures recorded in Fahrenheit instead of Celsius.

---

## 2. Detecting Errors in R

**Example: Identifying Format Issues**

```r
# Sample data with errors
data <- data.frame(
  ID = 1:5,
  Temperature = c("23.5", "25,4", "235.0", "25.7", "257"),
  Date = c("2023-01-01", "01/01/2023", "2023/01/01", "2023-01-01", "01-01-23")
)

# View the data
head(data)
```

---

## 3. Correcting Errors in R

**Fixing Numeric Formats**

```r
# Function to standardize numeric values
standardize_numbers <- function(x) {
  x <- gsub(",", ".", x)  # Replace commas with periods
  as.numeric(x)           # Convert to numeric
}

# Apply the function
data$Temperature <- standardize_numbers(data$Temperature)
head(data)
```



### Fixing Date Formats

```r
# Function to standardize dates
standardize_dates <- function(x) {
  as.Date(x, format = "%Y-%m-%d")
}

# Apply the function
data$Date <- standardize_dates(data$Date)
head(data)
```


---

## Task: Correcting Data Entry Errors


1. Create a dataset with common entry errors (e.g., mixed formats or invalid values).
2. Write functions to standardize numeric and date columns.
3. Apply the functions and inspect the cleaned dataset.

**Example Code**

```r
# Sample data
data <- data.frame(
  ID = 1:5,
  Temperature = c("10.5", "11,3", "1.2e2", "10.7", "107"),
  Date = c("2023-01-01", "2023.01.01", "01-01-23", "2023-01-01", "01/01/2023")
)

# Clean numeric and date columns
data$Temperature <- standardize_numbers(data$Temperature)
data$Date <- standardize_dates(data$Date)

# Inspect cleaned data
head(data)
```


---

## Quick Exercise


For the dataset below:
1. Identify and fix numeric formatting issues in the `Value` column.
2. Standardize all entries in the `Category` column to lowercase.

**Dataset**

```r
data <- data.frame(
  Value = c("1.5", "1,6", "1.7e1", "1.8", "19,000"),
  Category = c("CatA", "cata", "CATA", "Cat_A", "categorya")
)
```


---

### Solution

```r
# Fix numeric formats
data$Value <- standardize_numbers(data$Value)

# Standardize categories
data$Category <- tolower(gsub("_", "", data$Category))

# Inspect cleaned data
head(data)
```


---

# Outlier Detection

> 📊 Learn how to identify and handle outliers in your data using R.

---

Outliers can significantly impact your analysis. This presentation covers:

1. Visual methods for detecting outliers.
2. Statistical techniques to identify extreme values.
3. Strategies for handling outliers.


What you'll learn:

* How to detect outliers visually and statistically.
* Practical methods for addressing outliers.

---

## 1. What Are Outliers?

**Key Concepts**

1. Outliers are data points that deviate significantly from others in a dataset.
2. Causes:

- Data entry errors.
- Measurement anomalies.
- Genuine variability.
3. Impact:
- Skewed analysis and misleading results.

---

## 2. Visual Methods for Detecting Outliers

**Example: Box Plots**

```r
# Sample data
data <- data.frame(
  Value = c(5, 7, 8, 100, 6, 7, 5, 8)
)

# Create a box plot
boxplot(data$Value, main = "Box Plot of Values")
```


---

**Example: Scatter Plots**

```r
library(ggplot2)

# Sample data
data <- data.frame(
  x = 1:10,
  y = c(10, 12, 15, 20, 18, 50, 22, 25, 30, 500)
)

# Create a scatter plot
ggplot(data, aes(x = x, y = y)) +
  geom_point() +
  geom_smooth(method = "lm", se = FALSE)
```


---

## 3. Statistical Methods for Detecting Outliers

### Z-Score Method

```r
# Sample data
data <- data.frame(
  Value = c(10, 12, 15, 20, 18, 50, 22, 25, 30, 500)
)

# Calculate Z-scores
z_scores <- (data$Value - mean(data$Value)) / sd(data$Value)

# Identify outliers
outliers <- abs(z_scores) > 3
data[outliers, ]
```

---

### IQR Method

```r
# Calculate IQR
Q1 <- quantile(data$Value, 0.25)
Q3 <- quantile(data$Value, 0.75)
IQR <- Q3 - Q1

# Define bounds
lower_bound <- Q1 - 1.5 * IQR
upper_bound <- Q3 + 1.5 * IQR

# Identify outliers
outliers <- data$Value < lower_bound | data$Value > upper_bound
data[outliers, ]
```


---

## 4. Handling Outliers

### Key Strategies


1. **Remove**: Exclude outliers caused by data entry errors or irrelevant variability.
2. **Transform**: Use log transformations or scaling to reduce outlier impact.
3. **Impute**: Replace extreme values with mean, median, or modeled estimates.

**Example: Replacing Outliers with Median**

```r
# Replace outliers with the median
data_clean <- data$Value
data_clean[outliers] <- median(data$Value)
data_clean
```


---

## Task: Outlier Detection and Handling


1. Create a dataset with extreme values.
2. Use box plots and the IQR method to identify outliers.
3. Handle outliers by replacing them with the column median.

**Example Code**

```r
# Sample data
data <- data.frame(
  Value = c(5, 7, 8, 100, 6, 7, 5, 8)
)

# Detect and replace outliers
Q1 <- quantile(data$Value, 0.25)
Q3 <- quantile(data$Value, 0.75)
IQR <- Q3 - Q1

# Identify bounds
lower_bound <- Q1 - 1.5 * IQR
upper_bound <- Q3 + 1.5 * IQR

# Replace outliers with median
data$Value[data$Value < lower_bound | data$Value > upper_bound] <- median(data$Value)
data
```


---

## Quick Exercise


For the dataset below:

1. Identify outliers using the Z-score method.
2. Replace detected outliers with the column mean.

**Dataset**

```r
data <- data.frame(
  Value = c(10, 20, 15, 30, 18, 150, 25, 1000, 35, 40)
)
```


---

### Possible Solution

```r
# Z-score calculation
z_scores <- (data$Value - mean(data$Value)) / sd(data$Value)

# Replace outliers with mean
data$Value[abs(z_scores) > 3] <- mean(data$Value)
data
```


---

# Handling Missing Data

> 🛠 Learn how to detect and address missing data issues in R.

---

**Overview**


Missing data can introduce bias or reduce the reliability of analysis. This presentation covers:

1. Techniques to detect and visualize missing data.
2. Common strategies for handling missing values.

What you'll learn:

* How to identify missing data patterns.
* Practical approaches to treat missing values.

---

## 1. Detecting Missing Data

**Key Concepts**

1. Missing data is often represented as `NA` in R.
2. Use R functions to detect missing values:
```r
   # Count missing values in each column
   colSums(is.na(data))
```
3. Visualize missing data patterns with the `naniar` or `VIM` package.

**Example: Visualizing Missing Data**

```r
library(naniar)

# Sample data
data <- data.frame(
  ID = 1:10,
  Value = c(1, 2, NA, 4, 5, NA, 7, 8, 9, NA)
)

# Visualize missing values
vis_miss(data)
```


---

## 2. Handling Missing Data

**Key Strategies**


1. **Remove Rows/Columns**: Drop rows or columns with excessive missing values.

```r
   # Remove rows with missing values
   data <- na.omit(data)
```

2. **Impute Values**: Replace missing values with estimates like mean, median, or modeled values.

```r
   # Impute missing values with the column mean
   data$Value[is.na(data$Value)] <- mean(data$Value, na.rm = TRUE)
```

---

**Example: Imputing Missing Values**

```r
# Sample data
data <- data.frame(
  ID = 1:10,
  Value = c(1, 2, NA, 4, 5, NA, 7, 8, 9, NA)
)

# Impute missing values with median
data$Value[is.na(data$Value)] <- median(data$Value, na.rm = TRUE)
data
```


---

**Advanced Methods: Model-Based Imputation**

```r
# Using k-NN imputation (requires VIM package)
library(VIM)

# Sample data
data <- data.frame(
  ID = 1:10,
  Value = c(1, 2, NA, 4, 5, NA, 7, 8, 9, NA)
)

# k-NN imputation
imputed_data <- kNN(data)
imputed_data
```


---

## Task: Handling Missing Data

1. Load a dataset with missing values.
2. Visualize the missing data pattern using the `naniar` package.
3. Apply different imputation methods (mean, median, or k-NN).

**Example Code**

```r
# Visualize missing data
vis_miss(data)

# Impute missing values with mean
data$Value[is.na(data$Value)] <- mean(data$Value, na.rm = TRUE)

# Apply k-NN imputation
imputed_data <- kNN(data)
imputed_data
```

---

## Quick Exercise


For the dataset below:

1. Count the number of missing values in the `Value` column.
2. Impute missing values using the column mean.
3. Visualize the dataset before and after imputation.

**Dataset**

```r
data <- data.frame(
  ID = 1:10,
  Value = c(1, 2, NA, 4, 5, NA, 7, 8, 9, NA)
)
```
---

### Solution

```r
# Count missing values
sum(is.na(data$Value))

# Impute missing values with mean
data$Value[is.na(data$Value)] <- mean(data$Value, na.rm = TRUE)

# View imputed data
data
```

---

# Data Transformation

> 🔄 Learn how to reshape, scale, and engineer features in your data using R.

**Overview**


Data transformation is essential for preparing data for analysis. This presentation covers:

1. Feature engineering techniques.
2. Reshaping data between wide and long formats.
3. Scaling and normalization methods.

What you'll learn:

* How to create new variables from existing data.
* Methods for reshaping and standardizing data.

## 1. Feature Engineering

**Key Concepts**


1. Feature engineering involves creating new variables to enhance analysis.
2. Common techniques:
- Ratios and aggregations.
- Binning and categorization.

**Example: Creating New Variables**

```r
# Sample data
data <- data.frame(
  Sales = c(100, 200, 300),
  Cost = c(60, 120, 180)
)

# Add a new variable: Profit Margin
data$ProfitMargin <- (data$Sales - data$Cost) / data$Sales
data
```

---

## 2. Reshaping Data

**Key Concepts**


1. Reshape data between **wide** and **long** formats using `tidyr`.
2. Wide format: Each variable forms a column.
3. Long format: Each observation forms a row.

### Example: Converting Wide to Long

```r
library(tidyr)

# Sample data
data <- data.frame(
  ID = 1:3,
  Year2020 = c(100, 150, 200),
  Year2021 = c(110, 160, 210)
)

# Convert to long format
long_data <- pivot_longer(data, cols = starts_with("Year"), 
                          names_to = "Year", values_to = "Value")
long_data
```


---

### Example: Converting Long to Wide

```r
# Convert back to wide format
wide_data <- pivot_wider(long_data, names_from = "Year", values_from = "Value")
wide_data
```


---

## 3. Scaling and Normalization

**Key Concepts**

1. **Scaling** adjusts data to a specific range (e.g., [0, 1]).
2. **Normalization** adjusts data to have a mean of 0 and standard deviation of 1.

**Example: Normalization with `scale()`**

```r
# Sample data
data <- data.frame(
  Values = c(100, 200, 300, 400, 500)
)

# Normalize values
data$Normalized <- scale(data$Values)
data
```


---

**Example: Min-Max Scaling**

```r
# Min-Max Scaling
data$MinMaxScaled <- (data$Values - min(data$Values)) / 
                     (max(data$Values) - min(data$Values))
data
```

---

## Task: Data Transformation


1. Create a dataset with at least two numerical columns.
2. Perform feature engineering to add a ratio column.
3. Reshape the dataset from wide to long format.
4. Normalize one of the numerical columns.

**Example Code**

```r
# Sample data
data <- data.frame(
  Product = c("A", "B", "C"),
  Sales2020 = c(100, 200, 300),
  Sales2021 = c(110, 220, 330)
)

# Feature engineering
data$GrowthRate <- (data$Sales2021 - data$Sales2020) / data$Sales2020

# Reshape to long format
long_data <- pivot_longer(data, cols = starts_with("Sales"), 
                          names_to = "Year", values_to = "Sales")

# Normalize Sales
long_data$NormalizedSales <- scale(long_data$Sales)
long_data
```
---

## Quick Exercise


For the dataset below:

1. Create a new variable for the profit margin.
2. Reshape the dataset to long format.
3. Apply Min-Max scaling to the `Sales` column.

**Dataset**

```r
data <- data.frame(
  Product = c("X", "Y", "Z"),
  Sales2020 = c(100, 150, 200),
  Sales2021 = c(120, 170, 220)
)
```

---

### Solution

```r
# Create profit margin
data$ProfitMargin <- (data$Sales2021 - data$Sales2020) / data$Sales2020

# Reshape to long format
long_data <- pivot_longer(data, cols = starts_with("Sales"), 
                          names_to = "Year", values_to = "Sales")

# Apply Min-Max scaling
long_data$MinMaxScaled <- (long_data$Sales - min(long_data$Sales)) / 
                          (max(long_data$Sales) - min(long_data$Sales))
long_data
```


---

# Encoding Categorical Variables

> 🔧 Learn how to encode categorical data for use in machine learning models using R.

---

**Overview**


Machine learning algorithms often require numerical inputs. This presentation covers:

1. Common methods for encoding categorical variables.
2. Practical examples of one-hot and label encoding in R.


What you'll learn:

* How to prepare categorical variables for analysis.
* Practical encoding techniques with R.

---

## 1. Why Encode Categorical Variables?

**Key Concepts**

1. Categorical variables are non-numeric attributes (e.g., color, category).  
2. Machine learning models require numerical inputs.  
3. Common encoding methods:
-**One-Hot Encoding**: Creates binary columns for each category.
-**Label Encoding**: Assigns a numeric label to each category.

---

## 2. One-Hot Encoding

**Example: One-Hot Encoding with `caret`**

```r
library(caret)

# Sample data
data <- data.frame(
  Category = c("A", "B", "A", "C", "B")
)

# Apply one-hot encoding
dummy_vars <- dummyVars(~ Category, data = data)
encoded_data <- predict(dummy_vars, newdata = data)
encoded_data
```


---

**Example: One-Hot Encoding with `model.matrix`**

```r
# Apply one-hot encoding with model.matrix
encoded_data <- model.matrix(~ Category - 1, data = data)
encoded_data
```


---

## 3. Label Encoding

**Example: Label Encoding with `as.numeric`**

```r
# Sample data
data <- data.frame(
  Category = c("A", "B", "A", "C", "B")
)

# Convert to factor and then to numeric
data$LabelEncoded <- as.numeric(as.factor(data$Category))
data
```


---

## 4. Practical Considerations

### Key Points


1. **When to Use One-Hot Encoding**:
- Suitable for categorical variables with no ordinal relationship.
- Ideal for algorithms that can handle sparse data (e.g., tree-based methods).

2. **When to Use Label Encoding**:
- Suitable for ordinal data with a meaningful order (e.g., "low", "medium", "high").
- Avoid for non-ordinal data as it may introduce unintended relationships.

---

## Task: Encoding Categorical Variables


1. Create a dataset with a categorical variable.  
2. Apply both one-hot and label encoding.  
3. Compare the results.

**Example Code**

```r
# Sample data
data <- data.frame(
  Product = c("X", "Y", "X", "Z", "Y")
)

# One-hot encoding
dummy_vars <- dummyVars(~ Product, data = data)
one_hot <- predict(dummy_vars, newdata = data)

# Label encoding
data$LabelEncoded <- as.numeric(as.factor(data$Product))

# Results
list(OneHot = one_hot, LabelEncoded = data)
```


---

## Quick Exercise


For the dataset below:

1. Apply one-hot encoding to the `Color` column.
2. Apply label encoding to the `Size` column.

**Dataset**

```r
data <- data.frame(
  Color = c("Red", "Blue", "Green", "Blue", "Red"),
  Size = c("Small", "Medium", "Large", "Medium", "Small")
)
```


---

### Solution

```r
# One-hot encoding for Color
dummy_vars <- dummyVars(~ Color, data = data)
one_hot <- predict(dummy_vars, newdata = data)

# Label encoding for Size
data$LabelEncodedSize <- as.numeric(as.factor(data$Size))

# Results
list(OneHotColor = one_hot, LabelEncodedSize = data$LabelEncodedSize)
```


---

# Combining Datasets

> 🔗 Learn how to merge and join datasets effectively using R.

---

**Overview**


Combining datasets is a common task in data analysis. This presentation covers:

1. Methods to merge datasets based on keys.
2. Handling mismatched or missing keys during joins.


What you'll learn:

* How to use `dplyr` to merge datasets.
* Practical strategies for combining data with mismatched keys.

---

## 1. Why Combine Datasets?

**Key Concepts**

1. Datasets often need to be combined for comprehensive analysis.
2. Common scenarios include:
- Adding new variables from another dataset.
- Appending rows from a similar dataset.
- Aggregating data from multiple sources.

---

## 2. Merging Datasets by Key

**Example: Using `dplyr::left_join`**

```r
library(dplyr)

# Sample data
data1 <- data.frame(
  ID = 1:5,
  Value1 = c(10, 20, 30, 40, 50)
)

data2 <- data.frame(
  ID = c(3, 4, 5, 6, 7),
  Value2 = c(300, 400, 500, 600, 700)
)

# Perform a left join
merged_data <- left_join(data1, data2, by = "ID")
merged_data
```


---

**Example: Other Join Types**

```r
# Inner join: Keeps matching rows
inner_data <- inner_join(data1, data2, by = "ID")

# Full join: Includes all rows from both datasets
full_data <- full_join(data1, data2, by = "ID")

# Anti join: Keeps rows in data1 not in data2
anti_data <- anti_join(data1, data2, by = "ID")

list(InnerJoin = inner_data, FullJoin = full_data, AntiJoin = anti_data)
```


---

## 3. Appending Rows

**Example: Using `bind_rows`**

```r
# Sample data
data1 <- data.frame(
  ID = 1:3,
  Value = c(10, 20, 30)
)

data2 <- data.frame(
  ID = 4:5,
  Value = c(40, 50)
)

# Append rows
appended_data <- bind_rows(data1, data2)
appended_data
```


---

## 4. Handling Missing Keys

**Key Concepts**


1. Missing keys can cause `NA` values in merged datasets.
2. Strategies to handle missing keys:
-Replace `NA` with a default value.
-Remove rows with missing values.

**Example: Replacing Missing Keys**

```r
# Replace missing values with 0
merged_data[is.na(merged_data)] <- 0
merged_data
```


---

## Task: Combining Datasets


1. Create two datasets with a common key.
2. Perform a left join, an inner join, and a full join.
3. Handle missing keys in the resulting datasets.

**Example Code**

```r
# Sample data
data1 <- data.frame(
  ID = 1:4,
  Sales = c(100, 200, 300, 400)
)

data2 <- data.frame(
  ID = 3:6,
  Region = c("East", "West", "North", "South")
)

# Perform joins
left <- left_join(data1, data2, by = "ID")
inner <- inner_join(data1, data2, by = "ID")
full <- full_join(data1, data2, by = "ID")

# Handle missing values
full[is.na(full)] <- "Unknown"
list(LeftJoin = left, InnerJoin = inner, FullJoin = full)
```


---

## Quick Exercise


For the datasets below:

1. Perform an inner join on the `CustomerID` column.
2. Append the rows of both datasets.
3. Replace missing values in the joined dataset with `"N/A"`.

### Datasets

```r
# Dataset 1
customers <- data.frame(
  CustomerID = 1:3,
  Name = c("Alice", "Bob", "Charlie")
)

# Dataset 2
orders <- data.frame(
  CustomerID = c(2, 3, 4),
  OrderAmount = c(250, 300, 150)
)
```


---

### Solution

```r
# Perform inner join
inner <- inner_join(customers, orders, by = "CustomerID")

# Append rows
appended <- bind_rows(customers, orders)

# Replace missing values with "N/A"
inner[is.na(inner)] <- "N/A"

list(InnerJoin = inner, AppendedRows = appended)
```


---

# Combining Datasets

> 🔗 Learn how to merge and join datasets in R for comprehensive analysis.

---

**Overview**

Combining datasets is essential when data is stored in multiple tables. This presentation covers:

1. Common types of dataset joins (inner, left, right, full).
2. Merging datasets with R.


What you'll learn:

* How to perform joins and merges in R.
* Practical examples for handling mismatched keys.

---

## 1. Types of Joins

**Key Concepts**


1. **Inner Join**: Returns rows with matching keys in both datasets.
2. **Left Join**: Returns all rows from the left dataset, with matching rows from the right.
3. **Right Join**: Returns all rows from the right dataset, with matching rows from the left.
4. **Full Join**: Returns all rows from both datasets, matching where possible.

---

## 2. Joining Datasets in R

**Example: Inner Join**

```r
library(dplyr)

# Sample datasets
data1 <- data.frame(
  ID = c(1, 2, 3),
  Value1 = c(10, 20, 30)
)

data2 <- data.frame(
  ID = c(2, 3, 4),
  Value2 = c(200, 300, 400)
)

# Perform inner join
inner_joined <- inner_join(data1, data2, by = "ID")
inner_joined
```


---

**Example: Left Join**

```r
# Perform left join
left_joined <- left_join(data1, data2, by = "ID")
left_joined
```


---

**Example: Full Join**

```r
# Perform full join
full_joined <- full_join(data1, data2, by = "ID")
full_joined
```


---

## 3. Practical Considerations

### Key Points


1. Ensure datasets have a common key for merging.  
2. Handle mismatched keys by cleaning and standardizing key columns before joining.  
3. Use `bind_rows()` or `bind_cols()` for row-wise or column-wise combination without keys.

---

**Example: Handling Mismatched Keys**

```r
# Sample data with mismatched keys
data1 <- data.frame(
  ID = c(1, 2, "three"),
  Value1 = c(10, 20, 30)
)

data2 <- data.frame(
  ID = c(2, 3, 4),
  Value2 = c(200, 300, 400)
)

# Standardize keys
data1$ID <- as.numeric(data1$ID)

# Perform inner join
inner_joined <- inner_join(data1, data2, by = "ID")
inner_joined
```


---

## Task: Combining Datasets


1. Create two datasets with overlapping keys.
2. Perform an inner, left, and full join.
3. Handle mismatched keys by standardizing column formats.

**Example Code**

```r
# Sample datasets
data1 <- data.frame(
  ID = c(1, 2, 3),
  Sales = c(100, 200, 300)
)

data2 <- data.frame(
  ID = c(2, 3, 4),
  Profit = c(50, 75, 100)
)

# Perform joins
inner <- inner_join(data1, data2, by = "ID")
left <- left_join(data1, data2, by = "ID")
full <- full_join(data1, data2, by = "ID")

# Results
list(Inner = inner, Left = left, Full = full)
```


---

## Quick Exercise


For the datasets below:

1. Perform a left join to include all rows from `data1`.
2. Handle mismatched keys by converting `ID` to numeric.

### Datasets

```r
data1 <- data.frame(
  ID = c(1, "two", 3),
  Sales = c(100, 200, 300)
)

data2 <- data.frame(
  ID = c(2, 3, 4),
  Profit = c(50, 75, 100)
)
```


---

### Solution

```r
# Standardize keys
data1$ID <- as.numeric(data1$ID)

# Perform left join
left_joined <- left_join(data1, data2, by = "ID")
left_joined
```


---

# Validating Prepared Data

> ✅ Ensure the quality and consistency of your cleaned dataset using R.

---

**Overview**

Validation is the final step in data preparation. This presentation covers:

1. Checking data consistency and accuracy.
2. Comparing summary statistics before and after cleaning.


What you'll learn:

* How to validate dataset integrity.
* Methods to identify and resolve inconsistencies.

---

## 1. Why Validate Data?

**Key Concepts**

1. Validation ensures:
- Accurate and reliable analysis.
- Consistency in data formats, units, and relationships.
2. Common checks include:
- Summary statistics comparison.
- Validation of relationships between variables.

---

## 2. Validating Consistency

**Example: Checking Units**

```r
# Sample data
data <- data.frame(
  ID = 1:5,
  PriceUSD = c(100, 200, 300, 400, 500),
  PriceEUR = c(90, 180, 270, 360, 450)
)

# Validate unit conversion
conversion_rate <- 0.9
expected_eur <- data$PriceUSD * conversion_rate

# Compare actual and expected values
all.equal(data$PriceEUR, expected_eur)
```


---

## 3. Validating Summary Statistics

**Example: Comparing Before and After Cleaning**

```r
# Sample data before cleaning
raw_data <- data.frame(
  ID = 1:5,
  Value = c(10, 20, NA, 40, 50)
)

# After cleaning (impute missing values)
cleaned_data <- raw_data
cleaned_data$Value[is.na(cleaned_data$Value)] <- mean(cleaned_data$Value, na.rm = TRUE)

# Compare summary statistics
raw_summary <- summary(raw_data$Value)
cleaned_summary <- summary(cleaned_data$Value)

list(Raw = raw_summary, Cleaned = cleaned_summary)
```


---

## 4. Validating Relationships

**Example: Logical Relationships**

```r
# Sample data
data <- data.frame(
  Product = c("A", "B", "C"),
  Sales = c(100, 200, 300),
  Profit = c(50, 100, 150)
)

# Validate relationship: Profit <= Sales
valid_relationship <- all(data$Profit <= data$Sales)
valid_relationship
```


---

## Task: Validating Your Dataset


1. Create a dataset with multiple variables.
2. Validate relationships between columns (e.g., one column is always less than or equal to another).
3. Compare summary statistics before and after cleaning.

**Example Code**

```r
# Sample data
data <- data.frame(
  ID = 1:5,
  Sales = c(100, 200, NA, 400, 500),
  Profit = c(50, 100, 150, 200, NA)
)

# Cleaning
data$Sales[is.na(data$Sales)] <- mean(data$Sales, na.rm = TRUE)
data$Profit[is.na(data$Profit)] <- median(data$Profit, na.rm = TRUE)

# Validate relationships
valid_relationship <- all(data$Profit <= data$Sales)

# Compare statistics
cleaned_summary <- summary(data)
list(ValidRelationship = valid_relationship, CleanedSummary = cleaned_summary)
```


---

## Quick Exercise


For the dataset below:

1. Validate that `Cost` is always less than or equal to `Revenue`.
2. Compare summary statistics before and after cleaning missing values.

**Dataset**

```r
data <- data.frame(
  Product = c("X", "Y", "Z"),
  Revenue = c(500, NA, 700),
  Cost = c(300, 200, NA)
)
```


---

### Solution

```r
# Validate relationships
valid_relationship <- all(data$Cost <= data$Revenue, na.rm = TRUE)

# Impute missing values
data$Revenue[is.na(data$Revenue)] <- mean(data$Revenue, na.rm = TRUE)
data$Cost[is.na(data$Cost)] <- mean(data$Cost, na.rm = TRUE)

# Compare statistics
cleaned_summary <- summary(data)
list(ValidRelationship = valid_relationship, CleanedSummary = cleaned_summary)
```

