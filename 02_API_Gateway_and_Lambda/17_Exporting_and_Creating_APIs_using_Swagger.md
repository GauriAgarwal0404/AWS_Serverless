# Exporting and Creating APIs using Swagger - Quick Notes

## 📤 Exporting API using Swagger

**Purpose:** Export existing API Gateway as Swagger/OpenAPI specification

**Steps:**
1. Go to API Gateway Console
2. Select your API
3. Click "Stages" → Select stage (e.g., prod)
4. Click "Export" → Choose format (Swagger + API Gateway Extensions)
5. Download JSON/YAML file

**Export Options:**
- **Swagger** - Standard API specification
- **Swagger + API Gateway Extensions** - Includes AWS-specific configurations

## 📥 Creating API using Swagger

**Purpose:** Create new API Gateway from Swagger specification

**Steps:**
1. API Gateway Console → "Create API"
2. Choose "REST API" → "Import from Swagger or OpenAPI 3"
3. Upload Swagger file or paste content
4. Click "Import"
5. Deploy to stage

## 💡 Hands-On Example

**Sample Swagger Specification:**
```yaml
swagger: "2.0"
info:
  title: "User Management API"
  version: "1.0.0"
  description: "API for managing users"

basePath: "/prod"

paths:
  /users:
    get:
      summary: "Get all users"
      produces:
        - "application/json"
      responses:
        200:
          description: "Success"
          schema:
            type: "object"
            properties:
              users:
                type: "array"
                items:
                  type: "string"
      x-amazon-apigateway-integration:
        type: "aws_proxy"
        httpMethod: "POST"
        uri: "arn:aws:apigateway:us-east-1:lambda:path/2015-03-31/functions/arn:aws:lambda:us-east-1:123456789:function:getUsersFunction/invocations"
        
  /users/{id}:
    get:
      summary: "Get user by ID"
      parameters:
        - name: "id"
          in: "path"
          required: true
          type: "string"
      responses:
        200:
          description: "Success"
      x-amazon-apigateway-integration:
        type: "aws_proxy"
        httpMethod: "POST"
        uri: "arn:aws:apigateway:us-east-1:lambda:path/2015-03-31/functions/arn:aws:lambda:us-east-1:123456789:function:getUserFunction/invocations"
```

## 🔧 Key Components Explained

**Basic Information:**
```yaml
swagger: "2.0"              # Swagger version
info:
  title: "API Name"         # API display name
  version: "1.0.0"          # API version
  description: "API desc"   # API description
basePath: "/prod"           # Base path for all endpoints
```

**Path Definition:**
```yaml
paths:
  /users:                   # Resource endpoint
    get:                    # HTTP method
      summary: "Description" # Endpoint description
      responses:            # Response definitions
        200:                # HTTP status code
          description: "Success"
```

**AWS Integration:**
```yaml
x-amazon-apigateway-integration:
  type: "aws_proxy"         # Lambda proxy integration
  httpMethod: "POST"        # Always POST for Lambda
  uri: "lambda-arn"         # Lambda function ARN
```

## 🔄 Export vs Import Process

**Export Process:**
```
Existing API Gateway → Export → Swagger File → Version Control
```

**Import Process:**
```
Swagger File → Import → New API Gateway → Deploy to Stage
```

**Use Cases:**
- **Export:** Backup, version control, documentation
- **Import:** Clone APIs, multi-environment setup, Infrastructure as Code

## 💼 Practical Workflow

**1. Export from Production:**
```bash
# AWS CLI Export
aws apigateway get-export \
  --rest-api-id abc123 \
  --stage-name prod \
  --export-type swagger \
  --parameters extensions='apigateway' \
  swagger-export.json
```

**2. Modify for New Environment:**
```yaml
# Update Lambda ARNs for different account/region
uri: "arn:aws:apigateway:us-west-2:lambda:path/..."
```

**3. Import to New Environment:**
```bash
# AWS CLI Import
aws apigateway import-rest-api \
  --body file://swagger-export.json
```

## ❓ Quick Interview Q&A

**Q: How to export API Gateway as Swagger?**  
A: Go to Stages → Export → Choose Swagger + API Gateway Extensions format

**Q: How to create API from Swagger file?**  
A: Create API → Import from Swagger → Upload file → Import → Deploy

**Q: What is x-amazon-apigateway-integration?**  
A: AWS-specific extension in Swagger for defining backend integrations

**Q: Difference between Swagger and Swagger + Extensions?**  
A: Extensions include AWS-specific configurations like Lambda integrations

**Q: Why export APIs as Swagger?**  
A: Backup, version control, multi-environment deployment, Infrastructure as Code

**Q: Can you modify exported Swagger before importing?**  
A: Yes, you can update ARNs, configurations for different environments

## 🎯 Key Takeaways

✅ **Export** = API Gateway → Swagger file for backup/sharing  
✅ **Import** = Swagger file → New API Gateway creation  
✅ **Extensions** = AWS-specific configurations in Swagger  
✅ **Workflow** = Export → Modify → Import for multi-environment  
✅ **IaC** = Infrastructure as Code for API management

---
💡 **Interview Tip:** "Swagger enables API portability and Infrastructure as Code for API Gateway"