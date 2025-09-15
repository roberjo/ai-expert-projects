# Deployment Guide

## 🚀 Overview

This guide covers deployment strategies for all projects in the AI Expert Projects curriculum. Learn how to deploy your applications to various free hosting platforms, configure environments, and manage production deployments.

## 🌐 Free Hosting Platforms

### 1. Streamlit Cloud

#### Setup and Configuration
```bash
# 1. Push your code to GitHub
git add .
git commit -m "Initial commit"
git push origin main

# 2. Go to https://streamlit.io/cloud
# 3. Sign in with GitHub
# 4. Click "New app"
# 5. Select your repository
# 6. Configure deployment settings
```

#### Streamlit App Configuration
```python
# app.py - Main Streamlit application
import streamlit as st
import os
from dotenv import load_dotenv

# Load environment variables
load_dotenv()

st.set_page_config(
    page_title="AI Expert Project",
    page_icon="🤖",
    layout="wide"
)

st.title("AI Expert Project")
st.write("Your AI application is running!")

# Test API connection
api_key = os.getenv('GEMINI_API_KEY')
if api_key:
    st.success("✅ API key configured!")
else:
    st.error("❌ API key not found")
```

#### Environment Variables
```bash
# In Streamlit Cloud, add these environment variables:
GEMINI_API_KEY=your_gemini_api_key_here
GMAIL_ADDRESS=your_email@gmail.com
GMAIL_APP_PASSWORD=your_app_password_here
```

#### Requirements File
```bash
# requirements.txt
streamlit>=1.28.0
google-generativeai>=0.3.0
python-dotenv>=1.0.0
requests>=2.31.0
pandas>=2.0.0
numpy>=1.24.0
```

### 2. Hugging Face Spaces

#### Setup and Configuration
```bash
# 1. Go to https://huggingface.co/spaces
# 2. Create a new space
# 3. Choose "Streamlit" as the SDK
# 4. Connect your GitHub repository
# 5. Configure environment variables
```

#### Space Configuration
```yaml
# README.md for Hugging Face Space
---
title: AI Expert Project
emoji: 🤖
colorFrom: blue
colorTo: red
sdk: streamlit
sdk_version: 1.28.0
app_file: app.py
pinned: false
license: mit
---

# AI Expert Project

This is an AI application built as part of the AI Expert Projects curriculum.

## Features
- Feature 1
- Feature 2
- Feature 3

## Usage
1. Enter your input
2. Click the button
3. View the results

## API Keys
Make sure to set your API keys in the Space settings.
```

#### Environment Variables
```bash
# In Hugging Face Space settings:
GEMINI_API_KEY=your_gemini_api_key_here
HUGGINGFACE_API_KEY=your_hf_api_key_here
```

### 3. GitHub Pages (for Documentation)

#### Setup and Configuration
```bash
# 1. Create a docs folder in your repository
mkdir docs
cd docs

# 2. Create index.html
cat > index.html << EOF
<!DOCTYPE html>
<html>
<head>
    <title>AI Expert Project Documentation</title>
</head>
<body>
    <h1>AI Expert Project</h1>
    <p>Documentation for your AI application.</p>
</body>
</html>
EOF

# 3. Enable GitHub Pages in repository settings
# 4. Select source as "docs" folder
```

#### Jekyll Configuration
```yaml
# _config.yml
title: AI Expert Project
description: Documentation for AI Expert Project
baseurl: "/your-repo-name"
url: "https://your-username.github.io"

# Build settings
markdown: kramdown
theme: minima
plugins:
  - jekyll-feed
  - jekyll-sitemap
```

## 🔧 Deployment Strategies

### 1. Single Application Deployment

#### Basic Streamlit App
```python
# app.py
import streamlit as st
import os
from dotenv import load_dotenv

load_dotenv()

def main():
    st.title("AI Expert Project")
    
    # Your application code here
    user_input = st.text_input("Enter your input:")
    
    if st.button("Process"):
        # Process the input
        result = process_input(user_input)
        st.write("Result:", result)

if __name__ == "__main__":
    main()
```

