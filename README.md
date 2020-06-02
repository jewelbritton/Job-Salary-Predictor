# Predicting Salaries of Data Jobs 

## Table of Contents
* [Goals](#goals)
* [Data Collection](#data-collection)
* [Feature Engineering](#feature-engineering)
* [Modelling](#modelling)
* [Conclusion](#conclusion)

## Goals

1. Scrape data from Indeed.com 
2. Discover which job features impact salary the most
3. Create a classification model to predict if a job will pay over or under median salary based on the features of the job

## Data Collection

Using Beautiful Soup I scraped job listing information from over 15 cities in the US, UK, and Hungary. This required me to bu a function that would loop through all of the cities and iterate through all web pages and scrape all of the job information. Many listings were posted multiple times or did not provide salary information, so significant data cleaning was required. Salaries for the US given in dollars was converted to pounds, and salaries with a range given (20,000-25,000) were averaged to give one value. Even though there were thousands of postings on Indeed for these cities, once duplicates and postings without salary were deleted, the data diminished dramatically and left me with about 600 postings to base the model on. Ideally there would be more data to analyze, but this is a good amount to start with.

## Feature Engineering

In the inital data scraping, I collected information on the job's title, company, location, description, and salary. Once this information was cleaned, I did some feature engineering to produce additional columns for the modelling. 

**Company Type** <br>
Since many different companies had listings and there would often only be one job per company name, I decided that the individual companies name would not help predict salary. Instead, I grouped companies into categories based on the type of company that posted the job. In this stage I found that a vast majority were posted by recruitment companies, but other categories included healthcare, consulting, law and several others. 


**Job Type** <br>
Similarly, job titles were also very unique and there may only be one listing with the specific title. It was more beneficial to categorize job titles as well. Job titles were split into larger groups based on what the title indicated. For example, job titles of "Junior Data Scientist" and "Data Scientist Health Data - NHS" would both be grouped in the Data Scientist category. This allowed for several broader categories that could be used to help predict salary. 


**Seniority** <br>
Finally, if a job title indicated the seniority of a position it was split into entry or upper level. For instance, "Junior level" or "Graduate Scheme" postions were labeled as entry and "Lead", "Head" or "Manager" positions were marked as upper. This would seem to have a large impact on expected salary since higher seniority is often tied to more experience and therefore higher salary. Many positions did not indicate the type of position in the title, so this was not as good of a predictor as expected in many cases. 

These steps were important because they created broader categories to make predictions on, since it is impossible to make predictions if every listing has unique elements. These categories allow for generalizations of what impact predicted salary. However it also introduces the possibility of error if a job was misplaced in a category.

Some intial EDA visualisations showed trends that jobs in the US pay more than jobs in the UK, and salaries varied more in the USA based on the type of company than in the UK. Also, strategy jobs pay the highest in the UK, while engineer and data scientists jobs pay the highest in the USA. However, in both the UK and USA interns earn the lowest amount.

<img src = "visuals/median salary by location.png">

<img src = "visuals/us vs uk company category salary.png">

<img src = "visuals/usa vs uk job title salary.png">

## Modelling

### Predicting Based ONLY on Location

Once all of the features were compiled, the first models *only used location* as a factor to predict if salary was above or below the median salary of £55,000. Logisitic Regression, Decision Tree, Ada Boost and Bagging Classifier models were run on the location of a job posting to predict salary given only the listing's location. Of these four models, the best was a Decision Tree Classifier which had an accuracy of about 64%. This model was better than the baseline prediction, which is when the salary prediction is made randomly as above or below the median (50/50 chance). 

In the Decision Tree model, the feature importance showed that jobs in New York City, Oxford, London, Brighton and Boston had the highest impact on if a job would have a salary above or below median.

<img src = "visuals/location feature importance.png">

### Predicting on All Features

Since there was many other factors from the listing that could be considered, the models were repeated but used Job Titles, Company Categories, Location, and Job Type into account in order to predict salary. When the models were replicated on all of these features the best model was Logistic Regression, with an accuracy of about 77%. In this new model, the most important features for predicting salary were based on Job Titles and Job Types. For instance, an upper level position with a job title related to math or statistics had a much greater chance of earning more than the median. Since this model performed much better, it can be concluded that a job's salary is impacted more from a combination of type of job, job title and location rather than just location. In order to make more accurate predictions of salary we should take all of these elements into consideration. 

The feature importance for the Logistic Regression showed that job seniority (upper/lower) were strong predictors, as well as job titles that featured math, statistics or engineering. Since none of these features were location based, it can be concluded that job type and title have more influence on job salary than location.

<img src = "visuals/all feature importance.png">

### Model Tuning

The best model so far, Logistic Regression, was performing at about 77% accuracy, which was quite an improvement from the 50% accuracy of randomly selecting above or below median and the 64% accuracy by only taking location into account. However, it still makes incorrect predictions 23% of the time. In this scenerio, it would be better to wrongly predict that someone will earn less than median when they will actually earn more. In this case, even if the prediction is wrong they can be happily surprised that they will make more than expected. In order to shift our model's predictions so that we do not incorrectly tell someone that they will earn more than median, the prediction threshold must increase. Below is a graph that shows the tradeoff between accuracy and threshold. 

<img>

By increasing the threshold from .5 to .85 the model predicts class 1 (having a high salary) less often, and therefore decreases the number of false negatives and increases the precision score for class 1. The original precision was .75 and now the precision is .81. If the threshold was set further to .9 it would have a perfect precision score, but the model would suffer from inaccuracy since the predictions would simply be for class 0 nearly every time. Raising the threshold decreased the model's accuracy a bit since it will incorrectly classify a listing as having a low salary more than before, but in this scenerio it is better for this to be the case. 

### Conclusion

The modelling showed that job salaries are predicted more accurately when more features of the job (location, postion, type of company...) are included. The best model showed that the most important factors for predicting if a job will pay above or below the median salary are the seniority of the position, and if the job title refers to math, statistics or engineering. Tuning the model was also important for this scenerio so that people were rarely told that the job would pay above median if it would actually pay below median.
