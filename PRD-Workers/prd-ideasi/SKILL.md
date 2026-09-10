---
name: prd-ideasi
description: Menyaring ide mentah menjadi konsep yang solid dengan user stories yang jelas. Gunakan ketika ide masih samar atau abstrak, ketika perlu membangun fondasi ide sebelum membedah fitur secara spesifik, ketika pengguna menyebut "saya punya ide", "ideasi", "brainstorm", "pertajam ide ini", "apa yang harus dibangun", atau "mulai proyek baru". Bagian dari ekosistem skill PRD Produk.
---

# PRD Ideasi 💡

Mengubah ide yang abstrak dan samar menjadi konsep yang solid, tervalidasi, dan siap masuk ke tahap perencanaan fitur — dengan problem statement dan user stories yang konkret.

> **Tujuan Utama:** Menjawab dua pertanyaan inti produk — *Untuk siapa aplikasi ini?* dan *Masalah apa yang dipecahkan?*

---

## Cara Kerja (3 Fase)

```
[IDE MENTAH / ABSTRAK]
         │
         ▼
Fase 1: PAHAMI & PERLUAS (Divergen)
  → Restate ide, ajukan pertanyaan tajam, eksplorasi variasi
         │
         ▼
Fase 2: USER STORY MAPPING
  → Definisikan persona, tulis user stories, tentukan MVP scope
         │
         ▼
Fase 3: TAJAMKAN & OUTPUT
  → Hasilkan idea-brief.md yang solid dan siap jadi input fitur-plan
         │
         ▼
  [IDEA-BRIEF.MD ✅]
```

---

## Fase 1: Pahami & Perluas (Divergen)

### Langkah 1.1 — Restate sebagai "Bagaimana Kita Bisa..."

Ubah ide mentah menjadi problem statement yang jelas:

```
Contoh:
Ide mentah : "Aplikasi untuk manajemen tim remote"
Reframe    : "Bagaimana kita bisa membantu tim remote
              berkolaborasi seefektif tim yang bekerja
              dari satu kantor?"
```

### Langkah 1.2 — Pertanyaan Penajam (Maksimal 5)

Ajukan **3-5 pertanyaan** yang paling kritis. Fokus pada sisi bisnis dan pengguna:

| Pertanyaan | Tujuan |
|---|---|
| Siapa penggunanya, secara spesifik? | Definisikan persona target |
| Seperti apa kesuksesan terlihat? | Definisikan success criteria |
| Apa kendala nyatanya dari sisi bisnis/operasional? | Pahami constraint |
| Apa alternatif/pesaing yang sudah ada? | Pahami kompetisi |
| Mengapa sekarang? | Validasi timing & relevansi |

> ⚠️ **JANGAN** menanyakan detail teknis (seperti bahasa pemrograman atau database).
> ⚠️ **JANGAN** lanjut ke langkah berikutnya sebelum mengerti siapa penggunanya dan apa masalah nyatanya.

### Langkah 1.3 — Generate 3-5 Variasi Arah

Gunakan lensa-lensa berikut untuk menghasilkan variasi yang bermakna secara produk:

| Lensa | Pertanyaan Pemicu |
|---|---|
| **Simplifikasi** | "Versi yang 10x lebih sederhana secara fungsionalitas?" |
| **Inversi** | "Bagaimana jika kita lakukan pendekatan bisnis sebaliknya?" |
| **Kombinasi** | "Jika kita gabungkan dengan [ide produk lain]?" |
| **Ganti Target User** | "Jika aplikasi ini untuk [user yang berbeda]?" |

> Setiap variasi harus punya **alasan mengapa** ia ada — bukan sekadar daftar.

Setelah user merespons arah mana yang menarik, lanjut ke Fase 2.

---

## Fase 2: User Story Mapping

Fase ini adalah **inti dari ideasi** — mendefinisikan persona dan kebutuhan dasar mereka.

### Langkah 2.1 — Definisikan Persona

Tulis 1-3 persona utama dengan format:

