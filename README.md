# Financial Loan Data Analysis

## Introduction
  
This project goes through my processing for analyzing a 2.2 million row Dataset of approved financial loans.  The Dataset is available [here](https://www.kaggle.com/wordsforthewise/lending-club).

## The Data
The Dataset comes from LendingClub, a company that provides loans to consumers and businesses.  There is only consumer loan data in this Dataset, and it comes with all sorts of characteristics like FICO scores, annual income, employment history, loan amount, and interest rate.  All consumers in this dataset have FICO scores ranging from 600 - 850.

The data only includes approved loans, not ones that were rejected.  Every consumer in this Dataset was approved for the loan they requested and some have completed the terms of the loan, while others are still paying it off.

LendingClub also provides a "Grade" variable, which includes 7 groups that the company uses to grade the consumer on their level of risk.  A Grade "A" client is likely to be a good client and to pay off their loan, while a Grade "G" client indicates a high level of risk and the most likely to default.  


## Exploratory Data Analysis

![employmentHistory6](https://user-images.githubusercontent.com/16946556/92037353-7d99e800-ed26-11ea-9ac5-4e4c84c42dec.png)

The only meaningful insight to derive from employment history is that LendingClub isn't willing to take as much risk with consumers that are unwilling to provide details on their employment history.  You can see the average loan amount is considerably lower for these candidates with a missing employment history relative to all the others.


![gradebreakdown](https://user-images.githubusercontent.com/16946556/92037354-7e327e80-ed26-11ea-9452-975e1caa6aa9.png)

Here is a basic informal table where you can see the breakdown of the average statistics by the Grade type of the consumer.  Interest rate, loan amount, and average term of the loan all increase as the Grade declines, indicating a higher risk for LendingClub.

![LoanAmounts2](https://user-images.githubusercontent.com/16946556/92037357-7e327e80-ed26-11ea-8d6c-1069a46e07f0.png)

This is a breakdown of how much total $ LendingClub has handed out to consumers by Grade type.  Grades A, B, and C are less likely to default and have lower overall risk for LendingClub, so it makes sense that's where they're handing out most of their money.

![loanPurpose7](https://user-images.githubusercontent.com/16946556/92037359-7ecb1500-ed26-11ea-9487-8f607c279986.png)

In the Dataset is a variable called "purpose" which indicates the consumer's purpose for needing the loan.  The overwhelming majority seems to be clients who are looking to consolidate their debt or pay off other credit cards, which is an important insight to keep in mind.  LendingClub is giving loans to people who already have debt or have already taken on loans in the past that they can't pay off.

![LoanStatus4](https://user-images.githubusercontent.com/16946556/92037360-7ecb1500-ed26-11ea-8f44-0d52d9885470.png)

The status of the loan indicates whether the clients have paid it off, whether they defaulted (the loan was Charged Off), or if it's still ongoing.
![boxplots5](https://user-images.githubusercontent.com/16946556/92037361-7ecb1500-ed26-11ea-9d69-2e7ce6519444.png)

Boxplots help show the interest rate increasing linearly as the risk level increases for LendingClub based on the grade they've given to the consumer.  


![LoanDefaults3](https://user-images.githubusercontent.com/16946556/92037963-7b845900-ed27-11ea-8f9b-b485850e9738.png)

This is a similar graph, except showing # of loans rather than aggregate total $ of loans.  The text in black indicates the % Chance of Default for each individual Grade group.  

![LoanDistribution1](https://user-images.githubusercontent.com/16946556/92037964-7b845900-ed27-11ea-873c-4e070f4b8ba9.png)

This is the distribution of all loans handed out by LendingClub in this dataset.  They range from $500 to $40,000, with the average being $15,047 and the median being $12,900.  

## Data Modeling
