---
name: team-engineer-orchestrator
description: Master orchestrator untuk seluruh tim engineer — menggabungkan PRD, frontend, backend, database, dan deployment menjadi satu alur kerja terintegrasi. Gunakan skill ini SELALU dan PERTAMA KALI ketika user ingin memulai proyek baru, membuat PRD, merencanakan fitur, membangun sistem, atau ketika menyebut "buat aplikasi", "rencanakan sistem", "mulai proyek", "scaffold", "team engineer", "buat PRD", "desain arsitektur", atau "implementasikan fitur". Skill ini memastikan setiap task memanggil skill yang tepat secara eksplisit sebelum dikerjakan — PRD menjadi fondasi, diikuti backend/database/frontend/deployment secara berurutan.
---

# Team Engineer Orchestrator 🏗️

Master *controller* yang menyatukan **PRD → Database → Backend → Frontend → Deployment** menjadi satu alur kerja *engineering* yang kohesif, terisolasi, dan terstruktur.

> **Prinsip Utama:** PRD adalah fondasi. Tidak ada kode yang ditulis sebelum ada rencana yang disetujui. Setiap task memanggil skill yang sesuai secara **eksplisit** sebelum mulai dikerjakan.

---

<ATURAN_MUTLAK>
1. WORKFLOW WAJIB: Tidak ada kode ditulis sebelum pipeline ini selesai dan disetujui per fase: PRD (`SPEC.md`) → Tech-Spec per domain (`docs/tech-spec/`) → Tasks per domain (`docs/tasks/`) → Coding → Deployment.
2. CHAIN OF THOUGHT: Sebelum mengerjakan task, AI harus mendeklarasikan skill yang dipakai dan ID task dari `[domain]-tasks.md` menggunakan **Protokol Deklarasi Skill**.
3. NO SKIP: Dilarang keras melewati fase. Deployment TIDAK BOLEH dimulai sebelum coding semua domain selesai. Coding TIDAK BOLEH dimulai sebelum Tasks per domain disetujui.
4. SATU TASK PER EKSEKUTOR: Anda DILARANG memerintahkan eksekutor untuk mengerjakan lebih dari 1 task sekaligus. Sistem borongan diharamkan di semua level.
5. DILARANG MEMBUAT PLAN SENDIRI SAAT EKSEKUSI: Ketika fase eksekusi koding dimulai, eksekutor DILARANG membuat *implementation plan*, *task list*, atau susunan rencana kerja baru secara mandiri. Semua rencana kerja sudah tersedia di dokumen `[domain]-tasks.md` yang telah disetujui pengguna. Eksekutor mengambil satu task yang belum selesai dan menjalankannya. Improvisasi rencana di luar dokumen resmi adalah bentuk halusinasi.
6. HAK AKSES CHECKLIST MONOPOLI: HANYA Manajer Lapangan (`parallel-executor`) yang diizinkan memodifikasi (centang `[/]` atau `[x]`) dokumen `[domain]-tasks.md`. Agen Eksekutor (kuli kode) DILARANG KERAS mengedit file checklist tersebut untuk menghindari kehilangan data (write-conflict). Eksekutor hanya bertugas menulis kode dan melapor "Selesai/Gagal".
7. KEWAJIBAN MEMBACA DENGAN TOOL (ANTI BLANK SLATE): Agen Eksekutor setiap kali di-spawn tidak memiliki memori historis percakapan sebelumnya. SEBELUM MENULIS KODE APA PUN, eksekutor WAJIB menggunakan Tool `read` atau terminal `bash` untuk membaca dokumen Tech-Spec secara mutlak. Membaca spesifikasi tanpa menggunakan tool adalah halusinasi!
</ATURAN_MUTLAK>

---

## Peta Ekosistem Skill (Team Engineer Baru)

```text
┌──────────────────────────────────────────────────────────────┐
│               TEAM ENGINEER ORCHESTRATOR                     │
│              (Skill ini — Titik Masuk Utama)                 │
└──────────────────────┬───────────────────────────────────────┘
                       │
          ┌────────────▼────────────┐
          │     PRD ORCHESTRATOR    │  ← Fondasi semua keputusan
          │   (prd-orchestrator)    │
          └─┬──────┬──────┬────────┘
            │      │      │
   ┌─────────▼──┐ ┌─▼──────────┐ ┌▼──────────────┐
   │  DATABASE  │ │  BACKEND   │ │   FRONTEND    │
   │Orchestrator│ │Orchestrator│ │  Orchestrator │
   │(database-  │ │(backend-   │ │(frontend-     │
   │orchestr.)  │ │orchestr.)  │ │orchestr.)     │
   └────────────┘ └────────────┘ └───────────────┘
            │              │               │
            └──────┬───────┴──────┬────────┘
                   ▼              ▼
          ┌─────────────────────────────┐
          │  DEPLOYMENT ORCHESTRATOR    │
          │  (deployment-orchestrator)  │
          └──────────────┬──────────────┘
                         ▼
          ┌─────────────────────────────┐
          │  QA STANDAR KUALITAS  🛡️    │
          │  (qa-standar-kualitas)      │
          │  [Guardrail lintas tim]     │
          └─────────────────────────────┘
```

