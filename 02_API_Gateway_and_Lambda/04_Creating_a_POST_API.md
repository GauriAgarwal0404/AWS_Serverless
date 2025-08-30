# Creating a POST API - Study Notes

## 🚀 POST API with Lambda Function

### **Lambda Function Code:**
```python
import json 

def lambda_handler(event, context):
    # TODO implement
    print(event)
    return 'Hello from ' + event['Country']
```

### **What This Function Does:**
- **Receives event** from API Gateway POST request
- **Extracts 'Country'** from request data
- **Returns greeting** message with country name

## 📝 POST Method Basics

### **POST vs GET:**
- **POST** - Send data to server (create/submit)
- **GET** - Retrieve data from server (read-only)

### **POST Characteristics:**
- **Request body** contains data
- **Not cached** by browsers
- **Can handle large data**
- **Used for:** Creating records, form submissions, data processing

## 🔗 API Gateway + Lambda Integration

### **Request Flow:**
```
1. Client POST Request:
   POST /api/greet
   Body: {"Country": "India"}

2. API Gateway:
   - Receives POST request
   - Extracts body data
   - Passes to Lambda as 'event'

3. Lambda Function:
   - Processes event['Country']
   - Returns: "Hello from India"

4. API Gateway:
   - Returns response to client
```

### **Event Structure:**
```json
{
    "Country": "India",
    "httpMethod": "POST",
    "body": "{\"Country\": \"India\"}",
    "headers": {...},
    "queryStringParameters": null
}
```

## ⚙️ Creating POST API Steps

### **Quick Setup:**
1. **Create Lambda function** with the code above
2. **Create API Gateway** 
3. **Create Resource** (e.g., `/greet`)
4. **Create POST Method** under resource
5. **Integrate with Lambda** function
6. **Deploy API** to stage
7. **Test with POST request**

### **Testing the API:**
```bash
# Using curl
curl -X POST https://api-id.execute-api.region.amazonaws.com/prod/greet \
  -H "Content-Type: application/json" \
  -d '{"Country": "India"}'

# Response: "Hello from India"
```

## 📊 Request/Response Example

### **POST Request:**
```http
POST /greet HTTP/1.1
Host: api.example.com
Content-Type: application/json

{
    "Country": "USA"
}
```

### **Lambda Processing:**
```python
# event['Country'] = "USA"
return 'Hello from ' + event['Country']  # "Hello from USA"
```

### **API Response:**
```http
HTTP/1.1 200 OK
Content-Type: application/json

"Hello from USA"
```

## 🛠️ Common POST API Use Cases

### **Real-World Examples:**
- **User registration** - POST /users
- **Order creation** - POST /orders  
- **File upload** - POST /files
- **Form submission** - POST /contact
- **Data processing** - POST /process

### **Best Practices:**
- **Validate input data** in Lambda
- **Return appropriate status codes**
- **Handle errors gracefully**
- **Use proper JSON format**

## ❓ Interview Q&A

**Q: What's the difference between POST and GET?**  
A: POST sends data in request body for creation/submission, GET retrieves data

**Q: How does API Gateway pass POST data to Lambda?**  
A: Request body data is included in the 'event' parameter

**Q: What does event['Country'] access?**  
A: The 'Country' field from the POST request body

**Q: Why use POST for this example?**  
A: To demonstrate sending data to the server for processing

**Q: How to test a POST API?**  
A: Use tools like curl, Postman, or API Gateway test console

## 🎯 Key Points

✅ **POST Method** = Send data to server via request body  
✅ **Lambda Event** = Contains POST request data  
✅ **Data Access** = event['fieldName'] to extract values  
✅ **Use Cases** = User creation, form submission, data processing  
✅ **Testing** = Use curl or Postman with JSON body

---
💡 **Interview Tip:** "POST APIs send data in request body to Lambda, which processes the data and returns a response - perfect for creating or submitting information"