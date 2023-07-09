#  Pipeline
## From ML Model to ML Pipeline

Building machine learning model is not only about choosing the right algorithm and tuning its hyperparameters. Significant amount of time is spent wrangling data and feature engineering before model experimentation begins

# 📦 0. Setup

Let’s import libraries and a sample data: a subset of the [titanic dataset](https://github.com/mwaskom/seaborn-data/blob/master/titanic.csv) (_the data is available through Seaborn under the BSD-3 licence_).

![](https://miro.medium.com/max/1222/0*IGy4qWra-0-mx51C.png)

We will now define commonly used variables to easily reference later on:

![](https://miro.medium.com/max/998/0*CjL9shFjpnd8ET9n.png)

It’s time to look at the first approach.

# ❌ 1. Wrong approach

It’s not uncommon to use pandas methods like this when preprocessing:

![](https://miro.medium.com/max/1186/0*9fr9Zk_J1NGMqlUm.png)

partial output only

We imputed missing values, scaled numerical variables between 0 to 1 and one-hot-encoded categorical variables. After preprocessing, the data is partitioned and a model is fitted:

![](https://miro.medium.com/max/434/0*XWCJW2LiOKoo1SlD.png)

Okay, let’s analyse what was wrong with this approach:  
◼️ **Imputation:** Numerical variables should be imputed with a mean from the training data instead of the entire data.  
◼️ **Scaling:** Min and max should be calculated from the training data.  
◼️ **Encoding:** Categories should be inferred from the training data. In addition, even if the data is partitioned prior to preprocessing, one-hot-encoding with `pd.get_dummies(X_train)` and `pd.get_dummies(X_test)` can result in inconsistent training and test data (i.e. the columns may vary depending on the categories in both datasets). Therefore, `pd.get_dummies()` should not be used for one-hot-encoding when preparing data for a model.

> **💡** Test data should be set aside prior to preprocessing. Any statistics such as mean, min and max used for preprocessing should be derived from the training data. Otherwise, there will be a data leakage problem.

Now, let’s asses the model. We will use ROC-AUC to evaluate the model. We will create a function that calculates ROC-AUC since it will be useful for evaluating the subsequent approaches:

![](https://miro.medium.com/max/436/0*_d6jp5eTefUckSMt.png)

# ❔ 2. Correct approach but …

We will partition the data first and prepreprocess data using Scikit-learn’s transformers to prevent data leakage by preprocessing correctly:

![](https://miro.medium.com/max/1236/0*iSQLKXBGmhTGn99A.png)

partial output only

Lovely, we can fit the model now:

![](https://miro.medium.com/max/434/0*iohkplIbmBORcOgp.png)

We need to preprocess the test dataset in the same way before evaluating:

![](https://miro.medium.com/max/424/0*GVdNgn6tqoqkRuMV.png)

Awesome, this time the approach was correct. But writing good code doesn’t stop at being correct. For each preprocessing step, we stored interim outputs for both training and test datasets. When the number of preprocessing steps increase, this will soon become very tedious to keep up and therefore prone to error like missing a step in preprocessing the test data. This code can be made more organised, streamlined and readable. That’s what we will do in the next sections.

# ✔️ 3. Elegant approach #1

Let’s streamline the previous code using Scikit-learn’s `Pipeline` and `ColumnTransformer`. If you aren’t familiar with them, [this post](https://towardsdatascience.com/pipeline-columntransformer-and-featureunion-explained-f5491f815f) explains them concisely.

![](https://miro.medium.com/max/706/0*QdlWg2MnZgqDrgrC.png)

The pipeline:  
◼️ Splits input data into numerical and categorical groups  
◼️ Preprocesses both groups in parallel  
◼️ Concatenates the preprocessed data from both groups  
◼️ Passes the preprocessed data into the model

When raw data is passed to the trained pipeline, it will preprocess and make a prediction. This means we no longer have to store interim results for both training and test dataset. Scoring unseen data is as simple as `pipe.predict()`. That’s very elegant, isn’t it? Now, let’s evaluate the performance of the model:

![](https://miro.medium.com/max/414/0*V77wFe-RYVNl1glq.png)

Great to see that it matches the performance of previous approach since the transformation was exactly the same but just written in a more elegant way. For our small example, this is the best approach among the four approaches shown in this post.

Scikit-learn’s out-of-the-box transformers such as `OneHotEncoder` and `SimpleImputer` are fast and efficient. However, these prebuilt transformers may not always fulfill our unique preprocessing needs. In those cases, being familiar with the next approach gives us more control over bespoke ways of preprocessing.

# ✔️ 4. Elegant approach #2

In this approach, we will create custom transformers with Scikit-learn. Seeing how the same preprocessing steps we have familiarised translated into custom transformers hopefully will help you grasp the main idea of constructing them. If you are interested in example use-cases of custom transformers, check out [this GitHub repository](https://github.com/zluvsand/ml_pipeline).

![](https://miro.medium.com/max/410/0*MKd0yHKJkYPfSiES.png)

Unlike before, the steps are done sequentially one after another each step passing its output to the next step as an input. It’s time to evaluate the model:

![](https://miro.medium.com/max/414/0*rDLVJAtROuQ-CVad.png)

Yay, we just learned another elegant way to achieve same result as before. While we only exclusively used prebuilt transformers in third approach and exclusively used custom transformers in the fourth approach, they can be used together provided that the custom transformers are defined to work coherently with the out-of-the-box transformers.

That was it for this post! When using the latter 2 approaches, one benefit is that hyperparameter tuning can be done on the entire pipeline rather than only on the model. I hope you have learned practical ways to start using ML pipeline. ✨

![](https://miro.medium.com/max/1400/0*gCMoEuqtq-l73V7t)

Photo by [Michael Dziedzic](https://unsplash.com/@lazycreekimages?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

_Would you like to access more content like this? Medium members get unlimited access to any articles on Medium. If you become a member using_ [_my referral link_](https://zluvsand.medium.com/membership), _a portion of your membership fee will directly go to support me._