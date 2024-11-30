<script src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js"></script>
# Macro-Regressor

## Introduction
The Macro-Regressor is a Data Sience project conducting exploratory and predictive analysis on two datasets from [Food.com](https://www.food.com/). The Datasets consist of reciples, each uniquely indentified by their Reciple ID. Hence, the Recipes were merged on the recipe id to form the **Recipe Dataframe** we will be exploring.
#### Recipe Dataframe
The Recipe Dataframe originally consists of 234429 rows and 15 columns.

|         Column   | Description                                             |
|:-----------------|:--------------------------------------------------------|
| `name`           | The name of the recipe.                                 |
| `id`             | A unique identifier for the recipe.                     |
| `minutes`        | The total time required to prepare the recipe.          |
| `contributor_id` | The ID of the user who submitted the recipe.            |
| `submitted`      | The date when the recipe was submitted.                 |
| `tags`           | List of associated tags describing the recipe.                  |
| `nutrition`      | Nutritional information for the recipe. This is list which contains [calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)] where PDV is the % daily value.                 |
| `n_steps`        | The number of steps in the recipe.                      |
| `steps`          | Instructions detailing the steps to prepare the recipe. |
| `description`    | A brief overview or description of the recipe.          |
| `ingredients`    | List of ingredients required for the recipe.            |
| `n_ingredients`  | The number of ingredients in the recipe.                |
| `user_id`        | The ID of the user interacting with the recipe.         |
| `date`           | The date related to a specific event or interaction.    |
| `rating`         | The user rating for the recipe.                         |

This dataframe provides several insights on a topic which unites us all: food! Lot's can be drawn from the Dataframe, however, there's a pressing matter at hand; according to [CBS news](https://www.cbsnews.com/news/obesity-rate-us-adults-cdc-data-map/#:~:text=Now%20the%20CDC%20estimates%20that,virtually%20every%20year%20since%202011.), obesity rates in the U.S. exceed **40%** as of 2024. Hence, understanding our food's nutritional value is key to building a healthier nation. The focus of this project will be on nutrition.

## Data Cleaning and Exploratory Data Analysis
The following steps were taken to clean the Dataframe: <br>

1) Transforming each of the nutritional columns into appropriate **Macros**. I.e, `fat`, `sugar`, `sodium`, `protein`, `saturated fat`, `carbohydrates` in grams rather than PDV. This is because the reccomended PDV differs from person to person. To do this, the Food.com uses the following PDVs as reccomended by the FDA: 

|   Macro       |  PDV |
|:--------------|-----:|
| fat           |   78 |
| sugar         |   50 |
| sodium        | 2300 |
| protein       |   50 |
| saturated fat |   20 |
| carbohydrates |  275 |

2) The `calories` column was often times severely inaccurate. The following formula was used to recalculate the calories columns
<br>$$\hspace{15em}\text{calories} = \text{fat} \cdot 9 +\text{protein} \cdot 4 + \text{carbohydrates} \cdot 4$$<br> where the units for all of the macros are grams.

3) The `ingredeints` and `tags` columns were transformed into lists rather than strings of the list. <br>

4) Finally, no imputation was needed. None of the aforementioned columns had `NaN` or missing values.  <br>

Here's a sample of the resulting Dataframe



