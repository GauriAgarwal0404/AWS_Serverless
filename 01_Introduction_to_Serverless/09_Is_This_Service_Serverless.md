# Is This Service Serverless? - Study Notes

## 🤔 Serverless Characteristics
- **No server management**
- **Automatic scaling** 
- **Pay-per-use** pricing
- **Built-in high availability**

## 📊 SQS vs Kinesis

### **SQS - ✅ SERVERLESS**
- No infrastructure management
- Automatic scaling
- Pay-per-message
- Zero administration

### **Kinesis Data Streams - ❌ NOT SERVERLESS** 
- Requires shard provisioning
- Manual capacity planning
- Pay for provisioned shards
- Manual scaling

*Note: Kinesis Data Firehose is more serverless-like*

## 🔄 Lambda Alias Traffic Splitting

**Q: Can Lambda Alias split traffic between versions?**  
**A: ✅ TRUE**

**How it works:**
- Aliases point to specific Lambda versions
- Can route traffic percentage between versions
- Example: 90% to stable version, 10% to new version

**Use cases:**
- Canary deployments
- A/B testing
- Blue/Green deployments

## 📋 Quick Service Reference

**✅ Serverless:** Lambda, S3, DynamoDB, SQS, SNS, API Gateway  
**❌ Not Serverless:** Kinesis Data Streams, RDS, EC2, ECS (EC2 type)  
**🔄 Partially:** Aurora Serverless, Fargate, Kinesis Firehose

## ❓ Quick Q&A

**Q: Is SQS serverless?**  
A: Yes - no infrastructure, auto-scaling, pay-per-message

**Q: Why isn't Kinesis Data Streams serverless?**  
A: Requires shard provisioning and capacity planning

**Q: Lambda alias traffic splitting?**  
A: Yes - enables canary deployments and A/B testing

**Q: Using Lambda Alias you can split traffic between different lambda Versions?**
A:	You selected "True" because Lambda Aliases allow you to manage multiple versions of a Lambda function and control the distribution of incoming requests between those versions, effectively enabling you to split traffic. This practice is useful for gradual rollouts and testing new versions without impacting all users at once.

## 🎯 Key Points
✅ **SQS** = Serverless (no infrastructure)  
✅ **Kinesis Streams** = Not serverless (shard management)  
✅ **Lambda aliases** = Traffic splitting between versions  
✅ **Serverless** = No servers + Auto scaling + Pay-per-use

---
💡 **Interview Tip:** "SQS is serverless with zero management, Kinesis Data Streams requires shard provisioning"