#### Multi-Page Application
```python
# app.py
import streamlit as st

# Page configuration
st.set_page_config(
    page_title="AI Expert Projects",
    page_icon="🤖",
    layout="wide"
)

# Sidebar navigation
page = st.sidebar.selectbox(
    "Choose a project:",
    ["Project 1", "Project 2", "Project 3"]
)

if page == "Project 1":
    st.title("Project 1: Document Summarizer")
    # Project 1 code here
elif page == "Project 2":
    st.title("Project 2: Meeting Notes Summarizer")
    # Project 2 code here
elif page == "Project 3":
    st.title("Project 3: Model Comparison Tool")
    # Project 3 code here
```

### 2. Portfolio Dashboard Deployment

#### Dashboard Structure
```python
# app.py
import streamlit as st
import requests
import json

# Page configuration
st.set_page_config(
    page_title="AI Expert Portfolio",
    page_icon="🚀",
    layout="wide"
)

# Main dashboard
st.title("🚀 AI Expert Portfolio")
st.write("Welcome to my AI applications portfolio!")

# Project showcase
projects = [
    {
        "name": "Document Summarizer",
        "description": "AI-powered document summarization with email automation",
        "url": "https://your-app-1.streamlit.app",
        "status": "✅ Deployed"
    },
    {
        "name": "Meeting Notes Summarizer",
        "description": "Intelligent meeting transcript analysis and action item extraction",
        "url": "https://your-app-2.streamlit.app",
        "status": "✅ Deployed"
    },
    {
        "name": "Model Comparison Tool",
        "description": "Compare outputs from different AI models",
        "url": "https://your-app-3.streamlit.app",
        "status": "✅ Deployed"
    }
]

# Display projects
for project in projects:
    with st.container():
        st.subheader(project["name"])
        st.write(project["description"])
        st.write(f"Status: {project['status']}")
        st.link_button("View App", project["url"])
        st.divider()
```

### 3. API Deployment

#### FastAPI Application
```python
# main.py
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel
import uvicorn

app = FastAPI(title="AI Expert API")

class RequestModel(BaseModel):
    text: str
    max_length: int = 100

class ResponseModel(BaseModel):
    result: str
    success: bool

@app.post("/summarize", response_model=ResponseModel)
async def summarize_text(request: RequestModel):
    try:
        # Your summarization logic here
        result = summarize_document(request.text, request.max_length)
        return ResponseModel(result=result, success=True)
    except Exception as e:
        raise HTTPException(status_code=500, detail=str(e))

@app.get("/health")
async def health_check():
    return {"status": "healthy"}

if __name__ == "__main__":
    uvicorn.run(app, host="0.0.0.0", port=8000)
```

#### Deploy to Railway
```bash
# 1. Install Railway CLI
npm install -g @railway/cli

# 2. Login to Railway
railway login

# 3. Initialize project
railway init

# 4. Deploy
railway up
```

## 🔐 Environment Configuration

### 1. Environment Variables Management

#### Local Development
```bash
# .env file
GEMINI_API_KEY=your_gemini_api_key_here
GMAIL_ADDRESS=your_email@gmail.com
GMAIL_APP_PASSWORD=your_app_password_here
DATABASE_URL=sqlite:///app.db
DEBUG=True
LOG_LEVEL=INFO
```

#### Production Environment
```bash
# Production environment variables
GEMINI_API_KEY=your_production_api_key
GMAIL_ADDRESS=your_production_email@gmail.com
GMAIL_APP_PASSWORD=your_production_app_password
DATABASE_URL=postgresql://user:pass@host:port/db
DEBUG=False
LOG_LEVEL=WARNING
```

### 2. Configuration Management
```python
# config.py
import os
from dotenv import load_dotenv

load_dotenv()

class Config:
    """Application configuration."""
    
    # API Keys
    GEMINI_API_KEY = os.getenv('GEMINI_API_KEY')
    GMAIL_ADDRESS = os.getenv('GMAIL_ADDRESS')
    GMAIL_APP_PASSWORD = os.getenv('GMAIL_APP_PASSWORD')
    
    # Database
    DATABASE_URL = os.getenv('DATABASE_URL', 'sqlite:///app.db')
    
    # Application Settings
    DEBUG = os.getenv('DEBUG', 'False').lower() == 'true'
    LOG_LEVEL = os.getenv('LOG_LEVEL', 'INFO')
    
    # Validation
    @classmethod
    def validate(cls):
        """Validate required configuration."""
        required_vars = ['GEMINI_API_KEY']
        missing_vars = [var for var in required_vars if not getattr(cls, var)]
        
        if missing_vars:
            raise ValueError(f"Missing required environment variables: {missing_vars}")
```