| name                           |   calories |   sugar |   sodium |   protein |   carbohydrates |    fat |   saturated fat |   n_ingredients |   n_steps | ingredients                                                          | tags                                                                      |
|:-------------------------------|:-----------|:--------|---------:|----------:|----------------:|-------:|----------------:|----------------:|----------:|---------------------------------------------------------------------:|--------------------------------------------------------------------------:|
| dirty sriracha bloody mary     |      24    |     9   |      299 |       0.5 |             5.5 |   0    |             0   |               8 |         5 | ['celery salt', 'fresh lemon juice', 'horseradish', 'olive juice', 'sriracha sauce', 'tomato juice', 'vodka', 'worcestershire sauce']     | ['15-minutes-or-less', '3-steps-or-less', 'beverages', 'cocktails', 'course', 'easy', 'for-1-or-2', 'main-ingredient', 'number-of-servings', 'preparation', 'time-to-make', 'tomatoes', 'vegetables']                                  |
| rolachi                        |     451.74 |    31   |      828 |      26   |            22   |  28.86 |             7.6 |               8 |        10 | ['green peppers', 'ground beef', 'onion', 'pepper', 'salad oil', 'salt', 'stewed tomatoes', 'zucchini']                                       | ['3-steps-or-less', '30-minutes-or-less', 'beef', 'easy', 'ground-beef', 'main-ingredient', 'meat', 'preparation', 'time-to-make']                   |
| peanut butter truffle cupcakes |    5060.14 |   538.5 |     2898 |      89   |           374   | 356.46 |           157.8 |              14 |        20 | ['baking cocoa', 'baking soda', 'brewed coffee', 'butter', 'buttermilk', 'creamy peanut butter', 'eggs', 'flour', 'heavy whipping cream', 'salt', 'semisweet chocolate', 'sugar', 'vanilla', 'white baking chocolate']   | ['60-minutes-or-less', 'baking', 'cake-fillings-and-frostings', 'cakes', 'course', 'cupcakes', 'desserts', 'equipment', 'oven', 'preparation', 'time-to-make']    |

**Note** While there were more columns (`reviews`, `descriptions`, etc.), these were not used for the rest of the analysis and were disgarded.

### Univariate Analysis
Now let's look at the distributions of some of the plots.

**Note** There were `2000` bins used for the following macro distributions in order to be able to view the distributions. <br>
Here's the distribution of the calories. It's got a unique distribution but is still monotone decreasing for the most part. Dwelling deeper into it's compoenents (see formula above) leads to a more firm understanding of this distribution.
<iframe
  src="assets/dist-calories.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
Looking at the distribution of protein, we see a distribution which almost resembels a exponential distribution, just with a discerete variable. But then why doesn't the calorie distribution also resemble this shape?
<iframe
  src="assets/dist-protein.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
The distribution of the fat macro in grams play a big part. As seen above, it's more common for recipes to have more fat. Additionally, this isn't monotone decreasing and certain amounts of fat seem to be more common than others.
<iframe
  src="assets/dist-fat.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The distribution of the other numerical columns resembeled a normal distribution. Interestingly enough, while the variance in the number of steps is slightly greater, they both share the same mean.
Here's the distribution of the number of ingredients.
<iframe
  src="assets/dist-n_ing.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Here's the distribution of the number of steps.
<iframe
  src="assets/dist-n_steps.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>


### Bivariate Analysis
Now it's time to see how some of these features may be correlated. <br>
Perhaps the most obvious correlations which come to mind are those related to specific macros and calories. As you observe the following correlations, you're encouraged to view the $$R^2$$ and $$R$$ score to get an idea of which macros are most closely tied to the overall calories.
<iframe
  src="assets/bi-calories-carbohydrates.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
<iframe
  src="assets/bi-calories-fat.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
<iframe
  src="assets/bi-calories-protein.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
The trends for sugar, sodium, and saturated fats, were simlar. All of them had a strong, positive, linear relationship with the calories macro with few outliers.

Interestingly, as the number of ingredients increase, the amount of time taken to make the recipes in minutes tends to decrease, though the correlation is not very strong. This may not be as nutritionally relavant, but is interesting nonetheless. As expected, however, we see the opposite trend in minutes vs the number of steps as the recipes requiring more steps tend to take longer as well.
<iframe
  src="assets/bi-min-n_ing.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Interesting Aggregates
To undestand the relavance of the aggregates, you must be familiarized about details of the `ingredients` and `tag`features of the recipe dataframe.

Here's a few of the most popular tags and the proportion of recipes which had them. 
<iframe
  src="assets/popular-ings.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Similarly, here's some of the popular tags!

<iframe
  src="assets/popular-tags.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Feel free to refer to these as you go through the rest of the analysis and creating the model. These provides key insights on nutrition.

#### Recipe Composition and Ingredients
Here's the recipe composision of recipes with and without the `butter` ingredient. Observe how the macros are impacted.

