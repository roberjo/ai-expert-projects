# API Integration Guide

## 🎯 Overview

This guide covers all API integrations used throughout the AI Expert Projects curriculum. Learn how to work with various APIs, handle authentication, manage rate limits, and implement best practices for reliable API communication.

## 🔑 Core APIs

### 1. Gemini API (Google AI Studio)

#### Setup and Authentication
```python
import os
import google.generativeai as genai
from dotenv import load_dotenv

# Load environment variables
load_dotenv()

# Configure API
api_key = os.getenv('GEMINI_API_KEY')
genai.configure(api_key=api_key)

# Initialize model
model = genai.GenerativeModel('gemini-pro')
```

#### Basic Usage
```python
def generate_text(prompt: str) -> str:
    """Generate text using Gemini API."""
    try:
        response = model.generate_content(prompt)
        return response.text
    except Exception as e:
        print(f"Error generating text: {e}")
        return None

# Example usage
result = generate_text("Summarize this document: [document content]")
```

#### Advanced Features
```python
# Configure generation parameters
generation_config = {
    "temperature": 0.7,
    "top_p": 0.8,
    "top_k": 40,
    "max_output_tokens": 1024,
}

model = genai.GenerativeModel(
    'gemini-pro',
    generation_config=generation_config
)

# Generate with safety settings
safety_settings = [
    {
        "category": "HARM_CATEGORY_HARASSMENT",
        "threshold": "BLOCK_MEDIUM_AND_ABOVE"
    },
    {
        "category": "HARM_CATEGORY_HATE_SPEECH",
        "threshold": "BLOCK_MEDIUM_AND_ABOVE"
    }
]

model = genai.GenerativeModel(
    'gemini-pro',
    safety_settings=safety_settings
)
```

#### Rate Limiting and Error Handling
```python
import time
import random
from typing import Optional

def safe_generate_text(prompt: str, max_retries: int = 3) -> Optional[str]:
    """Generate text with retry logic and rate limiting."""
    for attempt in range(max_retries):
        try:
            response = model.generate_content(prompt)
            return response.text
        except Exception as e:
            if "quota" in str(e).lower() or "rate" in str(e).lower():
                # Rate limit hit, wait and retry
                wait_time = (2 ** attempt) + random.uniform(0, 1)
                print(f"Rate limit hit, waiting {wait_time:.2f} seconds...")
                time.sleep(wait_time)
            else:
                print(f"Error on attempt {attempt + 1}: {e}")
                if attempt == max_retries - 1:
                    return None
    return None
```

### 2. Gmail SMTP API

#### Setup and Configuration
```python
import smtplib
from email.mime.text import MIMEText
from email.mime.multipart import MIMEMultipart
import os
from dotenv import load_dotenv

load_dotenv()

# Gmail configuration
GMAIL_ADDRESS = os.getenv('GMAIL_ADDRESS')
GMAIL_APP_PASSWORD = os.getenv('GMAIL_APP_PASSWORD')
SMTP_SERVER = "smtp.gmail.com"
SMTP_PORT = 587
```

#### Send Email Function
```python
def send_email(to_email: str, subject: str, body: str) -> bool:
    """Send email using Gmail SMTP."""
    try:
        # Create message
        msg = MIMEMultipart()
        msg['From'] = GMAIL_ADDRESS
        msg['To'] = to_email
        msg['Subject'] = subject
        
        # Add body
        msg.attach(MIMEText(body, 'plain'))
        
        # Connect to server
        server = smtplib.SMTP(SMTP_SERVER, SMTP_PORT)
        server.starttls()
        server.login(GMAIL_ADDRESS, GMAIL_APP_PASSWORD)
        
        # Send email
        text = msg.as_string()
        server.sendmail(GMAIL_ADDRESS, to_email, text)
        server.quit()
        
        return True
    except Exception as e:
        print(f"Error sending email: {e}")
        return False
```

#### HTML Email Support
```python
def send_html_email(to_email: str, subject: str, html_body: str, text_body: str = None) -> bool:
    """Send HTML email with fallback text."""
    try:
        msg = MIMEMultipart('alternative')
        msg['From'] = GMAIL_ADDRESS
        msg['To'] = to_email
        msg['Subject'] = subject
        
        # Add text version
        if text_body:
            text_part = MIMEText(text_body, 'plain')
            msg.attach(text_part)
        
        # Add HTML version
        html_part = MIMEText(html_body, 'html')
        msg.attach(html_part)
        
        # Send email
        server = smtplib.SMTP(SMTP_SERVER, SMTP_PORT)
        server.starttls()
        server.login(GMAIL_ADDRESS, GMAIL_APP_PASSWORD)
        server.sendmail(GMAIL_ADDRESS, to_email, msg.as_string())
        server.quit()
        
        return True
    except Exception as e:
        print(f"Error sending HTML email: {e}")
        return False
```

