# Lambda Scaling, Concurrency, And Provisioned Concurrency - Quick Notes

## 🚀 1) How Does Lambda Scale?

### **Automatic Scaling Behavior:**
- **Event-Driven:** Lambda scales automatically based on incoming requests
- **Concurrent Executions:** Each request gets its own execution environment
- **No Manual Configuration:** AWS handles scaling infrastructure automatically
- **Pay Per Use:** Only pay for actual execution time

### **Scaling Process:**
```
Request 1 → Lambda Instance 1
Request 2 → Lambda Instance 2  
Request 3 → Lambda Instance 3
...
Request N → Lambda Instance N
```

**Key Points:**
- Each concurrent request = separate execution environment
- AWS manages the underlying infrastructure
- Scales from 0 to thousands of concurrent executions
- Automatic scale-down when requests decrease

## 🥶 2) Cold Start

### **What is Cold Start?**
Cold start occurs when Lambda creates a new execution environment for a function

### **Cold Start Process:**
```
1. Download Function Code
2. Initialize Runtime Environment  
3. Run Initialization Code (outside handler)
4. Execute Handler Function
```

### **Cold Start Triggers:**
- **First Invocation:** Function invoked for first time
- **Scaling Up:** Need more concurrent execution environments
- **Inactivity:** Function idle for ~15 minutes, containers destroyed
- **Code Updates:** New deployment invalidates existing containers

### **Cold Start Impact:**
```
Cold Start Latency:
- Java: 100-1000ms
- Python: 100-300ms  
- Node.js: 50-200ms
- Go: 50-100ms
```

### **Cold Start vs Warm Start:**
```
Cold Start: Download → Initialize → Run Init Code → Execute Handler
Warm Start: Execute Handler (reuse existing environment)
```

## 📊 3) Limit of Scaling for Concurrent Invocations

### **Default Concurrency Limits:**
- **Account Level:** 1000 concurrent executions per AWS account per region
- **Regional Limit:** Applies to entire AWS region
- **Shared Pool:** All Lambda functions share this 1000 limit

### **Concurrency Calculation:**
```
Concurrent Executions = (Invocations per second) × (Average Duration in seconds)

Example:
- 100 requests/second
- Average duration: 2 seconds  
- Concurrent executions = 100 × 2 = 200
```

### **When Limit is Reached:**
- **Throttling:** New invocations get throttled
- **Error Response:** HTTP 429 (Too Many Requests)
- **Retry Behavior:** AWS SDKs automatically retry throttled requests

## ⚡ 4) Rate of Scaling

### **Initial Burst:**
- **First Minute:** Up to 1000 concurrent executions immediately
- **Burst Capacity:** Available for sudden traffic spikes

### **Sustained Scaling:**
- **After Burst:** +500 executions every minute
- **Gradual Increase:** Prevents overwhelming downstream services

### **Scaling Timeline:**
```
Time 0:     0 → 1000 executions (Burst)
Minute 1:   1000 → 1500 executions (+500)
Minute 2:   1500 → 2000 executions (+500)
Minute 3:   2000 → 2500 executions (+500)
```

### **Scaling Example:**
```
Traffic Pattern:
- 0-1 min: 0 to 1000 requests/sec ✅ (Burst handles)
- 1-2 min: Need 1500 concurrent ✅ (+500 scaling)
- 2-3 min: Need 2000 concurrent ✅ (+500 scaling)
```

## 🎛️ 5) Two Ways to Set Concurrency

### **A) Unreserved Account Concurrency (Default: 1000)**

**How it Works:**
- **Shared Pool:** 1000 concurrent executions shared across ALL functions
- **First Come, First Served:** Functions compete for available concurrency
- **Dynamic Allocation:** Functions can use any available capacity

**Example Scenario:**
```
Account has 3 Lambda functions:
- Function A: Currently using 300 concurrent executions
- Function B: Currently using 400 concurrent executions  
- Function C: Can use remaining 300 concurrent executions
- Total: 300 + 400 + 300 = 1000 (Account limit)
```

**Problem with Unreserved:**
```
If Function A suddenly needs 800 concurrent executions:
- Function A: Gets 800
- Function B: Gets 200 (reduced from 400)
- Function C: Gets 0 (throttled)
- Result: Functions B & C get impacted
```

### **B) Reserved Concurrency**

**Purpose:** Guarantee and limit concurrency for specific functions

**How it Works:**
- **Dedicated Pool:** Reserve specific amount for a function
- **Guaranteed Capacity:** Function always has access to reserved amount
- **Isolation:** Other functions cannot use this reserved capacity

**Example Configuration:**
```
Account Limit: 1000
- Function A (Critical): Reserved = 400
- Function B (Important): Reserved = 300  
- Function C (Normal): Unreserved = 300 (remaining)
```

