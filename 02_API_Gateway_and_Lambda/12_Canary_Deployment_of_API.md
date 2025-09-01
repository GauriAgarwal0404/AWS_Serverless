# Canary Deployment of API - Quick Notes

## 🐦 What is Canary?

**Canary:** Deployment strategy that releases new version to small subset of users before full rollout

**Origin:** Named after "canary in coal mine" - early warning system

**Purpose:**
- Test new version with minimal risk
- Detect issues early
- Gradual rollout strategy

## 🚀 API Gateway Canary Deployment

**What:** API Gateway feature that splits traffic between stable and new API versions

**How it works:**
- Deploy new API version alongside existing one
- Route small percentage of traffic to new version
- Monitor performance and errors
- Gradually increase traffic or rollback

**Traffic Split Example:**
```
Stable Version: 90% traffic
Canary Version: 10% traffic
```

## 🔧 Canary Deployment Process

**Steps:**
1. **Deploy Canary Stage** - Create new stage with updated API
2. **Configure Traffic Split** - Set percentage (e.g., 10% canary, 90% stable)
3. **Monitor Metrics** - Watch errors, latency, success rates
4. **Promote or Rollback** - Move to 100% canary or revert to stable

**API Gateway Canary Settings:**
```
Stage: prod
Canary Settings:
  - Deployment ID: new-deployment-123
  - Traffic: 10%
  - Stage Variables: can be different
```

## 📊 Benefits of Canary Deployment

**Risk Mitigation:**
- Limited blast radius (only affects small percentage)
- Early error detection
- Quick rollback capability

**Testing in Production:**
- Real user traffic testing
- Performance validation
- Gradual confidence building

## 💡 Practical Example

**Before Canary:**
```
All traffic → Stable API (v1.0)
```

**During Canary:**
```
90% traffic → Stable API (v1.0)
10% traffic → Canary API (v2.0)
```

**After Successful Canary:**
```
All traffic → New Stable API (v2.0)
```

## ❓ Quick Interview Q&A

**Q: What is Canary deployment?**  
A: Deployment strategy that releases new version to small subset of users first

**Q: Why is it called Canary?**  
A: Named after canary in coal mine - early warning system for problems

**Q: What is API Gateway Canary deployment?**  
A: Feature that splits traffic between stable and new API versions for testing

**Q: How does traffic splitting work in API Gateway Canary?**  
A: Configure percentage split (e.g., 10% new version, 90% stable version)

**Q: Benefits of Canary deployment?**  
A: Risk mitigation, early error detection, real user testing, quick rollback

**Q: What do you monitor during Canary deployment?**  
A: Error rates, latency, success rates, user feedback

## 🎯 Key Takeaways

✅ **Canary** = Small-scale deployment for early testing  
✅ **Traffic Split** = Percentage-based routing between versions  
✅ **Risk Reduction** = Limited blast radius with quick rollback  
✅ **API Gateway** = Built-in canary deployment feature  
✅ **Monitoring** = Essential for canary success validation

---
💡 **Interview Tip:** "Canary deployment reduces risk by testing new versions with small percentage of users first"