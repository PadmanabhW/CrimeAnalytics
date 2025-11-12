# 🚀 Deployment Guide

## Option 1: Local Deployment (Recommended for Development)

### Step 1: Setup Environment
```bash
# Navigate to project directory
cd path/to/your/project

# Create virtual environment (optional but recommended)
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
```

### Step 2: Verify Data Files
Ensure both CSV files are present:
- `districtwise-missing-persons-2017-2022.csv`
- `districtwise-ipc-crime-by-juveniles-2017-onwards.csv`

### Step 3: Run Application
```bash
streamlit run streamlit_app.py
```

### Step 4: Access Dashboard
Open browser and navigate to: `http://localhost:8501`

---

## Option 2: Google Colab Deployment

### Step 1: Upload to Colab
1. Upload all Python files and CSV files to Colab
2. Or mount Google Drive if files are stored there

### Step 2: Install Dependencies
```python
!pip install streamlit plotly xgboost lightgbm
```

### Step 3: Run with Localtunnel
```python
# Install pyngrok for tunneling
!pip install pyngrok

# Import necessary libraries
from pyngrok import ngrok
import os

# Set auth token (get free token from ngrok.com)
ngrok.set_auth_token("YOUR_NGROK_TOKEN")

# Run streamlit in background
!streamlit run streamlit_app.py &>/dev/null&

# Create tunnel
public_url = ngrok.connect(8501)
print(f"Public URL: {public_url}")
```

### Alternative: Use Localtunnel
```bash
!npm install -g localtunnel
!streamlit run streamlit_app.py &>/dev/null&
!npx localtunnel --port 8501
```

---

## Option 3: Streamlit Cloud (Free & Easy)

### Step 1: Prepare Repository
1. Create a GitHub repository
2. Upload all project files including:
   - `streamlit_app.py`
   - `data_preprocessing.py`
   - `ml_models.py`
   - `deep_learning.py`
   - `requirements.txt`
   - Both CSV files