```markdown
### Persona: [Nama Persona]
- **Siapa:** [Deskripsi singkat — jabatan, konteks]
- **Tujuan utama:** [Apa yang ingin dicapai]
- **Frustrasi saat ini:** [Masalah yang dialami sekarang]
- **Kondisi sukses:** [Seperti apa hidupnya setelah masalah terpecahkan]
```

### Langkah 2.2 — Tulis User Stories (High-Level)

Format baku: **"Sebagai [persona], saya ingin [aksi], agar [tujuan bisnis]."**

Kelompokkan user stories berdasarkan **epic** (kategori kebutuhan besar):

```markdown
## Epic: Interaksi Utama
- US-01: Sebagai pengguna baru, saya ingin bergabung ke dalam platform, agar saya bisa mulai mengelola proyek.
- US-02: Sebagai manajer, saya ingin mendelegasikan tugas, agar pekerjaan terdistribusi.
```

### Langkah 2.3 — Tentukan MVP Scope

Dari semua user stories, identifikasi mana yang masuk MVP (Minimum Viable Product) dan mana yang bisa ditunda:

```markdown
## MVP (Harus Ada di Versi Pertama)
- US-01, US-02 — Alasan: Ini alur utama pengguna untuk merasakan manfaat produk.

## Post-MVP (Versi Berikutnya)
- US-03 — Alasan: Prioritas sekunder yang belum krusial di tahap awal peluncuran.
```

---

## Fase 3: Tajamkan & Output

### Template Idea Brief

Rangkum seluruh kesepakatan dan simpan ke `docs/product/idea-brief.md`:

```markdown
# Idea Brief: [Nama Produk]

## Problem Statement
[Kalimat "Bagaimana Kita Bisa..." yang menjadi fokus]

## Target User & Persona
[Daftar persona dengan deskripsi singkat]

## User Stories Utama (MVP)
[Daftar user stories MVP yang menjadi fondasi produk]

## Kebutuhan Ekstra (Post-MVP)
[Daftar user stories yang ditunda, beserta alasannya]

## Asumsi yang Perlu Divalidasi
- [ ] [Asumsi bisnis/pengguna 1 — cara mengujinya]
- [ ] [Asumsi bisnis/pengguna 2 — cara mengujinya]

## Langkah Selanjutnya
→ Idea Brief selesai. Arahkan pengguna untuk memanggil skill **fitur-plan** guna membedah daftar fitur secara detail berdasarkan dokumen ini.
```

---

## Anti-Pola yang Harus Dihindari

| Anti-Pola | Solusi |
|---|---|
| Membahas Tech Stack / Database | Tunda pembahasan teknis. Fokus pada "Apa" dan "Mengapa", bukan "Bagaimana". |
| Langsung membedah fitur secara granular | Gunakan `fitur-plan` di tahap selanjutnya. Di `prd-ideasi`, fokus pada User Story High-Level. |
| Memasukkan semua ide ke MVP | Buat explicit Post-MVP list — scope management adalah kunci. |
| User story terlalu generik ("user bisa kelola data") | Harus spesifik: persona + aksi nyata + manfaat bisnis. |
| **Halusinasi Task/Fitur** | **ATURAN ANTI-HALUSINASI:** DILARANG KERAS mengarang fitur, *user story*, atau kebutuhan teknis yang tidak disebutkan atau disetujui oleh pengguna. |

---

## Verifikasi Output Ideasi

Sebelum menyatakan selesai, konfirmasi:

- [ ] Problem statement "Bagaimana Kita Bisa..." sudah jelas dan terfokus
- [ ] Minimal 1 persona terdefinisi dengan baik
- [ ] User stories ditulis dalam format baku (Sebagai/ingin/agar)
- [ ] Pembagian scope MVP vs Post-MVP sudah ada
- [ ] Dokumen `docs/product/idea-brief.md` berhasil disimpan
- [ ] **TIDAK ADA** diskusi mengenai skema database, API, atau framework di dalam dokumen.

---

## Langkah Selanjutnya

Setelah `idea-brief.md` disetujui pengguna:
→ JANGAN langsung memanggil skill `fitur-plan` secara otomatis.
→ Beritahu pengguna bahwa dokumen ideasi telah selesai dan sarankan pengguna untuk memanggil skill **fitur-plan** guna menyusun daftar fitur secara lebih detail.
