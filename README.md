# ArthaBuku

## Description
ArthaBuku adalah web pencatat keuangan yang mengubah kalimat bebas berbahasa Indonesia, misalnya "tadi beli bensin 50rb sama kopi 18 ribu", menjadi transaksi terstruktur dengan bantuan AI. Pengguna memeriksa dan mengoreksi hasilnya sebelum disimpan ke buku kas, lalu melihat ringkasan harian atau mingguan.

## Problem & Target User
Masalah yang sering kita jumpai tentang keuangan. Tanpa catatan utuh, mereka kesulitan mengetahui uangnya pergi kemana, kapan kas mulai menipis,dan berapa selisih uang masuk dan keluar dalam sehari atau seminggu. Hal ini dialami oleh pelaku usaha kecil. Hasil wawancara membuktikan, dengan adanya pencatatan, pelaku usaha dapat mengetahui kemana uangnya keluar, dipaki untuk apa, dan kapan uang itu dipakai.

## Key Features
Semua fitur di bawah sudah berfungsi di URL deployment dan dapat dicoba tanpa login.

- **Catat dengan kalimat bebas (AI):** ketik transaksi dengan bahasa sehari-hari, AI mengubahnya menjadi baris transaksi (jenis, keterangan, jumlah, tanggal, kategori). Contoh input: `kemarin dapat transfer dari Budi 200rb, bayar listrik 350.000`.
- **Tinjau dan koreksi sebelum disimpan:** setiap baris hasil AI dapat diubah atau dihapus sebelum masuk buku kas.
- **Buku kas:** daftar transaksi tersimpan dengan kolom masuk, keluar, dan saldo berjalan.
- **Ringkasan harian dan mingguan:** total masuk, keluar, selisih, dan pengeluaran per kategori, dihitung langsung dari buku kas.
- **Isi manual tanpa AI:** jalur cadangan bila AI tidak tersedia atau kuota penuh.

## Tech Stack
- Frontend: HTML, CSS, dan JavaScript tanpa framework (satu file, `index.html`)
- Backend: Vercel Serverless Function (Node.js), `api/parse.js`
- Database: tidak ada; data buku kas disimpan di `localStorage` browser pengguna
- Hosting: Vercel

## AI Implementation
- **Model/API:** Google Gemini API, dipanggil dari server. Nama model diatur lewat variabel `GEMINI_MODEL` (saat ini `gemini-3.5-flash-lite`).
- **Alasan memilih model:** karena dapat di akses secara gratis dengan tier gratis, kemampuan bahasa Indonesia, dan kecepatan.
- **Fungsi dalam produk:** mengubah kalimat bebas menjadi array JSON transaksi. Tanpa AI, alur utama (mencatat dari kalimat bebas) tidak berjalan; yang tersisa hanya isian manual.
- **Input -> Output:** input berupa teks pengguna (maksimal 1000 karakter) dan tanggal hari ini (zona waktu WITA). Output berupa array JSON dengan field `tanggal`, `keterangan`, `kategori`, `jenis` (masuk/keluar), dan `jumlah` (rupiah). Instruksi ke model meminta jawaban JSON saja, dan teks pengguna diperlakukan sebagai data, bukan perintah.
- **Data yang dikirim ke penyedia model:** hanya kalimat yang diketik pengguna dan tanggal hari ini. Isi buku kas tidak dikirim. Pada tier gratis Gemini, Google dapat memakai permintaan untuk memperbaiki produknya, jadi jangan memasukkan data sensitif.
- **Penanganan error bila AI gagal:** server menolak input kosong atau terlalu panjang; respons API yang gagal, kuota penuh (429), dan jawaban yang bukan JSON ditangkap dan ditampilkan sebagai pesan jelas tanpa menghapus teks pengguna. Hasil AI divalidasi di sisi klien (jumlah lebih dari 0, tanggal valid, kategori dari daftar tetap) dan wajib ditinjau pengguna sebelum disimpan. Tersedia jalur manual.

## Architecture / Data Flow
```
Pengguna -> Frontend (index.html) -> POST /api/parse (Vercel Function) -> Gemini API
                                                                              |
Buku kas & Ringkasan <- localStorage <- Tinjau & koreksi <- JSON transaksi <--+
```
API key hanya ada di variabel lingkungan server dan tidak pernah dikirim ke browser.

## Installation / Run Instructions
1. Clone repo: `git clone https://github.com/gammaacha/ArthaBuku.git`
2. Salin `.env.example` menjadi `.env`, lalu isi `GEMINI_API_KEY` dengan key milik Anda dari Google AI Studio.
3. Tidak ada dependensi untuk dipasang.
4. Jalankan: `npx vercel dev`, lalu buka alamat lokal yang tampil. Membuka `index.html` langsung tidak cukup, karena fitur AI memerlukan `/api/parse`.

Untuk deploy sendiri: impor repo di Vercel, isi `GEMINI_API_KEY` dan `GEMINI_MODEL` di Environment Variables (centang Production), lalu Deploy.

## Deployment URL
https://arthabuku-gammapy-balidigifest.vercel.app

## Demo Account
Tidak diperlukan. Web dapat dibuka dan dicoba tanpa login.

## Known Limitations
- Data buku kas hanya tersimpan di browser dan perangkat yang dipakai; tidak ada akun, sinkronisasi, atau cadangan.
- Memakai kuota gratis Gemini, sehingga permintaan bisa ditolak saat banyak yang mencoba bersamaan.
- AI dapat salah menafsirkan kalimat yang ambigu; karena itu hasilnya wajib ditinjau sebelum disimpan.
- Hanya mendukung mata uang rupiah dan daftar kategori tetap.
- Transaksi yang sudah tersimpan hanya dapat dihapus, belum dapat diedit.
- Belum ada ekspor data.

## Third-party Resources / Credits
- Google Gemini API (model AI)
- Vercel (hosting dan serverless function)
- Google Fonts: Bricolage Grotesque dan Figtree (SIL Open Font License)

## Team Members
- Gama Brian Al Kamil

## AI & Technology Disclosure

| Komponen | Isi |
|---|---|
| Nama Produk | ArthaBuku |
| Framework / Frontend | HTML, CSS, JavaScript tanpa framework |
| Backend / Runtime | Vercel Serverless Function, Node.js |
| Database / Storage | `localStorage` browser (tanpa database server) |
| Hosting / Deployment | Vercel |
| AI Model / API / Method | Google Gemini API (`gemini-3.5-flash-lite`, diatur lewat `GEMINI_MODEL`), dipanggil dari server |
| Peran AI dalam Produk | Mengubah kalimat bebas menjadi transaksi terstruktur (fungsi utama) |
| Dataset / Data Source | Tidak ada dataset; input berasal dari pengguna |
| Open-source Libraries | Tidak ada library pihak ketiga; font dari Google Fonts |
| Template / Boilerplate | Tidak ada
| AI Coding Assistant | Claude (Anthropic) dipakai untuk membantu menulis dan merevisi kode frontend dan fungsi serverless serta menyusun dokumen. 
| Pre-existing Components | 
| Third-party Assets / Credits | Google Fonts (Bricolage Grotesque, Figtree) |

Peserta menyatakan bahwa informasi di atas benar dan lengkap, serta bertanggung jawab atas penggunaan teknologi, source code, data, library, API, model AI, dan aset yang dipakai.
