# Phase 1: Foundations (Weeks 1-3)

## 🎯 Overview

Phase 1 focuses on building foundational skills in AI integration and simple application development. You'll learn to work with the Gemini API, create user interfaces with Streamlit, and compare different AI models.

## 📋 Learning Objectives

By the end of Phase 1, you will be able to:
- Integrate the Gemini API into Python applications
- Build interactive web interfaces with Streamlit
- Process and analyze text documents
- Compare different AI models and their outputs
- Deploy applications to free hosting platforms
- Handle basic error scenarios and user input validation

## 🏗️ Project Architecture

### Common Patterns
All Phase 1 projects follow these architectural patterns:

1. **API-First Design** - Clear separation between API calls and UI
2. **Error Handling** - Graceful handling of API failures
3. **Input Validation** - User input sanitization and validation
4. **Responsive UI** - Mobile-friendly Streamlit interfaces
5. **Environment Configuration** - Secure API key management

### Technology Stack
- **Python 3.8+** - Core development language
- **Streamlit** - Web application framework
- **Gemini API** - Primary AI model service
- **Hugging Face** - Open-source model access
- **Gmail SMTP** - Email automation
- **SQLite** - Lightweight data storage

## 📁 Project Structure

```
phase-1/
├── project-1-document-summarizer/
│   ├── src/
│   │   ├── main.py
│   │   ├── api/
│   │   │   └── gemini_client.py
│   │   ├── services/
│   │   │   └── email_service.py
│   │   └── utils/
│   │       └── file_utils.py
│   ├── tests/
│   ├── requirements.txt
│   └── README.md
├── project-2-meeting-notes/
│   ├── src/
│   │   ├── main.py
│   │   ├── services/
│   │   │   └── meeting_processor.py
│   │   └── utils/
│   │       └── text_utils.py
│   ├── tests/
│   ├── requirements.txt
│   └── README.md
└── project-3-model-comparison/
    ├── src/
    │   ├── main.py
    │   ├── api/
    │   │   ├── gemini_client.py
    │   │   └── huggingface_client.py
    │   └── services/
    │       └── comparison_service.py
    ├── tests/
    ├── requirements.txt
    └── README.md
```

## 🚀 Project 1: Document Summarizer → Email Sender

### Overview
Build a system that takes a document, summarizes it using Gemini API, and sends the summary via email.

### Key Features
- Document text input (file upload or text area)
- AI-powered summarization
- Email automation with Gmail SMTP
- Error handling and validation
- Simple, intuitive interface

### Implementation Steps

#### 1. Setup and Configuration
```python
# src/api/gemini_client.py
import google.generativeai as genai
import os
from dotenv import load_dotenv

load_dotenv()

class GeminiClient:
    def __init__(self):
        api_key = os.getenv('GEMINI_API_KEY')
        if not api_key:
            raise ValueError("GEMINI_API_KEY not found in environment variables")
        
        genai.configure(api_key=api_key)
        self.model = genai.GenerativeModel('gemini-pro')
    
    def summarize_text(self, text: str, max_length: int = 200) -> str:
        """Summarize text using Gemini API."""
        try:
            prompt = f"Summarize the following text in {max_length} words or less:\n\n{text}"
            response = self.model.generate_content(prompt)
            return response.text
        except Exception as e:
            raise Exception(f"Failed to summarize text: {e}")
```

#### 2. Email Service
```python
# src/services/email_service.py
import smtplib
from email.mime.text import MIMEText
from email.mime.multipart import MIMEMultipart
import os
from dotenv import load_dotenv

load_dotenv()

class EmailService:
    def __init__(self):
        self.gmail_address = os.getenv('GMAIL_ADDRESS')
        self.app_password = os.getenv('GMAIL_APP_PASSWORD')
        self.smtp_server = "smtp.gmail.com"
        self.smtp_port = 587
    
    def send_summary_email(self, to_email: str, subject: str, summary: str) -> bool:
        """Send summary via email."""
        try:
            msg = MIMEMultipart()
            msg['From'] = self.gmail_address
            msg['To'] = to_email
            msg['Subject'] = subject
            
            body = f"Document Summary:\n\n{summary}"
            msg.attach(MIMEText(body, 'plain'))
            
            server = smtplib.SMTP(self.smtp_server, self.smtp_port)
            server.starttls()
            server.login(self.gmail_address, self.app_password)
            
            text = msg.as_string()
            server.sendmail(self.gmail_address, to_email, text)
            server.quit()
            
            return True
        except Exception as e:
            print(f"Error sending email: {e}")
            return False
```

