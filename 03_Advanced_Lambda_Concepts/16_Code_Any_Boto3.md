# Boto3 Code Analysis - EC2 Policy Enforcement with EventBridge

## 🔍 Code Overview

### **Use Case:**
Automated EC2 instance policy enforcement using EventBridge, Lambda, and SNS to stop non-compliant instances and notify managers.

### **Complete Code:**
```python
import json
import boto3

client = boto3.client('ec2')
snsclient = boto3.client('sns')

def lambda_handler(event, context):
    # Extract EC2 instance ID from EventBridge event
    ec2_instance_id = event['detail']['instance-id']
    
    # Get all tags for the EC2 instance
    tag_response = client.describe_tags(
        Filters=[
            {
                'Name': 'resource-id',
                'Values': [ec2_instance_id]
            },
        ]
    )
    print(tag_response)

    alltags = tag_response['Tags']

    # Check for special exception tag
    flag = 'STOP'
    for item in alltags:
        print(item['Key'])
        if item['Key'] == 'SPECIAL_EXCEPTION':
            flag = 'DONT_STOP'
            break
    print(flag)

    # Stop instance if no exception tag found
    if flag == 'STOP':
        response = client.stop_instances(
            InstanceIds=[ec2_instance_id],
        )

        # Send SNS notification
        snsarn = 'arn:aws:sns:us-east-1:717832118155:test'
        errormsg = "EC2 " + ec2_instance_id + " Stopped"
        snsresponse = snsclient.publish(
            TopicArn=snsarn,
            Message=errormsg,
            Subject='EC2 Violated Company Policy!! Manager will be Notified!!'
        )

    return {
        'statusCode': 200,
        'body': json.dumps('Hello from Lambda!')
    }
```

## 📝 Code Breakdown - Line by Line

### **1. Import and Client Setup:**
```python
import json
import boto3

client = boto3.client('ec2')          # EC2 service client
snsclient = boto3.client('sns')       # SNS service client
```

**Purpose:**
- `json` - Handle JSON data structures
- `boto3` - AWS SDK for Python
- `ec2 client` - Manage EC2 instances (describe tags, stop instances)
- `sns client` - Send notifications via SNS

### **2. Extract Instance ID from Event:**
```python
ec2_instance_id = event['detail']['instance-id']
```

**EventBridge Event Structure:**
```json
{
  "version": "0",
  "id": "event-id",
  "detail-type": "EC2 Instance State-change Notification",
  "source": "aws.ec2",
  "account": "717832118155",
  "time": "2023-09-06T10:30:00Z",
  "region": "us-east-1",
  "detail": {
    "instance-id": "i-1234567890abcdef0",
    "state": "running"
  }
}
```

### **3. Describe Instance Tags:**
```python
tag_response = client.describe_tags(
    Filters=[
        {
            'Name': 'resource-id',
            'Values': [ec2_instance_id]
        },
    ]
)
```

**API Response Structure:**
```json
{
    "Tags": [
        {
            "Key": "Name",
            "ResourceId": "i-1234567890abcdef0",
            "ResourceType": "instance",
            "Value": "WebServer"
        },
        {
            "Key": "Environment",
            "ResourceId": "i-1234567890abcdef0",
            "ResourceType": "instance", 
            "Value": "Production"
        }
    ]
}
```

### **4. Check for Exception Tag:**
```python
alltags = tag_response['Tags']

flag = 'STOP'
for item in alltags:
    print(item['Key'])
    if item['Key'] == 'SPECIAL_EXCEPTION':
        flag = 'DONT_STOP'
        break
```

**Logic:**
- Default action: STOP the instance
- If `SPECIAL_EXCEPTION` tag exists: DON'T STOP
- Loop through all tags to find exception

### **5. Stop Instance (Conditional):**
```python
if flag == 'STOP':
    response = client.stop_instances(
        InstanceIds=[ec2_instance_id],
    )
```

**API Response:**
```json
{
    "StoppingInstances": [
        {
            "InstanceId": "i-1234567890abcdef0",
            "CurrentState": {
                "Code": 64,
                "Name": "stopping"
            },
            "PreviousState": {
                "Code": 16,
                "Name": "running"
            }
        }
    ]
}
```

### **6. Send SNS Notification:**
```python
snsarn = 'arn:aws:sns:us-east-1:717832118155:test'
errormsg = "EC2 " + ec2_instance_id + " Stopped"
snsresponse = snsclient.publish(
    TopicArn=snsarn,
    Message=errormsg,
    Subject='EC2 Violated Company Policy!! Manager will be Notified!!'
)
```

## 🔗 EventBridge Integration Architecture

### **Complete Workflow:**
```
EC2 Instance State Change
        ↓
EventBridge Rule (Trigger)
        ↓
Lambda Function (This Code)
        ↓
Check Tags → Stop Instance → SNS Notification
```

### **EventBridge Rule Configuration:**

**1. Event Pattern:**
```json
{
  "source": ["aws.ec2"],
  "detail-type": ["EC2 Instance State-change Notification"],
  "detail": {
    "state": ["running"]
  }
}
```

**2. Target Configuration:**
```json
{
  "Id": "1",
  "Arn": "arn:aws:lambda:us-east-1:717832118155:function:ec2-policy-enforcer",
  "Input": "EventBridge passes the entire event to Lambda"
}
```

### **EventBridge Event Sources:**
- **EC2 State Changes:** Instance start/stop/terminate
- **CloudWatch Events:** Scheduled events
- **Custom Applications:** Custom event publishing
- **AWS Services:** 90+ AWS services can publish events

