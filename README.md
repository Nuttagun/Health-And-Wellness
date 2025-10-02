# Health and Wellness Web Application
แพลตฟอร์มสุขภาพครบวงจรที่ให้บริการแชทบอท AI อัจฉริยะ, การจัดการข้อมูลสุขภาพ, และระบบวิเคราะห์พฤติกรรมผู้ใช้ พัฒนาด้วยสถาปัตยกรรม Microservices ที่ทันสมัยและยืดหยุ่น

![Architecture Diagram](https://i.imgur.com/your_architecture_diagram.png )
*(แนะนำ: ใส่แผนภาพสถาปัตยกรรมของโปรเจกต์ที่นี่)*

## ✨ คุณสมบัติหลัก (Features)

*   **AI Chatbot (RAG Service):** แชทบอทที่ขับเคลื่อนด้วย LLMs (Gemma) และเทคนิค RAG เพื่อตอบคำถามสุขภาพจากคลังความรู้
*   **Backend Service (Go):** API หลักสำหรับจัดการข้อมูลผู้ใช้, การยืนยันตัวตน, และ Logic ของแอปพลิเคชัน
*   **Frontend Service (React):** ส่วนติดต่อผู้ใช้ที่สวยงามและใช้งานง่าย
*   **Analytics Engine:** บริการวิเคราะห์ข้อมูลพฤติกรรมเพื่อสร้างข้อมูลเชิงลึก
*   **Document Management:** ระบบอัปโหลดและจัดการเอกสารความรู้สำหรับ AI
*   **Secure & Scalable:** ออกแบบมาเพื่อความปลอดภัยและรองรับการขยายตัวในอนาคต

## 👥 ผู้พัฒนา (Contributors)

*   **นายณัฐกุล สมานใจ (B6504359)** - หัวหน้าโครงงาน (Project Lead) ผู้พัฒนาระบบแชทบอทเพื่อสุขภาพ (Health Chatbot System Developer)
*   **นายณัฐกิตติ์ อ่อนอินทร์ (B6508203)** - ผู้พัฒนาระบบสร้างเสริมสุขภาพ (Health Promotion System Developer)
*   **นายจีรพล เดชโสม (B6504748)** - ผู้พัฒนาระบบให้ความรู้สุขภาพ (Health Knowledge System Developer)

## 🛠️ สถาปัตยกรรมและเทคโนโลยี (Tech Stack)

*   **Containerization:** Docker & Docker Compose
*   **Backend:** Go (Golang)
*   **Frontend:** (React)
*   **Database:** MySQL 8.0
*   **AI / LLM:**
    *   **Orchestration:** LangChain
    *   **LLM Service:** Ollama
    *   **Models:** `gemma:4b`, `nomic-embed-text`
    *   **Vector Store:** ChromaDB
*   **Reverse Proxy:** Nginx

---

## 🚀 การติดตั้งและเริ่มต้นใช้งาน (Getting Started)

คู่มือนี้จะแนะนำขั้นตอนการติดตั้งและตั้งค่า Server สำหรับโปรเจกต์

### **1. ข้อกำหนดเบื้องต้น (Prerequisites)**

*   **Git:** สำหรับการดึงโค้ดโปรเจกต์
*   **Docker & Docker Compose:** สำหรับการรันแอปพลิเคชันในรูปแบบ Container
*   **Domain Name:** ชื่อโดเมนที่ชี้มายัง IP Address ของ Server
*   **(แนะนำ) Certbot:** สำหรับการสร้าง SSL Certificate จาก Let's Encrypt

### **2. การดึงโค้ดโปรเจกต์ (Clone the Project)**

```bash
git clone [URL_ของ_Git_Repository_ของคุณ]
cd [ชื่อ_โฟลเดอร์_โปรเจกต์]
```

### **3. การตั้งค่า Environment Variables (`.env`)**

โปรเจกต์นี้ใช้ไฟล์ `.env` เพื่อจัดการค่า Configuration ที่สำคัญสำหรับแต่ละบริการ คุณจะต้องสร้างและแก้ไขไฟล์เหล่านี้

#### **3.1. ไฟล์ `.env` หลัก**

สร้างไฟล์ `.env` ที่ Root ของโปรเจกต์ (ระดับเดียวกับ `docker-compose.yml`) สำหรับกำหนด Port ของบริการต่างๆ:

```env
# ./ .env

BACKEND_PORT=8080
FRONTEND_PORT=5173
RAG_PORT=8002
```

#### **3.2. บริการ Backend (`./backend/.env`)**

สร้างไฟล์ `.env` ที่ `./backend/.env` และใส่ค่าที่จำเป็น:

```env
# ./backend/.env
ตัวอย่าง
DB_HOST=db
DB_PORT=3306
DB_USER=bestAdmin
DB_PASSWORD=1234567890
DB_NAME=healthy_db

DOMAIN=https://yourdomain.com
SERVER=https://yourdomain.com/api
RAG_API_URL=http://rag_service:8002
OLLAMA_API_URL=http://ollama:11434
PYTHON_AI_SERVICE_URL=http://analytics_engine:8000/water-behavior

# --- Security & API Keys ---
GOOGLE_CLIENT_ID=your_google_client_id
GOOGLE_CLIENT_SECRET=your_google_client_secret
SENDER_EMAIL=your_sender_email@example.com
PASSWORD_SENDER_EMAIL=your_email_app_password
JWT_SECRET=generate_a_strong_jwt_secret
ENCRYPTION_KEY=generate_a_32_byte_encryption_key
INTERNAL_SERVICE_API_KEY=generate_a_strong_internal_api_key
LINE_CHANNEL_SECRET=your_line_channel_secret
LINE_CHANNEL_ACCESS_TOKEN=your_line_channel_access_token
```
**หมายเหตุ:** `JWT_SECRET`, `ENCRYPTION_KEY`, และ `INTERNAL_SERVICE_API_KEY` ควรเป็นค่าที่สุ่มขึ้นมาและมีความปลอดภัยสูง

#### **3.3. บริการ Frontend (`./frontend/.env` )**

สร้างไฟล์ `.env` ที่ `./frontend/.env`:

```env
# ./frontend/.env

VITE_FRONTEND_URL=https://yourdomain.com
VITE_API_URL=https://yourdomain.com/api
# VITE_WS_URL=wss://yourdomain.com/ws  (หากมีการใช้งาน WebSocket )
```

#### **3.4. บริการ RAG (`./rag-service/.env`)**

สร้างไฟล์ `.env` ที่ `./rag-service/.env`:

```env
# ./rag-service/.env

EMBEDDING_MODEL_NAME=nomic-embed-text
LLM_MODEL_NAME=gemma:4b
GO_BACKEND_URL=http://backend:8080
INTERNAL_SERVICE_API_KEY=your_strong_internal_api_key_must_match_backend
OLLAMA_PUBLIC_URL=http://ollama:11434
```

### **4. การตั้งค่า Nginx และ SSL**

Nginx ทำหน้าที่เป็น Reverse Proxy และจัดการการเชื่อมต่อแบบ HTTPS

#### **4.1. แก้ไข `nginx.conf`**

เปิดไฟล์ `nginx/nginx.conf` และแก้ไข `server_name` ให้เป็นชื่อโดเมนของคุณ:

```nginx
# nginx/nginx.conf

...
server {
    listen 80;
    server_name yourdomain.com www.yourdomain.com; # <-- แก้ไขตรงนี้
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name yourdomain.com www.yourdomain.com; # <-- และตรงนี้

    ssl_certificate /etc/nginx/ssl/nginx.crt;
    ssl_certificate_key /etc/nginx/ssl/nginx.key;
    ...
}
...
```

#### **4.2. การเตรียม SSL Certificate**

คุณต้องมีไฟล์ Certificate (`.crt` ) และ Key (`.key`) ในโฟลเดอร์ `nginx/ssl/`

*   **สำหรับ Production (แนะนำ):** ใช้ [Certbot](https://certbot.eff.org/ ) เพื่อขอ Certificate ฟรีจาก Let's Encrypt
    1.  ติดตั้ง Certbot บน Server ของคุณ
    2.  รันคำสั่ง `sudo certbot certonly --standalone -d yourdomain.com`
    3.  คัดลอกไฟล์ Certificate ที่ได้ไปยังโปรเจกต์:
        ```bash
        sudo cp /etc/letsencrypt/live/yourdomain.com/fullchain.pem ./nginx/ssl/nginx.crt
        sudo cp /etc/letsencrypt/live/yourdomain.com/privkey.pem ./nginx/ssl/nginx.key
        ```

*   **สำหรับ Development:** สร้าง Self-Signed Certificate
    ```bash
    mkdir -p ./nginx/ssl
    openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout ./nginx/ssl/nginx.key -out ./nginx/ssl/nginx.crt
    ```
    (เบราว์เซอร์จะแสดงคำเตือนความปลอดภัยเมื่อใช้ Certificate ประเภทนี้)

### **5. การเตรียมโมเดล AI (Ollama)**

ก่อนรันโปรเจกต์ทั้งหมด ควรดาวน์โหลดโมเดล AI ที่จำเป็นเก็บไว้ใน Ollama ก่อน เพื่อให้ RAG service พร้อมใช้งานทันที

1.  **เริ่มบริการ Ollama เพียงอย่างเดียว:**
    ```bash
    docker compose up -d ollama
    ```

2.  **รอสักครู่** แล้วดาวน์โหลดโมเดลที่ต้องการ:
    ```bash
    # ดาวน์โหลดโมเดล Gemma (สำหรับสร้างคำตอบ)
    docker compose exec ollama ollama pull gemma:2b

    # ดาวน์โหลดโมเดล Nomic (สำหรับทำ Embeddings)
    docker compose exec ollama ollama pull nomic-embed-text
    ```
    *ขั้นตอนนี้อาจใช้เวลาสักครู่ ขึ้นอยู่กับความเร็วอินเทอร์เน็ต*

3.  เมื่อดาวน์โหลดเสร็จแล้ว คุณสามารถหยุดบริการ Ollama ไว้ก่อนได้ (ถ้าต้องการ):
    ```bash
    docker compose stop ollama
    ```

### **6. การรันโปรเจกต์ทั้งหมด**

หลังจากตั้งค่าและเตรียมโมเดลเรียบร้อยแล้ว ให้รัน Docker Compose เพื่อเริ่มบริการทั้งหมด:

```bash
docker compose up -d --build
```

*   `--build`: บังคับให้ Docker สร้าง Image ใหม่ทั้งหมดในการรันครั้งแรก
*   `-d`: รัน Container ทั้งหมดในเบื้องหลัง (Detached mode)

### **7. การตรวจสอบสถานะ**

ตรวจสอบว่า Container ทั้งหมดทำงานปกติหรือไม่:

```bash
docker compose ps
```

คุณควรจะเห็นสถานะ `running` หรือ `Up` สำหรับทุกบริการ

หากต้องการดู Log ของบริการใดบริการหนึ่ง:
```bash
docker compose logs -f backend
```

---

## ⚖️ License

โปรเจกต์นี้เผยแพร่ภายใต้ **MIT License** - ดูรายละเอียดเพิ่มเติมได้ที่ไฟล์ [LICENSE](LICENSE).

### **โมเดล AI ที่ใช้งาน (AI Models Used)**

โปรเจกต์นี้มีการใช้งานโมเดลปัญญาประดิษฐ์จากภายนอก ซึ่งอยู่ภายใต้ License ของตนเอง:

*   **Gemma:** พัฒนาโดย Google และเผยแพร่ภายใต้ [Gemma Terms of Use](https://ai.google.dev/gemma/terms ) ซึ่งอนุญาตให้ใช้งานในเชิงพาณิชย์ได้
*   **Nomic Embed Text:** พัฒนาโดย Nomic และเผยแพร่ภายใต้ [Apache 2.0 License](https://www.apache.org/licenses/LICENSE-2.0 ) ซึ่งอนุญาตให้ใช้งานในเชิงพาณิชย์ได้

เราขอขอบคุณผู้พัฒนาโมเดลเหล่านี้สำหรับการสนับสนุนวงการ Open Source AI

## 🤝 การมีส่วนร่วม (Contributing)

เรายินดีต้อนรับการมีส่วนร่วมจากทุกคน! หากคุณสนใจที่จะช่วยพัฒนาโปรเจกต์ สามารถทำตามขั้นตอนต่อไปนี้:
1.  Fork โปรเจกต์นี้
2.  สร้าง Branch ใหม่สำหรับ Feature ของคุณ (`git checkout -b feature/AmazingFeature`)
3.  Commit การเปลี่ยนแปลงของคุณ (`git commit -m 'Add some AmazingFeature'`)
4.  Push ไปยัง Branch ของคุณ (`git push origin feature/AmazingFeature`)
5.  เปิด Pull Request

