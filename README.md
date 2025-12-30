# WAHA Local Installation - Mac Apple Silicon

Instalasi WAHA untuk development lokal di Mac dengan Apple Silicon.

## Persyaratan

- Docker Desktop untuk Mac (sudah terinstall)
- Docker Compose v2.27.0 atau lebih baru

## Instalasi

1. **Buat file `.env`** (copy dari template di bawah atau buat manual):

```bash
cat > .env << 'EOF'
# WAHA Configuration for Local Development
WAHA_BASE_URL=http://localhost:3000
WAHA_API_KEY=your-secret-api-key-change-me-12345
WAHA_DASHBOARD_USERNAME=admin
WAHA_DASHBOARD_PASSWORD=admin
WHATSAPP_SWAGGER_USERNAME=admin
WHATSAPP_SWAGGER_PASSWORD=admin
WAHA_ENGINE=WEBJS
EOF
```

**⚠️ PENTING:** Ganti `WAHA_API_KEY` dengan key yang aman sebelum menjalankan!

2. **Pastikan Docker Desktop berjalan**

2.1 **Jalankan comand in untuk generete .env**
```
docker compose run --no-deps -v "$(pwd)":/app/env waha init-waha /app/env
```

3. **Jalankan WAHA dengan Docker Compose:**

```bash
docker compose up -d
```

3. **Cek status container:**

```bash
docker compose ps
```

4. **Lihat logs:**

```bash
docker compose logs -f
```

## Akses WAHA

Setelah container berjalan, Anda bisa mengakses:

- **Dashboard**: http://localhost:3000/dashboard
  - Username: `admin` (atau sesuai `.env`)
  - Password: `admin` (atau sesuai `.env`)

- **Swagger API**: http://localhost:3000/api-docs
  - Username: `admin` (atau sesuai `.env`)
  - Password: `admin` (atau sesuai `.env`)

- **API Base URL**: http://localhost:3000

## Konfigurasi

Edit file `.env` untuk mengubah:
- API Key
- Username/Password untuk Dashboard dan Swagger
- Engine yang digunakan (WEBJS, GOWS, NOWEB, VENOM)

## Perintah Berguna

```bash
# Stop container
docker compose down

# Restart container
docker compose restart

# Update WAHA ke versi terbaru
docker compose pull
docker compose up -d

# Lihat logs real-time
docker compose logs -f

# Lihat logs 1 jam terakhir
docker compose logs --since 1h
```

## Catatan untuk Mac Apple Silicon

✅ **WAHA sudah dikonfigurasi untuk Apple Silicon (M1/M2)!**

File `docker-compose.yml` sudah menggunakan image ARM (`devlikeapro/waha:arm`) yang kompatibel dengan Apple Silicon. 

**Pastikan:**
- Docker Desktop sudah update ke versi terbaru
- Docker Desktop sudah berjalan sebelum menjalankan `docker compose up`
- Jika ada masalah, pastikan platform emulation sudah aktif di Docker Desktop settings

## Langkah Selanjutnya - Membuat Session WhatsApp

Setelah berhasil login ke dashboard, langkah selanjutnya adalah membuat session WhatsApp:

### 1. Membuat Session Baru via Dashboard

1. **Klik tombol "Start New"** di section "Sessions" pada dashboard
2. **Isi nama session** (contoh: `my-whatsapp`)
3. **Klik "Start"** untuk membuat session
4. **Scan QR Code** yang muncul dengan aplikasi WhatsApp di HP Anda:
   - Buka WhatsApp di HP
   - Settings → Linked Devices → Link a Device
   - Scan QR code yang muncul di dashboard
5. **Tunggu hingga terhubung** - status akan berubah menjadi "CONNECTED"

### 2. Membuat Session via API

Anda juga bisa membuat session menggunakan API:

```bash
# Start new session
curl -X POST http://localhost:3000/api/sessions/start \
  -H "X-Api-Key: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"name": "my-session"}'

# Get QR code untuk scan
curl http://localhost:3000/api/sessions/my-session/auth/qr \
  -H "X-Api-Key: YOUR_API_KEY"
```

### 3. Mengirim Pesan Pertama

