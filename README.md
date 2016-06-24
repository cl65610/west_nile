# West Nile Virus

### By Terry O'Neill and Cody Laminack
The West Nile Virus represents a clear and present threat to public health. Borne primarily by mosquitoes, the disease can move quickly through a community. While most who are infected with this virus only develop minor symptoms (headaches, fever, etc.), an unlucky few may develop much more serious and life-threatening symptoms menengitis and encephalitis (a swelling of the brain and spinal cord respectively). 

Prevention is the best measure in the fight against West Nile Virus. That means eradicating when possible as many infection vectors as possible. Working with a city the size of Chicago, local public health officials have neither the time nor the resources to spray discriminately across the city. Data science can play a critical role in this fight, by preemptively targeting areas where mosquito populations are likely to bloom, city health officials can maximize their chances of fighting this life-threatening virus.


Please feel free to track our progress on our [Trello Board](https://trello.com/b/OHzR6t4k).

More detailed information about the goals of our project can be found [here](https://github.com/cl65610/west_nile/blob/master/research/Scientific_Method.ipynb).

## Feature Engineering
The data provided by Kaggle was clean and clearly labeled. We decided to focus on preprocessing efforts on two main goals: reducing the feature space of the weather data through principal component analysis and finding a way to incorporate the data provided on Chicago's efforts to reduce the mosquito population by spraying insecticide in certain locations. Those efforts are detailed below.

### Principal Component Analysis
Our PCA was relatively straightforward. We applied this procedure to the weather data provided by the competition. The decision to do this was goverened by the fact that much of the weather data was highly correlational. PCA was a useful way to capture the signal of that weather data while reducing the feature space and, subsequently, its complexity.

### Incorporating Spray Data
The largest challenge we faced in our data mining process was the inclusion of the spray data. The city of Chicago provides information about when and where a location was sprayed, but there's nothing included about the effects of those sprayings. Moreover, the locations of sprays don't neatly match up with trap locations, and sprayings don't necessarily occur at set times relative to when mosquito traps were checked. In response to these challenges, we decided to group the city into different "spray zones", and then assign mosquito traps to their appropriate spray zones. We felt comfortable with this process because accurately predicting the results of a spraying wasn't something our team had the time or resources to take on. Grouping the city's mosquito traps into spray zones allowed us to capture some of the effects of spraying into the models that we built. 

The process of creating these spray zones required three steps:
  1. We ran a DBSCAN clustering on the spray locations. That created 13 distinct "spray zones."
  2. This cluster information became a feature of our spray data, and we could therefore set it as a target for modelling. That model would become the model used to predict which spray zone a mosquito trap fell in. We ran several models on this data, and our most accurate model as an Extra Trees Classifier. Extra trees models are notoriously prone to overfitting, but we felt comfortable with this because we were dealing with latitude and longitude data and our out of sample data would be very similar to our training data.
  3. The extra trees model that performed best was used to predict which spray zone a mosquiot trap fell into. The final results of this can be seen in the image below. The mosquito traps are morked by colored 'X's, and the spray zones are the different-colored clusters on the map. 
![DBSCAN Image](https://raw.githubusercontent.com/cl65610/west_nile/master/assets/visualizations/spray_zones_plus_labels.png "Spray Zones with Traps Labeled")
After these steps were completed, and after other miscellaneous cleanings, the data was ready for modeling. The results of which are described in the next section.

## Results 
Given the relatively low number of positive West Nile Tests, a simple accuracy score was an insufficient measure for judging a model. Therefore, we optimized all of our models toward their AUC scores. The model that performed the best on our training data was an Extra Trees classifier. That model had an AUC score of 0.99. After some initial excitement, we realized just how overfit this model was to our training data. And since our validation set comprised years for which we had no information, this overfitting would certainly hurt our final score. We had other models that didn't perform quite as well on our data, specifically because they were a little more skeptical when it came to diagnosing West Nile. We chose to go with Voting Classifier that combined weighted the predictions of many different models in an attempt to reach a consensus between more and less skeptical models. A graph showing our training ROC curves and AUC scores can be found below, and a more detailed write-up of our modeling can be found [here](https://github.com/cl65610/west_nile/blob/master/models/final_model_creation.ipynb).

![ROC Scores](https://raw.githubusercontent.com/cl65610/west_nile/master/assets/visualizations/roc_curve_compilation.png "ROC curves and AUC Scores")

The Voting classifier we used as a final predictor was submitted to Kaggle after the competition closed, and received 0.716 as an accuracy score. This would have placed us in 633rd place out of approximately 1300 scores. This score was also the best in our class by a significant margin.
