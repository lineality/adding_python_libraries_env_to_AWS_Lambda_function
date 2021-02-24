# adding_python_libraries_to_AWS_Lambda_function
adding_python_libraries_to_AWS_Lambda_function

# Endpoint_with_Python_virtual_environment_AWS_Lambda_apiGateway

# Overview: 
We will be adding a virtual environment with additional python packages to the AWS Lambda Function

It is relatively simple to set up the AWS Lambda Function with additional python libraries, but:
1. The steps must be done exactly-correctly and sadly the official documentation is a bit lacking.
2. It is more difficult to change the Lambda Function because it is hidden from view when bundled with the virtual environment that contains the additional python libraries.

# Steps:

## Step 1: Project Repo 
#### Create a project management repo (e.g. a github repo)

## Step 2: Create Your Virtual Environment -> specifically "venv"
#### (Note: AWS integration requires a venv (not a pipenv))
https://packaging.python.org/guides/installing-using-pip-and-virtual-environments/

#### Create & enter virtual environment and add the python package requirements:
#### Note: user python 3.8 (required by AWS...even thought 3.9 is current...hmm)

#### Steps to create venv virtual environment in python:
#### For more detailed install instructions see here:
https://docs.google.com/document/d/1dZJI20D7uIknT1pdlTSmlHH1WPYdVhs2PSUyH1qdnUo/edit

#### Run these 3 lines individually in a terminal:
```
python3 -m venv env
(or)
python -m venv env
```
## Active the Environment:
```
source env/bin/activate
```

## Step 3: Inside Your Virtual Environment 
### Add python packages:
#### You can add your libraries individually or altogether if they are listed in a requirements.txt doc:

```
pip install -r requirements.txt
```

## Step 4: First Part of zip-sandwhich
#### You will be creating a zip-file-sandwhich containing your virtual environment and code. Afterwards you upload that zip-sandwhich to AWS.

#### drill down to site packages in the venv folders-> env/lib/python3.8/sitepackages
#### Open another terminal there (and check where you are!)
#### And Run this line exactly as it is: 

```
zip -r9 ../../../../function.zip .
```
#### This should zip all the files from the root to that packages folder
#### Note: The resulting zip file will be made in the project root directory, not in the directory you traversed into, so...you won't see it created where you are (because it is somewhere else). 

## Step 6: Second part of zip-sandwhich: add App folder to the zip folder: 

#### Go back to the root folder (containing app env and now functions.zip, etc.)
#### Open a terminal (and check where you are)
#### In project-root folder: 
#### Run this line exactly as it is: 

```
zip -g ./function.zip -r lambda_function.py
```

## Check: look in the zip folder and you should see your lambda_function.py file (along with many others)

#### Note: you can re-run this to modify the lambda_handler.py
While you cannot simply change the lambda_handler.py file easily in AWS, you can change the file locally, rerun the above zip (it replaces the file in the zip) then re-upload to AWS. 

## Step 7: add to S3 (or upload directly into AWS Lambda)

## Step 8: create an "AWS Lambda Function" service
- upload (direct or from S3) that function.zip file
- Add permission to access S3 to the lambda-function:
permissions-> execution role -> role name -> policy name -> permissions -> edit policy -> json -> add this (aligned with the rest) -> review policy -> save changes!
       {
           "Effect": "Allow",
           "Action": "s3:*",
           "Resource": "*"
       },


- test with custom json input



## Step 9: create "AWS APIGateway" service
- create api
- action: add 
create a "POST" but do NOT select "use lambda proxy integration" but put the name of the proxy in anyway
Note: check to make sure all four sections are filled in.

- test POST


##### deploy
- deploy
- test the deployed api




# Data in AWS Lambda
- .csv (lambda env cannot create .csv files...)
- S3
- Redshift
- dynamo DB
- 



Notes for first risk score system:


