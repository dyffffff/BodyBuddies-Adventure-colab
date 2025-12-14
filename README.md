# BodyBuddies Adventures - Interactive Physiology Story Generator

An educational application that generates scientifically accurate stories about human physiological processes using RAG (Retrieval-Augmented Generation) and Google's Gemini API. The system creates engaging narratives with anime-style illustrations based on user-described activities.

## Step-by-Step Tutorial

### Prerequisites - What You Need Before Starting

Before you begin, make sure you have:

1. **Google Account** - Required for Google Colab access
2. **Google Gemini API Key** - For AI text and image generation
3. **Ngrok Account** - For creating public tunnels (free tier works)
4. **Data File** - The ontology CSV file for physiological knowledge base

### Clear Installation Instructions

#### Step 1: Open in Google Colab

1. [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/dyffffff/BodyBuddies-Adventure-colab/blob/main/bodybuddies.ipynb)

**Click the badge above to open the notebook directly in Google Colab!**
3. Ensure you're logged into your Google account

#### Step 2: Enable GPU Runtime

1. In Google Colab, navigate to `Runtime` → `Change runtime type`
2. Select `T4 GPU` under Hardware accelerator
3. Click `Save`

#### Step 3: Install Required Packages

Run the first cell in the notebook to install all dependencies:

```bash
!pip install --upgrade pip

# Streamlit & Ngrok - Web interface and tunneling
!pip install streamlit
!pip install pyngrok

# Google Gemini API - AI generation
!pip install google-generativeai

# NLP / RAG Components - Knowledge retrieval
!pip install sentence-transformers
!pip install langchain
!pip install langchain-community
!pip install chromadb

# Image Processing - Visual content handling
!pip install pillow
!pip install wordfreq

# Assistance Libraries - Data manipulation
!pip install pandas numpy
!pip install requests

# Optional: Streamlit UI enhancements
!pip install streamlit-extras
```

#### Step 4: Install Font Support (Optional but Recommended)

For better text rendering in generated images, especially for multilingual content:

```bash
!apt-get install -y fonts-noto-cjk fonts-noto-color-emoji
```

### Environment Setup Guide

#### Step 1: Configure Google Gemini API Key

**Method A: Using Colab Secrets (Recommended)**

1. Go to https://makersuite.google.com/app/apikey
2. Create or copy your API key
3. In Google Colab, click the key icon (🔑) in the left sidebar
4. Add a new secret named `GEMINI_API_KEY`
5. Paste your API key as the value
6. Enable "Notebook access" for this secret

The notebook will automatically load it using:
```python
from google.colab import userdata
os.environ["GEMINI_API_KEY"] = userdata.get("GEMINI_API_KEY")
```

**Method B: Direct Environment Variable**

If Method A doesn't work, modify the code to set the API key directly:
```python
os.environ["GEMINI_API_KEY"] = "YOUR_API_KEY_HERE"  # Replace with your actual key
```

#### Step 2: Configure Ngrok Authentication

**CRITICAL: You MUST set up your own Ngrok token, or the application will fail!**

1. Create a free account at https://ngrok.com
2. Go to https://dashboard.ngrok.com/auth
3. Copy your authentication token
4. In Google Colab, add another secret named `NGROK_AUTHTOKEN`
5. Paste your Ngrok token as the value
6. Enable "Notebook access" for this secret

The notebook loads it using:
```python
NGROK_TOKEN = os.getenv("NGROK_AUTHTOKEN")
if NGROK_TOKEN is None:
    raise ValueError("❌ NGROK_AUTHTOKEN not found. Please set it in Colab Secrets!")
```

**Alternative Method:**

If secrets don't work, modify the code directly:
```python
os.environ["NGROK_AUTHTOKEN"] = "YOUR_NGROK_TOKEN_HERE"  # Replace with your token
```

#### Step 3: Upload the Ontology Data File

**IMPORTANT: The application requires a CSV file containing physiological knowledge.**

1. Upload your `ontology_multi_organ_steps.csv` file to Colab
2. Place it in `/content/data/` directory:
```bash
!mkdir -p /content/data
# Then upload your file to this directory
```

3. Verify the file location:
```bash
!ls /content/data
```

The file should contain columns:
- `activity` - Physical or lifestyle activities
- `organ` - Affected body organs/systems
- `mechanism` - Physiological mechanisms
- `step` - Process sequence number

### Usage Examples and Demonstrations

#### Running the Application

1. **Execute all setup cells in order:**
   - Package installation (first cell)
   - Font installation (optional)
   - API key configuration
   - Data verification
   - Main application code (the cell with `%%writefile app.py`)

2. **Verify API keys are set:**
```python
print("Gemini API Key:", "Set" if os.environ.get("GEMINI_API_KEY") else "Not Set")
print("Ngrok Token:", "Set" if os.environ.get("NGROK_AUTHTOKEN") else "Not Set")
```

3. **Launch the Streamlit application:**
```python
!streamlit run app.py --server.port 8501 &>/content/logs.txt &
```

4. **Create the public tunnel:**
```python
from pyngrok import ngrok
public_url = ngrok.connect(8501)
print(public_url)
```

