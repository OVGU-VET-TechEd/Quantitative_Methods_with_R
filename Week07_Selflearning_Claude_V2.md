<!--
author:  Hannes Tegelbeckers
email:   hannes.tegelbeckers@ovgu.de
version: 2.0.0
language: en
narrator: US English Female

comment:  Group Comparison in Quantitative Research Using R (T-test)

logo:   media/logo.jpg

script: https://cdnjs.cloudflare.com/ajax/libs/PapaParse/5.4.1/papaparse.min.js

import: https://raw.githubusercontent.com/liaTemplates/ABCjs/main/README.md

@CSV
<script run-once style="display:block" modify="false">
async function csvToMarkdownTable(csvFile) {
  const response = await fetch(csvFile);
  const text = await response.text();
  const rows = Papa.parse(text).data;
  let markdownTable = "| " + rows[0].join(" | ") + " |\n"; // Header
  markdownTable += "| " + rows[0].map(() => "---").join(" | ") + " |\n"; // Separator
  for (let i = 1; i < rows.length; i++) {
    if (rows[i].length === rows[0].length) {
      markdownTable += "| " + rows[i].join(" | ") + " |\n";
    }
  }
  send.lia("LIASCRIPT: <!-- data-type='none' --" + ">" + markdownTable);
}
csvToMarkdownTable("@0")
"LIA: wait"
</script>
@end

@WebSerial
<script>
(async function() {
  // Check if the Web Serial API is supported.
  if (!("serial" in navigator)) {
    console.error("Web Serial API is not supported in this browser.");
    return;
  }

  // Declare connection-related variables for later cleanup.
  let port = null;
  let reader = null;

  try {
    // Request and open the serial port.
    port = await navigator.serial.requestPort();
    await port.open({ baudRate: 115200 });

    // Create a TextEncoder instance.
    const encoder = new TextEncoder();
    // Function to stop any currently running code by sending Ctrl-C.
    async function stopCurrentProgram() {
      try {
        const writer = port.writable.getWriter();
        // Send Ctrl-C (ASCII 0x03) to interrupt any running code.
        await writer.write(encoder.encode("\x03"));
        // Wait briefly to allow the interrupt to be processed.
        await new Promise(resolve => setTimeout(resolve, 100));
        // Send a second Ctrl-C in case the first one was missed.
        await writer.write(encoder.encode("\x03"));
        writer.releaseLock();
      } catch (e) {
        console.error("Error sending Ctrl-C:", e);
      }
    }

    // Stop any running code before sending new code.
    await stopCurrentProgram();

    // Retrieve the entire Python code from the liascript input.
    const pythonCode = `@input(0)`;

    // Function to send code using MicroPython's paste mode.
    // In paste mode, the REPL buffers all lines until Ctrl‑D is received,
    // then it compiles and executes the entire code block at once.
    async function sendCodeInPasteMode(code) {
      const writer = port.writable.getWriter();
      // Enter paste mode (Ctrl‑E, ASCII 0x05).
      await writer.write(encoder.encode("\x05"));
      // Wait briefly for paste mode to be activated.
      await new Promise(resolve => setTimeout(resolve, 100));

      // Split the code into lines, preserving all indentation.
      const codeLines = code.split(/\r?\n/);
      for (const line of codeLines) {
        // Send each line exactly as-is, with CR+LF.
        await writer.write(encoder.encode(line + "\r\n"));
      }
      // Exit paste mode by sending Ctrl‑D (ASCII 0x04).
      await writer.write(encoder.encode("\x04"));
      writer.releaseLock();
      send.lia("LIA: terminal");
    }

    // Function that sends the code and reads output until the REPL prompt (">>>") is detected.
    // This ensures the entire block is executed before further input is allowed.
    async function sendCodeAndWaitForPrompt(code) {
      await sendCodeInPasteMode(code);
      let outputBuffer = "";
      const tempReader = port.readable.getReader();
      const decoder = new TextDecoder();
      let promptFound = false;

      while (!promptFound) {
        const { value, done } = await tempReader.read();
        if (done) break;
        if (value) {
          const text = decoder.decode(value);
          outputBuffer += text;
          console.stream(text);
          // Look for the REPL prompt (adjust if your prompt differs).
          if (outputBuffer.includes(">>>")) {
            promptFound = true;
          }
        }
      }
      await tempReader.releaseLock();
      return outputBuffer;
    }

    // Send the Python code and wait until the prompt is detected.
    await sendCodeAndWaitForPrompt(pythonCode);
    console.log("Python code executed and prompt detected.");

    // Now that execution is complete, enable terminal input.
    send.lia("LIA: terminal");

    // Start a global read loop to capture and display subsequent output.
    reader = port.readable.getReader();
    const globalDecoder = new TextDecoder();
    (async function readLoop() {
      try {
        while (true) {
          const { value, done } = await reader.read();
          if (done) {
            console.debug("Stream closed");
            send.lia("LIA: stop");
            break;
          }
          if (value) {
            console.stream(globalDecoder.decode(value));
          }
        }
      } catch (error) {
        console.error("Read error:", error);
      } finally {
        try { reader.releaseLock(); } catch (e) { /* ignore */ }
      }
    })();

    // Handler to send terminal input lines to MicroPython.
    send.handle("input", input => {
      (async function() {
        try {
          const writer = port.writable.getWriter();
          // Send the terminal input (preserving any whitespace) with CR+LF.
          await writer.write(encoder.encode(input + "\r\n"));
          writer.releaseLock();
        } catch (e) {
          console.error("Error sending input to MicroPython:", e);
        }
      })();
    });

    // Handler to clean up all connections and variables when a "stop" command is received.
    send.handle("stop", async () => {
      console.log("Cleaning up connections and stopping execution.");

      // Cancel the reader if it exists.
      if (reader) {
        try {
          await reader.cancel();
        } catch (e) {
          console.error("Error canceling reader:", e);
        }
        try { reader.releaseLock(); } catch (e) { /* ignore */ }
      }

      // Close the serial port if it's open.
      if (port) {
        try {
          await port.close();
        } catch (e) {
          console.error("Error closing port:", e);
        }
      }

      // Reset connection variables.
      port = null;
      reader = null;
      console.log("Cleanup complete.");
    });

  } catch (error) {
    console.error("Error connecting to the MicroPython device:", error);
    send.lia("LIA: stop");
  }
})();

