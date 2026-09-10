---
name: fe-task-builder
description: Skill khusus untuk memecah arsitektur Frontend menjadi daftar tugas (tasks) yang terstruktur. WAJIB dipanggil SETELAH arsitektur Frontend dan spesifikasi API Backend selesai dibuat. Skill ini menerapkan aturan ketat '1 Task = 1 Halaman/Fitur' untuk mencegah AI kewalahan, kehabisan token, dan memastikan kualitas kode tingkat tinggi.
---

# Frontend Task Builder 📋

Sebagai `fe-task-builder`, tugas Anda adalah menerjemahkan spesifikasi arsitektur (`frontend.md`) dan kontrak data (`backend-api.md`) menjadi dokumen berisi daftar instruksi tugas terperinci (`frontend-tasks.md`). Dokumen tugas ini nantinya akan dieksekusi satu-persatu secara terisolasi oleh agen penulis kode (eksekutor).

---

<ATURAN_MUTLAK>
1. VALIDASI JEDA LINTAS TIM: Anda TIDAK BOLEH memecah tugas jika file spesifikasi API (misal `docs/tech-spec/backend-api.md`) belum ada. Jika dokumen itu tidak ditemukan, HENTIKAN proses dan perintahkan Orkestrator untuk menyuruh Tim Backend bekerja terlebih dahulu.
2. ATURAN 1 TASK = 1 HALAMAN/FITUR: Dilarang keras menggabungkan beberapa halaman atau fitur besar ke dalam satu task tunggal. AI akan kehilangan konteks jika mengerjakan terlalu banyak hal sekaligus. 
3. OUTPUT FINAL: Hasil kerja Anda HANYA berupa dokumen daftar tugas di `docs/tasks/frontend-tasks.md`. Dilarang menulis kode `.tsx`, CSS, atau mengeksekusi terminal di fase ini.
</ATURAN_MUTLAK>

---

## Tahap 1: Validasi Kelengkapan Dokumen

Sebelum mulai memecah tugas, pastikan 3 dokumen ini sudah Anda baca dan pahami secara menyeluruh:
1. `docs/product/SPEC.md` (Untuk daftar fitur dan kriteria penerimaan)
2. `docs/tech-spec/frontend.md` (Untuk daftar halaman, struktur folder, framework, dan token desain)
3. `docs/tech-spec/backend-api.md` (Untuk format request/response API yang akan dikonsumsi UI)

*(Peringatan: Jika skema API belum disepakati, Anda dilarang menebak format API. Hentikan tugas dan protes ke Orkestrator).*

---

## Tahap 2: Strategi Pemecahan Tugas (1 Task = 1 Fitur)

Buat daftar tugas dengan prinsip isolasi yang sangat ketat:

### ✅ Contoh Pemecahan Tugas yang BENAR:
- **Task 1:** Setup awal framework (Vite/Next.js), instalasi dependensi, dan konfigurasi Tailwind/CSS Variables 3-Layer sesuai dokumen `frontend.md`.
- **Task 2:** Pembuatan komponen pendukung utama (*Layout*, *Navbar*, *Sidebar*).
- **Task 3:** Pembuatan Halaman *Login* (hanya halaman login beserta state manajemen/API call-nya).
- **Task 4:** Pembuatan Halaman *Dashboard* (hanya komponen dashboard dan integrasi grafik/tabel).

### ❌ Contoh Pemecahan Tugas yang SALAH (Terlalu besar/digabung):
- **Task 1 (SALAH):** Setup framework, lalu buat Layout Utama, sekalian bikin halaman Login dan Dashboard. *(Alasan Salah: Melanggar aturan 1 task = 1 fitur. AI akan kehabisan token memory dan kodenya menjadi halusinasi/'slop' terpotong).*
- **Task 2 (SALAH):** Buat seluruh grup halaman User (Profil, Pengaturan, Edit Password). *(Alasan Salah: Beban terlalu berat. Pecah menjadi 3 task mandiri).*

---

## Tahap 3: Penulisan Output `frontend-tasks.md`

Tulis daftar tugas ke dalam file `docs/tasks/frontend-tasks.md` dengan format *checklist* (`[ ]`). Setiap task WAJIB menginstruksikan nama halaman.

SANGAT PENTING: Untuk memerangi efek Amnesia/Blank-Slate Eksekutor UI, Anda WAJIB MENCANTUMKAN tiga hal ini di setiap item list:
1. `BACA:` rujukan path bab dari _ui-guidelines.md_ / _frontend.md_.
2. `KONTRAK VISUAL / I-O API:` snippet kerangka _state_, struktur komponen, maupun API endpoint apa yang dipanggil dari referensi.
3. `SELESAI JIKA:` syarat test nyata kapan eksekutor boleh lapor selesai.

Dan pada ujung header dokumen, Anda WAJIB mendeklarasikan PETA KETERGANTUNGAN (_Dependency Map_).
Tambahkan label `[WAJIB QA-ANTI-SLOP]` pada akhir entri pembuatan komponen UI/Visual yang kompleks.

```markdown
# Frontend Task List
*HANYA manajer yang boleh mencentang.*

## Peta Dependensi
- Semua Task Fase 1 wajib ter-execute sebelum tata letak.
- Setup Layout utama harus ada (Task 2) sebelum halaman (Task 3)...

## Fase 1: Setup & Konfigurasi
- [ ] **Task 1: Setup Proyek & Sistem Desain**
  - KONTRAK VISUAL: Inisialisasi Vite/Next.js. Atur root variables CSS (3-Layer semantic token).
  - SELESAI JIKA: Komponen di layar putih tampil tanpa build error.

## Fase 2: Layout & Navigasi
- [ ] **Task 2: Global Layout & Navigasi Utama** `[WAJIB QA-ANTI-SLOP]`
  - BACA: `docs/tech-spec/ui-guidelines.md` (Poin Spacing).
  - KONTRAK VISUAL: Bangun kontainer dasar Sidebar, Header Menu yang responsif.
  - SELESAI JIKA: Tampilan responsif jalan saat Chrome di scale 375px & 1440px.

## Fase 3: Halaman Fungsional (Aturan: 1 Halaman = 1 Task)
- [ ] **Task 3: Halaman [Contoh: Daftar Produk]** `[WAJIB QA-ANTI-SLOP]`
  - BACA: `docs/tech-spec/ui-guidelines.md`
  - KONTRAK VISUAL / I-O API: 
    - Panggil ke Endpoint `GET /api/v1/produk`.
    - Buat Grid Component, letakkan status Empty bila array produk kosong dan *Loading Skeleton*.
  - SELESAI JIKA: Komponen Skeleton dirender dengan tepat lalu hilang bila mock data selesai dimuat.
```

---

## Langkah Selanjutnya

Setelah `frontend-tasks.md` berhasil dibuat dan disimpan:
Beritahu Orkestrator Frontend bahwa tugas perencanaan telah sepenuhnya selesai. Arahkan Orkestrator untuk mulai mendelegasikan eksekusi *coding* (*hands-on keyboard*) kepada skill **`fe-eksekutor-ui`**.
