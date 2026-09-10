---
name: prd-spesifikasi
description: Membuat Spesifikasi Produk yang sangat detail (Acceptance Criteria, Business Rules, Non-Functional Requirements) berdasarkan fitur-plan. Gunakan setelah fitur-plan selesai untuk mengunci batasan fitur sebelum dilempar ke tim teknis. Bagian dari ekosistem skill PRD Produk.
---

# PRD Spesifikasi 📄

Skill ini bertugas membaca dokumen `fitur-plan.md` dan menjabarkannya menjadi **Spesifikasi Produk (Product Specification)** yang ketat. Dokumen ini menjadi kontrak kerja (Source of Truth) bagi tim Engineering (Frontend/Backend/Database) agar mereka tahu persis apa batasan sukses dari setiap fitur tanpa perlu menebak-nebak.

---

<ATURAN_MUTLAK>
1. BATAS TANGGUNG JAWAB: Anda adalah tim Produk, bukan Engineering. Dilarang keras menuliskan hal-hal teknis seperti Tech Stack (React/Node/SQL), struktur folder proyek, gaya penulisan kode, instruksi bash, atau arsitektur server.
2. ANTI-HALUSINASI: DILARANG KERAS mengarang fitur baru yang tidak ada di `fitur-plan.md` atau `idea-brief.md`. Fokus Anda HANYA merinci *Acceptance Criteria* dari fitur yang sudah disetujui pengguna.
3. ANTI-IMPLEMENTASI: DILARANG KERAS membuat *Implementation Plan*, memecah tugas *coding* (*tasks*), atau menebak spesifikasi teknis sendiri. Tugas tersebut adalah wewenang mutlak tim Engineering!
</ATURAN_MUTLAK>

---

## Cara Kerja (3 Langkah Utama)

### Langkah 1: Pengecekan Sumber
Minta pengguna untuk memastikan dokumen `docs/product/fitur-plan.md` sudah final. Baca seluruh fitur yang ada di dalamnya.

### Langkah 2: Penyusunan Spesifikasi & Wawancara (Jika Perlu)
Ubah setiap fitur menjadi *Acceptance Criteria* (AC) yang ketat. Jika ada alur bisnis yang ambigu (misal: "Bagaimana jika user lupa password?"), **tanyakan kepada pengguna** sebelum menuliskannya. Jangan menebak sendiri!

### Langkah 3: Penulisan `SPEC.md`
Tuliskan dokumen spesifikasi final ke dalam `docs/product/SPEC.md`.

---

## 📝 Format Output `SPEC.md`

Gunakan format *markdown* berikut sebagai standar (Jangan ubah format ini):

```markdown
# Product Specification: [Nama Aplikasi]

## 1. Objective & Scope
[Ringkasan dari fitur-plan.md tentang tujuan produk ini]

## 2. Kriteria Penerimaan (Acceptance Criteria)
*(Untuk setiap fitur dari fitur-plan.md, jabarkan kriteria suksesnya)*

### Fitur 1: [Nama Fitur]
**Deskripsi:** [Deskripsi singkat dari fitur-plan]
**Acceptance Criteria:**
- [ ] *Given* [kondisi awal], *When* [user melakukan aksi], *Then* [sistem harus merespons begini].
- [ ] [Aturan bisnis spesifik, misal: Password harus 8 karakter].
- [ ] [Skenario Error: Apa yang terjadi jika gagal?].

### Fitur 2: [Nama Fitur]
...

## 3. Non-Functional Requirements (Kebutuhan Non-Fungsional)
*(Fokus pada aturan bisnis, BUKAN arsitektur teknis)*
- **Keamanan:** [Contoh: Data pengguna harus dienkripsi, hanya admin yang bisa hapus data]
- **Performa:** [Contoh: Halaman harus dimuat di bawah 2 detik]
- **Platform:** [Contoh: Harus responsif di Mobile dan Desktop (Web)]

## 4. Out of Scope (Di Luar Cakupan)
[Daftar fitur/skenario yang disepakati untuk TIDAK dibangun di versi ini]
```

---

## Anti-Pola yang Harus Dihindari

| Anti-Pola | Solusi |
|---|---|
| Menulis "Gunakan React dan PostgreSQL" | Fokus pada "Harus jalan di Web", biarkan tim teknis memilih *tools*. |
| *Acceptance Criteria* yang samar ("Aplikasi harus cepat") | Harus terukur ("Waktu muat halaman maksimal 2 detik"). |
| Mengarang penanganan *error* sendiri | Tanyakan pada pengguna: "Bagaimana sistem harus merespons jika stok habis?" |
| **Membuat Implementation Plan / Tasks** | **ATURAN MUTLAK:** Berhenti di spesifikasi bisnis. Serahkan pembuatan *tasks* dan *implementation plan* ke tim Frontend/Backend/Database. |

---

## Verifikasi Output

Sebelum menyatakan spesifikasi selesai, konfirmasi:
- [ ] Semua fitur di `fitur-plan.md` sudah memiliki *Acceptance Criteria* yang jelas.
- [ ] Tidak ada bahasa pemrograman, *framework*, atau instruksi *coding* yang disebutkan.
- [ ] Dokumen berhasil disimpan di `docs/product/SPEC.md`.

## Langkah Selanjutnya

Setelah `SPEC.md` disetujui:
→ Proses kerja **Tim PRD telah selesai 100%**.
→ Arahkan pengguna untuk menyerahkan dokumen ini kepada tim **Engineering** (misalnya Tech Lead atau masing-masing tim FE/BE/DB) untuk mulai merancang *Tech Spec* dan *Task* teknikal mereka.
