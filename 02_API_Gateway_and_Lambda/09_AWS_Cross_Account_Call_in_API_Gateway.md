# AWS Cross Account Call in API Gateway - Quick Notes

## 🔄 Cross Account Integration

**What:** API Gateway in Account A calls Lambda in Account B

**Key Steps:**
- Copy Lambda ARN from target account
- Add permission to Lambda for API Gateway access
- Configure integration in API Gateway

## 🔧 Setup Process

**1. Get Lambda ARN from Other Account:**
```
arn:aws:lambda:us-east-1:ACCOUNT-B:function:MyFunction
```

**2. Add Permission to Lambda (Account B):**
```bash
aws lambda add-permission \
  --function-name MyFunction \
  --statement-id api-gateway-cross-account \
  --action lambda:InvokeFunction \
  --principal apigateway.amazonaws.com \
  --source-arn "arn:aws:execute-api:region:ACCOUNT-A:api-id/*/*/*"
```

**3. Configure API Gateway Integration (Account A):**
- Integration Type: AWS Service
- AWS Service: Lambda
- Lambda Function ARN: Cross-account ARN

## 🛠️ AWS CLI Options

**Cloud9 for AWS CLI:**
- Pre-configured AWS CLI environment
- No credential setup needed
- Integrated terminal for commands

**Local AWS CLI:**
- Requires credential configuration
- Manual setup of access keys

## 💡 Permission Example

**Lambda Resource Policy (Account B):**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "apigateway.amazonaws.com"
      },
      "Action": "lambda:InvokeFunction",
      "Resource": "arn:aws:lambda:us-east-1:ACCOUNT-B:function:MyFunction",
      "Condition": {
        "StringEquals": {
          "AWS:SourceAccount": "ACCOUNT-A"
        }
      }
    }
  ]
}
```

## ❓ Quick Interview Q&A

**Q: What is cross-account API Gateway call?**  
A: API Gateway in one account calling Lambda function in different account

**Q: What permission is needed for cross-account calls?**  
A: Lambda function must grant invoke permission to API Gateway service

**Q: How to add permission to Lambda for cross-account access?**  
A: Use `aws lambda add-permission` command with API Gateway principal

**Q: What is Cloud9 benefit for AWS CLI?**  
A: Pre-configured environment with AWS CLI and credentials already set up

**Q: What principal is used for API Gateway in Lambda permissions?**  
A: `apigateway.amazonaws.com`

## 🎯 Key Takeaways

✅ **Cross Account** = API Gateway (Account A) → Lambda (Account B)  
✅ **Permission Required** = Lambda must allow API Gateway invoke  
✅ **AWS CLI** = Use Cloud9 for easy setup or local CLI  
✅ **Principal** = apigateway.amazonaws.com for Lambda permissions  
✅ **ARN Format** = Full cross-account Lambda ARN needed

---
💡 **Interview Tip:** "Cross-account calls require explicit Lambda permission for API Gateway service principal"