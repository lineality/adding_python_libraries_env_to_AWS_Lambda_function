# Adding Python Libraries to an AWS-Lambda-function

# Overview: 
#### AWS python Lambda-Functions (part of one system for making endpoints, with AWS Lambda -Fucntions and AWS-api-Gateway) come with some built-in python libraries but only a few. 
#### E.g. 

####  These are included:
- json
- boto3

#### These are NOT included:
- numpy
- pandas
- psycopg2 (for postgreSQL)

#### Method: To add additional libraries we will be uploading a zipped python-virtual-environment (which contains additional python packages) to the AWS Lambda Function.

It is relatively simple to set up the AWS Lambda Function with additional python libraries, but:
1. The steps must be done exactly-correctly and sadly the official documentation is a bit lacking.
2. It is more difficult to change that Lambda Function because the code is hidden from view when bundled with the virtual environment that contains the additional python libraries.
3. an older python 3.8 version needs to be used to make the environment (even though 3.9 is now the current python)
4. zipped environments larger than ~50mb must be loaded to S3 first, then imported into AWS Lambda. 

# Steps:

## Step 1: Project Repo 
#### Create a project repo to manage and share your code (e.g. a github repo)

## Step 2: Create Your Virtual Environment -> specifically "venv"
#### (Note: AWS integration requires a venv (not a pipenv))
https://packaging.python.org/guides/installing-using-pip-and-virtual-environments/

#### Create & enter virtual environment and add the python package requirements:
#### Note: user python 3.8 (required by AWS...even thought 3.9 is current...hmm)

#### Steps to create venv virtual environment in python:
#### For more detailed install instructions see here:
https://docs.google.com/document/d/1dZJI20D7uIknT1pdlTSmlHH1WPYdVhs2PSUyH1qdnUo/edit

# Run these lines individually in a terminal:

## Create your environment:
```
$ python3 -m venv env
(or)
$ python -m venv env
```
## Active the Environment:
```
$ source env/bin/activate
```

## Step 3: Inside Your Virtual Environment: 
### Add python packages:
#### You can add your libraries individually or altogether if they are listed in a requirements.txt doc:

```
$ pip install -r requirements.txt
```

## Step 4: First Part of zip-sandwhich
#### You will be creating a zip-file-sandwhich containing your virtual environment and code. Afterwards you upload that zip-sandwhich to AWS.

#### drill down to site packages in the venv folders-> env/lib/python3.8/sitepackages
#### Open another terminal there (and check where you are!)
#### And Run this line exactly as it is: 

```
$ zip -r9 ../../../../function.zip .
```
#### This should zip all the files from the root to that packages folder
#### Note: The resulting zip file will be made in the project root directory, not in the directory you traversed into, so...you won't see it created where you are (because it is somewhere else). 

## Step 6: Second part of zip-sandwhich: add App folder to the zip folder: 

#### Go back to the root folder (containing app env and now functions.zip, etc.)
#### Open a terminal (and check where you are)
#### In project-root folder: 
#### Run this line exactly as it is: 

```
$ zip -g ./function.zip -r lambda_function.py
```

## Check: look in the zip folder and you should see your lambda_function.py file (along with many others)

#### Note: you can re-run this to modify the lambda_handler.py
While you cannot simply change the lambda_handler.py file easily in AWS, you can change the file locally, rerun the above zip (it replaces the file in the zip) then re-upload to AWS. 

## Step 7: add to S3 (or upload directly into AWS Lambda)

## Step 8: create an "AWS Lambda Function" service
- upload (direct or from S3) that function.zip file
- Add permission to access S3 to the lambda-function:
There are a few ways to do this, and the AWS interface is perpetually changing. 
Two basic approaches:
1. manually add script to the policy
2. use the AWS GUI to add pre-made policy items

## Manual Method:
#### permissions-> execution role -> role name -> policy name -> permissions -> edit policy -> json -> add this (aligned with the rest) -> review policy -> save changes!
```
       {
           "Effect": "Allow",
           "Action": "s3:*",
           "Resource": "*"
       },
```
#### (Note: you may want to add a less sweeping set of access for your project.)


## Step 9: updating your lambda_function.py file
#### There are generally two methods for updating your file, and both involve re-uploading the function.zip file to AWS:
1. Update the file in your repo and re-run this command:
```
$ zip -g ./function.zip -r lambda_function.py
```
Then re-upload the function.zip to AWS.

2. Open the zip file, make the changes, save, then click "update" on the popup window. (This may not be available on every OS?
Then re-upload the function.zip to AWS.