Setelah session terhubung, coba kirim pesan:

```bash
curl -X POST http://localhost:3000/api/sessions/my-session/sendText \
  -H "X-Api-Key: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "chatId": "6281234567890@c.us",
    "text": "Hello from WAHA! 👋"
  }'
```

**Catatan:** Ganti `YOUR_API_KEY` dengan API key dari file `.env` (variabel `WAHA_API_KEY`)

### 4. Menerima Pesan

Untuk menerima pesan, Anda bisa:
- **Via Webhook**: Konfigurasi webhook di dashboard
- **Via Polling**: Gunakan endpoint `/api/sessions/{session}/chats` untuk polling pesan baru
- **Via Events**: Subscribe ke event stream untuk real-time updates

## Sessions

Folder `sessions/` akan menyimpan data session WhatsApp Anda. Folder ini sudah di-mount ke container.

**⚠️ PENTING:** 
- Jangan hapus folder `sessions/` karena berisi data autentikasi WhatsApp Anda
- Backup folder ini jika ingin pindah ke server lain
- Setiap session memiliki nama unik dan data tersimpan di `sessions/{engine}/{session-name}/`

## Dokumentasi

Untuk informasi lebih lengkap, kunjungi: https://waha.devlike.pro/docs/

**Link Penting:**
- 📤 [Send Messages](https://waha.devlike.pro/docs/how-to/send-messages/)
- 📥 [Receive Messages](https://waha.devlike.pro/docs/how-to/receive-messages/)
- 📊 [Dashboard Guide](https://waha.devlike.pro/docs/how-to/dashboard/)
- 🖥️ [Sessions Management](https://waha.devlike.pro/docs/how-to/sessions/)
- 📚 [Swagger API Docs](http://localhost:3000/api-docs) (setelah login)


## Flow to set up
1. after done all, we can connect here "http://localhost:3000/dashboard"
2. swagger here: "http://localhost:3000"
3. go ke dashboard -> create connect, here:
```
Name: WAHA
API URL: http://localhost:3000
API Key (NEEDED): env.WAHA_API_KEY
```
4. go ke dashboard -> create connect, here:
```
Name (optional): default
after that click "create & start"
```
5. if both Workers and Sessions working, check your terminal run "docker compose logs -f". theres have gonna be QR code. scan it VIA whats app
6. swagger need apikey as authorize env.WAHA_API_KEY
7. now we can play swagger or you can open whats app chat via dashborad Sessions -> Status -> chat UI
NOTE: swagger send text:
```
curl -X 'POST' \
  'http://localhost:3000/api/sendText' \
  -H 'accept: application/json' \
  -H 'X-Api-Key: 47c211eb0bed460582a98945adb75d99' \
  -H 'Content-Type: application/json' \
  -d '{
  "chatId": "6211111111111@c.us",
  "text": "Hi there!",
  "session": "default"
}'

chatId: country_code(without "+") + number
example: `${62}${11111111111}`

session: its your dashboard session name
```

## SUM
1. create docker-compose.yml
2. docker compose run --no-deps -v "$(pwd)":/app/env waha init-waha /app/env
3. docker compose up -d && docker compose ps && docker compose logs -f
4. go here: http://localhost:3000/dashboard, login use as env
5. create worker:
```
Name: WAHA
API URL: http://localhost:3000
API Key (NEEDED): env.WAHA_API_KEY
```
6. create sessions (can do dashboard or swagger)
7. all "working". now scan qr use whatsapp on terminal who run: "docker compose logs -f"
8. we can play with swagger. go here: http://localhost:3000
9. go "📤 Chatting Chatting methods" sections and used "sendText" or api/sendText
```
curl -X 'POST' \
  'http://localhost:3000/api/sendText' \
  -H 'accept: application/json' \
  -H 'X-Api-Key: 47c211eb0bed460582a98945adb75d99' \
  -H 'Content-Type: application/json' \
  -d '{
  "chatId": "6211111111111@c.us",
  "text": "Hi there!",
  "session": "default"
}'

chatId: country_code(without "+") + number
example: `${62}${11111111111}`

session: its your dashboard session name
```