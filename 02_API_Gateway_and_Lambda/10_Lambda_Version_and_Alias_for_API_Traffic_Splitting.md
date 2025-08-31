# Lambda Version and Alias for API Traffic Splitting - Quick Notes

## 🏷️ Lambda Version and Alias

**Lambda Version:**
- Immutable snapshot of Lambda function code
- Each version has unique ARN
- Cannot modify published version

**Lambda Alias:**
- Pointer to Lambda version(s)
- Mutable - can update to point to different versions
- Same alias can point to multiple versions for traffic splitting

**Key Benefit:**
Same Alias can point to more than 1 version, then divert traffic to 2 versions (e.g., 50% each) and hit this alias through API Gateway

## 🔄 Traffic Splitting Example

**Alias Configuration:**
```
PROD Alias:
  - Version 1: 50% traffic
  - Version 2: 50% traffic
```

**API Gateway Integration:**
```
API Gateway → PROD Alias → {50% v1, 50% v2}
```

## 🔵🟢 Blue-Green Deployment

**What is Blue-Green Deployment:**
Deployment strategy where you run two identical production environments (Blue and Green)

**Process:**
1. **Deploy new version** (Green) while old version (Blue) handles traffic
2. **Divert traffic** to existing application during deployment
3. **Split traffic** between Blue and Green versions for testing
4. **Switch all traffic** to new version once everything is fine

**Lambda Blue-Green Flow:**
```
Step 1: Blue (v1) = 100% traffic
Step 2: Deploy Green (v2) = 0% traffic
Step 3: Split traffic: Blue (v1) = 90%, Green (v2) = 10%
Step 4: Gradually shift: Blue (v1) = 50%, Green (v2) = 50%
Step 5: Full switch: Blue (v1) = 0%, Green (v2) = 100%
```

## 💡 Practical Example

**Version Management:**
```
Version 1: Original function
Version 2: Updated function
PROD Alias: Points to both versions
```

**Traffic Split via Alias:**
```json
{
  "AliasConfiguration": {
    "Name": "PROD",
    "FunctionVersion": "1",
    "RoutingConfig": {
      "AdditionalVersionWeights": {
        "2": 0.5
      }
    }
  }
}
```

## ❓ Quick Interview Q&A

**Q: What is Lambda Version?**  
A: Immutable snapshot of Lambda function code with unique ARN

**Q: What is Lambda Alias?**  
A: Mutable pointer to Lambda version(s) that can split traffic

**Q: Can one alias point to multiple versions?**  
A: Yes, same alias can point to multiple versions for traffic splitting

**Q: What is Blue-Green deployment?**  
A: Deployment strategy with two identical environments for zero-downtime deployments

**Q: How does Blue-Green work with Lambda?**  
A: Deploy new version, gradually shift traffic using alias, then full switch

**Q: Benefit of using Alias with API Gateway?**  
A: API Gateway calls alias, which can distribute traffic across versions

## 🎯 Key Takeaways

✅ **Version** = Immutable function snapshot  
✅ **Alias** = Mutable pointer with traffic splitting capability  
✅ **Traffic Split** = One alias → multiple versions with percentage  
✅ **Blue-Green** = Zero-downtime deployment strategy  
✅ **API Gateway** = Calls alias for automatic traffic distribution

---
💡 **Interview Tip:** "Alias enables traffic splitting between Lambda versions for safe deployments"