## 🔐 Required IAM Roles and Permissions

### **1. Lambda Execution Role:**

**Trust Policy:**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "lambda.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

**Permissions Policy:**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "logs:CreateLogGroup",
        "logs:CreateLogStream", 
        "logs:PutLogEvents"
      ],
      "Resource": "arn:aws:logs:*:*:*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "ec2:DescribeTags",
        "ec2:StopInstances"
      ],
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "sns:Publish"
      ],
      "Resource": "arn:aws:sns:us-east-1:717832118155:test"
    }
  ]
}
```

### **2. EventBridge Service Role:**

**For Lambda Target:**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "lambda:InvokeFunction"
      ],
      "Resource": "arn:aws:lambda:us-east-1:717832118155:function:ec2-policy-enforcer"
    }
  ]
}
```

### **3. EC2 Service Role (for EventBridge):**
EventBridge automatically has permissions to receive EC2 state change events (built-in).

### **4. SNS Topic Permissions:**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::717832118155:role/lambda-execution-role"
      },
      "Action": [
        "sns:Publish"
      ],
      "Resource": "arn:aws:sns:us-east-1:717832118155:test"
    }
  ]
}
```

## ⚙️ Service-by-Service Configuration

### **1. EventBridge Setup:**

**Create Rule:**
```bash
aws events put-rule \
  --name "EC2StateChangeRule" \
  --event-pattern '{
    "source": ["aws.ec2"],
    "detail-type": ["EC2 Instance State-change Notification"],
    "detail": {"state": ["running"]}
  }' \
  --state ENABLED
```

**Add Lambda Target:**
```bash
aws events put-targets \
  --rule "EC2StateChangeRule" \
  --targets "Id"="1","Arn"="arn:aws:lambda:us-east-1:717832118155:function:ec2-policy-enforcer"
```

### **2. Lambda Function Setup:**

**Add EventBridge Permission:**
```bash
aws lambda add-permission \
  --function-name ec2-policy-enforcer \
  --statement-id "allow-eventbridge" \
  --action "lambda:InvokeFunction" \
  --principal "events.amazonaws.com" \
  --source-arn "arn:aws:events:us-east-1:717832118155:rule/EC2StateChangeRule"
```

## 🎯 Use Case Scenarios

### **Scenario 1: Compliant Instance**
```
1. EC2 instance i-12345 starts
2. EventBridge triggers Lambda
3. Lambda checks tags: SPECIAL_EXCEPTION found
4. Lambda skips stopping instance
5. No SNS notification sent
```

### **Scenario 2: Non-Compliant Instance**
```
1. EC2 instance i-67890 starts  
2. EventBridge triggers Lambda
3. Lambda checks tags: No SPECIAL_EXCEPTION
4. Lambda stops instance
5. SNS notification sent to managers
```

### **Scenario 3: Error Handling**
```
1. Instance has no tags
2. tag_response['Tags'] = []
3. Loop finds no SPECIAL_EXCEPTION
4. flag remains 'STOP'
5. Instance gets stopped (policy enforced)
```


## 💡 Code Improvements

### **Enhanced Error Handling:**
```python
def lambda_handler(event, context):
    try:
        ec2_instance_id = event['detail']['instance-id']
        
        # Add validation
        if not ec2_instance_id:
            raise ValueError("Instance ID not found in event")
            
        tag_response = client.describe_tags(
            Filters=[{'Name': 'resource-id', 'Values': [ec2_instance_id]}]
        )
        
        # Rest of the code with try-catch blocks
        
    except KeyError as e:
        print(f"Event structure error: {e}")
        return {'statusCode': 400, 'body': 'Invalid event structure'}
    except Exception as e:
        print(f"Unexpected error: {e}")
        return {'statusCode': 500, 'body': 'Internal error'}
```

### **Environment Variables:**
```python
import os

SNS_TOPIC_ARN = os.environ['SNS_TOPIC_ARN']
EXCEPTION_TAG = os.environ.get('EXCEPTION_TAG', 'SPECIAL_EXCEPTION')
```

## ❓ Quick Interview Q&A

**Q: What triggers this Lambda function?**  
A: EventBridge rule that monitors EC2 instance state changes, specifically when instances enter "running" state

**Q: What AWS services does this code interact with?**  
A: EC2 (describe tags, stop instances), SNS (publish notifications), EventBridge (event source)

**Q: What permissions does Lambda need?**  
A: ec2:DescribeTags, ec2:StopInstances, sns:Publish, plus basic Lambda execution permissions

**Q: How does the tag checking logic work?**  
A: Loops through all instance tags, if SPECIAL_EXCEPTION tag found, sets flag to DON'T_STOP, otherwise stops instance

**Q: What happens if instance has no tags?**  
A: Empty tags array, loop finds no SPECIAL_EXCEPTION, flag remains 'STOP', instance gets stopped

**Q: How is EventBridge integrated?**  
A: EventBridge rule with EC2 state change pattern triggers Lambda, passes event data with instance-id

## 🎯 Key Takeaways

✅ **EventBridge Integration** = EC2 state changes trigger Lambda automatically  
✅ **Policy Enforcement** = Check tags to determine compliance before action  
✅ **Multi-Service** = EC2 + SNS + EventBridge + Lambda working together  
✅ **IAM Permissions** = Each service needs specific permissions for integration  
✅ **Error Handling** = Robust exception handling for production reliability  
✅ **Automation** = Fully automated compliance enforcement without manual intervention  

---
💡 **Interview Tip:** "This demonstrates event-driven architecture using EventBridge to automate EC2 policy enforcement with multi-service integration"