### 3. Hugging Face API

#### Setup and Authentication
```python
import requests
import os
from dotenv import load_dotenv

load_dotenv()

# Hugging Face configuration
HF_API_URL = "https://api-inference.huggingface.co/models"
HF_API_KEY = os.getenv('HUGGINGFACE_API_KEY')  # Optional for free tier
```

#### Text Generation
```python
def generate_text_hf(model_name: str, prompt: str, max_length: int = 100) -> str:
    """Generate text using Hugging Face API."""
    url = f"{HF_API_URL}/{model_name}"
    
    headers = {}
    if HF_API_KEY:
        headers["Authorization"] = f"Bearer {HF_API_KEY}"
    
    payload = {
        "inputs": prompt,
        "parameters": {
            "max_length": max_length,
            "temperature": 0.7,
            "do_sample": True
        }
    }
    
    try:
        response = requests.post(url, headers=headers, json=payload)
        response.raise_for_status()
        
        result = response.json()
        if isinstance(result, list) and len(result) > 0:
            return result[0].get('generated_text', '')
        return ""
    except Exception as e:
        print(f"Error generating text with HF: {e}")
        return ""
```

#### Model Comparison
```python
def compare_models(prompt: str, models: list) -> dict:
    """Compare outputs from multiple models."""
    results = {}
    
    for model_name in models:
        try:
            output = generate_text_hf(model_name, prompt)
            results[model_name] = output
        except Exception as e:
            results[model_name] = f"Error: {e}"
    
    return results
```

### 4. Local Ollama API

#### Setup and Configuration
```python
import requests
import json

# Ollama configuration
OLLAMA_BASE_URL = "http://localhost:11434"

def list_models() -> list:
    """List available Ollama models."""
    try:
        response = requests.get(f"{OLLAMA_BASE_URL}/api/tags")
        response.raise_for_status()
        data = response.json()
        return [model['name'] for model in data.get('models', [])]
    except Exception as e:
        print(f"Error listing models: {e}")
        return []
```

#### Generate Text
```python
def generate_text_ollama(model: str, prompt: str) -> str:
    """Generate text using local Ollama model."""
    url = f"{OLLAMA_BASE_URL}/api/generate"
    
    payload = {
        "model": model,
        "prompt": prompt,
        "stream": False
    }
    
    try:
        response = requests.post(url, json=payload)
        response.raise_for_status()
        
        result = response.json()
        return result.get('response', '')
    except Exception as e:
        print(f"Error generating text with Ollama: {e}")
        return ""
```

#### Stream Response
```python
def generate_text_stream(model: str, prompt: str):
    """Generate text with streaming response."""
    url = f"{OLLAMA_BASE_URL}/api/generate"
    
    payload = {
        "model": model,
        "prompt": prompt,
        "stream": True
    }
    
    try:
        response = requests.post(url, json=payload, stream=True)
        response.raise_for_status()
        
        for line in response.iter_lines():
            if line:
                data = json.loads(line)
                if 'response' in data:
                    yield data['response']
    except Exception as e:
        print(f"Error streaming from Ollama: {e}")
```

## 🔄 API Management Patterns

### 1. API Client Class
```python
class APIClient:
    """Base class for API clients."""
    
    def __init__(self, base_url: str, api_key: str = None):
        self.base_url = base_url
        self.api_key = api_key
        self.session = requests.Session()
        
        if api_key:
            self.session.headers.update({
                'Authorization': f'Bearer {api_key}'
            })
    
    def get(self, endpoint: str, params: dict = None) -> dict:
        """Make GET request."""
        url = f"{self.base_url}/{endpoint}"
        response = self.session.get(url, params=params)
        response.raise_for_status()
        return response.json()
    
    def post(self, endpoint: str, data: dict = None) -> dict:
        """Make POST request."""
        url = f"{self.base_url}/{endpoint}"
        response = self.session.post(url, json=data)
        response.raise_for_status()
        return response.json()
```

