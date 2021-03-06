# radical-shapley-values
Python code to directly compute "radical" Shapley values (EDIT: I later decided to change this name to the more apt "Naive" Shapley values, however this is not yet reflected in this repository) for model features, by re-training the model on a subset of features on each iteration.

See the [blog post on Towards Data Science](https://towardsdatascience.com/a-new-perspective-on-shapley-values-the-radical-shapley-method-6c2f4af7f922) (or [on my blog](https://edden-gerber.github.io/shapley-part-2/)) for an explanation of this method.

**radical_shapely_values.py** contains the main _compute_shapley_values_ function.

## Inputs to compute_shapley_values:
**payoff function**: any function that takes a dataset and returns a score (for instance, the profit generated by a team of workers). It is thus a general function that can be used for any kind of Shapley computation, but for the purpose of generating radical Shapley values it will always be a function that trains a particular type of model on the dataset, and returns a prediction for each row. This means that while we specify the model parameters in advance within the function (e.g. number of trees in a random forest), the model is re-trained each time on a dataset containing a subset of features supplied by the Shapley function.

**X**: A sample x feature (or game x player) dataset.

**y**: True labels for the X dataset for model training (optional)

**zero_payoff**: Value (or values) to be used as a payoff when the feature coalition is empty. Should be the same format as the output of the payoff function. Since a model cannot be trained with zero features, this payoff needs to be defined explicitly. This value can be zero by default, which means that the contribution of all features will add up to the exact value of the prediction. Alternatively, this could be set to the mean prediction value, which would result in feature contributions being relative to this mean (given that knowing nothing we would assume the mean prediction, each additional known feature will move this prediction up or down) - which is what we get when we use the SHAP library explainers.

## Output of compute_shapley_values:
**shapley_values**: a dictionary that gives the shapley value for each game/sample for each given feature/player. Use the _reshape_shapley_output_ function to rehsape this output into a sample x features numpy array comparable with the output of the SHAP library. All the visualization tools of the SHAP library can be used on this output.


## Demo script
The demo script demonstrates how to use the function and how to compare it to the SHAP library results.

The complexity of this function is exponential with the number of features, and so the demonstration uses a random dataset with a very log number of features (M = 4, N = 10^5).

The difference (and correlation) between the radical Shapley values and SHAP KernelExplainer values can be seen in this scatter plot:

![scatter](scatter-randdata.png)

(Why are the dots not on the line? ...You'll need to read the [post](https://edden-gerber.github.io/shapley-part-2/) to get what's going on). 
