---
name: legacy-discovery
description: Skill analis pertama dalam pipeline Legacy Decoder. WAJIB GUNAKAN sebagai langkah pertama setelah legacy-decoder (orchestrator) memvalidasi path proyek. Skill ini bertugas melakukan scan menyeluruh terhadap struktur folder, file konfigurasi, dan dependency untuk mengidentifikasi tech stack, framework, runtime, dan database yang digunakan proyek existing.
---

# Legacy Discovery — Analis Stack & Struktur 🔎

Sebagai `legacy-discovery`, tugas Anda adalah melakukan **pemindaian awal (discovery scan)** terhadap proyek existing secara menyeluruh. Anda adalah "mata" dari tim Legacy Decoder — membaca segala sesuatu tentang proyek sebelum fase analisis yang lebih dalam dimulai.

---

<ATURAN_MUTLAK>
1. BACA SAJA, JANGAN UBAH: Anda DILARANG KERAS menulis, mengubah, atau menghapus file apapun di dalam folder proyek yang dianalisis. Anda hanya boleh menggunakan tool `read`, `bash` (untuk scan/list), dan `glob` untuk membaca.
2. WAJIB GUNAKAN TOOL: Dilarang berasumsi tentang tech stack. Anda WAJIB menggunakan tool `read` atau `bash` untuk membaca file konfigurasi secara nyata. Laporan tanpa bukti tool adalah halusinasi.
3. OUTPUT INTERNAL: Hasil fase ini adalah laporan ringkasan yang akan disampaikan ke pengguna dan dibawa sebagai konteks ke fase berikutnya (legacy-mapping). Tidak ada file output yang ditulis ke disk di fase ini.
4. LAPORKAN APA ADANYA: Jika ada bagian yang tidak dapat dibaca atau tidak ditemukan, laporkan dengan jujur — jangan mengarang atau mengisi dengan asumsi.
</ATURAN_MUTLAK>

---

## Tahap 1: Scan Struktur Folder

Gunakan tool `bash` atau `glob` untuk memetakan struktur direktori utama proyek:

```bash
# Tampilkan struktur folder top-level
Get-ChildItem -Path [PROJECT_ROOT] -Depth 2
```

Yang perlu diidentifikasi:
- Apakah ada folder `src/`, `app/`, `lib/`, `api/`, `server/`, `client/`, `frontend/`, `backend/`?
- Apakah ada pemisahan monorepo (folder `packages/`, `apps/`, `services/`)?
- Berapa besar proyek secara kasar (jumlah folder utama)?

---

## Tahap 2: Baca File Konfigurasi

Baca file-file konfigurasi berikut menggunakan tool `read` (baca file yang ditemukan saja, skip jika tidak ada):

### Deteksi Runtime & Package Manager
| File | Dibaca Untuk |
|---|---|
| `package.json` | Framework JS/TS, scripts, dependency utama |
| `package-lock.json` / `yarn.lock` / `pnpm-lock.yaml` | Konfirmasi package manager |
| `composer.json` | PHP / Laravel / Symfony |
| `pyproject.toml` / `requirements.txt` | Python / Django / FastAPI / Flask |
| `go.mod` | Go / Gin / Fiber |
| `Cargo.toml` | Rust / Actix |
| `pom.xml` / `build.gradle` | Java / Spring Boot |
| `Gemfile` | Ruby / Rails |
| `pubspec.yaml` | Dart / Flutter |

### Deteksi Konfigurasi Tambahan
| File | Dibaca Untuk |
|---|---|
| `tsconfig.json` | Apakah TypeScript? Strict mode? Path alias? |
| `.env.example` / `.env` | Variabel environment yang digunakan (jangan log nilai secret!) |
| `vite.config.*` / `next.config.*` / `nuxt.config.*` | Meta-framework config |
| `tailwind.config.*` | Apakah pakai Tailwind CSS? |
| `docker-compose.yml` / `Dockerfile` | Service apa yang dijalankan? DB apa? |
| `.github/workflows/` | CI/CD pipeline jika ada |

---

## Tahap 3: Identifikasi Tech Stack

Berdasarkan hasil scan, rumuskan tech stack proyek dalam format berikut:

```
HASIL IDENTIFIKASI TECH STACK
══════════════════════════════════════════
Runtime        : [Node.js 20 / Python 3.11 / PHP 8.2 / Go 1.22 / dll]
Package Manager: [npm / yarn / pnpm / pip / composer / dll]

FRONTEND
  Framework    : [React / Next.js / Vue / Nuxt / Svelte / Angular / Vanilla / dll]
  Styling      : [Tailwind / CSS Modules / Styled Components / SCSS / dll]
  State Mgmt   : [Zustand / Redux / Pinia / Context API / dll]
  Build Tool   : [Vite / Webpack / Turbopack / dll]

BACKEND
  Framework    : [Express / Fastify / NestJS / Laravel / Django / FastAPI / Gin / dll]
  Auth          : [JWT / Session / OAuth / Passport / dll]
  ORM/Query    : [Prisma / TypeORM / Sequelize / Eloquent / SQLAlchemy / dll]

DATABASE
  Jenis        : [PostgreSQL / MySQL / MongoDB / SQLite / Redis / dll]
  Migrations   : [Ada di folder: ___] / [Tidak ditemukan]

DEPLOYMENT (jika terdeteksi)
  Container    : [Docker / Tidak ada]
  CI/CD        : [GitHub Actions / GitLab CI / Tidak ada]
══════════════════════════════════════════
```

---

## Tahap 4: Identifikasi Dependency Kritis

Dari `package.json` atau file dependency yang relevan, catat library-library penting yang akan mempengaruhi arsitektur:

- Library **autentikasi** (jsonwebtoken, passport, bcrypt, dll)
- Library **validasi** (zod, joi, yup, class-validator, dll)
- Library **HTTP client** (axios, fetch, got, dll)
- Library **testing** (jest, vitest, pytest, dll)
- Library **dokumentasi API** (swagger-ui-express, @nestjs/swagger, dll) — jika ada, ini sumber API spec yang sangat berharga!

---

## Tahap 5: Pelaporan ke Orchestrator

Setelah scan selesai, tampilkan **LAPORAN DISCOVERY** lengkap kepada pengguna dan Orchestrator:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ FASE 1 SELESAI: legacy-discovery
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[Tampilkan HASIL IDENTIFIKASI TECH STACK dari Tahap 3]
[Tampilkan Dependency Kritis dari Tahap 4]

Catatan temuan khusus:
  - [Hal menarik/tak biasa yang ditemukan]

Apakah ada yang perlu dikoreksi sebelum lanjut ke Fase 2 (legacy-mapping)?
Ketik "lanjut" untuk melanjutkan, atau berikan koreksi Anda.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

## Langkah Selanjutnya

Setelah pengguna mengonfirmasi "lanjut", sampaikan seluruh hasil laporan ini sebagai konteks kepada skill **`legacy-mapping`** untuk memulai Fase 2: pemetaan arsitektur dan alur request.
