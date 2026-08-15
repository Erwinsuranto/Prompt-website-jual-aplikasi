


# 
```



```
# 
```



```

# 
```



```
# 
```



```
# 
```



```
# Prompt: Phase 27 — Real Authentication
```

PHASE 27 — REAL AUTHENTICATION

Lanjutkan project toko-online dari kondisi saat ini.

Phase 26 sudah selesai dan sudah PASS:
- Repository Prisma
- API routes
- Order/Payment/User/Banner integration
- Admin CRUD
- Checkout/payment/profile/orders
- TypeScript
- Lint
- Production build

Sekarang kerjakan PHASE 27 saja.

TUJUAN:
Ganti auth mock menjadi authentication nyata menggunakan:
- JWT
- bcrypt/bcryptjs
- HTTP-only cookie
- login/logout
- session validation
- authentication middleware
- role-based access control

TASK:

1. Audit auth yang sekarang terlebih dahulu.
Cari:
- auth-service.ts
- login page/form
- user schema
- existing auth utilities
- middleware
- mock credentials
- cookie/session logic

2. Implement login nyata:
- email + password
- cari user dari Prisma
- verifikasi password dengan bcrypt
- generate JWT
- simpan JWT pada HTTP-only cookie
- jangan expose JWT ke client-side JavaScript

3. Implement logout:
- hapus/invalidate authentication cookie
- redirect ke login bila diperlukan

4. Implement session/current-user:
- endpoint atau server utility untuk mendapatkan user yang sedang login
- validasi JWT
- ambil user dari database
- jangan percaya user ID dari client

5. Middleware:
- lindungi route yang membutuhkan authentication
- unauthenticated user diarahkan ke login
- jangan mengganggu public routes

6. Role-based access:
- admin route hanya dapat diakses user dengan role admin
- user biasa tidak boleh mengakses admin
- lakukan pengecekan server-side, bukan hanya menyembunyikan UI

7. Password:
- password harus disimpan dalam bentuk hash
- jangan menyimpan plaintext password
- seed/dev user harus menggunakan password hash
- jangan menampilkan password di response/API/log

8. Hapus ketergantungan terhadap mock authentication setelah auth nyata berfungsi.

9. Perbaiki seluruh flow yang masih memakai auth mock agar menggunakan session nyata.

10. Jangan mengubah fitur bisnis Phase 26:
- order
- payment
- banner
- product
- category

11. Security:
- HTTP-only cookie
- Secure cookie pada production
- SameSite yang sesuai
- JWT secret dari environment variable
- jangan hardcode secret
- jangan expose secret ke browser
- validasi input login
- jangan bocorkan apakah email atau password yang salah

12. Setelah implementasi jalankan:
- TypeScript check
- lint
- production build

13. Test minimal:
- login valid
- login password salah
- user tidak login → protected route ditolak
- user biasa → admin route ditolak
- admin → admin route berhasil
- logout → session tidak lagi valid
- refresh browser → session tetap valid selama cookie masih berlaku

HASIL AKHIR:
Laporkan:
1. File yang dibuat/diubah
2. Auth flow yang sekarang digunakan
3. JWT disimpan di mana
4. Middleware yang digunakan
5. Role protection yang diterapkan
6. Mock auth yang sudah dihapus/diganti
7. TypeScript PASS/FAIL
8. Lint PASS/FAIL
9. Build PASS/FAIL
10. Hasil test login/logout/admin/user
11. Apakah Phase 27 sudah 100% selesai

JANGAN:
- mengerjakan Phase 28
- mengubah database schema tanpa alasan yang diperlukan
- menghapus data production
- commit
- push

```
# Prompt: Phase 26.1 — Selesaikan Integrasi Backend API
```
PHASE 26.1 — COMPLETE API & BROWSER INTEGRATION

Lanjutkan pengerjaan repository toko-online berdasarkan hasil audit Phase 26 sebelumnya.

Jangan mengubah arsitektur besar yang sudah ada.
Jangan mengerjakan Phase 27 Authentication.
Fokus hanya menyelesaikan sisa Phase 26.

Tugas:

1. Buat API routes nyata untuk:
- /api/orders
- /api/payments
- /api/users
- /api/banners

Gunakan repository Prisma yang sudah ada.
Jangan menggunakan mock data.

2. Hubungkan service:
- order-service.ts
- payment-service.ts
- user-service.ts
- banner-service.ts

ke API routes tersebut dengan benar.

3. Perbaiki browser/client flow yang sebelumnya broken:
- Admin Order Management
- Admin Payment Management
- Admin Banner Management
- Checkout → createOrder
- Payment page → getPaymentStatus
- Shop Orders page
- Profile page

4. Perbaiki bug:
app/src/components/.../OrderStatusSelect.tsx:96

Saat ini menggunakan:
order.invoiceNumber

Periksa tipe/order schema dan gunakan field ID yang benar sesuai Prisma.

5. Pastikan semua mutation/read dari browser tidak lagi memanggil mock service secara langsung jika seharusnya melalui API.

6. Jangan mengimplementasikan JWT, bcrypt, login middleware, HTTP-only cookie, atau role-based authentication.
Itu khusus Phase 27.

7. Setelah selesai jalankan:
- TypeScript check
- lint
- production build

8. Test API routes utama dan pastikan tidak menghasilkan 404.

HASIL AKHIR:
Laporkan:
- file yang dibuat
- file yang diubah
- API route yang berhasil
- browser flow yang sudah diperbaiki
- bug yang diperbaiki
- TypeScript PASS/FAIL
- lint PASS/FAIL
- build PASS/FAIL
- apakah Phase 26 sekarang 100% selesai

Jangan commit atau push.


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
