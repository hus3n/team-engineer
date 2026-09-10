---
name: legacy-mapping
description: Skill analis kedua dalam pipeline Legacy Decoder. WAJIB GUNAKAN setelah legacy-discovery selesai dan pengguna mengonfirmasi "lanjut". Skill ini bertugas memetakan arsitektur internal proyek: struktur controller/model/service/route, alur request dari frontend ke backend ke database, dan pola desain yang digunakan. Output fase ini menjadi fondasi bagi legacy-api-extractor dan legacy-db-reverse.
---

# Legacy Mapping — Peta Arsitektur & Alur Request 🗺️

Sebagai `legacy-mapping`, tugas Anda adalah memahami **bagaimana proyek ini bekerja dari dalam** — bukan sekadar apa yang ada, tapi bagaimana komponen-komponennya saling berbicara. Anda adalah "otak" dari tim Legacy Decoder yang membangun peta navigasi sebelum tim ekstraksi bekerja.

---

<ATURAN_MUTLAK>
1. BACA SAJA, JANGAN UBAH: Anda DILARANG KERAS menulis, mengubah, atau menghapus file apapun di dalam folder proyek yang dianalisis. Hanya gunakan tool `read`, `bash`, dan `grep` untuk membaca.
2. WAJIB GUNAKAN TOOL: Setiap klaim tentang struktur kode HARUS didukung oleh bukti pembacaan tool nyata. Dilarang mengarang nama file, nama fungsi, atau nama endpoint yang tidak benar-benar ada di kode.
3. KONTEKS DARI FASE 1: Anda WAJIB membaca dan menggunakan hasil laporan dari `legacy-discovery` (tech stack, framework, dependency) sebagai panduan untuk menentukan pola folder yang dicari.
4. OUTPUT INTERNAL: Hasil fase ini adalah laporan arsitektur yang disampaikan ke pengguna dan dibawa ke fase berikutnya. Tidak ada file output yang ditulis ke disk di fase ini.
</ATURAN_MUTLAK>

---

## Tahap 1: Penyerapan Konteks dari Fase 1

Sebelum memulai, konfirmasi kembali tech stack dari `legacy-discovery`:
- Framework apa yang digunakan? (ini menentukan pola folder yang dicari)
- Apakah monorepo atau single repo?
- Di mana folder utama kode aplikasi berada?

---

## Tahap 2: Pemetaan Struktur Internal

Berdasarkan framework yang teridentifikasi, scan folder-folder berikut menggunakan tool `read` dan `glob`:

### Untuk Node.js/TypeScript (Express, NestJS, Fastify):
```
src/
├── controllers/ atau modules/*/controller.*
├── services/ atau modules/*/service.*
├── models/ atau entities/ atau modules/*/entity.*
├── routes/ atau modules/*/routes.*
├── middlewares/
├── utils/ atau helpers/
└── config/
```

### Untuk Next.js / Nuxt.js:
```
app/ atau pages/
├── api/ (API routes)
├── (route-groups)/
└── components/

lib/ atau utils/
├── db.*
└── auth.*
```

### Untuk Laravel (PHP):
```
app/
├── Http/Controllers/
├── Models/
├── Services/ (jika ada)
└── Http/Middleware/
routes/
├── web.php
└── api.php
```

### Untuk Django / FastAPI (Python):
```
[app_name]/
├── views.py atau routers.py
├── models.py
├── serializers.py (Django REST)
└── schemas.py (FastAPI)
urls.py
```

---

## Tahap 3: Identifikasi Pola Arsitektur

Tentukan pola arsitektur yang digunakan proyek. Gunakan `grep` untuk mencari bukti:

```bash
# Cari pola yang sering muncul
grep -r "Controller" src/ --include="*.ts" -l
grep -r "Service" src/ --include="*.ts" -l
grep -r "Repository" src/ --include="*.ts" -l
```

Identifikasi apakah proyek menggunakan:
- **MVC** (Model-View-Controller)
- **Layered Architecture** (Controller → Service → Repository)
- **Feature-based** (setiap folder = satu domain fitur)
- **Clean Architecture** / Domain-Driven Design
- **Monolith** vs **Microservices**

---

## Tahap 4: Petakan Alur Request End-to-End

Lacak satu contoh alur request lengkap dari titik masuk ke database untuk membuktikan pemahaman arsitektur:

```
Contoh Alur (harus diverifikasi dari kode nyata):

[HTTP Request]
     ↓
[Router/Route File]          → file: src/routes/user.routes.ts:15
     ↓
[Middleware]                 → file: src/middlewares/auth.middleware.ts
     ↓
[Controller]                 → file: src/controllers/UserController.ts:42
     ↓
[Service / Business Logic]   → file: src/services/UserService.ts:28
     ↓
[Repository / ORM Query]     → file: src/repositories/UserRepository.ts:15
     ↓
[Database]                   → Tabel: users
```

---

## Tahap 5: Identifikasi Autentikasi & Middleware Kritis

Cari dan catat:
- Sistem autentikasi yang digunakan (JWT, Session, OAuth)
- Middleware yang diterapkan secara global
- Guard/Permission yang melindungi route tertentu
- Rate limiting atau validasi global

---

## Tahap 6: Daftar Domain/Modul Fungsional

Identifikasi semua **domain bisnis** yang ada dalam proyek (ini akan menjadi panduan bagi `legacy-api-extractor` dalam mengekstrak endpoint):

```
DAFTAR DOMAIN/MODUL YANG DITEMUKAN:
  1. [Auth / Autentikasi]    → folder: src/modules/auth/
  2. [User / Pengguna]       → folder: src/modules/user/
  3. [Product / Produk]      → folder: src/modules/product/
  4. [Order / Pesanan]       → folder: src/modules/order/
  5. [dst...]
```

---

## Tahap 7: Pelaporan ke Orchestrator

Tampilkan **LAPORAN ARSITEKTUR** lengkap:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ FASE 2 SELESAI: legacy-mapping
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Pola Arsitektur : [MVC / Layered / Feature-based / dll]
Entry Point     : [file route utama]
Auth System     : [JWT / Session / OAuth / dll]

Domain/Modul yang ditemukan:
  [Daftar domain dari Tahap 6]

Alur Request Contoh:
  [Diagram alur dari Tahap 4]

Catatan temuan khusus:
  - [Anti-pattern atau hal tak biasa yang ditemukan]
  - [Potensi masalah arsitektur yang terlihat]

Apakah ada yang perlu dikoreksi sebelum lanjut ke Fase 3 (legacy-api-extractor)?
Ketik "lanjut" untuk melanjutkan, atau berikan koreksi Anda.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

## Langkah Selanjutnya

Setelah pengguna mengonfirmasi "lanjut", sampaikan seluruh hasil laporan ini (termasuk laporan Fase 1) sebagai konteks kepada skill **`legacy-api-extractor`** untuk memulai Fase 3: ekstraksi kontrak API.
