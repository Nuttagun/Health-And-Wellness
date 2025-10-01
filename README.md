# คู่มือการติดตั้ง Server

คู่มือนี้จะแนะนำขั้นตอนการติดตั้งและตั้งค่า Server ของคุณ โดยครอบคลุมตั้งแต่การดึงโค้ดโปรเจกต์ การตั้งค่าไฟล์ .env สำหรับแต่ละบริการ และการตั้งค่า SSL สำหรับ Nginx

## สารบัญ

1. [การดึงโค้ดโปรเจกต์](#1-การดึงโค้ดโปรเจกต์-pulling-the-project)
2. [การตั้งค่าไฟล์ .env สำหรับแต่ละบริการ](#2-การตั้งค่าไฟล์-env-สำหรับแต่ละบริการ)
   - [2.1. บริการ backend](#21-บริการ-backend)
   - [2.2. บริการ frontend](#22-บริการ-frontend)
   - [2.3. บริการ rag_service](#23-บริการ-rag_service)
3. [การตั้งค่า Nginx และ SSL](#3-การตั้งค่า-nginx-และ-ssl)
   - [3.1. ไฟล์ nginx.conf](#31-ไฟล์-nginxconf)
   - [3.2. การตั้งค่า SSL Certificate และ Key](#32-การตั้งค่า-ssl-certificate-และ-key)
     - [3.2.1. การใช้ Let's Encrypt](#321-การใช้-lets-encrypt-แนะนำสำหรับ-production)
     - [3.2.2. การสร้าง Self-Signed Certificate](#322-การสร้าง-self-signed-certificate-สำหรับ-development-หรือ-testing)
4. [การรัน Docker Compose](#4-การรัน-docker-compose)
5. [การตรวจสอบสถานะ](#5-การตรวจสอบสถานะ)

## 1. การดึงโค้ดโปรเจกต์ (Pulling the Project)

ก่อนอื่น คุณต้องดึงโค้ดโปรเจกต์จาก Git repository มายังเครื่อง Server ของคุณ สมมติว่าคุณมี URL ของ repository แล้ว ให้ใช้คำสั่งต่อไปนี้ใน Terminal:

git clone [URL_ของ_Git_Repository_ของคุณ]
cd [ชื่อ_โฟลเดอร์_โปรเจกต์]

แทนที่ [URL_ของ_Git_Repository_ของคุณ] ด้วย URL จริงของ repository ของคุณ และ [ชื่อ_โฟลเดอร์_โปรเจกต์] ด้วยชื่อโฟลเดอร์ที่โปรเจกต์จะถูกสร้างขึ้นมา

## 2. การตั้งค่าไฟล์ .env สำหรับแต่ละบริการ

โปรเจกต์นี้มีการใช้งาน Docker Compose ซึ่งแต่ละบริการ (Service) จะมีไฟล์ .env ของตัวเองเพื่อเก็บค่าตัวแปรสภาพแวดล้อม (Environment Variables) ที่จำเป็น คุณจะต้องแก้ไขไฟล์เหล่านี้เพื่อให้ Server ทำงานได้อย่างถูกต้อง

จากไฟล์ docker-compose.yml ที่ให้มา มี 3 บริการหลักที่คุณต้องตรวจสอบและแก้ไขไฟล์ .env ได้แก่ backend, frontend และ rag_service

### 2.1. บริการ backend

ไฟล์ .env สำหรับบริการ backend จะอยู่ที่ ./backend/.env เนื้อหาตัวอย่างของไฟล์นี้คือ:

DB_HOST=db
DB_PORT=3306
DB_USER=bestAdmin
DB_PASSWORD=1234567890
DB_NAME=healthy_db
DOMAIN=https://localhost
SERVER=https://localhost/api
RAG_API_URL=http://rag_service:8002
OLLAMA_API_URL=http://ollama:11434
PYTHON_AI_SERVICE_URL=http://analytics_engine:8000/water-behavior
GOOGLE_CLIENT_ID=41428359........
GOOGLE_CLIENT_SECRET=GOC................
SENDER_EMAIL=j.......
PASSWORD_SENDER_EMAIL=gd..............
JWT_SECRET_KEY=ver.....................
JWT_SECRET=zx..............................
ENCRYPTION_KEY=il......................
TELEGRAM_BOT_TOKEN=8071........................
TELEGRAM_CHAT_IDS=-1...................
INTERNAL_SERVICE_API_KEY=S3......................
LINE_CHANNEL_SECRET=dac.........................................
LINE_CHANNEL_ACCESS_TOKEN=+YSOO....................................

คุณจะต้องแก้ไขค่าต่อไปนี้ให้ตรงกับการตั้งค่าของคุณ:

* **DOMAIN**: โดเมนหลักของ Server ของคุณ (เช่น https://yourdomain.com)
* **SERVER**: URL ของ API Backend ของคุณ (เช่น https://yourdomain.com/api)
* **GOOGLE_CLIENT_ID**, **GOOGLE_CLIENT_SECRET**: ข้อมูลรับรองสำหรับ Google OAuth (หากมีการใช้งาน). คุณสามารถดูวิธีการขอได้จาก [Google Cloud Console](https://console.cloud.google.com/)
* **SENDER_EMAIL**, **PASSWORD_SENDER_EMAIL**: ข้อมูลสำหรับส่งอีเมล (หากมีการใช้งาน). ตรวจสอบกับผู้ให้บริการอีเมลของคุณสำหรับรายละเอียด
* **JWT_SECRET_KEY**, **JWT_SECRET**, **ENCRYPTION_KEY**: คีย์ลับสำหรับความปลอดภัย. ควรสร้างคีย์ที่แข็งแกร่งและไม่ซ้ำกัน
* **TELEGRAM_BOT_TOKEN**, **TELEGRAM_CHAT_IDS**: ข้อมูลสำหรับ Telegram Bot (หากมีการใช้งาน). ดูวิธีการสร้าง Bot ได้จาก [BotFather ใน Telegram](https://core.telegram.org/bots#botfather)
* **INTERNAL_SERVICE_API_KEY**: คีย์ API สำหรับบริการภายใน (หากมีการใช้งาน). ควรสร้างคีย์ที่แข็งแกร่ง
* **LINE_CHANNEL_SECRET**, **LINE_CHANNEL_ACCESS_TOKEN**: ข้อมูลสำหรับ LINE Messaging API (หากมีการใช้งาน). ดูวิธีการตั้งค่าได้จาก [LINE Developers](https://developers.line.biz/)

### 2.2. บริการ frontend

ไฟล์ .env สำหรับบริการ frontend จะอยู่ที่ ./frontend/.env เนื้อหาตัวอย่างของไฟล์นี้คือ:

# VITE_WS_URL=ws://localhost:8080
# VITE_FRONTEND_URL=https://localhost
# VITE_API_URL=https://localhost/api

คุณจะต้องแก้ไขค่าต่อไปนี้ให้ตรงกับการตั้งค่าของคุณ (โดยการลบเครื่องหมาย # ออกและแก้ไขค่า):

* **VITE_FRONTEND_URL**: URL ของ Frontend ของคุณ (เช่น https://yourdomain.com)
* **VITE_API_URL**: URL ของ API Backend ของคุณ (เช่น https://yourdomain.com/api)
* **VITE_WS_URL**: URL ของ WebSocket (หากมีการใช้งาน) (เช่น wss://yourdomain.com/ws)

### 2.3. บริการ rag_service

ไฟล์ .env สำหรับบริการ rag_service จะอยู่ที่ ./rag-service/.env เนื้อหาตัวอย่างของไฟล์นี้คือ:

INTERNAL_SERVICE_API_KEY=S3......................
GO_BACKEND_URL=https://localhost/api
ollama_public_url=http://ollama:11434

คุณจะต้องแก้ไขค่าต่อไปนี้ให้ตรงกับการตั้งค่าของคุณ:

* **INTERNAL_SERVICE_API_KEY**: คีย์ API สำหรับบริการภายใน (ควรเป็นคีย์เดียวกับที่ใช้ใน backend)
* **GO_BACKEND_URL**: URL ของ API Backend ของคุณ (เช่น https://yourdomain.com/api)

## 3. การตั้งค่า Nginx และ SSL

Nginx ทำหน้าที่เป็น Reverse Proxy และจัดการ SSL/TLS สำหรับ Server ของคุณ คุณจะต้องตั้งค่าไฟล์ nginx.conf และเตรียม SSL Certificate และ Key

### 3.1. ไฟล์ nginx.conf

ไฟล์ nginx.conf ที่ให้มาอยู่ใน nginx/nginx.conf และถูก mount เข้าไปใน container ของ Nginx เนื้อหาตัวอย่างคือ:

nginx
events {
    worker_connections 1024;
}

http {
    server {
        listen 80;
        server_name _;
        return 301 https://$host$request_uri;
    }

    server {
        listen 443 ssl;
        server_name _;

        ssl_certificate /etc/nginx/ssl/nginx.crt;
        ssl_certificate_key /etc/nginx/ssl/nginx.key;

        # Frontend
        location / {
            proxy_pass http://frontend:5173; 
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "upgrade";
            proxy_set_header Host $host;
            proxy_cache_bypass $http_upgrade;
        }

        # Backend API
        location /api/ {
            proxy_pass http://backend:8080/;
            proxy_http_version 1.1;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
        }

        location /ws/ {
            proxy_pass ws://backend:8080/;
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "Upgrade";
            proxy_set_header Host $host;
            proxy_cache_bypass $http_upgrade;
        }
    }
}

คุณจะต้องแก้ไข server_name _; ใน server block ทั้งสองให้เป็นชื่อโดเมนของคุณ (เช่น server_name yourdomain.com;)

### 3.2. การตั้งค่า SSL Certificate และ Key

Nginx ต้องการไฟล์ ssl_certificate (.crt) และ ssl_certificate_key (.key) เพื่อเปิดใช้งาน HTTPS ไฟล์เหล่านี้จะถูกเก็บไว้ใน /etc/nginx/ssl ภายใน container ซึ่งถูก mount มาจาก ./nginx/ssl บนเครื่องโฮสต์ของคุณ

คุณมีสองทางเลือกในการรับ SSL Certificate:

#### 3.2.1. การใช้ Let's Encrypt (แนะนำสำหรับ Production)

Let's Encrypt ให้บริการ SSL Certificate ฟรีและเป็นที่ยอมรับ คุณสามารถใช้ Certbot เพื่อขอและติดตั้ง Certificate ได้โดยอัตโนมัติ

*ขั้นตอนโดยย่อ:*

1. *ติดตั้ง Certbot*: ทำตามคำแนะนำบนเว็บไซต์ [Certbot](https://certbot.eff.org/) เพื่อติดตั้ง Certbot บน Server ของคุณ (เลือก Nginx และระบบปฏิบัติการของคุณ)
2. *ขอ Certificate*: ใช้คำสั่ง Certbot เพื่อขอ Certificate สำหรับโดเมนของคุณ ตัวอย่าง:
   
   sudo certbot --nginx -d yourdomain.com -d www.yourdomain.com
   
   แทนที่ yourdomain.com ด้วยโดเมนจริงของคุณ Certbot จะจัดการการตั้งค่า Nginx ให้คุณโดยอัตโนมัติ และจะสร้างไฟล์ Certificate และ Key ใน /etc/letsencrypt/live/yourdomain.com/
3. *คัดลอกไฟล์*: คุณจะต้องคัดลอกไฟล์ fullchain.pem (สำหรับ ssl_certificate) และ privkey.pem (สำหรับ ssl_certificate_key) ไปยังโฟลเดอร์ ./nginx/ssl ของโปรเจกต์ของคุณ และเปลี่ยนชื่อให้เป็น nginx.crt และ nginx.key ตามลำดับ:
   
   sudo cp /etc/letsencrypt/live/yourdomain.com/fullchain.pem ./nginx/ssl/nginx.crt
   sudo cp /etc/letsencrypt/live/yourdomain.com/privkey.pem ./nginx/ssl/nginx.key
   
   *หมายเหตุ*: คุณอาจต้องปรับสิทธิ์การเข้าถึงไฟล์เหล่านี้เพื่อให้ Nginx สามารถอ่านได้

#### 3.2.2. การสร้าง Self-Signed Certificate (สำหรับ Development หรือ Testing)

Self-Signed Certificate เหมาะสำหรับการพัฒนาและทดสอบภายใน เนื่องจากไม่ได้ถูกรับรองโดย Certificate Authority (CA) ภายนอก ดังนั้นเบราว์เซอร์จะแสดงคำเตือนด้านความปลอดภัย

*ขั้นตอนโดยย่อ:*

1. *สร้างโฟลเดอร์ SSL*: ตรวจสอบให้แน่ใจว่ามีโฟลเดอร์ ./nginx/ssl อยู่ในโปรเจกต์ของคุณ หากไม่มี ให้สร้างขึ้นมา:
   
   mkdir -p ./nginx/ssl
   
2. *สร้าง Certificate และ Key*: ใช้ OpenSSL เพื่อสร้าง Self-Signed Certificate และ Private Key:
   
   sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout ./nginx/ssl/nginx.key -out ./nginx/ssl/nginx.crt
   
   คำสั่งนี้จะขอข้อมูลบางอย่างจากคุณ เช่น Country Name, State or Province Name, Common Name (ซึ่งควรเป็นชื่อโดเมนหรือ IP Address ของ Server ของคุณ) เป็นต้น

## 4. การรัน Docker Compose

หลังจากที่คุณได้ตั้งค่าไฟล์ .env และ SSL Certificate เรียบร้อยแล้ว คุณสามารถรัน Server ของคุณได้โดยใช้ Docker Compose:

sudo docker compose up -d

คำสั่งนี้จะสร้างและรันทุกบริการที่กำหนดไว้ใน docker-compose.yml ในโหมด detached (ทำงานในพื้นหลัง)

## 5. การตรวจสอบสถานะ

คุณสามารถตรวจสอบสถานะของบริการต่างๆ ได้โดยใช้คำสั่ง:

sudo docker compose ps

หากทุกอย่างทำงานได้อย่างถูกต้อง คุณควรจะสามารถเข้าถึง Server ของคุณผ่านโดเมนที่คุณตั้งค่าไว้ได้

## แหล่งอ้างอิง

* [Certbot Documentation](https://certbot.eff.org/)
* [How To Create a Self-Signed SSL Certificate for Nginx on CentOS 7](https://www.digitalocean.com/community/tutorials/how-to-create-a-self-signed-ssl-certificate-for-nginx-on-centos-7)
* [Configuring HTTPS servers Nginx](https://nginx.org/en/docs/http/configuring_https_servers.html)
