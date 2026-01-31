📘 PROJECT DOCUMENTATION


Project Title
Matrix-Based Secure Chat Application with Telegram Integration
________________________________________
1. Introduction
This project is a self-hosted secure messaging platform built using Matrix Synapse with Telegram Bridge (mautrix-telegram) integration.
It allows users to:
•	Create their own chat server
•	Communicate using Matrix clients (Element)
•	Connect Telegram accounts
•	Sync messages between Matrix and Telegram
•	Maintain privacy and control over data
This system works similar to WhatsApp/Telegram but is self-hosted.
________________________________________
2. Objectives
•	To build a decentralized chat server
•	To integrate Telegram with Matrix
•	To enable cross-platform messaging
•	To provide secure communication
•	To create a real-time messaging system
________________________________________
3. Technologies Used
Technology	Purpose
Linux (AWS EC2)	Server hosting
Docker	Container management
Matrix Synapse	Chat server
Element	Chat client
mautrix-telegram	Telegram bridge
DuckDNS	Domain service
SQLite	Database
Git	Version control
________________________________________







4. System Architecture
User (Element App)
        |
        |
Matrix Synapse Server
        |
        |
Telegram Bridge (mautrix)
        |
        |
Telegram Network
________________________________________
5. System Requirements
Hardware
•	Minimum 2GB RAM
•	20GB Storage
•	Internet connection
Software
•	Ubuntu / Amazon Linux
•	Docker & Docker Compose
•	Matrix Synapse
•	mautrix-telegram
•	Element Client
________________________________________
6. Project Setup Process
________________________________________
Step 1: Create Server (AWS EC2)
1.	Launch EC2 instance
2.	Select Linux OS
3.	Connect via SSH
ssh -i key.pem ec2-user@server-ip 
________________________________________
Step 2: Install Docker
sudo yum update -y sudo yum install docker -y sudo systemctl start docker sudo systemctl enable docker 
Check:
docker --version 
________________________________________
Step 3: Install Docker Compose
sudo curl -L "https://github.com/docker/compose/releases/download/v2.25.0/docker-compose-$(uname -s)-$(uname -m)" \ -o /usr/local/bin/docker-compose sudo chmod +x /usr/local/bin/docker-compose 
Check:
docker-compose --version 
________________________________________
Step 4: Setup Domain (DuckDNS)
1.	Register at duckdns.org
2.	Create domain: yourdomain.duckdns.org
3.	Update IP regularly
________________________________________
Step 5: Create Project Directory
mkdir matrix cd matrix mkdir synapse 
________________________________________
Step 6: Generate Synapse Configuration
docker run -it --rm \ -v $(pwd)/synapse/data:/data \ -e SYNAPSE_SERVER_NAME=yourdomain.duckdns.org \ -e SYNAPSE_REPORT_STATS=no \ matrixdotorg/synapse:latest generate 
________________________________________
Step 7: Edit homeserver.yaml
nano synapse/data/homeserver.yaml 
Main configuration:
server_name: "yourdomain.duckdns.org" listeners: - port: 8008 type: http tls: false bind_addresses: ['0.0.0.0'] 
________________________________________
Step 8: Create Docker Compose File
Create file:
nano docker-compose.yml 
version: "3" services: synapse: image: matrixdotorg/synapse:latest container_name: synapse volumes: - ./synapse/data:/data ports: - "8008:8008" restart: always 
Start server:
docker-compose up -d 
Check:
docker ps docker logs synapse 
________________________________________
Step 9: Setup Telegram Bridge
Create Directory
mkdir mautrix cd mautrix 
Download mautrix-telegram
docker pull dock.mau.dev/mautrix/telegram:latest 
________________________________________
Step 10: Generate Registration File
docker run --rm \ -v $(pwd)/mautrix:/data \ dock.mau.dev/mautrix/telegram:latest \ python3 -m mautrix_telegram -g 
________________________________________
Step 11: Configure mautrix
Edit config:
nano mautrix/config.yaml 
Set homeserver:
homeserver: address: http://synapse:8008 domain: yourdomain.duckdns.org 
________________________________________
Step 12: Register Bridge with Synapse
Edit homeserver.yaml:
app_service_config_files: - /data/registration.yaml 
Restart:
docker restart synapse 
________________________________________
Step 13: Start Telegram Bridge
docker run -d \ --name mautrix-telegram \ -v $(pwd)/mautrix:/data \ --network=host \ dock.mau.dev/mautrix/telegram:latest 
Check:
docker logs mautrix-telegram 
________________________________________
Step 14: Install Element Client
Download:
https://element.io
Login:
Username: @akash:yourdomain.duckdns.org
Server: https://yourdomain.duckdns.org
________________________________________
Step 15: Login to Telegram Bot
1.	Open DM with Telegram Bridge Bot
2.	Type:
login
3.	Enter phone number
4.	Enter OTP
________________________________________
Step 16: Create Bridged Room
!tg bridge
!tg chat list
!tg bridge <chat-id>

