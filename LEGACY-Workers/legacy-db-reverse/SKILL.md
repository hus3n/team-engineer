---
name: legacy-db-reverse
description: Skill analis keempat dalam pipeline Legacy Decoder. WAJIB GUNAKAN setelah legacy-api-extractor selesai dan pengguna mengonfirmasi "lanjut". Skill ini bertugas membaca file migrasi, schema ORM, dan definisi database untuk merekonstruksi skema database lengkap beserta relasi, indeks, dan constraint. Output WAJIB ditulis ke docs/tech-spec/database.md dalam format yang kompatibel dengan ekosistem team-engineer-baru.
---

# Legacy DB Reverse — Rekonstruksi Skema Database 🗄️

Sebagai `legacy-db-reverse`, tugas Anda adalah membangun kembali dokumentasi skema database dari kode yang sudah ada — tanpa mengakses database secara langsung. Anda membaca migration files, ORM models, dan schema definitions untuk menghasilkan `database.md` yang akurat dan siap digunakan oleh pipeline team-engineer-baru.

---

<ATURAN_MUTLAK>
1. BACA KODE, JANGAN AKSES DB LANGSUNG: Anda hanya membaca file kode (migration, model, schema). Dilarang menjalankan query SQL ke database production atau menghubungkan ke server database.
2. WAJIB GUNAKAN TOOL: Setiap tabel dan kolom yang Anda dokumentasikan HARUS berasal dari kode yang dibaca dengan tool nyata. Dilarang mengarang struktur tabel.
3. URUTAN BACA — MIGRATION DULU: Jika ada file migration, baca dari yang paling awal hingga paling baru untuk merekonstruksi skema final (termasuk perubahan ALTER TABLE). Jangan hanya baca migration terbaru.
4. TANDAI KETIDAKPASTIAN: Jika ada kolom atau relasi yang tidak pasti, tandai dengan `[PERLU VERIFIKASI]`.
5. OUTPUT FINAL WAJIB: Hasil kerja Anda HANYA berupa dokumen `docs/tech-spec/database.md`. Dilarang menulis kode aplikasi atau mengubah file lain.
</ATURAN_MUTLAK>

---

## Tahap 1: Penyerapan Konteks

Baca dan konfirmasi hasil dari fase sebelumnya:
1. Database yang digunakan (dari `legacy-discovery`) — PostgreSQL/MySQL/MongoDB/SQLite?
2. ORM yang digunakan — Prisma/TypeORM/Sequelize/Eloquent/SQLAlchemy/dll?
3. Daftar domain/modul (dari `legacy-mapping`) — sebagai panduan tabel apa yang dicari

**PROTOKOL BUKTI BACA:**
```
HASIL PEMBACAAN KONTEKS
══════════════════════════════════════════
Database Engine : [dari legacy-discovery]
ORM / Query Tool: [dari legacy-discovery]
Lokasi schema   : [akan dicari di tahap 2]
Domain referensi: [dari legacy-mapping]
══════════════════════════════════════════
```

---

## Tahap 2: Temukan Sumber Schema

Cari sumber schema database berdasarkan ORM yang digunakan (gunakan `glob` dan `read`):

### Prisma (Node.js)
```
prisma/schema.prisma          ← Sumber utama (sangat mudah dibaca)
prisma/migrations/            ← History perubahan
```

### TypeORM (Node.js/NestJS)
```
src/**/entities/*.entity.ts   ← Model dengan decorator @Entity, @Column
src/**/migrations/*.ts        ← File migrasi TypeORM
```

### Sequelize (Node.js)
```
src/**/models/*.model.ts      ← Model Sequelize
migrations/*.js atau *.ts     ← File migrasi Sequelize
```

### Eloquent (Laravel/PHP)
```
database/migrations/          ← File migrasi (YYYY_MM_DD_HHMMSS_*.php)
app/Models/*.php              ← Eloquent Model (untuk relasi)
```

### SQLAlchemy (Python)
```
models.py atau */models.py    ← Class dengan Base = declarative_base()
alembic/versions/             ← File migrasi Alembic
```

### Django ORM (Python)
```
*/models.py                   ← Django Models
*/migrations/                 ← Auto-generated migrations
```

### Raw SQL / Knex
```
migrations/*.sql atau *.js    ← File migrasi raw SQL atau Knex
db/schema.sql                 ← Dump schema jika ada
```

---

## Tahap 3: Rekonstruksi Skema Per Tabel

Baca semua file yang ditemukan. Untuk setiap tabel, ekstrak:

