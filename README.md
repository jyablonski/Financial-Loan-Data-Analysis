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

The only meaningful insight to derive from employment history is that loan amounts are typically lower if the borrower doesn't provide an employment history.  Borrowers with 10+ years of work experience typically have a higher loan amount, but that's the only group that sees a difference.

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
![boxplots5](https://user-images.githubusercontent.com/16946556/92037361-7ecb1500-ed26-11ea-9d69-2e7ce6519444.png)

Boxplots help show the interest rate increasing linearly as the risk level increases for LendingClub based on the grade they've given to the consumer.
   

![int_ratedefault9](https://user-images.githubusercontent.com/16946556/92512297-714cd980-f1c3-11ea-9b1e-2297cbe1f7bf.png)

![term_length10](https://user-images.githubusercontent.com/16946556/92512308-7578f700-f1c3-11ea-8feb-8a3488071e40.png)

![state plot 19](https://user-images.githubusercontent.com/16946556/92635652-0b2b8980-f28b-11ea-8a27-7633aa4087ee.png)


zzzzzzzzzzzzzzzzzzzzzzzzzz


![roc curve final 16](https://user-images.githubusercontent.com/16946556/92634904-d3701200-f289-11ea-9f4a-8eaa976403bb.png)

![xgb workflow](https://user-images.githubusercontent.com/16946556/92634909-d539d580-f289-11ea-8821-0f3df93e8b05.png)

![variable importance v2](https://user-images.githubusercontent.com/16946556/92634926-dc60e380-f289-11ea-878d-791a5f7282f6.png)

![XGboost hyperparameters 15](https://user-images.githubusercontent.com/16946556/92634937-e08d0100-f289-11ea-9c45-d5546f1be140.png)


## Data Modeling


![roc curve final 16](https://user-images.githubusercontent.com/16946556/92634904-d3701200-f289-11ea-9f4a-8eaa976403bb.png)

![xgb workflow](https://user-images.githubusercontent.com/16946556/92634909-d539d580-f289-11ea-8821-0f3df93e8b05.png)

![variable importance v2](https://user-images.githubusercontent.com/16946556/92634926-dc60e380-f289-11ea-878d-791a5f7282f6.png)

![XGboost hyperparameters 15](https://user-images.githubusercontent.com/16946556/92634937-e08d0100-f289-11ea-9c45-d5546f1be140.png)


Continuous Variables | Categorical Variables | Outcome Variable
-------------------- | --------------------- | ---------------
Loan Amount          | Grade                 | Loan Status
Debt-to-Income Ratio | Employment History    |
FICO Score           | Loan Purpose          |
Term Length          | 
Interest Rate        | 
Annual Income        | 



## Insights & Recommendations
* Item 1
* Item 2
  * Item 2a
  * Item 2b
  
variables i'd like to see added to see if there's any statistical impact to help:
