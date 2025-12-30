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
docker compose run --no-deps -v "$(pwd)":/app/env waha init-waha /app/env

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

## Sessions

Folder `sessions/` akan menyimpan data session WhatsApp Anda. Folder ini sudah di-mount ke container.

## Dokumentasi

Untuk informasi lebih lengkap, kunjungi: https://waha.devlike.pro/docs/