**Benefits:**
- **Predictable Performance:** Critical functions never get throttled
- **Cost Control:** Prevents one function from consuming all capacity
- **Isolation:** Function performance doesn't affect others

**When Reserved Concurrency is Exceeded:**
```
Function A has 400 reserved concurrency:
- Requests 1-400: ✅ Executed normally
- Requests 401+: ❌ Throttled (Error 429)
```

## 🔥 6) Provisioned Concurrency - Super Fast Scaling Solution

### **Problem: 0 to 1000 in 1 Second**
```
Challenge: Need 1000 concurrent executions immediately
Standard Scaling: Burst gives 1000, but cold starts cause delays
Result: High latency due to cold start penalties
```

### **Provisioned Concurrency to the Rescue**

**What is Provisioned Concurrency?**
- **Pre-Warmed Environments:** AWS keeps execution environments ready
- **No Cold Starts:** Environments already initialized and waiting
- **Instant Scaling:** Handle traffic spikes without initialization delay

### **How Provisioned Concurrency Works:**

#### **Pre-initialized Execution Environments:**
```
Normal Lambda:
Request → Create Environment → Download Code → Initialize → Execute
(Cold Start Delay: 100-1000ms)

Provisioned Concurrency:
Request → Execute (Environment already ready)
(No Cold Start: <10ms)
```

#### **AWS Keeps Assigned Capacity "Warm":**
```
Provisioned Concurrency = 100:
- AWS maintains 100 warm environments 24/7
- Environments are pre-initialized and ready
- Code already downloaded and runtime initialized
- Handler ready to execute immediately
```

#### **No Throttling Due to Super Fast Scaling:**
```
Scenario: 0 to 1000 concurrent invocations in 1 second

Without Provisioned Concurrency:
- Time 0-1s: Cold starts cause 100-1000ms delays
- Some requests might get throttled during scaling
- Inconsistent response times

With Provisioned Concurrency (1000):
- Time 0-1s: All 1000 requests served immediately
- No cold starts, no throttling
- Consistent low latency
```

### **Provisioned Concurrency Configuration:**
```
Function: my-critical-api
- Reserved Concurrency: 1000
- Provisioned Concurrency: 500
- Auto Scaling: Based on utilization

Result:
- 500 environments always warm and ready
- Can scale to 1000 total (additional 500 on-demand)
- Super fast response for first 500 concurrent requests
```

### **Use Cases for Provisioned Concurrency:**
- **Latency-Sensitive Applications:** APIs requiring <100ms response
- **Traffic Spikes:** Black Friday, product launches
- **Real-Time Applications:** Gaming, trading systems
- **Critical Business Functions:** Payment processing

### **Cost Consideration:**
```
Provisioned Concurrency Pricing:
- Pay for provisioned capacity even when not used
- Higher cost but guaranteed performance
- Use for critical functions only

Example:
- Provisioned: 100 concurrent environments
- Cost: ~$0.0000097 per hour per environment
- Monthly cost: ~$70 for 100 environments (24/7)
```

## 📊 Concurrency Comparison Table

| Feature | Unreserved | Reserved | Provisioned |
|---------|------------|----------|-------------|
| **Guarantee** | No | Yes | Yes |
| **Cold Start** | Yes | Yes | No |
| **Cost** | Lowest | Medium | Highest |
| **Performance** | Variable | Variable | Consistent |
| **Use Case** | Normal apps | Critical isolation | Ultra-low latency |

## ❓ Quick Interview Q&A

**Q: How does Lambda scale automatically?**  
A: Event-driven scaling, each request gets separate execution environment, scales from 0 to thousands automatically

**Q: What is cold start and when does it happen?**  
A: Initialization delay when creating new execution environment. Happens on first invocation, scaling up, after inactivity, or code updates

**Q: What's the default concurrency limit?**  
A: 1000 concurrent executions per AWS account per region

**Q: How fast can Lambda scale?**  
A: Initial burst: 0-1000 immediately, then +500 per minute sustained scaling

**Q: Difference between reserved and provisioned concurrency?**  
A: Reserved = guaranteed capacity pool, Provisioned = pre-warmed environments with no cold start

**Q: When to use provisioned concurrency?**  
A: For latency-sensitive apps, traffic spikes, real-time systems requiring consistent sub-100ms response times

## 🎯 Key Takeaways

✅ **Scaling** = Automatic, event-driven, pay-per-use  
✅ **Cold Start** = Initialization delay for new environments  
✅ **Limits** = 1000 concurrent executions per account/region  
✅ **Rate** = 1000 burst + 500/minute sustained scaling  
✅ **Reserved** = Guaranteed capacity pool for function isolation  
✅ **Provisioned** = Pre-warmed environments for zero cold start  

---
💡 **Interview Tip:** "Lambda scales automatically but provisioned concurrency eliminates cold starts for critical applications"