Links: https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-authorization-flow.html
https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-resource-policies.html


tep-by-step guide for creating an API Gateway integrated with the Lambda function you’ve created. This covers the steps from creating the API Gateway to customizing requests and responses.


![image](https://github.com/user-attachments/assets/7e33ef79-af87-48af-93c2-245c4c718b73)


---

### Full Lab Document: Creating an API Gateway Integrated with Lambda

---

#### Prerequisites
- Ensure that you have an AWS account.
- Have a Lambda function ready (created in Step 1) that returns a 200 OK response.

---

### Step 1: Create a Lambda Function to Return a 200 OK Response (Already Completed)

---

### Step 1: Create a Lambda Function to Return a 200 OK Response

#### 1. **Navigate to AWS Lambda**:
   - Go to the **AWS Management Console**.
   - Search for and select **Lambda**.

#### 2. **Create a New Lambda Function**:
   - Click on **Create function**.
   - Choose **Author from scratch**.
   - **Function name**: Enter a name, e.g., `APIGatewayDemoLambda`.
   - **Runtime**: Select **Python 3.x** (or another runtime of your choice).
   - **Permissions**: Use an existing role with permissions or create a new one (Lambda needs permission to execute).

#### 3. **Write the Lambda Function Code**:
   - In the **Function code** section, replace the default code with a basic function that returns a 200 OK response. Here’s an example in Python:

     ```python
     import json

     def lambda_handler(event, context):
         return {
             'statusCode': 200,
             'body': json.dumps('Hello from Lambda!')
         }
     ```

   - This code simply responds with a 200 status code and a message, "Hello from Lambda!".

#### 4. **Deploy the Lambda Function**:
   - Click **Deploy** to save the code changes.

#### 5. **Test the Lambda Function** (Optional):
   - Click on **Test** to create a new test event.
   - Use the default settings or configure the event, then click **Test**.
   - Check the **Execution result** section to confirm it returns a `200` status and the expected response.

---



---

### Step 2: Create an API Gateway

1. **Navigate to API Gateway**:
   - From the **AWS Management Console**, search for and select **API Gateway**.

2. **Create a New API**:
   - Choose **Create API**.
   - **API type**: Select **REST API** (for full feature capabilities).
   - **Protocol**: Choose **REST**.
   - **Create New API**: Select **New API**.
   - **API Name**: Enter a name, e.g., `StudentAPI`.
   - **Description**: (Optional) Enter a brief description of your API.
   - **Endpoint Type**: Choose **Regional** (for this lab).
   - Click **Create API** to proceed.

---

### Step 3: Create a Resource

1. **Create the Resource Path**:
   - In your API Gateway console, click on **Actions** and select **Create Resource**.
   - **Resource Name**: Enter a name for the resource, e.g., `students`.
   - **Resource Path**: The path should auto-populate as `/students`.
   - **Enable API Gateway CORS**: (Optional) Enable if you plan to access this API from a web browser.
   - Click **Create Resource**.

---

### Step 4: Create an HTTP Method for the Resource

1. **Add a Method**:
   - Select the `/students` resource, click on **Actions**, and choose **Create Method**.
   - **Method Type**: Choose **GET** from the dropdown.
   - Click on the checkmark to confirm.

2. **Integrate the GET Method with Lambda**:
   - **Integration Type**: Choose **Lambda Function**.
   - **Use Lambda Proxy Integration**: Keep this checked for simplicity.
   - **Lambda Region**: Select the same region where your Lambda function is located.
   - **Lambda Function**: Enter the name of your Lambda function, e.g., `APIGatewayDemoLambda`.
   - Click **Save** and then **OK** to grant API Gateway permissions to invoke your Lambda function.

---

### Step 5: Customize Request and Response

1. **Method Request** (Optional for Basic Setup):
   - In the **GET - Method Execution** section, select **Method Request**.
   - **Authorization**: Choose from options like AWS IAM if you want to add request-level security (skip for a public API).
   - **Request Validator**: Select if you want API Gateway to validate query parameters, headers, or body content.
   - **API Key Requirement**: Set to **true** if you want to enforce API key validation (skip for now).

2. **Integration Request** (Optional for Basic Setup):
   - Go to **Integration Request**.
   - **Mapping Templates**: If you need to transform the incoming request, select **Mapping Templates** and use **Velocity Template Language (VTL)** for custom mappings.

3. **Method Response** (Optional for Basic Setup):
   - Click on **Method Response**.
   - **Response Headers**: Customize headers if required, such as `Content-Type` for your responses.

4. **Integration Response** (Optional for Basic Setup):
   - Customize the **Integration Response** to map or transform the Lambda function’s response if necessary.

---

### Step 6: Test the API

1. **Testing Directly in API Gateway**:
   - In the API Gateway console, navigate to **GET - Method Execution**.
   - Click on **Test**.
   - Click **Test** again in the pop-up window.
   - **Expected Result**: You should see a response with **Status Code 200** and the message `Hello from Lambda!` (or your Lambda’s defined response).

---

### Step 7: Deploy the API

1. **Create a Deployment Stage**:
   - In the **API Gateway** console, click on **Actions** and select **Deploy API**.
   - **Deployment Stage**: Choose **[New Stage]**.
   - **Stage Name**: Enter a name, e.g., `dev`.
   - **Description**: (Optional) Describe this stage.
   - Click **Deploy**.

2. **Access the API Endpoint**:
   - After deployment, you’ll see an **Invoke URL** for your API (e.g., `https://your-api-id.execute-api.region.amazonaws.com/dev/students`).
   - Copy this URL and paste it into a web browser or an API testing tool (like Postman) to confirm the API returns the expected response.

---

### Step 8: Additional Security and Customization (Optional)

1. **Enable CORS**:
   - If you plan to access the API from a web application, enable **CORS** under **Method Request** → **Enable CORS** for your methods.

2. **Authentication and Authorization**:
   - Go to **Method Request** and set up **Authorization** (e.g., AWS IAM) if you require authentication for the API.

3. **API Keys**:
   - Under **API Gateway Console** → **Usage Plans**, you can create usage plans and enforce API key validation for additional security.

---