### Direktori Skill (di dalam `team-engineer-baru/`)

| Skill | Path Folder | Fungsi |
|---|---|---|
| `prd-orchestrator` | `PRD-Workers/prd-orchestrator/` | Pipeline PRD lengkap |
| `frontend-orchestrator` | `frontend-orchestrator/` | Koordinator Tim Frontend |
| `fe-ui-designer` | `FE-Workers/fe-ui-designer/` | Wawancara UI, Warna & Layout |
| `fe-arsitektur` | `FE-Workers/fe-arsitektur/` | Tech stack & Struktur Folder FE |
| `fe-task-builder` | `FE-Workers/fe-task-builder/` | Pecah fitur FE menjadi Tasks |
| `fe-eksekutor-ui` | `FE-Workers/fe-eksekutor-ui/` | Kuli Koding Komponen UI |
| `fe-animator` | `FE-Workers/fe-animator/` | Polishing Animasi & Interaksi |
| `backend-orchestrator` | `backend-orchestrator/` | Koordinator Tim Backend |
| `be-arsitektur` | `BE-Workers/be-arsitektur/` | Kontrak API & Keamanan Server |
| `be-task-builder` | `BE-Workers/be-task-builder/` | Pecah API menjadi Tasks |
| `be-eksekutor-api` | `BE-Workers/be-eksekutor-api/` | Kuli Koding API & Service |
| `database-orchestrator` | `database-orchestrator/` | Koordinator Tim Database |
| `db-arsitektur` | `DB-Workers/db-arsitektur/` | Desain Skema, ERD & Indeks |
| `db-task-builder` | `DB-Workers/db-task-builder/` | Pecah Skema menjadi Migrasi |
| `db-eksekutor-sql` | `DB-Workers/db-eksekutor-sql/` | Kuli Koding SQL/ORM Migrasi |
| `deployment-orchestrator` | `deployment-orchestrator/` | Koordinator Tim Deployment |
| `dp-arsitektur` | `DP-Workers/dp-arsitektur/` | 5 Wawancara Infra & Tech-Spec |
| `dp-task-builder` | `DP-Workers/dp-task-builder/` | Pecah rilis menjadi Tasks CI/CD |
| `dp-eksekutor-infra` | `DP-Workers/dp-eksekutor-infra/` | Kuli Koding Docker/Nginx/CI |
| `qa-standar-kualitas` | `qa-standar-kualitas/` | 🛡️ Polisi Kualitas Lintas Tim |
| `parallel-executor` | `parallel-executor/` | ⚡ Manajer Sub-Agent Eksekusi (Paralel & Sekuensial) |

---

## Alur Kerja Master (5 Fase)

### Fase 1: PRD (Selalu Pertama Tanpa Pengecualian)

```text
SEMUA proyek dimulai di sini.

1. Panggil: prd-orchestrator
2. Output: docs/product/SPEC.md (disetujui pengguna)
3. Gate: Jangan lanjut sebelum SPEC.md ada.
```

### Fase 2: Coding Domain (Berurutan DB → BE → FE)

Urutan ini bukan pilihan, ini **wajib**. Database mendefinisikan kontrak data, Backend membaca kontrak itu, Frontend membaca kontrak API Backend.

```text
Step A: Panggil → database-orchestrator
  → Output: docs/tech-spec/database.md & docs/tasks/database-tasks.md
  → Gate: Disetujui pengguna dulu

Step B: Panggil → backend-orchestrator
  → Membaca: SPEC.md & database.md
  → Output: docs/tech-spec/backend-api.md & docs/tasks/backend-tasks.md
  → Gate: Disetujui pengguna dulu

Step C: Panggil → frontend-orchestrator
  → Membaca: SPEC.md & backend-api.md
  → Output: docs/tech-spec/ui-guidelines.md, frontend.md, frontend-tasks.md
  → Gate: Disetujui pengguna dulu
```

