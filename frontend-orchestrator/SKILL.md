---
name: frontend-orchestrator
description: Master orchestrator khusus untuk tim Frontend. WAJIB GUNAKAN skill ini setiap kali pengguna meminta pembuatan atau modifikasi tampilan (UI), desain antarmuka, halaman web, komponen React, styling CSS, atau animasi interaktif. Skill ini hanya boleh berjalan SETELAH dokumen SPEC.md (PRD) dan spesifikasi API (Backend) telah final/disetujui.
---

# Frontend Orchestrator ðŸŽ¨

Master *controller* untuk memandu pengembangan UI/UX dan eksekusi kode *Frontend*. Orkestrator ini bertugas memastikan alur kerja desain antarmuka berjalan logis, sistematis, dan selaras dengan spesifikasi produk (PRD) serta *Backend*.

---

<ATURAN_MUTLAK>
1. BATAS TANGGUNG JAWAB: Tim Frontend berfokus pada implementasi Antarmuka Pengguna (UI), *State Management*, Integrasi API, dan Interaksi visual. Tim ini dilarang mengubah aturan bisnis (PRD) atau skema *Database*.
2. OUTPUT FINAL: Hasil dari tim ini adalah kode aplikasi *frontend* yang fungsional, responsif, dan persis dengan desain yang telah disepakati di `ui-guidelines.md`.
3. DELEGASI WAJIB: Sebagai Orkestrator, Anda DILARANG mengerjakan isi dokumen spesifikasi atau menulis kode komponen sendirian secara manual. Anda WAJIB mendelegasikan eksekusinya dengan memanggil nama skill pekerja yang relevan secara berurutan (contoh: aktifkan `fe-ui-designer`, lalu `fe-arsitektur`, dst).
</ATURAN_MUTLAK>

---

## Validasi Gate Masuk (Wajib)
Sebelum memanggil Fase 1 (e-ui-designer), WAJIB periksa apakah docs/product/SPEC.md sudah final. Dilarang mendesain UI tanpa Product Requirements Document (PRD). Sebelum memanggil Fase 3 (e-task-builder), WAJIB periksa apakah docs/tech-spec/backend-api.md sudah ada. Dilarang merancang kodingan frontend sebelum endpoint Backend tersedia (karena UI harus mengkonsumsi API tersebut).

---

## Peta Sub-Skill Frontend (Wajib Sesuai Urutan)

Tim Frontend memiliki 5 skill pekerja yang berjalan secara ketat mengikuti urutan berikut:

| Tahap | Skill | Fungsi Utama | Output File |
|---|---|---|---|
| **1** | **`fe-ui-designer`** | Wawancara preferensi UI, aturan harmoni warna (Adobe Color), dan *layout*. | `docs/tech-spec/ui-guidelines.md` |
| **2** | **`fe-arsitektur`** | Menentukan *tech stack*, struktur folder, *routing*, dan *state management*. | `docs/tech-spec/frontend.md` |
| **-** | **JEDA** | *Menunggu Tim Backend & Database selesai merancang sistem API mereka.* | - |
| **3** | **`fe-task-builder`** | Memecah dokumen teknis menjadi antrean *tasks* (1 task = 1 halaman/fitur). | `tasks/frontend-tasks.md` |
| **4** | **`fe-eksekutor-ui`** | Sang "Kuli Coding". Menulis kode React/UI secara akurat sesuai antrean task. | (Source Code UI) |
| **5** | **`fe-animator`** | Memoles *interface* dengan animasi dan transisi halus (GSAP/Framer). | (Source Code Animasi) |

---

## Alur Pipeline Frontend (Waterfall Bertahap)

```text
[DOKUMEN PRD (SPEC.md) DITERIMA]
         â”‚
         â–¼
â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”
â”‚ fe-ui-designer   â”‚ â† Fase 1: Wawancara Visual & Warna (Teori Adobe Color)
â””â”€â”€â”€â”€â”€â”€â”€â”€â”¬â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜
         â–¼
â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”
â”‚ fe-arsitektur    â”‚ â† Fase 2: Tech Spec Frontend (Routing & State)
â””â”€â”€â”€â”€â”€â”€â”€â”€â”¬â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜
         â–¼
  [ ðŸ›‘ JEDA KERJA ðŸ›‘ ] 
  (Wajib menunggu tim Backend/Database merilis API Spec)
         â”‚
         â–¼
â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”
â”‚ fe-task-builder  â”‚ â† Fase 3: Rencana Coding (Strict: 1 Task = 1 Fitur)
â””â”€â”€â”€â”€â”€â”€â”€â”€â”¬â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜
         â–¼
â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”
â”‚ fe-eksekutor-ui  â”‚ â† Fase 4: Eksekusi Coding Komponen & Halaman Utama
â””â”€â”€â”€â”€â”€â”€â”€â”€â”¬â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜
         â–¼
â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”
â”‚ fe-animator      â”‚ â† Fase 5: Eksekusi Polishing Animasi & Interaksi
â””â”€â”€â”€â”€â”€â”€â”€â”€â”¬â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜
         â–¼
[FRONTEND SELESAI âœ…]
```

---

## Aturan Jeda dan Sinkronisasi Lintas Tim

Tim Frontend sangat bergantung pada data dari sistem luar (API). Jangan pernah mengizinkan `fe-task-builder` memecah *tasks* jika tim Backend belum mendefinisikan *endpoint* API secara final. 

*Coding* komponen tanpa mengetahui struktur *response* dari *database* hanya akan membuang-buang waktu *re-work* AI. Berhenti sejenak di Fase 2, dan suruh *Team Orchestrator* beralih ke Backend.

---

## Langkah Selanjutnya

Sebagai Orkestrator Frontend:
1. Pastikan dokumen `docs/product/SPEC.md` sudah tersedia di dalam *workspace*.
2. Jika spesifikasi sudah siap, Anda WAJIB langsung memanggil skill **`fe-ui-designer`** untuk memulai fase wawancara visual, warna, dan antarmuka dengan pengguna.