1. fastAPI
2. AWS ? lambda, ec2?
3. python pandas dataframe for risk score
4. heuristic: if no values -> .5
5. if values -> average
6. plotly dash URL: ec2?
7. second endpoint?
8. 3 endpoints...
	- both
	- score
	- dashboard...url?
	- insert data into dashboard for company number query?


Example input:

http://127.0.0.1:8000/api/v1/score/?company_number=12 


locomex...

https://8w35qfypv9.execute-api.us-east-1.amazonaws.com/dev



https://8w35qfypv9.execute-api.us-east-1.amazonaws.com/dev/api/v1/score/ 
in theory this URL might work internally with the json request
{"company_number": 2}


try...
set up a direct root put...

why doesn't put work? 


...





https://s3rre36ytf.execute-api.us-east-1.amazonaws.com/dev 

AWS + FastAPI
https://www.youtube.com/watch?v=6fE31084Uks

https://fastapi.tiangolo.com/tutorial/body-multiple-params/ 

https://fastapi.tiangolo.com/tutorial/first-steps/

## Possible Resources
#### AWS Lambda & FastAPI
https://towardsdatascience.com/fastapi-aws-robust-api-part-1-f67ae47390f9 

https://github.com/anna-anisienia/cryptoAPI

https://www.youtube.com/watch?v=uFsaiEhr1zs 

?
https://towardsdatascience.com/autodeploy-fastapi-app-to-heroku-via-git-in-these-5-easy-steps-8c7958ef5d41?gi=3cfcd0f1c922 

https://packaging.python.org/guides/installing-using-pip-and-virtual-environments/

...
https://stackoverflow.com/questions/49163099/writing-a-file-to-s3-using-lambda-in-python-with-aws#49163707 


https://foobar123.com/serverless-save-a-file-in-s3-using-aws-lamdba-d3f087880dd2
https://www.youtube.com/watch?v=UWEI5lrOe5w&feature=emb_title 

for uploading a file:





key = 'csv/' + key
bucket.upload_file('/tmp/output2.csv', key)


vs. 

s3.upload_fileobj(fileobj, 'mybucket', 'mykey')

```
import os

# AWS library for connecting to S3
s3 = boto3.resource('s3')

# pick your bucket by name
bucket = s3.Bucket('api-sample-bucket1')

# safe file in /tmp/ directory because AWS Lambda requires it
with open('/tmp/test.csv', 'w') as data:
    data.write("Hello_World")

key = 'test.csv'
bucket.upload_file('/tmp/output2.csv', key)

# remove file because AWS is buggy
os.remove('/tmp/test.csv') 
```


...

import json
import os
import boto3

def lambda_handler(event, context):

    # AWS library for connecting to S3
    s3 = boto3.resource('s3')
    
    # pick your bucket by name
    bucket = s3.Bucket('api-sample-bucket1')
    
    # safe file in /tmp/ directory because AWS Lambda requires it
    with open('/tmp/test.csv', 'w') as data:
        data.write("Hello_World")
    
    key = 'test.csv'
    bucket.upload_file('/tmp/test.csv', key)
    
    # remove file because AWS is buggy
    # os.remove('/tmp/test.csv') 

    return {
        'statusCode': 200,
        'body': json.dumps('Hello csv Lambda!')
    }



....

# works

import json
import os
import boto3

def lambda_handler(event, context):

    # AWS library for connecting to S3
    s3 = boto3.resource('s3')
    
    # pick your bucket by name
    bucket = s3.Bucket('api-sample-bucket1')
    
    # safe file in /tmp/ directory because AWS Lambda requires it
    with open('/tmp/test.csv', 'w') as data:
        data.write("Hello_World")
    
    name_of_file_you_make_key = 'test.csv'
    bucket.upload_file('/tmp/test.csv', name_of_file_you_make_key)
    
    # remove file because AWS is buggy
    os.remove('/tmp/test.csv') 

    return {
        'statusCode': 200,
        'body': json.dumps('Hello csv Lambda!')
    }




