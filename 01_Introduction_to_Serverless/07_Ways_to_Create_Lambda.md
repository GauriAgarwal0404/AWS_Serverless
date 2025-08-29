# Ways to Create Lambda - Study Notes

## 🛠️ Three Main Ways to Create Serverless

### **1. AWS Console**

### **2. Infrastructure as Code (IaC)**

#### **CloudFormation**
- **AWS native** infrastructure as code
- **JSON/YAML** templates
- **Declarative** resource definition

#### **SAM (Serverless Application Model)**
- **Extension of CloudFormation** for serverless
- **Simplified syntax** for Lambda, API Gateway, DynamoDB
- **Built-in best practices** for serverless

#### **CDK (Cloud Development Kit)**
- **Code-based** infrastructure definition
- **Multiple languages:** TypeScript, Python, Java, C#
- **Object-oriented** approach to infrastructure

### **3. AWS CLI**
- **Command-line interface** for scripting
- **Programmatic** Lambda creation
- **Good for:** Automation, CI/CD pipelines

## 📋 Quick Comparison

| Method | Best For | Pros | Cons |
|--------|----------|------|------|
| **Console** | Learning | Easy, Visual | Manual, Not scalable |
| **CloudFormation** | Enterprise | Native, Comprehensive | Verbose JSON/YAML |
| **SAM** | Serverless Apps | Simple, Best practices | Limited to serverless |
| **CDK** | Developers | Code-based, Type-safe | Learning curve |
| **CLI** | Automation | Scriptable, Fast | Command complexity |


## ❓ Quick Interview Q&A

**Q: What are the main ways to create Lambda functions?**  
A: AWS Console (manual), Infrastructure as Code (CloudFormation/SAM/CDK), and AWS CLI

**Q: When would you use SAM over CloudFormation?**  
A: SAM for serverless-specific applications with simplified syntax and built-in best practices

**Q: What's the advantage of CDK?**  
A: Code-based infrastructure with programming language features like loops, conditions, and type safety

**Q: Which method for production deployments?**  
A: Infrastructure as Code (CloudFormation/SAM/CDK) for repeatability and version control

## 🎯 Key Points
✅ **Console:** Easy learning, manual process  
✅ **CloudFormation:** AWS native, comprehensive  
✅ **SAM:** Serverless-focused, simplified  
✅ **CDK:** Code-based, developer-friendly  
✅ **CLI:** Automation, scripting

---
💡 **Interview Tip:** "For production serverless applications, use Infrastructure as Code (SAM/CDK) for repeatability, version control, and team collaboration"