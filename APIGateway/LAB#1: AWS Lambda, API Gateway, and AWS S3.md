## LAB#1: AWS Lambda, API Gateway, and AWS S3

### **Introduction:**

In this lab, we will explore a serverless architecture using AWS Lambda, API Gateway, and S3. The objective is to create an API that, when invoked, triggers a Lambda function. This Lambda function will read JSON data from an S3 bucket. Initially, we'll hardcode the bucket and object names in the Lambda function. In the next part, we'll enhance this by making the Lambda function dynamic, allowing it to read data from different buckets based on query string parameters passed in the API request.

### **Hands-on Lab Steps:**

#### **1. Create S3 Buckets and Upload JSON Files**

- **Step 1:** Create two S3 buckets.
  - **Bucket 1:** `first-09-01-09`
  - **Bucket 2:** `second-09-01-09`
  
- **Step 2:** Upload a JSON file to each bucket.
  - **Bucket 1 JSON:**
    ```json
    {
        "name": "John",
        "age": 22
    }
    ```
  - **Bucket 2 JSON:**
    ```json
    {
        "name": "Doe",
        "age": 32
    }
    ```

#### **2. Create an AWS Lambda Function**

- **Step 1:** Go to the AWS Lambda console and create a new Lambda function named `s3-read-09-01-09` using Python as the runtime.

- **Step 2:** Increase the timeout for the function to 1 minute under the General Configuration settings.

- **Step 3:** Attach the `AmazonS3FullAccess` policy to the Lambda execution role to allow it to access S3.

- **Step 4:** Write the Lambda function code to read the JSON file from the S3 bucket.
  - **Code:**
    ```python
    import json
    import boto3

    s3 = boto3.client('s3')

    def lambda_handler(event, context):
        bucket_name = 'first-09-01-09'
        key = 'your-json-file.json'
        
        response = s3.get_object(Bucket=bucket_name, Key=key)
        content = response['Body'].read().decode('utf-8')
        json_content = json.loads(content)
        
        return {
            'statusCode': 200,
            'body': json.dumps(json_content)
        }
    ```

- **Step 5:** Test the Lambda function by creating a test event. The output should match the content of the JSON file in the S3 bucket.

#### **3. Create an API Gateway to Trigger the Lambda Function**

- **Step 1:** In the API Gateway console, create a new REST API.

- **Step 2:** Create a new resource named `/student`.

- **Step 3:** Add a `GET` method to the `/student` resource. Configure this method to integrate with the Lambda function created earlier.

- **Step 4:** Deploy the API to a new stage named `dev`.

- **Step 5:** Invoke the API via its endpoint. The output should match the JSON data from the S3 bucket.

#### **4. Enhancing the Lambda Function to Accept Query String Parameters**

- **Step 1:** Modify the Lambda function to accept `bucket` and `key` as input parameters from the API request.

  - **Updated Lambda Code:**
    ```python
    import json
    import boto3

    s3 = boto3.client('s3')

    def lambda_handler(event, context):
        bucket_name = event['queryStringParameters']['bucket']
        key = event['queryStringParameters']['key']
        
        response = s3.get_object(Bucket=bucket_name, Key=key)
        content = response['Body'].read().decode('utf-8')
        json_content = json.loads(content)
        
        return {
            'statusCode': 200,
            'body': json.dumps(json_content)
        }
    ```

- **Step 2:** Update the API Gateway method integration to pass the query string parameters to the Lambda function.

- **Step 3:** Test the API with different query string parameters to retrieve data from both S3 buckets dynamically.

### **Conclusion:**

In this lab, we created a serverless architecture using AWS Lambda, API Gateway, and S3. Initially, we hardcoded the bucket and object names in the Lambda function. Later, we enhanced the Lambda function to accept query string parameters, making it dynamic. This allows the API to read data from different S3 buckets based on the user's input. This lab demonstrates how to set up a simple yet powerful serverless architecture using AWS services.
