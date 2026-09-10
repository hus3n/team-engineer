---
name: legacy-spec-writer
description: Skill penulis dokumentasi final kelima dalam pipeline Legacy Decoder. WAJIB GUNAKAN setelah legacy-db-reverse selesai dan pengguna mengonfirmasi "lanjut". Skill ini bertugas merekonstruksi atau MERGE docs/product/SPEC.md dari seluruh temuan fase sebelumnya. Jika SPEC.md lama sudah ada, skill ini WAJIB membandingkan PRD lama dengan temuan kode nyata dan menghasilkan versi SPEC.md yang diperbarui (merge) — bukan menimpa. Output: docs/product/SPEC.md (baru atau diperbarui) + docs/PROJECT-CONTEXT.md.
---

# Legacy Spec Writer — Rekonstruksi & Merge Dokumentasi Produk 📝

Sebagai `legacy-spec-writer`, Anda adalah penutup pipeline Legacy Decoder. Tugas Anda adalah menyatukan semua temuan dari 4 fase sebelumnya menjadi dokumen produk yang kohesif — `SPEC.md` dan `PROJECT-CONTEXT.md` — yang menjadi **pintu gerbang** bagi seluruh pipeline team-engineer-baru untuk bisa berjalan.

> **Prinsip Inti:** Kode adalah sumber kebenaran. Jika PRD lama menyebutkan fitur yang tidak ada di kode, atau sebaliknya — kode-lah yang benar. Tugas Anda adalah mendokumentasikan realita, bukan harapan yang tidak terimplementasi.

---

<ATURAN_MUTLAK>
1. MERGE BUKAN OVERWRITE: Jika docs/product/SPEC.md sudah ada, Anda WAJIB membacanya dengan tool terlebih dahulu, lalu membandingkan isi PRD lama vs temuan kode. Hasilkan SPEC.md yang merged — bukan menghapus begitu saja.
2. JUJUR TENTANG KESENJANGAN: Jika ada fitur di PRD lama yang tidak ditemukan di kode (mungkin belum diimplementasi atau sudah dihapus), dokumentasikan di section "Kesenjangan PRD vs Kode" — jangan dihilangkan diam-diam.
3. WAJIB GUNAKAN TOOL: Setiap klaim tentang fitur yang ada di kode HARUS berdasarkan temuan dari fase sebelumnya yang didukung tool. Dilarang mengarang fitur.
4. DUA OUTPUT WAJIB: Fase ini HARUS menghasilkan dua file: docs/product/SPEC.md DAN docs/PROJECT-CONTEXT.md. Keduanya wajib ada sebelum melaporkan selesai.
5. BAHASA BISNIS: SPEC.md ditulis dalam bahasa yang dapat dipahami oleh non-teknis (bisnis/produk). Hindari jargon teknis yang berlebihan — simpan detail teknis untuk tech-spec domain.
</ATURAN_MUTLAK>

---

## Tahap 1: Kumpulkan Semua Temuan Sebelumnya

Konfirmasi bahwa Anda memiliki informasi dari semua fase:

```
INVENTARIS KONTEKS YANG DITERIMA
══════════════════════════════════════════
Fase 1 (Discovery) :
  ✓ Tech stack: [frontend + backend + database]
  ✓ Framework : [nama framework]

Fase 2 (Mapping)   :
  ✓ Arsitektur: [pola yang digunakan]
  ✓ Domain    : [daftar modul bisnis]

Fase 3 (API)       :
  ✓ File      : docs/tech-spec/backend-api.md ✓
  ✓ Endpoint  : [N] endpoints dari [N] domain

Fase 4 (Database)  :
  ✓ File      : docs/tech-spec/database.md ✓
  ✓ Tabel     : [N] tabel direkonstruksi
══════════════════════════════════════════
```

---

## Tahap 2: Periksa SPEC.md yang Ada (Gate Merge)

Gunakan tool `read` untuk memeriksa:

```bash
# Cek apakah SPEC.md sudah ada
Test-Path "docs/product/SPEC.md"
```

