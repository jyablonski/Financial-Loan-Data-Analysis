# Financial Loan Data Analysis

## Introduction
  
This project goes through my process of analyzing a 2.2 million row dataset of approved financial loans.  The motivation for this project came from applying to a financial loan company similar to the one that provides this dataset, and wanting to get an idea of the kind of data these companies work with and see how I could apply my skills to an industry that I wasn't familiar with.

## The Data
[The Dataset](https://www.kaggle.com/wordsforthewise/lending-club) comes from LendingClub, a peer-to-peer lending company.  They pair investors with borrowers looking to get a loan.  The investors get interest, the borrowers get their loan, and LendingClub makes money by charging origination & service fees for connecting the two entities together.  

The dataset comes with all sorts of characteristics of the client like credit scores, annual income, employment history, and details of the loan like term length, loan amount, and interest rate.  All consumers in this dataset have FICO scores ranging from 600 - 850.  LendingClub also provides a "Grade" variable, which includes 7 unique groups that the company uses to grade the consumer based on their level of risk, and LendingClub uses it to determine the interest rate of the loan.  A Grade "A" client is likely to be a good client is typically assigned the lower interest rates, while a Grade "G" client indicates a high level of risk and typically gets a higher interest rate.  

The data only includes approved loans, not the ones that were rejected.  Every consumer in this dataset was approved for the loan they requested and some have completed the terms of the loan, while others are still paying it off.  It's important to keep in mind that almost 40% of the loans in the dataset are still current and not completed yet, so the number of defaults are likely to be higher than what is shown in the analysis as of right now.  

Continuous Variables | Categorical Variables
-------------------- | ---------------------
Loan Amount          | Loan Status
Debt-to-Income Ratio | Employment History
FICO Score           | Loan Purpose
Term Length          | Grade
Interest Rate        | 
Annual Income        | 

## Exploratory Data Analysis


![LoanDistribution1](https://user-images.githubusercontent.com/16946556/92037964-7b845900-ed27-11ea-873c-4e070f4b8ba9.png)

This is the distribution of all loans handed out by LendingClub in this dataset.  They range from $1000 to $40,000, with the average being $15,047 and the median being $12,900. 


![employmentHistory6](https://user-images.githubusercontent.com/16946556/92037353-7d99e800-ed26-11ea-9ac5-4e4c84c42dec.png)

The only meaningful insight to derive from employment history is that loan amounts are typically lower if the borrower doesn't provide an employment history (NA).  Borrowers with 10+ years of work experience typically are given a higher loan amount, but that's the only group that sees a noticeable difference.


![gradebreakdown](https://user-images.githubusercontent.com/16946556/92037354-7e327e80-ed26-11ea-9452-975e1caa6aa9.png)

Here is a basic informal table where you can see the breakdown of the average statistics by the Grade type of the consumer.  Interest rate, loan amount, and average term of the loan all increase as the Grade declines.  The default term length for a loan is 3 years, LendingClub charges higher fees and interest rates if a borrower tries getting a 5 year loan.  The table kind of gives you an idea of how LendingClub's proprietary grading system works.


![LoanAmounts2](https://user-images.githubusercontent.com/16946556/92037357-7e327e80-ed26-11ea-8d6c-1069a46e07f0.png)

This is a breakdown of how much total $ LendingClub has handed out to consumers by Grade type.  Grades A, B, and C are less likely to default and are lower risk clients, so it makes sense those are the largest groups.


![LoanDefaults3](https://user-images.githubusercontent.com/16946556/92037963-7b845900-ed27-11ea-8f9b-b485850e9738.png)

This is a similar graph, except showing # of loans rather than aggregate total $ of loans.  The text in black indicates the % Chance of Default for each individual Grade group.


![loanPurpose7](https://user-images.githubusercontent.com/16946556/92037359-7ecb1500-ed26-11ea-9487-8f607c279986.png)

In the dataset is a variable called "purpose" which indicates the consumer's purpose for needing the loan.  The overwhelming majority seems to be clients who are looking to consolidate their debt or pay off other credit cards, which is an important insight to keep in mind.  These loans are being given to people who already have debt or have already taken on loans in the past that they can't pay off.


![LoanStatus4](https://user-images.githubusercontent.com/16946556/92037360-7ecb1500-ed26-11ea-8f44-0d52d9885470.png)

The status of the loan indicates whether the clients have paid it off, whether they defaulted (the loan was Charged Off), or if it's still ongoing.


Interest rates seemed to be the most significant predictor in identifying which loans are likely to default.  They range from 6% to 26% and there are a number of insights to develop from analyzing different interest rate related plots.

![boxplots5](https://user-images.githubusercontent.com/16946556/92037361-7ecb1500-ed26-11ea-9d69-2e7ce6519444.png)

Boxplots help show the interest rate increasing linearly as the risk level increases for LendingClub based on the grade they've given to the consumer.
   

![int_ratedefault9](https://user-images.githubusercontent.com/16946556/92512297-714cd980-f1c3-11ea-9b1e-2297cbe1f7bf.png)

Similar idea, as interest rates increase the % chance of default skyrockets.

![term_length10](https://user-images.githubusercontent.com/16946556/92512308-7578f700-f1c3-11ea-8feb-8a3488071e40.png)

We can see as you jump from a 3 year loan to a 5 year loan, the # chance of default doubles from 15 to 31%.  This is likely due to those increased interest rates I previously spoke about, as well as a 5 year debt being more difficult to manage than a 3 year debt.  Regardless, it's not a surprise why LendingClub prefers borrowers to stay on 3 year loan plans.

![state plot 19](https://user-images.githubusercontent.com/16946556/92635652-0b2b8980-f28b-11ea-8a27-7633aa4087ee.png)

This is a basic plot of Number of Loans per State, with the number of default loans in red.  It's not a surprise seeing California and New York leading the list, and I tried exploring other methods to see if there were any other insights to gather on State data but didn't find anything worth noting.  


The Exploratory Analysis helped give me an idea of what variables are probably most important to predicting loan defaults, and which ones probably won't help me as I head into the Data Modeling phase.

## Data Modeling

I used the tidymodels package in R to handle all of my data preprocessing, perform all of the machine learning algorithms, and handle all of the hyperparameter tuning.  I used a 75% Training, 25% Testing Split with 10 fold cross validation resampling, and tried out 6 different models.  Below is the list of variables I chose to utilize.

Continuous Variables | Categorical Variables | Outcome Variable
-------------------- | --------------------- | ---------------
Loan Amount          | Grade                 | Loan Status
Debt-to-Income Ratio | Employment History    |
FICO Score           |                       |
Term Length          | 
Interest Rate        | 
Annual Income        | 


![plot 20](https://user-images.githubusercontent.com/16946556/93003894-4631f500-f4f7-11ea-9714-3788042fd986.png)

This is the list of preprocessing steps I applied to the training data.  Correlation filter to make sure there's no multicolinearity, zero variance, and normalization of all numeric variables.


![roc curve final 16](https://user-images.githubusercontent.com/16946556/92634904-d3701200-f289-11ea-9f4a-8eaa976403bb.png)

I chose to build XGBoost, Logistic Regression, Random Forest, Decision Tree, KNN, and SVM Models and compared them with an ROC Curve using their predictions after the resampling process.  These results aren't stellar, but KNN and Decision Trees were clearly lagging behind in terms of predictive power.  Moving forward I chose to use the XGBoost model as my final model.

![XGboost hyperparameters 15](https://user-images.githubusercontent.com/16946556/92634937-e08d0100-f289-11ea-9c45-d5546f1be140.png)

This is a plot of the XGBoost hyperparameter tuning, the best parameters are automatically selected when using the select_best function from the tune package.

![xgb workflow](https://user-images.githubusercontent.com/16946556/92634909-d539d580-f289-11ea-8821-0f3df93e8b05.png)

This is the workflow for the XGBoost model.  The hyperparameter tuning took 3 hours and these parameters gave me the best accuracy.


![xgboost accuracy](https://user-images.githubusercontent.com/16946556/93003877-361a1580-f4f7-11ea-9a33-6c0ada6d42ac.png)

These are the accuracy numbers of the XGBoost model on the testing data.  81% accuracy and .70 AUC are not the most tremendous numbers, but we're getting somewhat reasonable levels of accurate prediction with this model.


![new vip plot 21](https://user-images.githubusercontent.com/16946556/93004095-bf7e1780-f4f8-11ea-826f-1de4d45f42b5.png)

This is a variable importance plot using the vip package which helps explain which variables contributed the most to the model predictions.  It helps show that Loan Purpose didn't have much predictive power and that it should probably be removed in future model building.  This is probably due to almost all of the applicants using the loans for the same general purpose (debt consolidation).


## Insights & Recommendations
Predicting loan defaults is hard !  Lending companies have no idea which applicants are going to be able to pay out their loans or whether they're accepting a client who will end up defaulting and causing a headache for them.  Based on what I've learned from this project with regarding to the lending industry, I've laid out some key recommendations as well as suggested improvements to help increase the productivity of these companies.

* Keep track of all current oustanding loans, the associated Grade Types of the clients, and the term lengths of the loans.
  * Don't hand too many loans or total $ out to any 1 Grade Type in particular at once, keep the portfolio diverse & spread out.
* Set thresholds in the system for handing out too many loans of 1 type such as: 25% + interest rate, Grade G clients, and 5 year term length loans.
  * Create warnings & alert systems when any of thresholds are about to be exceeded.

Some variables I'd like to see added in future datasets that might help increase predictive power would be age & gender of the applicant, a more detailed report of their credit history, and in an ideal world it'd be really informative to see data of the clients that defaulted and get an idea of what factors led to them defaulting on their loan.  While not all of these characteristics might be meaningful, I believe some of them could help increase the predictive power of the modeling process and help the loan companies get a more accurate & realistic understanding of their clients which could improve the efficiency of the business.