#### 3. Main Application
```python
# src/main.py
import streamlit as st
from api.gemini_client import GeminiClient
from services.email_service import EmailService
import os

def main():
    st.set_page_config(
        page_title="Document Summarizer",
        page_icon="📄",
        layout="wide"
    )
    
    st.title("📄 Document Summarizer → Email Sender")
    st.write("Upload a document or paste text to get an AI-generated summary sent to your email.")
    
    # Initialize services
    try:
        gemini_client = GeminiClient()
        email_service = EmailService()
    except Exception as e:
        st.error(f"Configuration error: {e}")
        return
    
    # Input section
    st.header("📝 Input Document")
    
    input_method = st.radio(
        "Choose input method:",
        ["Upload File", "Paste Text"]
    )
    
    document_text = ""
    
    if input_method == "Upload File":
        uploaded_file = st.file_uploader(
            "Choose a file",
            type=['txt', 'pdf', 'docx']
        )
        
        if uploaded_file:
            if uploaded_file.type == "text/plain":
                document_text = str(uploaded_file.read(), "utf-8")
            else:
                st.error("Please upload a .txt file for now.")
    
    else:
        document_text = st.text_area(
            "Paste your document text here:",
            height=200,
            placeholder="Enter your document content..."
        )
    
    # Configuration section
    st.header("⚙️ Configuration")
    
    col1, col2 = st.columns(2)
    
    with col1:
        max_length = st.slider(
            "Maximum summary length (words):",
            min_value=50,
            max_value=500,
            value=200
        )
    
    with col2:
        email_address = st.text_input(
            "Email address to send summary:",
            placeholder="your.email@example.com"
        )
    
    # Process button
    if st.button("🚀 Generate Summary & Send Email", type="primary"):
        if not document_text.strip():
            st.error("Please provide document text.")
            return
        
        if not email_address:
            st.error("Please provide an email address.")
            return
        
        # Generate summary
        with st.spinner("Generating summary..."):
            try:
                summary = gemini_client.summarize_text(document_text, max_length)
                
                st.success("✅ Summary generated successfully!")
                st.subheader("📋 Generated Summary")
                st.write(summary)
                
                # Send email
                with st.spinner("Sending email..."):
                    subject = "Document Summary - AI Expert Project"
                    success = email_service.send_summary_email(
                        email_address, subject, summary
                    )
                    
                    if success:
                        st.success("✅ Email sent successfully!")
                    else:
                        st.error("❌ Failed to send email. Please check your configuration.")
                        
            except Exception as e:
                st.error(f"❌ Error: {e}")

if __name__ == "__main__":
    main()
```

### Testing
```python
# tests/test_document_summarizer.py
import pytest
from unittest.mock import patch, MagicMock
from src.api.gemini_client import GeminiClient
from src.services.email_service import EmailService

class TestDocumentSummarizer:
    def test_gemini_client_initialization(self):
        """Test Gemini client initialization."""
        with patch.dict('os.environ', {'GEMINI_API_KEY': 'test_key'}):
            client = GeminiClient()
            assert client is not None
    
    def test_summarize_text(self):
        """Test text summarization."""
        with patch.dict('os.environ', {'GEMINI_API_KEY': 'test_key'}):
            client = GeminiClient()
            
            with patch.object(client.model, 'generate_content') as mock_generate:
                mock_response = MagicMock()
                mock_response.text = "Test summary"
                mock_generate.return_value = mock_response
                
                result = client.summarize_text("Test document")
                assert result == "Test summary"
    
    def test_email_service_initialization(self):
        """Test email service initialization."""
        with patch.dict('os.environ', {
            'GMAIL_ADDRESS': 'test@gmail.com',
            'GMAIL_APP_PASSWORD': 'test_password'
        }):
            service = EmailService()
            assert service is not None
```

