# human-vs-llm-prose
README – Final Project Replication Package
==========================================


1. Overview
-----------
This folder contains all code and data needed to replicate the results in my final project on 
the relationship between fresh vegetable consumption and macroeconomic conditions.

All analysis is done in R. The main script is: final_project.Rmd


2. Data
-------
The replication package includes all raw data files used in the analysis.
Each file is in .csv format and contains a monthly time series from 1986–2023.
All datasets include a date column named "observation_date".

Below is a description of each file and how it is used in the analysis.

- expenditure_Y.csv

Source: FRED
Variable: Monthly consumer expenditure on fresh fruits and vegetables (USD).

- medianincome.csv

Source: U.S. Census / FRED
Variable: Monthly median household income (USD).

- unemployment.csv

Source: Bureau of Labor Statistics (BLS)
Variable: Monthly unemployment rate (percent).

- price.csv

Source: BLS CPI (Fresh Vegetables CPI component)
Variable: Vegetable price index.

- cpi_veg_fruit.csv

Source: FRED
Variable: Combined fruits and vegetables CPI index.

- fresh_fruit_and_veg_retail.csv

Source: FRED
Variable: Retail prices for fresh fruits and vegetables.

**Notes on Data Processing**
All series are merged by date using left joins.
Missing months in any dataset lead to removal of that month from the merged dataset.
No statistical transformations were applied except log(), described in the code for Figs 4–5.


3. How to run the code
----------------------
1. Open RStudio.
2. Set the working directory to the folder containing this replication package.
3. Open `final_project.Rmd`.
4. Install required packages if needed:
       install.packages(c("dplyr","ggplot2","stargazer","AER","car",
                          "lmtest","sandwich","tseries","gridExtra","knitr"))
5. Knit the R Markdown file.  
   This will sequentially produce all outputs described below.

4. Mapping from code to outputs
-------------------------------

FIGURE 1 – “Determinants of Home Expenditure (fig 1)”

Code block:
    model4 <- lm(home_expenditure ~ income + unemployment + price_of_veg, data = df2)
    stargazer(model4, ... title = "Determinants of Home Expenditure (fig 1)", ...)

Output:
    Regression table with coefficients for income, unemployment rate, and price of vegetables.


FIGURE 2 – “Relationship between home expenditure and each variable (fig 2)”

Code block:
    avPlots(model4, ask = FALSE,
            main = "Relationship between home expenditure and each variable (fig 2)")

Output:
    Added-variable plots showing partial relationship of home expenditure with each regressor.


FIGURE 3 – “Non linearity Check for OLS Model (Fig 3a/3b)”

Code block:
    par(mfrow = c(2, 2))
    plot(model4)
    mtext("Non linearity Check for OLS Model (Fig 3a)", outer = TRUE, ...)
    residualPlots(model4, main = "Residual Plots for OLS Model (Fig 3b)")
    par(mfrow = c(1, 1))

Output:
    Standard diagnostic plots (residuals vs fitted, QQ plot, etc.) plus car::residualPlots.


FIGURE 4 – “IV Demand Curve (fig 4)”

Code block:
    iv_demand <- ivreg(log_q ~ log_p | income + unemployment, data = df_iv)
    df_iv$log_q_hat <- fitted(iv_demand)
    plot(df_iv$log_p, df_iv$log_q, ..., main = "IV Demand Curve (fig 4)")

Output:
    Scatter plot of log(price) vs log(quantity) with IV demand line added in Fig 5.


FIGURE 5 – “IV Regression: Demand for Vegetables (fig 5)”

Code block:
    p_seq <- seq(min(df_iv$log_p), max(df_iv$log_p), length.out = 200)
    df_tmp <- data.frame(log_p = p_seq,
                         income = mean(df_iv$income),
                         unemployment = mean(df_iv$unemployment))
    lines(p_seq, predict(iv_demand, newdata = df_tmp), ...)
    stargazer(iv_demand, title = "IV Regression: Demand for Vegetables (fig 5)", ...)

Output:
    (i) Fitted IV demand line overlaid on Fig 4;  
    (ii) Regression table for IV demand model.


FIGURE 6 – “Statistics table (fig 6)”

Code block:
    stargazer(df1[, c("expenditure","income","unemployment","price_of_veg")],
              type = "text", title = "statistics table (fig 6)",
              summary.stat = c("n","mean","sd","max","min"))
Output:
    Descriptive statistics table for main variables.


FIGURE 7 – “Time Series: Expenditure, Vegetable Prices, and Unemployment (fig 7)”

Code block:
    df1_clean <- df1 %>% mutate(observation_date = as.Date(observation_date)) %>% drop_na(expenditure)
    p1 <- ggplot(... expenditure ...)
    p2 <- ggplot(... price_of_veg ...)
    p3 <- ggplot(... unemployment ...)
    grid.arrange(p1, p2, p3, ncol = 2,
                 top = "Time Series: Expenditure, Vegetable Prices, and Unemployment (fig 7)")
Output:
    Combined time-series panel with three ggplot graphs.


FIGURE 8 – “Regression Results: OLS vs IV (fig 8)”

Code block:
    m1 <- lm(home_expenditure ~ income + unemployment + price_of_veg, data = df2)
    m2 <- ivreg(log_q ~ log_p | income + unemployment, data = df_iv)
    stargazer(m1, m2, title = "Regression Results: OLS vs IV (fig 8)", ...)
Output:
    Side-by-side regression table comparing OLS and IV models.


FIGURE 9 – “Diagnostics for OLS Regression (Fig 9)”

Code block:
    bptest(m1); resettest(m1); shapiro.test(residuals(m1))
    par(mfrow = c(1, 2), oma = c(0,0,3,0))
    plot(fitted(m1), residuals(m1), main = "Residuals vs Fitted")
    qqnorm(residuals(m1)); qqline(residuals(m1), col = "red")
    mtext("Diagnostics for OLS Regression (Fig 9)", outer = TRUE, ...)

Output:
    (i) Numerical test results (Breusch–Pagan, RESET, Shapiro–Wilk);  
    (ii) Residuals-vs-fitted plot and QQ plot with overall title.


FIGURE 10 – “Diagnostics for OLS and IV Models (fig 10)”

Code block:
    model_diagnostics <- function(model, model_name) { ... }
    diag_table <- bind_rows(model_diagnostics(m1, "OLS (m1)"),
                            model_diagnostics(m2, "IV (m2)"))
    kable(diag_table, digits = 4,
          caption = "Diagnostics for OLS and IV Models (fig 10)")

Output:
    Summary table with R2, adjusted R2, F-stat, residual standard error, and p-values
    for BP, RESET, and Jarque–Bera tests for both models.


5. Reproducing results in the Word document
-------------------------------------------

- Descriptive statistics table: Fig 6
- Graphs of data: Fig 2, 3, 4
- Diagnostics: Fig 9, 10
- Determinants of Home Expenditure: Fig 1
- Table “Regression Results: OLS vs IV”: Fig 8  
- Table “Diagnostics for OLS and IV Models”: Fig 10  


End of README
