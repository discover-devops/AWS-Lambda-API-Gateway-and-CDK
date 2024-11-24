### **Step-by-Step Guide to Creating a Serverless Application using AWS CDK**

---

### **Use Case**
We will create a fictitious banking application where a customer can check their balance status via an API. The application involves:

1. An **S3 bucket** for storing a JSON file with the account balance.
2. An **IAM role** to allow access to the S3 bucket from a Lambda function.
3. A **Lambda function** that reads the JSON file from the S3 bucket and returns the balance.
4. An **API Gateway** to expose the Lambda function via a RESTful API.

---

### **Prerequisites**

Before proceeding, ensure you have the following:

1. **AWS Account**:
   - Sign up for a free-tier AWS account if you don’t already have one.

2. **Visual Studio Code**:
   - Download and install [Visual Studio Code](https://code.visualstudio.com/).

3. **AWS CLI**:
   - Download and install [AWS CLI](https://aws.amazon.com/cli/).
   - Validate installation by running:  
     ```bash
     aws --version
     ```

4. **Node.js and NPM**:
   - Download and install [Node.js](https://nodejs.org/).  
   - Verify installation:
     ```bash
     node --version
     ```

5. **AWS CDK**:
   - Install the AWS CDK globally:  
     ```bash
     npm install -g aws-cdk
     ```
   - Verify installation:  
     ```bash
     cdk --version
     ```

6. **AWS CLI Configuration**:
   - Configure AWS CLI credentials using:
     ```bash
     aws configure
     ```
   - Provide your AWS access key, secret key, default region (e.g., `us-east-1`), and default output format (`json`).

7. **AWS CDK Bootstrap**:
   - Run the bootstrap command to prepare your account:
     ```bash
     cdk bootstrap
     ```

---

### **Steps to Build the Application**

#### **Step 1: Create a Project Directory**
1. Create a new folder for the project:
   ```bash
   mkdir banking-app-cdk
   cd banking-app-cdk
   ```

2. Initialize the CDK project:
   ```bash
   cdk init app --language typescript
   ```

---

#### **Step 2: Install Dependencies**
Install necessary AWS CDK libraries for S3, Lambda, IAM, and API Gateway:
```bash
npm install @aws-cdk/aws-s3 @aws-cdk/aws-lambda @aws-cdk/aws-iam @aws-cdk/aws-apigateway
```

---

#### **Step 3: Create the Directory Structure**
Organize the project:
```bash
mkdir -p infra services
```
- **`infra`**: Contains infrastructure-related code.
- **`services`**: Contains application-specific Lambda code.

---

#### **Step 4: Create the S3 Bucket**
1. Open `lib/banking-app-stack.ts` and import the required module:
   ```typescript
   import * as s3 from '@aws-cdk/aws-s3';
   ```

2. Define the S3 bucket in the stack:
   ```typescript
   const accountStatusBucket = new s3.Bucket(this, 'AccountStatusBucket', {
       bucketName: 'balance-status-0125',
       removalPolicy: cdk.RemovalPolicy.DESTROY, // Auto-delete bucket during stack destruction
   });
   ```

---

#### **Step 5: Create an IAM Role**
1. Import IAM module:
   ```typescript
   import * as iam from '@aws-cdk/aws-iam';
   ```

2. Add the IAM role:
   ```typescript
   const lambdaRole = new iam.Role(this, 'BankingLambdaRole', {
       assumedBy: new iam.ServicePrincipal('lambda.amazonaws.com'),
       managedPolicies: [iam.ManagedPolicy.fromAwsManagedPolicyName('AmazonS3FullAccess')],
   });
   ```

---

#### **Step 6: Write the Lambda Function Code**
1. Navigate to the `services` folder:
   ```bash
   cd services
   ```

2. Create a new file named `lambda_function.py` and add the following Python code:
   ```python
   import json
   import boto3

   def lambda_handler(event, context):
       s3 = boto3.client('s3')
       bucket_name = 'balance-status-0125'
       file_key = 'account-status.json'

       response = s3.get_object(Bucket=bucket_name, Key=file_key)
       data = json.loads(response['Body'].read().decode('utf-8'))

       return {
           'statusCode': 200,
           'body': json.dumps(data)
       }
   ```

---

#### **Step 7: Create the Lambda Function**
1. Import the Lambda module:
   ```typescript
   import * as lambda from '@aws-cdk/aws-lambda';
   ```

2. Add the Lambda function:
   ```typescript
   const bankingLambda = new lambda.Function(this, 'BankingLambda', {
       runtime: lambda.Runtime.PYTHON_3_8,
       code: lambda.Code.fromAsset('services'),
       handler: 'lambda_function.lambda_handler',
       role: lambdaRole,
   });
   ```

---

#### **Step 8: Create the API Gateway**
1. Import API Gateway module:
   ```typescript
   import * as apigateway from '@aws-cdk/aws-apigateway';
   ```

2. Add API Gateway:
   ```typescript
   const bankingApi = new apigateway.LambdaRestApi(this, 'BankingApi', {
       handler: bankingLambda,
       restApiName: 'BankingRestApi',
   });
   ```

---

#### **Step 9: Deploy the Stack**
1. Build the CDK application:
   ```bash
   npm run build
   ```

2. Deploy the stack:
   ```bash
   cdk deploy
   ```

3. Confirm deployment when prompted.

---

### **Step 10: Test the API**
1. Copy the API Gateway endpoint from the CloudFormation outputs.
2. Use **Postman** or a browser to send a GET request to the `/bank-status` endpoint.
3. Verify the response matches the JSON data in your S3 bucket.

---

### **Step 11: Cleanup**
To avoid incurring charges, delete the stack:
```bash
cdk destroy
```

---

### **Conclusion**

By following this guide, you built a serverless application using AWS CDK. You defined the infrastructure (S3, IAM, Lambda, API Gateway) using TypeScript and deployed it efficiently. This approach simplifies infrastructure management, reduces boilerplate code, and integrates seamlessly with your existing development pipeline.
