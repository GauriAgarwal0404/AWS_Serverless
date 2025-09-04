# Demo of External Dependency Problem - Quick Notes

## 📦 Lambda External Dependencies Overview

### **Built-in Dependencies in Lambda Environment:**
```python
# These come pre-installed with Lambda:
import json      # ✅ Available by default
import boto3     # ✅ Available by default (AWS SDK)
import os        # ✅ Available by default
import datetime  # ✅ Available by default
import urllib    # ✅ Available by default
```

### **External Dependencies Problem:**
```python
# These require separate installation:
import requests  # ❌ ModuleNotFoundError
import pandas    # ❌ ModuleNotFoundError
import numpy     # ❌ ModuleNotFoundError
import psycopg2  # ❌ ModuleNotFoundError
```

**Error Example:**
```
[ERROR] Runtime.ImportModuleError: Unable to import module 'lambda_function': No module named 'requests'
```

## 🛠️ Solutions for External Dependencies

### **1. Local Installation using Visual Studio Code**
Install dependencies locally and package with your function code

### **2. Using pip install with -t flag:**
```bash
pip install requests -t .
```
- **-t .** = Install packages in current directory (target directory)
- Creates local package folder structure
- Packages get bundled with your Lambda code

## 🚀 Hands-On Example - Bitcoin Price API

### **Problem Scenario:**
Need to fetch Bitcoin price from external API using `requests` library (not available in Lambda by default)

### **Python Code:**
```python
import json
import boto3
import requests  # ❌ External dependency - not available by default
from datetime import datetime

def lambda_handler(event, context):
    # Bitcoin price API endpoint
    URL = "https://data-api.coindesk.com/index/cc/v1/latest/tick?market=sda&instruments=XBX-USD&groups=VALUE,LAST_UPDATE&api_key=YOUR_KEY"
    
    # Make HTTP request (requires 'requests' library)
    r = requests.get(url=URL)
    data = r.json()
    print(data)  # Raw JSON for debugging

    # Extract price and timestamp correctly
    price = data["Data"]["XBX-USD"]["VALUE"]
    ts = data["Data"]["XBX-USD"]["VALUE_LAST_UPDATE_TS"]
    time_data = datetime.utcfromtimestamp(ts).isoformat() + "Z"
    
    output = f"Price of Bitcoin is {price} USD at datetime {time_data}"
    print(output)
    return output
```

### **The Problem:**
```
Deployment Error:
[ERROR] Runtime.ImportModuleError: Unable to import module 'lambda_function': No module named 'requests'

Cause: 'requests' library is not included in Lambda's default Python runtime
```

## 🔧 Step-by-Step Solution using Visual Studio Code

### **Step 1: Create Project Structure**
```
bitcoin-lambda/
├── lambda_function.py
├── requirements.txt
└── (dependencies will be installed here)
```

### **Step 2: Create requirements.txt**
```txt
requests==2.31.0
```

### **Step 3: Install Dependencies Locally**
```bash
# Navigate to project directory
cd bitcoin-lambda

# Install dependencies in current directory
pip install -r requirements.txt -t .

# Alternative: Install specific package
pip install requests -t .
```

### **Step 4: Verify Installation**
After running `pip install requests -t .`, your directory structure becomes:
```
bitcoin-lambda/
├── lambda_function.py
├── requirements.txt
├── requests/                 # ✅ requests library folder
├── urllib3/                  # ✅ requests dependency
├── certifi/                  # ✅ requests dependency
├── charset_normalizer/       # ✅ requests dependency
├── idna/                     # ✅ requests dependency
└── requests-2.31.0.dist-info/
```

### **Step 5: Test Locally**
```python
# Test in VS Code terminal
python lambda_function.py

# Should now work without import errors
```

### **Step 6: Package for Deployment**
```bash
# Create deployment package
zip -r bitcoin-lambda.zip .

# Upload to Lambda via AWS Console or CLI
aws lambda update-function-code \
    --function-name bitcoin-price-fetcher \
    --zip-file fileb://bitcoin-lambda.zip
```

## 💡 Understanding pip install -t . Command

### **Command Breakdown:**
```bash
pip install requests -t .
```

- **pip install** = Python package installer command
- **requests** = Package name to install
- **-t** = Target directory flag
- **.** = Current directory (target location)