### Step 2: Deploy on Streamlit Cloud
1. Go to [share.streamlit.io](https://share.streamlit.io)
2. Sign in with GitHub
3. Click "New app"
4. Select your repository
5. Set main file: `streamlit_app.py`
6. Click "Deploy"

### Step 3: Access Your App
You'll get a public URL like: `https://your-app.streamlit.app`

---

## Option 4: Heroku Deployment

### Prerequisites
- Heroku account
- Heroku CLI installed

### Step 1: Create Additional Files

**Procfile**:
```
web: streamlit run streamlit_app.py --server.port $PORT --server.address 0.0.0.0
```

**setup.sh**:
```bash
mkdir -p ~/.streamlit/

echo "\
[server]\n\
headless = true\n\
port = $PORT\n\
enableCORS = false\n\
\n\
" > ~/.streamlit/config.toml
```

### Step 2: Deploy
```bash
# Login to Heroku
heroku login

# Create new app
heroku create your-crime-analytics-app

# Add buildpack
heroku buildpacks:set heroku/python

# Deploy
git add .
git commit -m "Deploy to Heroku"
git push heroku main

# Open app
heroku open
```

---

## Option 5: Docker Deployment

### Step 1: Create Dockerfile
```dockerfile
FROM python:3.9-slim

WORKDIR /app

# Copy files
COPY requirements.txt .
COPY *.py .
COPY *.csv .

# Install dependencies
RUN pip install --no-cache-dir -r requirements.txt

# Expose port
EXPOSE 8501

# Run app
CMD ["streamlit", "run", "streamlit_app.py", "--server.port=8501", "--server.address=0.0.0.0"]
```

### Step 2: Build and Run
```bash
# Build image
docker build -t crime-analytics .

# Run container
docker run -p 8501:8501 crime-analytics
```

### Step 3: Access
Navigate to: `http://localhost:8501`

---

## Option 6: AWS EC2 Deployment

### Step 1: Launch EC2 Instance
1. Choose Ubuntu Server 20.04 LTS
2. Instance type: t2.medium or larger
3. Configure security group to allow port 8501

### Step 2: Connect and Setup
```bash
# Connect to instance
ssh -i your-key.pem ubuntu@your-instance-ip

# Update system
sudo apt update && sudo apt upgrade -y

# Install Python and pip
sudo apt install python3-pip -y

# Clone your repository or upload files
git clone your-repo-url
cd your-repo

# Install dependencies
pip3 install -r requirements.txt

# Run with nohup
nohup streamlit run streamlit_app.py --server.port 8501 --server.address 0.0.0.0 &
```

### Step 3: Access
Navigate to: `http://your-instance-ip:8501`

---

## Option 7: Azure Web Apps

### Step 1: Create Web App
```bash
# Login to Azure
az login

# Create resource group
az group create --name crime-analytics-rg --location eastus

# Create app service plan
az appservice plan create --name crime-analytics-plan --resource-group crime-analytics-rg --sku B1 --is-linux

# Create web app
az webapp create --resource-group crime-analytics-rg --plan crime-analytics-plan --name your-crime-analytics --runtime "PYTHON|3.9"

# Deploy code
az webapp up --name your-crime-analytics --resource-group crime-analytics-rg
```

---

## Performance Optimization Tips

### 1. Data Caching
Already implemented with `@st.cache_data` decorator

### 2. Reduce Model Training Time
- Use smaller subsets for demonstration
- Pre-train models and save with pickle
- Load pre-trained models on startup

### 3. Optimize Visualizations
- Limit number of data points in plots
- Use sampling for large datasets
- Lazy load heavy components

### 4. Memory Management
```python
# Add to streamlit_app.py
import gc

# After heavy operations
gc.collect()
```

---

## Monitoring & Maintenance

### Check Application Logs
```bash
# Streamlit Cloud: Available in dashboard
# Heroku: heroku logs --tail
# Docker: docker logs container-id
# EC2: cat nohup.out
```

### Update Application
```bash
# Pull latest changes
git pull origin main

# Restart service (method depends on deployment)
# Streamlit Cloud: Auto-deploys on git push
# Heroku: git push heroku main
# Docker: Rebuild and restart container
# EC2: Kill process and restart
```

---

## Troubleshooting

### Issue: Port Already in Use
```bash
# Find process using port 8501
lsof -i :8501
# Or on Windows: netstat -ano | findstr :8501

# Kill the process
kill -9 PID
```

### Issue: Out of Memory
- Reduce batch size in deep learning
- Use sampling for large datasets
- Increase instance size (cloud deployments)

### Issue: Slow Loading
- Implement progressive data loading
- Pre-process and cache results
- Optimize database queries

---

## Security Considerations

1. **Environment Variables**: Store sensitive data in `.env` file
2. **HTTPS**: Enable SSL/TLS for production
3. **Authentication**: Add login if needed (Streamlit-authenticator)
4. **Rate Limiting**: Implement to prevent abuse
5. **Data Privacy**: Ensure compliance with data protection regulations

---

## Cost Estimation

### Free Options
- **Streamlit Cloud**: Free for public apps
- **Heroku Free Tier**: Limited hours/month
- **Google Colab**: Limited GPU time

### Paid Options
- **AWS EC2**: ~$10-30/month (t2.small to t2.medium)
- **Heroku Hobby**: $7/month
- **Azure**: ~$15-50/month depending on tier
- **DigitalOcean**: $5-20/month

---

## Backup & Version Control

### Git Best Practices
```bash
# Create .gitignore
echo "*.pyc
__pycache__/
venv/
.env
*.pkl
.streamlit/secrets.toml" > .gitignore

# Commit regularly
git add .
git commit -m "Descriptive message"
git push origin main

# Create tags for releases
git tag -a v1.0 -m "First release"
git push origin v1.0
```

---

## Need Help?

- **Streamlit Docs**: https://docs.streamlit.io
- **Deployment Guide**: https://docs.streamlit.io/streamlit-cloud
- **Community Forum**: https://discuss.streamlit.io

---

**Recommendation**: For quick testing and presentation, use **Streamlit Cloud** (Option 3). For production with full control, use **AWS EC2** or **Docker**.
