# codesphere
# CodeSphere

**CodeSphere** is a full-stack online code execution platform that lets users write, run, save, share, and interact with code directly from a browser.

It provides a browser-based code editor with support for multiple programming languages, secure containerized execution, real-time execution status, interactive REPL sessions, user authentication, and persistent code-file storage.

---

## ✨ Features

* 🧑‍💻 **Online Code Editor**

  * Monaco Editor-based coding experience
  * Syntax highlighting and language-specific editing

* 🚀 **Multi-language Execution**

  * Python
  * C
  * C++
  * Java
  * JavaScript

* ⚡ **Real-time Execution**

  * Code submissions are placed into a Redis-backed job queue
  * Worker processes execute jobs asynchronously
  * WebSockets provide real-time job status and output

* 🖥️ **Interactive REPL**

  * Interactive Python sessions
  * Interactive JavaScript/Node.js sessions
  * Terminal interface powered by xterm

* 💾 **Code File Management**

  * Save source code
  * Load previously saved files
  * Delete saved files
  * Language-specific files

* 🔗 **Code Sharing**

  * Generate shareable links for saved code
  * Load shared code directly through a URL

* 🔐 **Authentication**

  * User registration
  * Login using JWT authentication
  * Password hashing with bcrypt
  * Protected user-specific resources

* 🐳 **Containerized Execution**

  * Programs execute inside a dedicated Docker sandbox
  * Non-root execution user
  * Network disabled for interactive execution
  * Execution timeout for batch jobs

* 🗄️ **Persistent Database**

  * PostgreSQL stores users and saved code files
  * Redis handles job queues, job state, and real-time updates

---

## 🏗️ Architecture

CodeSphere consists of five main services:

```text
                         ┌───────────────────┐
                         │     Browser       │
                         │ React + Monaco    │
                         │      xterm        │
                         └─────────┬─────────┘
                                   │
                                   ▼
                         ┌───────────────────┐
                         │     Frontend      │
                         │ Vite + Nginx      │
                         │    Port 3000      │
                         └─────────┬─────────┘
                                   │
                         ┌─────────┴─────────┐
                         │                   │
                         ▼                   ▼
                ┌────────────────┐   ┌────────────────┐
                │    Backend     │   │     Worker     │
                │    FastAPI     │   │ Python + Docker│
                │    Port 8000   │   │    Port 8001   │
                └───────┬────────┘   └───────┬────────┘
                        │                    │
              ┌─────────┴─────────┐          │
              ▼                   ▼          ▼
       ┌─────────────┐     ┌──────────┐ ┌──────────────┐
       │ PostgreSQL  │     │  Redis   │ │ Code Sandbox │
       │    :5432    │     │  :6379   │ │    Docker    │
       └─────────────┘     └──────────┘ └──────────────┘
```

### Request Flow

1. User writes code in the browser.
2. Frontend sends the submission to the FastAPI backend.
3. Backend creates a job and pushes it to Redis.
4. Worker retrieves the job from Redis.
5. Worker creates the required source file.
6. Code is compiled when necessary.
7. Code executes inside the sandbox container.
8. Output is stored in Redis.
9. Redis publishes a job update.
10. Backend forwards the update to the frontend through WebSockets.
11. Frontend displays the execution result.

---

## 🛠️ Technology Stack

### Frontend

* React 19
* Vite
* Monaco Editor
* xterm
* Tailwind CSS
* Radix UI
* Lucide React
* React Router

### Backend

* Python 3.10
* FastAPI
* Uvicorn
* SQLAlchemy
* PostgreSQL
* Redis
* Pydantic
* JWT
* Passlib / bcrypt
* Docker SDK

### Worker

* Python
* Redis
* Docker SDK
* aiohttp
* Docker containers for code execution

### Infrastructure

* Docker
* Docker Compose
* Nginx
* PostgreSQL 13
* Redis Alpine

---

## 📁 Project Structure

```text
CodeSphere/
│
├── backend/
│   ├── app/
│   │   ├── routers/
│   │   │   ├── auth.py
│   │   │   ├── files.py
│   │   │   └── jobs.py
│   │   │
│   │   ├── database.py
│   │   ├── main.py
│   │   ├── models.py
│   │   ├── schemas.py
│   │   └── security.py
│   │
│   ├── Dockerfile
│   └── requirements.txt
│
├── frontend/
│   ├── public/
│   ├── src/
│   │   ├── components/
│   │   ├── contexts/
│   │   ├── lib/
│   │   ├── api.js
│   │   ├── App.jsx
│   │   └── main.jsx
│   │
│   ├── Dockerfile
│   ├── nginx.conf
│   ├── package.json
│   └── vite.config.js
│
├── worker/
│   ├── worker.py
│   ├── Dockerfile
│   └── requirements.txt
│
├── Dockerfile.sandbox
├── docker-compose.yml
└── README.md
```

---

## 💻 Supported Languages