|   Butter Ingredient |   calories |   carbohydrates |   protein |   fat |   sugar |
|----------:|-----------:|----------------:|----------:|------:|--------:|
|         False |       41.7 |            22   |      10   | 13.26 |    10   |
|         True |       49.1 |            27.5 |       7.5 | 19.5  |    13.5 |

More, interestingly, let's visualize this:
<iframe
  src="assets/macro-compo-butter-ing.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Here's the impact of adding `eggs` to the recipes. 

|   Eggs Ingredient |   calories |   carbohydrates |   protein |   fat |   sugar |
|----------:|-----------:|----------------:|----------:|------:|--------:|
|         False |       42.9 |            22   |       9   | 14.82 |    10.5 |
|         True |       49.1 |            27.5 |       8.5 | 19.5  |    20.5 |

<iframe
  src="assets/macro-compo-eggs-ing.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>





#### Recipe Composition and Tags
A similar trend can be ovserved with tags.
Here's the recipes with the `meat` tag.

|   Meat Tag |   calories |   carbohydrates |   protein |   fat |   sugar |
|----------:|-----------:|----------------:|----------:|------:|--------:|
|         False |       41.4 |           24.75 |         6 | 12.48 |    12   |
|         True |       53   |           19.25 |        29 | 25.74 |     8.5 |

<iframe
  src="assets/macro-compo-meat-tag.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Adding an opposite effect, here's the recipes with and without the `vegetable` tag.

|   Vegtable Tag |   calories |   carbohydrates |   protein |   fat |   sugar |
|----------:|-----------:|----------------:|----------:|------:|--------:|
|         False |       44.7 |           24.75 |       9.5 | 15.6  |    12.5 |
|         True |       41.4 |           19.25 |       8   | 14.04 |     9   |

<iframe
  src="assets/macro-compo-veg-tag.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

## Framing a Prediction Problem
These recipes seem to provide in depth nutritional explanations. However, what if this weren't the case. <br>
One may one want to know the nutritional information of their recipes, but not have adequete information (calories, protein, fat, arbohydrates, sodium, sugar, and saturated fat). In a realistic setting, we'd only know a few things: how  long the recipe takes, the # of ingredeints associated with the recipe, what the ingredeints are, and finally any tags you would associate with the recipe (the vibe of the recipe, if you will).

Hence, we will greate a **Regressor** for each of the aforementioned macros. <br>
Here's a few specifications on the regressor in addition to why we choose them. <br>
 - We will use a **Linear Regression** model for the regressor. Due to it's parametric nature, we will later be able to interprate the importance of certain features in the model. This can also provide key insights on certain ingredeints, tags, and trends based on the # of steps etc.
 - We will **Regularize** the model. In this way, we will be using **Lasso Regression**  for the model. The reason for Lasso is, again, intepretability. Lasso, standing for *Least Absolute Shrinkage and Selection* means that our trained model will only contain the parameters which actually impact our predictions.
 - Lastly, since we are predicting each macro, we will be creating a class whoose API consists of being able to select which Macro you'd like to see predicted.

## Baseline Model
In the baseline model, we will test this theory. It will be a more simple model. One aimed a solely predicting protein based on the presense of the meat tag, number of steps, number of `ingredeints`, and `eggs` (nominal and the only categorical). Later, we will add additional features and transform the numerical features to better match the models.
The model achieved the following accuracies:

|:----------|--------:|
| Train MSE | 441.738 |
| Test MSE  | 604.551 |

