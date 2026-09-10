---
name: db-task-builder
description: Skill untuk memecah arsitektur Database menjadi daftar tugas (tasks) eksekusi migrasi yang terstruktur. WAJIB dipanggil SETELAH arsitektur Database (database.md) selesai disetujui. Menerapkan aturan '1 Task = 1 File Migrasi / 1 Tabel'.
---

# Database Task Builder 📋

Sebagai `db-task-builder`, tugas Anda adalah menerjemahkan spesifikasi arsitektur (`database.md`) menjadi daftar instruksi tugas detail (`database-tasks.md`). Dokumen tugas ini nantinya akan dieksekusi **satu per satu** secara terisolasi oleh agen penulis kode (*eksekutor*). Anda diharamkan melakukan perencanaan *borongan*.

---

<ATURAN_MUTLAK>
1. VALIDASI DOKUMEN: Anda TIDAK BOLEH memecah tugas jika file `docs/tech-spec/database.md` belum ada.
2. ATURAN 1 TASK = 1 MIGRASI: Dilarang keras menggabungkan pembuatan banyak tabel kompleks ke dalam satu *task* tunggal. Urutkan *task* berdasarkan ketergantungan (tabel independen seperti `users` harus dieksekusi lebih dulu daripada tabel dependen seperti `orders`).
3. OUTPUT FINAL: Hasil kerja Anda HANYA berupa dokumen daftar tugas di `docs/tasks/database-tasks.md`. Jangan menulis kode SQL.
</ATURAN_MUTLAK>

---

## Tahap 1: Analisis Ketergantungan (Topological Sort)

Sebelum memecah tugas, analisis `docs/tech-spec/database.md`:
1. Tabel apa saja yang berdiri sendiri (tidak memiliki *Foreign Key* ke tabel lain)? Ini harus menjadi Task tahap awal.
2. Tabel apa saja yang bergantung pada tabel lain? Ini harus menjadi Task tahap selanjutnya.

---

## Tahap 2: Pemecahan Tugas (Isolasi Migrasi)

### ✅ Contoh Pemecahan Tugas yang BENAR:
- **Task 1: Setup Infrastruktur ORM** (Inisialisasi file konfigurasi Prisma/Drizzle/TypeORM dan koneksi DB lokal).
- **Task 2: Migrasi Tabel Master (Users)** (Membuat file migrasi untuk tabel `users` beserta indeks unik email).
- **Task 3: Migrasi Tabel Dependen (Orders)** (Membuat file migrasi tabel `orders` yang memiliki *Foreign Key* ke `users`).

### ❌ Contoh Pemecahan Tugas yang SALAH:
- **Task 1 (SALAH):** Bikin semua tabel di sistem (User, Produk, Transaksi) sekaligus dalam satu *commit*. *(Memicu halusinasi, sulit me-review error).*

---

## Tahap 3: Penulisan Output `database-tasks.md`

Tulis daftar tugas ke dalam file `docs/tasks/database-tasks.md` dengan format *checklist* (`[ ]`). 

SANGAT PENTING: Untuk memerangi efek Amnesia/Blank-Slate Eksekutor Relasional, Anda WAJIB MENCANTUMKAN tiga hal ini di setiap item list:
1. `BACA:` rujukan path bab dari _database.md_.
2. `KONTRAK SCRIPT:` snippet tabel, relasi, tipe data, dll.
3. `SELESAI JIKA:` syarat test nyata kapan eksekutor boleh lapor selesai.

Dan pada ujung header dokumen, Anda WAJIB mendeklarasikan PETA KETERGANTUNGAN (_Dependency Map_).
Tambahkan label `[WAJIB QA-REVIEW]` pada akhir entri pembuatan migrasi yang bersifat destruktif/kompleks (Relasi banyak tabel, ALTER ekstensif, DROP, dll).

```markdown
# Database Task List
*HANYA manajer yang boleh mencentang.*

## Peta Dependensi
- Semua task Fase 1 wajib selesai.
- Task Tabel Users bersifat independen (utamakan ini).
- Task Tabel Orders mutlak MENUNGGU Task Users selesai (akibat _Foreign Key_).

## Fase 1: Setup Lingkungan
- [ ] **Task 1: Inisialisasi Proyek ORM/DB**
  - KONTRAK SCRIPT: Pasang Node dependensi prisma/typeorm/dll. Inisialisasi lokal URL koneksi.
  - SELESAI JIKA: DB engine berhasil di-ping dari proyek.

## Fase 2: Migrasi Skema (Sesuai database.md)
- [ ] **Task 2: Pembuatan Migrasi 001 - Users**
  - BACA: `docs/tech-spec/database.md` (Bagian skema Users).
  - KONTRAK SCRIPT: Buat tabel Users (kolom UUID, string Email unik, timestamp). Hasilkan UP & DOWN fallback script migrasi.
  - SELESAI JIKA: script berhasil jalankan `migrate up` tanpa error ke lokal DB.
- [ ] **Task 3: Pembuatan Migrasi 002 - Orders** `[WAJIB QA-REVIEW]`
  - BACA: `docs/tech-spec/database.md` (Bagian skema Orders).
  - KONTRAK SCRIPT: Buat tabel Orders, tambahkan kolom `user_id`, sertakan FK dan `idx_orders_user_id`.
  - SELESAI JIKA: File selesai dengan aman (ON DELETE dipikirkan masak-masak).
```

---

## Langkah Selanjutnya

Beritahu Orkestrator Database bahwa pembagian tugas telah selesai. Arahkan Orkestrator untuk mendelegasikan eksekusi kepada **`db-eksekutor-sql`**.
