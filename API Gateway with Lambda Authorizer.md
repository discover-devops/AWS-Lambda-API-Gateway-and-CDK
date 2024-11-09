## Module 3: Hands-On Implementation of API Gateway with Lambda Authorizer for Authentication and Authorization

In this hands-on lab, we’ll build a secure API Gateway solution that uses a Lambda Authorizer to validate requests. We'll start with a simple setup where a client requests access to a backend Lambda function via API Gateway. Once this basic architecture is in place, we’ll enhance it with a Lambda Authorizer to enforce authentication and authorization.

### Solution Overview

1. **Initial Setup**: A client sends a request to API Gateway, which invokes a backend Lambda function that returns a simple response.
2. **Enhancing Security**: We’ll add a Lambda Authorizer, which validates access tokens before granting access to the backend function, thus securing the API Gateway endpoint.

---

### Step 1: Create the Backend Lambda Function

1. **Navigate to AWS Lambda Console**:
   - Go to **AWS Lambda** in the AWS Management Console.
   - Click **Create Function**.

2. **Configure the Backend Function**:
   - **Function name**: `DemoLambdaBackend`
   - **Runtime**: **Python 3.7+**
   - Click **Create Function**.

3. **Edit the Function Code**:
   - Replace the default Lambda function code with:
     ```python
     import json

     def lambda_handler(event, context):
         return {
             'statusCode': 200,
             'body': json.dumps('Hello from AWS Lambda Backend!')
         }
     ```
   - Click **Deploy** to save the changes.

4. **Test the Backend Lambda Function**:
   - Go to the **Test** tab, create a new test event with sample JSON input.
   - Run the test to verify that the function returns `"Hello from AWS Lambda Backend!"`.

---

### Step 2: Create an API Gateway to Invoke the Lambda Function

1. **Navigate to API Gateway Console**:
   - Go to **API Gateway** in the AWS Management Console.
   - Click **Create API** > **REST API** > **Build**.

2. **Configure API Details**:
   - **API Name**: `DemoLambdaAuthorizerAPI`
   - **Endpoint Type**: **Regional**
   - Click **Create API**.

3. **Create a Resource and Method**:
   - Click **Actions** > **Create Resource**.
   - **Resource Name**: `students`
   - Click **Create Resource**.
   - With the `students` resource selected, click **Actions** > **Create Method** > **GET**.
   - Set **Integration Type** to **Lambda Function** and select `DemoLambdaBackend`.
   - Click **Save** and confirm.

4. **Test the API**:
   - In the **GET** method, go to **Test**, click **Test**, and verify it returns `"Hello from AWS Lambda Backend!"`.

5. **Deploy the API**:
   - Click **Actions** > **Deploy API**.
   - **Deployment Stage**: **New Stage** named `dev`.
   - Click **Deploy**.
   - Copy the **Invoke URL** from the `GET` method in the `dev` stage. This URL can be tested in **Postman** or a browser.

---

### Step 3: Create a Lambda Authorizer Function

1. **Create Lambda Authorizer Function**:
   - Go back to **AWS Lambda Console**.
   - Click **Create Function**.
   - **Function Name**: `DemoLambdaAuthorizerFunction`
   - **Runtime**: **Python 3.7+**
   - Click **Create Function**.

2. **Edit the Lambda Authorizer Code**:
   - Replace the default code with:
     ```python
     import json

     def lambda_handler(event, context):
         # Log the event data for debugging
         print("Event received:", event)

         # Check for the token in the event header
         token = event.get('authorizationToken', '')

         # Validate the token (for demonstration, use "123456" as the valid token)
         if token == "123456":
             effect = "Allow"
         else:
             effect = "Deny"

         # Generate IAM policy based on the token validation
         policy_document = {
             "Version": "2012-10-17",
             "Statement": [
                 {
                     "Action": "execute-api:Invoke",
                     "Effect": effect,
                     "Resource": "arn:aws:execute-api:us-east-1:<your_account_id>:<api_id>/dev/GET/students"
                 }
             ]
         }

         # Return the policy and principal ID
         return {
             "principalId": "user",
             "policyDocument": policy_document
         }
     ```
   - Replace `<your_account_id>` and `<api_id>` with your AWS account ID and API Gateway ID (found in **API Gateway Console** under **Stages** > `dev` stage).
   - Click **Deploy** to save the changes.

3. **Test the Lambda Authorizer**:
   - Go to the **Test** tab, create a new test event with:
     ```json
     {
         "authorizationToken": "123456"
     }
     ```
   - Run the test to verify the function returns an `Allow` policy if the token is correct and a `Deny` policy for any other token.

---

### Step 4: Add the Lambda Authorizer to API Gateway

1. **Configure the Authorizer in API Gateway**:
   - Go to **API Gateway Console** and select `DemoLambdaAuthorizerAPI`.
   - Select **Authorizers** > **Create New Authorizer**.
   - **Name**: `DemoLambdaAuthorizer`
   - **Type**: **Lambda**
   - **Lambda Function**: Choose `DemoLambdaAuthorizerFunction`.
   - **Token Source**: Enter `Authorization` (the header where the token will be passed).
   - Click **Create**.

2. **Associate the Authorizer with the API Method**:
   - Go to the **GET** method under `/students`.
   - Click **Method Request**.
   - Set **Authorization** to `DemoLambdaAuthorizer`.
   - Save changes.

3. **Deploy the API Again**:
   - Click **Actions** > **Deploy API**.
   - Select the **dev** stage and click **Deploy**.

---

### Step 5: Test the Secured API

1. **Testing Without Token**:
   - In **Postman** or a browser, send a `GET` request to the invoke URL **without** the `Authorization` header.
   - The response should return a `401 Unauthorized` error as no token was provided.

2. **Testing with Valid Token**:
   - In **Postman**, add an `Authorization` header with the value `123456`.
   - Send the request again, and it should return the response `"Hello from AWS Lambda Backend!"`.

3. **Testing with Invalid Token**:
   - Change the `Authorization` header to an invalid token (e.g., `654321`).
   - Send the request; this time, it should return a `403 Forbidden` response, indicating the token is invalid.

---

### Summary

In this lab, we created a backend Lambda function and secured it with an API Gateway Lambda Authorizer. This authorizer validated access tokens and generated an IAM policy to control access based on the token's validity. By integrating custom authentication into our API, we’ve secured our backend and demonstrated how to use Lambda Authorizers for flexible access control in API Gateway. 

