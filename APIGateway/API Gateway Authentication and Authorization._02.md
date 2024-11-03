## Module 2: Securing APIs with Lambda Authorizer for Authentication and Authorization

In our previous lab, we built a basic architecture where a client sends an API request to the API Gateway, which invokes a Lambda function to retrieve JSON data from an S3 bucket. However, we identified a significant security issue: anyone with the API's invoke URL can access the S3 data. In this module, we’ll address this by learning how to secure the API using authentication and authorization with AWS Lambda Authorizers.

### Overview of API Gateway Security Options

To secure your APIs, AWS API Gateway provides three primary approaches:
1. **IAM Authentication** – For internal AWS services or cross-account access.
2. **Cognito User Pools** – For external users accessing via web or mobile applications.
3. **Lambda Authorizers** – For custom authentication, especially when using third-party identity providers (IDPs).

Each approach is suited to specific scenarios:
- **IAM** is ideal for AWS-internal services.
- **Cognito** is tailored for external users, providing user management and token-based authentication.
- **Lambda Authorizers** are highly customizable and allow integration with third-party identity providers that support OAuth 2.0 or other standards.

### Using Lambda Authorizer to Secure API Gateway

In this module, we’ll dive into using a **Lambda Authorizer** for API authentication and authorization. This method is flexible and commonly used when integrating with third-party identity providers, making it suitable for scenarios where Cognito or IAM alone may not meet the requirements.

---

### **Existing Architecture Recap**

Our existing architecture is simple:
1. A client sends an API request to API Gateway.
2. API Gateway invokes a Lambda function.
3. The Lambda function reads data from a JSON file in an S3 bucket and returns the response.

This setup lacks authentication, meaning anyone with the invoke URL can access the API. To secure this, we’ll add a Lambda Authorizer, enabling custom authentication and authorization logic based on an external identity provider.

---

### **How Lambda Authorizer Works**

The Lambda Authorizer introduces a more secure flow:

1. **Client Authenticates with Third-Party Identity Provider (IDP)**:
   - The client (e.g., a mobile app or third-party service) sends login credentials to an external IDP that supports OAuth 2.0 or other authentication standards.
   - The IDP validates the credentials and, if correct, returns an **access token** to the client.

2. **Client Sends API Request with Access Token**:
   - The client includes this access token in the **Authorization** header of the API request to API Gateway.
   - Example header:
     ```plaintext
     Authorization: Bearer <access-token>
     ```

3. **API Gateway Passes Token to Lambda Authorizer**:
   - API Gateway forwards the access token to the Lambda Authorizer (a Lambda function with custom code to handle authentication).
   - The Lambda Authorizer validates the access token by verifying it with the third-party IDP.

4. **Lambda Authorizer Generates IAM Policy**:
   - Once the Lambda Authorizer validates the token with the IDP, it generates an **IAM policy** that specifies access permissions.
   - The generated policy includes either `Allow` or `Deny` actions based on token validation. This policy is then returned to API Gateway.

5. **API Gateway Enforces the Policy**:
   - API Gateway evaluates the returned IAM policy:
     - If `Allow`, API Gateway forwards the request to the Lambda function, allowing access to the backend S3 data.
     - If `Deny`, API Gateway returns a `403 Forbidden` response to the client.
   - API Gateway can also cache the policy to reduce latency on subsequent requests from the same client.

---

### **Detailed Flow of Lambda Authorizer Authentication**

1. **Client Authentication**:
   - The client sends credentials (username, password, or other identity parameters) to the third-party IDP.
   - IDP verifies credentials and issues an access token upon successful authentication.

2. **API Request with Token**:
   - The client sends an API request with the access token included in the `Authorization` header to the API Gateway.

3. **Token Validation by Lambda Authorizer**:
   - API Gateway forwards the access token to the Lambda Authorizer.
   - The Lambda Authorizer is configured with custom logic to validate the token by querying the IDP (e.g., checking token validity, expiry, and scopes).

4. **Policy Generation**:
   - The Lambda Authorizer generates an IAM policy based on the token's validity and other verification criteria.
   - This policy is returned to API Gateway with either `Allow` or `Deny` actions for specific API resources.

5. **Access Control by API Gateway**:
   - API Gateway evaluates the policy:
     - **Allow** – API Gateway forwards the request to the backend service (e.g., the Lambda function that retrieves data from S3).
     - **Deny** – API Gateway responds with `403 Forbidden`, denying access to the client.
   - API Gateway can cache the policy for the client to enhance performance on subsequent requests.

---

### **Benefits of Using Lambda Authorizer**

- **Customizable**: Lambda Authorizers allow you to write custom logic, making it adaptable to various authentication needs.
- **Third-Party Integration**: It supports OAuth 2.0 and integrates well with other IDPs (such as Okta, Auth0, and Google).
- **Dynamic Access Control**: You can implement complex authorization logic within the Lambda function, providing fine-grained control over API access.
- **Token-Based Authentication**: This approach reduces the need for users to manage API keys or IAM roles, relying on modern authentication standards.

---

### **Configuration Steps for Lambda Authorizer**

1. **Set Up the Lambda Authorizer Function**:
   - Write a Lambda function to handle token validation, including the logic to authenticate with your third-party IDP and generate IAM policies.

2. **Create Authorizer in API Gateway**:
   - In API Gateway, go to **Authorizers** and create a new **Lambda Authorizer**.
   - Choose the Lambda function created in the previous step.
   - Specify the token source (e.g., the `Authorization` header) and configure token validation.

3. **Associate the Authorizer with an API Method**:
   - Go to your API method (e.g., `GET` on `/student` resource).
   - Set **Authorization** to the Lambda Authorizer created.

4. **Test the Integration**:
   - Send a request to API Gateway with a valid access token and verify that it allows or denies access based on the token’s validity.

---

### **Summary**

Adding a Lambda Authorizer to our API Gateway allows us to secure API endpoints with flexible, token-based authentication and authorization. This setup supports OAuth 2.0 tokens, integrates with third-party identity providers, and offers customizable authorization policies for fine-grained access control.

By leveraging Lambda Authorizers, we can ensure only authenticated users with valid tokens can access sensitive data in our backend services. This approach enhances the security and control over our APIs, making it suitable for enterprise use cases with complex authentication requirements. 

