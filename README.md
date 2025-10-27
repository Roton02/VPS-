# VPS SETUP

### STEP 1
<pre><code id="example-code">ssh root@your_ip_adress</code></pre>
Then enter your password

### STEP 2
Updated backdated packges
<pre><code id="example-code">sudo apt update</code></pre>
<pre><code id="example-code">sudo apt upgrade</code></pre>

### STEP 3
Enabling and Configuring UFW (Uncomplicated Firewall)

<pre><code id="example-code">sudo apt install ufw -y (if debian first install ufw)</code></pre>
<pre><code id="example-code">sudo ufw enable</code></pre>
<pre><code id="example-code">sudo ufw allow 22</code></pre>

Check allow port status 

<pre><code id="example-code">sudo ufw status</code></pre>

### STEP 3
Install NVM & Node.js

<pre><code id="example-code">curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash</code></pre>
<pre><code id="example-code">nvm -v</code></pre>
<pre><code id="example-code">nvm install --lts</code></pre>
<pre><code id="example-code">nvm install 18.17.1 (for specific version)</code></pre>
<pre><code id="example-code">node -v</code></pre>
<pre><code id="example-code">node -v</code></pre>
<pre><code id="example-code">npm -v</code></pre>
Note: If version not showing please close the terminal and try again

## STEP 4
git hub connection 
<pre><code id="example-code">ls -al ~/.ssh</code></pre>
<pre><code id="example-code">ssh-keygen -t rsa -b 4096 -C "github@smtech24.com"</code></pre>
<pre><code id="example-code">eval "$(ssh-agent -s)"</code></pre>
<pre><code id="example-code">cat ~/.ssh/id_rsa.pub</code></pre>
Then copy the ssh key and pas it on your SSH and GPG kesy on your git hub clik new ssh key and write project name and past the key

<pre><code id="example-code">ssh -T git@github.com</code></pre>

### STEP 5
Setup nginx 
<pre>
  <code id="example-code">
    sudo ufw allow 80/tcp
    sudo ufw allow 3000
    sudo ufw allow 443/tcp
    sudo ufw reload
    sudo apt install nginx -y
    sudo systemctl status nginx
    sudo systemctl start nginx
    sudo systemctl enable nginx
    sudo ufw allow 'Nginx Full'
    sudo ufw enable
  </code>
</pre>