## 📊 Monitoring and Logging

### 1. Application Logging
```python
# logging_config.py
import logging
import sys
from pathlib import Path

def setup_logging():
    """Set up application logging."""
    log_dir = Path("logs")
    log_dir.mkdir(exist_ok=True)
    
    # Configure logging
    logging.basicConfig(
        level=logging.INFO,
        format='%(asctime)s - %(name)s - %(levelname)s - %(message)s',
        handlers=[
            logging.FileHandler(log_dir / "app.log"),
            logging.StreamHandler(sys.stdout)
        ]
    )
    
    return logging.getLogger(__name__)
```

### 2. Performance Monitoring
```python
# monitoring.py
import time
import streamlit as st
from functools import wraps

def monitor_performance(func):
    """Decorator to monitor function performance."""
    @wraps(func)
    def wrapper(*args, **kwargs):
        start_time = time.time()
        try:
            result = func(*args, **kwargs)
            success = True
        except Exception as e:
            success = False
            raise e
        finally:
            duration = time.time() - start_time
            st.write(f"⏱️ {func.__name__} took {duration:.2f}s")
        
        return result
    return wrapper
```

### 3. Error Tracking
```python
# error_tracking.py
import logging
import traceback
from typing import Optional

logger = logging.getLogger(__name__)

def track_error(error: Exception, context: str = ""):
    """Track and log errors."""
    error_info = {
        'error_type': type(error).__name__,
        'error_message': str(error),
        'context': context,
        'traceback': traceback.format_exc()
    }
    
    logger.error(f"Error occurred: {error_info}")
    
    # In production, you might want to send this to an error tracking service
    # like Sentry, Rollbar, or Bugsnag
```

## 🚀 CI/CD Pipeline

### 1. GitHub Actions Workflow
```yaml
# .github/workflows/deploy.yml
name: Deploy to Streamlit Cloud

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Set up Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.8'
      
      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install -r requirements.txt
      
      - name: Run tests
        run: |
          pytest tests/
      
      - name: Lint code
        run: |
          flake8 src/
          black --check src/
  
  deploy:
    needs: test
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    steps:
      - uses: actions/checkout@v3
      
      - name: Deploy to Streamlit Cloud
        run: |
          echo "Deployment triggered by push to main branch"
          # Streamlit Cloud will automatically deploy
```

### 2. Automated Testing
```python
# tests/test_deployment.py
import pytest
import requests
import os

class TestDeployment:
    """Test deployment functionality."""
    
    def test_health_check(self):
        """Test application health check."""
        # This would test your deployed application
        # Replace with your actual deployment URL
        url = os.getenv('DEPLOYMENT_URL', 'http://localhost:8501')
        response = requests.get(f"{url}/health")
        assert response.status_code == 200
    
    def test_api_endpoints(self):
        """Test API endpoints."""
        # Test your API endpoints
        pass
```

## 🔄 Database Deployment

### 1. SQLite Database
```python
# database.py
import sqlite3
from pathlib import Path

def init_database():
    """Initialize SQLite database."""
    db_path = Path("data/app.db")
    db_path.parent.mkdir(exist_ok=True)
    
    conn = sqlite3.connect(db_path)
    cursor = conn.cursor()
    
    # Create tables
    cursor.execute("""
        CREATE TABLE IF NOT EXISTS users (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            email TEXT UNIQUE NOT NULL,
            created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
        )
    """)
    
    cursor.execute("""
        CREATE TABLE IF NOT EXISTS documents (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            user_id INTEGER,
            content TEXT NOT NULL,
            summary TEXT,
            created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
            FOREIGN KEY (user_id) REFERENCES users (id)
        )
    """)
    
    conn.commit()
    conn.close()
```

