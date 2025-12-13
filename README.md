# Introduction

I've chosen to explore the following question: what types of recipes tend to have higher average ratings? I've considered a few options, like that recipes that take less time or require fewer ingredients may have higher ratings. I'm also curious if baked goods and desserts have higher ratings. I speculate that people who bake have more culinary experience and are more likely to use recipes online. Overall, I'm mostly curious if I can infer something about the reveiwer's diet and lifestyle based on their ratings.  

This dataset has 234429 rows, each of which represents a review. All recipes are present, even if the recipe does not have a review. If the recipe has no review, its rating is null, and it should only have one row in the dataframe.  

A variety of columns are important for answering this question, since 'type' of recipe is a very vague framing. Some recipes are sweet, some are savory, some take only a few ingredients, and others take a very long time to make. Because we aren't defining what a 'type' could mean, we have to analyze many columns. The most significant column is 'tags.' This column contains lists of tags that inform which recipes will appear when searching Food.com, and they can include things like ingredients, cook time, serving size, type of cuisine, and more. Other important columns include 'n_steps' and 'n_ingredients', which tell us the number of steps and ingredients required for the recipe, respectively. The 'ingredients' and 'nutrition' column are also necessary to tell whether healthier or high-calorie meals are rated higher. And, of course, the 'rating' and 'avg_rating' columns are the base of our analysis. The 'rating' column contains a discrete float 1-5 or a null value if there is no rating. The 'avg_rating' column is continous, and contains any float between 1-5 or a null value if there is no rating.  

# Data Cleaning and Exploratory Data Analysis

Here are all the steps I applied to clean the data:
1. Fill all 0 ratings with null
2. Removed any white spaces and trailing characters
3. Converted the 'tags' and 'nutrition' columns to a list of strings
4. Expanded 'nutrition' to 7 separate columns - calories, total_fat, sugar, sodium, protein, saturated_fat, and carbs
5. Filled any missing values in above 7 columns with null
6. Replaced empty strings and strings like 'nan', 'null', or 'none' with null values

These steps are crucial for any future analysis. The first step makes logical sense, as ratings of 0 imply no review is given. This will come in handy when evaluating the missingness of different columns later on. Step 2 is necessary to identify specific tags and how they effect the data. Step 3 makes it easier to analyze specific nutritional information that may be more important to users' ratings, like calories and sugar content. And without steps 4 and 5, several columns with missing values would appear to have none. 

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
  height="200"
  frameborder="0"
></iframe>

Once again, because the ratings in our data are skewed high, every average rating in the pivot table is above a 4.5. One thing of note is that in general, recipes with very few steps or recipes with 20+ steps seem to have higher average ratings. The exception to this is recipes with the most sugar (1000g+) and the most steps (20+) which have an average rating of about 4.548. In fact, the highest ratings cluster around 1 of the two extremes: they either have lots of sugar and very few steps, or lots of steps with very little sugar. This suggests to us that users may prioritize one aspect of a recipe over all others - they care about sugar content OR they care about recipe length, but not both. For example, let's imaginge a user who is on a diet. They may be willing to spend more time cooking if the result is healthier food. This example makes intuitive sense and aligns with out data.

# Assessment of Missingness

There are 7 columns in the data set with missing values: name, description, user_id, date, rating, avg_rating and review. There is only 1 missing name, user_id, and date, which are all part of the same review. We can assume it's likely the user's account was deleted, but their review remained posted. For our purposes then, we will only focus on the missingness of the descriptions (117), ratings (5036), average ratings (2777), and reviews (58). 

Of these columns, it is possible the missingness of the descriptions is NMAR. It is difficult to determine if this missingness could be dependent on other columns since the description is determined before ratings and reviews. It is possible that older recipes could have deleted descriptions, which would make this column MAR. If so, I would believe none of the columns with missing values are NMAR.

However, I am much more interested in the missingness of the ratings column. There are very many missing ratings in the dataset. Following the same logic as above, wehre older recipes could have deleted decriptions, they could also have deleted ratings. I tested to see if this is the case. In addition, I tested to see if recipe length has an effect on a rating's missingness.