## 🚀 Project 2: Meeting Notes Summarizer

### Overview
Create a web application that processes meeting transcripts and generates structured summaries with action items.

### Key Features
- Meeting transcript input
- Structured summary generation
- Action item extraction
- Key points identification
- Clean, professional output format

### Implementation Steps

#### 1. Meeting Processor Service
```python
# src/services/meeting_processor.py
import google.generativeai as genai
import os
from dotenv import load_dotenv
from typing import Dict, List

load_dotenv()

class MeetingProcessor:
    def __init__(self):
        api_key = os.getenv('GEMINI_API_KEY')
        if not api_key:
            raise ValueError("GEMINI_API_KEY not found")
        
        genai.configure(api_key=api_key)
        self.model = genai.GenerativeModel('gemini-pro')
    
    def process_meeting(self, transcript: str) -> Dict[str, any]:
        """Process meeting transcript and extract structured information."""
        try:
            # Generate summary
            summary = self._generate_summary(transcript)
            
            # Extract action items
            action_items = self._extract_action_items(transcript)
            
            # Extract key points
            key_points = self._extract_key_points(transcript)
            
            # Extract decisions
            decisions = self._extract_decisions(transcript)
            
            return {
                'summary': summary,
                'action_items': action_items,
                'key_points': key_points,
                'decisions': decisions
            }
        except Exception as e:
            raise Exception(f"Failed to process meeting: {e}")
    
    def _generate_summary(self, transcript: str) -> str:
        """Generate meeting summary."""
        prompt = f"""
        Summarize the following meeting transcript in 3-4 sentences:
        
        {transcript}
        """
        response = self.model.generate_content(prompt)
        return response.text
    
    def _extract_action_items(self, transcript: str) -> List[str]:
        """Extract action items from transcript."""
        prompt = f"""
        Extract all action items from this meeting transcript. 
        Return them as a numbered list:
        
        {transcript}
        """
        response = self.model.generate_content(prompt)
        return response.text.split('\n')
    
    def _extract_key_points(self, transcript: str) -> List[str]:
        """Extract key points from transcript."""
        prompt = f"""
        Extract the key points discussed in this meeting.
        Return them as a bulleted list:
        
        {transcript}
        """
        response = self.model.generate_content(prompt)
        return response.text.split('\n')
    
    def _extract_decisions(self, transcript: str) -> List[str]:
        """Extract decisions made in the meeting."""
        prompt = f"""
        Extract all decisions made in this meeting.
        Return them as a numbered list:
        
        {transcript}
        """
        response = self.model.generate_content(prompt)
        return response.text.split('\n')
```

#### 2. Main Application
```python
# src/main.py
import streamlit as st
from services.meeting_processor import MeetingProcessor
import os

def main():
    st.set_page_config(
        page_title="Meeting Notes Summarizer",
        page_icon="📝",
        layout="wide"
    )
    
    st.title("📝 Meeting Notes Summarizer")
    st.write("Upload meeting transcripts and get AI-generated summaries with action items.")
    
    # Initialize processor
    try:
        processor = MeetingProcessor()
    except Exception as e:
        st.error(f"Configuration error: {e}")
        return
    
    # Input section
    st.header("📄 Upload Meeting Transcript")
    
    transcript = st.text_area(
        "Paste your meeting transcript here:",
        height=300,
        placeholder="Enter the meeting transcript..."
    )
    
    # Process button
    if st.button("🚀 Process Meeting Notes", type="primary"):
        if not transcript.strip():
            st.error("Please provide a meeting transcript.")
            return
        
        with st.spinner("Processing meeting notes..."):
            try:
                result = processor.process_meeting(transcript)
                
                # Display results
                st.success("✅ Meeting notes processed successfully!")
                
                # Summary
                st.subheader("📋 Meeting Summary")
                st.write(result['summary'])
                
                # Key Points
                st.subheader("🔑 Key Points")
                for point in result['key_points']:
                    if point.strip():
                        st.write(f"• {point.strip()}")
                
                # Decisions
                st.subheader("✅ Decisions Made")
                for decision in result['decisions']:
                    if decision.strip():
                        st.write(f"• {decision.strip()}")
                
                # Action Items
                st.subheader("📋 Action Items")
                for item in result['action_items']:
                    if item.strip():
                        st.write(f"• {item.strip()}")
                
            except Exception as e:
                st.error(f"❌ Error processing meeting: {e}")

if __name__ == "__main__":
    main()
```

