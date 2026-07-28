# Understanding of the Problem
The objective of this exercise is to figure out why the telecom client is losing customers. By diving into the customer data, we want to spot the main behavioral and demographic patterns that signal when someone is about to walk away. With those insights, we can build a clean, reliable data pipeline and a predictive model to flag high-risk customers before they leave, helping the company step in with targeted retention offers to save revenue.

# Important Data-Quality Issues Discovered
- The 'TotalCharges' column was initially text, instead of numerical values.
- The data type issue was caused by rows with an empty text space inside them. Because these weren't standard null values, running a typical `.isnull().sum()` search returned `0`, keeping these missing values completely invisible until we forced a numeric conversion.
- Several features (like `OnlineSecurity`, `OnlineBackup`, and `TechSupport`) had a third option called `"No internet service"`. `MultipleLines` had a similar issue with `"No phone service"`.

# Major Preprocessing & Feature-Engineering Decisions
- I used `pd.to_numeric(..., errors='coerce')` to force hidden blank spaces into true `NaN` values and replaced them with the median of the data columns.
- I ran a loop to replace the `"No internet/phone service"` text blocks with `"No"`.
- Added 5 new Features:
    1.  `TotalServices`: Adds up how many add-on features a customer actually uses.
    2.  `AvgChargePerService`: Tracks how much a customer pays relative to how many features they have.
    3.  `IsSeniorAlone`: Checks if a customer is a senior with no partner or dependents and separates those citizens.
    4.  `LongTermContract`: Checks the Contract duration and converts it to a binary value (0 or 1).
    5.  `UsesStreamingServices`: A flag showing a customer's streaming medium and media.
- I split the data into training and tests before running the 'StandardScaler' to prevent the data from leaking into the model's training process.
- I added 'dtype=int' to 'pd.get-dummies()' to convert the categorical features into '1's and '0's.

# Model Results & Evaluation Metrics
- The program uses the Logistic Regression model scaled with a `StandardScaler`, due to its training speed, binary output of 0 and 1, etc.
- I used a 80/20 train-test split to make sure the ratio of churned vs. retained customers reamined identical in both sets.
- The model has an overall validation accuracy of 80%. The precision and recall scores are stable across both outcomes, showing that the preprocessing and encoding I used is reliable.

# Most Important Churn-Related Findings
- The  best  indicator of churn is the contract type. Customers on Month-to-Month contracts leave at an incredibly high rate compared to those that have 1- or 2-year agreements.
- On average, customers who left had notably higher monthly bills than those who stayed.
- Customers who only have a phone or only internet are much more likely to leave than those who use multiple add-ons

# Limitations of The Analysis
- This dataset is limited in the sense that it cannot show dynamic trends, such as sudden changes in a customer's data usage, for example.
- We don't have data on external factors like competitor pricing drops, local network outages, or regional economic changes, all of which heavily influence why someone decides to switch providers.
- The dataset does not take external factors into consideration, such as a competitor company dropping prices, the economic conditions, etc.

# Three Practical Business Recommendations
1. Provide month-to-month customers with discounts, if they choose to extend their contract duration.
2. Create packages that lower the cost of add-ons if a customers uses more of them.
3. Offer fixed-rate packages to customers to avoid eventually providing them with a rise in billing.