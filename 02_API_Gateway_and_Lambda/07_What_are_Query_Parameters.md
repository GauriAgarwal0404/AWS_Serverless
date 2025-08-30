# What are Query Parameters - Study Notes

## 🔍 Query Parameters Definition

**What are Query Parameters?**
Data passed in URL **after the `?` symbol** - used when GET requests can't use body

**Format:**
```
https://domain.com/path?key1=value1&key2=value2
```

**Real Example:**
```
https://us-east-1.console.aws.amazon.com/console/home?region=us-east-1
                                                    ↑
                                           Query Parameter
```

## 📝 Why Query Parameters?

**GET Request Problem:**
- GET requests **don't have a body** (unlike POST)
- Need alternative way to pass data to server
- Solution: Query parameters in URL

**POST vs GET:**
```yaml
POST Request:
  ✅ Parameters in: Request Body
  ✅ Hidden from URL

GET Request:
  ❌ No body available
  ✅ Parameters in: Query String (URL)
```

## 💡 Examples

**Single Parameter:**
```
https://api.example.com/users?id=123
```

**Multiple Parameters:**
```
https://api.example.com/users?id=123&format=json&page=1
```

**Lambda Access:**
```python
def lambda_handler(event, context):
    user_id = event['queryStringParameters']['id']  # 123
    format = event['queryStringParameters']['format']  # json
    return {"statusCode": 200, "body": f"User: {user_id}"}
```

## 🔒 GET vs POST Security

### **GET Security Issues (❌ Insecure):**
- **URL Logging** - Parameters stored in server logs
- **Browser History** - Saved in browser history  
- **Caching** - Cached by browsers/proxies
- **URL Sharing** - Accidentally shared with sensitive data

### **POST Security Benefits (✅ Secure):**
- **Request Body** - Parameters hidden in body
- **No URL Logging** - Sensitive data not in URL
- **No Caching** - POST responses not cached
- **HTTPS Encryption** - Body encrypted in transit

### **Security Example:**
```javascript
// ❌ Insecure - GET with sensitive data
https://api.com/login?username=john&password=secret123

// ✅ Secure - POST with body
fetch('/login', {
    method: 'POST',
    body: JSON.stringify({
        username: 'john',
        password: 'secret123'  // Hidden in body
    })
});
```

## 📊 Quick Comparison

| Aspect | GET (Query Params) | POST (Body) |
|--------|-------------------|-------------|
| **Visibility** | ❌ Visible in URL | ✅ Hidden |
| **Logging** | ❌ Logged in URLs | ✅ Not logged |
| **Caching** | ❌ Cached | ✅ Not cached |
| **Sensitive Data** | ❌ Insecure | ✅ Secure |

## 🎯 Best Practices

**Use GET Query Parameters For:**
✅ Filtering: `?category=tech&price=100`  
✅ Pagination: `?page=2&limit=10`  
✅ Public searches: `?search=serverless`  
✅ Non-sensitive data: `?format=json`

**Use POST Body For:**
✅ Authentication credentials  
✅ Personal information  
✅ Payment details  
✅ Large data objects

## ❓ Interview Q&A

**Q: What are Query Parameters?**  
A: Data passed in URL after `?` symbol, used when GET requests can't use body

**Q: Why do we need Query Parameters?**  
A: GET requests don't have a body, so we use query parameters to pass data

**Q: Why is POST more secure than GET for sensitive data?**  
A: POST hides data in request body, GET exposes data in URL (logs, history, caching)

**Q: What are GET security risks?**  
A: URL logging, browser history, caching, accidental sharing of sensitive data

**Q: When to use Query Parameters vs POST body?**  
A: Query params for filtering/public data, POST body for sensitive/large data

**Q: How to access Query Parameters in Lambda?**  
A: `event['queryStringParameters']['paramName']`

**Q: Format of Query Parameters?**  
A: `?key1=value1&key2=value2` - starts with `?`, separated by `&`

## 🎯 Key Takeaways

✅ **Query Parameters** = Data after `?` in URL for GET requests  
✅ **GET Limitation** = No request body available  
✅ **Security Risk** = GET parameters visible in URLs/logs  
✅ **POST Security** = Data hidden in encrypted body  
✅ **Best Practice** = Use POST for sensitive data, GET for filtering

---
💡 **Interview Tip:** "Query parameters are visible in URLs, making POST more secure for sensitive data due to hidden request body"