## 🚀 Project 3: Gemini vs OSS Comparison Tool

### Overview
Build a tool that compares outputs from Gemini API with open-source models from Hugging Face.

### Key Features
- Side-by-side model comparison
- Performance metrics
- Output quality analysis
- Model selection interface
- Cost and performance insights

### Implementation Steps

#### 1. Hugging Face Client
```python
# src/api/huggingface_client.py
import requests
import os
from dotenv import load_dotenv
from typing import Dict, Any

load_dotenv()

class HuggingFaceClient:
    def __init__(self):
        self.api_url = "https://api-inference.huggingface.co/models"
        self.api_key = os.getenv('HUGGINGFACE_API_KEY')  # Optional
    
    def generate_text(self, model_name: str, prompt: str, max_length: int = 100) -> Dict[str, Any]:
        """Generate text using Hugging Face model."""
        url = f"{self.api_url}/{model_name}"
        
        headers = {}
        if self.api_key:
            headers["Authorization"] = f"Bearer {self.api_key}"
        
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
                return {
                    'text': result[0].get('generated_text', ''),
                    'success': True,
                    'error': None
                }
            return {
                'text': '',
                'success': False,
                'error': 'No response generated'
            }
        except Exception as e:
            return {
                'text': '',
                'success': False,
                'error': str(e)
            }
```

#### 2. Comparison Service
```python
# src/services/comparison_service.py
from api.gemini_client import GeminiClient
from api.huggingface_client import HuggingFaceClient
import time
from typing import Dict, List, Any

class ComparisonService:
    def __init__(self):
        self.gemini_client = GeminiClient()
        self.hf_client = HuggingFaceClient()
    
    def compare_models(self, prompt: str, hf_models: List[str]) -> Dict[str, Any]:
        """Compare Gemini with Hugging Face models."""
        results = {
            'prompt': prompt,
            'gemini': {},
            'huggingface': {},
            'comparison': {}
        }
        
        # Test Gemini
        start_time = time.time()
        try:
            gemini_response = self.gemini_client.summarize_text(prompt, 200)
            gemini_time = time.time() - start_time
            
            results['gemini'] = {
                'text': gemini_response,
                'response_time': gemini_time,
                'success': True,
                'error': None
            }
        except Exception as e:
            results['gemini'] = {
                'text': '',
                'response_time': 0,
                'success': False,
                'error': str(e)
            }
        
        # Test Hugging Face models
        for model in hf_models:
            start_time = time.time()
            hf_response = self.hf_client.generate_text(model, prompt, 200)
            hf_time = time.time() - start_time
            
            results['huggingface'][model] = {
                'text': hf_response['text'],
                'response_time': hf_time,
                'success': hf_response['success'],
                'error': hf_response['error']
            }
        
        # Generate comparison
        results['comparison'] = self._generate_comparison(results)
        
        return results
    
    def _generate_comparison(self, results: Dict[str, Any]) -> Dict[str, Any]:
        """Generate comparison analysis."""
        comparison = {
            'fastest_model': None,
            'most_detailed': None,
            'cost_analysis': {},
            'quality_metrics': {}
        }
        
        # Find fastest model
        all_times = []
        if results['gemini']['success']:
            all_times.append(('Gemini', results['gemini']['response_time']))
        
        for model, data in results['huggingface'].items():
            if data['success']:
                all_times.append((model, data['response_time']))
        
        if all_times:
            comparison['fastest_model'] = min(all_times, key=lambda x: x[1])[0]
        
        # Cost analysis (simplified)
        comparison['cost_analysis'] = {
            'gemini': 'Free tier: 60 requests/minute',
            'huggingface': 'Free tier: 1000 requests/month'
        }
        
        return comparison
```

