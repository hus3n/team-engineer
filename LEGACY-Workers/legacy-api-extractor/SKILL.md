---
name: legacy-api-extractor
description: Skill ekstraktor ketiga dalam pipeline Legacy Decoder. WAJIB GUNAKAN setelah legacy-mapping selesai dan pengguna mengonfirmasi "lanjut". Skill ini bertugas membaca semua file router, controller, dan decorator API untuk mengekstrak daftar endpoint lengkap beserta method, path, request body, response, dan autentikasi yang dibutuhkan. Output WAJIB ditulis ke docs/tech-spec/backend-api.md dalam format yang kompatibel dengan ekosistem team-engineer-baru.
---

# Legacy API Extractor — Rekonstruksi Kontrak API 📡

Sebagai `legacy-api-extractor`, tugas Anda adalah membaca kode router dan controller secara teliti untuk menghasilkan dokumen kontrak API (`backend-api.md`) yang akurat. Dokumen ini adalah **sumber kebenaran API** yang akan dikonsumsi oleh `fe-task-builder` dan `be-task-builder` — sehingga akurasi adalah prioritas mutlak.

---

<ATURAN_MUTLAK>
1. BACA KODE, JANGAN ASUMSI: Setiap endpoint yang Anda dokumentasikan HARUS berasal dari kode yang Anda baca dengan tool. Dilarang mengarang endpoint, method, atau response struktur yang tidak ada di kode.
2. PRIORITAS SUMBER: Jika proyek memiliki Swagger/OpenAPI (`swagger.json`, `openapi.yaml`, anotasi `@ApiProperty` NestJS, dll), baca itu PERTAMA karena lebih akurat dari router. Jika tidak ada, baca file router/controller secara langsung.
3. TANDAI KETIDAKPASTIAN: Jika ada bagian response atau request body yang tidak jelas dari kode, tandai dengan `[PERLU VERIFIKASI]` — jangan mengarang.
4. OUTPUT FINAL WAJIB: Hasil kerja Anda HANYA berupa dokumen `docs/tech-spec/backend-api.md`. Dilarang menulis kode aplikasi atau mengubah file lain.
5. FORMAT KOMPATIBEL: Format output WAJIB mengikuti standar ekosistem team-engineer-baru agar dapat langsung dikonsumsi oleh fe-task-builder dan be-task-builder.
</ATURAN_MUTLAK>

---

## Tahap 1: Penyerapan Konteks

Baca dan konfirmasi hasil dari fase sebelumnya:
1. Daftar domain/modul dari `legacy-mapping` (ini panduan folder yang akan discan)
2. Framework yang digunakan (ini menentukan pola pencarian route)
3. Sistem autentikasi yang ditemukan

**PROTOKOL BUKTI BACA:**
Tampilkan blok ini sebelum mulai ekstraksi:
```
HASIL PEMBACAAN KONTEKS
══════════════════════════════════════════
Framework Backend : [dari legacy-mapping]
Auth System       : [dari legacy-mapping]
Domain yang akan diekstrak:
  1. [domain 1]
  2. [domain 2]
  ...
Sumber utama API  : [Swagger file / Router files / Controller decorators]
══════════════════════════════════════════
```

---

## Tahap 2: Cari Sumber API yang Tersedia

Periksa secara berurutan (gunakan yang paling akurat):

### Prioritas 1 — File Swagger/OpenAPI (paling akurat)
```bash
# Cari file swagger atau openapi
Get-ChildItem -Recurse -Include "swagger.json","openapi.json","openapi.yaml","swagger.yaml"
```
Jika ditemukan → baca file tersebut sebagai sumber utama.

### Prioritas 2 — Anotasi/Decorator dalam Kode
Untuk NestJS: cari `@Controller`, `@Get`, `@Post`, `@Put`, `@Delete`, `@ApiProperty`
Untuk FastAPI: cari `@router.get`, `@router.post`, `@app.get`
Untuk Laravel: baca `routes/api.php`
Untuk Express: cari `router.get`, `router.post`, `app.get`, `app.post`

### Prioritas 3 — File Route Langsung
Baca semua file route menggunakan `glob` dan `read`:
```
routes/*.ts, routes/*.js, src/routes/**/*.ts
app/api/**/*.ts (Next.js App Router)
pages/api/**/*.ts (Next.js Pages Router)
routes/api.php (Laravel)
urls.py (Django)
```

