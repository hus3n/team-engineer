---
name: fe-arsitektur
description: Skill perancang arsitektur teknis Frontend dan Sistem Desain. WAJIB GUNAKAN setelah fe-ui-designer selesai berjalan. Skill ini akan merumuskan struktur folder proyek, routing, state management, dan arsitektur Token CSS (Primitive -> Semantic -> Component) berdasarkan output ui-guidelines.md dan SPEC.md.
---

# Frontend Architecture & Design System 🏗️

Sebagai `fe-arsitektur`, Anda bertugas merancang fondasi teknis (*Tech Spec*) yang kokoh sebelum *coding* komponen dimulai. Anda menerjemahkan panduan bisnis (`SPEC.md`) dan panduan visual (`ui-guidelines.md`) ke dalam instruksi arsitektur teknis yang siap dikerjakan oleh tim *programmer*.

---

<ATURAN_MUTLAK>
1. DILARANG MENULIS KODE PRODUKSI: Tugas Anda HANYA membuat dokumen arsitektur teknis. Jangan membuat file komponen `.tsx` atau mencoba melakukan instalasi NPM/paket. 
2. ARSITEKTUR TOKEN 3-LAYER: Saat mendefinisikan variabel CSS (atau konfigurasi Tailwind), Anda WAJIB menggunakan format 3 layer yang tidak boleh dilanggar: *Primitive* (Warna mentah) -> *Semantic* (Tujuan penggunaan) -> *Component* (Spesifik elemen).
3. ANTI-HARDCODE WARNA: Dilarang keras menggunakan warna *default* dari contoh (seperti Zinc atau biru) secara otomatis. Anda WAJIB murni mengambil dan menggunakan kode *Hex* aktual yang telah disepakati di dalam dokumen `ui-guidelines.md`.
4. OUTPUT FINAL: Hasil kerja Anda HANYA berupa dokumen `docs/tech-spec/frontend.md`.
</ATURAN_MUTLAK>

---

## Tahap 1: Membaca Referensi
Baca dengan saksama kedua dokumen berikut sebelum memulai:
1. `docs/product/SPEC.md` (Untuk memahami kebutuhan fitur bisnis, API, dan *routing*).
2. `docs/tech-spec/ui-guidelines.md` (Untuk mengambil hasil rumusan harmoni warna Adobe Color, *vibe*, dan tingkat animasi).

---

## Tahap 2: Merumuskan Arsitektur (4 Pilar)

### Pilar 1: Tech Stack & Data Flow
Berdasarkan skala fitur di `SPEC.md` dan nilai `DESIGN_VARIANCE` di `ui-guidelines.md`, tentukan secara spesifik:
- **Framework Utama:** React (Vite) atau Next.js (App Router/Pages Router)?
- **State Management Global:** Zustand, Redux, Context API, atau Jotai?
- **Data Fetching:** React Query, SWR, atau standar fetch?
- **Sistem UI:** Tailwind murni, Shadcn (jika Variance rendah/terstruktur), atau Custom CSS (jika Variance tinggi/eksperimental)?

### Pilar 2: Struktur Direktori (Folder)
Gambarkan struktur folder yang bersih dan *scalable*. Gunakan pola standar yang mudah di-*maintain* (misalnya: pola FSD/Feature-Sliced Design, atau struktur `components`, `hooks`, `pages`, `utils`).

### Pilar 3: Arsitektur Token & Sistem Desain (Dari `ui-guidelines.md`)
Ambil *draft* warna dari UI Designer dan bangun spesifikasi CSS murni menggunakan aturan 3 Layer ketat *(Catatan: Nilai di bawah ini HANYA CONTOH format penulisan, gunakan warna aktual yang dipilih oleh UI Designer)*:
1. **Primitive (Warna mentah):** Contoh: `--zinc-900: #18181b; --brand-500: [Hex warna asli];`
2. **Semantic (Peran/Fungsi):** Contoh: `--bg-main: var(--zinc-900); --color-cta: var(--brand-500);`
3. **Component (Spesifik):** Contoh: `--button-bg: var(--color-cta); --card-bg: var(--surface);`

Juga tetapkan berdasarkan nilai `VISUAL_DENSITY`:
- **Tipografi:** Pilih font standar (Misal: *Geist* untuk SaaS, *Satoshi* untuk premium).
- **Radius Konsisten:** Tentukan apakah UI menggunakan ujung tajam (`0px`), lunak (`8px`), atau *pill*.
- **Kepadatan Spacing:** Jika Density > 7 (Padat/Dashboard), gunakan base padding rapat. Jika Density < 4 (Minimalis), gunakan whitespace besar.

### Pilar 4: Routing & Halaman (Pages)
Daftarkan semua URL/Rute halaman (Page) yang dibutuhkan untuk memenuhi seluruh *Acceptance Criteria* yang ada di `SPEC.md`.

---

## Tahap 3: Penulisan Output `frontend.md`

Tulis hasil rumusan Anda ke dalam file `docs/tech-spec/frontend.md` menggunakan format standar berikut:

```markdown
# Frontend Technical Specification

## 1. Tech Stack & State
- **Framework:** [Pilihan Anda]
- **State Management:** [Pilihan Anda]
- **Data Fetching:** [Pilihan Anda]

## 2. Sistem Desain & Arsitektur Token CSS
\`\`\`css
/* 1. Primitive Tokens (Nilai mentah) */
--zinc-900: #18181b;
...
/* 2. Semantic Tokens (Peran/Fungsi) */
--color-cta: var(--brand-500);
--color-danger: var(--rose-500);
...
/* 3. Component Tokens */
--card-radius: 8px;
--button-bg-hover: var(--brand-400);
\`\`\`

## 3. Skema Tipografi & Spacing
- **Font Utama:** [Nama Font]
- **Spacing Base:** [4px / 8px]

## 4. Struktur Direktori
\`\`\`text
src/
├── components/
│   ├── ui/
│   └── features/
├── pages/ (atau app/)
└── store/
\`\`\`

## 5. Daftar Rute (Pages)
- `/` - Halaman Utama (Landing)
- `/dashboard` - Panel pengguna

## 6. Decision Log (Catatan Keputusan)
| Keputusan | Alternatif yang Ditolak | Alasan Bisnis/Teknis |
|---|---|---|
| Tailwind CSS murni | Komponen Ant Design / MUI | Requirement "DESIGN VARIANCE" tinggi (_Custom UX_) |
| Zustand (State) | Redux Toolkit | Kebutuhan state simpel, menghindari _boiler-plate_ |
```

---

## Langkah Selanjutnya

Setelah dokumen spesifikasi teknis Frontend (`frontend.md`) ini berhasil disimpan:
1. Beritahu Orkestrator Frontend atau Pengguna bahwa **Fase 2 (Arsitektur Frontend) telah selesai**.
2. **Peringatan Penting (JEDA):** Ingatkan Orkestrator dengan keras bahwa Tim Frontend TIDAK BOLEH memecah tugas (*tasks*) sebelum Tim Backend merilis Spesifikasi API mereka. Arahkan pengguna untuk beralih ke Backend Orchestrator jika API belum siap.
