# Week 3: Framework Security Update Lab

Repository ini berisi praktikum **Hands-On Pertemuan 3** tentang keamanan aplikasi web. Praktikum membandingkan aplikasi dengan konfigurasi keamanan dasar (*baseline*) dan versi yang telah diperbaiki (*secure*) untuk memahami pentingnya validasi output, security headers, serta pemeriksaan dependensi.

> **Peringatan:** aplikasi ini dibuat untuk pembelajaran dan pengujian di lingkungan lokal. Jalankan pengujian hanya pada aplikasi milik sendiri di `localhost`, bukan pada website atau server pihak lain.

## Tujuan Praktikum

- Membandingkan respons aplikasi sebelum dan sesudah perbaikan keamanan.
- Mengamati risiko *reflected Cross-Site Scripting* (XSS) akibat output yang tidak di-escape.
- Memeriksa keberadaan HTTP security headers.
- Melakukan pemeriksaan dependensi menggunakan `npm audit` dan meninjau advisori yang tersedia.
- Mendokumentasikan hasil pemeriksaan dan rekomendasi perbaikan.

## Teknologi yang Digunakan

- **Node.js** untuk menjalankan aplikasi dan skrip pemeriksaan.
- **npm** untuk mengelola dependensi dan menjalankan audit paket.
- **HTTP security headers** untuk menambah lapisan perlindungan pada respons web.
- **curl** (opsional) untuk melihat respons HTTP dari terminal.

## Struktur Repository

```text
week3_framework_security_lab/
├── app-baseline.js          # Aplikasi contoh dengan kontrol keamanan yang lemah
├── app-secure.js            # Aplikasi contoh dengan output escaping dan security headers
├── package.json             # Konfigurasi proyek dan dependensi
├── advisory-sample.json     # Contoh sumber advisori untuk peninjauan offline
├── security-checklist.md    # Template pencatatan bukti dan hasil praktikum
├── tools/
│   └── header_check.js      # Pemeriksaan beberapa HTTP security headers
└── report/                  # Direktori laporan atau bukti praktikum
```

## Persiapan

Pastikan **Node.js** dan **npm** sudah terpasang. Periksa instalasi melalui terminal:

```bash
node -v
npm -v
```

Unduh repository dan masuk ke folder proyek:

```bash
git clone https://github.com/fauzanatillah123-gif/week3_framework_security_lab.git
cd week3_framework_security_lab
```

Pasang dependensi:

```bash
npm install
```

## Langkah Pengujian

### 1. Menjalankan aplikasi baseline

Jalankan perintah berikut:

```bash
npm run baseline
```

Aplikasi akan berjalan di `http://localhost:3000/`. Biarkan terminal ini tetap terbuka selama pengujian.

Di terminal lain, periksa security headers:

```bash
node tools/header_check.js http://localhost:3000/
```

Untuk mengamati penanganan input, buka URL berikut di browser atau gunakan `curl`:

```text
http://localhost:3000/?q=%3Cscript%3Ealert(1)%3C%2Fscript%3E
http://localhost:3000/profile?name=%3Cscript%3Ealert(1)%3C%2Fscript%3E
```

Versi baseline sengaja menampilkan input pengguna tanpa output escaping dan tidak menetapkan seluruh security headers yang diperiksa. Karena itu, input HTML/JavaScript dapat masuk ke respons sebagai markup aktif.

Setelah selesai, hentikan server dengan menekan **Ctrl+C**.

### 2. Menjalankan aplikasi secure

Jalankan:

```bash
npm run secure
```

Lalu, dari terminal lain, jalankan pemeriksa header yang sama:

```bash
node tools/header_check.js http://localhost:3000/
```

Uji kembali dua URL input di atas. Versi secure melakukan **HTML escaping** pada nilai input sebelum ditampilkan dan menambahkan header seperti `Content-Security-Policy`, `X-Frame-Options`, `X-Content-Type-Options`, `Referrer-Policy`, serta `Permissions-Policy`.

Pada respons halaman utama, pemeriksa header diharapkan menampilkan status `OK` untuk header yang diwajibkan oleh skrip. Hasil aktual tetap perlu dicatat dari pengujian yang dijalankan.

Setelah selesai, hentikan server dengan **Ctrl+C**.

### 3. Memeriksa dependensi

Jalankan audit dependensi:

```bash
npm audit --audit-level=high
```

Perintah ini memerlukan akses ke registry npm dan dapat menghasilkan temuan berbeda sesuai kondisi advisori saat audit dilakukan. Jika akses internet tidak tersedia, gunakan `advisory-sample.json` sebagai **contoh bahan peninjauan offline**, bukan sebagai pengganti hasil audit terkini.

### 4. Mencatat hasil praktikum

Isi file [`security-checklist.md`](./security-checklist.md) berdasarkan hasil pengujian yang benar-benar diperoleh, termasuk:

- Perintah pemeriksaan dan security headers yang ditemukan atau tidak ditemukan.
- Payload uji serta perbedaan respons baseline dan secure.
- Hasil peninjauan source code: sumber input, lokasi output, kontrol yang tersedia, dan kontrol yang masih kurang.
- Temuan dependensi, tingkat keparahan, dampak, rekomendasi, serta sumber advisori.
- Refleksi mengenai kontrol yang dapat dibantu framework/middleware dan keputusan yang tetap memerlukan pertimbangan developer.

Jangan menuliskan hasil sebagai berhasil/gagal sebelum menjalankan pengujiannya.

## Ringkasan Perbedaan

| Area | Baseline | Secure |
|---|---|---|
| Output input pengguna | Belum melakukan HTML escaping | Melakukan HTML escaping sebelum nilai ditampilkan |
| HTTP security headers | Header keamanan yang diperiksa belum lengkap | Menetapkan beberapa header keamanan pada respons halaman |
| Pemeriksaan | Dibandingkan menggunakan URL uji dan `header_check.js` | Diuji menggunakan langkah yang sama agar hasilnya dapat dibandingkan |

## Perintah Cepat

| Kebutuhan | Perintah |
|---|---|
| Menjalankan baseline | `npm run baseline` |
| Menjalankan versi secure | `npm run secure` |
| Memeriksa security headers | `npm run headers` |
| Audit dependensi | `npm audit --audit-level=high` |

## Kesimpulan

Praktikum ini menunjukkan bahwa aplikasi web perlu menangani input pengguna secara aman dan mengatur security headers dengan tepat. Output escaping membantu mencegah input dianggap sebagai HTML/JavaScript aktif, sedangkan security headers memberi perlindungan tambahan di sisi browser. Pemeriksaan dependensi dan dokumentasi hasil juga penting untuk menemukan serta memprioritaskan risiko keamanan.

---

**Repository:** [week3_framework_security_lab](https://github.com/fauzanatillah123-gif/week3_framework_security_lab)