5. **Access your application:**
   - Click the generated URL (format: `https://xxxxx.ngrok-free.app`)
   - You may see an Ngrok warning page - click "Visit Site"

#### Using the Application Interface

Once the application is running:

1. **Enter an activity description** in the text area:
   - Example: "I just finished a marathon and drank lots of water"
   - Example: "Staying up all night studying and drinking coffee"
   - Example: "Eating spicy food and then exercising"

2. **Configure generation settings:**
   - **Number of illustrations**: Choose 1-4 scenes
   - **OCR correction**: Enable for better text accuracy in images

3. **Click "✨ Generate Story & Illustrations"**
   - Wait 30-60 seconds for complete generation
   - View the 4-scene physiological narrative
   - See anime-style illustrations for each scene

#### Example Input and Expected Output

**Input**: "Running in the morning and drinking coffee"

**Expected Output Structure**:
```
Activity 1: Running
- Organ: Heart → Mechanism: Increased cardiac output
- Organ: Lungs → Mechanism: Enhanced oxygen uptake
- Organ: Muscles → Mechanism: ATP production increase

Activity 2: Drinking coffee
- Organ: Brain → Mechanism: Adenosine receptor blocking
- Organ: Adrenal glands → Mechanism: Adrenaline release
- Organ: Heart → Mechanism: Increased heart rate
```

The system then generates a narrative connecting these mechanisms into an educational story with accompanying illustrations.

### Troubleshooting Guide

#### Common Errors and Solutions

1. **"GEMINI_API_KEY is not set"**
   - **Cause**: API key not configured properly
   - **Solution**: 
     - Check Colab Secrets configuration
     - Verify the secret name is exactly `GEMINI_API_KEY`
     - Try setting directly in code as fallback

2. **"NGROK_AUTHTOKEN not found"**
   - **Cause**: Ngrok token not in Colab Secrets
   - **Solution**:
     - Add `NGROK_AUTHTOKEN` to Colab Secrets
     - Ensure "Notebook access" is enabled
     - Use direct environment variable as fallback

3. **"FileNotFoundError: ontology_multi_organ_steps.csv"**
   - **Cause**: Data file not uploaded or wrong path
   - **Solution**:
     - Upload the CSV file to `/content/data/`
     - Verify with `!ls /content/data`
     - Check file name matches exactly

4. **"Connection refused" when accessing URL**
   - **Cause**: Streamlit not running or tunnel failed
   - **Solution**:
     - Check logs: `!cat /content/logs.txt`
     - Restart runtime and run all cells again
     - Kill existing ngrok: `ngrok.kill()` before reconnecting

5. **"CUDA out of memory"**
   - **Cause**: GPU memory exhausted
   - **Solution**:
     - Restart runtime (`Runtime` → `Restart runtime`)
     - Reduce number of images to generate
     - Clear GPU memory between runs

6. **"Rate limit exceeded" from Gemini API**
   - **Cause**: Too many API calls
   - **Solution**:
     - Wait a few minutes before retrying
     - Reduce number of generations
     - Check API quota at Google Cloud Console

#### Quick Debugging Checklist

- [ ] GPU runtime enabled?
- [ ] All packages installed successfully?
- [ ] GEMINI_API_KEY configured in Colab Secrets?
- [ ] NGROK_AUTHTOKEN configured in Colab Secrets?
- [ ] Data CSV file uploaded to `/content/data/`?
- [ ] App.py file created with `%%writefile`?
- [ ] Streamlit running (check logs)?
- [ ] Waited 5-10 seconds before creating tunnel?

#### Checking Application Logs

To debug issues, check various logs:

```bash
# Check Streamlit logs
!cat /content/logs.txt

# Check if app.py was created
!ls -la app.py

# Verify data file
!head /content/data/ontology_multi_organ_steps.csv

# Check running processes
!ps aux | grep streamlit
```

### Tips for Best Results

1. **Activity Descriptions**:
   - Be specific about physical activities
   - Include multiple related activities for richer stories
   - Works in both English and Chinese

2. **Generation Settings**:
   - Start with 2 images for faster testing
   - Enable OCR for better text quality
   - Allow 30-60 seconds for full generation

3. **API Management**:
   - Monitor your Gemini API usage
   - Use Colab Secrets for security
   - Don't share your tokens publicly

4. **Performance Optimization**:
   - Close other Colab notebooks to free resources
   - Restart runtime if experiencing slowdowns
   - Use T4 GPU for best performance

### Data File Requirements

The `ontology_multi_organ_steps.csv` must have this structure:

```csv
activity,organ,mechanism,step
running,heart,increased cardiac output,1
running,lungs,enhanced oxygen uptake,2
running,muscles,ATP production,3
...
```

Each row represents a physiological step in response to an activity.

### Support Resources

- **Ngrok Documentation**: https://ngrok.com/docs
- **Google Gemini API**: https://ai.google.dev/
- **Streamlit Documentation**: https://docs.streamlit.io/
- **Google Colab Help**: https://colab.research.google.com/notebooks/FAQ.ipynb
- **LangChain Documentation**: https://python.langchain.com/

---

**Remember**: The two most common setup errors are:
1. Not setting up Ngrok authentication token in Colab Secrets
2. Not uploading the ontology CSV data file

Double-check these first if you encounter any issues!
