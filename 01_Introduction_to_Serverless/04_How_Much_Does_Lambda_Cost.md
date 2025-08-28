# How Much Does Lambda Cost? - Study Notes

## 🆓 AWS Lambda Free Tier (Non-Expiring)

### **Monthly Free Allocation:**
- **1 Million requests** per month
- **400,000 GB-seconds** of compute time per month
- **Never expires** - available every month
- **No commitment required**
- **Zero cost for idle time**

### **Key Pricing Features (2020 Update):**
- **Billing increment:** 1 millisecond (improved from 100ms)
- **More granular billing** = better cost optimization
- **Pay only when code executes**

## 💰 Lambda Pricing Model

### **Two Cost Components:**

#### 1. **Request Charges**
```
- First 1M requests: FREE
- Additional requests: $0.20 per 1M requests
```

#### 2. **Duration Charges**
```
- First 400,000 GB-seconds: FREE
- Additional: $0.0000166667 per GB-second
- Based on: Memory allocated × Execution time
```

### **Pricing Formula:**
```python
# Lambda Cost Calculation
def calculate_lambda_cost(requests, memory_mb, duration_ms):
    # Free tier limits
    free_requests = 1_000_000
    free_gb_seconds = 400_000
    
    # Convert memory to GB
    memory_gb = memory_mb / 1024
    
    # Calculate GB-seconds used
    gb_seconds = (memory_gb * duration_ms / 1000) * requests
    
    # Request cost (after free tier)
    request_cost = max(0, requests - free_requests) * 0.20 / 1_000_000
    
    # Duration cost (after free tier)
    duration_cost = max(0, gb_seconds - free_gb_seconds) * 0.0000166667
    
    total_cost = request_cost + duration_cost
    return total_cost
```

## 📊 Real-World Cost Examples

### **Example 1: Simple API Function**
```yaml
Configuration:
  Memory: 128 MB
  Duration: 100 ms
  Requests: 100,000/month

Calculation:
  GB-seconds: (128/1024) * 0.1 * 100,000 = 1,250 GB-seconds
  Cost: $0 (within free tier)
```

### **Example 2: Data Processing Function**
```yaml
Configuration:
  Memory: 512 MB
  Duration: 2 seconds
  Requests: 1,000,000/month

Calculation:
  GB-seconds: (512/1024) * 2 * 1,000,000 = 1,000,000 GB-seconds
  Request cost: $0 (within 1M free requests)
  Duration cost: (1,000,000 - 400,000) * $0.0000166667 = $10.00
  Total: $10.00/month
```

### **Example 3: High-Volume Application**
```yaml
Configuration:
  Memory: 1024 MB (1 GB)
  Duration: 500 ms
  Requests: 5,000,000/month

Calculation:
  GB-seconds: 1 * 0.5 * 5,000,000 = 2,500,000 GB-seconds
  Request cost: (5,000,000 - 1,000,000) * $0.20/1M = $0.80
  Duration cost: (2,500,000 - 400,000) * $0.0000166667 = $35.00
  Total: $35.80/month
```

## 🧮 Cost Optimization Tips

### **Memory vs Duration Trade-off:**
```python
# Higher memory = faster execution = potentially lower cost
scenario_1 = {
    "memory": 128,    # MB
    "duration": 1000, # ms
    "cost_per_gb_second": 0.0000166667
}

scenario_2 = {
    "memory": 512,    # MB  
    "duration": 250,  # ms (4x faster with 4x memory)
    "cost_per_gb_second": 0.0000166667
}

# Scenario 1: 0.125 GB * 1s = 0.125 GB-seconds
# Scenario 2: 0.5 GB * 0.25s = 0.125 GB-seconds
# Same cost but faster execution!
```

### **Best Practices:**
- **Right-size memory** allocation
- **Optimize code** for faster execution
- **Use provisioned concurrency** only when needed
- **Monitor with CloudWatch** for cost insights

## 🔗 Pricing Calculator

**AWS Lambda Pricing Calculator:**
```
URL: https://s3.amazonaws.com/lambda-tools/pricing-calculator.html

Use for:
✅ Estimate monthly costs
✅ Compare different configurations
✅ Plan capacity and budget
✅ Optimize memory settings
```

## ❓ Quick Interview Q&A

**Q: What's included in Lambda free tier?**  
A: 1M requests + 400,000 GB-seconds monthly, never expires

**Q: How is Lambda billed?**  
A: Per request ($0.20/1M) + duration (memory × time)

**Q: What's the minimum billing increment?**  
A: 1 millisecond (improved from 100ms in 2020)

**Q: Do you pay for idle time?**  
A: No, only pay when code executes

**Q: How to optimize Lambda costs?**  
A: Right-size memory, optimize code, avoid over-provisioning

**Q: What's GB-second?**  
A: Memory (in GB) × execution time (in seconds)

## 💡 Cost Comparison

### **Lambda vs EC2 (1M requests/month):**
```
EC2 t3.micro (always running):
- Cost: ~$8.50/month
- Utilization: Often <1%
- Management: Required

Lambda (typical workload):
- Cost: $0-5/month
- Utilization: 100% when running
- Management: Zero
```

## 🎯 Key Takeaways

✅ **Free tier:** 1M requests + 400K GB-seconds monthly  
✅ **Pricing:** Requests + (Memory × Duration)  
✅ **Billing:** 1ms increments, no idle charges  
✅ **Optimization:** Balance memory vs execution time  
✅ **Tools:** Use pricing calculator for estimates

---
💡 **Interview Tip:** "Lambda's pay-per-execution model with generous free tier makes it extremely cost-effective for most workloads"