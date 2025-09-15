# Setup Guide

## 🚀 Getting Started

This guide will help you set up your development environment for the AI Expert Projects curriculum. Follow these steps to get everything configured and ready for your 12-week learning journey.

## 📋 Prerequisites

### System Requirements
- **Operating System:** Windows 10+, macOS 10.14+, or Linux
- **RAM:** Minimum 8GB (16GB recommended)
- **Storage:** At least 10GB free space
- **Internet:** Stable internet connection for API calls and downloads

### Software Requirements
- **Python 3.8+** - Primary development language
- **Git** - Version control system
- **Code Editor** - VS Code recommended
- **Web Browser** - Chrome, Firefox, or Safari

## 🐍 Python Setup

### 1. Install Python
```bash
# Check if Python is already installed
python --version

# If not installed, download from https://python.org
# Make sure to check "Add Python to PATH" during installation
```

### 2. Create Virtual Environment
```bash
# Create project directory
mkdir ai-expert-projects
cd ai-expert-projects

# Create virtual environment
python -m venv venv

# Activate virtual environment
# On Windows:
venv\Scripts\activate
# On macOS/Linux:
source venv/bin/activate
```

### 3. Install Core Dependencies
```bash
# Upgrade pip
python -m pip install --upgrade pip

# Install core packages
pip install streamlit
pip install google-generativeai
pip install python-dotenv
pip install requests
pip install pandas
pip install numpy
```

## 🔑 API Setup

