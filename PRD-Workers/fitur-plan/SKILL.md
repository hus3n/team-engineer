---
name: fitur-plan
description: Skill untuk membedah dan merencanakan daftar fitur aplikasi. Gunakan skill ini setelah fase prd-ideasi selesai atau ketika pengguna meminta untuk membuat PRD fitur baru/aplikasi dari nol. Skill ini WAJIB mewawancarai pengguna untuk mendetailkan fitur dan menghasilkan dokumen fitur-plan.md.
---

# Fitur Plan (Feature Planner) 📋

Skill khusus dalam tim PRD (Product) yang bertugas menyusun, membedah, dan merencanakan fitur-fitur yang akan ada pada suatu aplikasi.

---

## 🎯 Kapan Menggunakan Skill Ini

Gunakan skill ini JIKA:
1. Fase 1 (`prd-ideasi`) sudah selesai dan dokumen *Idea Brief* sudah ada.
2. Pengguna meminta untuk membuat PRD untuk aplikasi dari nol.
3. Pengguna ingin melakukan penambahan fitur tertentu pada aplikasi yang sudah ada.
4. Pengguna menyebut "bedah fitur", "rencanakan fitur", atau "fitur-plan".

---

## 📋 Instruksi Kerja Utama

Jika skill ini dipicu, Anda WAJIB mengikuti urutan langkah berikut TANPA terlewat:

### Langkah 1: Pengecekan Dokumen
Selalu mulai dengan mengecek keberadaan dokumen dari `prd-ideasi` (biasanya berada di `docs/product/idea-brief.md` atau direktori serupa). 
- Jika dokumen belum ada dan pengguna ingin memulai dari nol, mintalah pengguna untuk menjelaskan ide mentahnya terlebih dahulu.
- Jika dokumen sudah ada, baca dan pelajari dokumen tersebut untuk memahami konteks aplikasi.

### Langkah 2: Sesi Wawancara (Interview)
Lakukan wawancara (tanya jawab) interaktif dengan pengguna untuk membedah fitur-fitur yang akan dibangun.
Tanyakan hal-hal seperti:
- Fitur utama apa saja yang wajib ada (Must-have)?
- Apa tujuan spesifik dari masing-masing fitur tersebut?
- Siapa target pengguna dari fitur ini?
- Apakah ada *edge case* atau aturan bisnis khusus yang harus diperhatikan?

*Catatan untuk AI: Ajukan pertanyaan secara bertahap dan interaktif. Jangan bombardir pengguna dengan banyak pertanyaan sekaligus, melainkan bedah fitur satu per satu jika fiturnya kompleks.*

### Langkah 3: Penulisan Dokumen `fitur-plan.md`
Setelah wawancara dirasa cukup dan pengguna menyetujui daftar fiturnya, rangkum SEMUA hasil wawancara ke dalam sebuah dokumen bernama `docs/product/fitur-plan.md`.

Dokumen tersebut WAJIB berisi:
- **Konteks Produk:** Ringkasan singkat aplikasi atau fitur yang dibangun.
- **Daftar Fitur:** Ada berapa fitur, dan daftar lengkapnya.
- **Detail per Fitur:** Untuk setiap fitur, jelaskan:
  - Tujuan fitur (Untuk apa?)
  - Aktor/Pengguna fitur
  - Fungsionalitas utama (Apa yang bisa dilakukan user?)
  - Catatan tambahan/Aturan bisnis dari wawancara.

---

## 📝 Format Output `fitur-plan.md`

Gunakan format *markdown* berikut sebagai acuan standar:

```markdown
# Dokumen Rencana Fitur (Feature Plan)

## 1. Konteks Produk
[Penjelasan singkat mengenai aplikasi dan tujuan utamanya]

## 2. Ringkasan Fitur
Total Fitur yang direncanakan: [Jumlah Fitur]
1. [Nama Fitur 1]
2. [Nama Fitur 2]

## 3. Detail Spesifikasi Fitur

### Fitur 1: [Nama Fitur 1]
- **Tujuan:** [Untuk apa fitur ini dibangun?]
- **Aktor:** [Siapa yang menggunakan?]
- **Fungsionalitas Utama:**
  - [Aksi 1]
  - [Aksi 2]
- **Catatan Wawancara:** [Hasil spesifik dari diskusi dengan user]

*(ulangi untuk fitur lainnya)*
```

## 4. Anti-Pola & Aturan Anti-Halusinasi
- ❌ **ATURAN ANTI-HALUSINASI:** DILARANG KERAS mengarang fitur atau detail implementasi teknis sendiri. Semua fitur yang dimasukkan ke dalam `fitur-plan.md` HARUS berasal murni dari hasil wawancara dengan pengguna dan isi dari `idea-brief.md`. Jangan menebak-nebak kebutuhan sistem jika pengguna tidak memintanya!

## 5. Langkah Selanjutnya

Setelah dokumen `fitur-plan.md` selesai dirangkum dan disetujui pengguna:
1. BERHENTI (Hard-Stop). Dilarang keras melakukan auto-loop ke skill berikutnya.
2. Beritahu pengguna bahwa dokumen rencana fitur telah selesai 100%.
3. Sarankan pengguna untuk memanggil skill **`prd-visualisasi`** untuk mengubah data ini menjadi diagram flowchart visual, dan TUNGGU instruksi atau persetujuan pengguna sebelum melakukan tindakan apapun.
