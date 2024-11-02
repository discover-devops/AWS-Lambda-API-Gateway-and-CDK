Step-by-step guide to modifying the Lambda function and API Gateway configuration to dynamically fetch JSON data from different S3 buckets based on query string parameters.

---

### **Objective:**
Modify the API Gateway and Lambda function to accept `bucket` and `key` as query string parameters. These parameters will determine which S3 bucket and object to read when the API is invoked.

---

### **Step 1: Update the Lambda Function**

1. **Go to Lambda Console**:
   - Open the AWS Management Console and navigate to the **Lambda** service.
   - Select the Lambda function you created in Lab 1 (`s3DataReaderFunction`).

2. **Update the Lambda Code**:
   - Replace the hardcoded Lambda code with the following code to make it dynamic:
     ```python
     import json
     import boto3
     client = boto3.client('s3')
     
     def lambda_handler(event, context):
         # Retrieve bucket and key from the event
         bucket = event['bucket']
         key = event['key']
         
         # Fetch the object from S3
         response = client.get_object(
             Bucket=bucket,
             Key=key,
         )
         
         # Convert the streaming body to bytes
         data_bytes = response['Body'].read()
         
         # Convert bytes to string
         data_string = data_bytes.decode("UTF-8")
         
         # Convert string to dictionary
         data_dict = json.loads(data_string)
         
         return {
             'statusCode': 200,
             'body': data_dict
         }
     ```
   - **Explanation**:
     - `bucket = event['bucket']` and `key = event['key']`: The Lambda function reads the `bucket` and `key` values from the event object (which API Gateway will provide when the query parameters are passed).
     - This change allows the Lambda to dynamically fetch data from different S3 buckets based on the input.

3. **Save and Deploy**:
   - Click **Deploy** to save and apply the changes to the Lambda function.

4. **Test the Lambda Function**:
   - Go to the **Test** tab.
   - Create a new test event with the following JSON to simulate passing the `bucket` and `key` parameters:
     ```json
     {
         "bucket": "firstbucket02112024",
         "key": "bucket1.json"
     }
     ```
   - Click **Test** and verify that the Lambda function returns the correct JSON data from the specified S3 bucket.

---

### **Step 2: Modify API Gateway to Accept Query Parameters**

1. **Go to API Gateway Console**:
   - Open the **API Gateway** console.
   - Select the API you created in Lab 1 (`StudentDataAPI`).

2. **Navigate to the GET Method**:
   - Expand the API structure to find the `/student` resource.
   - Click on the **GET** method under the `/student` resource.

3. **Configure Method Request to Accept Query String Parameters**:
   - Under **Method Request**, find the **URL Query String Parameters** section.
   - Click **Add query string** for each parameter:
     - Parameter Name: `bucket` – Check **Required**.
     - Parameter Name: `key` – Check **Required**.
   - Click the **checkmark** to save each parameter.

4. **Update Integration Request to Pass Query Parameters to Lambda**

   - Under **Integration Request**, find the **Mapping Templates** section.
   - Click **Add mapping template**.
     - Content-Type: `application/json`
     - Click the **checkmark** to save.
   - **Define the Mapping Template**:
     - In the `application/json` mapping template, enter the following code:
       ```json
       {
           "bucket": "$input.params('bucket')",
           "key": "$input.params('key')"
       }
       ```
     - This template maps the `bucket` and `key` query parameters from the API request to JSON format, which will be passed as the `event` object to the Lambda function.

5. **Save Changes**:
   - Click **Save** to apply the changes.

---

### **Step 3: Test the API Gateway**

1. **Deploy the API**:
   - Go back to the **Actions** dropdown in the API Gateway console and select **Deploy API**.
   - Choose the **dev** stage (or create a new stage if necessary).
   - Click **Deploy**.

2. **Invoke the API with Query Parameters**:
   - Go to the **Stages** section in API Gateway, select the **dev** stage, and find the **Invoke URL**.
   - Append the query parameters to the URL to test:
     ```
     https://your-api-id.execute-api.region.amazonaws.com/dev/student?bucket=firstbucket02112024&key=bucket1.json
     ```
   - Replace `firstbucket02112024` and `bucket1.json` with appropriate values if they are different.
   - Open this URL in your browser or use a tool like **Postman** to send a GET request.
   - Verify that the API response contains the data from the specified S3 bucket and key.

---

### **Step 4: Test with the Second Bucket**

1. **Invoke the API for the Second Bucket**:
   - To verify that the API works for a different bucket and key, repeat Step 3 with the following parameters:
     ```
     https://your-api-id.execute-api.region.amazonaws.com/dev/student?bucket=secondbucket02112024&key=bucket2.json
     ```
   - Verify that the API response contains the data from the second S3 bucket.

---

### **Summary**

In this lab, we updated the Lambda function and API Gateway to accept `bucket` and `key` as query string parameters. This allows us to fetch JSON data dynamically from different S3 buckets based on the parameters passed in the API request.