### 1. Google AI Studio (Gemini API)
1. Go to [Google AI Studio](https://aistudio.google.com/)
2. Sign in with your Google account
3. Click "Get API Key"
4. Create a new API key
5. Copy the API key for later use

### 2. Environment Variables
```bash
# Create .env file
touch .env

# Add your API key
echo "GEMINI_API_KEY=your_api_key_here" >> .env
```

### 3. Gmail Setup (for email projects)
1. Enable 2-factor authentication on your Google account
2. Generate an app password:
   - Go to Google Account settings
   - Security → 2-Step Verification → App passwords
   - Generate password for "Mail"
3. Add to .env file:
```bash
echo "GMAIL_APP_PASSWORD=your_app_password_here" >> .env
echo "GMAIL_ADDRESS=your_email@gmail.com" >> .env
```

## 🛠️ Development Tools

### 1. VS Code Setup
```bash
# Install VS Code from https://code.visualstudio.com/
# Install recommended extensions:
# - Python
# - GitLens
# - Streamlit
# - Jupyter
```

### 2. Git Configuration
```bash
# Configure Git
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# Initialize repository
git init
```

### 3. Project Structure
```bash
# Create project structure
mkdir -p src/{api,models,services,utils}
mkdir -p tests
mkdir -p docs
mkdir -p data

# Create initial files
touch src/__init__.py
touch src/main.py
touch requirements.txt
touch .gitignore
touch README.md
```

## 📦 Package Management

### 1. Requirements File
```bash
# Create requirements.txt
cat > requirements.txt << EOF
streamlit>=1.28.0
google-generativeai>=0.3.0
python-dotenv>=1.0.0
requests>=2.31.0
pandas>=2.0.0
numpy>=1.24.0
pytest>=7.4.0
black>=23.0.0
flake8>=6.0.0
EOF
```

### 2. Install Dependencies
```bash
# Install all dependencies
pip install -r requirements.txt

# Verify installation
pip list
```

## 🔧 Configuration Files

### 1. .gitignore
```bash
# Create .gitignore
cat > .gitignore << EOF
# Python
__pycache__/
*.py[cod]
*$py.class
*.so
.Python
build/
develop-eggs/
dist/
downloads/
eggs/
.eggs/
lib/
lib64/
parts/
sdist/
var/
wheels/
*.egg-info/
.installed.cfg
*.egg

# Virtual Environment
venv/
env/
ENV/

# Environment Variables
.env
.env.local
.env.development.local
.env.test.local
.env.production.local

# IDE
.vscode/
.idea/
*.swp
*.swo

# OS
.DS_Store
Thumbs.db

# Logs
*.log
logs/

# Database
*.db
*.sqlite
*.sqlite3

# Data files
data/
*.csv
*.json
*.txt
*.pdf
EOF
```

### 2. .env.example
```bash
# Create .env.example
cat > .env.example << EOF
# Gemini API
GEMINI_API_KEY=your_gemini_api_key_here

# Gmail Configuration
GMAIL_ADDRESS=your_email@gmail.com
GMAIL_APP_PASSWORD=your_app_password_here

# Database
DATABASE_URL=sqlite:///app.db

# Application Settings
DEBUG=True
LOG_LEVEL=INFO
EOF
```

## 🧪 Testing Setup

### 1. Test Configuration
```bash
# Create test configuration
mkdir -p tests/{unit,integration,e2e}
touch tests/__init__.py
touch tests/conftest.py
```

### 2. Pytest Configuration
```bash
# Create pytest.ini
cat > pytest.ini << EOF
[tool:pytest]
testpaths = tests
python_files = test_*.py
python_classes = Test*
python_functions = test_*
addopts = -v --tb=short
EOF
```

## 🚀 Deployment Setup

### 1. Streamlit Cloud
1. Go to [Streamlit Cloud](https://streamlit.io/cloud)
2. Sign in with your GitHub account
3. Connect your repository
4. Configure deployment settings

### 2. Hugging Face Spaces
1. Go to [Hugging Face Spaces](https://huggingface.co/spaces)
2. Create a new space
3. Choose Streamlit as the SDK
4. Connect your repository

## 📊 Monitoring Setup

### 1. Logging Configuration
```python
# Create logging configuration
cat > src/config/logging.py << EOF
import logging
import sys
from pathlib import Path

def setup_logging():
    """Set up logging configuration."""
    log_dir = Path("logs")
    log_dir.mkdir(exist_ok=True)
    
    logging.basicConfig(
        level=logging.INFO,
        format='%(asctime)s - %(name)s - %(levelname)s - %(message)s',
        handlers=[
            logging.FileHandler(log_dir / "app.log"),
            logging.StreamHandler(sys.stdout)
        ]
    )
    
    return logging.getLogger(__name__)
EOF
```

### 2. Error Tracking
```python
# Create error handling
cat > src/utils/error_handling.py << EOF
import logging
from functools import wraps

logger = logging.getLogger(__name__)

def handle_errors(func):
    """Decorator to handle errors gracefully."""
    @wraps(func)
    def wrapper(*args, **kwargs):
        try:
            return func(*args, **kwargs)
        except Exception as e:
            logger.error(f"Error in {func.__name__}: {e}")
            return None
    return wrapper
EOF
```

## 🔍 Verification

### 1. Test Installation
```bash
# Test Python installation
python --version

# Test package installation
python -c "import streamlit; print('Streamlit installed successfully')"
python -c "import google.generativeai; print('Gemini API installed successfully')"

# Test virtual environment
which python  # Should point to venv/bin/python
```

### 2. Test API Connection
```python
# Create test script
cat > test_setup.py << EOF
import os
from dotenv import load_dotenv
import google.generativeai as genai

# Load environment variables
load_dotenv()

# Test API connection
api_key = os.getenv('GEMINI_API_KEY')
if api_key:
    genai.configure(api_key=api_key)
    model = genai.GenerativeModel('gemini-pro')
    response = model.generate_content("Hello, world!")
    print("API connection successful!")
    print(f"Response: {response.text}")
else:
    print("API key not found. Please check your .env file.")
EOF

# Run test
python test_setup.py
```

## 🎯 Next Steps

### 1. Project 1 Setup
```bash
# Create Project 1 directory
mkdir -p projects/project-1-document-summarizer
cd projects/project-1-document-summarizer

# Create project structure
mkdir -p src/{api,models,services,utils}
touch src/__init__.py
touch src/main.py
touch requirements.txt
touch README.md
```

### 2. First Application
```python
# Create basic Streamlit app
cat > src/main.py << EOF
import streamlit as st
import os
from dotenv import load_dotenv

# Load environment variables
load_dotenv()

st.title("AI Expert Projects - Project 1")
st.write("Document Summarizer → Email Sender")

# Test API key
api_key = os.getenv('GEMINI_API_KEY')
if api_key:
    st.success("✅ Gemini API key configured!")
else:
    st.error("❌ Gemini API key not found. Please check your .env file.")

st.write("Ready to start building!")
EOF
```

### 3. Run First App
```bash
# Run Streamlit app
streamlit run src/main.py
```

## 🆘 Troubleshooting

### Common Issues

#### 1. Python Not Found
```bash
# Solution: Add Python to PATH
# Windows: Add Python installation directory to PATH
# macOS/Linux: Use python3 instead of python
python3 --version
```

#### 2. Virtual Environment Issues
```bash
# Solution: Recreate virtual environment
rm -rf venv
python -m venv venv
source venv/bin/activate  # or venv\Scripts\activate on Windows
```

#### 3. API Key Issues
```bash
# Solution: Check .env file
cat .env
# Make sure GEMINI_API_KEY is set correctly
```

#### 4. Package Installation Issues
```bash
# Solution: Upgrade pip and try again
python -m pip install --upgrade pip
pip install --no-cache-dir -r requirements.txt
```

### Getting Help

1. **Check Documentation** - Review project documentation
2. **Search Issues** - Look for similar problems online
3. **Ask Community** - Post questions in relevant forums
4. **Review Logs** - Check application logs for errors

## 📚 Additional Resources

### Documentation
- [Python Documentation](https://docs.python.org/)
- [Streamlit Documentation](https://docs.streamlit.io/)
- [Gemini API Documentation](https://ai.google.dev/docs)
- [Git Documentation](https://git-scm.com/doc)

### Learning Resources
- [Python Tutorial](https://docs.python.org/3/tutorial/)
- [Streamlit Tutorial](https://docs.streamlit.io/get-started/tutorials)
- [API Best Practices](https://restfulapi.net/)
- [Git Tutorial](https://git-scm.com/docs/gittutorial)

## 🎉 Congratulations!

You've successfully set up your development environment for the AI Expert Projects curriculum. You're now ready to start building amazing AI applications!

### What's Next?
1. **Start Project 1** - Begin with the Document Summarizer
2. **Follow the Timeline** - Work through the 12-week curriculum
3. **Build Your Portfolio** - Create deployable applications
4. **Share Your Progress** - Connect with the community

Happy coding! 🚀