"LIA: wait"
</script>
@end

@WebSerial2
<script>
(async function() {
  // Check if the Web Serial API is supported.
  if (!("serial" in navigator)) {
    console.error("Web Serial API is not supported in this browser.");
    return;
  }

  // Declare connection-related variables for later cleanup.
  let port = null;
  let reader = null;

  try {
    // First, check if a port was previously granted.
    const ports = await navigator.serial.getPorts();
    if (ports.length > 0) {
      port = ports[0];
      console.log("Reusing previously granted port.");
    } else {
      // If no port is available, request a new one.
      port = await navigator.serial.requestPort();
    }

    // Open the port at the typical MicroPython baud rate.
    await port.open({ baudRate: 115200 });

    // Create a TextEncoder instance.
    const encoder = new TextEncoder();

    // Function to stop any currently running code by sending Ctrl-C.
    async function stopCurrentProgram() {
      try {
        const writer = port.writable.getWriter();
        // Send Ctrl-C (ASCII 0x03) to interrupt any running code.
        await writer.write(encoder.encode("\x03"));
        // Wait briefly to allow the interrupt to be processed.
        await new Promise(resolve => setTimeout(resolve, 100));
        // Send a second Ctrl-C in case the first was missed.
        await writer.write(encoder.encode("\x03"));
        writer.releaseLock();
      } catch (e) {
        console.error("Error sending Ctrl-C:", e);
      }
    }

    // Stop any running code before sending new code.
    await stopCurrentProgram();

    // Retrieve the entire Python code from the liascript input.
    const pythonCode = `@input(0)`;

    // Function to send code using MicroPython's paste mode.
    // In paste mode, the REPL buffers all lines until Ctrl‑D is received,
    // then it compiles and executes the entire code block at once.
    async function sendCodeInPasteMode(code) {
      const writer = port.writable.getWriter();
      // Enter paste mode (Ctrl‑E, ASCII 0x05).
      await writer.write(encoder.encode("\x05"));
      // Wait briefly for paste mode to be activated.
      await new Promise(resolve => setTimeout(resolve, 100));
      
      // Split the code into lines, preserving all indentation.
      const codeLines = code.split(/\r?\n/);
      for (const line of codeLines) {
        // Send each line exactly as-is, with CR+LF.
        await writer.write(encoder.encode(line + "\r\n"));
      }
      // Exit paste mode by sending Ctrl‑D (ASCII 0x04).
      await writer.write(encoder.encode("\x04"));
      writer.releaseLock();
      send.lia("LIA: terminal");
    }

    // Function that sends the code and reads output until the REPL prompt (">>>") is detected.
    // This ensures the entire block is executed before further input is allowed.
    async function sendCodeAndWaitForPrompt(code) {
      await sendCodeInPasteMode(code);
      let outputBuffer = "";
      const tempReader = port.readable.getReader();
      const decoder = new TextDecoder();
      let promptFound = false;
      
      while (!promptFound) {
        const { value, done } = await tempReader.read();
        if (done) break;
        if (value) {
          const text = decoder.decode(value);
          outputBuffer += text;
          console.stream(text);
          // Look for the REPL prompt (adjust if your prompt differs).
          if (outputBuffer.includes(">>>")) {
            promptFound = true;
          }
        }
      }
      await tempReader.releaseLock();
      return outputBuffer;
    }

    // Send the Python code and wait until the prompt is detected.
    await sendCodeAndWaitForPrompt(pythonCode);
    console.log("Python code executed and prompt detected.");

    // Now that execution is complete, enable terminal input.
    send.lia("LIA: terminal");

    // Start a global read loop to capture and display subsequent output.
    reader = port.readable.getReader();
    const globalDecoder = new TextDecoder();
    (async function readLoop() {
      try {
        while (true) {
          const { value, done } = await reader.read();
          if (done) {
            console.debug("Stream closed");
            send.lia("LIA: stop");
            break;
          }
          if (value) {
            console.stream(globalDecoder.decode(value));
          }
        }
      } catch (error) {
        console.error("Read error:", error);
      } finally {
        try { reader.releaseLock(); } catch (e) { /* ignore */ }
      }
    })();

    // Handler to send terminal input lines to MicroPython.
    send.handle("input", input => {
      (async function() {
        try {
          const writer = port.writable.getWriter();
          // Send the terminal input (preserving any whitespace) with CR+LF.
          await writer.write(encoder.encode(input + "\r\n"));
          writer.releaseLock();
        } catch (e) {
          console.error("Error sending input to MicroPython:", e);
        }
      })();
    });

    // Handler to clean up all connections and variables when a "stop" command is received.
    send.handle("stop", async () => {
      console.log("Cleaning up connections and stopping execution.");

      // Cancel the reader if it exists.
      if (reader) {
        try {
          await reader.cancel();
        } catch (e) {
          console.error("Error canceling reader:", e);
        }
        try { reader.releaseLock(); } catch (e) { /* ignore */ }
      }

      // Close the serial port if it's open.
      if (port) {
        try {
          await port.close();
        } catch (e) {
          console.error("Error closing port:", e);
        }
      }

      // Reset connection variables.
      port = null;
      reader = null;
      console.log("Cleanup complete.");
    });

  } catch (error) {
    console.error("Error connecting to the MicroPython device:", error);
    send.lia("LIA: stop");
  }
})();

