# Full-Stack React, Node.js, Express, and MySQL Deployment on VPS (Hostinger)

This guide will walk you through deploying a full-stack React, Node.js, Express, and MySQL application to a VPS hosted by Hostinger. 

## Table of Contents

1. [Step 1: SSH into Your VPS](#step-1-ssh-into-your-vps)
2. [Step 2: Install Required Packages](#step-2-install-required-packages)
3. [Step 3: Set Up SSH Key for Git Access](#step-3-set-up-ssh-key-for-git-access)
4. [Step 4: Clone Your Repository](#step-4-clone-your-repository)
5. [Step 5: Install Project Dependencies](#step-5-install-project-dependencies)
6. [Step 6: Configure the Environment Variables](#step-6-configure-the-environment-variables)
7. [Step 7: Run the Backend and Frontend](#step-7-run-the-backend-and-frontend)
8. [Step 8: Set Up Nginx for Reverse Proxy (Optional)](#step-8-set-up-nginx-for-reverse-proxy-optional)
9. [Step 9: Set Up PM2 for Process Management](#step-9-set-up-pm2-for-process-management)
10. [Step 10: Automate Deployment with Git](#step-10-automate-deployment-with-git)
11. [Step 11: Test Your Application](#step-11-test-your-application)

---

## Step 1: SSH into Your VPS

First, access your VPS via SSH. Open your terminal and run:

```bash
ssh username@your-server-ip
Replace username with your VPS username (often root) and your-server-ip with the IP address of your VPS.

Step 2: Install Required Packages
Once logged into your VPS, run the following commands to install the necessary tools:

Update your system
bash
Copy code
sudo apt update && sudo apt upgrade -y
Install Node.js
To install Node.js via the NodeSource repository for the latest stable version:

bash
Copy code
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt-get install -y nodejs
Install Git
bash
Copy code
sudo apt install -y git
Install MySQL
To install MySQL:

bash
Copy code
sudo apt install mysql-server
sudo systemctl start mysql
sudo systemctl enable mysql
Secure MySQL Installation
bash
Copy code
sudo mysql_secure_installation
Create MySQL Database and User
bash
Copy code
mysql -u root -p
CREATE DATABASE your_db_name;
CREATE USER 'your_db_user'@'localhost' IDENTIFIED BY 'your_db_password';
GRANT ALL PRIVILEGES ON your_db_name.* TO 'your_db_user'@'localhost';
FLUSH PRIVILEGES;
EXIT;
Step 3: Set Up SSH Key for Git Access
If your Git repository is private, you will need to set up SSH keys for authentication.

Generate an SSH Key
bash
Copy code
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
Copy the SSH Public Key
bash
Copy code
cat ~/.ssh/id_rsa.pub
Copy the output and add it to the SSH keys section of your Git repository provider (GitHub, GitLab, etc.).

Test SSH Connection
bash
Copy code
ssh -T git@github.com  # for GitHub, or git@gitlab.com for GitLab
Step 4: Clone Your Repository
Now, clone your private Git repository to your server:

bash
Copy code
git clone git@github.com:yourusername/your-repository.git /var/www/your-project
cd /var/www/your-project
Step 5: Install Project Dependencies
Backend (Node.js + Express)
Navigate to the backend folder (if separated):

bash
Copy code
cd backend
Install the necessary Node.js packages:

bash
Copy code
npm install
Frontend (React)
Navigate to the frontend folder (if separated):

bash
Copy code
cd frontend
Install the necessary React dependencies:

bash
Copy code
npm install
Step 6: Configure the Environment Variables
Ensure that your application is set up with the appropriate environment variables, such as MySQL credentials and API keys. Create a .env file in the root directory of your backend and frontend if not already present.

Example Backend .env file
bash
Copy code
DB_HOST=localhost
DB_USER=your_db_user
DB_PASSWORD=your_db_password
DB_NAME=your_db_name
PORT=5000
Example Frontend .env file
bash
Copy code
REACT_APP_API_URL=http://your-server-ip:5000
Step 7: Run the Backend and Frontend
Start the Backend (Node + Express)
If you're using nodemon for auto-reloading during development, you can install it globally:

bash
Copy code
sudo npm install -g nodemon
Run the backend:

bash
Copy code
npm start  # or use "nodemon" for development
Build the React Frontend
In the frontend directory, build the production version of your React app:

bash
Copy code
npm run build
Serve the React Frontend
You can use serve or Nginx to serve the static files. First, install serve globally if you're using it:

bash
Copy code
sudo npm install -g serve
Serve the React app:

bash
Copy code
serve -s build
Step 8: Set Up Nginx for Reverse Proxy (Optional)
You can set up Nginx to serve both the React app and reverse proxy requests to the backend API.

Edit the Nginx Configuration
bash
Copy code
sudo nano /etc/nginx/sites-available/default
Example Nginx Configuration
nginx
Copy code
server {
    listen 80;

    server_name your-server-ip;

    root /var/www/your-project/frontend/build;

    location / {
        try_files $uri /index.html;
    }

    location /api/ {
        proxy_pass http://localhost:5000/;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
Test Nginx Configuration and Restart
bash
Copy code
sudo nginx -t
sudo systemctl restart nginx
Step 9: Set Up PM2 for Process Management
To keep your app running even if the server is restarted, you can use PM2 (a process manager for Node.js).

Install PM2 Globally
bash
Copy code
sudo npm install -g pm2
Start the Backend with PM2
bash
Copy code
pm2 start backend/app.js --name "my-backend"
pm2 save
Set Up PM2 to Start on Boot
bash
Copy code
pm2 startup
sudo pm2 startup systemd -u your-username --hp /home/your-username
pm2 save
Step 10: Automate Deployment with Git
You can set up Git hooks, such as a post-merge hook, to automatically deploy the latest changes from your private Git repository.

Step 11: Test Your Application
Open your browser and navigate to your server’s IP address. You should see your React app, and it should be communicating with the Express API.

Conclusion
You've now deployed your full-stack React, Node.js, Express, and MySQL application to a VPS with Ubuntu. You've also set up Git-based deployment, using SSH for authentication. If you need to make changes, simply push them to your Git repository, SSH into your server, pull the latest changes, and restart the services.