#### 3. Main Application
```python
# src/main.py
import streamlit as st
from services.comparison_service import ComparisonService
import os

def main():
    st.set_page_config(
        page_title="Model Comparison Tool",
        page_icon="⚖️",
        layout="wide"
    )
    
    st.title("⚖️ Gemini vs OSS Model Comparison")
    st.write("Compare outputs from Gemini API with open-source models from Hugging Face.")
    
    # Initialize service
    try:
        comparison_service = ComparisonService()
    except Exception as e:
        st.error(f"Configuration error: {e}")
        return
    
    # Input section
    st.header("📝 Input Prompt")
    
    prompt = st.text_area(
        "Enter your prompt:",
        height=150,
        placeholder="Enter a prompt to test with different models..."
    )
    
    # Model selection
    st.header("🤖 Select Models to Compare")
    
    col1, col2 = st.columns(2)
    
    with col1:
        st.subheader("Gemini API")
        use_gemini = st.checkbox("Include Gemini", value=True)
    
    with col2:
        st.subheader("Hugging Face Models")
        hf_models = st.multiselect(
            "Select Hugging Face models:",
            [
                "gpt2",
                "distilgpt2",
                "microsoft/DialoGPT-medium",
                "facebook/blenderbot-400M-distill"
            ],
            default=["gpt2", "distilgpt2"]
        )
    
    # Compare button
    if st.button("🚀 Compare Models", type="primary"):
        if not prompt.strip():
            st.error("Please provide a prompt.")
            return
        
        if not use_gemini and not hf_models:
            st.error("Please select at least one model to compare.")
            return
        
        with st.spinner("Comparing models..."):
            try:
                results = comparison_service.compare_models(prompt, hf_models)
                
                st.success("✅ Model comparison completed!")
                
                # Display results
                st.header("📊 Comparison Results")
                
                # Gemini results
                if use_gemini and results['gemini']['success']:
                    st.subheader("🤖 Gemini API")
                    col1, col2 = st.columns([3, 1])
                    
                    with col1:
                        st.write("**Output:**")
                        st.write(results['gemini']['text'])
                    
                    with col2:
                        st.metric(
                            "Response Time",
                            f"{results['gemini']['response_time']:.2f}s"
                        )
                
                # Hugging Face results
                if hf_models:
                    st.subheader("🤗 Hugging Face Models")
                    
                    for model, data in results['huggingface'].items():
                        if data['success']:
                            st.write(f"**{model}**")
                            col1, col2 = st.columns([3, 1])
                            
                            with col1:
                                st.write(data['text'])
                            
                            with col2:
                                st.metric(
                                    "Response Time",
                                    f"{data['response_time']:.2f}s"
                                )
                            
                            st.divider()
                
                # Comparison analysis
                st.subheader("📈 Analysis")
                
                if results['comparison']['fastest_model']:
                    st.write(f"**Fastest Model:** {results['comparison']['fastest_model']}")
                
                st.write("**Cost Analysis:**")
                for model, cost in results['comparison']['cost_analysis'].items():
                    st.write(f"• {model}: {cost}")
                
            except Exception as e:
                st.error(f"❌ Error comparing models: {e}")

if __name__ == "__main__":
    main()
```

## 🧪 Testing Strategy

