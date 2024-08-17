Here’s a step-by-step lab guide to create and manage an S3 bucket using AWS Lambda, along with launching an EC2 instance. This lab will cover:

1. Creating an S3 bucket.
2. Listing all S3 buckets.
3. Deleting an S3 bucket.
4. Launching an EC2 instance.

### Prerequisites:
- AWS account.
- IAM Role with sufficient permissions (S3, Lambda, EC2).
- AWS CLI or AWS Management Console access.

### Step 1: Create an IAM Role for Lambda
1. Go to the IAM Console.
2. Create a new role:
   - Select **Lambda** as the trusted entity.
   - Attach the following policies:
     - `AmazonS3FullAccess`
     - `AmazonEC2FullAccess`
   - Give your role a name, e.g., `LambdaS3EC2Role`.

### Step 2: Create a Lambda Function to Create an S3 Bucket
1. Go to the AWS Lambda Console.
2. Create a new Lambda function:
   - Name: `CreateS3Bucket`
   - Runtime: Python 3.x
   - Role: Use the IAM role created in Step 1.
3. Replace the default code with the following:

   ```python
   import json
   import boto3
   
   client = boto3.client('s3')
   
   def lambda_handler(event, context):
       create_s3bucket = client.create_bucket(
           Bucket='kumarnewbucket',
           CreateBucketConfiguration={
               'LocationConstraint': 'ap-south-2'  # Modify as per your region
           }
       )
       
       print(create_s3bucket)
   ```

4. Deploy the function.
5. Test the function by creating a test event and execute. Verify that the S3 bucket named `kumarnewbucket` is created in the specified region.

### Step 3: Create a Lambda Function to List S3 Buckets
1. Create another Lambda function:
   - Name: `ListS3Buckets`
   - Runtime: Python 3.x
   - Role: Use the same IAM role from Step 1.
2. Replace the default code with the following:

   ```python
   import json
   import boto3
   
   client = boto3.client('s3')
   
   def lambda_handler(event, context):
       list_s3 = client.list_buckets()
       for bucket in list_s3['Buckets']:
           print(bucket['Name'])
   ```

3. Deploy the function.
4. Test the function and check the logs to verify that it lists all S3 buckets in your account.

### Step 4: Create a Lambda Function to Delete an S3 Bucket
1. Create another Lambda function:
   - Name: `DeleteS3Bucket`
   - Runtime: Python 3.x
   - Role: Use the same IAM role from Step 1.
2. Replace the default code with the following:

   ```python
   import json
   import boto3
   
   client = boto3.client('s3')
   
   def lambda_handler(event, context):
       delete_bucket = client.delete_bucket(
           Bucket='kumarbucket'
       )
       
       print(delete_bucket)
   ```

3. Deploy the function.
4. Test the function by deleting the bucket named `kumarbucket`. Ensure that the bucket is deleted successfully.

### Step 5: Create a Lambda Function to Launch an EC2 Instance
1. Create another Lambda function:
   - Name: `LaunchEC2Instance`
   - Runtime: Python 3.x
   - Role: Use the same IAM role from Step 1.
2. Replace the default code with the following:

   ```python
   import json
   import boto3
   
   client = boto3.client('ec2')
   
   def lambda_handler(event, context):
       response = client.run_instances(
           ImageId='ami-0d147324c76e8210a',  # Modify ImageID as per your region
           InstanceType='t2.micro',
           MaxCount=1,
           MinCount=1
       )
       print(response['Instances'][0]['InstanceId'])
   ```

3. Deploy the function.
4. Test the function to launch an EC2 instance. Verify that an EC2 instance is created and the instance ID is printed.

### Step 6: Clean Up
- Delete the Lambda functions after the lab.
- Terminate any EC2 instances created during the lab.
- Delete any S3 buckets created.

### Conclusion
This lab demonstrates how to create and manage S3 buckets and EC2 instances using AWS Lambda functions. You learned how to create, list, and delete S3 buckets and launch an EC2 instance using Python and Boto3 within Lambda.
