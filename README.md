# MSDS498-FakeNewsDetectorUsing URL as Input
This is a Simple Flask web application for fake news detection. Run on Google Cloud Run and then storing the prediction results on Google BigQuery. Google Cloud Platform, BigQuery, Flask, and GitHub.
## The application will
1. The input is read  from the URL entered on the front-end Web App 'Search Box' and the user clicks 'Submit'. 
2. ![image](https://user-images.githubusercontent.com/6859309/120953679-1d036580-c713-11eb-8009-95ed46bfa3c0.png)
Results
![image](https://user-images.githubusercontent.com/6859309/120953747-4a501380-c713-11eb-8006-39fd93c8e762.png)

3.The text context is pre-process, vectorize, and transform to the tf-idf representation format.
4. This leads to the prediction for a fake or real URL link (news link). 
5. The result is updated/nserted as a prediction to the data table stored in BigQuery for further analyisis. This can be used (but not demoed here) as an analytics set. 
6. The results are then displayed to the user. 
# Architecture
![image](https://user-images.githubusercontent.com/6859309/120954919-d4997700-c715-11eb-91fa-20b7d1f3d886.png)

# The the table BigQuery to store predictions 
Table schema
title: The title of the news
content: The text content of the news
model: The name of the model that yielded the prediction
prediction: Prediction results: real or fake
confidence: The prediction’s probability, denoting the model’s confidence in its prediction
url: The URL of the original news article
prediction_date: The date and time of when the prediction was made
## code 
table_id = 'sunny-emissary-293912.fakenewsdeploy.model_predictions'
schema = [
    bigquery.SchemaField("title", "STRING", mode="REQUIRED"),
    bigquery.SchemaField("content", "STRING", mode="REQUIRED"),
    bigquery.SchemaField("model", "STRING", mode="REQUIRED"),
    bigquery.SchemaField("prediction", "STRING", mode="REQUIRED"),
    bigquery.SchemaField("confidence", "FLOAT", mode="REQUIRED"),
    bigquery.SchemaField("url", "STRING", mode="REQUIRED"),
    bigquery.SchemaField("prediction_date", "DATETIME", mode="REQUIRED"),

]
table = bigquery.Table(table_id, schema=schema)
table = client.create_table(table)  # API request
print(
    "Created table {}.{}.{}".format(table.project, table.dataset_id, table.table_id)
# The Data set
The data set comes from a Kaggle data set  https://www.kaggle.com/clmentbisaillon/fake-and-real-news-dataset 

# Upload the Dataset
Upload to the project root folder by GCP python -m src.data.upload_dataset
Adding to the external data located at data/external into a dataframe and split into train/test dataset.

# The model is built from several online/we site/GitHub training ML Prediction Fake News Dector 
Several published models are combined here. 
# Train Final Model
python -m src.models.train_final

Once the type of model and appropriate text preprocessing is defined, the final model is trained, classification reports and confusion matrix is logged to W&B, joblib model is saved to S3 bucket and referenced to W&B.

# Retrain Model
python -m src.retrain.assert_commit_retrain

The retraining script assumes the existence of required infrastructure. Check the full explanation here
# References
To make the model run efficiently, I used code (or borrowed code) from this GitHub account: https://github.com/FelipeAdachi/fake-news-deploy 