"LIA: wait"
</script>
@end

@style
@keyframes burn {
  0% {
    text-shadow: 0 0 5px #ff0, 0 0 10px #ff0, 0 0 15px #f00, 0 0 20px #f00,
      0 0 25px #f00, 0 0 30px #f00, 0 0 35px #f00;
  }
  50% {
    text-shadow: 0 0 10px #ff0, 0 0 15px #ff0, 0 0 20px #ff0, 0 0 25px #f00,
      0 0 30px #f00, 0 0 35px #f00, 0 0 40px #f00;
  }
  100% {
    text-shadow: 0 0 5px #ff0, 0 0 10px #ff0, 0 0 15px #f00, 0 0 20px #f00,
      0 0 25px #f00, 0 0 30px #f00, 0 0 35px #f00;
  }
}

.burning-text {
  font-weight: bold;
  color: #fff;
  animation: burn 1.5s infinite alternate;
}
@end

@burn: <span class="burning-text">@0</span>

-->


# Group Comparison in Quantitative Research Using R (T-test)

<span style="font-size: 22px;">**Teaching Team**</span>

> **Responsible Teacher:** M.A. Hannes Tegelbeckers
> **Tutors:** A E M Sabbir Rifat, Masub Makhdoom, Mahwish Kanwal

# Objectives of the Lecture

* Understand different group comparison techniques
* Learn when to apply each method
* Apply methods using R with real environmental data
* Interpret results meaningfully

# Group Comparison

