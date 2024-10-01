Matching Adjusted Indirect Comparison (MAIC)
================
Joshua Edefo
2024-10-01

Libraries
`{r a, message=FALSE} library(survey)  # For propensity weighting library(usethis)`

Step 1: Prepare your data

\`\`\`{r b} \# Example of individual patient data (IPD) from study A
ipd_data \<- data.frame( age = c(45, 60, 52, 38, 50, 62), sex = c(1, 0,
0, 1, 1, 0), \# 1 = Male, 0 = Female outcome = c(1, 0, 0, 1, 1, 0) \#
Binary outcome (e.g., response to treatment) )

# Example of aggregate data from study B (e.g., summary statistics for age and sex)

aggregate_summarised_data \<- list( mean_age = 50, \# Mean age in study
B prop_male = 0.6 \# Proportion of males in study B )


    Step 2: Calculate Weights Using Logistic Regression

    ```{r c}
    # Center the covariates by subtracting the study B mean and proportion
    ipd_data$age_centered <- ipd_data$age - summary_data$mean_age
    ipd_data$sex_centered <- ipd_data$sex - summary_data$prop_male

    # Fit logistic regression model to estimate the weights
    logit_model <- glm(rep(1, nrow(ipd_data)) ~ age_centered + sex_centered, 
                       family = binomial(link = "logit"), data = ipd_data)

    # Calculate weights as the inverse of the predicted probabilities
    ipd_data$weights <- 1 / fitted(logit_model)

Step 3: Apply Weights and Compare Outcomes

\`\`\`{r d}

# Compare outcomes between the weighted IPD data and study B

# For example, calculate weighted mean outcome in the IPD study

weighted_mean_outcome \<- svymean(~outcome, design = svydesign(~1, data
= ipd_data, weights = ~weights)) weighted_mean_outcome
print(weighted_mean_outcome)

    session information

    ```{r e}
    sessionInfo()
