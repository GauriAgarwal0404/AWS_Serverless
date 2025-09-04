# REINVENT 2020 RELEASE - Lambda Container Images - Deep Dive and Demo

## 📦 Lambda Container Image - What and Why

### **What is Lambda Container Image?**
Lambda Container Image support allows you to package and deploy Lambda functions as container images instead of traditional ZIP files.

### **How it Works:**
```
Traditional Lambda:
AWS Provides Container → Your Code/Dependencies Loaded → Function Execution

Lambda Container Images:
Your Custom Container (from ECR) → Direct Function Execution
```

### **Key Concept:**
- **Instead of AWS providing container:** You create and control the container
- **Container stored in ECR:** Amazon Elastic Container Registry
- **Lambda invokes your container:** Uses your custom environment
- **Same Lambda service:** Still serverless, still event-driven

## 🔄 Lambda Container Image Support Flow

### **Traditional vs Container Image:**

**Traditional Lambda (ZIP):**
```
1. AWS provides base container
2. Your code/dependencies get loaded at runtime
3. Function executes in AWS-managed environment
4. Limited to 50 MB package size
```

**Container Image Lambda:**
```
1. You create custom container image
2. Push container to ECR (Elastic Container Registry)
3. Lambda pulls your container when invoked
4. Function executes in YOUR container environment
5. Support up to 10 GB package size
```

### **Container Image Deployment Process:**
```
Developer Machine:
├── Create Dockerfile
├── Build container image
├── Test locally (Runtime Interface Emulator)
└── Push to ECR

AWS Lambda:
├── Pull container from ECR
├── Initialize container
└── Execute function
```

## 🏗️ AWS Provided Base Images

### **Starting Point - Base Images:**
AWS provides pre-built base images for different runtimes with predefined libraries:

**Available Base Images:**
```
- aws/lambda-python:3.9
- aws/lambda-nodejs:16
- aws/lambda-java:11
- aws/lambda-dotnet:6
- aws/lambda-go:1.x
- aws/lambda-ruby:2.7
```

### **Base Image Benefits:**
- **Pre-configured Runtime:** Language runtime already installed
- **Lambda Runtime Interface:** Built-in Lambda integration
- **Optimized for Lambda:** Fast cold start performance
- **Security Updates:** AWS maintains and updates base images

### **Custom Dependencies on Base Images:**
```dockerfile
FROM aws/lambda-python:3.9

# Install additional Python packages
COPY requirements.txt .
RUN pip install -r requirements.txt

# Copy your function code
COPY app.py ${LAMBDA_TASK_ROOT}

# Set the CMD to your handler
CMD ["app.lambda_handler"]
```

## 🔌 Runtime Interface Client

### **What is Runtime Interface Client?**
The Runtime Interface Client enables your container to communicate with the Lambda service.

### **How it Works:**
```
Lambda Service ←→ Runtime Interface Client ←→ Your Function Code
```

### **Key Functions:**
- **Event Reception:** Receives events from Lambda service
- **Response Handling:** Sends function responses back to Lambda
- **Error Management:** Handles and reports function errors
- **Lifecycle Management:** Manages function initialization and cleanup

### **Built into Base Images:**
- AWS base images include Runtime Interface Client
- No additional configuration needed when using base images
- Custom images need to include Runtime Interface Client

## ⚡ Container vs ZIP Deployment Comparison

| Feature | ZIP Deployment | Container Image |
|---------|----------------|-----------------|
| **Package Size** | 50 MB max | 10 GB max |
| **Runtime Control** | AWS managed | You control |
| **Dependencies** | Limited options | Full flexibility |
| **Local Testing** | Limited | Full container testing |
| **Tooling** | AWS-specific | Standard container tools |
| **Cold Start** | Faster | Slightly slower |
| **Complexity** | Simple | More complex |

## 🚫 Important Clarification

### **What Lambda Container Images ARE NOT:**
```
❌ Lambda Container Images ≠ EKS/ECS containers
❌ NOT equivalent to Knative/Cloud Run
❌ NOT running Lambda on Kubernetes
❌ NOT long-running container services
```

### **What Lambda Container Images ARE:**
```
✅ Serverless Lambda functions in custom containers
✅ Event-driven execution model remains same
✅ Auto-scaling and pay-per-invocation model
✅ Lambda service manages container lifecycle
✅ Custom runtime environment for your function
```

## 🎯 Advantages of Lambda Container Images

### **1. Utilize Existing Container Tooling**
```
Benefits:
- Use Docker/Podman for development
- Leverage existing CI/CD pipelines
- Apply container security scanning
- Use familiar container orchestration tools
```

**Example Workflow:**
```bash
# Build container
docker build -t my-lambda-function .

# Test locally
docker run -p 9000:8080 my-lambda-function

# Push to ECR
docker tag my-lambda-function:latest 123456789012.dkr.ecr.us-west-2.amazonaws.com/my-lambda-function:latest
docker push 123456789012.dkr.ecr.us-west-2.amazonaws.com/my-lambda-function:latest
```

### **2. Create Image with What You Need - Faster Startup Time**
```
Custom Optimization:
- Include only required dependencies
- Pre-compile code where possible
- Optimize layer caching
- Remove unnecessary files
```

