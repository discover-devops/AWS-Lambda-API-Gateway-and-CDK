### Step-by-Step Lab: Automating EC2 Instance Creation with EventBridge and Lambda

In this lab, you'll learn how to set up an automated process to create a new EC2 instance when an existing one is terminated using Amazon EventBridge and AWS Lambda. This scenario ensures that your application remains available even if an EC2 instance fails.

![image](https://github.com/user-attachments/assets/28eef60c-3f66-4ba5-8ac2-0c032fa17821)



### Prerequisites
- An AWS account with access to the EC2, Lambda, and EventBridge services.
- Basic knowledge of AWS Lambda, EventBridge, and EC2.

### Lab Overview
1. **Create a Lambda Function to Launch EC2 Instances**
2. **Set Up EventBridge Rule to Monitor EC2 Termination Events**
3. **Test the Automation**

### Step 1: Create a Lambda Function to Launch EC2 Instances

1. **Navigate to the AWS Lambda Console:**
   - Go to the [AWS Management Console](https://aws.amazon.com/console/) and search for **Lambda** in the search bar.
   - Click on **Create function**.

2. **Create a New Lambda Function:**
   - Choose **Author from scratch**.
   - Function name: `EC2Automation`
   - Runtime: **Python 3.10** (or the latest available)
   - Click **Create function**.

3. **Add Permissions to Lambda:**
   - In the **Configuration** tab, select **Permissions**.
   - Click on the **Execution role** name to open the IAM console.
   - In the IAM console, under **Permissions policies**, click **Attach policies**.
   - Search for `AmazonEC2FullAccess` and select it.
   - Click **Attach policy**.

4. **Update the Lambda Function Code:**
   - In the **Code** tab, replace the default code with the following Python script:

     ```python
     import boto3

     def lambda_handler(event, context):
         ec2 = boto3.client('ec2')
         
         # AMI ID (You can use an AMI with pre-installed software if required)
         ami_id = 'ami-12345678'  # Replace with your AMI ID
         
         # Create a new EC2 instance
         instance = ec2.run_instances(
             ImageId=ami_id,
             MinCount=1,
             MaxCount=1,
             InstanceType='t2.micro',
             KeyName='your-key-pair',  # Replace with your key pair
             TagSpecifications=[
                 {
                     'ResourceType': 'instance',
                     'Tags': [
                         {
                             'Key': 'Name',
                             'Value': 'Auto-Launched-Instance'
                         },
                     ]
                 },
             ]
         )
         print(f"New EC2 instance created: {instance['Instances'][0]['InstanceId']}")
     ```

   - Click **Deploy** to save the changes.

5. **Increase Lambda Timeout:**
   - Go to **Configuration** -> **General configuration**.
   - Click **Edit** and increase the **Timeout** to `1 minute`.
   - Click **Save**.

### Step 2: Set Up EventBridge Rule to Monitor EC2 Termination Events

1. **Navigate to the Amazon EventBridge Console:**
   - Search for **EventBridge** in the AWS Management Console.
   - Click on **Create rule**.

2. **Create a New EventBridge Rule:**
   - Rule name: `EC2TerminationAutomation`
   - Rule type: **Rule with an event pattern**.
   - Click **Next**.

3. **Define the Event Source:**
   - Event source: **AWS services**.
   - Service Name: **EC2**.
   - Event Type: **EC2 Instance State-change Notification**.
   - Specific state: **terminated**.
   - Click **Next**.

4. **Select the Lambda Function as the Target:**
   - Target type: **Lambda function**.
   - Function name: `EC2Automation`.
   - Click **Next**.

5. **Review and Create the Rule:**
   - Review the settings and click **Create rule**.

### Step 3: Test the Automation

1. **Terminate the EC2 Instance:**
   - Go to the **EC2 Console** and select an existing instance.
   - Click on **Instance State** -> **Terminate**.

2. **Monitor the EventBridge and Lambda:**
   - After termination, the EventBridge rule should trigger the Lambda function.
   - This Lambda function will launch a new EC2 instance automatically.

3. **Verify the New EC2 Instance:**
   - Go to the **EC2 Console** and confirm that a new instance has been created.
   - Check the **CloudWatch Logs** for the Lambda function to see the log of the instance creation.

### Conclusion

This lab demonstrated how to automate the creation of an EC2 instance using Amazon EventBridge and AWS Lambda when an existing EC2 instance is terminated. This automation helps maintain application availability and provides a fault-tolerant infrastructure solution.
