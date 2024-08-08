### key points related to AWS Lambda:

1. **Compute Service**: AWS Lambda is a compute service similar to EC2 but designed for serverless operations.

2. **Serverless**: It allows you to run code without provisioning or managing servers. AWS handles the infrastructure, including operating system maintenance, capacity provisioning, scaling, and logging.

3. **Highly Available**: Lambda runs on a highly available compute infrastructure, automatically ensuring reliability and scalability.

4. **Pay-as-You-Go**: Unlike EC2, where you pay for the provisioned instance size, Lambda charges you only for the compute time you actually use.

5. **Event-Driven**: Lambda functions are triggered by events (e.g., S3 uploads, API requests) rather than running continuously. This makes it ideal for unpredictable workloads.

6. **Automatic Scaling**: Lambda automatically scales in response to incoming requests, without requiring manual intervention.

7. **Use Cases**:
   - **Image Processing**: Resize images upon upload to an S3 bucket.
   - **API Backend**: Use Lambda with API Gateway to build scalable REST APIs.

8. **When Not to Use Lambda**:
   - When you need control over the infrastructure for compliance or security reasons.
   - For workloads requiring continuous server operation, like traditional web servers.

9. **Architecture**:
   - Lambda runs on shared physical infrastructure managed by AWS.
   - It leverages virtualization to create micro VMs, each serving as an isolated environment for executing Lambda functions.

This summary captures the key aspects of AWS Lambda, focusing on its serverless nature, cost efficiency, and event-driven architecture.
