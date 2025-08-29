# S3 Images Triggering Lambda - Study Notes

## 🚀 S3 Lambda Trigger Function

**Function Name:** `triggerlambdafroms3bucket`

```python
import json
import urllib.parse
import boto3

print('Loading function')

s3 = boto3.client('s3')

def lambda_handler(event, context):
    #print("Received event: " + json.dumps(event, indent=2))

    # Get the object from the event and show its content type
    bucket = event['Records'][0]['s3']['bucket']['name']
    key = urllib.parse.unquote_plus(event['Records'][0]['s3']['object']['key'], encoding='utf-8')
    print(key)
    try:
        response = s3.get_object(Bucket=bucket, Key=key)
        print("CONTENT TYPE: " + response['ContentType'])
        return response['ContentType']
    except Exception as e:
        print(e)
        print('Error getting object {} from bucket {}. Make sure they exist and your bucket is in the same region as this function.'.format(key, bucket))
        raise e
```

## 📋 Code Breakdown

### **1. Imports**
```python
import json               # Handle JSON data
import urllib.parse       # URL decode S3 object keys
import boto3             # AWS SDK for Python
```

### **2. S3 Client Initialization**
```python
s3 = boto3.client('s3')  # Create S3 client outside handler (reused across invocations)
```

### **3. Event Processing**
```python
# Extract bucket name from S3 event
bucket = event['Records'][0]['s3']['bucket']['name']

# Extract and decode object key (file path)
key = urllib.parse.unquote_plus(event['Records'][0]['s3']['object']['key'], encoding='utf-8')
```

### **4. S3 Object Operations**
```python
# Get object metadata and content
response = s3.get_object(Bucket=bucket, Key=key)
print("CONTENT TYPE: " + response['ContentType'])
return response['ContentType']
```

## 🔧 Understanding Boto3

### **What is Boto3?**
- **AWS SDK for Python** - Official library to interact with AWS services
- **Programmatic access** to AWS APIs from Python code
- **Two main interfaces:** Clients (low-level) and Resources (high-level)

### **Boto3 Client vs Resource:**
```python
# Client (low-level) - mirrors AWS service APIs
s3_client = boto3.client('s3')
response = s3_client.get_object(Bucket='bucket', Key='key')

# Resource (high-level) - more Pythonic
s3_resource = boto3.resource('s3')
obj = s3_resource.Object('bucket', 'key')
content = obj.get()
```

### **Why Initialize Outside Handler?**
```python
# ✅ Good - Client reused across invocations
s3 = boto3.client('s3')
def lambda_handler(event, context):
    response = s3.get_object(...)

# ❌ Bad - Creates new client every invocation
def lambda_handler(event, context):
    s3 = boto3.client('s3')  # Slower, more expensive
    response = s3.get_object(...)
```

## 🎯 S3 Lambda Triggers

### **What are Triggers?**
- **Event sources** that automatically invoke Lambda functions
- **S3 triggers Lambda** when objects are created, deleted, or modified

### **How to Create S3 Trigger**
**AWS Console Method:**
1. S3 Console → Select bucket → Properties → Event notifications
2. Create event notification → Select Lambda function
3. Choose event types (e.g., "All object create events")

**Or from Lambda Console:**
1. Lambda Console → Function → Add trigger → S3
2. Select bucket and event type

## 🔑 Required Permissions

**Lambda needs:**
- **s3:GetObject** permission for the bucket
- **Basic Lambda execution** role for CloudWatch logs

```json
{
    "Effect": "Allow",
    "Action": ["s3:GetObject"],
    "Resource": "arn:aws:s3:::my-bucket/*"
}
```

## 🔗 S3 Event Structure

**Sample S3 Event:**
```json
{
  "Records": [
    {
      "s3": {
        "bucket": {
          "name": "my-bucket"
        },
        "object": {
          "key": "images/photo.jpg",
          "size": 1024
        }
      },
      "eventName": "ObjectCreated:Put"
    }
  ]
}
```

## ⚙️ Setup Steps

### **1. Create S3 Bucket**
```bash
# AWS CLI
aws s3 mb s3://my-image-bucket
```

### **2. Create Lambda Function**
- Function name: `triggerlambdafroms3bucket`
- Runtime: Python 3.9
- Role: Lambda execution role with S3 permissions