### Jika SPEC.md TIDAK ADA → Mode: Buat Baru
Lanjut langsung ke Tahap 4 dengan membuat SPEC.md dari nol berdasarkan semua temuan.

### Jika SPEC.md SUDAH ADA → Mode: Merge
Baca PRD lama menggunakan tool `read`, lalu lakukan analisis perbandingan:

```
ANALISIS KESENJANGAN PRD LAMA VS KODE NYATA
══════════════════════════════════════════════════
[A] Fitur di PRD lama yang TERKONFIRMASI ada di kode:
  ✓ [fitur 1] — endpoint: POST /api/v1/auth/login
  ✓ [fitur 2] — tabel: orders
  ...

[B] Fitur di PRD lama yang TIDAK DITEMUKAN di kode:
  ✗ [fitur X] — tidak ada endpoint atau tabel terkait
  ✗ [fitur Y] — mungkin belum diimplementasi
  ...

[C] Fitur di KODE yang TIDAK ADA di PRD lama (pembaruan tidak terdokumentasi):
  + [fitur baru 1] — ditemukan endpoint: GET /api/v1/analytics
  + [fitur baru 2] — ditemukan tabel: audit_logs
  ...
══════════════════════════════════════════════════
```

Tampilkan analisis ini kepada pengguna dan minta konfirmasi:
> *"Saya menemukan [N] fitur baru di kode yang tidak ada di PRD lama, dan [N] fitur di PRD lama yang tidak ditemukan di kode. Apakah fitur yang tidak ditemukan di kode ingin tetap dicantumkan di SPEC.md sebagai 'Belum Diimplementasi', atau dihapus?"*

---

## Tahap 3: Rekonstruksi Daftar Fitur dari Kode

Berdasarkan domain yang ditemukan di `legacy-mapping` dan endpoint di `backend-api.md`, susun daftar fitur dalam bahasa bisnis:

Contoh konversi dari teknis ke bisnis:
```
TEKNIS (dari kode)          → BISNIS (untuk SPEC.md)
----------------------------   ---------------------------------
POST /api/v1/auth/login     → Pengguna dapat login dengan email & password
GET  /api/v1/products       → Pengguna dapat melihat daftar produk
POST /api/v1/orders         → Pengguna dapat membuat pesanan baru
GET  /api/v1/orders/:id     → Pengguna dapat melihat detail pesanan
PATCH /api/v1/orders/:id/status → Admin dapat mengubah status pesanan
```

---

## Tahap 4: Penulisan Output `SPEC.md`

Tulis hasil ke `docs/product/SPEC.md`:

```markdown
# Product Requirement Document (SPEC)
> [MODE: Rekonstruksi dari kode existing / Merge dari PRD lama + temuan kode]
> Tanggal: [YYYY-MM-DD]
> Dihasilkan oleh: legacy-spec-writer (Legacy Decoder Pipeline)

## 1. Ringkasan Produk
[Deskripsi singkat 2-3 kalimat tentang apa yang dilakukan aplikasi ini, berdasarkan domain dan fitur yang ditemukan di kode]

## 2. Tech Stack (Existing)
- **Frontend:** [Framework + Styling dari legacy-discovery]
- **Backend:** [Framework + Runtime dari legacy-discovery]
- **Database:** [Engine + ORM dari legacy-discovery]
- **Deployment:** [Container/CI jika ditemukan]

## 3. Daftar Fitur (Terkonfirmasi dari Kode)

### [Domain 1: misal Autentikasi]
- [ ] **Login** — Pengguna login menggunakan email dan password
- [ ] **Register** — Pengguna mendaftar akun baru
- [ ] **Logout** — Pengguna mengakhiri sesi

### [Domain 2: misal Manajemen Produk]
- [ ] **Lihat Daftar Produk** — Pengguna melihat semua produk yang tersedia
- [ ] **Lihat Detail Produk** — Pengguna melihat informasi lengkap satu produk
- [ ] **Tambah Produk (Admin)** — Admin menambahkan produk baru ke katalog
[dst...]

## 4. Role & Permission
| Role | Hak Akses |
|---|---|
| `admin` | [daftar hak akses admin dari kode] |
| `user` | [daftar hak akses user dari kode] |
[dst...]

## 5. Acceptance Criteria (Fitur Utama)
### [Fitur Utama 1]
- **Given** [kondisi awal], **When** [aksi], **Then** [hasil]

## 6. Kesenjangan PRD vs Kode
*(Section ini hanya ada jika SPEC.md lama ditemukan — Mode Merge)*

### Fitur di PRD Lama yang Tidak Ditemukan di Kode
| Fitur | Status | Keputusan |
|---|---|---|
| [fitur X] | Tidak terimplementasi | [Tetap di backlog / Hapus dari scope] |

### Fitur Baru di Kode yang Tidak Ada di PRD Lama
| Fitur | Endpoint/Tabel | Keterangan |
|---|---|---|
| [fitur baru 1] | GET /api/v1/analytics | Ditambahkan setelah PRD ditulis |

## 7. Catatan untuk Tim
- [Hal penting yang perlu diketahui tim sebelum mulai bekerja]
- [Utang teknis yang terlihat dari kode]
```

