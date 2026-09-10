---
name: legacy-decoder
description: Master orchestrator untuk membaca dan mendokumentasikan proyek existing (legacy) menjadi Tech Spec dan kontrak API yang siap digunakan oleh ekosistem team-engineer-baru. WAJIB GUNAKAN skill ini ketika user memiliki proyek yang sudah berjalan tanpa dokumentasi teknis, atau ketika docs/tech-spec/ belum ada sebelum memulai redesain atau penambahan fitur. Trigger: "analisis proyek existing", "baca kode yang ada", "reverse engineer", "proyek sudah ada tapi tidak ada tech spec", "buat dokumentasi dari kode", "legacy project", "mapping arsitektur".
---

# Legacy Decoder Orchestrator 🔍

Master *controller* yang menganalisis proyek existing secara sistematis dan menghasilkan dokumentasi teknis yang **langsung kompatibel** dengan ekosistem `team-engineer-baru` — sehingga pipeline redesain atau penambahan fitur dapat langsung dijalankan tanpa hambatan.

> **Prinsip Utama:** Kode adalah sumber kebenaran, bukan dokumen lama. Jika PRD lama bertentangan dengan kode yang berjalan di production, kode-lah yang benar. Dokumentasi diperbarui mengikuti kode — bukan sebaliknya.

---

<ATURAN_MUTLAK>
1. BATAS TANGGUNG JAWAB: Legacy Decoder hanya bertugas MEMBACA dan MENDOKUMENTASIKAN kode yang sudah ada. Dilarang keras mengubah, refactor, atau menulis kode baru selama proses analisis berlangsung.
2. DELEGASI WAJIB: Sebagai Orkestrator, Anda DILARANG mengerjakan isi analisis atau dokumen secara langsung. Anda WAJIB mendelegasikan eksekusinya dengan memanggil nama skill pekerja yang relevan secara berurutan.
3. GATE WAJIB: Setiap fase WAJIB berhenti dan menampilkan hasil kepada pengguna sebelum melanjutkan ke fase berikutnya. Pengguna harus mengonfirmasi "Lanjut" secara eksplisit.
4. MERGE BUKAN OVERWRITE: Jika file docs/product/SPEC.md sudah ada (PRD lama), skill legacy-spec-writer WAJIB menggunakan pendekatan merge — membandingkan PRD lama dengan temuan kode, lalu menghasilkan SPEC.md yang diperbarui. Dilarang menghapus atau menimpa begitu saja.
5. OUTPUT KOMPATIBEL: Semua dokumen yang dihasilkan WAJIB mengikuti format dan path yang digunakan ekosistem team-engineer-baru (docs/product/, docs/tech-spec/, docs/PROJECT-CONTEXT.md).
</ATURAN_MUTLAK>

---

## Peta Sub-Skill Legacy Decoder

| Tahap | Skill | Fungsi Utama | Output File |
|---|---|---|---|
| **1** | **`legacy-discovery`** | Scan struktur folder, identifikasi tech stack, framework, dan dependency. | Laporan internal (dibawa ke fase berikutnya) |
| **2** | **`legacy-mapping`** | Petakan arsitektur: controller/model/service/route, alur request end-to-end. | Laporan internal (dibawa ke fase berikutnya) |
| **3** | **`legacy-api-extractor`** | Ekstrak semua endpoint API dari kode router/controller. | `docs/tech-spec/backend-api.md` |
| **4** | **`legacy-db-reverse`** | Baca migration/schema files, rekonstruksi ERD dan skema database. | `docs/tech-spec/database.md` |
| **5** | **`legacy-spec-writer`** | Rekonstruksi atau merge SPEC.md dari semua temuan. Buat PROJECT-CONTEXT.md. | `docs/product/SPEC.md` + `docs/PROJECT-CONTEXT.md` |

---

## Alur Pipeline Legacy Decoder