### **What -t . Does:**
```
Normal pip install:
pip install requests
└── Installs to system Python site-packages

With -t . flag:
pip install requests -t .
└── Installs to current directory (local folder)
```
Packages installed locally with your code


## 📁 Directory Structure Examples

### **Before Installing Dependencies:**
```
my-lambda-function/
├── lambda_function.py
└── requirements.txt
```

### **After pip install requests -t .:**
```
my-lambda-function/
├── lambda_function.py
├── requirements.txt
├── requests/                    # Main requests package
├── urllib3/                     # Dependency 1
├── certifi/                     # Dependency 2
├── charset_normalizer/          # Dependency 3
├── idna/                        # Dependency 4
├── requests-2.31.0.dist-info/   # Package metadata
└── (other dependency folders)
```

### **After Zipping for Lambda:**
```bash
zip -r deployment.zip .
# Creates deployment.zip containing:
# - Your code (lambda_function.py)
# - All dependencies (requests/, urllib3/, etc.)
```

## 🚨 Common Issues and Solutions

### **Issue 1: Import Still Fails After Installation**
```
Problem: ModuleNotFoundError even after pip install -t .
Solution: Check directory structure, ensure packages in same folder as code
```

### **Issue 2: Package Size Too Large**
```
Problem: Deployment package exceeds 50MB limit
Solutions:
1. Use Lambda Layers for common dependencies
2. Use Container Images (10GB limit)
3. Optimize packages (remove unnecessary files)
```

### **Issue 3: Version Conflicts**
```
Problem: Package version incompatible with Lambda Python version
Solution: Specify compatible versions in requirements.txt
```

### **Issue 4: Platform-Specific Packages**
```
Problem: Packages compiled for wrong platform (Windows vs Linux)
Solution: Use --platform flag or develop on Linux/Docker
```

## 🔄 Alternative Solutions

### **1. Lambda Layers:**
```bash
# Create layer with dependencies
pip install requests -t python/
zip -r requests-layer.zip python/

# Attach layer to function (no need for -t . in function code)
```

### **2. Container Images:**
```dockerfile
FROM aws/lambda-python:3.9
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY lambda_function.py ${LAMBDA_TASK_ROOT}
CMD ["lambda_function.lambda_handler"]
```

### **3. AWS SAM:**
```yaml
# template.yaml
Resources:
  BitcoinFunction:
    Type: AWS::Serverless::Function
    Properties:
      Runtime: python3.9
      Handler: lambda_function.lambda_handler
      # SAM automatically handles dependencies
```

## 🎯 Best Practices

### **Development Workflow:**
```
1. Create virtual environment locally
2. Install dependencies: pip install -r requirements.txt -t .
3. Test locally in VS Code
4. Package everything: zip -r deployment.zip .
5. Deploy to Lambda
6. Test in AWS console
```

### **Package Optimization:**
```bash
# Remove unnecessary files to reduce size
find . -type d -name "__pycache__" -delete
find . -name "*.pyc" -delete
find . -name "tests" -type d -exec rm -rf {} +
```

## ❓ Quick Interview Q&A

**Q: What libraries come pre-installed with Lambda?**  
A: json, boto3, os, datetime, urllib - basic Python standard library and AWS SDK

**Q: How do you install external dependencies for Lambda?**  
A: Use `pip install package-name -t .` to install in current directory, then package with your code

**Q: What does the -t flag do in pip install?**  
A: Specifies target directory for installation; `-t .` installs packages in current directory

**Q: What happens if you don't include dependencies in deployment package?**  
A: Get `ModuleNotFoundError` at runtime when Lambda tries to import the package

**Q: What are alternatives to bundling dependencies with code?**  
A: Lambda Layers for shared dependencies, Container Images for large packages, or AWS SAM for automatic handling

**Q: What's the size limit for Lambda deployment packages?**  
A: 50MB for ZIP deployments, 10GB for Container Images

## 🎯 Key Takeaways

✅ **Default Libraries** = json, boto3, datetime come pre-installed  
✅ **External Dependencies** = Must be installed separately and packaged  
✅ **pip install -t .** = Install packages in current directory for packaging  
✅ **VS Code Development** = Test locally before deploying to Lambda  
✅ **Package Structure** = Dependencies must be in same directory as code  
✅ **Size Limits** = 50MB for ZIP, consider Layers/Container Images for larger packages  

---
💡 **Interview Tip:** "Use `pip install -t .` to bundle external dependencies with Lambda code for deployment"