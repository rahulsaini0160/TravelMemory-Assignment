1️⃣ Architecture Diagram
          Internet
             |
       [Security Group: Web SG]
             |
       EC2 Web Server (Public Subnet)
       ----------------------------
       | Node.js + Express backend |
       | React Frontend served by  |
       |        Nginx             |
       ----------------------------
             |
       [Security Group: DB SG]
             |
       EC2 DB Server (Private Subnet)
       ----------------------------
       |       MongoDB            |
       ----------------------------


Frontend: React served by Nginx on port 80.

Backend: Node.js API running on PM2 (port 5001) with .env for MongoDB URI and JWT secret.

MongoDB: Runs on private EC2, accessible only from web server.

Security Groups:

Web SG → SSH/HTTP/HTTPS

DB SG → MongoDB 27017 only from web server

2️⃣ EC2, MongoDB, Backend, Frontend Setup
Component	IP / Port	Notes
Web Server (EC2)	34.208.165.140	Hosts backend (5001) + frontend via Nginx (80)
Backend	localhost:5001	Node.js + Express, PM2-managed
Frontend	34.208.165.140	React build served by Nginx
DB Server	172.31.45.249:27017	MongoDB, user appUser with password rahul123
Security Groups	Web SG / DB SG	Web SG: SSH/HTTP/HTTPS; DB SG: MongoDB only from Web SG

Environment files:

Backend .env:

PORT=5001
MONGO_URI=mongodb://appUser:rahul123@172.31.45.249:27017/testDatabase
JWT_SECRET=your_jwt_secret_key


Frontend .env:

REACT_APP_API_URL=http://34.208.165.140:5001/api

3️⃣ Commands We Ran
Web Server (EC2)
# Update OS and install git
sudo yum update -y
sudo yum install git -y

# Clone repository
git clone https://github.com/UnpredictablePrashant/TravelMemory.git
cd TravelMemory/backend

# Install dependencies
npm install

# Start backend using PM2
pm2 start index.js --name travelmemory-backend

# Check PM2 logs
pm2 logs travelmemory-backend

# React frontend
cd ../frontend
npm install
npm run build
sudo npm install -g serve

# Nginx setup
sudo nano /etc/nginx/conf.d/travelmemory.conf
# Add config for frontend + backend proxy
sudo nginx -t
sudo systemctl reload nginx

DB Server (EC2)
# Update OS
sudo yum update -y

# Install MongoDB
sudo yum install -y mongodb-org

# Start MongoDB
sudo systemctl start mongod
sudo systemctl enable mongod

# Create user in testDatabase
mongosh -u admin -p rahul123 --authenticationDatabase admin
use testDatabase
db.createUser({
  user: "appUser",
  pwd: "rahul123",
  roles: [ { role: "readWrite", db: "testDatabase" } ]
})


4️⃣ Screenshots / Evidence

Capture the following for submission:

PM2 list showing backend running

pm2 list


Backend logs showing “Server started”

pm2 logs travelmemory-backend


Frontend React app running in browser at http://34.208.165.140/

MongoDB connection working

mongosh -u appUser -p rahul123 --authenticationDatabase testDatabase


Nginx config: /etc/nginx/conf.d/travelmemory.conf

Folder structure of project (backend + frontend directories).


5️⃣ Files to Push to GitHub

Push all the project files needed to run the app, but exclude large node_modules folders. Example:

TravelMemory/
├── backend/
│   ├── index.js
│   ├── package.json
│   ├── package-lock.json
│   ├── controllers/
│   ├── models/
│   ├── routes/
│   └── .env.example   # optional, don't push real secrets
├── frontend/
│   ├── package.json
│   ├── package-lock.json
│   ├── src/
│   ├── public/
│   └── .env.example   # optional, replace secret with placeholder
├── README.md





### Running Ansible Playbooks

- Setup web server:
```bash
ansible-playbook -i hosts.ini web.yml
Setup database server:

bash
Copy code
ansible-playbook -i hosts.ini db.yml
Deploy or update app:

bash
Copy code
ansible-playbook -i hosts.ini deploy.yml




Initialize Git and commit:

git init
git add .
git commit -m "Initial commit: MERN stack assignment with Terraform and Ansible"


Create a GitHub repo (e.g., TravelMemory-Assignment) and copy the repo URL.

Add remote and push:

git remote add origin https://github.com/<your-username>/TravelMemory-Assignment.git
git branch -M main
git push -u origin main