# 🛡️ Secure Node.js & Next.js Deployment on VPS

This README provides an **A–Z, production-ready, security-first** guide to deploy **Node.js (Backend)** and **Next.js (Frontend)** on a **fresh VPS** using **NGINX, PM2, Firewall, Rate Limiting, and SSL**.

> ✅ Designed to **prevent crypto-miner & bot attacks**
> ✅ Node ports are **never exposed publicly**
> ✅ Suitable for GitHub documentation

---

## 📌 Prerequisites

* Fresh VPS (Ubuntu **22.04 LTS** recommended)
* Root SSH access
* Domain name (e.g. `example.com`, `api.example.com`)

---

## 🔐 STEP 1: SSH into VPS

```bash
ssh root@YOUR_SERVER_IP
# Enter root password
```

---

## 🔄 STEP 2: Update System & Install Essentials

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install ufw git curl build-essential -y
```

---

## 🔥 STEP 3: Firewall (VERY IMPORTANT)

```bash
sudo ufw reset
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 22
sudo ufw allow 80
sudo ufw allow 443
sudo ufw enable
ufw status
```

✅ Only **22, 80, 443** must be allowed
❌ **Never allow Node ports (3000, 5000, etc.)**

---

## 🛑 STEP 4: Install Fail2Ban (SSH Protection)

```bash
sudo apt install fail2ban -y
sudo systemctl enable fail2ban
sudo systemctl start fail2ban
```

---

## 🟢 STEP 5: Install NVM, Node.js & PM2

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash
source ~/.bashrc

nvm install --lts
node -v
npm -v

npm install -g pm2
pm2 -v
```

---

## 🔑 STEP 6: GitHub SSH Setup

```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
eval "$(ssh-agent -s)"
cat ~/.ssh/id_rsa.pub
```

➡️ Add the key to **GitHub → Settings → SSH Keys**

```bash
ssh -T git@github.com
```

---

## 📂 STEP 7: Project Directory Setup

```bash
sudo mkdir -p /var/www
sudo chown -R $USER:$USER /var/www
cd /var/www
```

---

## ⚙️ STEP 8: Backend Setup (Node.js)

```bash
git clone <BACKEND_REPO_URL>
cd backend
npm install
npx prisma generate
npm run build
```

### ▶️ Run Backend (localhost only)

```bash
PORT=5000 HOST=127.0.0.1 pm2 start npm --name backend -- start --max-memory-restart 500M
pm2 save
```

---

## 🎨 STEP 9: Frontend Setup (Next.js)

```bash
cd /var/www
git clone <FRONTEND_REPO_URL>
cd frontend
npm install
npm run build
```

### ▶️ Run Frontend (localhost only)

```bash
pm2 start npm --name frontend -- start --max-memory-restart 500M
pm2 save
```

---

## 🌐 STEP 10: Install NGINX

```bash
sudo apt install nginx -y
sudo systemctl enable nginx
sudo systemctl start nginx
```

---

## 🚦 STEP 11: Enable Rate Limiting (Global)

Edit NGINX config:

```bash
sudo nano /etc/nginx/nginx.conf
```

Add inside `http {}` block:

```nginx
limit_req_zone $binary_remote_addr zone=global:10m rate=10r/s;
```

---

## 🔁 STEP 12: NGINX Reverse Proxy Configuration

### Frontend

`/etc/nginx/sites-available/frontend`

```nginx
server {
    listen 80;
    server_name example.com www.example.com;

    location / {
        limit_req zone=global burst=20 nodelay;
        proxy_pass http://127.0.0.1:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
    }
}
```

### Backend

`/etc/nginx/sites-available/backend`

```nginx
server {
    listen 80;
    server_name api.example.com;

    location / {
        limit_req zone=global burst=5 nodelay;
        proxy_pass http://127.0.0.1:5000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

Enable configs:

```bash
sudo ln -s /etc/nginx/sites-available/frontend /etc/nginx/sites-enabled
sudo ln -s /etc/nginx/sites-available/backend /etc/nginx/sites-enabled
sudo nginx -t
sudo systemctl reload nginx
```

---

## 🔐 STEP 13: SSL (Certbot)

```bash
sudo apt install certbot python3-certbot-nginx -y
sudo certbot --nginx -d example.com -d www.example.com
sudo certbot --nginx -d api.example.com
```

---

## 🔍 STEP 14: Security Verification

```bash
ss -tulpn
```

Expected result:

```
0.0.0.0:22
0.0.0.0:80
0.0.0.0:443
127.0.0.1:3000
127.0.0.1:5000
```

❌ If any Node port is on `0.0.0.0` → **INSECURE**

---

## 🔁 STEP 15: Rotate Secrets (IMPORTANT)

* Database user & password
* JWT secret
* API keys
* SMTP / third-party tokens

---

## 🔄 STEP 16: PM2 Auto Restart

```bash
pm2 startup
pm2 save
pm2 restart all --cron "0 4 * * *"
```

---

## ☁️ OPTIONAL: Cloudflare (Highly Recommended)

* Enable proxy (orange cloud)
* Bot Fight Mode ON
* Managed WAF ON
* Rate limiting rules

---

## ✅ FINAL SECURITY RULES

* 🚫 Never expose Node ports publicly
* ✅ Only ports 22, 80, 443 are public
* 🧱 All traffic goes through NGINX
* 🚦 Rate limiting always enabled
* 🔒 Rotate secrets after any breach

---

🎉 **Your VPS is now production-ready & secure.**
