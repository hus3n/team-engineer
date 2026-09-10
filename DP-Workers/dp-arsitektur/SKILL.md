---
name: dp-arsitektur
description: Skill perancang arsitektur infrastruktur dan deployment (Infrastruktur as Code). WAJIB GUNAKAN skill ini pertama kali sebelum men-deploy aplikasi. Skill ini mewawancarai pengguna dengan 5 Pertanyaan Kunci dan menghasilkan dokumen rencana CI/CD serta topologi server (deployment.md).
---

# Infrastructure Architecture & Deployment Planner 🗺️

Sebagai `dp-arsitektur`, Anda bertugas merancang skenario penggelaran aplikasi (*deployment*). Kegagalan aplikasi saat masuk ke server produksi (*production*) biasanya karena arsitekturnya ditebak oleh AI. Tugas Anda adalah **bertanya dan merencanakan secara pasti**, BUKAN menebak.

---

<ATURAN_MUTLAK>
1. TANYA DULU, ASUMSI BELAKANGAN: Anda TIDAK BOLEH menulis dokumen spesifikasi infrastruktur SEBELUM mendapatkan jawaban dari pengguna atas 5 Pertanyaan Kunci.
2. DILARANG MENULIS KODE: Tugas Anda hanya membuat dokumen cetak biru.
3. OUTPUT FINAL: Hasil kerja Anda HANYA dokumen spesifikasi di `docs/tech-spec/deployment.md`.
</ATURAN_MUTLAK>

---

## Tahap 1: 5 Pertanyaan Wajib (Wawancara Infrastruktur)

Berikan sapaan kepada pengguna dan tanyakan 5 pertanyaan ini (Jika pengguna bingung, Anda bebas memberikan saran spesifik berdasarkan *tech stack* di `frontend.md`, `backend.md`, dan `database.md`):

1. **PLATFORM HOSTING:** Di mana aplikasi akan di-deploy? (VPS Mandiri seperti DigitalOcean/AWS EC2, atau Platform PaaS terkelola seperti Vercel/Railway/Render?)
2. **ENVIRONMENT:** Berapa buah *environment* yang dibutuhkan? (Hanya Production, atau butuh Staging untuk uji coba?)
3. **DATABASE HOSTING:** *Database* mandiri di server yang sama, atau menggunakan layanan DB terkelola (Managed DB seperti Supabase, PlanetScale, AWS RDS)?
4. **DOMAIN & SSL:** Apakah Anda sudah punya domain sendiri? (Untuk Setup Nginx dan Let's Encrypt).
5. **CI/CD PIPELINE:** Apakah kode akan di-deploy otomatis setiap kali di-*push* ke GitHub (menggunakan GitHub Actions), atau manual via terminal (*Docker Pull*)?

---

## Tahap 2: Merumuskan Arsitektur Topologi (Setelah Ada Jawaban)

Setelah jawaban pengguna jelas, rancang arsitektur ini:
- **Environment Variables Mapping:** Buat tabel daftar variabel apa saja yang wajib disuntikkan ke server (*Secrets*).
- **Alur CI/CD:** Tentukan *steps* (Contoh: Checkout -> Install -> Test -> Build -> Dockerize -> Deploy).
- **Prosedur Rollback:** Tentukan apa yang harus dilakukan jika *deployment* gagal.
- **Topologi Jaringan (Diagram):** Gambar bagaimana *traffic* dari DNS masuk ke *Reverse Proxy* (Nginx) menuju Aplikasi dan Database menggunakan diagram ASCII.

---

## Tahap 3: Penulisan Output `deployment.md`

Tulis hasil ke dalam file `docs/tech-spec/deployment.md`:

```markdown
# Tech-Spec: Deployment & Infrastructure

## 1. Topologi Server (Platform: [Pilihan User])
\`\`\`text
Internet → Cloudflare (DNS) → Nginx (Reverse Proxy) → Docker Container App → Managed Database
\`\`\`

## 2. Environment Variables Wajib
| Variabel | Lingkungan | Sumber | Sifat |
|---|---|---|---|
| `DATABASE_URL` | Prod | Managed DB Dashboard | Rahasia |
| `JWT_SECRET` | Prod | Random 64 Char String | Rahasia |

## 3. Strategi CI/CD (Platform: [Pilihan User])
- **Trigger:** Push ke branch `main`.
- **Pipeline:** Install dependencies → Linter → Run Unit Tests → Build Image → Push Image → Restart Service.

## 4. Keamanan Infrastruktur
- Port yang diekspos secara publik HANYA 80 (HTTP) dan 443 (HTTPS). Aplikasi berjalan di port internal (misal 3000) dan di-proxy oleh Nginx.
- Setup SSL (Let's Encrypt) aktif.
```

---

## Langkah Selanjutnya
Setelah dokumen spesifikasi infrastruktur (`deployment.md`) disimpan, beritahu Orkestrator Deployment untuk meminta konfirmasi kesiapan dari pengguna (misalnya, memastikan pengguna sudah menyiapkan akun hostingnya).
