# Express.js + GitHub Actions CI/CD Sample

![Banner: a conveyor belt carries boxes past gears, a rack of test tubes, and an inspection lamp](docs/banner.png)

Portfolio: https://suphanut.pages.dev

A sample Express.js project with a GitHub Actions CI/CD pipeline.

## Table of Contents

- [Features](#features)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Install and Run](#install-and-run)
- [Run Tests](#run-tests)
- [Docker](#docker)
- [GitHub Actions CI/CD](#github-actions-cicd)
- [API Endpoints](#api-endpoints)
- [Set Up Secrets](#set-up-secrets)

---

## Features

- Express.js REST API
- Jest + Supertest for unit tests
- Docker multi-stage build
- GitHub Actions CI/CD pipeline
- Code coverage report (see [Coverage Report](#coverage-report) for a limit)
- Security scan with Trivy
- Health check endpoint
- Error handling

---

## Tech Stack

- **Runtime**: Node.js 22
- **Framework**: Express.js 4.x
- **Testing**: Jest 29.x + Supertest 6.x
- **Containerization**: Docker
- **CI/CD**: GitHub Actions
- **Security**: Trivy Scanner

---

## Project Structure

```
sample-github-action-demo/
├── .github/
│   └── workflows/
│       └── main.yml          # GitHub Actions Workflow
├── docs/
│   └── banner.png           # README banner
├── src/
│   └── app.js               # Express Application
├── tests/
│   └── app.test.js          # Unit Tests
├── .env.example             # Environment Variables Template
├── .gitignore               # Git Ignore
├── COMPARISON.md            # Jenkins vs GitHub Actions comparison (in Thai)
├── Dockerfile               # Docker Configuration
├── QUICKSTART.md            # Quick start guide (in Thai)
├── package.json             # Dependencies and scripts
├── package-lock.json        # Locked dependency versions
└── README.md                # This file
```

---

## Install and Run

### 1. Clone Repository

```bash
git clone <your-repo-url>
cd sample-github-action-demo
```

### 2. Install Dependencies

```bash
npm install
```

### 3. Create the .env File

```bash
cp .env.example .env
```

Edit `.env` as needed:

```env
PORT=3000
NODE_ENV=development
```

### 4. Run the Application

**Development Mode** (with auto-reload):
```bash
npm run dev
```

**Production Mode**:
```bash
npm start
```

Open http://localhost:3000 in your browser.

---

## Run Tests

### Run Tests Once

```bash
npm test
```

### Run Tests in Watch Mode

```bash
npm run test:watch
```

### Example Output

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

## Docker

### Build Docker Image

```bash
docker build -t express-app:latest .
```

### Run Docker Container

```bash
docker run -d -p 3000:3000 --name express-app express-app:latest
```

### Check the Container

```bash
docker ps
docker logs express-app
```

### Stop and Remove the Container

```bash
docker stop express-app
docker rm express-app
```

### Docker Compose (Optional)

The repository has no `docker-compose.yml`. To use Docker Compose, create one:

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

Run it with Docker Compose:

```bash
docker-compose up -d
docker-compose logs -f
docker-compose down
```

---

## GitHub Actions CI/CD

### Workflow Overview

The pipeline in `.github/workflows/main.yml` has 4 jobs:

1. **Build & Test**
   - Checkout code
   - Install dependencies
   - Run tests with coverage
   - Upload coverage report

2. **Build & Push Docker**
   - Build Docker image
   - Push to Docker Hub
   - Tag with branch name and SHA, plus `latest` on the default branch

3. **Security Scan**
   - Scan the Docker image for vulnerabilities
   - Upload results to GitHub Security
   - Both steps use `continue-on-error`, so a failed scan does not stop the pipeline

4. **Deploy**
   - Runs on a self-hosted runner with the label `self-hosted-windows-docker`. Without such a runner, the job stays queued.
   - Prints deploy messages only. The SSH deploy step is commented out. Edit it to match your server.

### Triggers

- **Push to main**: runs the full pipeline.
- **Pull request to main**: runs Build & Test only.
- **Manual**: run it from the Actions tab. The Deploy job runs only when you pick `main`.

---

## API Endpoints

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
    },
    {
      "id": 2,
      "name": "Jane Smith",
      "email": "jane@example.com"
    },
    {
      "id": 3,
      "name": "Bob Johnson",
      "email": "bob@example.com"
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

Any other route returns 404 with `{"error": "Route not found"}`.

---

## Set Up Secrets

### In the GitHub Repository

1. Go to **Settings** → **Secrets and variables** → **Actions**.
2. Click **New repository secret**.
3. Add these secrets:

| Secret Name | Description | Example |
|-------------|-------------|---------|
| `DOCKERHUB_USERNAME` | Docker Hub username | `yourusername` |
| `DOCKERHUB_TOKEN` | Docker Hub access token | `dckr_pat_xxxxx` |
| `HOST` | Production server IP | `192.168.1.100` |
| `USERNAME` | SSH username | `ubuntu` |
| `SSH_PRIVATE_KEY` | SSH private key | `-----BEGIN RSA...` |

The workflow needs only `DOCKERHUB_USERNAME` and `DOCKERHUB_TOKEN` today. `HOST`, `USERNAME`, and `SSH_PRIVATE_KEY` are for the commented-out SSH deploy step.

### Create a Docker Hub Access Token

1. Go to [Docker Hub](https://hub.docker.com/).
2. Open **Account Settings** → **Security** → **New Access Token**.
3. Name it `github-actions`.
4. Set permissions to **Read & Write**. Pushing an image needs no more than that.
5. Copy the token and add it to GitHub Secrets.

---

## Use GitHub Actions

### 1. Push to the Main Branch

```bash
git add .
git commit -m "Add new feature"
git push origin main
```

→ The workflow runs: Test → Build → Security Scan → Deploy

### 2. Create a Pull Request

```bash
git checkout -b feature/new-feature
git add .
git commit -m "Add new feature"
git push origin feature/new-feature
```

→ Open a pull request on GitHub.
→ The workflow runs tests only.

### 3. Manual Trigger

1. Open the **Actions** tab.
2. Select **CI/CD Pipeline - Express App**.
3. Click **Run workflow**.
4. Pick a branch.
5. Click **Run workflow**.

---

## Troubleshooting

### Problem: Tests Fail

```bash
# Check the Node.js version
node --version  # should be v22.x

# Remove node_modules and reinstall
rm -rf node_modules package-lock.json
npm install
npm test
```

### Problem: Docker Build Fails

```bash
# Rebuild without the cache
docker build --no-cache -t express-app:latest .

# If the container starts but fails, read its logs
docker logs express-app
```

### Problem: GitHub Actions Fails

1. Check that you set all required secrets.
2. Read the logs in the Actions tab.
3. Check that the Docker Hub username is correct.
4. Set `DOCKER_IMAGE` in the workflow to your Docker Hub username.

---

## Coverage Report

`npm test` runs Jest with `--coverage` and writes the report to `coverage/`.

Note: `collectCoverageFrom` in `package.json` excludes `src/app.js`, which is the only source file. The report lists no files until you remove `"!src/app.js"` from that list.

Open the HTML report:

```bash
# macOS
open coverage/lcov-report/index.html

# Linux
xdg-open coverage/lcov-report/index.html

# Windows
start coverage/lcov-report/index.html
```

---

## Configuration

### Change the Port

In `.env`:
```env
PORT=3000
```

### Change the Docker Image Name

In `.github/workflows/main.yml`:
```yaml
env:
  DOCKER_IMAGE: "yourusername/express-app-action" # change this
```

---

## Further Reading

- [Express.js Documentation](https://expressjs.com/)
- [Jest Documentation](https://jestjs.io/)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [Docker Documentation](https://docs.docker.com/)

---

## License

MIT, as declared in `package.json`. The repository has no separate `LICENSE` file.

---

## Contributing

Pull requests are welcome. For major changes, open an issue first.

---

## Support

For problems or questions, open a [GitHub Issue](../../issues).