### 2. Rate Limiting Decorator
```python
import time
from functools import wraps
from typing import Callable

def rate_limit(calls_per_minute: int = 60):
    """Decorator to limit API calls per minute."""
    def decorator(func: Callable) -> Callable:
        last_called = [0.0]
        
        @wraps(func)
        def wrapper(*args, **kwargs):
            elapsed = time.time() - last_called[0]
            left_to_wait = 60.0 / calls_per_minute - elapsed
            if left_to_wait > 0:
                time.sleep(left_to_wait)
            ret = func(*args, **kwargs)
            last_called[0] = time.time()
            return ret
        return wrapper
    return decorator

# Usage
@rate_limit(calls_per_minute=60)
def call_gemini_api(prompt: str):
    return model.generate_content(prompt)
```

### 3. Caching Strategy
```python
from functools import lru_cache
import hashlib
import json

def cache_api_response(func):
    """Cache API responses based on input hash."""
    cache = {}
    
    @wraps(func)
    def wrapper(*args, **kwargs):
        # Create cache key from arguments
        key = hashlib.md5(
            json.dumps([args, kwargs], sort_keys=True).encode()
        ).hexdigest()
        
        if key in cache:
            return cache[key]
        
        result = func(*args, **kwargs)
        cache[key] = result
        return result
    
    return wrapper

# Usage
@cache_api_response
def generate_summary(text: str):
    return model.generate_content(f"Summarize: {text}")
```

## 🛡️ Error Handling and Resilience

### 1. Retry Mechanism
```python
import time
import random
from typing import Callable, Any

def retry_on_failure(max_retries: int = 3, backoff_factor: float = 1.0):
    """Decorator to retry function on failure."""
    def decorator(func: Callable) -> Callable:
        @wraps(func)
        def wrapper(*args, **kwargs) -> Any:
            for attempt in range(max_retries):
                try:
                    return func(*args, **kwargs)
                except Exception as e:
                    if attempt == max_retries - 1:
                        raise e
                    
                    wait_time = backoff_factor * (2 ** attempt) + random.uniform(0, 1)
                    print(f"Attempt {attempt + 1} failed: {e}. Retrying in {wait_time:.2f}s...")
                    time.sleep(wait_time)
            
            return None
        return wrapper
    return decorator
```

### 2. Circuit Breaker Pattern
```python
import time
from enum import Enum

class CircuitState(Enum):
    CLOSED = "closed"
    OPEN = "open"
    HALF_OPEN = "half_open"

class CircuitBreaker:
    """Circuit breaker for API calls."""
    
    def __init__(self, failure_threshold: int = 5, timeout: int = 60):
        self.failure_threshold = failure_threshold
        self.timeout = timeout
        self.failure_count = 0
        self.last_failure_time = None
        self.state = CircuitState.CLOSED
    
    def call(self, func: Callable, *args, **kwargs):
        """Execute function with circuit breaker protection."""
        if self.state == CircuitState.OPEN:
            if time.time() - self.last_failure_time > self.timeout:
                self.state = CircuitState.HALF_OPEN
            else:
                raise Exception("Circuit breaker is OPEN")
        
        try:
            result = func(*args, **kwargs)
            self.on_success()
            return result
        except Exception as e:
            self.on_failure()
            raise e
    
    def on_success(self):
        """Handle successful call."""
        self.failure_count = 0
        self.state = CircuitState.CLOSED
    
    def on_failure(self):
        """Handle failed call."""
        self.failure_count += 1
        self.last_failure_time = time.time()
        
        if self.failure_count >= self.failure_threshold:
            self.state = CircuitState.OPEN
```

## 📊 Monitoring and Analytics

### 1. API Usage Tracking
```python
import time
from collections import defaultdict
from typing import Dict, Any

class APIUsageTracker:
    """Track API usage and performance."""
    
    def __init__(self):
        self.usage_stats = defaultdict(list)
        self.error_stats = defaultdict(int)
    
    def track_call(self, api_name: str, duration: float, success: bool):
        """Track API call statistics."""
        self.usage_stats[api_name].append({
            'timestamp': time.time(),
            'duration': duration,
            'success': success
        })
        
        if not success:
            self.error_stats[api_name] += 1
    
    def get_stats(self, api_name: str) -> Dict[str, Any]:
        """Get statistics for an API."""
        calls = self.usage_stats[api_name]
        if not calls:
            return {}
        
        durations = [call['duration'] for call in calls]
        success_rate = sum(1 for call in calls if call['success']) / len(calls)
        
        return {
            'total_calls': len(calls),
            'success_rate': success_rate,
            'avg_duration': sum(durations) / len(durations),
            'max_duration': max(durations),
            'min_duration': min(durations),
            'error_count': self.error_stats[api_name]
        }
```

