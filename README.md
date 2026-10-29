# TCID — Reservasi Kendaraan GA (Vercel + Supabase)

Project ini mengubah prototipe localStorage menjadi website statis yang dapat di-host di Vercel dengan data terpusat di Supabase.

## Fitur
- Login berbasis Supabase Auth menggunakan ID karyawan yang dipetakan ke email internal `ID@tcid.internal`.
- Role `admin` dan `employee` diverifikasi melalui tabel `profiles`.
- Dashboard, form reservasi, status approval, daftar armada, dan aturan Ganjil Genap.
- Data master 8 kendaraan diambil dari workbook terlampir.
- RLS membatasi karyawan hanya membaca reservasinya sendiri; admin dapat melihat/mengelola semua reservasi.
- Pengecekan awal jam Ganjil Genap dan jadwal bentrok pada UI.

## Langkah A — Buat Supabase project
1. Buat project di https://supabase.com dan simpan URL project serta publishable/anon key.
2. Buka **SQL Editor**, jalankan seluruh isi `schema.sql`.
3. Buat file lokal `config.js` dengan menyalin `config.js.example`, lalu isi `url` dan `anonKey`.
4. **Jangan** masukkan `service_role` key ke `config.js`, HTML, GitHub, atau Vercel frontend environment.

## Langkah B — Buat dua akun demo
1. Pasang Node.js LTS di komputer.
2. Di folder project jalankan `npm install`.
3. Set environment variable `SUPABASE_URL` dan `SUPABASE_SERVICE_ROLE_KEY` hanya pada terminal lokal (ambil service-role key dari Supabase Project Settings → API Keys; jangan dibagikan).
4. Jalankan `npm run create-users`. Skrip membuat:
   - Admin GA: ID `2190482`, password awal `TCID2026!`
   - Karyawan: ID `2181237`, password awal `TCID2026!`
5. Ganti password awal sebelum menyebarkan tautan ke banyak karyawan. Untuk penggunaan produksi, buat akun individu setiap karyawan.

Contoh PowerShell (jalankan di terminal lokal, jangan taruh nilai key di file yang di-commit):
```powershell
$env:SUPABASE_URL="https://PROJECT.supabase.co"
$env:SUPABASE_SERVICE_ROLE_KEY="ISI_SERVICE_ROLE_KEY_DI_TERMINAL"
npm run create-users
```

## Langkah C — Uji lokal
Buka `index.html` melalui server lokal, bukan `file://` jika browser memblokir script/config:
```bash
npx serve .
```
Lalu buka alamat localhost yang ditampilkan. Pastikan `config.js` sudah dibuat dan dua akun sudah terdaftar.

## Langkah D — Deploy ke Vercel
1. Buat repository GitHub private dan upload isi folder project. Pastikan `config.js`, `.env`, dan service role key tidak ikut di-commit.
2. Di https://vercel.com pilih **Add New → Project**, import repository tersebut.
3. Karena ini static site, Framework Preset pilih **Other**, Build Command kosong, Output Directory `.` (root project).
4. Deploy. Setelah berhasil, Vercel memberikan URL seperti `https://nama-project.vercel.app`.
5. Untuk mengubah website, edit file di repository GitHub lalu push ke branch production; Vercel akan deploy ulang otomatis.

## Penting untuk produksi
- URL Supabase dan anon/publishable key di browser bukan rahasia; keamanan data ditangani oleh RLS. Service role key sangat rahasia.
- Password demo sama untuk dua akun pada permintaan awal. Ganti sebelum dipakai secara luas dan gunakan akun unik karyawan.
- Jangan masukkan data sensitif sebelum organisasi menyetujui Supabase sebagai penyedia layanan dan konfigurasi keamanan.
- Validasi bentrok di UI belum mencegah race condition dua orang submit bersamaan. Sebelum penggunaan operasional, pindahkan pemeriksaan bentrok dan aturan waktu ke fungsi database/RPC dengan transaksi/constraint yang sesuai.
- Prototipe ini menyimpan tanggal, waktu, dan reservasi terpusat, tetapi belum mengirim email/WA notifikasi dan belum menyediakan fitur edit/hapus kendaraan.
- Periksa kembali data nomor polisi/paritas dari workbook sebelum dipakai. Paritas di master saat ini mengikuti nilai workbook.