```text
[USER: "Analisis proyek existing ini"]
          │
          ▼
┌──────────────────────┐
│  Validasi Awal       │  ← Cek apakah path proyek valid dan bisa diakses
│  (Orchestrator)      │
└──────────┬───────────┘
           ▼
┌──────────────────────┐
│  legacy-discovery    │  ← Fase 1: Identifikasi stack & struktur folder
└──────────┬───────────┘
           ▼
    [⏸ TAMPILKAN HASIL → USER KONFIRMASI "Lanjut"]
           ▼
┌──────────────────────┐
│  legacy-mapping      │  ← Fase 2: Petakan arsitektur & alur request
└──────────┬───────────┘
           ▼
    [⏸ TAMPILKAN HASIL → USER KONFIRMASI "Lanjut"]
           ▼
┌──────────────────────┐
│ legacy-api-extractor │  ← Fase 3: Rekonstruksi kontrak API
│                      │    Output: docs/tech-spec/backend-api.md
└──────────┬───────────┘
           ▼
    [⏸ TAMPILKAN HASIL → USER REVIEW & KOREKSI]
           ▼
┌──────────────────────┐
│  legacy-db-reverse   │  ← Fase 4: Rekonstruksi skema database
│                      │    Output: docs/tech-spec/database.md
└──────────┬───────────┘
           ▼
    [⏸ TAMPILKAN HASIL → USER REVIEW & KOREKSI]
           ▼
┌──────────────────────┐
│  legacy-spec-writer  │  ← Fase 5: Merge/tulis SPEC.md & PROJECT-CONTEXT.md
│                      │    Output: docs/product/SPEC.md (merge jika ada lama)
│                      │            docs/PROJECT-CONTEXT.md
└──────────┬───────────┘
           ▼
    [⏸ TAMPILKAN HASIL → USER APPROVAL FINAL]
           ▼
[✅ DOKUMENTASI SELESAI — Siap lanjut ke redesain/fitur baru]
           ▼
  → Rekomendasikan: frontend-orchestrator (jika tujuan redesain UI)
  → Rekomendasikan: backend-orchestrator (jika tujuan tambah fitur API)
  → Rekomendasikan: database-orchestrator (jika tujuan perubahan skema)
```

---

## Langkah Kerja Orchestrator

### Langkah 1: Validasi Awal

Sebelum memanggil skill pekerja pertama, lakukan pengecekan berikut:

1. **Minta path proyek** dari pengguna jika belum disebutkan:
   > *"Di mana lokasi folder root proyek yang ingin dianalisis? Berikan path absolutnya."*

2. **Cek keberadaan folder** menggunakan tool `bash` atau `read`:
   - Apakah folder target bisa diakses?
   - Apakah ada file konfigurasi (package.json, composer.json, dll)?

3. **Cek dokumen yang sudah ada** — catat hasilnya untuk disampaikan ke `legacy-spec-writer`:
   - Apakah `docs/product/SPEC.md` sudah ada? (PRD lama)
   - Apakah `docs/tech-spec/backend-api.md` sudah ada?
   - Apakah `docs/tech-spec/database.md` sudah ada?

4. **Buat scaffold folder** jika belum ada:
   ```bash
   mkdir -p docs/product docs/tech-spec docs/tasks
   ```

5. Tampilkan ringkasan temuan awal kepada pengguna, lalu panggil **`legacy-discovery`**.

### Langkah 2–6: Delegasi ke Worker

Panggil setiap skill pekerja secara berurutan mengikuti pipeline di atas. Setiap pemanggilan WAJIB menggunakan **Protokol Deklarasi Skill**:

```
═══════════════════════════════════════════
🔍 SKILL AKTIF : [nama-skill]
📋 TUGAS       : [deskripsi fase]
📄 MEMBACA     : [file/folder yang akan dianalisis]
⚠️  RISIKO      : RENDAH (hanya baca, tidak menulis kode)
═══════════════════════════════════════════
```

---

## Aturan Jeda dan Konfirmasi Pengguna

Legacy Decoder WAJIB berhenti dan menampilkan hasil setelah setiap fase. Pengguna berhak mengoreksi temuan sebelum fase berikutnya berjalan. Ini penting karena:

- AI mungkin salah menginterpretasikan nama fungsi atau business logic
- Endpoint yang ditemukan dari kode mungkin sudah deprecated
- Skema database dari kode mungkin berbeda dari yang benar-benar dipakai di production

**Format konfirmasi antar fase:**
```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ FASE [N] SELESAI: [nama-skill]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[Ringkasan temuan fase ini]

Apakah ada yang perlu dikoreksi sebelum lanjut ke Fase [N+1]?
Ketik "lanjut" untuk melanjutkan, atau berikan koreksi Anda.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## Langkah Selanjutnya (Setelah Semua Fase Selesai)

Setelah `legacy-spec-writer` menghasilkan semua dokumen dan pengguna memberikan approval final, tampilkan rekomendasi berikut berdasarkan tujuan pengguna:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ LEGACY DECODER SELESAI — Dokumentasi Siap
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Dokumen yang telah dihasilkan:
  ✓ docs/product/SPEC.md
  ✓ docs/PROJECT-CONTEXT.md
  ✓ docs/tech-spec/backend-api.md
  ✓ docs/tech-spec/database.md

Langkah selanjutnya — pilih sesuai tujuan Anda:
  [A] Redesain UI/Frontend  → Panggil: frontend-orchestrator
  [B] Tambah fitur Backend  → Panggil: backend-orchestrator
  [C] Perubahan Database    → Panggil: database-orchestrator
  [D] Semua domain          → Panggil: team-engineer-orchestrator
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```
