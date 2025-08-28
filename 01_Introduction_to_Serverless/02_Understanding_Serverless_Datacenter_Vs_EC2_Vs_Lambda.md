# Datacenter vs EC2 vs Lambda - Study Notes

## � Quick Comparison

| Aspect | Datacenter | EC2 | Lambda |
|--------|------------|-----|--------|
| **Management** | Everything | OS + App | Code only |
| **Scaling** | Manual (weeks) | Auto (minutes) | Instant |
| **Cost** | Fixed + upfront | Pay per hour | Pay per execution |
| **Idle Cost** | Always paying | Always paying | **Zero** |
| **Best For** | Legacy/Control | Persistent apps | Event-driven |

## 🎯 Key Points

**Datacenter:** Own servers, high cost, full control  
**EC2:** Virtual machines, hourly billing, cloud flexibility  
**Lambda:** Serverless, pay-per-use, zero management

## 💻 Code Examples

```python
# Lambda Function (Serverless)
def lambda_handler(event, context):
    return {
        'statusCode': 200,
        'body': 'Hello from Lambda!'
    }
```

```bash
# EC2 Launch
aws ec2 run-instances --image-id ami-12345 --instance-type t3.medium
```

## 💰 Cost Example (1M requests/month)
- **Datacenter:** $5,000/month (fixed)
- **EC2:** $60/month (always running)  
- **Lambda:** $12/month (pay-per-execution)

## ❓ Quick Interview Q&A

**Q: When to use each?**  
- **Datacenter:** Legacy apps, full control needed
- **EC2:** Long-running apps (>15min), persistent connections
- **Lambda:** Event-driven, APIs, microservices

**Q: Lambda limitations?**  
- 15-min execution limit, 10GB memory, cold starts

**Q: What's cold start?**  
- ~100-200ms delay when function hasn't run recently

**Q: Scaling speed?**  
- Datacenter: weeks, EC2: minutes, Lambda: instant

**Q: Security responsibility?**  
- Datacenter: everything, EC2: OS+apps, Lambda: just code

## 🎯 Key Takeaways
✅ **Evolution path:** Datacenter → EC2 → Lambda  
✅ **Lambda wins:** Zero idle cost, instant scaling, no management  
✅ **Choose based on:** workload pattern, control needs, cost

---
💡 **Interview Tip:** Lambda = "Pay only when code runs, scale instantly, manage nothing"