Group comparison in quantitative research refers to the statistical methods used to compare differences between groups in terms of their central tendencies (e.g., means, medians), variability, or other statistical properties. It helps researchers determine whether the observed differences between groups are statistically significant or likely due to random chance.

## Key Measures of Variability

Before comparing groups, it is useful to recall some fundamental measures of variability in data:

* **Range:** The difference between the minimum and maximum values in the dataset. It gives the span of the data.
* **Variance:** The average of the squared deviations from the mean. This measures how spread out the data points are around the mean.
* **Standard Deviation:** The square root of the variance. It is expressed in the same units as the data and represents the typical distance of data points from the mean.
* **Interquartile Range (IQR):** The range of the middle 50% of the data (between the first and third quartiles). It reflects the spread of the central half of the dataset and is less influenced by outliers than the full range.

## Self assessment (Key Measures of Variability)

**1. What is the primary advantage of using the range as a measure of variability?**

* \[( )] A. It is robust to outliers.
* \[( )] B. It considers all data points.
* \[(X)] C. It is simple to calculate and understand.
* \[( )] D. It provides information about the data's distribution shape.

---

**2. What does variance fundamentally measure?**

* \[(X)] A. The spread of data points around the mean.
* \[( )] B. The average distance between data points.
* \[( )] C. The difference between the highest and lowest values.
* \[( )] D. The central tendency of the data.

---

**3. How is standard deviation related to variance?**

* \[( )] A. Standard deviation is the square of the variance.
* \[(X)] B. Standard deviation is the square root of the variance.
* \[( )] C. Standard deviation is half of the variance.
* \[( )] D. Standard deviation is the reciprocal of the variance.

---

**4. The Interquartile Range (IQR) measures the spread of the:**

* \[( )] A. Entire dataset.
* \[(X)] B. Middle 50% of the data.
* \[( )] C. Top 25% of the data.
* \[( )] D. Bottom 25% of the data.

## Assumptions in Group Comparisons

Before conducting t-tests (or other parametric group comparison techniques), certain assumptions should be checked to ensure the results are valid. The key assumptions include **normality** of the data, **homogeneity of variance** between groups, **independence of observations**, and an adequately large **sample size**. Below, we explain each assumption in detail and how to check it:

### 1. Normality test

For parametric tests, we assume that the data (in each group) are approximately normally distributed (bell-shaped). This assumption is especially important for small sample sizes. In an independent two-sample t-test, each of the two groups should be roughly normal. In a paired t-test, the **differences** between paired observations should be approximately normal.

**How to check:** Visually inspect the distribution using histograms, Q-Q plots, or density plots to see if it roughly follows a bell curve without severe skew or outliers. You can also perform a formal normality test such as the **Shapiro-Wilk test** or **Kolmogorov-Smirnov test**. For example, in R you can use `shapiro.test(your_data)` to test normality. If the normality test yields a high p-value (e.g., p > 0.05), it suggests the data do not significantly deviate from normality. A low p-value (p < 0.05) indicates the data **likely are not normally distributed** (normality assumption is violated).

### 2. Homogeneity of Variance