### **3. Configure S3 Trigger**
```yaml
Event Type: ObjectCreated (All)
Prefix: images/        # Optional: only trigger for specific folder
Suffix: .jpg,.png      # Optional: only trigger for image files
```

### **4. IAM Permissions**
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject"
            ],
            "Resource": "arn:aws:s3:::my-bucket/*"
        }
    ]
}
```

## 🧪 Testing

### **Upload Test File:**
```bash
aws s3 cp image.jpg s3://my-bucket/images/
```

### **Expected Output:**
```
Loading function
images/image.jpg
CONTENT TYPE: image/jpeg
```

## 🎯 Use Cases

### **Image Processing:**
- **Thumbnail generation**
- **Image resizing**
- **Format conversion**
- **Metadata extraction**

### **Data Processing:**
- **Log file analysis**
- **CSV processing**
- **Data validation**
- **File format conversion**

## ❓ Quick Interview Q&A

**Q: How does S3 trigger Lambda?**  
A: S3 sends event notifications when objects are created/deleted/modified

**Q: What's in the S3 event object?**  
A: Records array with bucket name, object key, event type, and metadata

**Q: Why use urllib.parse.unquote_plus()?**  
A: S3 object keys are URL-encoded, need to decode for proper file names

**Q: Where to set S3 trigger?**  
A: S3 bucket properties → Event notifications → Create event notification

**Q: Required IAM permissions?**  
A: Lambda needs s3:GetObject permission for the specific bucket

## 🐛 Common Issues

### **Issue 1: Permission Denied**
```python
# Solution: Add S3 permissions to Lambda execution role
{
    "Effect": "Allow",
    "Action": "s3:GetObject",
    "Resource": "arn:aws:s3:::bucket-name/*"
}
```

### **Issue 2: Key Encoding Problems**
```python
# Always decode the key
key = urllib.parse.unquote_plus(event['Records'][0]['s3']['object']['key'])
```

### **Issue 3: Same Region Requirement**
- S3 bucket and Lambda must be in same region
- Cross-region triggers not supported

## 💡 Best Practices

### **Performance Optimization:**
```python
# Initialize boto3 client outside handler
s3 = boto3.client('s3')  # Reused across invocations

def lambda_handler(event, context):
    # Handler code here
```

### **Error Handling:**
```python
try:
    response = s3.get_object(Bucket=bucket, Key=key)
except ClientError as e:
    if e.response['Error']['Code'] == 'NoSuchKey':
        print(f"Object {key} not found")
    raise e
```

### **Filtering Events:**
```python
# Process only image files
if key.lower().endswith(('.jpg', '.jpeg', '.png', '.gif')):
    # Process image
    pass
```

## 🎯 Key Points
✅ S3 can automatically trigger Lambda on object events  
✅ Event contains bucket name, object key, and metadata  
✅ Always decode object keys with urllib.parse  
✅ Lambda needs S3 GetObject permissions  
✅ Bucket and Lambda must be in same region

---
## 💡 Interview Tips

**Key Talking Points:**
1. **"S3 triggers Lambda automatically when objects are uploaded, deleted, or modified"**
2. **"Boto3 is AWS SDK for Python - initialize clients outside handler for performance"**
3. **"Triggers use push model - AWS services push events to Lambda asynchronously"**
4. **"Event structure contains Records array with S3 bucket and object information"**
5. **"Object keys are URL-encoded and need urllib.parse.unquote_plus() to decode"**
6. **"Lambda execution role needs s3:GetObject permission for the bucket"**

**Common Interview Questions:**
- **"What is boto3?"** → AWS SDK for Python, provides programmatic access to AWS services
- **"Client vs Resource in boto3?"** → Client is low-level API, Resource is high-level Pythonic interface
- **"Why initialize boto3 outside handler?"** → Client reuse improves performance and reduces cost
- **"How to create S3 trigger?"** → S3 Console → Properties → Event notifications OR Lambda Console → Add trigger
- **"What permissions needed?"** → Lambda execution role needs s3:GetObject + S3 invoke permission for Lambda
- **"Types of Lambda triggers?"** → Synchronous (API Gateway), Asynchronous (S3, SNS), Stream-based (DynamoDB)
- **"How to process S3 event?"** → Extract bucket/key from event['Records'][0]['s3']
- **"Best practices?"** → Initialize boto3 outside handler, proper error handling, filter events