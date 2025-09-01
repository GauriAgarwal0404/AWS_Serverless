# HTTP API Vs REST API - Quick Notes

## 🌐 HTTP APIs - What and Why?

**HTTP API:** Newer, faster, cheaper alternative to REST API in API Gateway

**Why HTTP APIs:**
- **70% cost reduction** compared to REST APIs
- **Lower latency** - optimized for performance
- **Simpler configuration** - less complexity
- **Built for modern applications** - serverless-first design

## 📊 API Types Comparison

| Feature | HTTP API | REST API |
|---------|----------|----------|
| **Performance** | ✅ Faster | ⚠️ Slower |
| **Cost** | ✅ 70% cheaper | ❌ More expensive |
| **Features** | ⚠️ Basic | ✅ Full-featured |
| **Maturity** | ⚠️ Newer | ✅ Mature |
| **Use Case** | Simple APIs | Complex APIs |

## 🔌 Integration Comparison

### **HTTP API Integration:**
**Supported Integrations:**
- Lambda functions
- HTTP endpoints
- AWS services (limited)

### **REST API Integration:**
**Supported Integrations:**
- Lambda functions
- HTTP endpoints
- AWS services (all)
- VPC links
- Mock integrations

## 🔒 Security Comparison

| Security Feature | HTTP API | REST API |
|------------------|----------|----------|
| **AWS IAM** | ✅ Yes | ✅ Yes |
| **Cognito User Pools** | ✅ Yes | ✅ Yes |
| **API Keys** | ❌ No | ✅ Yes |
| **Resource Policies** | ❌ No | ✅ Yes |
| **Client Certificates** | ❌ No | ✅ Yes |
| **WAF Integration** | ❌ No | ✅ Yes |

## 👮 Authorizers Comparison

### **HTTP API Authorizers:**
- **JWT Authorizers** - OpenID Connect/OAuth 2.0
- **Lambda Authorizers** - Custom authorization logic

### **REST API Authorizers:**
- **Cognito User Pool Authorizers**
- **Lambda Authorizers** (Token/Request based)
- **IAM Authorization**

## 📈 API Management Comparison

| Management Feature | HTTP API | REST API |
|--------------------|----------|----------|
| **Request Validation** | ❌ Limited | ✅ Full |
| **Request/Response Transformation** | ❌ No | ✅ Yes |
| **Caching** | ❌ No | ✅ Yes |
| **Usage Plans** | ❌ No | ✅ Yes |
| **API Keys** | ❌ No | ✅ Yes |
| **SDK Generation** | ❌ No | ✅ Yes |

## 📊 Monitoring Comparison

### **HTTP API Monitoring:**
- **CloudWatch Metrics** - Basic metrics
- **Access Logging** - Simple format
- **AWS X-Ray** - Distributed tracing

**Available Metrics:**
- Count, Latency, 4XXError, 5XXError

### **REST API Monitoring:**
- **CloudWatch Metrics** - Detailed metrics
- **Access Logging** - Customizable format
- **AWS X-Ray** - Advanced tracing
- **Execution Logging** - Detailed request/response logs

**Available Metrics:**
- Count, Latency, CacheHitCount, CacheMissCount, 4XXError, 5XXError

## 🛠️ Development Comparison

### **HTTP API Development:**
**Benefits:**
- Faster deployment
- Less configuration
- Auto CORS
- OpenAPI 3.0 support

### **REST API Development:**
**Benefits:**
- Full control
- Advanced features
- Backward compatibility
- Extensive customization

## 💰 Cost Comparison

**HTTP API Pricing:**
- $1.00 per million requests
- No additional charges

**REST API Pricing:**
- $3.50 per million requests
- Additional charges for caching, data transfer

**Savings: 70% with HTTP API**

## 🎯 When to Use Which

### **Use HTTP API When:**
✅ Simple serverless applications  
✅ Cost optimization is priority  
✅ Basic authentication is sufficient  
✅ High performance needed  
✅ Modern application development

### **Use REST API When:**
✅ Complex enterprise applications  
✅ Advanced security requirements  
✅ Request/response transformation needed  
✅ Caching required  
✅ Legacy system integration

## ❓ Quick Interview Q&A

**Q: What is HTTP API?**  
A: Newer, faster, cheaper alternative to REST API optimized for modern applications

**Q: Main difference between HTTP API and REST API?**  
A: HTTP API is 70% cheaper and faster but has fewer features than REST API

**Q: Security differences?**  
A: REST API has more security options (API keys, resource policies, WAF), HTTP API has basic IAM and JWT

**Q: Can HTTP API do caching?**  
A: No, caching is only available in REST API

**Q: Which is better for cost optimization?**  
A: HTTP API - 70% cheaper than REST API

**Q: When to use REST API over HTTP API?**  
A: When you need advanced features like caching, request transformation, or complex security

## 🎯 Key Takeaways

✅ **HTTP API** = Faster, cheaper, simpler (modern apps)  
✅ **REST API** = Full-featured, mature (enterprise apps)  
✅ **Cost** = HTTP API 70% cheaper than REST API  
✅ **Security** = REST API has more security options  
✅ **Features** = REST API has advanced management features  
✅ **Performance** = HTTP API optimized for speed

---
💡 **Interview Tip:** "Choose HTTP API for simple, cost-optimized serverless apps; REST API for complex enterprise applications with advanced features"