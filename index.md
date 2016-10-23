# Predicting Exercise Effectiveness with Fitness Device Data and Machine Learning
David R Scott  
October 23, 2016  
**NOTE FOR GRADERS:** 
  The complied .html version can be viewed here:
  <https://belacqua.github.io/practicalmachinelearning/>
  
  And for reference, the path that brought you to the current file in this     repository, which contains the index.Rmd, index.md., and index.html files
  which also contain this report and the code and outcome of this project, is   here:
  <https://github.com/Belacqua/practicalmachinelearning>.
  
  
**Background**

Using devices such as Jawbone Up, Nike FuelBand, and Fitbit; it is now possible to collect a large amount of data about personal activity relatively inexpensively. These type of devices are part of the quantified self movement - a group of enthusiasts who take measurements about themselves regularly to improve their health, to find patterns in their behavior, or because they are tech geeks. 

These device users regularly  quantify how much of a particular activity they do, but they rarely quantify how well they do it. In this project, the goal is to use data from accelerometers on the belt, forearm, arm, and dumbell of 6 participants. They were asked to perform barbell lifts correctly and incorrectly in 5 different ways. 


### Aims of the Project  

The goal of your project is to predict the manner and effectiveness in which these device users did the exercise -- the "classe" variable in the training set. The other variables in the device data will be used to predict with.


### Data  

The training data for this project are available here:

<https://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv>

The test data are available here:

<https://d396qusza40orc.cloudfront.net/predmachlearn/pml-testing.csv>


### Process, Results  

The process and results of the project include the following items, which are detailed, in order, in the remainder of this report:

* Understanding the prediction question. 
      + The question in this project is, can we identify the when the participants perform barbell        lifts correctly and incorrectly in 5 different ways, from the various metrics recorded 
       on their devices. 
      + Also, it would help to identify some of the key factors in such                predictions.
       
* Getting and cleaning the device data. 
      + This includes not only reading it into R as an R object, but also            dealing with preprocessing to account for skewed or missing data,            creation of dummy variables, and otherwise accommodating the data to           the assumptions of the prediction problem and modeling techniques             available.

* Final Feature Selection. 
      + Analyze data for correlation analysis, and pick those which matter.          There are over 150 features available and many  may reflect                  same information, or contain no relevant information. Here is where
        we choose those which seem to have the most effect on the target.

* Algorithm selection, and building and evaluating the models. 
      + Classification trees are created, along with supporting graphs.         
      + But Random Forests are eventually used, for better performance. The          model is then tested         on the test (held out) data.
      
* Interpreting the final model, and making predictions. 
      + A method to estimate the out of sample error is selected and applied
     
      + The model output should allow interpretation of the results in terms         of the model features, so that the results can be meaningful to the          users.
      
      + New predictions are the main result of the whole process, so the test        data is used to make predictions on 20 test cases.

### Getting and cleaning the device data 

**Begin by reading in the CSV file of training and test data.**
Read the data from the local working directory, and looking at the distribution of the target variable 'classe'. 



```
## [1] A
## Levels: A B C D E
```

```
## 
##    A    B    C    D    E 
## 5580 3797 3422 3216 3607
```

![](index_files/figure-html/unnamed-chunk-1-1.png)<!-- -->

Note that while 'A' is the mode of the class levels, it represents much less than half the results. So a predictive model is required to get some traction, since guessing 'A' would usually be wrong.

Next, we pre-process the data.

This step includes looking at the structure of the data, using this information for finding and conversion of factor variables to numeric, as well as dropping useless fields, imputing missing values with their nearest neighbor via the k-nearest neighbor algorithm, and creating dummy variables for the 5 levels of the 'classe' variable.

We also center and scale the numeric variables.


### Final Feature Selection.

* Next, after creating dummy variables for the levels of 'classe', we build 
a correlation table to discover which of the newly transformed features are most closely related to the 'classe' dummy variables.

* Each variable is ranked by the absolute value of its correlation to each of the target dummy variables.



* The results are collated and used as the final model data set.

### Algorithm selection, and building and evaluating the models. 

The first algorithm chosen is a decision tree model. This model is built on the final training data set from the last step above, and evaluated for it's accuracy (SEE TREE CHART BELOW):
![](index_files/figure-html/unnamed-chunk-6-1.png)<!-- -->

While there is clearly some improvement over random guessing with just a few learned classification rules which the model generated, note that the decision tree predicted classes are only correct about half the time, even in the training data: 

(0.98 x 0.09)+(0.33 x 0.54)+(0.59 x 0.09)+(0.57 x 0.09)+(0.55 x 0.2)= **48.1%** 

Part of the problem in the decision tree approach was the lack of any cross-validation in the use of the training data. 

But since this model did not perform well at all, the next step is to improve this modeling approach with a **Random Forest model,** which will create many boostrapped sub-samples of the training data. In addition, this approach will try many subsets of all possible trees, and internally select the 'champion' of all these hidden alternative models. 

As seen below, the random forest results are far more promising than the decision tree results, with an accuracy of over 87% in the training data: 


```
## Random Forest 
## 
## 1887 samples
##   38 predictor
##    5 classes: 'A', 'B', 'C', 'D', 'E' 
## 
## No pre-processing
## Resampling: Bootstrapped (25 reps) 
## Summary of sample sizes: 1887, 1887, 1887, 1887, 1887, 1887, ... 
## Resampling results across tuning parameters:
## 
##   mtry  Accuracy   Kappa    
##    2    0.7929248  0.7381742
##   20    0.8637568  0.8277481
##   38    0.8760338  0.8433584
## 
## Accuracy was used to select the optimal model using  the largest value.
## The final value used for the model was mtry = 38.
```

### Interpreting the final model, and making predictions. 

Note above that in addition to the the random forest model's 87% training data accuracy in its final iteration, it also has a Kappa coefficient of over .84. This measures the degree that the categories are correct *above what would be expected with random luck*, on a scale of roughly 0 to 1, and is recommended as a method to evaluate non-binary classification predictions.

**Interpreting the 0.84 Magnitude of Kappa**

Landis and Koch have proposed the following as standards for strength of agreement for the kappa coefficient: 

(zero or less) = poor

   .01 –  .20  = slight

   .21 –  .40  = fair

   .41 –  .60  = moderate

   .61 –  .80  = substantial, and 

   .81 – 1.0   = almost perfect. 

Based on this benchmark, the model has performed almost perfectly at disintguishing classes beyond expected random luck. The choice of such benchmarks as 'poor' or 'almost perfect', however, is inevitably arbitrary,and the effects of prevalence and bias on kappa must be considered when judging its magnitude. *(see* <http://ptjournal.apta.org/content/85/3/257> *for details on how to interpret the Kappa coefficient.)*

While the hold-out (test) data is not in general likely to be as accurate as our 87% training data result each time the model is applied, this model can be expected to correctly classify approximately 80% of all cases. And in our application of the model in the project quiz to th 20 test observations, the model was accurate on 18/20 cases (90%)

(see <http://ptjournal.apta.org/content/85/3/257> for details on how to interpret the Kappa coefficient.) 

### Conclusions

The model shows that accuracy is greatly improved when methods of cross-validation are used. In particular, the random forest allows for thorough cross-validation with the training data, increasing the accuracy of the model significantly over that of the decision tree model w/o cross-validation.

