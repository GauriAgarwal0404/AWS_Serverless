# Understanding IAM: Roles, Policies, Users & Groups - Study Notes

## 🔐 What is Role-Based Access?

**Role-Based Access Control (RBAC):**
- **Assign permissions** to roles, not directly to users/services
- **Services assume roles** to get temporary permissions
- **Principle of least privilege** - only necessary permissions

## 📜 AWS Policies

**What are AWS Policies?**
- **JSON documents** that define permissions
- **Specify actions** allowed/denied on AWS resources
- **Attached to roles, users, or groups**

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::my-bucket/*"
        }
    ]
}
```

## 🎭 Why IAM Roles?

### **Key Reason: AWS Services Need Access to Other Services**

**Problem:** EC2 instance needs to access S3, DynamoDB, etc.
**Solution:** IAM Roles provide temporary, secure access

### **Critical Rule:**
> **Policies cannot be attached directly to AWS services (EC2, Lambda)**  
> **They must be included in IAM Roles, then roles attached to services**

## 📊 Real-World Example (From Screenshot)

### **Scenario: EC2 Application Accessing Multiple Services**

```yaml
EC2 Instance:
  - Runs application
  - Needs to: Read from S3, Read/Write DynamoDB
  
IAM Role attached to EC2:
  - Policy 1: S3 access permissions
  - Policy 2: DynamoDB read permissions  
  - Policy 3: DynamoDB write permissions

Flow:
  EC2 → Assumes Role → Gets Policies → Access S3 & DynamoDB
```

### **Example Policies for EC2 Role:**
```json
// Policy to access S3
{
    "Effect": "Allow",
    "Action": ["s3:GetObject", "s3:ListBucket"],
    "Resource": ["arn:aws:s3:::my-bucket", "arn:aws:s3:::my-bucket/*"]
}

// Policy to read DynamoDB
{
    "Effect": "Allow", 
    "Action": ["dynamodb:GetItem", "dynamodb:Query"],
    "Resource": "arn:aws:dynamodb:region:account:table/MyTable"
}

// Policy to write DynamoDB
{
    "Effect": "Allow",
    "Action": ["dynamodb:PutItem", "dynamodb:UpdateItem"], 
    "Resource": "arn:aws:dynamodb:region:account:table/MyTable"
}
```

## 👥 IAM Role vs IAM User

| Aspect | IAM Role | IAM User |
|--------|----------|----------|
| **Purpose** | **AWS services** to access other services | **Human users** to access AWS |
| **Credentials** | **Temporary** (assume role) | **Permanent** (access keys) |
| **Usage** | EC2, Lambda, ECS assume roles | Developers, admins login |
| **Best For** | Service-to-service access | Human access |
| **Security** | More secure (temporary) | Less secure (permanent keys) |

## 🔄 How Roles Work

### **Role Assumption Process:**
1. **EC2 instance** starts with attached IAM role
2. **AWS STS** (Security Token Service) provides temporary credentials
3. **Application** uses temporary credentials to access S3/DynamoDB
4. **Credentials expire** and auto-refresh

```python
# In EC2 application - no hardcoded credentials needed!
import boto3

# boto3 automatically uses IAM role credentials
s3 = boto3.client('s3')
dynamodb = boto3.client('dynamodb')

# Access granted based on role policies
s3.get_object(Bucket='my-bucket', Key='file.txt')
dynamodb.get_item(TableName='MyTable', Key={'id': {'S': '123'}})
```

## ❓ Quick Interview Q&A

**Q: Can you attach policies directly to EC2?**  
A: No, policies must be in IAM roles, then roles attached to EC2

**Q: What's the difference between IAM Role and User?**  
A: Roles for AWS services (temporary), Users for humans (permanent)

**Q: Why use roles instead of hardcoded credentials?**  
A: Security - temporary credentials, automatic rotation, no credential management

**Q: How does EC2 access other services securely?**  
A: Attach IAM role with required policies, AWS handles credential management

**Q: What happens when role policies change?**  
A: Changes take effect immediately for new API calls

## 🎯 Key Points

✅ **Policies** define permissions in JSON format  
✅ **Roles** are containers for policies  
✅ **Services assume roles** to get temporary access  
✅ **Cannot attach policies directly** to AWS services  
✅ **Roles are more secure** than hardcoded credentials

## 🏗️ Best Practices

**Security:**
- Use least privilege principle
- Separate roles for different functions
- Regular policy reviews

**Serverless:**
- Lambda execution roles for accessing other services
- API Gateway roles for backend access
- Step Functions roles for workflow permissions

---
💡 **Interview Tip:** "IAM Roles provide secure, temporary access for AWS services to communicate with each other without hardcoded credentials"