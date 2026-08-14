


# 
```



```
# 
```



```
# 
```



```
# Prompt 01 — Backend Foundation
```
Lanjutkan repository `toko-online` berdasarkan hasil audit sebelumnya.

TASK: IMPLEMENTASI BACKEND FOUNDATION

KONDISI PROJECT:
- UI/frontend saat ini sudah sekitar 95% selesai.
- Backend masih 0% dan sebagian besar data masih mock.
- Jangan merombak atau mendesain ulang UI yang sudah ada.
- Pertahankan tampilan dan routing frontend yang sudah bekerja.
- Gunakan architecture dan dependency yang SUDAH ada di repository.
- Jangan mengganti framework utama.
- Jangan menambahkan dependency besar jika tidak diperlukan.

TUJUAN:
Membangun fondasi backend nyata secara bertahap tanpa merusak frontend.

LANGKAH:

1. AUDIT SEBELUM IMPLEMENTASI
Periksa kembali:
- package.json
- struktur app
- API routes yang sudah ada
- database/config yang sudah tersedia
- schema/model yang sudah ada
- authentication yang sudah direncanakan
- mock data yang digunakan frontend.

Jangan membuat architecture baru jika repository sudah memiliki pola yang bisa digunakan.

2. DATABASE FOUNDATION
Jika database/schema belum tersedia:
- buat struktur database minimal yang sesuai dengan kebutuhan toko online.
- gunakan teknologi database yang paling sesuai dengan dependency/project yang sudah ada.

Minimal siapkan konsep:
- users
- categories
- products
- orders

Jangan membuat fitur kompleks yang belum diperlukan.

3. API FOUNDATION
Implementasikan API routes nyata untuk kebutuhan dasar:

GET  /api/products
GET  /api/products/[id]
GET  /api/categories

Jika diperlukan:
POST /api/products
PATCH /api/products/[id]
DELETE /api/products/[id]

API harus:
- menggunakan database/repository layer;
- mempunyai validation dasar;
- mempunyai error response yang konsisten;
- tidak menggunakan mock data sebagai sumber utama.

4. MOCK DATA
Jangan langsung menghapus semua mock data.

Cari komponen yang masih menggunakan mock data dan ubah secara bertahap agar membaca API/database.

Jika ada mock data yang masih diperlukan untuk fallback/development, pertahankan tetapi pisahkan dengan jelas dari production data.

5. ERROR HANDLING
Buat response error yang konsisten.

Minimal:
- 400 Bad Request
- 404 Not Found
- 500 Internal Server Error

Jangan membocorkan credential atau informasi sensitif ke response.

6. SECURITY DASAR
Pastikan:
- secret tidak ditulis langsung di source code;
- gunakan environment variables;
- jangan commit credential;
- validasi input;
- jangan percaya data dari client.

7. FRONTEND
Jangan melakukan redesign.

Hanya ubah frontend jika memang diperlukan agar:
- product listing mengambil data API;
- category mengambil data API;
- product detail mengambil data API;
- loading state tetap bekerja;
- empty state tetap bekerja;
- error state tetap bekerja.

Pertahankan UI yang sudah ada.

8. TESTING / VALIDATION
Setelah implementasi:
- jalankan type-check;
- jalankan lint jika tersedia;
- jalankan build;
- periksa semua API route;
- pastikan tidak ada import error;
- pastikan frontend masih bisa dijalankan.

Jangan menganggap berhasil hanya karena build selesai.

9. HASIL AKHIR

Laporkan:

## Backend yang dibuat
- Database:
- Schema:
- API:
- Validation:
- Error handling:

## Frontend yang dihubungkan
- Products:
- Categories:
- Product detail:

## File yang diubah
List semua file.

## Testing
- Typecheck:
- Lint:
- Build:
- API test:

## Masalah yang ditemukan
Jelaskan jika ada.

## Next Step
Berikan rekomendasi tahap berikutnya.

PENTING:
- Kerjakan HANYA Backend Foundation pada task ini.
- Jangan implementasikan authentication JWT/bcrypt dulu.
- Jangan implementasikan payment dulu.
- Jangan implementasikan admin lengkap dulu.
- Jangan redesign UI.
- Jangan menghapus fitur existing.
- Jangan mengubah struktur besar project tanpa alasan.
- Jika ada keputusan architecture yang belum jelas, gunakan pola yang sudah ada di repository.

Setelah selesai, berhenti dan berikan laporan.


```
