## Module 1: Introduction to API Gateway Authentication and Authorization in AWS

In this module, we’ll explore various methods available within AWS API Gateway for authenticating and authorizing incoming requests. Understanding these methods is essential for securing your APIs and ensuring that only authorized users or services can access them.

### Overview of Authentication and Authorization Methods

AWS API Gateway offers three primary approaches to authentication and authorization:
1. **AWS IAM Authentication**
2. **Amazon Cognito User Pools**
3. **Lambda Authorizer**

Each method suits different scenarios and use cases. Let’s discuss these approaches in detail.

---

### 1. **AWS IAM Authentication**

AWS IAM (Identity and Access Management) is typically used when requests to the API Gateway come from internal AWS services, either within the same AWS account or from a different AWS account (cross-account access). This approach leverages IAM roles and policies to authenticate and authorize requests.

#### **Use Case**
- **Internal AWS Services**: When your API is accessed by AWS services like EC2 instances, Lambda functions, or other resources within AWS.
- **Cross-Account Access**: If services in another AWS account need access, use IAM with a combination of resource policies.

#### **How It Works**
1. A client (such as an EC2 instance) makes an API call to API Gateway using **Signature Version 4 (Sigv4)** signing process.
2. API Gateway has built-in integration with IAM. When a request is received, API Gateway:
   - Validates the IAM role and attached policies associated with the client.
   - Checks the permissions defined in the IAM policy to ensure the request is authorized.
3. If the request originates from a different AWS account, a **resource policy** on the API Gateway must also allow access.

#### **Configuration Steps**
- In API Gateway, open the method settings and set **Authorization** to **AWS_IAM**.
- For cross-account access:
   - Go to **Resource Policy** in the API Gateway console.
   - Define which AWS accounts, IP ranges, or VPCs are allowed access using the options available in the resource policy editor.

---

### 2. **Amazon Cognito User Pools**

Amazon Cognito is an ideal choice for handling authentication when you have external users, such as web or mobile app users. Cognito User Pools help manage user registration, authentication, and authorization through API Gateway.

#### **Use Case**
- **External Users**: Ideal for scenarios where you have user-facing applications (web or mobile) and need to authenticate and authorize external users.


![image](https://github.com/user-attachments/assets/138fec19-e5e4-442a-b68f-41857d28a1f8)


#### **How It Works**
1. A client (such as a mobile app) sends an authentication request to Amazon Cognito User Pool.
2. Cognito authenticates the user and, upon successful authentication, issues a **token** (JWT token).
3. The client includes this token in subsequent API requests to API Gateway.
4. API Gateway validates the token by integrating with Cognito User Pool.
5. If the token is valid, API Gateway forwards the request to the backend; if invalid, it denies access.

#### **Configuration Steps**
- In API Gateway, go to **Authorizers** and create a new **Cognito User Pool Authorizer**.
- Specify the **Cognito User Pool** that you want to use.
- Set up the **token source** (usually from the `Authorization` header).

---

### 3. **Lambda Authorizer**

Lambda Authorizers allow you to build custom authentication and authorization solutions, especially when using third-party identity providers (such as Auth0, Google, etc.). A Lambda function handles the authorization logic, which can include validating tokens from an external identity provider.

#### **Use Case**
- **Third-Party Authentication**: When you want to use an external identity provider to authenticate users.
- **Custom Authorization Logic**: Useful for complex authorization logic beyond what IAM and Cognito User Pools offer.


![image](https://github.com/user-attachments/assets/175dc663-93b1-4fe4-86f7-6affb9d3683d)


#### **How It Works**
1. A client authenticates with a third-party identity provider, receiving a **bearer token** or other authentication credentials.
2. The client includes this token in requests to API Gateway.
3. API Gateway forwards the token or request parameters to a **Lambda Authorizer** function.
4. The Lambda function:
   - Validates the token or parameters with the external identity provider.
   - If valid, the Lambda function returns an **IAM policy** to API Gateway, which defines access permissions.
   - API Gateway caches this policy for subsequent requests from the same client, reducing latency.
5. If the request is authorized, API Gateway forwards it to the backend; otherwise, it returns a 403 error.

#### **Configuration Steps**
- In API Gateway, go to **Authorizers** and create a new **Lambda Authorizer**.
- Choose the **Lambda function** that will handle authorization.
- Define the **token source** (typically the `Authorization` header) and specify **token validation** options.
- Enable caching for improved performance, if desired.

---

### **Summary of API Gateway Authentication and Authorization Approaches**

| Method                     | Use Case                           | Authentication                     | Authorization                              |
|----------------------------|------------------------------------|------------------------------------|--------------------------------------------|
| **AWS IAM**                | Internal AWS Services              | IAM Roles and Policies             | IAM Policies (with optional Resource Policy for cross-account) |
| **Cognito User Pools**     | External Web/Mobile Users         | Cognito User Pools (JWT tokens)    | API Gateway Method Authorization           |
| **Lambda Authorizer**      | Third-Party Identity Providers    | Custom Logic (via Lambda Function) | Custom Logic (via Lambda Function)         |

### **Choosing the Right Approach**
- **AWS IAM**: Use when accessing API Gateway from AWS services, especially within the same account or for controlled cross-account access.
- **Cognito User Pools**: Suitable for applications with external users who require user management, login, and identity verification.
- **Lambda Authorizer**: Ideal when working with third-party identity providers or needing highly customized authentication and authorization logic.

Each approach provides unique benefits and fits specific scenarios. Selecting the appropriate method depends on the nature of your client, the identity provider, and any additional customization requirements for authorization logic.