---

## Tahap 3: Ekstraksi Endpoint per Domain

Untuk setiap domain yang ditemukan di Fase 2, ekstrak endpoint dengan detail berikut:

### Template Ekstraksi per Endpoint:
```
METHOD  PATH                    Auth?    Deskripsi
------  ----------------------  -------  ---------------------------------
GET     /api/v1/users           JWT      Ambil daftar semua user
GET     /api/v1/users/:id       JWT      Ambil detail user berdasarkan ID
POST    /api/v1/users           JWT+Admin  Buat user baru
PUT     /api/v1/users/:id       JWT      Update data user
DELETE  /api/v1/users/:id       JWT+Admin  Hapus user
```

Untuk setiap endpoint, cari juga:
- **Request Body** — baca dari DTO/schema/validator di kode
- **Response sukses** — baca dari return statement atau serializer
- **Error responses** — baca dari try/catch atau middleware error handler
- **Query params** — baca dari penggunaan `req.query` atau `@Query()`

---

## Tahap 4: Penulisan Output `backend-api.md`

Tulis hasil ekstraksi ke `docs/tech-spec/backend-api.md` dengan format berikut:

```markdown
# Backend API Specification
> Dokumen ini direkonstruksi dari kode existing oleh legacy-api-extractor.
> Tanggal rekonstruksi: [YYYY-MM-DD]
> Verifikasi dengan tim backend sebelum implementasi frontend dimulai.

## Informasi Umum
- **Base URL:** `[http://localhost:PORT/api/v1 atau sesuai kode]`
- **Autentikasi:** [JWT Bearer Token / Session Cookie / API Key]
- **Format:** JSON
- **Versi API:** [v1 / v2 / tidak ada versioning]

## Skema Autentikasi
```http
Authorization: Bearer <token>
```
[Jelaskan cara mendapatkan token — dari endpoint mana]

---

## Domain: [Nama Domain 1, misal: Auth]

### POST /api/v1/auth/login
Deskripsi: [dari komentar kode atau nama fungsi controller]

**Request Body:**
```json
{
  "email": "string",
  "password": "string"
}
```

**Response Sukses (200):**
```json
{
  "token": "string",
  "user": {
    "id": "number",
    "email": "string",
    "name": "string"
  }
}
```

**Response Error:**
- `400` — Validasi gagal (field tidak lengkap)
- `401` — Email atau password salah
- `500` — Server error

---

### POST /api/v1/auth/register
[dst...]

---

## Domain: [Nama Domain 2, misal: Users]
[dst...]

---

## Catatan Rekonstruksi
- [PERLU VERIFIKASI]: [endpoint atau field yang tidak jelas dari kode]
- [MUNGKIN DEPRECATED]: [endpoint yang terlihat tidak digunakan lagi]
- [TIDAK DITEMUKAN]: [endpoint yang mungkin ada tapi tidak terdeteksi dari scan]
```

---

## Tahap 5: Pelaporan ke Orchestrator

Setelah file berhasil ditulis, tampilkan laporan:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ FASE 3 SELESAI: legacy-api-extractor
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Output  : docs/tech-spec/backend-api.md ✓

Ringkasan ekstraksi:
  Total domain   : [N]
  Total endpoint : [N]
  Perlu verifikasi: [N endpoint ditandai PERLU VERIFIKASI]

Domain yang diekstrak:
  ✓ [Domain 1] — [N] endpoints
  ✓ [Domain 2] — [N] endpoints
  ...

PENTING: Harap review docs/tech-spec/backend-api.md dan koreksi
bagian yang ditandai [PERLU VERIFIKASI] sebelum fe-task-builder dijalankan.

Apakah ada yang perlu dikoreksi sebelum lanjut ke Fase 4 (legacy-db-reverse)?
Ketik "lanjut" untuk melanjutkan, atau berikan koreksi Anda.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

## Langkah Selanjutnya

Setelah pengguna mengonfirmasi "lanjut", sampaikan konteks (termasuk daftar domain dan ORM/database yang digunakan) kepada skill **`legacy-db-reverse`** untuk memulai Fase 4: rekonstruksi skema database.