### 2. Database Migrations
```python
# migrations.py
import sqlite3
from pathlib import Path

def run_migrations():
    """Run database migrations."""
    db_path = Path("data/app.db")
    conn = sqlite3.connect(db_path)
    cursor = conn.cursor()
    
    # Migration 1: Add new column
    try:
        cursor.execute("ALTER TABLE documents ADD COLUMN status TEXT DEFAULT 'pending'")
        conn.commit()
        print("Migration 1 completed")
    except sqlite3.OperationalError:
        print("Migration 1 already applied")
    
    # Migration 2: Create new table
    try:
        cursor.execute("""
            CREATE TABLE IF NOT EXISTS api_usage (
                id INTEGER PRIMARY KEY AUTOINCREMENT,
                endpoint TEXT NOT NULL,
                timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
                response_time REAL,
                success BOOLEAN
            )
        """)
        conn.commit()
        print("Migration 2 completed")
    except sqlite3.OperationalError:
        print("Migration 2 already applied")
    
    conn.close()
```

## 📱 Mobile and Responsive Design

### 1. Responsive Streamlit App
```python
# app.py
import streamlit as st

# Configure page for mobile
st.set_page_config(
    page_title="AI Expert App",
    page_icon="🤖",
    layout="wide",
    initial_sidebar_state="collapsed"
)

# Mobile-friendly layout
def mobile_layout():
    """Mobile-friendly layout."""
    st.title("🤖 AI Expert App")
    
    # Use columns for responsive design
    col1, col2 = st.columns([1, 1])
    
    with col1:
        st.subheader("Input")
        user_input = st.text_area("Enter your text:", height=100)
    
    with col2:
        st.subheader("Output")
        if st.button("Process", type="primary"):
            result = process_input(user_input)
            st.write(result)
    
    # Mobile-specific features
    if st.checkbox("Show mobile menu"):
        st.sidebar.title("Menu")
        st.sidebar.write("Mobile navigation here")

if __name__ == "__main__":
    mobile_layout()
```

### 2. Progressive Web App (PWA)
```html
<!-- manifest.json -->
{
  "name": "AI Expert App",
  "short_name": "AI Expert",
  "description": "AI Expert Projects Application",
  "start_url": "/",
  "display": "standalone",
  "background_color": "#ffffff",
  "theme_color": "#000000",
  "icons": [
    {
      "src": "icon-192.png",
      "sizes": "192x192",
      "type": "image/png"
    },
    {
      "src": "icon-512.png",
      "sizes": "512x512",
      "type": "image/png"
    }
  ]
}
```

## 🔒 Security Considerations

### 1. API Key Security
```python
# security.py
import os
import hashlib
import hmac
from typing import Optional

class SecurityManager:
    """Manage application security."""
    
    @staticmethod
    def validate_api_key(api_key: str) -> bool:
        """Validate API key format."""
        if not api_key or len(api_key) < 20:
            return False
        return True
    
    @staticmethod
    def hash_sensitive_data(data: str) -> str:
        """Hash sensitive data."""
        return hashlib.sha256(data.encode()).hexdigest()
    
    @staticmethod
    def verify_hmac(data: str, signature: str, secret: str) -> bool:
        """Verify HMAC signature."""
        expected_signature = hmac.new(
            secret.encode(),
            data.encode(),
            hashlib.sha256
        ).hexdigest()
        return hmac.compare_digest(signature, expected_signature)
```

### 2. Input Validation
```python
# validation.py
from pydantic import BaseModel, validator
import re

class SecureInput(BaseModel):
    """Secure input validation."""
    text: str
    max_length: int = 1000
    
    @validator('text')
    def validate_text(cls, v):
        # Remove potentially dangerous characters
        v = re.sub(r'[<>"\']', '', v)
        
        # Check length
        if len(v) > 10000:
            raise ValueError('Text too long')
        
        return v
    
    @validator('max_length')
    def validate_max_length(cls, v):
        if v > 5000:
            raise ValueError('Max length too large')
        return v
```

## 📊 Analytics and Monitoring

