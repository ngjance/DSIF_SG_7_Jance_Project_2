# Project 2 - Ames Housing Data and Kaggle Challenge


## Purpose of the Project
The purpose of this project is to create a model with the least Root Mean Square Error (RMSE), to predict the price of the house at sales.

## Data Cleaning

1. Rename columns
2. Missing Data Handling
   i. Remove rows with NA values where the number of NAs is lesser or equal to 5% of the total number of rows.
   ii. It is observed that cells with NaN denote not application, meaning the house does not have the feature.
   This is also same as what is stated in the data dictionary in Kaggle.
   https://www.kaggle.com/competitions/dsi-us-11-project-2-regression-challenge/data
   
   Example:
   NaN in 'Pool QC' is due to the house not having a pool.
   
   Hence, decided to fill NaN with zeros if data type is integer and 'NA' if data type is string

3. Data type
   There are three data types: integer, float and object

## Deciding on the Approach

1. There are 81 columns.
2. The columns describe different areas of house.
   Example:
   BsmtFin Type 1 refers to rating of basement finished area
   HeatingQC refers to heating quality and condition
   KitchenQual refers to kitchen quality
3. At first, I wanted to group similar columns together (eg, X_kitchen  = all the kitchen related columns) and calculate the coefficient and correlation to the sales price.
   
   Example:
   > **Bathroom**
   > bsmt_full_bath
   > bsmt_half_bath
   > full_bath  
   > half_bath  
   > bedroom_abvgr
   >
   > **Kitchen**
   > kitchen_abvgr  
   > kitchen_qual 
   >
   > **Fireplace**
   > fireplaces  
   > fireplace_qu
   
4. However there are too many areas and I am not familiar with some of the terms.
   That is, I am not sure if they should belong together in the same group.
5. To further complicate things, about 50% of the columns are categorial columns.
   This means that I have to transform each of these columns one by one before attempting to build the model.
   This would take up a lot of time and effort.
6. Due to the above two limitations in point 4 and 5, I decided to go for a simplified approach.

## The Approach - High Level

1. On high level, the approach is to transform all the columns, numeric and categorical, at the same time, and then fit the Linear Regression model.

## The Approach - Detailed
1. There are two sets of data - Train and Test.
   The test set is without the y-variable, ie, saleprice
2. I first split the Train dataset into train set and test set, as I want have a portion of the Train dataset as test set.
3. For numeric columns, I apply Standard Scaler to make sure that the values are on the same scale.
4. Instead of defining a value for each of the categorical column value, I apply the Ordinal Encoder to transform all the categorial columns.
5. I transform all the numeric and catergorial columns at the same time using ColumnTransformer.
6. I then add the Linear Regression model into the pipeline.
7. I fit the pipeline to the train set.
8. Train set score = 0.86919
9. Test set score = 0.87508

## Evaluate the Base Model   
1. RMSE = 28017.26788
2. RMSE is about 15.10% of error from y_test mean.
3. The model does not seem to work well when the sales price is about more than 400,000.

## Coefficients, Lasso and Ridge for Feature Selection
1. Calculate the absolute coefficients of the features to the saleprice so as to select the top few features to refine the model
2. Replace the Linear Regression model in the pipeline with Lasso and fit the pipeline.
3. Replace the Linear Regression model in the pipeline with Ridge and fit the pipeline.
4. All three show the same top 20 features

## KNN
1. I wanted to see if KNN would be a better model compared to Linear Regression
2. I replaced the Linear Regression model in the pipeline with Lasso and fit the pipeline.
3. KNN did not perform well

## Refining the Model
1. I wanted to know what is the optimal number of features to select.
2. I also wanted to use PolynomialFeatures to generate a new feature matrix.
   I need to find out what is the optimal polynomial degree.
3. I use a for loop to build and test the model to find the optimal number of features and polynomial degree.
4. The best combination of number of features and polynomial degree is 20 and 2 respectively.

## Fitting the Final Model
1. Define new X_train with the top 20 features.
2. Apply PolynomialFeatures with degree of 2 to numeric columns
3. Apply the Ordinal Encoder to transform all the categorial columns
4. Transform all the numeric and catergorial columns at the same time using ColumnTransformer
5. Add the Linear Regression model into the pipeline.
7. Fit the pipeline to the train set.
8. Train set score = 0.90034
9. Test set score = 0.89267
10. There is very minor overfitting, which can be overlooked
11. RSME = 25970.38887, which is lower than the initial model
12. RMSE is about 14.00% of error from y_test mean, which is 1% lower than the initial model
13. The plot of y_test against y_pred shows a better fit than the initial model except for a few more obvious outliers.

## Apply Model to Test Set and Predict Sales Price
1. Mean Predicted sales price = 181,040.08

## Conclusion
- I use a 'one-size fit all' transformation approach due to time and effort efficiency.
- The initial model shows a better score than when I only use 20 features (with no PolynomialFeatures).
This could mean that all the variables are important in determining the price of the house.
I would attest it is true that all variables have some form of importance, because you can't just look at the kitchen (for example) of the house to determine the price of the house and not look at the bedroom condition or the neighborhood of the house.
- The final model chosen is with 20 features and applying polynomial degree of 2 to numeric columns.
- The mean predicted price of the house at sales $181,040.08