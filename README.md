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
  src="assets/rating_distribution.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The vast majority of recipes are rated a 5/5. This implies that no matter what kind of recipe it is, the ratings are bound to skew higher. This is an important consideration to keep in mind. It presents us with two possible alternatives: the majority of recipes on Food.com are perfect, or that the users are more likely to post a review when they enjoy a recipe. Thankfully, because I will be trying to analyze the average rating of recipes, there will be more variation to analyze. Even if all the ratings skew high, we can compare the differences between a 4.2 and a 4.7 average rated recipe.

To better understand the relationship between recipe ratings and the types of recipes, let's look at how the sugar content of a recipe affects its rating.

<iframe
  src="assets/sugar_rating.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The graph above shows a ton of recipes clustered below 250 grams of sugar. This is because the average sugar content of all our recipes is around 64 grams, and the standard deviation is around 210 grams. Because we already know most recipes are 5 stars, we should expect the majority of data points to be clustered around the top left of the graph. What's most interesting about this graph is that as the sugar content grows more extreme, the lower ratings drop off. Amongst recipes with 1000 grams of sugar or more, there are only 4 1-star reviews and 5 2-star reviews. And because all the 1 and 2-star reviews after this point lie on the graph's tick marks, this implies that there is either only 1 review for this recipe or that all reviews gave that recipe the same low rating. By contrast, there are too many 3-star or more recipes to visibly count.

Let's explore sugar content even further. I've created a pivot table that bins sugar content into 5 separate bins. I've also binned the number of steps a recipe has into bins. The contents of this table are the average rating based on these groups.

<iframe
  src="assets/pivot_table.html"
  width="500"
  height="300"
  frameborder="0"
></iframe>

Once again, because the ratings in our data are skewed high, every average rating in the pivot table is above a 4.5. One thing of note is that in general, recipes with very few steps or recipes with 20+ steps seem to have higher average ratings. The exception to this is recipes with the most sugar (1000g+) and the most steps (20+) which have an average rating of about 4.548. In fact, the highest ratings cluster around 1 of the two extremes: they either have lots of sugar and very few steps, or lots of steps with very little sugar. This suggests to us that users may prioritize one aspect of a recipe over all others - they care about sugar content OR they care about recipe length, but not both. For example, let's imaginge a user who is on a diet. They may be willing to spend more time cooking if the result is healthier food. This example makes intuitive sense and aligns with out data.

# Assessment of Missingness

# Hypothesis Testing

# Framing a Prediction Problem

# Baseline Model

# Final Model

# Fairness Analysis

For my fairness analysis, my two groups are as follows:
- X: Quick Recipes (30 minutes or under)
- Y: Long Recipes (longer than 30 minutes)

I chose the cutoff of 30 minutes because roughly half of the ratings belong to each group (107529 ratings of recipes 30 minutes or under versus 126900 ratings of recipes longer than 30 minutes). Given that there are just under 10000 recipes that are longer than 300 minutes (5 hours), I find this imbalance fascnating. I speculate that recipes longer than 5 hours include waiting overnight for batter, dough, or similar circumstances. Because of this, I suspect sweet recipes take longer, and because we know savory recipes are rated higher, I think there could be a slight bias towards quick recipes.

Once again, my evaluation metric is the root mean-squared error (RMSE). This is the same metric used in both models. This metric measures the average prediction error for recipe ratings. A higher RMSE indicates worse model performance, meaning the model's predictions are further from the actual ratings.

Here are my two hypotheses:
- **Null:** Our model is fair. There is no significant difference between how the model treats short (30 minutes or under) versus long (over 30 minutes) recipes.
- **Alternative:** Our model is unfair. There is a significant difference between how the model treats short (30 minutes or under) versus long (over 30 minutes) recipes.

My test statistic is the difference in mean-squared errors between the two groups. Using difference of means for a test-statistic is standard for a two-tailed permutation test, which I will perform to determine the fairness of my model. I will use a significance level of p = 0.05.

After running a two-tailed permutation test with 1,000 permutations, I found the following statistics:
Observed difference in MSE between the 2 groups: -0.018981
Observed difference in RMSE between the two groups: -0.026868
P-value: 0.0070

Because the resulting p-value (0.007) is less than our significance level, I reject the null hypothesis. There is a significant difference between how my final model treats the two groups, meaning that the model is unfair.


