# MSDS498-FakeNewsDetectorUsing URL as Input
Simple Flask web application for fake news detection. Run on Google Cloud Run and then storing the prediction results on Google BigQuery.
# 
from google.cloud import bigquery
import os
import datetime
os.environ['GOOGLE_APPLICATION_CREDENTIALS'] = 'creds.json'


client = bigquery.Client()

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
# 
fully traceable ML Experiment: code, train/test data, models and performance metrics, through different stages: preprocessing, grid search optimization and training final models. Google Cloud Platform, BigQuery, Flask, and pipeline and repository GitHub.
https://github.com/FelipeAdachi/fake-news-deploy 


# Uploading Dataset
From the projects root folder:

python -m src.data.upload_dataset

This will get external data located at data/external, properly assemble it into a dataframe and split into train/test dataset.

The x/y train-test splits are then uploaded to an S3 bucket, and logged as artifacts at W&B, using references to the bucket. Metadata is included to link bucket versionIDs to the W&B artifacts.

# Grid Search Optimization
wandb sweep src/models/sweep.yaml

then run sweep agent given, e.g.

wandb agent felipeadachi/fn_experiments/<sweep_id>

This runs a grid search hyperparameter tuning, in order to assess performance for differente sklearn models, such as sdg, svm, random forest and multionomial naive-bayes. An additional parameter related to a text preprocessing step is included, to assess the impact of denoising the text.

# Train Final Model
python -m src.models.train_final

Once the type of model and appropriate text preprocessing is defined, the final model is trained, classification reports and confusion matrix is logged to W&B, joblib model is saved to S3 bucket and referenced to W&B.

# Retrain Model
python -m src.retrain.assert_commit_retrain

The retraining script assumes the existence of required infrastructure. Check the full explanation here
