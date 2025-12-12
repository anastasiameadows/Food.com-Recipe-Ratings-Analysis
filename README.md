# Introduction

I've chosen to explore the following question: what types of recipes tend to have higher average ratings? I've considered a few options, like that recipes that take less time or require fewer ingredients may have higher ratings. I'm also curious if baked goods and desserts have higher ratings. I speculate that people who bake have more culinary experience and are more likely to use recipes online. Overall, I'm mostly curious if I can infer something about the reveiwer's diet and lifestyle based on their ratings.  

This dataset has 234429 rows, each of which represents a review. All recipes are present, even if the recipe does not have a review. If the recipe has no review, its rating is null, and it should only have one row in the dataframe.  

A variety of columns are important for answering this question, since 'type' of recipe is a very vague framing. Some recipes are sweet, some are savory, some take only a few ingredients, and others take a very long time to make. Because we aren't defining what a 'type' could mean, we have to analyze many columns. The most significant column is 'tags.' This column contains lists of tags that inform which recipes will appear when searching Food.com, and they can include things like ingredients, cook time, serving size, type of cuisine, and more. Other important columns include 'n_steps' and 'n_ingredients', which tell us the number of steps and ingredients required for the recipe, respectively. The 'ingredients' and 'nutrition' column are also necessary to tell whether healthier or high-calorie meals are rated higher. And, of course, the 'rating' and 'avg_rating' columns are the base of our analysis. The 'rating' column contains a discrete float 1-5 or a null value if there is no rating. The 'avg_rating' column is continous, and contains any float between 1-5 or a null value if there is no rating.  

# Data Cleaning and Exploratory Data Analysis

Here are all the steps I applied to clean the data:
1. Removed any white spaces and trailing characters
2. Converted the 'tags' and 'nutrition' columns to a list of strings
3. Expanded 'nutrition' to 7 separate columns - calories, total_fat, sugar, sodium, protein, saturated_fat, and carbs
4. Filled any missing values in above 7 columns with null
5. Replaced empty strings and strings like 'nan', 'null', or 'none' with null values

These steps are crucial for any future analysis. Step 2 is necessary to identify specific tags and how they effect the data. Step 3 makes it easier to analyze specific nutritional information that may be more important to users' ratings, like calories and sugar content. And without steps 4 and 5, several columns with missing values would appear to have none. 

Let's look at some of the distributions of these columns. The most important columns of our dataset are 'rating' and 'avg_rating.' The distribution of each discrete rating is shown below.

<iframe
  src="assets/rating-distribution.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>


# Assessment of Missingness

# Hypothesis Testing

# Framing a Prediction Problem

# Baseline Model

# Final Model

# Fairness Analysis