For the first test, here are my two hypotheses:  
**Null:** The missingness of a rating is independent of the year the recipe was posted.    
**Alternative:** The missingness of rating is dependent of the year the recipe was posted.  

First, I added a binary column that determined if a rating was present or missing. Then, I observed the difference in the average year a recipe was posted between reviews with present and absent ratings. The result was -0.2963042658775521, or a little more than 100 days difference between the two means.

I ran a permutation test on these values with 10000 permutations. This resulted in a p-value of 0. Because this value is less than 0.05, **I reject the null hypothesis** and find that the missingness of a rating is dependednt on the year the recipe was posted. Below is a graph to help interpret the results.

<iframe
  src="assets/year_distribution.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The above graph shows the difference in distributions between the years a recipe was posted depending on the missingness of the ratings column. The distributions are both skewed right, although because there are far more reviews with ratings than without, the distribution for present ratings is a lot larger. Despite this, we can clearly see that for reviews with ratings, there are far fewer reviews for recipes posted in 2009 than in 2008. By contrast, this drop-off is a lot smaller in the distribution for reviews without ratings. This implies that there are a lot more missing ratings for older recipes since the distribution for missing ratings is much more even. This makes sense intuitively - users might have deleted their old ratings, or ratings have become more commonplace overtime.

I also ran a permutation test on the minutes column with respect to missing ratings. Here are my two hypotheses:  
**Null:** The missingness of a rating is independent of the recipe's average rating.  
**Alternative:** The missingness of rating is dependent of the recipe's average rating.

Once again, I take the difference of means in the observed values. This is -51.45237039852127, which means recipes with ratings take on average about 50 minutes less than recipes with ratings. However, the permutation test gave a p-value of 0.1249, which is greater than 0.05, meaning this difference is insignificant, and **we retain the null hypothesis.**

Despite this, the first permutation test is evidence the missingness of the ratings column is MAR.

# Hypothesis Testing

I ran another permutation test based on this research question - do savory recipes tend to receive higher ratings than sweet recipes? I chose this question since savory and sweet recipes have large, roughly equal sample sizes (11679 vs 10371). It's important to note that, while all recipes should fall into a camp of either savory or sweet, not every recipe is tagged savory or sweet. We could assume these things through other tags, but that would lead to potential errors. For example, in most cases, the tag 'dessert' would imply sweet, but this is not always the case. For our purposes, since the sample sizes are still large, I will stick to the 'savory' and 'sweet' tags as indicators.

Here are my hypotheses:  
**Null:** Sweet recipes and savory recipes have the same average rating.  
**Alternative:** Savory recipes have a higher average rating than sweet recipes.

My test statistic is the difference in sample means, and I applied this statistic in a one-sided Welch's two-sample t-test with a significance level of 0.05. These choices are appropriate because of the following reasons:
1. Ratings is numeric and ordinal, which means taking differences/averages is a meaningful.
2. T-test relies on means, so a numeric variable is necessary
3. Compared to a standard t-test, Welch's t-test does not assume equal variances between groups, which is necessary when using real user-generated data
4. A significance value of 0.05 is standard

After running the test, I got the following statistics:
- Average sweet rating: 4.658320292123109
- Average savory rating: 4.686966006180694
- t-statistic: 2.752254802962745
- p-value 0.0029621322519279556

With a p-value of 0.00296, we reject the null hypothesis and conclude that savory recipes do have a higher rating on average. Because our ratings are skewed so high, this means there's only a small difference between the average ratings of the two groups (4.658 vs 4.687). Despite this, our results are statiscally significant. With a larger sample size, the difference between ratings would likely increase.

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
- Observed difference in MSE between the 2 groups: -0.018981
- Observed difference in RMSE between the two groups: -0.026868
- P-value: 0.0070

Because the resulting p-value (0.007) is less than our significance level, I reject the null hypothesis. There is a significant difference between how my final model treats the two groups, meaning that the model is unfair.