### Unit Tests
```python
# tests/test_phase1.py
import pytest
from unittest.mock import patch, MagicMock

class TestPhase1Projects:
    def test_meeting_processor(self):
        """Test meeting processor functionality."""
        with patch.dict('os.environ', {'GEMINI_API_KEY': 'test_key'}):
            from services.meeting_processor import MeetingProcessor
            processor = MeetingProcessor()
            
            # Mock the model response
            with patch.object(processor.model, 'generate_content') as mock_generate:
                mock_response = MagicMock()
                mock_response.text = "Test summary"
                mock_generate.return_value = mock_response
                
                result = processor.process_meeting("Test transcript")
                assert 'summary' in result
    
    def test_comparison_service(self):
        """Test model comparison functionality."""
        with patch.dict('os.environ', {'GEMINI_API_KEY': 'test_key'}):
            from services.comparison_service import ComparisonService
            service = ComparisonService()
            
            # Mock responses
            with patch.object(service.gemini_client, 'summarize_text') as mock_gemini:
                mock_gemini.return_value = "Gemini response"
                
                with patch.object(service.hf_client, 'generate_text') as mock_hf:
                    mock_hf.return_value = {
                        'text': 'HF response',
                        'success': True,
                        'error': None
                    }
                    
                    result = service.compare_models("Test prompt", ["gpt2"])
                    assert 'gemini' in result
                    assert 'huggingface' in result
```

### Integration Tests
```python
# tests/test_integration.py
import pytest
import os

class TestIntegration:
    def test_gemini_api_integration(self):
        """Test actual Gemini API integration."""
        if not os.getenv('GEMINI_API_KEY'):
            pytest.skip("GEMINI_API_KEY not set")
        
        from api.gemini_client import GeminiClient
        client = GeminiClient()
        
        result = client.summarize_text("This is a test document.", 50)
        assert result is not None
        assert len(result) > 0
    
    def test_email_service_integration(self):
        """Test email service integration."""
        if not os.getenv('GMAIL_ADDRESS') or not os.getenv('GMAIL_APP_PASSWORD'):
            pytest.skip("Gmail credentials not set")
        
        from services.email_service import EmailService
        service = EmailService()
        
        # Test with a dummy email (don't actually send)
        # This would require a test email address
        pass
```

## 📊 Performance Metrics

### Key Performance Indicators
- **API Response Time** - Average response time for each model
- **Success Rate** - Percentage of successful API calls
- **User Engagement** - Time spent on application
- **Error Rate** - Percentage of failed operations

### Monitoring
```python
# monitoring.py
import time
import logging
from functools import wraps

logger = logging.getLogger(__name__)

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
            logger.error(f"Error in {func.__name__}: {e}")
            raise e
        finally:
            duration = time.time() - start_time
            logger.info(f"{func.__name__} took {duration:.2f}s, success: {success}")
        
        return result
    return wrapper
```

## 🚀 Deployment

### Streamlit Cloud Deployment
1. Push code to GitHub repository
2. Connect Streamlit Cloud to repository
3. Configure environment variables
4. Deploy application

### Environment Variables
```bash
# Required for all Phase 1 projects
GEMINI_API_KEY=your_gemini_api_key_here

# Required for Project 1
GMAIL_ADDRESS=your_email@gmail.com
GMAIL_APP_PASSWORD=your_app_password_here

# Optional for Project 3
HUGGINGFACE_API_KEY=your_hf_api_key_here
```

## 📚 Next Steps

After completing Phase 1, you'll be ready to move on to Phase 2, which focuses on:
- Multi-step workflows
- Database integration
- Advanced error handling
- Production-ready applications

### Phase 2 Preview
- **Project 4:** Customer Email Classifier → Auto-Responder
- **Project 5:** Job Description → Resume Matcher
- **Project 6:** Error Handling + Logging Layer

## 🔗 Additional Resources

- [Streamlit Documentation](https://docs.streamlit.io/)
- [Gemini API Documentation](https://ai.google.dev/docs)
- [Hugging Face API Documentation](https://huggingface.co/docs/api-inference)
- [Gmail SMTP Documentation](https://developers.google.com/gmail/api)
- [Python Testing Guide](https://docs.python.org/3/library/unittest.html)
