# Express.js + GitHub Actions CI/CD Sample

![แบนเนอร์: สายพานลำเลียงกล่องผ่านเฟือง ชั้นหลอดทดลอง และโคมไฟตรวจ](docs/banner.png)

🚀 ตัวอย่างโปรเจกต์ Express.js พร้อม GitHub Actions CI/CD Pipeline แบบสมบูรณ์

## 📋 สารบัญ

- [คุณสมบัติ](#คุณสมบัติ)
- [เทคโนโลยีที่ใช้](#เทคโนโลยีที่ใช้)
- [โครงสร้างโปรเจกต์](#โครงสร้างโปรเจกต์)
- [การติดตั้งและใช้งาน](#การติดตั้งและใช้งาน)
- [การรัน Tests](#การรัน-tests)
- [Docker](#docker)
- [GitHub Actions CI/CD](#github-actions-cicd)
- [API Endpoints](#api-endpoints)
- [การตั้งค่า Secrets](#การตั้งค่า-secrets)

---

## ✨ คุณสมบัติ

- ✅ Express.js REST API
- ✅ Jest + Supertest สำหรับ Unit Testing
- ✅ Docker Multi-stage Build
- ✅ GitHub Actions CI/CD Pipeline
- ✅ Code Coverage Report
- ✅ Security Scanning with Trivy
- ✅ Health Check Endpoint
- ✅ Error Handling

---

## 🛠 เทคโนโลยีที่ใช้

- **Runtime**: Node.js 22
- **Framework**: Express.js 4.x
- **Testing**: Jest 29.x + Supertest 6.x
- **Containerization**: Docker
- **CI/CD**: GitHub Actions
- **Security**: Trivy Scanner

---

## 📁 โครงสร้างโปรเจกต์

```
sample-github-action/
├── .github/
│   └── workflows/
│       └── main.yml          # GitHub Actions Workflow
├── src/
│   └── app.js               # Express Application
├── tests/
│   └── app.test.js          # Unit Tests
├── .env.example             # Environment Variables Template
├── .gitignore               # Git Ignore
├── Dockerfile               # Docker Configuration
├── package.json             # Dependencies
└── README.md                # This file
```

---

## 🚀 การติดตั้งและใช้งาน

### 1. Clone Repository

```bash
git clone <your-repo-url>
cd sample-github-action
```

### 2. ติดตั้ง Dependencies

```bash
npm install
```

### 3. สร้างไฟล์ .env

```bash
cp .env.example .env
```

แก้ไขไฟล์ `.env` ตามต้องการ:

```env
PORT=3000
NODE_ENV=development
```

### 4. รัน Application

**Development Mode** (with auto-reload):
```bash
npm run dev
```

**Production Mode**:
```bash
npm start
```

เปิดเบราว์เซอร์: http://localhost:3000

---

## 🧪 การรัน Tests

### รัน Tests ครั้งเดียว

```bash
npm test
```

### รัน Tests แบบ Watch Mode

```bash
npm run test:watch
```

### ตัวอย่างผลลัพธ์

```
 PASS  tests/app.test.js
  Express API Tests
    GET /
      ✓ should return welcome message (25 ms)
    GET /health
      ✓ should return health status (3 ms)
    GET /api/users
      ✓ should return list of users (4 ms)
    GET /api/users/:id
      ✓ should return a single user (3 ms)
      ✓ should return 404 for non-existent user (3 ms)
    POST /api/users
      ✓ should create a new user (4 ms)
      ✓ should return 400 if name is missing (3 ms)
      ✓ should return 400 if email is missing (2 ms)
    404 Handler
      ✓ should return 404 for unknown routes (2 ms)

Test Suites: 1 passed, 1 total
Tests:       9 passed, 9 total
```

---

## 🐳 Docker

### Build Docker Image

```bash
docker build -t express-app:latest .
```

### Run Docker Container

```bash
docker run -d -p 3000:3000 --name express-app express-app:latest
```

### ตรวจสอบ Container

```bash
docker ps
docker logs express-app
```

### หยุดและลบ Container

```bash
docker stop express-app
docker rm express-app
```

### Docker Compose (Optional)

สร้างไฟล์ `docker-compose.yml`:

```yaml
version: '3.8'

services:
  app:
    build: .
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
      - PORT=3000
    restart: unless-stopped
```

รันด้วย Docker Compose:

```bash
docker-compose up -d
docker-compose logs -f
docker-compose down
```

---

## 🔄 GitHub Actions CI/CD

### Workflow Overview

Pipeline ประกอบด้วย 4 Jobs:

1. **Build & Test** 🧪
   - Checkout code
   - Install dependencies
   - Run tests with coverage
   - Upload coverage report

2. **Build & Push Docker** 🐳
   - Build Docker image
   - Push to Docker Hub
   - Tag with branch name and SHA

3. **Security Scan** 🔒
   - Scan Docker image for vulnerabilities
   - Upload results to GitHub Security

4. **Deploy** 🚀
   - Deploy to production (configurable)

### Triggers

- ✅ **Push to main**: รัน full pipeline
- ✅ **Pull Request to main**: รัน build & test
- ✅ **Manual**: กดรันได้จากหน้า Actions

---

## 📡 API Endpoints

### 1. Root Endpoint
```http
GET /
```

**Response:**
```json
{
  "message": "Welcome to Express GitHub Actions Sample API",
  "version": "1.0.0",
  "timestamp": "2025-10-21T10:00:00.000Z"
}
```

### 2. Health Check
```http
GET /health
```

**Response:**
```json
{
  "status": "healthy",
  "uptime": 123.456,
  "timestamp": "2025-10-21T10:00:00.000Z"
}
```

### 3. Get All Users
```http
GET /api/users
```

**Response:**
```json
{
  "users": [
    {
      "id": 1,
      "name": "John Doe",
      "email": "john@example.com"
    }
  ]
}
```

### 4. Get User by ID
```http
GET /api/users/:id
```

**Response:**
```json
{
  "user": {
    "id": 1,
    "name": "John Doe",
    "email": "john@example.com"
  }
}
```

**Error Response (404):**
```json
{
  "error": "User not found"
}
```

### 5. Create User
```http
POST /api/users
Content-Type: application/json

{
  "name": "Test User",
  "email": "test@example.com"
}
```

**Response (201):**
```json
{
  "user": {
    "id": 1234567890,
    "name": "Test User",
    "email": "test@example.com"
  },
  "message": "User created successfully"
}
```

**Error Response (400):**
```json
{
  "error": "Name and email are required"
}
```

---

## 🔐 การตั้งค่า Secrets

### ใน GitHub Repository

1. ไปที่ **Settings** → **Secrets and variables** → **Actions**
2. คลิก **New repository secret**
3. เพิ่ม Secrets ดังนี้:

| Secret Name | Description | Example |
|-------------|-------------|---------|
| `DOCKERHUB_USERNAME` | Docker Hub username | `yourusername` |
| `DOCKERHUB_TOKEN` | Docker Hub access token | `dckr_pat_xxxxx` |
| `HOST` | Production server IP | `192.168.1.100` |
| `USERNAME` | SSH username | `ubuntu` |
| `SSH_PRIVATE_KEY` | SSH private key | `-----BEGIN RSA...` |

### สร้าง Docker Hub Access Token

1. ไปที่ [Docker Hub](https://hub.docker.com/)
2. **Account Settings** → **Security** → **New Access Token**
3. ตั้งชื่อ: `github-actions`
4. Permissions: **Read, Write, Delete**
5. คัดลอก token และเพิ่มใน GitHub Secrets

---

## 📝 การใช้งาน GitHub Actions

### 1. Push to Main Branch

```bash
git add .
git commit -m "Add new feature"
git push origin main
```

→ Workflow จะรันอัตโนมัติ: Test → Build → Security Scan → Deploy

### 2. Create Pull Request

```bash
git checkout -b feature/new-feature
git add .
git commit -m "Add new feature"
git push origin feature/new-feature
```

→ สร้าง Pull Request ใน GitHub
→ Workflow จะรัน: Test only

### 3. Manual Trigger

1. ไปที่แท็บ **Actions**
2. เลือก **CI/CD Pipeline - Express App**
3. คลิก **Run workflow**
4. เลือก branch
5. คลิก **Run workflow**

---

## 🐛 Troubleshooting

### ปัญหา: Tests ล้มเหลว

```bash
# ตรวจสอบ Node.js version
node --version  # ควรเป็น v22.x

# ลบ node_modules และติดตั้งใหม่
rm -rf node_modules package-lock.json
npm install
npm test
```

### ปัญหา: Docker build ล้มเหลว

```bash
# ตรวจสอบ Dockerfile syntax
docker build --no-cache -t express-app:latest .

# ดู logs
docker logs express-app
```

### ปัญหา: GitHub Actions ล้มเหลว

1. ตรวจสอบว่าตั้งค่า Secrets ครบถ้วน
2. ดู logs ใน Actions tab
3. ตรวจสอบว่า Docker Hub username ถูกต้อง
4. เปลี่ยน `DOCKER_IMAGE` ใน workflow ให้ตรงกับ username ของคุณ

---

## 📊 Coverage Report

หลังจากรัน tests ไฟล์ coverage จะถูกสร้างใน `coverage/` directory

เปิดดู HTML report:

```bash
# Linux/Mac
open coverage/lcov-report/index.html

# Windows
start coverage/lcov-report/index.html
```

---

## 🔧 Configuration

### แก้ไข Port

ใน `.env`:
```env
PORT=3000
```

### แก้ไข Docker Image Name

ใน `.github/workflows/main.yml`:
```yaml
env:
  DOCKER_IMAGE: 'yourusername/express-app'  # เปลี่ยนตรงนี้
```

---

## 📚 เอกสารเพิ่มเติม

- [Express.js Documentation](https://expressjs.com/)
- [Jest Documentation](https://jestjs.io/)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [Docker Documentation](https://docs.docker.com/)

---

## 📄 License

MIT License - ใช้งานได้อย่างอิสระ

---

## 👨‍💻 Author

Your Name - [@yourusername](https://github.com/yourusername)

---

## 🙏 Contributing

Pull requests are welcome! สำหรับการเปลี่ยนแปลงครั้งใหญ่ กรุณาเปิด issue ก่อน

---

## 📮 Support

หากพบปัญหาหรือมีคำถาม:
- เปิด [GitHub Issue](https://github.com/yourusername/yourrepo/issues)
- ติดต่อผ่าน email: your.email@example.com

---

**Happy Coding! 🚀**
