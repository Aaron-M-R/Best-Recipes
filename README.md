# Best Recipes

### Introduction

Cooking recipes have been around for thousands of years, passed down from generations, and an important part of all cultures worldwide. Almost a quarter into the 21st century, there are so many recipes on the internet it's now impossible to decide what to use. Without the famous chefs' cookbooks of the past, most people are left to their own devices to investigate a recipe before trying it. **But how do they know which recipes to trust?**

Ratings are clearly designed to indicate quality of a recipe on most websites, but that doesn't mean there aren't any other important factors. People often use things like the number of steps, cooktime, and title of a recipe to gauge the quality of a recipe as well. While the quality of a recipe is subjective, we want to investigate the relationship between these different methods of detecting quality. Specifically, assuming ratings are a trusted measure of public approval, we would like to know if recipes that claim to be high quality using their title, are also enjoyed by the public more often. Is a recipe called 'Best Chocolate Cake Ever' likely to have a better rating than a recipe just called 'Chocolate Cake'?

Using datasets sourced from <a href="https://www.food.com/">food.com</a>, we will investigate how indications of high quality (IHQ) in a recipe title, affects the ratings. There are two datasets: one for recipes and one for reviews. The recipes dataset has a row for every recipe (83,782 recipes) posted between 2008 and 2018. There's information such as recipe name, ID, preparation time, contributor ID, submission date, tags, nutrition information, number of steps, steps text, and description for every recipe. The ratings dataset has a row for every comment under a recipe (731,927 comments) from 2008 to 2018 and contains user ID, recipe ID, date of interaction, rating, and review text.

We're interested in classifying recipes containing IHQ in their titles to determine if this has a correlation with recipe ratings. Within this investigation we hope to discover some kind of relationship between IHQ and other recipe features. That is, does including superlative and self-praising language in a recipe title tell you anything about the recipe itself? In order to research this question and the more general relationship surrounding it, we'll first take a look at the ratings. Once we understand the distribution of ratings and its relationship with other features such as number of steps and ingredients, cooktime and nutrition, we can compare how IHQ fits into the equation.

### Cleaning and Exploratory Data Analysis

##### Data Wrangling
We first clean the data and extract the relevant information. This involves merging the recipe dataset with the rating dataset (left merge) so that no rating/comment is without a recipe. Then, we rename a few columns to clarify their meaning. Next, we have to deal with comments that give a rating of zero. This is actually not technically a rating, but just a comment. Ratings are on a scale of 1 to 5, so 0 represents absence of a rating, and thus shouldn't be counted as a rating of 0 out of 5, but should be counted as missing (NaN). Now we can calculate the average rating of each recipe. This operation creates duplicate columns, which we immediately remove. Let's take a look at our dataset so far.

##### Data Extraction
Now that we have our single DataFrame, let's make the features of interest easily accessible. First, notice that the dates in the date column are strings, which is tedious to perform operations on. Instead, we convert all of the dates from string to datetime which makes them much easier to deal with later. Second, we also notice that columns like tags, nutrition, steps and ingredients look like lists, but are actually strings. This is also quite annoying to use in data analysis, so we convert those strings to lists in order to access their items easier. Since we know we'll want to investigate nutrition later, we separate each item in the list of nutrition facts into its own column, where each value is a float. Let's look at what the nutrition columns look like.

<iframe src="plots/ratings_histogram.html" width=800 height=600 frameBorder=0></iframe>

<iframe src="plots/minutes_histogram.html" width=800 height=600 frameBorder=0></iframe>

<iframe src="plots/scatter_nsteps_rating.html" width=800 height=600 frameBorder=0></iframe>

<iframe src="plots/scatter_calories_rating.html.html" width=800 height=600 frameBorder=0></iframe>

There are 2 columns with more than 1 missing value, and the only column that's missing more than 100 values is the rating column. The rows that are missing rating are recipes that never got a comment/rating. Although we could easily argue that rating missingness is dependent on the number of comments (missing at random), we want to investigate further. Are there other features in our dataset that could influence the missingness of ratings? Specifically, is rating missingness dependent on nutrition? Perhaps recipes that are designed based on certain nutrition are less popular. This is reasonable since people often cook without thinking about nutrition facts like protein. Let's see if missingness of the recipe ratings depends on the percent daily value of protein in a recipe.

In order to test this theory, we will run a permutation test with 10,000 trials and a p-value of .05. If less than 5% of the trials in the permutation test contain test statistics equal to or greater than the observed test statistic, we can reject the null hypothesis.

##### Rating NMAR Test on Protein

Null hypothesis: the distribution of protein per recipe <u>without</u> a rating is the **same** as the distribution of the protein per recipe <u>with</u> a rating <br>

Alternative hypothesis: the distribution of protein per recipe <u>without</u> a rating is the **different** from the distribution of the protein per recipe <u>with</u> a rating <br>

Test statistic: the absolute difference between average protein per recipe including rating and protein per recipe missing rating.

Result: Our test yielded a p-value of .202, with which we fail to reject the null hypothesis. This means that we don't have sufficient evidence to dispute that rating missingness depends on protein in a recipe. Maybe rating missingness depends on a different nutrition fact. Since protein varies a lot less than ingredients like sugar in most recipes, this could've made it difficult to prove anything statistically significant. Now, let's run the same permutation test only with sugar (PDV) per recipe as our independent feature. 

##### Rating NMAR Test on Sugar

Null hypothesis: the distribution of sugar per recipe <u>without</u> a rating is the **same** as the distribution of the sugar per recipe <u>with</u> a rating <br>

Alternative hypothesis: the distribution of sugar per recipe <u>without</u> a rating is the **different** from the distribution of the sugar per recipe <u>with</u> a rating <br>

Test statistic: the absolute difference between average sugar per recipe including rating and sugar per recipe missing rating.

Result: This permutation test returned a p-value of approximately 0.0, which is certainly below our threshold of 0.05 and thus small enough to reject the null hypothesis. We can now infer that whether or not a recipe has a rating (rating missingness) is dependent on the percent daily value of sugar in the recipe. 