### STEP 6
Configure nginx 
<pre>
  <code id="example-code">
    sudo nano /etc/nginx/sites-available/frontend-server

    #this is for next js
    server {
    listen 80;
    server_name yourdomain.com www.yourdomain.com;
    location / {
        proxy_pass http://localhost:3000; # Next.js runs on port 3000
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
        }
    }
    
    sudo ln -s /etc/nginx/sites-available/frontend-server /etc/nginx/sites-enabled
    sudo nginx -t
    sudo systemctl reload nginx

    #this is backend server setup
    sudo nano /etc/nginx/sites-available/backend-server
    server{
        server_name test.com;

        location / {
            proxy_pass http://localhost:5002;
            proxy_http_version 1.1;
    
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "upgrade";
    
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
        client_max_body_size 3000M;
    }
    
    sudo ln -s /etc/nginx/sites-available/backend-server /etc/nginx/sites-enabled
    sudo nginx -t
    sudo systemctl reload nginx
  </code>
</pre>

## ⚙️ IF YOU ARE USING UBUNTU ON AWS – READ THIS FIRST!

When you try to clone a private GitHub repository on an **AWS Ubuntu VPS**, you might face an authentication or permission error like this:

```bash
Cloning into 'test-nest-ws-cicd-dcker-postgree-backend'...
git@github.com: Permission denied (publickey).
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists. 
````
🧩 Reason:
By default, the /var/www directory is owned by root, so your ubuntu user doesn’t have permission to write or clone repositories there.

✅ Solution:
Run the following command to fix directory ownership and allow your ubuntu user to access it:

```bash
Copy code
sudo chown -R ubuntu:ubuntu /var/www
```
This command changes the ownership of /var/www and gives your user permission to manage files inside it.
Now you can safely clone your repository:


### STEP 7
Install Project & Setup
<pre>
  <code id="example-code">
    git clone <git repository using ssh>
    npm install
    npm run build
    npm run start
    npm install -g pm2
    pm2 --version
      pm2 start npm --name "project-frontend" -- start (for next js frontend)
      pm2 start dist/server.js --name project-backend (for backend)
    pm2 list
    pm2 startup
    pm2 save
    pm2 restart all (restart)
      pm2 stop nextjs-app (stop server)
      pm2 delete nextjs-app (delete server)
  </code>
</pre>

### STEP 8
Install SSL
<pre>
  <code id="example-code">
    sudo apt install certbot python3-certbot-nginx -y
    sudo certbot --nginx -d yourdomain.com -d www.yourdomain.com
  </code>
</pre>

INSTALL SSL FROM HOSTINGER
<pre>
For APT-based distributions (such as Debian or Ubuntu), run the following:
<code id="example-code">
sudo apt update
sudo apt install python3 python3-venv libaugeas0
</code>
</pre>

<pre>
Once it's ready, run the following to set up a Python virtual environment:
<code id="example-code">
sudo python3 -m venv /opt/certbot/
sudo /opt/certbot/bin/pip install --upgrade pip
</code>
</pre>

<pre>
To install Certbot, this for NGINX:
<code id="example-code">
sudo /opt/certbot/bin/pip install certbot certbot-nginx
</code>
</pre>

<pre>
Next, create a symbolic link so that Certbot can be executed from any path:
<code id="example-code">
sudo ln -s /opt/certbot/bin/certbot /usr/bin/certbot
</code>
</pre>

<pre>
Install and activate SSL for your websites
<code id="example-code">
sudo certbot --nginx
</code>
</pre>


### STEP 9
Install Mongodb with Prisma ORM (if Ubuntu version 22.04 needs Mongodb version 8.0 and if Ubuntu version 22.00 needs Mongodb version 6.0)
  
<pre>
  ##for namecheap
  <code>
    sudo systemctl stop mongod
    sudo apt-get purge mongodb-org*
    sudo rm -r /var/log/mongodb
    sudo rm -r /var/lib/mongodb
  </code>
  <code>
    wget -qO - https://www.mongodb.org/static/pgp/server-4.4.asc | sudo apt-key add -
    echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/4.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-4.4.list
    sudo apt-get update
    sudo apt-get install mongodb-org=4.4.8 mongodb-org-server=4.4.8 mongodb-org-shell=4.4.8 mongodb-org-mongos=4.4.8 mongodb-org-tools=4.4.8
  </code>
  <code>
    sudo systemctl start mongod
    sudo systemctl enable mongod
    sudo systemctl status mongod
  </code>

  
  ##for other  vps provider
  <code id="example-code">
    sudo apt-get install gnupg curl
  </code>
  <code id="example-code">
    curl -fsSL https://www.mongodb.org/static/pgp/server-8.0.asc | \
   sudo gpg -o /usr/share/keyrings/mongodb-server-8.0.gpg \
   --dearmor
  </code>
  <code id="example-code">
    echo "deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb-server-8.0.gpg ] https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/8.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-8.0.list
    if not work this use this command
    echo "deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb-server-8.0.gpg ] https://repo.mongodb.org/apt/ubuntu noble/mongodb-org/8.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-8.0.list
  </code>

  <code id="example-code">
    sudo apt-get update
    sudo apt-get install -y mongodb-org
    sudo systemctl start mongod
    sudo systemctl daemon-reload
    sudo systemctl status mongod
    sudo systemctl restart mongod
    mongosh

    ##prisma replica setup
    sudo nano /etc/mongod.conf

    replication:
  replSetName: "rs0"

    sudo systemctl restart mongod (restart after replicate set config)
    mongosh (enter mongodb)
    rs.initiate() (replica set initiate)
    rs.status() (check status)
    ctrl + z (exit from mongosh)
    sudo systemctl status mongod (ensure mongodb is running)
    sudo ufw allow 27017 (allow 27017 port for mongodb localhost port)
    sudo ufw reload
    DATABASE_URL="mongodb://127.0.0.1:27017/lunatix?replicaSet=rs0"
  </code>
</pre>
