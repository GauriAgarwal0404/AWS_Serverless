# Creating and Testing Our Very First Lambda - Study Notes

## 🚀 Basic Lambda Function

```python
import json

def lambda_handler(event, context):
    print(event)                        # Input data
    print(context.invoked_function_arn) # Function ARN
    print(context.memory_limit_in_mb)
    return {
        'statusCode': 200,
        'body': json.dumps('Hello from Lambda!')
    }
```

## 📋 Key Components

**Handler Function:**
- Entry point: `lambda_handler(event, context)`
- Always requires 2 parameters(arguments)
- lambda_handler is main function, everytime lambda gets invoked, this function is executed first

**Event Parameter:**
- It is input parameter that we can pass(application inputs)
- Input data from trigger (API Gateway, S3, etc.)
- JSON object varies by source

Event like:
```python
{
    "key1": "India",
    "key2": "USA",
    "key3": "UK",
}
```

**Context Parameter:**
- system information like memory limit, log group
```python
context.function_name           # Function name
context.aws_request_id          # Unique request ID
context.remaining_time_in_millis # Time left
context.invoked_function_arn    # Function ARN
```

**Response Format:**
- `statusCode`: HTTP status (200 = success)
- `body`: JSON string (use `json.dumps()`)

## 🔧 Creation Steps

1. **AWS Console** → Lambda → Create function
2. **Configure:** Name, Runtime (Python 3.9), Role
3. **Deploy:** Paste code or upload ZIP
4. **Test:** Create test event and execute

## ⚙️ Configuration Settings

**From Lambda Console → Configuration Tab:**
- **Memory:** 128 MB to 10,008 MB (affects CPU allocation)
- **Timeout:** 1 second to 15 minutes maximum
- **Environment Variables:** Key-value pairs for configuration
- **Execution Role:** IAM permissions for AWS service access

```python
# Access memory limit in code:
print(f"Memory: {context.memory_limit_in_mb} MB")
print(f"Time remaining: {context.get_remaining_time_in_millis()} ms")
```

## 🧪 Testing

**Basic Test Event:**
```json
{"name": "AWS Developer", "test": true}
```

**Expected Response:**
```json
{
    "statusCode": 200,
    "body": "\"Hello from Lambda!\""
}
Function logs: 
<event variable is coming under function logs>
```

## ❓ Quick Interview Q&A

**Q: Lambda handler parameters?**  
A: `event` (input data) and `context` (runtime info)

**Q: Why json.dumps() in response?**  
A: API Gateway expects string, not Python object

**Q: How to check remaining time?**  
A: `context.get_remaining_time_in_millis()`

**Q: Required response fields?**  
A: `statusCode` and `body` for API Gateway

## 🐛 Common Mistakes

```python
# Wrong:
return {'body': {'message': 'hello'}}

# Correct:
return {'body': json.dumps({'message': 'hello'})}
```

## 🎯 Key Points
✅ Handler function is the entry point  
✅ Event contains trigger data  
✅ Context provides runtime metadata  
✅ Response must be properly formatted  
✅ Use json.dumps() for response body

---
## 💡 Interview Tips

**Key Talking Points:**
1. **"Lambda handler is the entry point - it always takes event and context parameters"**
2. **"Event contains input data from the trigger source like API Gateway or S3"**
3. **"Context provides runtime information like memory limit and remaining execution time"**
4. **"For API Gateway integration, response must have statusCode and JSON string body"**
5. **"Memory and timeout can be configured in the Configuration tab - memory affects CPU allocation"**

**Common Interview Questions:**
- "Walk me through creating a Lambda function" → Console → Create → Configure → Deploy → Test
- "What's the difference between event and context?" → Event is input data, context is runtime metadata
- "How do you handle timeouts?" → Use `context.get_remaining_time_in_millis()` to check remaining time
- "Why use json.dumps()?" → API Gateway expects string response, not Python objects