### 2. Performance Monitoring
```python
import time
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
            print(f"{func.__name__} took {duration:.2f}s, success: {success}")
        
        return result
    return wrapper
```

## 🔐 Security Best Practices

### 1. API Key Management
```python
import os
from cryptography.fernet import Fernet
import base64

class SecureAPIKeyManager:
    """Secure API key management."""
    
    def __init__(self, master_key: str = None):
        if master_key:
            self.cipher = Fernet(master_key.encode())
        else:
            self.cipher = None
    
    def encrypt_key(self, api_key: str) -> str:
        """Encrypt API key."""
        if not self.cipher:
            return api_key
        return self.cipher.encrypt(api_key.encode()).decode()
    
    def decrypt_key(self, encrypted_key: str) -> str:
        """Decrypt API key."""
        if not self.cipher:
            return encrypted_key
        return self.cipher.decrypt(encrypted_key.encode()).decode()
    
    def get_key(self, key_name: str) -> str:
        """Get API key from environment."""
        key = os.getenv(key_name)
        if not key:
            raise ValueError(f"API key {key_name} not found")
        return key
```

### 2. Input Validation
```python
from pydantic import BaseModel, validator
from typing import Optional

class APIRequest(BaseModel):
    """Base class for API requests."""
    prompt: str
    max_length: Optional[int] = 100
    temperature: Optional[float] = 0.7
    
    @validator('prompt')
    def prompt_must_not_be_empty(cls, v):
        if not v.strip():
            raise ValueError('Prompt cannot be empty')
        if len(v) > 10000:
            raise ValueError('Prompt too long')
        return v
    
    @validator('max_length')
    def max_length_must_be_positive(cls, v):
        if v and v <= 0:
            raise ValueError('Max length must be positive')
        return v
    
    @validator('temperature')
    def temperature_must_be_valid(cls, v):
        if v < 0 or v > 2:
            raise ValueError('Temperature must be between 0 and 2')
        return v
```

## 🧪 Testing API Integrations

### 1. Mock API Responses
```python
from unittest.mock import patch, MagicMock
import pytest

def test_gemini_api_integration():
    """Test Gemini API integration."""
    with patch('google.generativeai.GenerativeModel') as mock_model:
        # Mock the response
        mock_response = MagicMock()
        mock_response.text = "Test response"
        mock_model.return_value.generate_content.return_value = mock_response
        
        # Test the function
        result = generate_text("Test prompt")
        assert result == "Test response"
        mock_model.return_value.generate_content.assert_called_once_with("Test prompt")
```

### 2. API Health Checks
```python
def health_check_apis() -> dict:
    """Check health of all APIs."""
    health_status = {}
    
    # Check Gemini API
    try:
        response = model.generate_content("Health check")
        health_status['gemini'] = 'healthy'
    except Exception as e:
        health_status['gemini'] = f'unhealthy: {e}'
    
    # Check Gmail SMTP
    try:
        server = smtplib.SMTP(SMTP_SERVER, SMTP_PORT)
        server.starttls()
        server.login(GMAIL_ADDRESS, GMAIL_APP_PASSWORD)
        server.quit()
        health_status['gmail'] = 'healthy'
    except Exception as e:
        health_status['gmail'] = f'unhealthy: {e}'
    
    return health_status
```

## 📚 Next Steps

1. **Implement API Clients** - Create reusable API client classes
2. **Add Error Handling** - Implement comprehensive error handling
3. **Set Up Monitoring** - Track API usage and performance
4. **Test Integrations** - Write tests for all API integrations
5. **Optimize Performance** - Implement caching and rate limiting
6. **Secure APIs** - Follow security best practices
7. **Document Usage** - Document all API integrations

## 🔗 Additional Resources

- [Gemini API Documentation](https://ai.google.dev/docs)
- [Gmail SMTP Documentation](https://developers.google.com/gmail/api)
- [Hugging Face API Documentation](https://huggingface.co/docs/api-inference)
- [Ollama Documentation](https://ollama.ai/docs)
- [Python Requests Documentation](https://requests.readthedocs.io/)
- [Pydantic Documentation](https://pydantic-docs.helpmanual.io/)
