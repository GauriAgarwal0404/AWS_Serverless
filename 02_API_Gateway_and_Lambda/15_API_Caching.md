# API Caching - Quick Notes

## 🏪 What is API Caching

**API Caching:** Store API responses temporarily to reduce backend calls and improve performance

**How it works:**
- First request → Backend → Cache response
- Subsequent requests → Return cached response (no backend call)
- Cache expires → Next request hits backend again

**Benefits:**
- Reduced latency
- Lower backend load
- Cost savings (fewer Lambda invocations)
- Better user experience

## ⚙️ API Gateway Caching Features

**Cache Configuration:**
- **Cache Cluster Size** - 0.5GB to 237GB
- **TTL (Time To Live)** - 0 to 3600 seconds
- **Cache Key Parameters** - Query strings, headers, path parameters
- **Per-Method Caching** - Enable/disable per endpoint
- **Cache Invalidation** - Manual or automatic

## 🔧 Console Configuration Steps

**1. Enable Caching (Stage Level):**
```
API Gateway Console → Stages → Select Stage → Settings
→ Enable API cache → Set cache cluster size → Set default TTL
```

**2. Method-Level Cache Settings:**
```
Resources → Select Method → Method Execution → Caching
→ Enable caching → Configure cache key parameters
```

**3. Cache Key Configuration:**
```
Cache key parameters:
✅ Query string parameters: userId, category
✅ Headers: Authorization, Accept
✅ Path parameters: {id}, {type}
```

## 💡 Hands-On Example

**API Endpoint:**
```
GET /users?category=premium&region=us-east
```

**Cache Configuration:**
```json
{
  "cachingEnabled": true,
  "cacheTtlInSeconds": 300,
  "cacheKeyParameters": [
    "method.request.querystring.category",
    "method.request.querystring.region"
  ]
}
```

**Cache Key Generated:**
```
GET/users/category=premium&region=us-east
```

**Request Flow:**
```
1st Request: Client → API Gateway → Lambda → Response (cached)
2nd Request: Client → API Gateway → Cached Response (no Lambda call)
```

## 🎯 Cache Scenarios

**Scenario 1: User Data API**
```python
# Lambda function for user data
def lambda_handler(event, context):
    user_id = event['queryStringParameters']['userId']
    # Expensive database call
    user_data = get_user_from_database(user_id)
    return {
        'statusCode': 200,
        'body': json.dumps(user_data),
        'headers': {
            'Cache-Control': 'max-age=300'  # Cache for 5 minutes
        }
    }
```

**Cache Configuration:**
- Cache Key: `userId` parameter
- TTL: 300 seconds
- Cache Size: 6.1GB (for high traffic)

**Scenario 2: Product Catalog API**
```
GET /products?category=electronics&sort=price
```

**Cache Settings:**
- Cache both `category` and `sort` parameters
- TTL: 1800 seconds (30 minutes)
- Different cache entries for different parameter combinations

## 🔑 Cache Key Best Practices

**Include in Cache Key:**
- Query parameters that affect response
- Headers that change response content
- Path parameters for different resources

**Exclude from Cache Key:**
- Authentication tokens (unless personalized)
- Tracking parameters
- Request IDs

**Example Cache Key Configuration:**
```
Include:
✅ method.request.querystring.category
✅ method.request.querystring.page
✅ method.request.header.Accept-Language

Exclude:
❌ method.request.header.Authorization
❌ method.request.querystring.requestId
```

## 🚫 Cache Invalidation

**Manual Invalidation (Console):**
```
Stages → Caching → Invalidate Cache → Flush entire cache
```

**Programmatic Invalidation:**
```python
# In Lambda response
return {
    'statusCode': 200,
    'body': json.dumps(data),
    'headers': {
        'Cache-Control': 'no-cache'  # Skip cache for this response
    }
}
```

**AWS CLI Invalidation:**
```bash
aws apigateway flush-stage-cache \
  --rest-api-id abc123 \
  --stage-name prod
```

## 📊 Cache Monitoring

**CloudWatch Metrics:**
- CacheHitCount - Successful cache hits
- CacheMissCount - Cache misses (backend calls)
- CacheHitRate - Percentage of cache hits
- Latency - Response time with/without cache

**Console Monitoring:**
```
CloudWatch → Metrics → API Gateway → Stage Metrics
→ View CacheHitCount and CacheMissCount
```

## ❓ Quick Interview Q&A

**Q: What is API Gateway caching?**  
A: Temporary storage of API responses to reduce backend calls and improve performance

**Q: How to enable caching in API Gateway?**  
A: Go to Stages → Settings → Enable API cache → Configure cluster size and TTL

**Q: What determines cache key?**  
A: Query parameters, headers, and path parameters you configure for each method

**Q: Maximum cache TTL in API Gateway?**  
A: 3600 seconds (1 hour)

**Q: How to invalidate cache?**  
A: Manual flush in console, Cache-Control headers, or AWS CLI flush command

**Q: Benefits of API caching?**  
A: Reduced latency, lower backend load, cost savings, better user experience

**Q: What cache sizes are available?**  
A: 0.5GB to 237GB cluster sizes

**Q: How to monitor cache performance?**  
A: CloudWatch metrics - CacheHitCount, CacheMissCount, CacheHitRate

## 🎯 Key Takeaways

✅ **Caching** = Store responses temporarily to reduce backend calls  
✅ **Configuration** = Stage-level enable + method-level cache keys  
✅ **Cache Key** = Query params + headers + path params that affect response  
✅ **TTL** = 0-3600 seconds, default based on your needs  
✅ **Monitoring** = CloudWatch metrics for hit/miss rates  
✅ **Invalidation** = Manual flush or Cache-Control headers

---
💡 **Interview Tip:** "API caching reduces costs and latency by storing responses and skipping backend calls for repeated requests"