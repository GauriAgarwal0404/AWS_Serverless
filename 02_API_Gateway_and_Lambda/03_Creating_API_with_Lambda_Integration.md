# Creating API with Lambda Integration - Study Notes

## 🔧 Lambda as API Backend

**Concept:** Lambda functions serve as the **backend logic** for API Gateway endpoints.

**Benefits:**
- **Serverless** - No server management
- **Auto-scaling** - Handles any request volume
- **Pay-per-execution** - Cost-effective
- **Event-driven** - Triggered by API calls

## 📁 What are Resources?

**Resources** = Different **projects/endpoints** under your API

**Think of Resources as:**
- **URL paths** in your API structure
- **Different sections** of your application
- **Logical groupings** of related functionality

### **Example API Structure:**
```
API: MyApp
├── /users (Resource)
├── /orders (Resource)
├── /products (Resource)
└── /payments (Resource)
```

### **Real-World Example:**
```yaml
E-commerce API Resources:
  /users     - User management
  /products  - Product catalog  
  /cart      - Shopping cart
  /orders    - Order processing
  /payments  - Payment handling
```

## 🔄 What are Methods?

**Methods** = **HTTP operations** (GET, POST, PUT, DELETE) under each resource

**Standard HTTP Methods:**
- **GET** - Retrieve data
- **POST** - Create new data
- **PUT** - Update existing data
- **DELETE** - Remove data

### **Example Methods under Resources:**
```yaml
/users Resource:
  GET /users        - List all users
  POST /users       - Create new user
  GET /users/{id}   - Get specific user
  PUT /users/{id}   - Update user
  DELETE /users/{id} - Delete user

/orders Resource:
  GET /orders       - List orders
  POST /orders      - Create order
  GET /orders/{id}  - Get order details
```

## 🏗️ API Structure Overview

### **Hierarchy:**
```
API Gateway
├── Resource (/users)
│   ├── Method (GET)    → Lambda Function
│   ├── Method (POST)   → Lambda Function
│   └── Method (DELETE) → Lambda Function
├── Resource (/orders)
│   ├── Method (GET)    → Lambda Function
│   └── Method (POST)   → Lambda Function
```

### **Each Method Integration:**
- **One method** = **One Lambda function** (typical pattern)
- **Method receives request** → **Triggers Lambda** → **Returns response**

## 🔗 Lambda Integration Flow

### **API Call Flow:**
```
1. Client Request:
   POST /users
   Body: {"name": "John", "email": "john@example.com"}

2. API Gateway:
   - Routes to /users resource
   - Identifies POST method
   - Triggers associated Lambda function

3. Lambda Function:
   - Processes user creation
   - Stores in database
   - Returns success response

4. API Gateway:
   - Formats response
   - Returns to client
```

## ⚙️ Creating API Steps

### **Quick Setup Process:**
1. **Create Lambda function** (backend logic)
2. **Create API Gateway** 
3. **Create Resources** (/users, /orders, etc.)
4. **Create Methods** (GET, POST, etc.) under resources
5. **Integrate methods** with Lambda functions
6. **Deploy API** to stage
7. **Test endpoints**

## ❓ Interview Q&A

**Q: What serves as the backend in API Gateway?**  
A: Lambda functions serve as the serverless backend for API endpoints

**Q: What are Resources in API Gateway?**  
A: Resources are different URL paths/projects under your API (like /users, /orders)

**Q: What are Methods in API Gateway?**  
A: Methods are HTTP operations (GET, POST, PUT, DELETE) under each resource

**Q: How many Lambda functions per method?**  
A: Typically one Lambda function per method for clean architecture

**Q: Example of Resource vs Method?**  
A: Resource: /users, Methods: GET /users, POST /users, DELETE /users/{id}

## 🎯 Key Points

✅ **Lambda** = Serverless backend for API Gateway  
✅ **Resources** = URL paths/projects (/users, /orders)  
✅ **Methods** = HTTP operations (GET, POST, PUT, DELETE)  
✅ **Structure** = API → Resources → Methods → Lambda  
✅ **Integration** = Each method triggers specific Lambda function

---
💡 **Interview Tip:** "Resources organize your API into logical sections, Methods define HTTP operations, and Lambda provides serverless backend processing"