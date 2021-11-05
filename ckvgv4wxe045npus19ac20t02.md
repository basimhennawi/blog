## 5 Anti-Patterns to Avoid in Lambda based Apps

Lambda is not a prescriptive service and provides broad functionality for you to build applications as needed. While this flexibility is important, it can also lead to designs that are technically functional but may be suboptimal from an architecture and cost point of view. As with all patterns and anti-patterns in technology, these are not rules.

Let's highlight the 5 anti-patterns to avoid in Lambda based applications as per the AWS docs.

### **1. Monolith Lambda**

In many applications migrated from traditional servers, EC2 instances or Elastic Beanstalk applications, developers "lift and shift" existing code. Frequently, this results in a single Lambda function that contains all of the application logic that is triggered for all events. For a basic web application, a monolithic Lambda function would handle all API Gateway routes and integrate with all necessary downstream resources.

![lambda1a.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1635780524707/uV3TialzT.png)

This approach has several drawbacks:

- **Package size**: the Lambda function may be much larger because it contains all possible code for all paths, which makes it slower for the Lambda service to download and run.

- **Hard to enforce least privilege**: the function’s IAM role must allow permissions to all resources needed for all paths, making the permissions very broad. Many paths in the functional monolith do not need all the permissions that have been granted.

- **Harder to upgrade**: in a production system, any upgrades to the single function are more risky and could cause the entire application to stop working. Upgrading a single path in the Lambda function is an upgrade to the entire function.

- **Harder to maintain**: it’s more difficult to have multiple developers working on the service since it’s a monolithic code repository. It also increases the cognitive burden on developers and makes it harder to create appropriate test coverage for code.

- **Harder to reuse code**: typically, it can be harder to separate reusable libraries from monoliths, making code reuse more difficult. As you develop and support more projects, this can make it harder to support the code and scale your team’s velocity.

- **Harder to test**: as the lines of code increase, it becomes harder to unit all the possible combinations of inputs and entry points in the code base. It’s generally easier to implement unit testing for smaller services with less code.

The preferred alternative is to decompose the monolithic Lambda function into individual microservices, mapping a single Lambda function to a single, well-defined task. In this simple web application with a few API endpoints, the resulting microservice-based architecture can be based upon the API Gateway routes.

![lambda1b.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1635780553002/RMX-QHoRz.png)

### **2. Orchestrator Lambda**

Many business workflows result in complex workflow logic, where the flow of operations depends on multiple factors. Implementing this logic in a Lambda function can result in "spaghetti code" that’s difficult to read, understand, and maintain. It can also become very fragile in production systems. The complexity is compounded if you must handle error handling, retry logic, and inputs and outputs processing. These types of orchestration functions are an anti-pattern in Lambda-based applications.

Instead, use  [AWS Step Functions](https://aws.amazon.com/step-functions/)  to orchestrate these workflows using a versionable, JSON-defined state machine. State machines can handle nested workflow logic, errors, and retries. Using this approach also results in significantly less custom code, making an application easier to test and maintain.

### **3. Recursive patterns Lambda**

AWS services generate events that invoke Lambda functions, and Lambda functions can send messages to AWS services. Generally, the service or resource that invokes a Lambda function should be different to the service or resource that the function outputs to. Failure to manage this can result in infinite loops.

For example, a Lambda function writes an object to an S3 object, which in turn invokes the same Lambda function via a put event. The invocation causes a second object to be written to the bucket, which invokes the same Lambda function:

![lambda3a.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1635780970344/GVhHrJPHi.png)

While the potential for infinite loops exists in most programming languages, this anti-pattern has the potential to consume more resources in serverless applications. Both Lambda and S3 automatically scale based upon traffic, so the loop may cause Lambda to scale to consume all available concurrency and S3 will continue to write objects and generate more events for Lambda.

In this event, you can press the "Throttle" button in the Lambda console to scale the function concurrency down to zero and break the recursion cycle. After fixing this anti-pattern you could scroll down to "Concurrency" on the "Configuration" tab. The Reserve Concurrency was set to 0. Adjusting this to a positive number removes the Throttle.

![3b.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1635781251073/EIoPPWZ6u.png)

### **4. Lambda functions calling Lambda functions**

Functions enable encapsulation and code re-use. Most programming languages support the concept of code synchronously calling functions within a code base. In this case, the caller waits until the function returns a response.

When this happens on a traditional server or virtual instance, the operating system scheduler switches to other available work. Whether the CPU runs at 0% or 100% does not affect the overall cost of the application, since you are paying for the fixed cost of owning and operating a server.

This model often does not adapt well to serverless development. For example, consider a simple ecommerce application consisting of three Lambda functions that process an order:

![lambda4a.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1635781334298/tvGiXYfC-.png)

In this case, the Create order function calls the Process payment function, which in turn calls the Create invoice function. While this synchronous flow may work within a single application on a server, it introduces several avoidable problems in a distributed serverless architecture:

- **Cost**: with Lambda, you pay for the duration of an invocation. In this example, while the Create invoice functions runs, two other functions are also running in a wait state, shown in red on the diagram.

- **Error handling**: in nested invocations, error handling can become much more complex. Either errors are thrown to parent functions to handle at the top-level function, or functions require custom handling. For example, an error in Create invoice might require the Process payment function to reverse the charge, or it may instead retry the Create invoice process.

- **Tight coupling**: processing a payment typically takes longer than creating an invoice. In this model, the availability of the entire workflow is limited by the slowest function.

- **Scaling**: the concurrency of all three functions must be equal. In a busy system, this uses more concurrency than would otherwise be needed.

In serverless applications, there are two common approaches to avoid this pattern:

- SQS queue for decoupling between Lambda functions
- Step Functions, for complex processes with multiple types of failure and retry logic

### **5. Synchronous waiting within a single Lambda function**

Within a single Lambda, ensure that any potentially concurrent activities are not scheduled synchronously. For example, a Lambda function might write to an S3 bucket and then write to a DynamoDB table:

![lambda5a.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1635781631397/bk3HronqV.png)

The wait states, shown in the red in the diagram, are compounded because the activities are sequential. If the tasks are independent, they can be run in parallel, which results in the total wait time being set by the longest-running task.


![lambda5b.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1635781646285/t1swFxbe8.png)

In cases where the second task depends on the completion of the first task, you may be able to reduce the total waiting time and the cost of execution by splitting the Lambda functions:

![lambda5c.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1635781659547/8IuEG7iZS.png)

In this design, the first Lambda function responds immediately after putting the object to the S3 bucket. The S3 service invokes the second Lambda function, which then writes data to the DynamoDB table. This approach minimizes the total wait time in the Lambda function executions

### Summary

- Try to separate functionality into different functions that can provide better performance.
- Try to avoid "spaghetti code" that’s difficult to read, understand, and maintain.
- Try to use positive triggers that only triggered on the first invocation.
- Try to use queue if needed to decouple between two functions and persists messages.
- Try to ensure that concurrent activities are not scheduled synchronously.

That's it!

### References

- [AWS Lambda Developer Guide](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html)
- [AWS Lambda Operator Guide](https://docs.aws.amazon.com/lambda/latest/operatorguide/intro.html)