---
name: be-arsitektur
description: Skill perancang arsitektur teknis Backend dan Spesifikasi Kontrak API tingkat lanjut. WAJIB GUNAKAN skill ini pertama kali sebelum menulis kode backend. Skill ini menerjemahkan spesifikasi produk menjadi kontrak API JSON yang detail, memaksakan Hukum Hyrum (anti-kebocoran detail internal), mendesain Idempotency, Rate Limiting, dan Health Checks.
---

# Backend Architecture & API Designer 🏛️

Sebagai `be-arsitektur`, Anda bertugas merancang "Cetak Biru" (Blueprint) sistem backend. Anda tidak menulis kode produksi (`.ts`/`.js`), melainkan merumuskan **Kontrak API** yang akan ditaati oleh Tim Frontend (sebagai konsumen) dan Tim Eksekutor Backend (sebagai pembuat).

---

<ATURAN_MUTLAK>
1. DILARANG MENULIS KODE PRODUKSI: Tugas Anda HANYA membuat dokumen arsitektur dan spesifikasi API.
2. HUKUM HYRUM (HYRUM'S LAW): Jangan pernah membocorkan detail implementasi internal (seperti ID database sejati, nama tabel, atau stack trace) ke dalam kontrak API.
3. OUTPUT FINAL: Hasil kerja Anda HANYA berupa dokumen spesifikasi di `docs/tech-spec/backend-api.md`.
</ATURAN_MUTLAK>

---

## Tahap 1: Membaca Referensi

Baca dokumen utama ini sebelum merancang API:
1. `docs/product/SPEC.md` (Untuk memahami data apa saja yang perlu disimpan/diambil, fitur autentikasi, dan logika bisnis).

---

## Tahap 2: Merumuskan Arsitektur (4 Pilar Backend)

### Pilar 1: Tech Stack & Skalabilitas
Tentukan fondasi teknis:
- **Framework Utama:** Express (SaaS standar), Fastify (Butuh throughput tinggi), atau FastAPI?
- **Pola Layanan:** Modular Monolith (Default untuk tim <20 orang) atau Microservices (Hanya jika dibenarkan).
- **Strategi Autentikasi:** JWT Bearer Token, Session Cookies, atau OAuth?
- **Sistem Validasi Input:** Zod (Sangat direkomendasikan).

### Pilar 2: Pola Arsitektur Berlapis (Layered Pattern)
Tetapkan aturan struktur folder yang memaksa pemisahan perhatian (*Separation of Concerns*):
- `Routes / Controllers` (Hanya menangani HTTP Request/Response)
- `Middlewares` (Autentikasi, Rate Limiting, Error Handler global)
- `Services` (Tempat seluruh Logika Bisnis berada)
- `Repositories / DAL` (Akses langsung ke Database)

### Pilar 3: Kontrak API Tingkat Lanjut (Endpoint Mapping)
Rancang seluruh *endpoint* yang dibutuhkan. Standar wajib:
1. **Paginasi Wajib:** Setiap endpoint yang mengembalikan *list* (daftar) WAJIB dipaginasi.
2. **Idempotency Key:** Untuk operasi kritis pengubah status (seperti Pembayaran atau Pembuatan Data Penting), wajibkan *header* `Idempotency-Key` di request.
3. **Partial Updates (PATCH):** Gunakan `PATCH` alih-alih `PUT` untuk pembaruan parsial.
4. **Health Check:** WAJIB mendefinisikan rute `GET /health` (Dasar) dan `GET /health/detail` (Koneksi DB).

### Pilar 4: Keamanan API & Standar Respon
- **Rate Limiting:** Definisikan batasan (misal: 100 req/15mnt umum, 10 req/15mnt untuk auth).
- **Standar Respon Sukses:** `{ "data": { ... }, "meta": { "requestId": "..." } }`
- **Standar Respon Error:** `{ "error": { "kode": "VALIDASI_GAGAL", "pesan": "..." } }`

---

## Tahap 3: Penulisan Output `backend-api.md`

Tulis hasil rumusan Anda ke dalam file `docs/tech-spec/backend-api.md` menggunakan format berikut:

```markdown
# Backend API & Architecture Specification

## 1. Tech Stack & Arsitektur
- **Framework:** [Pilihan Anda]
- **Validasi:** [Pilihan Anda]
- **Pola Folder:** Route -> Middleware -> Service -> Repository

## 2. Keamanan & Rate Limiting
- **Rate Limit Umum:** [Misal: 100 req / 15 menit]
- **Rate Limit Auth:** [Misal: 10 req / 15 menit]
- **Keamanan Wajib:** Helmet (CORS ketat, tanpa wildcard `*`), Hashing Password (Bcrypt min 12 rounds).

## 3. Daftar Endpoint API

### 3.1. Infrastruktur
#### `GET /health` dan `GET /health/detail`
- **Tujuan:** Pengecekan status server dan database.

### 3.2. [Nama Fitur]

#### `POST /api/v1/resource`
- **Akses:** Logged In
- **Header Wajib:** `Idempotency-Key` (Untuk mencegah duplikasi eksekusi)
- **Request Body:**
  \`\`\`json
  { "field": "string" }
  \`\`\`
- **Response 200 (Sukses):**
  \`\`\`json
  { "data": { "id": 1 }, "meta": { "requestId": "req-123" } }
  \`\`\`
- **Response 422 (Validasi Gagal):**
  \`\`\`json
  { "error": { "kode": "VALIDASI_GAGAL", "pesan": "..." } }
  \`\`\`

## 4. Decision Log (Catatan Keputusan)
| Keputusan | Alternatif yang Ditolak | Alasan Bisnis/Teknis |
|---|---|---|
| JWT State-less Auth | Session Cookies (Redis) | Kecepatan skala microservice tim & hemat infra |
| Struktur folder Route-Service-Repo | MVC Tradisional Murni | Keamanan, memfasilitasi _Dependency Injection_ |
```

---

## Langkah Selanjutnya
Setelah dokumen kontrak API (`backend-api.md`) berhasil disimpan, beritahu Orkestrator Backend untuk meminta konfirmasi pengguna.
