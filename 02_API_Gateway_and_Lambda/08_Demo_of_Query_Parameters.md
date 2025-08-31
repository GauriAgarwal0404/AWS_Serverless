# Demo of Query Parameters - Quick Notes

## 🔧 Request Validator in Method Request

**What:** Validates query parameters before they reach backend
if someone is calling this method but didn't pass nameofCountry parameter then error it out from API gateway itself rathen than going to the lambda backend.

**Key Points:**
- Set in Method Request section
- Validates required parameters
- Validates parameter format/type
- Returns 400 error if validation fails

## 🔄 Query String to Lambda Flow

**How it works:**
```
Client Request: ?country=US&format=json
     ↓
Method Request: Validates parameters
     ↓
Integration Request: Maps to Lambda event
     ↓
Lambda: Accesses via event.queryStringParameters
```

## 🗂️ Mapping Templates in Integration Request

**Purpose:** Transform query parameters using Apache Velocity Template Language (VTL)

**Why Use Mapping Templates:**
- Transform parameter names (country → Country)
- Add default values
- Combine multiple parameters
- Format data before sending to Lambda

**Apache Velocity Syntax:**
```json
{
    "Country": "$input.params('nameofCountry')",
    "Format": "$input.params('format')",
    "Timestamp": "$context.requestTime"
}
```

**Common VTL Functions:**
- `$input.params('paramName')` - Get query parameter
- `$context.requestTime` - Current timestamp
- `$util.escapeJavaScript()` - Escape special characters
- `$input.path('$.field')` - Extract from JSON body

**Multiple Parameters Example:**
```json
{
    "UserRequest": {
        "Country": "$input.params('country')",
        "Format": "$input.params('format')",
        "Page": "$input.params('page')",
        "DefaultLimit": "10"
    }
}
```

**Before vs After Mapping:**

**Without Mapping Template:**
```python
# Lambda receives raw query parameters
def lambda_handler(event, context):
    country = event['queryStringParameters']['country']  # Raw parameter
```

**With Mapping Template:**
```python
# Lambda receives transformed data
def lambda_handler(event, context):
    country = event['Country']  # Mapped parameter
    user_request = event['UserRequest']  # Nested object
```

## 💡 Examples

**URL with Multiple Parameters:**
```
https://api.example.com/users?country=US&format=json&page=1
```

**Lambda Event Structure:**
```python
def lambda_handler(event, context):
    # Direct access
    country = event['queryStringParameters']['country']
    
    # Via mapping template
    mapped_country = event['Country']  # From mapping template
```

## ❓ Quick Interview Q&A

**Q: What is Request Validator?**  
A: Validates query parameters in Method Request before reaching backend

**Q: How do query parameters reach Lambda?**  
A: Through event.queryStringParameters object

**Q: What is Apache Velocity mapping?**  
A: Template language (VTL) to transform query parameters in Integration Request

**Q: Why use Mapping Templates?**  
A: Transform parameter names, add defaults, format data, create nested objects

**Q: How to map query parameter 'country' to 'Country'?**  
A: `{"Country": "$input.params('country')"}`

**Q: What's $context.requestTime in mapping?**  
A: VTL function that adds current timestamp to the mapped data

**Q: How to pass multiple query parameters?**  
A: Use & sign: `?param1=value1&param2=value2`

**Q: Difference between with/without mapping templates?**  
A: Without: event.queryStringParameters['param'], With: event['MappedName']

## 🎯 Key Takeaways

✅ **Request Validator** = Validates params in Method Request  
✅ **Parameter Flow** = URL → Method Request → Integration Request → Lambda  
✅ **Mapping Template** = Apache Velocity transforms parameters  
✅ **Multiple Params** = Use & separator in URL  
✅ **Lambda Access** = event.queryStringParameters['paramName']

---
💡 **Interview Tip:** "Query parameters flow through validation → mapping → Lambda event object"