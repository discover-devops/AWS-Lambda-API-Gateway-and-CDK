### AWS Lambda key features of AWS Lambda:

1. **Environment Variables**:
   - Modify function behavior without changing the code by using environment variables.

2. **Versions**:
   - Manage different versions of functions for tasks like beta testing while maintaining a stable production version.

3. **Container Images**:
   - Deploy Lambda functions using container images, allowing reuse of existing container tooling and handling larger workloads.

4. **Layers**:
   - Package and reuse libraries and dependencies across multiple functions to reduce deployment package size and speed up deployments.

5. **Lambda Extensions**:
   - Enhance Lambda functions with tools for monitoring, observability, security, and governance.

6. **Function URLs**:
   - Provide dedicated HTTP(S) endpoints for your Lambda functions.

7. **Response Streaming**:
   - Stream response payloads back to clients from Node.js functions to improve time to first byte (TTFB) and handle larger payloads.

8. **Concurrency and Scaling Controls**:
   - Implement fine-grained controls over the scaling and responsiveness of applications.

9. **Code Signing**:
   - Ensure that only approved and trusted code is deployed by verifying the integrity of your Lambda function code.

10. **Private Networking**:
    - Create private networks to securely connect your Lambda functions to resources like databases and internal services.

11. **File System Access**:
    - Enable functions to access and modify shared resources by mounting Amazon Elastic File System (Amazon EFS).

12. **Lambda SnapStart for Java**:
    - Boost startup performance for Java runtimes by up to 10x with no additional cost, typically without code changes.
