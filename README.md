# 🐍 Deploying a Python Flask Web App on EC2 (Production Setup)

---

## ✅ Prerequisites

- An AWS EC2 instance (Amazon Linux)
- Python 3 installed, if not check and install (`python3 --version`)
```bash
sudo yum install python3-pip -y
```
- Security Group with HTTP (port 80) allowed

---

## 📦 Step 1: Install Required Packages

```bash
sudo yum update -y
sudo yum install python3-pip nginx -y
pip3 install flask gunicorn
```
## Step 2: Create Your Flask App (app.py)
```bash
nano app.py
```
Paste this code:
```py
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello():
    return "Hello from Flask on EC2 with Gunicorn and Nginx!"
```
Save and exit: `Ctrl + O`, `Enter, Ctrl + X`.

## Step 3: Start Flask App Using Gunicorn
Run this from the folder where app.py exists:
```bash
gunicorn --bind 127.0.0.1:8000 app:app
```
To run in background:
```bash
gunicorn --bind 127.0.0.1:8000 app:app &
```
## Step 4: Configure Nginx as Reverse Proxy
Create a config file:
```bash
sudo nano /etc/nginx/conf.d/flaskapp.conf
```
Paste:
```nginxserver {
    listen 80;
    server_name YOUR_EC2_PUBLIC_IP;

    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```
> Replace YOUR_EC2_PUBLIC_IP with your instance’s public IP.
Restart Nginx:
```bash
sudo systemctl restart nginx
sudo systemctl enable nginx
```
##  Step 6: Access the App:
Open browser
```bash
http://<your-ec2-public-ip>
```
You should see:
```text
Hello from Flask on EC2 with Gunicorn and Nginx!
```
## Troubleshooting the most common error "502 Bad Gateway":
-  Make sure Gunicorn is running: ps aux | grep gunicorn
-  Ensure Nginx config has correct proxy_pass
-  Restart Nginx after edits
-  Ensure you're in the correct directory when running Gunicorn