### Fase 3: Eksekusi Koding (Otomatis dengan Task Executor Manager)

Setelah semua dokumen `[domain]-tasks.md` disetujui, panggil manajer eksekusi tunggal:

```text
Panggil → parallel-executor (Task Executor Manager)
```

Skill `parallel-executor` kini berfungsi sebagai manajer lapangan untuk segala situasi. Ia akan menanyakan mode apa yang ingin digunakan:

**Mode A — Sekuensial (Aman & Stabil - Default):**
- Ia akan menganalisis semua task dan menyusunnya dalam urutan topologis.
- Mengerjakan task satu per satu secara otomatis.
- Memanggil eksekutor spesifik (`db-eksekutor-sql`, dll) untuk setiap task.
- Sangat aman untuk menghindari konflik modifikasi file.

**Mode B — Paralel (Lebih cepat, gunakan jika task independen):**
- Ia akan menganalisis dependensi dan mengelompokkan task ke dalam "Gelombang".
- Mengerjakan task dalam gelombang yang sama secara bersamaan (paralel).
- Sangat cepat, namun hanya gunakan jika task-task tersebut tidak memodifikasi file yang sama atau saling bergantung.

⚠️ **Catatan Kualitas:** Terlepas dari mode yang dipilih, pastikan `qa-standar-kualitas` dipanggil SEBELUM atau SESUDAH task berisiko tinggi.

### Fase 4: Deployment

```text
Setelah semua coding task [x] selesai:

1. Panggil → deployment-orchestrator
  → Output: docs/tech-spec/deployment.md & docs/tasks/deployment-tasks.md
  → Gate: Disetujui pengguna dulu

2. Panggil → dp-eksekutor-infra (1 task per panggil)
  → Staging first → Production
```

---

## Protokol Deklarasi Skill (WAJIB Tiap Eksekusi)

Setiap kali Anda mendelegasikan tugas, AI eksekutor WAJIB mendeklarasikan:

```
═══════════════════════════════════════════
🔧 SKILL AKTIF : [nama-skill]
📋 TASK        : [ID dan deskripsi task]
📄 MEMBACA     : [file tech-spec yang dibaca]
⚠️  RISIKO      : [RENDAH | SEDANG | TINGGI]
═══════════════════════════════════════════
```

**Contoh nyata:**
```
═══════════════════════════════════════════
🔧 SKILL AKTIF : db-eksekutor-sql
📋 TASK        : Task 2 - Migrasi Tabel Users
📄 MEMBACA     : docs/tech-spec/database.md → §3.1
⚠️  RISIKO      : TINGGI — aktifkan qa-standar-kualitas
═══════════════════════════════════════════
```

---

## Panduan per Skenario

### Skenario 1: Proyek Baru dari Nol
```text
1. prd-orchestrator → SPEC.md (approval)
2. database-orchestrator → database.md + database-tasks.md (approval)
3. backend-orchestrator → backend-api.md + backend-tasks.md (approval)
4. frontend-orchestrator → ui-guidelines.md + frontend.md + frontend-tasks.md (approval)
5. parallel-executor → pilih Mode Sekuensial (aman) atau Paralel (cepat)
     → Ia mendistribusikan semua task DB, BE, FE secara otomatis
6. deployment-orchestrator → deployment.md + deployment-tasks.md (approval)
7. dp-eksekutor-infra (staging → production)
```

### Skenario 2: Fitur Baru pada Proyek Berjalan
```text
1. prd-orchestrator → Mini-spec (approval)
2. Update Tech-Spec domain yang terpengaruh
3. Tambah tasks baru ke domain-tasks.md yang relevan
4. Eksekusi menggunakan eksekutor domain yang sesuai
5. Jika ada perubahan infra → deployment-orchestrator
```

### Skenario 3: Debugging / Optimasi
```text
- Query lambat?          → db-arsitektur (baca strategi indeks)
- API error/security?    → be-eksekutor-api + qa-standar-kualitas
- UI bermasalah?         → fe-eksekutor-ui
- Kualitas kode meragukan? → qa-standar-kualitas (code review)
```

---

## Aturan Kontrak Antar Domain

### Database → Backend
```text
Database mendefinisikan:  Nama tabel (snake_case), tipe data, relasi, FK.
Backend WAJIB mengikuti: Nama field dari skema (jangan rename di ORM).
Backend DILARANG:        Bypass repository layer, akses DB langsung di Controller.
```