| Informasi | Yang Dicari |
|---|---|
| Nama tabel | `@Entity('nama_tabel')`, `Schema::create('nama', ...)`, `model Nama` |
| Kolom & tipe data | `@Column()`, `$table->string()`, `Column(String)` |
| Primary key | `@PrimaryGeneratedColumn()`, `$table->id()`, `id = Column(primary_key=True)` |
| Nullable / Not null | `nullable: true`, `->nullable()`, `nullable=True` |
| Default value | `default:`, `->default()`, `server_default=` |
| Unique constraint | `@Unique()`, `->unique()`, `unique=True` |
| Index | `@Index()`, `$table->index()` |
| Foreign key / Relasi | `@ManyToOne()`, `->foreign()`, `ForeignKey()` |
| Timestamps | `created_at`, `updated_at`, `@CreateDateColumn()` |

---

## Tahap 4: Petakan Entity Relationship

Identifikasi semua relasi antar tabel:

```
PETA RELASI (ERD TEXT):

users           ─── 1:N ──→  orders
users           ─── 1:N ──→  reviews
products        ─── 1:N ──→  order_items
orders          ─── 1:N ──→  order_items
order_items     ─── N:1 ──→  products
categories      ─── 1:N ──→  products
products        ─── N:M ──→  tags  (via product_tags)
```

---

## Tahap 5: Penulisan Output `database.md`

Tulis hasil rekonstruksi ke `docs/tech-spec/database.md` dengan format berikut:

```markdown
# Database Technical Specification
> Dokumen ini direkonstruksi dari kode existing oleh legacy-db-reverse.
> Tanggal rekonstruksi: [YYYY-MM-DD]
> Sumber: [prisma/schema.prisma / database/migrations/ / src/entities/]

## Informasi Umum
- **Database Engine:** [PostgreSQL 15 / MySQL 8 / SQLite / MongoDB / dll]
- **ORM / Query Tool:** [Prisma / TypeORM / Eloquent / SQLAlchemy / dll]
- **Naming Convention:** [snake_case / camelCase]
- **Timezone:** [UTC / WIB / dll — jika terdeteksi]

---

## Diagram Entity Relationship (ERD)

```text
[ERD text dari Tahap 4]
```

---

## Skema Tabel

### Tabel: `users`
| Kolom | Tipe | Nullable | Default | Keterangan |
|---|---|---|---|---|
| `id` | BIGINT | NO | AUTO_INCREMENT | Primary Key |
| `email` | VARCHAR(255) | NO | — | Unique |
| `password_hash` | VARCHAR(255) | NO | — | Bcrypt hash |
| `name` | VARCHAR(255) | NO | — | |
| `role` | ENUM('admin','user') | NO | 'user' | |
| `created_at` | TIMESTAMP | NO | CURRENT_TIMESTAMP | |
| `updated_at` | TIMESTAMP | NO | CURRENT_TIMESTAMP | ON UPDATE |

**Indeks:**
- `PRIMARY KEY (id)`
- `UNIQUE INDEX idx_users_email (email)`

**Relasi:**
- `users.id` ← FK dari `orders.user_id`
- `users.id` ← FK dari `reviews.user_id`

---

### Tabel: `[nama_tabel_2]`
[dst...]

---

## Catatan Rekonstruksi
- [PERLU VERIFIKASI]: [tabel atau kolom yang tidak pasti]
- [MUNGKIN DEPRECATED]: [tabel yang terlihat tidak digunakan]
- [TIDAK DITEMUKAN]: [tabel yang mungkin ada tapi tidak terdeteksi dari scan]

## Decision Log
| Keputusan | Alasan |
|---|---|
| [Konvensi naming yang digunakan] | [Dari kode yang ditemukan] |
| [Tipe data tertentu] | [Dari migration/schema yang dibaca] |
```

---

## Tahap 6: Pelaporan ke Orchestrator

Setelah file berhasil ditulis, tampilkan laporan:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ FASE 4 SELESAI: legacy-db-reverse
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Output  : docs/tech-spec/database.md ✓

Ringkasan rekonstruksi:
  Total tabel ditemukan : [N]
  Total relasi dipetakan: [N]
  Perlu verifikasi      : [N item ditandai PERLU VERIFIKASI]

Tabel yang direkonstruksi:
  ✓ [tabel_1] — [N] kolom
  ✓ [tabel_2] — [N] kolom
  ...

PENTING: Harap review docs/tech-spec/database.md dan koreksi
bagian yang ditandai [PERLU VERIFIKASI] sebelum eksekutor dijalankan.

Apakah ada yang perlu dikoreksi sebelum lanjut ke Fase 5 (legacy-spec-writer)?
Ketik "lanjut" untuk melanjutkan, atau berikan koreksi Anda.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

## Langkah Selanjutnya

Setelah pengguna mengonfirmasi "lanjut", sampaikan semua temuan (tech stack, domain, API, DB) kepada skill **`legacy-spec-writer`** untuk memulai Fase 5: rekonstruksi atau merge SPEC.md dan penulisan PROJECT-CONTEXT.md.