Homogeneity of variance means that the variances (spread) of the populations from which different groups are drawn are equal. This assumption mainly applies to **independent two-sample t-tests** (it is not relevant for one-sample tests, and in paired tests we look at differences within individuals). If the variances are unequal between two groups, the standard (pooled) t-test may not be valid (although an alternative Welch's t-test can accommodate unequal variances).

**How to check:** You can compare group spreads using visual tools like boxplots. Statistically, tests like **Levene's test** and **Bartlett's test** can be used to check for equal variances. Levene's test is robust to non-normal data; Bartlett's test (used in R via `bartlett.test()`) is more sensitive and assumes normality. For example, in R you might use `bartlett.test(values ~ group, data=myData)` to test if two groups have equal variance. A non-significant result (p > 0.05) from such a test suggests variances are roughly equal (assumption holds), whereas a significant result (p < 0.05) indicates a **violation of the homogeneity of variance** assumption. (By default, R's `t.test` uses Welch's t-test which does not assume equal variances; you can explicitly set `var.equal=TRUE` to use the equal-variance t-test when appropriate.)

### 3. Independence of Observations

Independence means that each observation is not influenced by or paired with any other (except in the case of paired designs, which are handled separately). For an **independent samples t-test**, this implies the two groups contain different individuals with no overlap – one person's measurement cannot appear in both groups. Generally, independence is ensured by the study design: using random sampling or random assignment and not measuring the same subject twice (in the same comparison). There is no direct statistical test for independence; it must be ensured by proper data collection. If observations are not independent (for example, if the same individuals appear in both groups or if data points are repeated measures without accounting for it), the t-test results will be invalid. Always design experiments so that observations meet this assumption, or use paired/repeated-measures methods when observations are related.

### 4. Sample Size Considerations

While not a strict assumption, sample size affects the reliability and power of a t-test. **Larger sample sizes** make the t-test more robust to violations of normality (by virtue of the Central Limit Theorem) and provide greater power to detect true differences. **Small sample sizes** (especially less than \~30 per group) make it harder to assess normality (tests have low power) and can result in a lack of power to detect differences even if they exist. It's important to ensure you have an adequate sample size for your expected effect. You can perform a **power analysis** (using tools or functions like `power.t.test()` in R) to estimate how many observations you need for a given effect size and significance level. In short, ensure each group's sample size is large enough to provide confidence in the results – if the sample is very small, interpret results with caution (and consider that the t-test may fail to detect differences or the normality assumption is hard to verify).

**Video:** The following video provides an overview of these assumptions for t-tests and how to check them in practice.

!?[Video: T-test assumptions explained.](https://www.youtube.com/watch?v=NgZnqm0erhg)

## Self assessment (Assumptions)

**1. Which of the following are common assumptions for parametric t-tests? (Select all that apply.)**

* \[\[X]] Data are approximately normally distributed.
* \[\[X]] Observations are independent of each other.
* \[\[X]] The variances of the groups are equal (for a two-sample t-test).
* \[\[ ]] Each group must have the same sample size.
* \[\[ ]] The dependent variable can be categorical.

## Key Elements in Test Output (Interpreting Results)

When you run a t-test, the output will include several key statistics. Understanding these elements will help you interpret the results:

* **Test Statistic** – A numerical value that summarizes the difference between groups relative to variation. (In a t-test, this is the calculated *t* value.)
* **Degrees of Freedom (df)** – The number of independent pieces of information used to calculate the test statistic. (For example, in a one-sample t-test with *n* observations, df = *n* – 1.)
* **p-value** – The probability of obtaining results at least as extreme as the observed results, assuming the null hypothesis is true. A small p-value indicates that such extreme results would be unlikely if there were no true difference.
* **Significance Level** (usually α = 0.05) – The threshold for determining statistical significance. If p-value < α, you reject the null hypothesis; if p-value ≥ α, you do not reject the null.
* **Rejecting or Failing to Reject the Null Hypothesis** – If p-value < α, reject H₀ (there is evidence of a significant difference); otherwise, fail to reject H₀. (Note: "Failing to reject" H₀ is **not** the same as proving H₀ true—it simply means there isn't enough evidence to show a difference.)
* **Effect Size** – A measure of the magnitude of the difference between groups, independent of sample size (e.g., Cohen's d for the difference between two means). An effect size helps you understand if a statistically significant difference is small or large in practical terms.

*Important:* A result can be statistically significant (low p-value) even if the effect size is very small (especially with large samples), and a non-significant result could still have a moderate effect size (if sample size is small). Always consider both p-value and effect size when interpreting results.

# Overview of Group Comparison Methods

## Types of Comparison Methods

<div style="text-align: center;">
  <img src="https://raw.githubusercontent.com/AEMSabbirRifat/QuRM-Sabbir-/97cf7d5d07315d817559561f306ead3e6dcfa1a7/media/Group%20Comparison%20in%20Quantitative%20Research%20Using%20R%20(T-test)/Picture1.jpg" alt="Overview of group comparison methods diagram" width="50%">
</div>

# Parametric Test

\--{{0}}--
!?[Video: Introduction to parametric tests.](https://github.com/OVGU-VET-TechEd/Quantitative_Methods_with_R/blob/1d188ad461793879e91d090c21260f1f89d38782/Media_week7/Parametric%20Test.mp4?raw=true)

<details>
  <summary>👉 Let's see what Aditi is talking about</summary>

Hi, I am **Aditi**. I will help you to understand the concept of **parametric test**.

Parametric test is a statistical procedure that relies on assumptions regarding the distributional form of the underlying population — most commonly, that the population data follow a **normal distribution**.

These tests estimate population parameters (e.g., means, variances) and are generally applied to **interval** or **ratio-scale** data.

When their assumptions are met, **parametric tests are more statistically powerful** than their non-parametric counterparts, enabling more precise inference about population parameters.

</details>

## Parametric Tests´ Data Set Criteria

* **Normal Distribution**

  * Data should come from a roughly normal distribution (bell-shaped curve).
* **Homogeneity of Variance**

  * Groups should have similar variances (spread of data is similar across groups).
* **Interval or Ratio Scale**

  * The data should be measured on an interval or ratio scale (e.g., height, weight, income).

# T-tests

T-tests are used to **compare means between groups** to determine if differences are **statistically significant** (i.e., unlikely to be due to chance).

**When to Use:**

* The **dependent variable** is **continuous**

  * *(e.g., temperature, ozone levels, wind speed)*
* The **independent variable** is **categorical with up to two levels**

  * *(a binary grouping variable, e.g., summer vs. winter months)*

## Types of T-tests

<div style="text-align: center;">
  <img src="https://raw.githubusercontent.com/AEMSabbirRifat/QuRM-Sabbir-/97cf7d5d07315d817559561f306ead3e6dcfa1a7/media/Group%20Comparison%20in%20Quantitative%20Research%20Using%20R%20(T-test)/Picture2.jpg" alt="Types of t-tests diagram" width="50%">
</div>

| Type                                | Purpose                                                 | Example                                                                                        |
| ----------------------------------- | ------------------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| **One-Sample T-test**               | Compare the mean of one group to a known value.         | Test if average temperature differs from 75°F.                                            |
| **Independent T-test (Two-Sample)** | Compare means of two independent groups.                | Test if temperature differs between May and September.                                   |
| **Paired T-test**                   | Compare means of two related groups (same individuals). | Test if ozone levels are different before and after implementing pollution control measures. |

## Self assessment (Types of T-tests)

**1. You want to determine if the average temperature in New York during the study period differs significantly from the national average temperature of 75°F. Which type of T-test would be most appropriate for this analysis?**

* \[(X)] A. One-Sample T-test
* \[( )] B. Independent T-test
* \[( )] C. Paired T-test
* \[( )] D. ANOVA

---

**2. A researcher wants to compare ozone levels between summer months (June, July, August) and spring months (May). Which type of T-test should be used to compare their mean ozone concentrations?**

* \[( )] A. One-Sample T-test
* \[(X)] B. Independent T-test (Two-Sample T-test)
* \[( )] C. Paired T-test
* \[( )] D. Chi-Square Test

---

**3. An environmental scientist wants to assess if a new air quality monitoring system gives different temperature readings compared to the old system. They measure temperature at the same 20 locations using both systems. Which statistical test should be applied to analyze the difference in temperature readings?**

* \[( )] A. One-Sample T-test
* \[( )] B. Independent T-test
* \[(X)] C. Paired T-test
* \[( )] D. Correlation Analysis

## One-Sample T-test in R

A **one-sample t-test** compares the mean of a single sample to a known or hypothesized population mean. In R, you can perform a one-sample t-test using the `t.test()` function on a numeric vector of data, specifying the hypothesized mean via the `mu` argument.

For example, suppose we want to test if the average temperature in the airquality dataset is different from 75°F:

```r
# Load the airquality dataset
data("airquality")

# Load the data from the URL if needed
# airquality <- read.csv("https://github.com/OVGU-VET-TechEd/Quantitative_Methods_with_R/raw/main/Media_week7/airquality.csv")

# Check the structure of the data
str(airquality)
head(airquality)

# Remove missing values for the analysis
temp_clean <- airquality$Temp[!is.na(airquality$Temp)]

# Perform one-sample t-test (H0: true mean = 75°F)
t.test(temp_clean, mu = 75)

# Calculate descriptive statistics
mean(temp_clean)
sd(temp_clean)
length(temp_clean)
```

This code will output the results of the one-sample t-test, including the *t* statistic, degrees of freedom, and p-value. To interpret the output, compare the p-value to your significance level (α = 0.05 by default):

* If the p-value is **less than 0.05**, you reject the null hypothesis and conclude the sample mean is significantly different from 75°F.
* If the p-value is **greater than or equal to 0.05**, you **fail to reject** the null hypothesis, indicating no statistically significant difference from 75°F.

**Video:** The following video demonstrates how to perform a one-sample t-test in R and interpret the results.

!?[Video: One-sample t-test in R (demo).](https://www.youtube.com/watch?v=xloO6zktZ3g)

## Two-Sample T-test (Independent T-test) in R

An **independent two-sample t-test** compares the means of two separate groups. Using the airquality dataset, we can compare temperature between different months or seasons.

For example, let's compare temperature between May (month 5) and September (month 9):

```r
# Load the airquality dataset
data("airquality")

# Create subsets for May and September
may_temp <- airquality$Temp[airquality$Month == 5]
sep_temp <- airquality$Temp[airquality$Month == 9]

# Remove any missing values
may_temp <- may_temp[!is.na(may_temp)]
sep_temp <- sep_temp[!is.na(sep_temp)]

# Check the data
length(may_temp)  # Sample size for May
length(sep_temp)  # Sample size for September

# Calculate descriptive statistics
mean(may_temp)
mean(sep_temp)
sd(may_temp)
sd(sep_temp)

# Perform independent two-sample t-test (H0: mean_May = mean_Sep)
t.test(may_temp, sep_temp, var.equal = TRUE)

# Alternative: using formula notation
t.test(Temp ~ Month, data = airquality[airquality$Month %in% c(5, 9), ])

# Check assumptions: normality
shapiro.test(may_temp)
shapiro.test(sep_temp)

# Check assumptions: equal variances
var.test(may_temp, sep_temp)
```

This will carry out a two-sample t-test assuming equal variances. The output will indicate whether the difference in means is statistically significant. If the p-value is below 0.05, we reject the null hypothesis and conclude that the temperature means between May and September are significantly different.

**Video:** The following video shows how to conduct an independent two-sample t-test in R and interpret the output.

!?[Video: Independent two-sample t-test in R (demo).](https://www.youtube.com/watch?v=j2RwQQnsbZY)

## Paired T-test (Dependent T-test) in R

A **paired t-test** is used when you have two related observations for each subject. While the airquality dataset doesn't have natural pairs, we can demonstrate this concept by creating a meaningful paired comparison.

For instance, let's compare ozone levels from the first half versus the second half of each month:

```r
# Load the airquality dataset
data("airquality")

# Remove rows with missing Ozone values
airquality_clean <- airquality[!is.na(airquality$Ozone), ]

# Create paired data: early month (days 1-15) vs late month (days 16-31)
early_month <- airquality_clean$Ozone[airquality_clean$Day <= 15]
late_month <- airquality_clean$Ozone[airquality_clean$Day >= 16]

# For proper pairing, we need equal length vectors
# Let's use a different approach: compare consecutive days
consecutive_days <- airquality_clean[1:(nrow(airquality_clean)-1), ]
next_days <- airquality_clean[2:nrow(airquality_clean), ]

# Create pairs of consecutive daily ozone readings
day1_ozone <- consecutive_days$Ozone
day2_ozone <- next_days$Ozone

# Check that we have pairs
length(day1_ozone)
length(day2_ozone)

# Calculate descriptive statistics
mean(day1_ozone)
mean(day2_ozone)
mean(day2_ozone - day1_ozone)  # Mean difference

# Perform paired t-test (H0: mean_day1 = mean_day2)
t.test(day1_ozone, day2_ozone, paired = TRUE)

# Alternative approach: test differences directly
differences <- day2_ozone - day1_ozone
t.test(differences, mu = 0)

# Check assumption: normality of differences
shapiro.test(differences)

# Visualize the differences
hist(differences, main = "Distribution of Daily Ozone Changes", 
     xlab = "Ozone Change (ppb)")
```

This will perform a paired t-test on consecutive daily ozone readings. The output will give a *t* statistic, degrees of freedom, and a p-value. To interpret:

* If p-value < 0.05, reject the null hypothesis and conclude that there is a significant difference between consecutive days' ozone levels.
* If p-value ≥ 0.05, fail to reject the null hypothesis, indicating no statistically significant difference in ozone levels between consecutive days.

**Video:** The video below demonstrates how to perform a paired t-test in R and interpret the results.

!?[Video: Paired t-test in R (demo).](https://www.youtube.com/watch?v=isickY41_TM)

# Self Evaluation

Now it's time for a comprehensive self-evaluation. The questions below will test your understanding of group comparisons and t-tests. Try to answer each one, then check if you got it correct. Keep track of how many you get right for a quick score of your learning progress!

**1. Which of the following tests can be used to check if two groups have equal variances?**

* \[\[X]] Levene's test
* \[\[X]] Bartlett's test
* \[\[ ]] Shapiro-Wilk test
* \[\[ ]] t-test for two means
* \[\[ ]] Kolmogorov-Smirnov test

---

**2. Which of the following is *not* typically part of a t-test output?**

* \[( )] A. p-value
* \[( )] B. Degrees of freedom
* \[( )] C. t-statistic
* \[(X)] D. Correlation coefficient

---

**3. True or False: A very small p-value (e.g., p = 0.001) always implies that the difference between groups is larger in magnitude than a result with p = 0.04.**

* \[( )] True
* \[(X)] False

---

**4. A Shapiro-Wilk normality test on a dataset returns p = 0.002. Which of the following is the correct interpretation?**

* \[(X)] A. The sample likely does **not** come from a normal distribution (normality assumption is violated).
* \[( )] B. The sample is approximately normal; p = 0.002 indicates strong normality.
* \[( )] C. The group means are significantly different.
* \[( )] D. The data are categorical.

---

**5. True or False: In an independent samples t-test, the two groups must consist of completely different individuals (no person is in both groups).**

* \[(X)] True
* \[( )] False

---

**6. Using the airquality dataset, if you wanted to test whether wind speed differs between months with high ozone (>50 ppb) versus low ozone (≤50 ppb), which type of t-test would be most appropriate?**

* \[( )] A. One-sample t-test
* \[(X)] B. Independent two-sample t-test
* \[( )] C. Paired t-test
* \[( )] D. None of the above

# Practical Exercise

**Formulate and test a hypothesis using the airquality dataset with either an Independent T-test or Dependent T-test.**

## Exercise Instructions:

1. **Load the airquality dataset** from the provided URL:
   ```r
   # Load data
   airquality <- read.csv("https://github.com/OVGU-VET-TechEd/Quantitative_Methods_with_R/raw/main/Media_week7/airquality.csv")
   ```

2. **Explore the dataset:**
   ```r
   # Basic exploration
   str(airquality)
   summary(airquality)
   head(airquality)
   ```

3. **Choose one of these research questions** or formulate your own:

   **Option A (Independent t-test):** 
   - Do ozone levels differ between early summer (May-June) and late summer (August-September)?
   - Is there a difference in wind speed between months with high vs. low solar radiation?
   
   **Option B (Paired t-test):**
   - Are there systematic differences in temperature between consecutive days?
   - Do ozone levels show patterns when comparing the first and second half of each month?

4. **Implement your analysis in R:**
   - Check assumptions (normality, equal variances if applicable)
   - Perform the appropriate t-test
   - Interpret the results

5. **Document your findings:**
   - State your hypothesis clearly
   - Report the test results (t-statistic, degrees of freedom, p-value)
   - Interpret the p-value in context
   - Discuss whether the assumptions were met
   - Draw appropriate conclusions

## Example Solution Framework:

```r
# Example: Comparing temperature between May and September
# H0: Mean temperature in May = Mean temperature in September
# H1: Mean temperature in May ≠ Mean temperature in September

# Load and prepare data
data("airquality")
may_temp <- airquality$Temp[airquality$Month == 5 & !is.na(airquality$Temp)]
sep_temp <- airquality$Temp[airquality$Month == 9 & !is.na(airquality$Temp)]

# Check assumptions
shapiro.test(may_temp)    # Normality for May
shapiro.test(sep_temp)    # Normality for September
var.test(may_temp, sep_temp)  # Equal variances

# Perform t-test
result <- t.test(may_temp, sep_temp, var.equal = TRUE)
print(result)

# Interpret results
if(result$p.value < 0.05) {
  cat("We reject the null hypothesis. There is a significant difference in temperature between May and September.\n")
} else {
  cat("We fail to reject the null hypothesis. No significant difference found.\n")
}
```

<span style="background-color: yellow; font-weight: bold;">Use AI platforms for this step-by-step procedure, check feedback, rationalize and improve accordingly</span>

## Additional Dataset Information

The **airquality dataset** contains daily air quality measurements in New York from May to September 1973:

* **Ozone**: Mean ozone concentration in parts per billion (ppb)
* **Solar.R**: Solar radiation in Langleys 
* **Wind**: Average wind speed in miles per hour (mph)
* **Temp**: Maximum daily temperature in degrees Fahrenheit (°F)
* **Month**: Month (5 = May, 6 = June, 7 = July, 8 = August, 9 = September)
* **Day**: Day of month (1-31)

The dataset has 153 observations with some missing values (37 NAs in Ozone, 7 NAs in Solar.R).