---

## Tahap 5: Penulisan Output `PROJECT-CONTEXT.md`

Buat dokumen ringkasan 1-halaman sebagai "kompas" untuk semua eksekutor:

```markdown
# PROJECT CONTEXT
> Anchor 1-halaman untuk semua eksekutor. Baca ini PERTAMA sebelum membaca tech-spec manapun.

## Identitas Proyek
- **Nama:** [Nama aplikasi dari package.json atau folder]
- **Tipe:** [Web App / API / Mobile Backend / dll]
- **Status:** Proyek existing — dokumentasi direkonstruksi [YYYY-MM-DD]

## Tech Stack Ringkas
| Layer | Teknologi |
|---|---|
| Frontend | [Framework] |
| Backend | [Framework + Runtime] |
| Database | [Engine] |
| ORM | [ORM] |
| Auth | [Metode] |

## Struktur Folder Kunci
```text
[Struktur folder penting dari legacy-mapping]
```

## Aturan Non-Fungsional (Dari Kode Existing)
- Naming convention database: [snake_case/camelCase]
- API response format: [camelCase JSON / snake_case JSON]
- Auth: semua endpoint kecuali [login/register] membutuhkan Bearer Token
- [Aturan lain yang ditemukan dari kode]

## Dokumen Referensi
- PRD lengkap: `docs/product/SPEC.md`
- Kontrak API: `docs/tech-spec/backend-api.md`
- Skema DB: `docs/tech-spec/database.md`
```

---

## Tahap 6: Pelaporan Final ke Orchestrator

Setelah kedua file berhasil ditulis, tampilkan laporan final:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ FASE 5 SELESAI: legacy-spec-writer
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Output yang dihasilkan:
  ✓ docs/product/SPEC.md         [BARU / DIPERBARUI]
  ✓ docs/PROJECT-CONTEXT.md      [BARU]

Mode yang digunakan: [Buat Baru / Merge dengan PRD lama]

[Jika Merge] Ringkasan kesenjangan:
  ✓ Fitur terkonfirmasi di kode : [N]
  + Fitur baru (tidak di PRD)   : [N]
  ✗ Fitur PRD tidak ditemukan   : [N]

Harap review semua dokumen berikut sebelum memulai pipeline:
  → docs/product/SPEC.md
  → docs/PROJECT-CONTEXT.md
  → docs/tech-spec/backend-api.md
  → docs/tech-spec/database.md

Apakah semua dokumen sudah sesuai dan disetujui?
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

## Langkah Selanjutnya

Setelah pengguna memberikan **approval final**, sampaikan kepada `legacy-decoder` (orchestrator) bahwa semua fase telah selesai. Orchestrator akan menampilkan menu pilihan pipeline selanjutnya:

- **Redesain UI/Frontend** → `frontend-orchestrator`
- **Tambah fitur Backend** → `backend-orchestrator`
- **Perubahan Database** → `database-orchestrator`
- **Semua domain** → `team-engineer-orchestrator`
