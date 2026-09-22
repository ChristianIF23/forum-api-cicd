# Forum API — CI/CD & Security

Forum API adalah RESTful API untuk platform diskusi berbasis thread. Proyek ini menerapkan arsitektur **Clean Architecture** dengan pipeline **CI/CD otomatis** menggunakan GitHub Actions dan deployment ke VPS via SSH.

## 🚀 Live Server

| Layanan | URL |
|---|---|
| API Base URL | `https://tianpael.fun` |
| Contoh endpoint | `https://tianpael.fun/threads` |

## 🛠️ Tech Stack

- **Runtime**: Node.js
- **Framework**: Hapi.js
- **Database**: PostgreSQL 16.4
- **Web Server / Reverse Proxy**: Nginx (HTTPS + Rate Limiting)
- **Containerization**: Docker & Docker Compose
- **CI/CD**: GitHub Actions
- **SSL**: Let's Encrypt (Certbot)

## ⚙️ CI/CD Pipeline

### Continuous Integration (CI)
Berjalan otomatis pada setiap **Pull Request** ke branch `main`.

- ✅ Unit Test
- ✅ Integration Test (menggunakan PostgreSQL service container)
- ✅ Functional Test (Hapi server test)

### Continuous Deployment (CD)
Berjalan otomatis pada setiap **Push** ke branch `main`.

- Terhubung ke VPS via SSH (`appleboy/ssh-action`)
- Menarik kode terbaru (`git pull`)
- Membangun ulang dan menjalankan Docker container (`docker compose up --build`)

## 🔐 Fitur Keamanan

### HTTPS
Seluruh traffic dienkripsi menggunakan **TLS/SSL** via Let's Encrypt. HTTP secara otomatis diredirect ke HTTPS.

### Rate Limiting
Endpoint `/threads` dibatasi **90 request per menit** menggunakan Nginx (`limit_req_zone`) dan `hapi-rate-limit` sebagai perlindungan ganda.

## 📦 Cara Menjalankan Lokal

### Prasyarat
- Docker & Docker Compose

### Langkah

1. Clone repository:
   ```bash
   git clone https://github.com/ChristianIF23/forum-api-cicd.git
   cd forum-api-cicd
   ```

2. Buat file `.env` di root folder:
   ```env
   HOST=0.0.0.0
   PORT=5000
   PGHOST=postgres
   PGUSER=developer
   PGDATABASE=forumapi
   PGPASSWORD=supersecretpassword
   PGPORT=5432
   POSTGRES_PASSWORD=supersecretpassword
   POSTGRES_USER=developer
   POSTGRES_DB=forumapi
   ACCESS_TOKEN_KEY=your_access_token_key
   REFRESH_TOKEN_KEY=your_refresh_token_key
   ACCESS_TOKEN_AGE=3000
   ```

3. Jalankan dengan Docker Compose:
   ```bash
   docker compose up --build -d
   ```

4. API tersedia di `http://localhost:5000`

## 📋 Endpoint API

| Method | Endpoint | Deskripsi | Auth |
|---|---|---|---|
| POST | `/users` | Registrasi user baru | ❌ |
| POST | `/authentications` | Login | ❌ |
| PUT | `/authentications` | Refresh token | ❌ |
| DELETE | `/authentications` | Logout | ❌ |
| POST | `/threads` | Buat thread baru | ✅ |
| GET | `/threads/{threadId}` | Lihat detail thread | ❌ |
| POST | `/threads/{threadId}/comments` | Tambah komentar | ✅ |
| DELETE | `/threads/{threadId}/comments/{commentId}` | Hapus komentar | ✅ |
| POST | `/threads/{threadId}/comments/{commentId}/replies` | Tambah balasan | ✅ |
| DELETE | `/threads/{threadId}/comments/{commentId}/replies/{replyId}` | Hapus balasan | ✅ |
| PUT | `/threads/{threadId}/comments/{commentId}/likes` | Like/Unlike komentar | ✅ |