| Language   | Batch Execution | Interactive Mode |
| ---------- | :-------------: | :--------------: |
| Python     |        ✅        |         ✅        |
| C          |        ✅        |         ❌        |
| C++        |        ✅        |         ❌        |
| Java       |        ✅        |         ❌        |
| JavaScript |        ✅        |         ✅        |

### Execution Commands

| Language   | Source File | Compile                | Execute           |
| ---------- | ----------- | ---------------------- | ----------------- |
| Python     | `main.py`   | —                      | `python3 main.py` |
| C          | `main.c`    | `gcc main.c -o main`   | `./main`          |
| C++        | `main.cpp`  | `g++ main.cpp -o main` | `./main`          |
| Java       | `Main.java` | `javac Main.java`      | `java Main`       |
| JavaScript | `main.js`   | —                      | `node main.js`    |

---

## 🚀 Getting Started

### Prerequisites

Make sure the following are installed:

* Docker
* Docker Compose
* Git

Verify the installation:

```bash
docker --version
docker compose version
```

---

## 📥 Installation

Clone the repository:

```bash
git clone <repository-url>
cd CodeSphere
```

Create the directory used for temporary execution files:

```bash
mkdir -p temp_jobs
```

---

## 🐳 Run with Docker Compose

Build and start all services:

```bash
docker compose up --build
```

The application starts the following services:

| Service          | Port   |
| ---------------- | ------ |
| Frontend         | `3000` |
| Backend API      | `8000` |
| PostgreSQL       | `5432` |
| Redis            | `6379` |
| Worker WebSocket | `8001` |

Open the application in your browser:

```text
http://localhost:3000
```

---

## 🔄 Running in the Background

To start the platform in detached mode:

```bash
docker compose up --build -d
```

View service logs:

```bash
docker compose logs -f
```

View logs for an individual service:

```bash
docker compose logs -f backend
docker compose logs -f worker
docker compose logs -f frontend
```

Stop the application:

```bash
docker compose down
```

Stop and remove the PostgreSQL volume as well:

```bash
docker compose down -v
```

> Removing the volume deletes the persisted PostgreSQL data.

---

## 🔑 Authentication

CodeSphere uses JWT-based authentication.

### Register

```http
POST /api/register
```

Example:

```json
{
  "username": "demo",
  "password": "password123"
}
```

### Login

```http
POST /api/token
```

The login endpoint returns:

```json
{
  "access_token": "<JWT_TOKEN>",
  "token_type": "bearer"
}
```

The token is then supplied with authenticated API requests:

```http
Authorization: Bearer <JWT_TOKEN>
```

### Current User

```http
GET /api/users/me
```

---

## ▶️ Code Execution API

### Submit Code

```http
POST /api/submit
```

Example:

```json
{
  "code": "print('Hello, CodeSphere!')",
  "language": "python",
  "stdin": ""
}
```

The API returns a job object containing a unique job ID.

### Get Previous Submissions

```http
GET /api/submissions
```

---

## 📡 Real-time Job Updates

Execution status is delivered through WebSockets:

```text
/api/ws/status/{job_id}
```

Typical job states include:

```text
queued
running
completed
error
```

The frontend automatically connects to the WebSocket after submitting code and updates the terminal/output panel when the worker finishes execution.

---

## 🖥️ Interactive Sessions

Interactive execution is available for:

* Python
* JavaScript

A session is started using:

```http
POST /api/repl/start
```

Example:

```json
{
  "language": "python"
}
```

The backend returns a temporary session ID.

The frontend then connects through:

```text
/api/ws/interactive/{session_id}
```

The worker starts an interactive process inside the sandbox and forwards input/output between the container and browser terminal.

---

## 💾 File Management

Authenticated users can save source files.

### Save File

```http
POST /api/files
```

Example:

```json
{
  "filename": "hello",
  "language": "python",
  "code": "print('Hello World')"
}
```

### List Files

```http
GET /api/files
```

### Get File

```http
GET /api/files/{file_id}
```

### Delete File

```http
DELETE /api/files/{file_id}
```

---

## 🔗 Sharing Code

A saved file can be shared using:

```http
POST /api/files/share/{file_id}
```

The API generates a unique share ID and returns a shareable URL.

Shared code can subsequently be retrieved using:

```http
GET /api/files/shared/{share_id}
```

---

## 🗄️ Database

PostgreSQL is used for persistent application data.

The database contains two primary entities:

### Users

Stores:

* User ID
* Username
* Hashed password

### Code Files

Stores:

* File ID
* Filename
* Programming language
* Source code
* Owner username
* Creation timestamp
* Share ID

Database tables are initialized automatically when the FastAPI application starts.

---

## ⚡ Redis

Redis is used for asynchronous execution and real-time communication.

It handles:

* Job queue
* Job state
* User job lists
* REPL session information
* Job update Pub/Sub

The worker listens to the Redis job queue and processes submitted programs.

---

## 🔒 Code Execution Sandbox

User code is **not executed directly by the FastAPI application**.

Instead, CodeSphere uses a dedicated Docker image:

```text
code-executor-sandbox
```