### Backend → Frontend
```text
Backend mendefinisikan:  Format JSON response (camelCase), error codes, endpoint URL.
Frontend WAJIB mengikuti: Menangani SEMUA error code dari backend.
Frontend DILARANG:       Hard-code URL endpoint, bypass error handling.
```

### QA → Semua Eksekutor
```text
qa-standar-kualitas berhak mengeluarkan LAPORAN MERAH 🔴 yang
membekukan eksekutor dari melanjutkan ke task berikutnya hingga
semua temuan diperbaiki.
```

---

## Checklist Master Team Engineer

### PRD Gate (Sebelum Ada Kode)
- [ ] `docs/product/SPEC.md` sudah ada dan disetujui pengguna
- [ ] Tech stack sudah dipilih di PRD

### Database Gate
- [ ] `docs/tech-spec/database.md` disetujui
- [ ] `docs/tasks/database-tasks.md` disetujui
- [ ] Semua file migrasi UP & DOWN tersedia

### Backend Gate
- [ ] `docs/tech-spec/backend-api.md` disetujui
- [ ] `docs/tasks/backend-tasks.md` disetujui
- [ ] Health check `GET /health` sudah ada
- [ ] Tidak ada secret yang di-hardcode

### Frontend Gate
- [ ] `docs/tech-spec/ui-guidelines.md` disetujui
- [ ] `docs/tech-spec/frontend.md` disetujui
- [ ] `docs/tasks/frontend-tasks.md` disetujui
- [ ] Mengikuti kontrak API Backend

### Deployment Gate
- [ ] `docs/tech-spec/deployment.md` disetujui
- [ ] `docs/tasks/deployment-tasks.md` disetujui
- [ ] Staging/dry-run berhasil sebelum production
- [ ] Rollback procedure tersedia

---

## Bendera Merah (Hentikan dan Perbaiki)

- **[KRITIS] Membuat Plan Sendiri Saat Eksekusi:** Eksekutor menyusun *task list* atau *implementation plan* baru secara mandiri ketika sudah ada dokumen `[domain]-tasks.md` yang valid. Eksekutor hanya boleh membaca dokumen tasks yang telah disetujui pengguna dan mengikutinya kata per kata — bukan berimprovisasi.
- **[KRITIS] Terburu-buru Tanpa Review:** AI melewatkan tahap review pengguna di akhir setiap fase (Tech-Spec, Tasks, atau setelah 1 task eksekusi) dengan alasan "efisiensi".
- Menulis kode sebelum `SPEC.md` ada dan disetujui.
- Membiarkan eksekutor mengerjakan lebih dari 1 task sekaligus (sistem borongan).
- Frontend mengakses database secara langsung (bypass Backend).
- Backend mengabaikan skema yang sudah dirancang oleh `db-arsitektur`.
- Mengubah skema database tanpa file migrasi UP & DOWN.
- Deploy ke production sebelum staging berhasil diverifikasi.
- Hardcode secrets/credentials di dalam file konfigurasi manapun.
- Memanggil eksekutor tanpa lebih dulu membaca tech-spec yang relevan.

---

## Penamaan & Lokasi File Output (Standar Proyek)

```text
project-root/
├── docs/
│   ├── product/
│   │   └── SPEC.md                    ← prd-orchestrator
│   └── tech-spec/
│       ├── database.md                ← db-arsitektur    [WAJIB]
│       ├── backend-api.md             ← be-arsitektur    [WAJIB]
│       ├── ui-guidelines.md           ← fe-ui-designer   [WAJIB]
│       ├── frontend.md                ← fe-arsitektur    [WAJIB]
│       └── deployment.md              ← dp-arsitektur    [WAJIB]
├── docs/tasks/
│   ├── database-tasks.md              ← db-task-builder  [WAJIB]
│   ├── backend-tasks.md               ← be-task-builder  [WAJIB]
│   ├── frontend-tasks.md              ← fe-task-builder  [WAJIB]
│   └── deployment-tasks.md            ← dp-task-builder  [WAJIB]
├── migrations/
│   └── YYYYMMDD_NNN_deskripsi.sql     ← db-eksekutor-sql
├── src/
│   ├── modules/[domain]/              ← be-eksekutor-api
│   └── components/[nama]/             ← fe-eksekutor-ui
├── .github/workflows/
│   └── deploy.yml                     ← dp-eksekutor-infra
├── Dockerfile                         ← dp-eksekutor-infra
└── nginx.conf                         ← dp-eksekutor-infra
```
