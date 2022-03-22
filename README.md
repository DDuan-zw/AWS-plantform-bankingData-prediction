# AWS-platform-bankingData-prediction
AWS-SageMaker Studio-Data WareHouse-Pipeline-S3-Athena

This is a personal project for practice only, I learn these techniques from AWS workshop for perosonal improvement. It builds workflow in Sagemaker Studio and deploys a XGboost model as inference. The datasets are stored S3, after prepoccessing by Data Wrangler, transformed into SageMaker Feature Store to store, retrieve, and share machine learning (ML) features(in group working).       
Dataset: https://archive.ics.uci.edu/ml/datasets/bank+marketing

[Moro et al., 2014] S. Moro, P. Cortez and P. Rita. A Data-Driven Approach to Predict the Success of Bank Telemarketing. Decision Support Systems, Elsevier, 62:22-31, June 2014

## AWS Service
AWS provides all kinds of powerful tools that cover amost all scenario in data engineering and machine learning.

* SageMaker Studio is a web-based, integrated development environment (IDE) for machine learning that lets you build, train, debug, deploy, and monitor your machine learning models. Studio provides all the tools you need to take your models from experimentation to production while boosting your productivity.

* S3 offers a range of storage classes that you can choose from based on the data access, resiliency, and cost requirements of your workloads. S3 storage classes are purpose-built to provide the lowest cost storage for different access patterns. S3 storage classes are ideal for virtually any use case, including those with demanding performance needs, data residency requirements, unknown or changing access patterns, or archival storage.
* Amazon Athena is an interactive query service that makes it easy to analyze data in Amazon S3 and other federated data sources using standard SQL.
## Brefily Route
Create S3 bucket and define IAM roles in SageMaker Studio, it takes a while, once it done, we can upload the dataset we desire.

![image](https://user-images.githubusercontent.com/64514218/159199323-5b3aae90-8784-4c2c-b7cb-1adc223a119e.png)
Upload the dataset into Sagemaker Wrangler from S3, then build a data flow to analysis and prepoccessing the data.

![image](https://user-images.githubusercontent.com/64514218/159200135-096e954e-253f-41f0-afa9-97ea797d2684.png)
After finished prepoccessing, export it into Feature Store. This step is actually unnecessary for personal analysis, but it will be useful in team work, people can share their processed data with others.

![image](https://user-images.githubusercontent.com/64514218/159200246-f19afa88-c913-448d-b71c-5a779b11b01b.png)
Adding identifier using "FS_ID".
``` Python
record_identifier_feature_name = "FS_ID"
if record_identifier_feature_name is None:
   raise SystemExit("Select a column name as the feature group record identifier.")
```
Upon done, we can check it in Feature Store.
![image](https://user-images.githubusercontent.com/64514218/159200908-41b2c825-c0c5-4cb2-b1a6-42a4734f3ad4.png)
Again, the advantage by doing so is sharing our processed data with others in Athena.
![image](https://user-images.githubusercontent.com/64514218/159201034-23237b51-e555-4bb2-a9c9-9bc6f0a7b7ee.png)
## Modeling and Deploy

The model is built by XGboost Algorithm which is popular right now. Rather than extract data from S3, the code import data from Feature Store we build before to mimic team work scenario, it make sence since we will hard get a chance to work on individual task.
![image](https://user-images.githubusercontent.com/64514218/159201656-1d71033c-13db-49af-a1e2-2ca7c080fd66.png)
After training the model, deploys our trained model to a real-time endpoint. Hoting the trained model allows us to make inferences against it. Then, we can check it in Sagemaker endpoint.
```Python
xgb_predictor = xgb.deploy(initial_instance_count=1,
                           instance_type='ml.m4.xlarge') 
```    
![image](https://user-images.githubusercontent.com/64514218/159202866-4471e981-b4b6-4667-a91a-31475fdb16f3.png)

Using the ```xgb_predictor``` we defined to predict test dataset, its performance evaluation as follow, and the weighted F-1 Score is 0.8718576186783866.
|predictions	|0|1|
|:---|:---|:---|
|actuals| |	|	
|0	|3570	|68|
|1	|369	|112|
Check the jupyter notebook for details.