The sandbox contains:

* GCC
* G++
* OpenJDK 17
* Python 3
* Node.js
* Coreutils

Programs execute as the non-root `appuser`.

Batch executions have a default timeout of **10 seconds**.

Interactive sessions also run with restricted resources and network access disabled.

---

## 🌐 Nginx Routing

The frontend container uses Nginx to serve the React application and proxy API/WebSocket requests.

```text
/api/                     → Backend
/api/ws/status/           → Backend WebSocket
/api/ws/interactive/      → Worker WebSocket
/                         → React frontend
```

This allows the browser to communicate with the entire application through the frontend's origin.

---

## ⚙️ Configuration

The project currently contains development-oriented configuration values in the source code and Docker Compose configuration.

For production deployment, replace hard-coded values with environment variables, especially:

* PostgreSQL username/password
* Database URL
* JWT secret key
* Host configuration
* Share URL
* Docker execution settings

For example:

```env
DATABASE_URL=postgresql://user:password@db/mydatabase
SECRET_KEY=your-secure-random-secret
```

**Do not use development credentials or the current JWT secret in a production deployment.**

---

## 🧪 Development Without Docker

### Frontend

```bash
cd frontend
npm install
npm run dev
```

### Backend

Create a Python virtual environment:

```bash
cd backend
python -m venv venv
```

Activate it:

**Linux/macOS**

```bash
source venv/bin/activate
```

**Windows**

```powershell
venv\Scripts\activate
```

Install dependencies:

```bash
pip install -r requirements.txt
```

Run FastAPI:

```bash
uvicorn app.main:app --reload --port 8000
```

> Redis, PostgreSQL, and the Docker execution environment still need to be available for the complete application to function.

---

## 🩺 API Documentation

Once the backend is running, FastAPI automatically provides interactive API documentation.

Swagger UI:

```text
http://localhost:8000/docs
```

ReDoc:

```text
http://localhost:8000/redoc
```

---

## 🛡️ Security Considerations

Code execution platforms must be treated as high-risk applications because users can submit arbitrary programs.

For a production deployment, consider adding:

* Strict CPU limits
* Memory limits for every batch execution
* Process limits
* Disk quotas
* Stronger container isolation
* Read-only container filesystems where possible
* Seccomp/AppArmor profiles
* Network isolation
* Job concurrency limits
* Request rate limiting
* Maximum source-code size
* Maximum stdin size
* Strong randomly generated JWT secrets
* Environment-based credentials
* HTTPS/TLS
* Database migrations
* Authentication/authorization checks for every shared resource

The current project is best suited as a **development, educational, or prototype environment** until these production hardening measures are implemented.

---

## 🧹 Cleanup

Remove running containers:

```bash
docker compose down
```

Remove containers, networks, and the database volume:

```bash
docker compose down -v
```

Remove unused Docker resources:

```bash
docker system prune
```

Use the last command carefully because it can remove unused Docker resources from other projects.

---

## 🐛 Troubleshooting

### Backend cannot connect to PostgreSQL

Check:

```bash
docker compose logs db
docker compose logs backend
```

Make sure PostgreSQL is running:

```bash
docker compose ps
```

### Worker cannot execute code

Check:

```bash
docker compose logs worker
```

Make sure the sandbox image exists:

```bash
docker images
```

The required image is:

```text
code-executor-sandbox
```

### Redis connection problems

Check:

```bash
docker compose logs redis
```

The backend and worker expect Redis to be available through the Docker Compose service name:

```text
redis:6379
```

### Frontend cannot reach the API

Make sure all services are running:

```bash
docker compose ps
```

Then check the backend directly:

```text
http://localhost:8000/
```

Expected response:

```json
{
  "message": "Code Execution API is running!"
}
```

---

## 📌 Future Improvements

Potential improvements for future versions include:

* [ ] User dashboard
* [ ] Execution history UI
* [ ] Code auto-save
* [ ] Code formatting
* [ ] More programming languages
* [ ] Test-case based execution
* [ ] Competitive-programming mode
* [ ] Multiple-file projects
* [ ] Git integration
* [ ] Collaborative editing
* [ ] Execution resource monitoring
* [ ] Improved sandbox isolation
* [ ] Production-grade authentication
* [ ] Cloud deployment
* [ ] Automated testing and CI/CD

---

## 🤝 Contributing

Contributions are welcome.

1. Fork the repository.
2. Create a feature branch.

```bash
git checkout -b feature/your-feature
```

3. Make your changes.
4. Test the application.
5. Commit your changes.

```bash
git commit -m "Add your feature"
```

6. Push the branch.

```bash
git push origin feature/your-feature
```

7. Open a Pull Request.

---

## 📄 License

Add the project's chosen license here, for example:

```text
MIT License
```

If no license has been selected yet, replace this section with the appropriate license before publishing the repository.

---

## 👨‍💻 Project

**CodeSphere** — A browser-based, containerized code execution platform built with React, FastAPI, Redis, PostgreSQL, and Docker.