**Performance Benefits:**
```
Traditional ZIP:
- Download dependencies at runtime
- Install packages during cold start
- Slower initialization

Container Image:
- Dependencies pre-installed
- Code pre-compiled
- Faster cold start (after image pull)
```

### **3. Perform Local Testing with Runtime Interface Emulator**

**Runtime Interface Emulator (RIE):**
- **Local Testing:** Test Lambda functions locally in containers
- **Debug Locally:** Use standard debugging tools
- **Faster Development:** No need to deploy for testing

**Local Testing Setup:**
```bash
# Run container locally with RIE
docker run -p 9000:8080 my-lambda-function

# Test with curl
curl -XPOST "http://localhost:9000/2015-03-31/functions/function/invocations" \
  -d '{"key": "value"}'
```

### **4. 10 GB Package Size vs 50 MB ZIP Deployment**

**Size Comparison:**
```
ZIP Deployment:
- Maximum: 50 MB (compressed)
- Limitations: Small ML models, limited libraries
- Workaround: External dependencies, layers

Container Images:
- Maximum: 10 GB (uncompressed)
- Benefits: Large ML models, comprehensive libraries
- Freedom: Include everything you need
```

**Use Cases for Large Packages:**
```
Examples:
- Machine Learning models (TensorFlow, PyTorch)
- Scientific computing libraries
- Large datasets for processing
- Complex dependency trees
- Multiple language runtimes
```

## 🛠️ Practical Example - Container Image Setup

### **Other NodeJS Example**

https://aws.amazon.com/blogs/aws/new-for-aws-lambda-container-image-support/

### **Step 1: Create Dockerfile**
```dockerfile
FROM aws/lambda-python:3.9

# Install system dependencies
RUN yum update -y && yum install -y \
    gcc \
    python3-dev

# Install Python packages
COPY requirements.txt .
RUN pip install -r requirements.txt

# Copy function code
COPY app.py ${LAMBDA_TASK_ROOT}
COPY models/ ${LAMBDA_TASK_ROOT}/models/

# Set the CMD to your handler
CMD ["app.lambda_handler"]
```

### **Step 2: Build and Test Locally**
```bash
# Build image
docker build -t my-ml-lambda .

# Test locally
docker run -p 9000:8080 my-ml-lambda

# Test function
curl -XPOST "http://localhost:9000/2015-03-31/functions/function/invocations" \
  -d '{"input": "test data"}'
```

### **Step 3: Deploy to AWS**
```bash
# Create ECR repository
aws ecr create-repository --repository-name my-ml-lambda

# Get login token
aws ecr get-login-password --region us-west-2 | docker login --username AWS --password-stdin 123456789012.dkr.ecr.us-west-2.amazonaws.com

# Tag and push
docker tag my-ml-lambda:latest 123456789012.dkr.ecr.us-west-2.amazonaws.com/my-ml-lambda:latest
docker push 123456789012.dkr.ecr.us-west-2.amazonaws.com/my-ml-lambda:latest

# Create Lambda function
aws lambda create-function \
  --function-name my-ml-lambda \
  --package-type Image \
  --code ImageUri=123456789012.dkr.ecr.us-west-2.amazonaws.com/my-ml-lambda:latest \
  --role arn:aws:iam::123456789012:role/lambda-execution-role
```

## 📋 When to Use Container Images vs ZIP

### **Use Container Images When:**
- Package size > 50 MB
- Complex dependencies (ML libraries, scientific packages)
- Custom runtime requirements
- Existing container-based workflows
- Need local testing with full environment
- Multiple language runtimes in one function

### **Use ZIP Deployment When:**
- Simple functions with minimal dependencies
- Quick prototyping and development
- Faster cold start requirements
- Simpler deployment process
- Standard runtime environments sufficient

## ❓ Quick Interview Q&A

**Q: What is Lambda Container Image support?**  
A: Package and deploy Lambda functions as container images up to 10GB, stored in ECR, giving full control over runtime environment

**Q: How does it differ from traditional Lambda?**  
A: Instead of AWS providing container and loading your code, you create custom container that Lambda runs directly

**Q: Does this make Lambda run on EKS/ECS?**  
A: No, it's still serverless Lambda service, just using your custom container image instead of ZIP packages

**Q: What's the Runtime Interface Client?**  
A: Component that enables container to communicate with Lambda service for event handling and responses

**Q: Key advantages of container images?**  
A: 10GB size limit, existing container tooling, local testing, faster startup with pre-installed dependencies

**Q: When to use container images over ZIP?**  
A: Large packages (>50MB), complex dependencies, ML models, custom runtimes, existing container workflows

## 🎯 Key Takeaways

✅ **Custom Containers** = You control the runtime environment instead of AWS  
✅ **ECR Storage** = Container images stored in Elastic Container Registry  
✅ **10GB Limit** = Much larger than 50MB ZIP deployment limit  
✅ **Container Tooling** = Use familiar Docker/container development tools  
✅ **Local Testing** = Runtime Interface Emulator for local development  
✅ **Still Serverless** = Same Lambda service, just custom container execution  

---
💡 **Interview Tip:** "Container images give you full control over Lambda runtime while maintaining serverless benefits"