### 1. Usage Analytics
```python
# analytics.py
import streamlit as st
import time
from typing import Dict, Any

class Analytics:
    """Track application usage."""
    
    def __init__(self):
        self.session_data = {}
    
    def track_event(self, event_name: str, data: Dict[str, Any] = None):
        """Track user events."""
        if data is None:
            data = {}
        
        event = {
            'timestamp': time.time(),
            'event': event_name,
            'data': data,
            'session_id': st.session_state.get('session_id', 'unknown')
        }
        
        # Store event (in production, send to analytics service)
        self.session_data[event_name] = event
    
    def track_page_view(self, page_name: str):
        """Track page views."""
        self.track_event('page_view', {'page': page_name})
    
    def track_api_call(self, endpoint: str, success: bool, duration: float):
        """Track API calls."""
        self.track_event('api_call', {
            'endpoint': endpoint,
            'success': success,
            'duration': duration
        })
```

### 2. Performance Monitoring
```python
# performance.py
import time
import psutil
import streamlit as st

def monitor_performance():
    """Monitor application performance."""
    # CPU usage
    cpu_percent = psutil.cpu_percent()
    
    # Memory usage
    memory = psutil.virtual_memory()
    memory_percent = memory.percent
    
    # Display metrics
    col1, col2, col3 = st.columns(3)
    
    with col1:
        st.metric("CPU Usage", f"{cpu_percent}%")
    
    with col2:
        st.metric("Memory Usage", f"{memory_percent}%")
    
    with col3:
        st.metric("Available Memory", f"{memory.available / (1024**3):.1f} GB")
```

## 🎯 Best Practices

### 1. Deployment Checklist
- [ ] Code tested and reviewed
- [ ] Environment variables configured
- [ ] Database migrations run
- [ ] Security measures implemented
- [ ] Monitoring set up
- [ ] Documentation updated
- [ ] Backup strategy in place

### 2. Rollback Strategy
```python
# rollback.py
import subprocess
import sys

def rollback_deployment(previous_version: str):
    """Rollback to previous version."""
    try:
        # Checkout previous version
        subprocess.run(['git', 'checkout', previous_version], check=True)
        
        # Redeploy
        subprocess.run(['streamlit', 'run', 'app.py'], check=True)
        
        print(f"Successfully rolled back to {previous_version}")
    except subprocess.CalledProcessError as e:
        print(f"Rollback failed: {e}")
        sys.exit(1)
```

### 3. Health Checks
```python
# health_check.py
import requests
import time
from typing import Dict, Any

def health_check() -> Dict[str, Any]:
    """Comprehensive health check."""
    health_status = {
        'timestamp': time.time(),
        'status': 'healthy',
        'checks': {}
    }
    
    # Check API connectivity
    try:
        response = requests.get('https://api.example.com/health', timeout=5)
        health_status['checks']['api'] = 'healthy' if response.status_code == 200 else 'unhealthy'
    except Exception as e:
        health_status['checks']['api'] = f'unhealthy: {e}'
        health_status['status'] = 'unhealthy'
    
    # Check database connectivity
    try:
        # Your database health check here
        health_status['checks']['database'] = 'healthy'
    except Exception as e:
        health_status['checks']['database'] = f'unhealthy: {e}'
        health_status['status'] = 'unhealthy'
    
    return health_status
```

## 📚 Next Steps

1. **Choose Deployment Platform** - Select the best platform for your needs
2. **Set Up CI/CD** - Implement automated deployment
3. **Configure Monitoring** - Set up performance and error tracking
4. **Implement Security** - Add security measures
5. **Test Deployment** - Verify everything works correctly
6. **Monitor Performance** - Track application performance
7. **Plan Scaling** - Prepare for increased usage

## 🔗 Additional Resources

- [Streamlit Cloud Documentation](https://docs.streamlit.io/streamlit-community-cloud)
- [Hugging Face Spaces Documentation](https://huggingface.co/docs/hub/spaces)
- [GitHub Pages Documentation](https://docs.github.com/en/pages)
- [Railway Documentation](https://docs.railway.app/)
- [Docker Documentation](https://docs.docker.com/)
- [Kubernetes Documentation](https://kubernetes.io/docs/)