While this model did attain a decently low MSE, there's a few problems with the model. Firstly, it's only trained for protein. Encoding the ingredients must be done one at a time. In this case, the eggs ingredient was binary encoded, I.e., each recipe either contained eggs somewhere along it's recipe list or didn't. This ingredient alone wouldn't be effective in classifying the other macros. Additionally, ingredients and tags whose proportions are too small will not be present enough for the model to learn from the recipes which contain them. Hence, it's important to choose the right ingredients. This is also why using Lasso is helpful. Due to it's shrinkage property, the nessessary ingredeints and tags will be filtered for each of the macros.
Secondly, the `n_ingredients` and `n_steps` features don't have a high $$R^2$$ value with protein. This means it's highly likely that their relation to the protein macro isn't linear. Hence, we can use sklearn's *PolynomialFeatures* to determine the right relationship between these numerical features and macros.
## Final Model
In the final model, we will make use of sklearn's **GridSearchCV** class. This way, we will **cross validate** while training to reduce overfitting, allowing our training MSE to more closely match our testing MSE. Additionally, we'll also experiment with different hyperparams such as the punishment coefficient for Lasso and the polynomial features for each of the numerical features. Aside from that, we'll be adding the top $$20$$ most popular tags and ingredients. After the top $$20@$$, their proportions get too small to be able to adequetly train the Lasso model and would simply introduce noice. Addtionally, while this approach may not gurantee that soley the best features are being used to train each model, different features may be useful for different macros. One of the goals of this project is interpretability. After creating the model, we are able to interpret the parameters to determine which features were of service to which macros.
### Results
The following are the MSE of the test sets.

|    Macro      |     Test MSE |
|:--------------|-----------------:|
| calories      | 418557           |
| fat           |   2144.63        |
| sugar         |  13047.8         |
| sodium        |      1.4756700.1 |
| protein       |    566.39        |
| saturated fat |    204.198       |
| carbohydrates |   5490.71        |

As can be see, there's a significant decrease in the MSE for `protein` from the baseline model. The training MSEs are also lesser with the protein training MSE being $$405.1$$$.

The following are the optimal hyperparams for each of the models.

|                             |   calories |    fat |   sugar |   sodium |   protein |   saturated fat |   carbohydrates |
|:----------------------------|-----------:|-------:|--------:|---------:|----------:|----------------:|----------------:|
| N_Steps Poly Features       |     2      | 2      |  2      |      3   |    1      |          2      |          2      |
| N_Ingredients Poly Features |     2      | 2      |  3      |      2   |    1      |          2      |          2      |
| Lassor regressor            |     0.0625 | 0.0625 |  0.0625 |      0.5 |    0.0625 |          0.0625 |          0.0625 |

As can be seen, the polynomial features most most of the models are greather than 1, suggesting a non-linear relationship between said macros and the numerical features. The optimal punishment tends to be $$0.0625$$ for most of the values. 
**Note** The range of the polynomial features exponent ranges from $$1$$ to $$4$$ and $$2^{-5}$$ to $$2^{4}$$ for something the Lasso punishment.
#### Interpreting the Parameters
Now, we are able to interpret the impact of adding each of the ingredients to the model. The following table is the meat tag coefficient. As expected, it's positive for `protein` and negative for `carbohydrates`. Take a look at the **Interesting Aggregates** section to see why.

|   Macro       |   meat_tag coefficient |
|:--------------|-----------:|
| calories      |  115.549   |
| fat           |    9.74835 |
| sugar         |   -1.04353 |
| sodium        |  251.082   |
| protein       |   11.1625  |
| saturated fat |    2.35439 |
| carbohydrates |   -4.08609 |

Now, if we were to specialize towards predicting the values of a certain macro, we can do so by selecting only the columns which provice any significant value.
In the following, the **all_coefficients** dataframe represents the coefficient for each of the macros.<br>`all_coefficients[(all_coefficients['protein'] > 1) | (all_coefficients['protein'] < -1)]['protein']`<br>
This singles out any important ingredient or tag! We get the following series:

|      Feature         |   Protein (Grams) |
|:-----------------------|----------:|
| 15-minutes-or-less_tag |  -3.74627 |
| 30-minutes-or-less_tag |  -2.27869 |
| 60-minutes-or-less_tag |  -2.51325 |
| baking powder_ing      |  -1.00415 |
| baking soda_ing        |  -1.69974 |
| course_tag             |  -5.9061  |
| low-carb_tag           |   4.09764 |
| main-dish_tag          |  10.6352  |
| meat_tag               |  11.1625  |
| n_ingredients          |   1.61722 |
| n_steps                |   1.09601 |
| sugar_ing              |  -2.62982 |
| time-to-make_tag       |  -2.06129 |
| vegetables_tag         |  -5.13664 |

Hence, future Macro regressors can focus solely on one macro at a time and aim to increase the training time using only the features whch are of use. Additional research can be done, interpreting the parameters.

Thank you!