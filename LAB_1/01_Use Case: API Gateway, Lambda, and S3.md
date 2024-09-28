### Serverless Architecture Use Case: API Gateway, Lambda, and S3

In this lab, we will design a serverless architecture using **Amazon API Gateway**, **AWS Lambda**, and **Amazon S3**. The purpose of this use case is to dynamically retrieve data from S3 buckets using query string parameters passed via an API.

### Use Case Overview

1. **User Request via API Gateway**:
   - The user will invoke an API through **Amazon API Gateway**.
   - The API Gateway will trigger a **Lambda function**.

2. **Lambda Function Reads from S3**:
   - The Lambda function will read the contents of a **JSON file** stored in a specified **S3 bucket**.

3. **Dynamic Content Retrieval**:
   - In the next part, the user will pass **query string parameters** via the API request.
   - The Lambda function will use these parameters to determine which **S3 bucket** to retrieve the data from.
   - Based on the provided query string parameters, the function will read the JSON data from either the first or the second S3 bucket, providing dynamic data retrieval.


![image](https://github.com/user-attachments/assets/ddc8c0d0-848f-461e-b531-ae2fbb35b832)


### Step-by-Step Breakdown

1. **Set up the API Gateway**: This will be the front-end for users to invoke the Lambda function.
   
2. **Create a Lambda Function**: The function will process the request and interact with S3.
   
3. **Store Data in S3 Buckets**: Create and store JSON data in multiple S3 buckets for the Lambda function to retrieve.

4. **Dynamic Query String Parameters**: The Lambda function will process the query string parameters passed via the API request to decide from which S3 bucket to read.

### Hands-On Part

- **Part 1**: The Lambda function reads from a hardcoded S3 bucket when triggered by the API Gateway.
  
- **Part 2**: Make the solution dynamic by using query string parameters to select the appropriate S3 bucket based on user input.

This lab will guide you through building a scalable, serverless solution that interacts with multiple S3 buckets dynamically based on user input via API Gateway and Lambda.
