
![image](https://github.com/user-attachments/assets/a9312b79-34a1-49bc-b500-0a2899617944)


Step-by-step guide to creating a basic serverless application using **AWS API Gateway**, **AWS Lambda**, and **Amazon S3**.

---

### **Objective:**
Create a Lambda function that reads JSON data from S3 buckets and expose it through an API Gateway. The API Gateway will trigger the Lambda function, which will fetch JSON data from an S3 bucket and return it as a response.

---

### **Step 1: Create S3 Buckets and Upload JSON Files**

1. **Go to S3 Console** in AWS Management Console.
2. **Create the First Bucket**:
   - Click on **Create Bucket**.
   - Enter the bucket name, e.g., `firstbucket02112024`.
   - Leave other settings as default and click **Create**.
   - After the bucket is created, click on the bucket name to open it.
   - **Upload JSON File**:
     - Click **Upload**, select a JSON file with the following content:
       ```json
       {
           "name": "bucket1student1",
           "age": 22
       }
       ```
     - Click **Upload**.
3. **Create the Second Bucket**:
   - Repeat the above steps to create a second bucket, e.g., `secondbucket02112024`.
   - **Upload JSON File**:
     - Click **Upload**, select a JSON file with the following content:
       ```json
       {
           "name": "bucket2student2",
           "age": 32
       }
       ```
     - Click **Upload**.

---

### **Step 2: Create an AWS Lambda Function**

1. **Go to Lambda Console** in AWS Management Console.
2. **Create Function**:
   - Click on **Create Function**.
   - Select **Author from Scratch**.
   - Function name: `s3DataReaderFunction`.
   - Runtime: **Python 3.x** (or your preferred Python version).
   - Click **Create Function**.
3. **Configure Permissions**:
   - Under **Configuration** > **Permissions**, find the **Execution Role**.
   - Click on the role name to open the IAM console.
   - Click **Attach policies**.
   - Search for **AmazonS3FullAccess** and attach it to the role, so the Lambda function can access S3 buckets.
4. **Write Lambda Code**:
   - In the **Code** tab, replace the default code with the following:
     ```python
     import json
     import boto3
     
     client = boto3.client('s3')
     
     def lambda_handler(event, context):
         response = client.get_object(
             Bucket='firstbucket02112024', 
             Key='bucket1.json'
         )
         # Convert streaming body to bytes
         data_bytes = response['Body'].read()
         
         # Bytes to string
         data_string = data_bytes.decode("UTF-8")
         
         # Convert string to dict
         data_dict = json.loads(data_string)
         
         return {
             'statusCode': 200,
             'body': data_dict
         }
     ```
   - Replace `'firstbucket02112024'` and `'bucket1.json'` with your bucket and file names if they are different.
5. **Increase Timeout**:
   - Go to **Configuration** > **General configuration**.
   - Increase the timeout to 1 minute for testing purposes and save the configuration.

---

### **Step 3: Test the Lambda Function**

1. **Create a Test Event**:
   - Click on **Test**.
   - Configure the test event (you can use any JSON since we aren’t using event data in this lab).
   - Click **Create**.
2. **Run the Test**:
   - Click **Test** to invoke the function.
   - Verify that the response contains the data from the JSON file in your S3 bucket.

---

### **Step 4: Set Up API Gateway**

1. **Go to API Gateway Console** in AWS Management Console.
2. **Create a REST API**:
   - Select **REST API** and click **Build**.
   - Choose **New API**.
   - API name: `StudentDataAPI`.
   - Click **Create API**.
3. **Create Resource**:
   - In the API Gateway console, click on **Actions** > **Create Resource**.
   - Resource Name: `student`.
   - Ensure **Enable API Gateway CORS** is selected.
   - Click **Create Resource**.
4. **Create GET Method**:
   - Click on the **/student** resource.
   - Click **Actions** > **Create Method** and select **GET**.
   - **Setup the GET Method**:
     - Integration type: **Lambda Function**.
     - Check **Use Lambda Proxy integration**.
     - Region: Select your AWS region.
     - Lambda Function: Enter the name of your Lambda function (`s3DataReaderFunction`).
     - Click **Save**, then **OK** when prompted to add required permissions.

---

### **Step 5: Test API Gateway**

1. **Deploy API**:
   - Click **Actions** > **Deploy API**.
   - **Deployment Stage**: Create a new stage called `dev`.
   - Click **Deploy**.
2. **Invoke API**:
   - Go to the **Stages** section in API Gateway and find the **Invoke URL** for the `dev` stage.
   - Open this URL in your browser to test. It should return the JSON data from the specified S3 bucket.

---

### **Next Steps**
In the next lab, we’ll modify the Lambda function to dynamically select the bucket and key based on query string parameters.
