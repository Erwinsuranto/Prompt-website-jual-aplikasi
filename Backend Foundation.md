


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
LANJUT — DEEP BUSINESS-FLOW AUDIT ADMIN PANEL

Kondisi terakhir:
- Admin Panel navigation PASS.
- Authorization ADMIN vs CUSTOMER PASS.
- Browser E2E PASS.
- 7 viewport × 10 route admin = 0 horizontal overflow.
- Console: 0 uncaught exception, 0 hydration error, 0 API 500.
- Typecheck PASS.
- Build PASS.
- Test suite: 98 tests, 0 fail.
- Git clean.
- HEAD = origin/main.
- Commit terakhir: a3d1c4e.
- Category label sudah human-readable dan tidak lagi menampilkan CUID.
- Dropdown/filter responsive sudah PASS.

JANGAN mengulang audit responsive/navigation yang sudah PASS.
JANGAN mengubah customer UI yang sudah PASS.
JANGAN reset/drop database.
JANGAN membuat mock database.
JANGAN force push.

Sekarang lakukan DEEP BUSINESS-FLOW AUDIT menggunakan database nyata.

1. PRODUCT CRUD

Gunakan satu produk TEST yang aman.

Verifikasi:
- CREATE product
- category selection
- price
- original price
- stock
- active
- featured
- save
- READ setelah refresh
- UPDATE
- category change
- stock change
- price change
- active toggle
- featured toggle
- DELETE hanya jika benar-benar data test

Pastikan:
- categoryId tersimpan benar;
- UI tetap menampilkan category.name;
- tidak ada duplicate product akibat retry;
- update benar-benar UPDATE record existing;
- refresh tidak mengembalikan data lama;
- customer melihat perubahan produk yang memang aktif.

2. CATEGORY CRUD

Gunakan database nyata.

Test:
- create category test;
- tampil di admin;
- pilih category tersebut pada product;
- edit nama category;
- refresh;
- product tetap memiliki relation category yang benar;
- delete category hanya jika aman.

Periksa foreign-key behavior.

Jangan menghapus kategori production yang masih digunakan produk.

3. ORDER FLOW

Buat atau gunakan satu order TEST yang aman.

Verifikasi rantai:

PRODUCT
→ CHECKOUT
→ ORDER
→ ORDER ITEM
→ PAYMENT
→ ADMIN ORDERS

Pastikan:
- satu checkout menghasilkan order yang benar;
- quantity benar;
- subtotal benar;
- total benar;
- order item mengarah ke product yang benar;
- customer benar;
- tidak ada duplicate order akibat refresh/retry.

4. ORDER STATUS vs PAYMENT STATUS

Audit secara khusus.

Pastikan:
- Order Status adalah field/state tersendiri.
- Payment Status adalah field/state tersendiri.
- Mengubah payment status tidak sembarangan mengubah order status.
- Mengubah order status tidak sembarangan mengubah payment status.

Verifikasi state transition yang memang diizinkan project.

Jika ada:
PENDING
PROCESSING
PAID
FAILED
CANCELLED
COMPLETED

ikuti enum/schema existing, jangan membuat enum baru tanpa kebutuhan.

5. PAYMENT

Audit seluruh payment flow yang memang sudah ada.

Verifikasi:
- payment method;
- payment amount;
- payment status;
- order relation;
- payment reference;
- payment timestamp;
- manual verification jika tersedia.

Test invalid payment input.

Pastikan transaksi invalid:
- ditolak;
- tidak merusak order;
- tidak membuat payment duplicate;
- tidak mengubah status secara ilegal.

6. CUSTOMER / USER

Audit data user dari database.

Pastikan:
- CUSTOMER tetap CUSTOMER;
- ADMIN tetap ADMIN;
- nomor WhatsApp canonical konsisten;
- tidak ada duplicate account akibat format nomor berbeda.

Periksa khusus nomor yang pernah muncul:
6281234567890
dan
62812345678900

Jangan menghapus atau mengubah akun secara sembarangan.

Cari source of truth sebenarnya dan dokumentasikan hasilnya.

Password/hash/token tidak boleh ditampilkan.

7. AUTHORIZATION API

Jangan hanya test browser.

Panggil endpoint admin secara langsung dengan:
- customer session;
- admin session;
- guest/no session.

Pastikan:

guest → 401/redirect sesuai desain
customer → 403 atau redirect sesuai desain
admin → 200

Test minimal endpoint:
- products admin;
- categories admin;
- orders admin;
- users/admin;
- settings;
- payment;
- reports.

Pastikan authorization server-side.

8. REPORTS

Gunakan data order nyata/test yang tersedia.

Verifikasi:
- revenue;
- order count;
- average order value;
- conversion jika memang tersedia;
- daily;
- weekly;
- monthly;
- custom range.

Bandingkan angka laporan dengan query/database secara langsung.

Jika ada perbedaan angka:
cari akar masalahnya, jangan hanya memperbaiki tampilan.

9. DASHBOARD CONSISTENCY

Bandingkan:

Dashboard
vs
Orders
vs
Reports
vs
Database

Pastikan angka yang sama memang berasal dari source yang konsisten.

Contoh:
jumlah order dashboard harus konsisten dengan jumlah order pada periode yang sama di report.

10. TRANSACTION SAFETY

Audit operasi penting yang menyentuh beberapa tabel.

Pastikan operasi seperti:
- create order;
- update payment;
- update order;
- delete product/category jika ada relation;

menggunakan transaction/atomic operation jika memang diperlukan oleh schema.

Cari kemungkinan partial write.

Contoh:
jika Order berhasil dibuat tetapi OrderItem gagal, sistem tidak boleh meninggalkan data setengah jadi.

11. RETRY / DOUBLE SUBMIT

Simulasikan:
- klik submit dua kali;
- refresh setelah submit;
- request retry;
- back lalu submit kembali.

Pastikan tidak menghasilkan:
- duplicate order;
- duplicate payment;
- duplicate product;
- duplicate category.

Jika business flow memang membutuhkan idempotency, implementasikan secara minimal menggunakan struktur existing.

12. DATA INTEGRITY

Jalankan pemeriksaan database:

- orphan order items;
- orphan payments;
- products tanpa category jika category diwajibkan;
- duplicate canonical phone;
- invalid status combination;
- negative price;
- negative stock;
- invalid totals;
- missing required relation.

Jangan memperbaiki data production secara massal tanpa bukti.

13. VALIDATION

Audit backend validation.

Jangan mengandalkan validation frontend.

Test:
- empty name;
- invalid price;
- negative price;
- invalid stock;
- invalid categoryId;
- malformed order;
- invalid payment amount;
- unauthorized role.

Backend harus menolak input invalid.

14. ERROR HANDLING

Pastikan error API:
- tidak membocorkan stack trace;
- tidak membocorkan DATABASE_URL;
- tidak membocorkan password/hash;
- tidak membocorkan cookie;
- tidak membocorkan secret.

Browser hanya menerima error yang aman dan dapat ditangani UI.

15. CUSTOMER REGRESSION

Setelah semua audit admin selesai, WAJIB verifikasi ulang:

Customer:
login
→ home
→ category
→ product
→ checkout
→ order
→ refresh
→ logout

Pastikan customer tetap PASS.

Jangan mengorbankan customer flow untuk memperbaiki admin.

16. FINAL TEST

Jalankan:

npm run typecheck
npm run build
npm run test

Kemudian production server yang sesuai dengan arsitektur project.

Lakukan smoke/E2E terhadap:
- customer;
- admin;
- product;
- category;
- order;
- payment;
- reports.

17. GIT

Jika ada perubahan:

git status --short
git diff --check

Pastikan tidak ada:
.env
.env.local
secret
credential
password
hash
cookie
build artifact
temporary file

Commit hanya jika benar-benar ada perubahan.

Gunakan message:

feat(admin): verify business flows end-to-end

Kemudian:

git push origin main

TANPA force push.

Setelah push:

git status --short
git log -1 --oneline
git status -sb

Pastikan:
working tree clean
HEAD = origin/main

HASIL AKHIR WAJIB:

- Product CRUD: PASS/FAIL
- Category CRUD: PASS/FAIL
- Order flow: PASS/FAIL
- Payment: PASS/FAIL
- Order/Payment status separation: PASS/FAIL
- Customer/User: PASS/FAIL
- Admin API authorization: PASS/FAIL
- Reports/database consistency: PASS/FAIL
- Transaction safety: PASS/FAIL
- Duplicate-submit protection: PASS/FAIL
- Validation: PASS/FAIL
- Customer regression: PASS/FAIL
- Typecheck: PASS/FAIL
- Build: PASS/FAIL
- Test: jumlah tests/pass/fail
- Git commit
- Push status
- Blocker jika ada

Jangan berhenti hanya karena test otomatis PASS.
Gunakan database nyata dan verifikasi business flow sampai ke akar.


```
# Prompt berikutnya — Audit Admin Panel End-to-End
```


PROMPT: AUDIT ADMIN PANEL END-TO-END

Project: Digital Cell / toko-online

Kondisi terakhir:
- Admin Panel sudah dapat diakses oleh role ADMIN.
- Authentication dan authorization sudah diverifikasi.
- Admin dashboard sudah PASS.
- Product/category CRUD sudah berjalan.
- Category selector sudah menampilkan nama kategori, bukan CUID.
- Dropdown kategori sudah responsive dan tidak overflow.
- Customer UI sebelumnya sudah PASS.
- Typecheck, build, production server dan smoke test terakhir PASS.
- Jangan mengulang pekerjaan yang sudah PASS kecuali ditemukan regresi.

TUGAS UTAMA:

Sekarang lakukan audit menyeluruh terhadap SELURUH ADMIN PANEL yang sudah ada.

Jangan membuat ulang Admin Panel.
Jangan mengganti database.
Jangan menghapus fitur existing.
Jangan menggunakan mock data sebagai pengganti database.
Gunakan database Prisma yang sedang dipakai aplikasi.

1. AUDIT NAVIGASI ADMIN

Periksa seluruh menu Admin Panel yang tersedia.

Pastikan minimal area berikut dapat ditemukan jika memang sudah tersedia di project:

- Dashboard
- Produk
- Kategori
- Pesanan
- Customer/User
- Pembayaran
- Banner/Promo
- Pengaturan
- Laporan
- fitur admin lain yang memang sudah ada

Pastikan:
- menu desktop tampil benar;
- drawer mobile tampil benar;
- tidak ada menu admin yang hilang;
- customer tidak dapat melihat menu admin;
- setiap menu mengarah ke route yang benar;
- tidak ada link menuju route yang belum ada.

2. DASHBOARD ADMIN

Audit dashboard:

- statistik produk;
- kategori;
- pesanan;
- revenue;
- customer;
- pesanan terbaru;
- produk terlaris;
- laporan/graph jika tersedia.

Pastikan semua angka berasal dari database nyata.

Jangan menggunakan angka hardcoded.

Periksa:
- loading state;
- empty state;
- error state;
- responsive mobile;
- desktop;
- refresh halaman.

3. PRODUK

Audit CRUD produk secara penuh.

Test:

CREATE:
- buat produk baru;
- gunakan kategori existing;
- masukkan harga jual;
- masukkan harga coret jika tersedia;
- masukkan stok;
- aktif/nonaktif;
- unggulan jika tersedia;
- simpan.

READ:
- produk muncul di tabel/list;
- nama benar;
- harga benar;
- kategori menampilkan nama;
- stok benar;
- status benar.

UPDATE:
- edit nama;
- edit harga;
- edit kategori;
- edit stok;
- toggle aktif/nonaktif;
- simpan;
- refresh;
- pastikan perubahan tetap tersimpan.

DELETE:
- gunakan delete hanya pada data test jika memang diperlukan;
- pastikan confirmation dialog;
- pastikan tidak terjadi delete tanpa konfirmasi.

Jangan menghapus data production nyata.

4. KATEGORI

Audit CRUD kategori.

Pastikan:
- nama kategori;
- slug;
- status;
- jumlah produk;
- create;
- edit;
- delete;
- relasi dengan produk.

Pastikan categoryId tetap menjadi ID internal.

Tidak boleh ada CUID yang tampil kepada user/admin kecuali memang field teknis yang sengaja ditampilkan.

5. PESANAN

Audit Admin Orders.

Pastikan admin dapat:

- melihat daftar pesanan;
- melihat detail pesanan;
- melihat customer;
- melihat produk;
- melihat jumlah;
- melihat subtotal;
- melihat total;
- melihat status order;
- melihat status pembayaran;
- melihat tanggal;
- mencari order;
- filter order;
- membuka detail order.

Periksa terutama perbedaan:

Order Status
dan
Payment Status

Keduanya harus tetap terpisah.

Jangan membuat perubahan status order yang secara tidak sengaja mengubah payment status kecuali memang aturan bisnis mengharuskannya.

6. PAYMENT

Audit payment flow admin.

Pastikan admin dapat melihat:

- ID pesanan;
- nominal;
- metode pembayaran;
- payment status;
- bukti pembayaran jika ada;
- waktu pembayaran.

Jika terdapat verifikasi manual:

- approve;
- reject;
- pending;

harus memiliki guard dan validasi yang benar.

Pastikan transaksi invalid tidak merusak database.

7. CUSTOMER / USER

Audit halaman user/customer jika sudah tersedia.

Pastikan admin dapat melihat data yang memang diperlukan:

- nama;
- nomor WhatsApp;
- role;
- tanggal bergabung;
- status akun.

Jangan menampilkan:
- password;
- password hash;
- secret;
- token session;
- credential sensitif.

Pastikan role ADMIN dan CUSTOMER tetap benar.

8. SETTINGS

Audit Admin Settings.

Periksa seluruh setting existing.

Jangan menghapus setting yang sudah ada.

Pastikan:
- GET settings;
- update settings;
- validation;
- persistence;
- refresh;
- database tetap benar.

Jika ada payment settings:
- QRIS;
- bank;
- e-wallet;
- metode aktif/nonaktif;

pastikan perubahan benar-benar tersimpan.

9. LAPORAN

Audit halaman laporan.

Periksa:

- filter 7 hari;
- 30 hari;
- 3 bulan;
- semua;
- custom date range;
- grafik;
- revenue;
- jumlah order;
- conversion;
- produk terlaris.

Pastikan filter tanggal tidak overflow pada:

360px
375px
390px
414px
768px
1280px
1440px

Pastikan input tanggal tetap berada di dalam container.

10. RESPONSIVE ADMIN PANEL

Verifikasi seluruh route admin pada:

- 360px
- 375px
- 390px
- 414px
- 768px
- 1280px
- 1440px

Cari:

- horizontal overflow;
- card keluar container;
- dropdown keluar viewport;
- tabel terlalu lebar;
- tombol terpotong;
- modal keluar layar;
- drawer tidak bisa ditutup;
- header overlap;
- bottom navigation overlap;
- text terpotong.

Customer UI yang sudah PASS jangan disentuh.

11. DATABASE INTEGRITY

Gunakan database nyata.

Verifikasi:

- create tidak membuat duplicate yang tidak perlu;
- update tidak membuat record baru;
- delete tidak merusak foreign key;
- order dan order item tetap konsisten;
- product dan category relation benar;
- payment dan order relation benar.

Jangan melakukan:

- DROP DATABASE;
- reset database;
- delete seluruh data;
- regenerate data production.

12. SECURITY

Audit authorization.

Pastikan:

CUSTOMER:
- tidak dapat membuka /admin/*
- tidak dapat memanggil API admin
- tidak dapat mengubah data produk
- tidak dapat mengubah kategori
- tidak dapat mengubah payment settings
- tidak dapat mengubah order admin

ADMIN:
- dapat menggunakan area customer;
- dapat mengakses Admin Panel;
- dapat menjalankan operasi admin yang memang diizinkan.

Authorization wajib dilakukan server-side.

Jangan hanya menyembunyikan menu frontend.

13. API

Audit endpoint yang digunakan Admin Panel.

Cari:

- endpoint yang masih mock;
- endpoint yang mengembalikan data hardcoded;
- endpoint yang salah authorization;
- endpoint yang memakai database berbeda;
- endpoint yang tidak melakukan validation.

Pastikan frontend dan backend menggunakan source of truth yang sama.

14. ERROR HANDLING

Pastikan setiap operasi memiliki:

- loading;
- success;
- error;
- empty state.

Jangan menampilkan stack trace kepada user.

Jangan mencetak password, token, cookie, hash, DATABASE_URL atau secret ke log.

15. BROWSER E2E

Gunakan browser/public server yang benar-benar sedang aktif.

Test minimal:

ADMIN:
login
→ dashboard
→ produk
→ create product
→ edit product
→ kategori
→ orders
→ payment
→ settings
→ laporan
→ refresh setiap halaman.

CUSTOMER:
login
→ customer dashboard/store
→ products
→ checkout/orders
→ refresh
→ coba /admin/dashboard
→ harus ditolak.

16. CONSOLE

Pastikan browser console:

- 0 uncaught exception;
- 0 hydration error;
- tidak ada API 500;
- tidak ada route 404 yang tidak disengaja;
- tidak ada failed request yang menyebabkan UI rusak.

17. TEST AUTOMATION

Jalankan test yang tersedia.

Minimal:

npm run typecheck
npm run build
npm run test

Jika script test bawaan project gagal karena konfigurasi Node/environment, jangan mengubah test hanya agar PASS.

Jelaskan akar masalahnya dan gunakan metode test yang kompatibel dengan konfigurasi project.

18. GIT

Sebelum commit:

git status --short
git diff --check

Pastikan:

- tidak ada .env;
- tidak ada secret;
- tidak ada credential;
- tidak ada build artifact;
- tidak ada file temporary.

Jika tidak ada perubahan yang diperlukan, jangan membuat commit kosong.

Jika ada perubahan:

commit dengan message yang jelas, misalnya:

feat(admin): complete admin panel e2e audit

Kemudian:

git push origin main

TANPA force push.

Setelah push:

git status --short
git log -1 --oneline
git status -sb

Pastikan working tree bersih dan branch sinkron dengan origin/main.

HASIL AKHIR:

Laporkan:

1. route admin yang diverifikasi;
2. fitur admin yang PASS;
3. fitur yang diperbaiki;
4. hasil database verification;
5. hasil authorization;
6. hasil responsive test;
7. hasil browser E2E;
8. typecheck;
9. build;
10. test;
11. commit hash;
12. push status;
13. blocker jika masih ada.

PENTING:
Jangan berhenti hanya karena UI terlihat bagus.
Verifikasi sampai level database + API + authorization + browser.
Jangan merusak customer UI yang sebelumnya sudah PASS.
```
# 
```
## Audit & Perbaiki Integrasi Backend Prisma

Project: **Digital Cell / toko-online**

Lakukan audit menyeluruh terhadap project yang sedang aktif. Jangan membuat ulang project dan jangan menghapus fitur yang sudah ada.

Temuan awal:

* Next.js 14.2.33
* TypeScript
* Prisma 5.22
* Database migration sudah ada di `app/prisma/migrations/`
* `npm run typecheck` berhasil
* `npm run build` berhasil
* `npm run start` gagal karena `next.config` menggunakan `output: "export"`
* Pencarian `PrismaClient` di `src` tidak menemukan penggunaan Prisma.
* Project memiliki service seperti `auth-service`, `banner-service`, `category-service`, `order-service`, `payment-service`, `product-service`, `user-service`, dll.

Tugas:

1. Audit seluruh struktur project terlebih dahulu.
2. Tentukan apakah project memang membutuhkan Next.js server runtime untuk Prisma/database.
3. Jika Prisma belum terintegrasi, implementasikan integrasi Prisma dengan benar.
4. Jika `output: "export"` tidak sesuai dengan arsitektur aplikasi, perbaiki `next.config` agar aplikasi dapat berjalan menggunakan `npm run start`.
5. Buat/revisi Prisma client singleton yang aman untuk development dan production.
6. Hubungkan service yang memang membutuhkan database ke Prisma tanpa merusak UI/store yang sudah ada.
7. Periksa authentication, user, product, category, banner, order, order item, payment, notification dan data settings agar siap menggunakan database sesuai schema Prisma yang sudah ada.
8. Jangan membuat API route hanya untuk formalitas. Buat endpoint/server logic yang memang diperlukan oleh frontend.
9. Jangan menyimpan `DATABASE_URL`, secret, atau `.env.local` ke Git.
10. Jangan mengubah desain UI kecuali diperlukan untuk memperbaiki integrasi.
11. Jangan menghapus migration yang sudah dibuat.
12. Pastikan semua perubahan modular dan mudah dikembangkan.
13. Jalankan:

* `npm run typecheck`
* `npm run build`
* `npm run start` atau validasi production server dengan cara yang sesuai arsitektur.

14. Jika ditemukan error, perbaiki sampai ketiga tahap tersebut valid.

15. Setelah selesai, tampilkan ringkasan file yang diubah dan alasan setiap perubahan.

16. Setelah seluruh verifikasi PASS, lakukan commit dan push ke `origin main`.

17. Sebelum commit, jalankan `git status --short` dan `git diff --check`.

18. Pastikan tidak ada `.env`, secret, credential, build artifact, atau file temporary yang ikut di-commit.

19. Gunakan commit message yang jelas, misalnya `feat(backend): integrate Prisma database services`.

20. Jalankan `git push origin main` tanpa force push.

21. Setelah push, verifikasi `git status --short`, `git log -1 --oneline`, dan sinkronisasi dengan `origin/main`.

22. Perbaiki akses dan navigasi Admin Panel yang saat ini belum terlihat dari UI meskipun route `/admin/dashboard` sudah pernah terverifikasi HTTP 200.

23. Audit terlebih dahulu implementasi authentication/session dan role authorization. Pastikan akun dengan role `ADMIN` benar-benar dikenali sebagai admin dari session yang sedang aktif, bukan hanya berdasarkan tampilan profil.

24. Untuk akun `ADMIN`, tambahkan akses navigasi yang jelas menuju **Admin Panel** tanpa mengubah navigasi customer yang sudah ada. Admin tetap boleh menggunakan seluruh fitur customer, tetapi harus mendapatkan tambahan menu/entry `Admin Panel`.

25. Menu `Admin Panel` hanya boleh tampil untuk user dengan role `ADMIN`. Customer biasa tidak boleh melihat menu tersebut.

26. Saat admin memilih `Admin Panel`, arahkan ke `/admin/dashboard`. Jangan membuat dashboard baru jika dashboard admin yang sudah ada masih dapat digunakan; hubungkan navigasi ke implementasi existing.

27. Audit seluruh route `/admin/*` dan middleware/guard-nya. Semua route admin harus menolak customer/non-admin dengan mekanisme authorization yang benar. Jangan hanya menyembunyikan menu di frontend.

28. Pastikan login admin dari UI menghasilkan session/cookie yang valid, kemudian `/admin/dashboard` dapat dibuka langsung, direfresh, dan tetap terautentikasi sebagai ADMIN.

29. Pastikan login customer tetap bekerja dan customer tidak dapat mengakses `/admin/dashboard` maupun endpoint admin hanya dengan mengetik URL secara manual.

30. Audit perbedaan nomor WhatsApp akun test yang pernah muncul (`6281234567890` dan `62812345678900`). Jangan membuat akun duplikat atau mengubah data production sembarangan. Identifikasi akun ADMIN yang benar dari database/source of truth dan gunakan akun tersebut untuk verifikasi.

31. Jangan mengubah halaman profil customer/admin menjadi Admin Panel. Profil tetap halaman akun. Admin Panel harus menjadi area administrasi terpisah dengan route dan layout yang jelas.

32. Verifikasi minimal end-to-end:

* customer login → session PASS → `/orders` PASS;
* customer → `/admin/dashboard` ditolak/redirect PASS;
* admin login → session role `ADMIN` PASS;
* admin → `/admin/dashboard` HTTP 200 PASS;
* admin refresh dashboard minimal 5 kali PASS;
* admin menu menampilkan `Admin Panel` PASS;
* customer menu tidak menampilkan `Admin Panel` PASS;
* seluruh route admin utama yang sudah ada tetap PASS.

33. Jangan mengubah desain/UI customer yang sudah dinyatakan PASS, kecuali perubahan kecil yang diperlukan untuk menambahkan entry `Admin Panel` secara responsif.

PENTING:

* Jangan hanya menghilangkan `output: "export"` tanpa memastikan seluruh aplikasi tetap berfungsi.
* Jangan mengganti database.
* Jangan menggunakan mock data sebagai pengganti database production.
* Jangan menghapus fitur existing.
* Gunakan schema Prisma yang sudah tersedia sebagai sumber data utama.
* Jika ada bagian yang ternyata memang sengaja static, pertahankan bagian tersebut dan integrasikan backend hanya pada fitur yang membutuhkan database.
* Karena VPS berisiko mati, jangan berhenti setelah implementasi. Jika typecheck, build, dan production server verification sudah PASS, langsung commit dan push ke GitHub.
* Jangan force push.
* Jika ada error yang belum terselesaikan, jangan commit perubahan yang rusak; perbaiki terlebih dahulu jika aman, atau berhenti dan laporkan blocker.

34. BLOCKER TERKINI — LOGIN ADMIN UI MASIH `INVALID_CREDENTIALS` MESKIPUN TEST SERVER PASS:

* Browser UI saat ini menolak login admin dengan `INVALID_CREDENTIALS`.
* Jangan menganggap hasil smoke test sebelumnya cukup. Reproduksi login melalui endpoint/API yang benar-benar dipakai form login UI.
* Audit alur lengkap: input nomor WhatsApp → normalisasi nomor → request login → lookup database → verifikasi password/hash → pembuatan session cookie → redirect.
* Gunakan akun ADMIN yang benar-benar ada di database dev yang sedang dipakai server. Jangan membuat akun baru, jangan reset/drop database, dan jangan mengubah akun production.
* Audit khusus perbedaan nomor `6281234567890` dan `62812345678900`. Tentukan nomor canonical yang tersimpan di DB dan pastikan UI/API menggunakan format canonical yang sama.
* Audit password test yang benar-benar tersimpan/di-seed. Jangan mengasumsikan password hanya berdasarkan catatan lama. Verifikasi hash secara aman dari server-side/test harness tanpa mencetak hash atau secret ke output.
* Pastikan UI login tidak mengirim field yang salah, tidak melakukan transformasi nomor yang merusak, dan tidak memakai endpoint login lama/mock.
* Pastikan frontend dan backend menggunakan database/environment yang sama dengan production server yang sedang dijalankan.
* Tambahkan logging diagnosis server-side yang aman untuk membedakan: user tidak ditemukan, password salah, role salah, session gagal, atau request menuju endpoint yang salah. Jangan log password, hash, cookie value, atau secret.
* Setelah akar masalah ditemukan, perbaiki sumber masalahnya secara minimal dan modular. Jangan menurunkan keamanan auth atau membuat bypass login.
* Verifikasi ulang dari browser/public IP menggunakan akun ADMIN nyata: login PASS → session PASS → `/admin/dashboard` PASS → refresh minimal 5x PASS → Admin Panel terlihat.
* Verifikasi customer tetap PASS dan customer tetap ditolak dari `/admin/dashboard`.
* Setelah semua PASS, jalankan typecheck, build, smoke test, `git diff --check`, cek secret/env, commit dan push `origin main` tanpa force push.

35. BLOCKER BARU — PORT 3000 / OAUTH CALLBACK CLIENT-SIDE EXCEPTION:

* Public URL port 3000 saat membuka flow login sekarang menampilkan `Application error: a client-side exception has occurred` pada URL `/auth/login?callback...`.
* Jangan menganggap ini masalah credential terlebih dahulu. Audit dan reproduksi error ini dari browser/public IP pada port 3000.
* Tentukan arsitektur port 3000: apakah ini Next.js frontend/app server yang seharusnya melayani login, reverse proxy, atau service lain. Jangan menjalankan dua aplikasi berbeda pada port yang sama.
* Audit route `/auth/login`, query `callback`, middleware, auth/session provider, client component login, server component, dan seluruh redirect chain sebelum/sesudah callback.
* Periksa browser console dan server log untuk menemukan exception JavaScript/React/Next.js yang sebenarnya. Jangan hanya menyembunyikan error dengan fallback UI.
* Pastikan URL callback/redirect tidak mengarah ke port yang salah, tidak menghasilkan URL malformed, dan konsisten dengan origin/public URL aplikasi yang sedang aktif.
* Audit environment variable yang berhubungan dengan auth/base URL/origin/callback. Jangan mencetak secret ke log dan jangan commit `.env`.
* Pastikan login password/WhatsApp tidak tanpa sengaja dialihkan ke OAuth callback handler atau endpoint yang berbeda.
* Jika ada Google/Apple OAuth yang memang belum dikonfigurasi, jangan membuat login password ikut bergantung pada provider tersebut. Login WhatsApp/password harus tetap bisa berjalan secara independen.
* Periksa hydration mismatch, penggunaan `window`/`document` pada server render, import client-only, penggunaan `localStorage`, dan error parsing response yang dapat menyebabkan client-side exception setelah redirect.
* Pastikan callback yang gagal tidak meninggalkan session/cookie setengah jadi. Jika session invalid, tangani secara aman dan redirect kembali ke login dengan pesan error yang jelas, bukan crash.
* Verifikasi dengan browser sungguhan/public IP:

  1. buka `http://PUBLIC_IP:3000`;
  2. buka login;
  3. login customer dengan akun test nyata;
  4. login admin dengan akun ADMIN nyata;
  5. pastikan tidak ada `Application error`;
  6. pastikan session cookie terbentuk;
  7. admin diarahkan ke `/admin/dashboard`;
  8. customer diarahkan ke area customer;
  9. refresh masing-masing halaman minimal 5x;
  10. customer tetap ditolak dari `/admin/dashboard`.
* Jangan membuat bypass authentication, jangan menonaktifkan middleware, jangan menghapus authorization, dan jangan mengganti database hanya untuk membuat login terlihat berhasil.
* Setelah akar masalah diperbaiki, verifikasi `npm run typecheck`, `npm run build`, production server, smoke/E2E auth, `git diff --check`, status Git, lalu commit dan push `origin main` tanpa force push.

36. BLOCKER UI ADMIN — ID KATEGORI MASIH MENAMPILKAN ID DATABASE DAN DROPDOWN/FILTER OVERFLOW:

* Pada halaman Admin Panel, form tambah/edit produk masih menampilkan nilai seperti `cmt8fiedp0003us0jiz23mbwd` pada field `Kategori`. Ini adalah ID internal database/Prisma dan tidak boleh ditampilkan sebagai label kepada admin.
* Audit seluruh komponen category selector, product form, filter kategori, query/API kategori, dan mapping data dari database ke UI.
* Field kategori harus menampilkan **nama kategori yang mudah dibaca manusia** (contoh: `AI & Tools`, `Aplikasi`, `Streaming`, `Top Up`, `Cloud & Server`, `Lainnya`), sedangkan ID database hanya boleh dipakai sebagai value internal untuk request/API.
* Pastikan ketika kategori dipilih, UI menampilkan nama kategori; saat submit, frontend tetap mengirim `categoryId` canonical ke backend. Jangan mengganti primary key database hanya demi mempercantik UI.
* Audit semua tempat lain yang masih menampilkan `categoryId`, `productId`, `userId`, atau CUID/ID internal secara langsung kepada admin/customer. Jika memang merupakan field teknis yang tidak perlu dilihat user, tampilkan label/nama yang sesuai.
* Dropdown kategori pada screenshot juga terlihat terlalu panjang dan keluar dari batas card/container. Perbaiki positioning, width, max-height, overflow, z-index, dan scrolling agar dropdown selalu berada di dalam viewport dan tidak memotong/keluar dari card secara buruk.
* Dropdown harus responsif pada mobile maupun desktop. Jangan menggunakan fixed width yang menyebabkan overflow horizontal.
* Untuk mobile, gunakan menu/dropdown yang dapat discroll secara vertikal dengan `max-height` yang aman terhadap viewport, dan pastikan item terakhir tetap dapat dipilih.
* Pastikan dropdown tidak tertutup oleh parent dengan `overflow: hidden` atau berada di bawah elemen lain karena stacking context/z-index yang salah. Jika arsitektur existing memakai portal/popover, gunakan pola existing tersebut secara konsisten.
* Saat dropdown dibuka dekat bagian bawah layar, komponen harus dapat memilih posisi yang sesuai (misalnya membuka ke atas) atau tetap menggunakan area scroll yang tidak keluar viewport.
* Perbaiki filter kategori pada halaman produk/laporan jika masalah yang sama terjadi. Filter harus menampilkan nama kategori, bukan ID, dan tidak boleh keluar dari box/card.
* Audit juga filter periode pada halaman Laporan berdasarkan screenshot: input tanggal dan separator tidak boleh overflow keluar dari container pada mobile. Susun ulang menjadi layout responsif (misalnya stack/2 baris pada viewport sempit) tanpa merusak desktop.
* Jangan mengubah style global secara agresif. Gunakan komponen/shared utility yang memang bertanggung jawab terhadap select/filter/card agar perubahan tidak merusak customer UI yang sebelumnya sudah PASS.
* Jangan menghapus fungsi pencarian/filter/sort yang sudah ada. Fokus pada presentation, responsive layout, dan mapping label/value.
* Verifikasi dengan data database nyata bahwa setiap kategori memiliki label yang benar dan kategori yang dipilih pada edit produk tetap terpilih setelah halaman dibuka ulang.
* Verifikasi create product: pilih `Top Up` → UI menampilkan `Top Up` → submit → database menyimpan `categoryId` yang benar → edit product kembali menampilkan `Top Up`, bukan CUID.
* Verifikasi semua kategori yang tersedia, termasuk kategori yang memiliki nama panjang.
* Verifikasi viewport minimal 360px, 375px, 390px, 414px, 768px, 1280px, dan 1440px. Tidak boleh ada horizontal overflow baru pada halaman Admin Panel.
* Verifikasi dropdown/filter dibuka dan ditutup berulang kali, klik item terakhir, scroll dropdown, resize viewport, dan navigasi kembali/refresh.
* Pastikan tidak ada console error setelah perubahan.
* Setelah perbaikan, jalankan typecheck, build, smoke test, dan visual/responsive verification. Pastikan customer UI tetap PASS.
* Jangan membuat data kategori baru hanya untuk mengatasi tampilan ID. Gunakan data kategori yang sudah ada di database.
* Setelah seluruh verifikasi PASS, lakukan `git diff --check`, pastikan tidak ada secret/env/artifact, commit perubahan dengan message yang jelas, lalu `git push origin main` tanpa force push.



```
# 
```
## Audit & Perbaiki Integrasi Backend Prisma

Project: **Digital Cell / toko-online**

Lakukan audit menyeluruh terhadap project yang sedang aktif. Jangan membuat ulang project dan jangan menghapus fitur yang sudah ada.

Temuan awal:

* Next.js 14.2.33
* TypeScript
* Prisma 5.22
* Database migration sudah ada di `app/prisma/migrations/`
* `npm run typecheck` berhasil
* `npm run build` berhasil
* `npm run start` gagal karena `next.config` menggunakan `output: "export"`
* Pencarian `PrismaClient` di `src` tidak menemukan penggunaan Prisma.
* Project memiliki service seperti `auth-service`, `banner-service`, `category-service`, `order-service`, `payment-service`, `product-service`, `user-service`, dll.

Tugas:

1. Audit seluruh struktur project terlebih dahulu.
2. Tentukan apakah project memang membutuhkan Next.js server runtime untuk Prisma/database.
3. Jika Prisma belum terintegrasi, implementasikan integrasi Prisma dengan benar.
4. Jika `output: "export"` tidak sesuai dengan arsitektur aplikasi, perbaiki `next.config` agar aplikasi dapat berjalan menggunakan `npm run start`.
5. Buat/revisi Prisma client singleton yang aman untuk development dan production.
6. Hubungkan service yang memang membutuhkan database ke Prisma tanpa merusak UI/store yang sudah ada.
7. Periksa authentication, user, product, category, banner, order, order item, payment, notification dan data settings agar siap menggunakan database sesuai schema Prisma yang sudah ada.
8. Jangan membuat API route hanya untuk formalitas. Buat endpoint/server logic yang memang diperlukan oleh frontend.
9. Jangan menyimpan `DATABASE_URL`, secret, atau `.env.local` ke Git.
10. Jangan mengubah desain UI kecuali diperlukan untuk memperbaiki integrasi.
11. Jangan menghapus migration yang sudah dibuat.
12. Pastikan semua perubahan modular dan mudah dikembangkan.
13. Jalankan:

* `npm run typecheck`
* `npm run build`
* `npm run start` atau validasi production server dengan cara yang sesuai arsitektur.

14. Jika ditemukan error, perbaiki sampai ketiga tahap tersebut valid.

15. Setelah selesai, tampilkan ringkasan file yang diubah dan alasan setiap perubahan.

16. Setelah seluruh verifikasi PASS, lakukan commit dan push ke `origin main`.

17. Sebelum commit, jalankan `git status --short` dan `git diff --check`.

18. Pastikan tidak ada `.env`, secret, credential, build artifact, atau file temporary yang ikut di-commit.

19. Gunakan commit message yang jelas, misalnya `feat(backend): integrate Prisma database services`.

20. Jalankan `git push origin main` tanpa force push.

21. Setelah push, verifikasi `git status --short`, `git log -1 --oneline`, dan sinkronisasi dengan `origin/main`.

22. Perbaiki akses dan navigasi Admin Panel yang saat ini belum terlihat dari UI meskipun route `/admin/dashboard` sudah pernah terverifikasi HTTP 200.

23. Audit terlebih dahulu implementasi authentication/session dan role authorization. Pastikan akun dengan role `ADMIN` benar-benar dikenali sebagai admin dari session yang sedang aktif, bukan hanya berdasarkan tampilan profil.

24. Untuk akun `ADMIN`, tambahkan akses navigasi yang jelas menuju **Admin Panel** tanpa mengubah navigasi customer yang sudah ada. Admin tetap boleh menggunakan seluruh fitur customer, tetapi harus mendapatkan tambahan menu/entry `Admin Panel`.

25. Menu `Admin Panel` hanya boleh tampil untuk user dengan role `ADMIN`. Customer biasa tidak boleh melihat menu tersebut.

26. Saat admin memilih `Admin Panel`, arahkan ke `/admin/dashboard`. Jangan membuat dashboard baru jika dashboard admin yang sudah ada masih dapat digunakan; hubungkan navigasi ke implementasi existing.

27. Audit seluruh route `/admin/*` dan middleware/guard-nya. Semua route admin harus menolak customer/non-admin dengan mekanisme authorization yang benar. Jangan hanya menyembunyikan menu di frontend.

28. Pastikan login admin dari UI menghasilkan session/cookie yang valid, kemudian `/admin/dashboard` dapat dibuka langsung, direfresh, dan tetap terautentikasi sebagai ADMIN.

29. Pastikan login customer tetap bekerja dan customer tidak dapat mengakses `/admin/dashboard` maupun endpoint admin hanya dengan mengetik URL secara manual.

30. Audit perbedaan nomor WhatsApp akun test yang pernah muncul (`6281234567890` dan `62812345678900`). Jangan membuat akun duplikat atau mengubah data production sembarangan. Identifikasi akun ADMIN yang benar dari database/source of truth dan gunakan akun tersebut untuk verifikasi.

31. Jangan mengubah halaman profil customer/admin menjadi Admin Panel. Profil tetap halaman akun. Admin Panel harus menjadi area administrasi terpisah dengan route dan layout yang jelas.

32. Verifikasi minimal end-to-end:

* customer login → session PASS → `/orders` PASS;
* customer → `/admin/dashboard` ditolak/redirect PASS;
* admin login → session role `ADMIN` PASS;
* admin → `/admin/dashboard` HTTP 200 PASS;
* admin refresh dashboard minimal 5 kali PASS;
* admin menu menampilkan `Admin Panel` PASS;
* customer menu tidak menampilkan `Admin Panel` PASS;
* seluruh route admin utama yang sudah ada tetap PASS.

33. Jangan mengubah desain/UI customer yang sudah dinyatakan PASS, kecuali perubahan kecil yang diperlukan untuk menambahkan entry `Admin Panel` secara responsif.

PENTING:

* Jangan hanya menghilangkan `output: "export"` tanpa memastikan seluruh aplikasi tetap berfungsi.
* Jangan mengganti database.
* Jangan menggunakan mock data sebagai pengganti database production.
* Jangan menghapus fitur existing.
* Gunakan schema Prisma yang sudah tersedia sebagai sumber data utama.
* Jika ada bagian yang ternyata memang sengaja static, pertahankan bagian tersebut dan integrasikan backend hanya pada fitur yang membutuhkan database.
* Karena VPS berisiko mati, jangan berhenti setelah implementasi. Jika typecheck, build, dan production server verification sudah PASS, langsung commit dan push ke GitHub.
* Jangan force push.
* Jika ada error yang belum terselesaikan, jangan commit perubahan yang rusak; perbaiki terlebih dahulu jika aman, atau berhenti dan laporkan blocker.

34. BLOCKER TERKINI — LOGIN ADMIN UI MASIH `INVALID_CREDENTIALS` MESKIPUN TEST SERVER PASS:

* Browser UI saat ini menolak login admin dengan `INVALID_CREDENTIALS`.
* Jangan menganggap hasil smoke test sebelumnya cukup. Reproduksi login melalui endpoint/API yang benar-benar dipakai form login UI.
* Audit alur lengkap: input nomor WhatsApp → normalisasi nomor → request login → lookup database → verifikasi password/hash → pembuatan session cookie → redirect.
* Gunakan akun ADMIN yang benar-benar ada di database dev yang sedang dipakai server. Jangan membuat akun baru, jangan reset/drop database, dan jangan mengubah akun production.
* Audit khusus perbedaan nomor `6281234567890` dan `62812345678900`. Tentukan nomor canonical yang tersimpan di DB dan pastikan UI/API menggunakan format canonical yang sama.
* Audit password test yang benar-benar tersimpan/di-seed. Jangan mengasumsikan password hanya berdasarkan catatan lama. Verifikasi hash secara aman dari server-side/test harness tanpa mencetak hash atau secret ke output.
* Pastikan UI login tidak mengirim field yang salah, tidak melakukan transformasi nomor yang merusak, dan tidak memakai endpoint login lama/mock.
* Pastikan frontend dan backend menggunakan database/environment yang sama dengan production server yang sedang dijalankan.
* Tambahkan logging diagnosis server-side yang aman untuk membedakan: user tidak ditemukan, password salah, role salah, session gagal, atau request menuju endpoint yang salah. Jangan log password, hash, cookie value, atau secret.
* Setelah akar masalah ditemukan, perbaiki sumber masalahnya secara minimal dan modular. Jangan menurunkan keamanan auth atau membuat bypass login.
* Verifikasi ulang dari browser/public IP menggunakan akun ADMIN nyata: login PASS → session PASS → `/admin/dashboard` PASS → refresh minimal 5x PASS → Admin Panel terlihat.
* Verifikasi customer tetap PASS dan customer tetap ditolak dari `/admin/dashboard`.
* Setelah semua PASS, jalankan typecheck, build, smoke test, `git diff --check`, cek secret/env, commit dan push `origin main` tanpa force push.

35. BLOCKER BARU — PORT 3000 / OAUTH CALLBACK CLIENT-SIDE EXCEPTION:

* Public URL port 3000 saat membuka flow login sekarang menampilkan `Application error: a client-side exception has occurred` pada URL `/auth/login?callback...`.
* Jangan menganggap ini masalah credential terlebih dahulu. Audit dan reproduksi error ini dari browser/public IP pada port 3000.
* Tentukan arsitektur port 3000: apakah ini Next.js frontend/app server yang seharusnya melayani login, reverse proxy, atau service lain. Jangan menjalankan dua aplikasi berbeda pada port yang sama.
* Audit route `/auth/login`, query `callback`, middleware, auth/session provider, client component login, server component, dan seluruh redirect chain sebelum/sesudah callback.
* Periksa browser console dan server log untuk menemukan exception JavaScript/React/Next.js yang sebenarnya. Jangan hanya menyembunyikan error dengan fallback UI.
* Pastikan URL callback/redirect tidak mengarah ke port yang salah, tidak menghasilkan URL malformed, dan konsisten dengan origin/public URL aplikasi yang sedang aktif.
* Audit environment variable yang berhubungan dengan auth/base URL/origin/callback. Jangan mencetak secret ke log dan jangan commit `.env`.
* Pastikan login password/WhatsApp tidak tanpa sengaja dialihkan ke OAuth callback handler atau endpoint yang berbeda.
* Jika ada Google/Apple OAuth yang memang belum dikonfigurasi, jangan membuat login password ikut bergantung pada provider tersebut. Login WhatsApp/password harus tetap bisa berjalan secara independen.
* Periksa hydration mismatch, penggunaan `window`/`document` pada server render, import client-only, penggunaan `localStorage`, dan error parsing response yang dapat menyebabkan client-side exception setelah redirect.
* Pastikan callback yang gagal tidak meninggalkan session/cookie setengah jadi. Jika session invalid, tangani secara aman dan redirect kembali ke login dengan pesan error yang jelas, bukan crash.
* Verifikasi dengan browser sungguhan/public IP:

  1. buka `http://PUBLIC_IP:3000`;
  2. buka login;
  3. login customer dengan akun test nyata;
  4. login admin dengan akun ADMIN nyata;
  5. pastikan tidak ada `Application error`;
  6. pastikan session cookie terbentuk;
  7. admin diarahkan ke `/admin/dashboard`;
  8. customer diarahkan ke area customer;
  9. refresh masing-masing halaman minimal 5x;
  10. customer tetap ditolak dari `/admin/dashboard`.
* Jangan membuat bypass authentication, jangan menonaktifkan middleware, jangan menghapus authorization, dan jangan mengganti database hanya untuk membuat login terlihat berhasil.
* Setelah akar masalah diperbaiki, verifikasi `npm run typecheck`, `npm run build`, production server, smoke/E2E auth, `git diff --check`, status Git, lalu commit dan push `origin main` tanpa force push.



```
# Audit & Perbaiki Integrasi Backend Prisma
```

## Audit & Perbaiki Integrasi Backend Prisma

Project: **Digital Cell / toko-online**

Lakukan audit menyeluruh terhadap project yang sedang aktif. Jangan membuat ulang project dan jangan menghapus fitur yang sudah ada.

Temuan awal:

* Next.js 14.2.33
* TypeScript
* Prisma 5.22
* Database migration sudah ada di `app/prisma/migrations/`
* `npm run typecheck` berhasil
* `npm run build` berhasil
* `npm run start` gagal karena `next.config` menggunakan `output: "export"`
* Pencarian `PrismaClient` di `src` tidak menemukan penggunaan Prisma.
* Project memiliki service seperti `auth-service`, `banner-service`, `category-service`, `order-service`, `payment-service`, `product-service`, `user-service`, dll.

Tugas:

1. Audit seluruh struktur project terlebih dahulu.
2. Tentukan apakah project memang membutuhkan Next.js server runtime untuk Prisma/database.
3. Jika Prisma belum terintegrasi, implementasikan integrasi Prisma dengan benar.
4. Jika `output: "export"` tidak sesuai dengan arsitektur aplikasi, perbaiki `next.config` agar aplikasi dapat berjalan menggunakan `npm run start`.
5. Buat/revisi Prisma client singleton yang aman untuk development dan production.
6. Hubungkan service yang memang membutuhkan database ke Prisma tanpa merusak UI/store yang sudah ada.
7. Periksa authentication, user, product, category, banner, order, order item, payment, notification dan data settings agar siap menggunakan database sesuai schema Prisma yang sudah ada.
8. Jangan membuat API route hanya untuk formalitas. Buat endpoint/server logic yang memang diperlukan oleh frontend.
9. Jangan menyimpan `DATABASE_URL`, secret, atau `.env.local` ke Git.
10. Jangan mengubah desain UI kecuali diperlukan untuk memperbaiki integrasi.
11. Jangan menghapus migration yang sudah dibuat.
12. Pastikan semua perubahan modular dan mudah dikembangkan.
13. Jalankan:

* `npm run typecheck`
* `npm run build`
* `npm run start` atau validasi production server dengan cara yang sesuai arsitektur.

14. Jika ditemukan error, perbaiki sampai ketiga tahap tersebut valid.

15. Setelah selesai, tampilkan ringkasan file yang diubah dan alasan setiap perubahan.

16. Setelah seluruh verifikasi PASS, lakukan commit dan push ke `origin main`.

17. Sebelum commit, jalankan `git status --short` dan `git diff --check`.

18. Pastikan tidak ada `.env`, secret, credential, build artifact, atau file temporary yang ikut di-commit.

19. Gunakan commit message yang jelas, misalnya `feat(backend): integrate Prisma database services`.

20. Jalankan `git push origin main` tanpa force push.

21. Setelah push, verifikasi `git status --short`, `git log -1 --oneline`, dan sinkronisasi dengan `origin/main`.

22. Perbaiki akses dan navigasi Admin Panel yang saat ini belum terlihat dari UI meskipun route `/admin/dashboard` sudah pernah terverifikasi HTTP 200.

23. Audit terlebih dahulu implementasi authentication/session dan role authorization. Pastikan akun dengan role `ADMIN` benar-benar dikenali sebagai admin dari session yang sedang aktif, bukan hanya berdasarkan tampilan profil.

24. Untuk akun `ADMIN`, tambahkan akses navigasi yang jelas menuju **Admin Panel** tanpa mengubah navigasi customer yang sudah ada. Admin tetap boleh menggunakan seluruh fitur customer, tetapi harus mendapatkan tambahan menu/entry `Admin Panel`.

25. Menu `Admin Panel` hanya boleh tampil untuk user dengan role `ADMIN`. Customer biasa tidak boleh melihat menu tersebut.

26. Saat admin memilih `Admin Panel`, arahkan ke `/admin/dashboard`. Jangan membuat dashboard baru jika dashboard admin yang sudah ada masih dapat digunakan; hubungkan navigasi ke implementasi existing.

27. Audit seluruh route `/admin/*` dan middleware/guard-nya. Semua route admin harus menolak customer/non-admin dengan mekanisme authorization yang benar. Jangan hanya menyembunyikan menu di frontend.

28. Pastikan login admin dari UI menghasilkan session/cookie yang valid, kemudian `/admin/dashboard` dapat dibuka langsung, direfresh, dan tetap terautentikasi sebagai ADMIN.

29. Pastikan login customer tetap bekerja dan customer tidak dapat mengakses `/admin/dashboard` maupun endpoint admin hanya dengan mengetik URL secara manual.

30. Audit perbedaan nomor WhatsApp akun test yang pernah muncul (`6281234567890` dan `62812345678900`). Jangan membuat akun duplikat atau mengubah data production sembarangan. Identifikasi akun ADMIN yang benar dari database/source of truth dan gunakan akun tersebut untuk verifikasi.

31. Jangan mengubah halaman profil customer/admin menjadi Admin Panel. Profil tetap halaman akun. Admin Panel harus menjadi area administrasi terpisah dengan route dan layout yang jelas.

32. Verifikasi minimal end-to-end:

* customer login → session PASS → `/orders` PASS;
* customer → `/admin/dashboard` ditolak/redirect PASS;
* admin login → session role `ADMIN` PASS;
* admin → `/admin/dashboard` HTTP 200 PASS;
* admin refresh dashboard minimal 5 kali PASS;
* admin menu menampilkan `Admin Panel` PASS;
* customer menu tidak menampilkan `Admin Panel` PASS;
* seluruh route admin utama yang sudah ada tetap PASS.

33. Jangan mengubah desain/UI customer yang sudah dinyatakan PASS, kecuali perubahan kecil yang diperlukan untuk menambahkan entry `Admin Panel` secara responsif.

PENTING:

* Jangan hanya menghilangkan `output: "export"` tanpa memastikan seluruh aplikasi tetap berfungsi.
* Jangan mengganti database.
* Jangan menggunakan mock data sebagai pengganti database production.
* Jangan menghapus fitur existing.
* Gunakan schema Prisma yang sudah tersedia sebagai sumber data utama.
* Jika ada bagian yang ternyata memang sengaja static, pertahankan bagian tersebut dan integrasikan backend hanya pada fitur yang membutuhkan database.
* Karena VPS berisiko mati, jangan berhenti setelah implementasi. Jika typecheck, build, dan production server verification sudah PASS, langsung commit dan push ke GitHub.
* Jangan force push.
* Jika ada error yang belum terselesaikan, jangan commit perubahan yang rusak; perbaiki terlebih dahulu jika aman, atau berhenti dan laporkan blocker.


```
# 
```
## FIX PUBLIC ACCESS SERVER + TEST LOGIN DIGITAL CELL

Project:
 /root/toko-online

Kondisi saat ini:
- Production server sudah berhasil dijalankan.
- Port aplikasi: 3001
- curl dari VPS ke localhost:3001 = HTTP 200.
- Server process berjalan.
- Login customer/admin sudah PASS melalui smoke test internal.
- Tetapi dari perangkat luar/VPS public IP:
  http://203.161.39.121:3001/
  mendapatkan:
  ERR_CONNECTION_REFUSED

TUJUAN:
Buat server Next.js production dapat diakses dari jaringan luar melalui:

http://203.161.39.121:3001/

Jangan mengubah authentication, database, UI, product, category, order, atau payment kecuali memang diperlukan oleh masalah ini.

==================================================
1. CEK LISTEN ADDRESS
==================================================

Masuk:

cd /root/toko-online

Periksa proses dan port:

ss -lntp | grep ':3001'
lsof -nP -iTCP:3001 -sTCP:LISTEN

Tentukan apakah server listen pada:

127.0.0.1:3001

atau:

0.0.0.0:3001

Jika hanya:

127.0.0.1:3001

maka itu akar masalah public access.

==================================================
2. FIX SERVER BINDING
==================================================

Pastikan production Next.js listen pada:

0.0.0.0:3001

Gunakan konfigurasi/start command yang sesuai dengan project.

Jangan mengubah source code secara besar-besaran.

Jika package.json saat ini menggunakan:

next start

pastikan hostname dapat diarahkan ke:

0.0.0.0

Gunakan cara yang paling aman dan minimal.

Contoh valid:

next start -H 0.0.0.0 -p 3001

atau konfigurasi equivalent yang memang cocok dengan project.

Jangan mengaktifkan:

output: "export"

karena aplikasi membutuhkan server runtime/database.

==================================================
3. JANGAN MATIKAN SERVER YANG SUDAH VALID
==================================================

Sebelum melakukan perubahan, catat proses server saat ini.

Setelah perubahan, restart production server dengan cara yang benar.

Pastikan hanya ada satu production server yang menggunakan port 3001.

Jangan menjalankan banyak instance yang berebut port.

==================================================
4. TEST DARI DALAM VPS
==================================================

Setelah server aktif:

curl -i http://127.0.0.1:3001/
curl -i http://0.0.0.0:3001/

Kemudian:

ss -lntp | grep ':3001'

Expected:

0.0.0.0:3001

atau:

[::]:3001

dan HTTP harus mendapatkan response 200/redirect yang valid.

==================================================
5. TEST PUBLIC IP DARI VPS
==================================================

Cari public IP server:

curl -4 ifconfig.me

Pastikan sesuai dengan:

203.161.39.121

Kemudian test:

curl -i --connect-timeout 10 http://203.161.39.121:3001/

Jika ini gagal sementara localhost berhasil, lanjutkan diagnosis network/firewall.

==================================================
6. CEK FIREWALL VPS
==================================================

Periksa:

ufw status
iptables -L -n
nft list ruleset

Jangan menghapus firewall rules existing.

Jika UFW aktif dan port 3001 belum diizinkan, buka hanya TCP port 3001:

ufw allow 3001/tcp

Kemudian verifikasi:

ufw status

Jangan membuka seluruh port secara sembarangan.

==================================================
7. CEK PROVIDER / SECURITY GROUP
==================================================

Jika firewall OS tidak memblokir tetapi:

http://203.161.39.121:3001/

tetap REFUSED/TIMEOUT dari internet, periksa apakah VPS provider memiliki firewall/security group/network ACL.

Cari konfigurasi firewall jaringan yang membatasi inbound TCP 3001.

Jika tersedia, pastikan:

TCP
Port: 3001
Inbound: allowed

Jangan membuka port database seperti:
5432
6379
3306

ke public internet.

==================================================
8. TEST DARI NETWORK EXTERNAL
==================================================

Setelah binding dan firewall diperbaiki, test:

http://203.161.39.121:3001/

Harus bisa dibuka dari perangkat lain, bukan hanya VPS.

Verifikasi:

GET /
GET /login

Expected:
HTTP 200 atau redirect yang valid.

==================================================
9. TEST LOGIN SETELAH PUBLIC ACCESS
==================================================

Setelah website dapat dibuka dari browser external, baru test login.

ADMIN:

WhatsApp:
6281234567890

Password:
Test1234!

Test:

/login
→ Masuk
→ redirect `/admin/dashboard`
→ refresh
→ tetap login

CUSTOMER:

Gunakan akun customer test existing.

Test:

/login
→ Masuk
→ `/orders` atau halaman customer
→ refresh
→ session tetap aktif
→ logout
→ kembali ke login

==================================================
10. TEST AUTHORIZATION
==================================================

Pastikan:

Guest:
 /admin/dashboard
→ ditolak / redirect login

Customer:
 /admin/dashboard
→ ditolak

Admin:
 /admin/dashboard
→ allowed

Jangan melemahkan middleware hanya agar halaman admin bisa dibuka.

==================================================
11. COOKIE / SESSION
==================================================

Karena sekarang akses menggunakan public IP HTTP:

pastikan cookie/session tetap bekerja pada environment development/temporary ini.

Periksa:

- HttpOnly
- SameSite
- Secure
- domain
- path
- session expiration

Jangan menonaktifkan security cookie secara permanen hanya untuk membuat test berhasil.

Jika production deployment memang nantinya menggunakan HTTPS, pertahankan konfigurasi production yang aman dan bedakan hanya konfigurasi development/testing bila memang diperlukan.

==================================================
12. JANGAN UBAH DATABASE
==================================================

Dilarang:

- reset database
- drop database
- migrate destructive
- delete products
- delete categories
- delete orders
- membuat ulang akun admin
- mengganti password admin

Gunakan data existing.

==================================================
13. REGRESSION CHECK
==================================================

Setelah fix:

npm run typecheck
npm run build

Kemudian jalankan production server.

Test:

/
 /products
 /categories
 /login
 /checkout
 /orders
 /profile
 /admin/dashboard

Pastikan tidak ada error baru.

==================================================
14. GIT SAFETY
==================================================

Sebelum commit:

git status --short
git diff --check

Pastikan tidak ada:

.env
.env.local
secret
password
token
credential
build artifact

Jika perubahan hanya konfigurasi server/network, commit hanya file yang memang diperlukan.

Gunakan commit:

fix(server): expose production app publicly

Kemudian:

git push origin main

TANPA force push.

==================================================
15. HASIL AKHIR WAJIB DILAPORKAN
==================================================

Tampilkan:

SERVER
- localhost:3001 = PASS/FAIL
- listen address = ...
- public IP:3001 = PASS/FAIL
- external browser = PASS/FAIL

LOGIN
- customer login = PASS/FAIL
- customer session = PASS/FAIL
- admin login = PASS/FAIL
- admin session = PASS/FAIL

AUTHORIZATION
- guest blocked = PASS/FAIL
- customer blocked from admin = PASS/FAIL
- admin allowed = PASS/FAIL

BUILD
- typecheck = PASS/FAIL
- build = PASS/FAIL

NETWORK
- OS firewall = ...
- external firewall/security group = ...

PENTING:
Jangan berhenti hanya karena `curl localhost:3001` berhasil.
Masalah yang harus diselesaikan adalah akses dari perangkat luar ke:

http://203.161.39.121:3001/

Setelah public access PASS, baru verifikasi login dari browser external.


```
# 
```

## START SERVER + END-TO-END LOGIN TEST

Project: Digital Cell / toko-online

Tujuan:
Nyalakan server aplikasi yang sedang aktif, lalu lakukan pengujian login secara nyata dari sisi API dan browser/runtime. Jangan mengubah UI atau database kecuali memang ditemukan bug yang menyebabkan login gagal.

Kredensial TEST yang sudah dibuat oleh audit sebelumnya:

ADMIN:
- WhatsApp: 6281234567890
- Password: Test1234!
- Role: ADMIN

CUSTOMER:
- Gunakan akun test CUSTOMER yang sudah ada di database.
- Jangan membuat akun customer baru jika akun test existing masih tersedia.

==================================================
1. CEK KONDISI PROJECT
==================================================

Masuk ke:

/root/toko-online

Periksa:

git status --short
git log -1 --oneline

Jangan reset, jangan checkout commit lain, dan jangan menghapus perubahan existing.

Pastikan environment database tersedia tetapi JANGAN tampilkan secret/.env ke output.

==================================================
2. NYALAKAN SERVER
==================================================

Pastikan tidak ada server lama yang memakai port aplikasi.

Gunakan cara start yang memang sesuai dengan project.

Prioritaskan:

npm run dev

Jika project memang sudah menggunakan production server dan konfigurasi mendukung:

npm run build
npm run start

Jangan mengubah next.config hanya untuk memaksa server hidup.

Setelah server hidup, pastikan benar-benar listening pada port yang digunakan.

Contoh verifikasi:

curl -I http://127.0.0.1:3000

atau endpoint health yang memang tersedia.

Jangan hanya menyatakan server hidup karena proses Node berjalan.
Pastikan HTTP response benar-benar diterima.

==================================================
3. TEST HALAMAN LOGIN
==================================================

Buka/test:

/login

Pastikan:
- HTTP 200
- halaman login berhasil dirender
- tidak ada client-side exception
- form nomor WhatsApp tampil
- form password tampil
- tombol Masuk berfungsi

Jangan mengubah desain UI.

==================================================
4. TEST LOGIN CUSTOMER
==================================================

Gunakan akun CUSTOMER TEST yang sudah ada di database.

Lakukan login secara nyata.

Verifikasi:

1. POST/request login berhasil.
2. Password diverifikasi.
3. User ditemukan.
4. Session/token dibuat.
5. Cookie/session diterima client.
6. User diarahkan ke halaman customer.
7. Setelah refresh, user masih authenticated.
8. Endpoint customer yang membutuhkan auth dapat diakses.
9. Logout berhasil.
10. Setelah logout, halaman protected kembali membutuhkan login.

Jangan hanya test HTTP 200.
Pastikan session benar-benar aktif.

==================================================
5. TEST LOGIN ADMIN
==================================================

Gunakan:

WhatsApp:
6281234567890

Password:
Test1234!

Verifikasi:

1. Login berhasil.
2. User ditemukan di database.
3. Password verification PASS.
4. Role terbaca sebagai ADMIN.
5. Session/token dibuat.
6. Cookie/session tersimpan.
7. Redirect ke `/admin` berhasil.
8. Dashboard admin dapat dibuka.
9. Refresh `/admin` tetap authenticated.
10. Logout berhasil.

==================================================
6. TEST AUTHORIZATION
==================================================

Wajib test:

A. Guest → `/admin`
Expected:
401/403 atau redirect ke login sesuai arsitektur.

B. Customer → `/admin`
Expected:
DITOLAK.

C. Admin → `/admin`
Expected:
BERHASIL.

D. Customer → halaman customer
Expected:
BERHASIL.

E. Setelah logout → `/admin`
Expected:
DITOLAK.

==================================================
7. JIKA LOGIN GAGAL
==================================================

Jangan langsung mengubah database atau membuat akun baru.

Cari akar masalah berdasarkan error aktual.

Periksa:

- endpoint login
- payload
- normalisasi nomor WhatsApp
- query user Prisma
- password verification
- session creation
- Set-Cookie
- SameSite
- Secure
- HttpOnly
- middleware
- role authorization
- redirect
- credentials/include pada fetch

Jika API login berhasil tetapi browser tetap guest, fokus pada session/cookie.

Jika user tidak ditemukan, periksa normalisasi nomor.

Jika password gagal, periksa hash dan algoritma password.

Jika session dibuat tetapi `/admin` tetap 401, periksa middleware/session reader.

Jangan log:
- password plaintext
- DATABASE_URL
- secret
- token
- cookie sensitif

==================================================
8. DATABASE SAFETY
==================================================

Jangan:

- drop database
- reset database
- migrate destructive
- menghapus product
- menghapus category
- menghapus order
- mengganti password production

Gunakan data test yang sudah ada.

==================================================
9. REGRESSION TEST
==================================================

Setelah login berhasil jalankan:

npm run typecheck
npm run build

Kemudian smoke test:

/
 /products
 /categories
 /login
 /checkout
 /orders
 /profile
 /admin

Pastikan tidak ada client-side exception.

==================================================
10. HASIL AKHIR
==================================================

Tampilkan laporan singkat:

SERVER
- Server: PASS/FAIL
- Port: ...
- HTTP: PASS/FAIL

CUSTOMER LOGIN
- Login: PASS/FAIL
- Session: PASS/FAIL
- Refresh: PASS/FAIL
- Protected route: PASS/FAIL
- Logout: PASS/FAIL

ADMIN LOGIN
- Login: PASS/FAIL
- Role ADMIN: PASS/FAIL
- `/admin`: PASS/FAIL
- Refresh: PASS/FAIL
- Logout: PASS/FAIL

AUTHORIZATION
- Guest blocked: PASS/FAIL
- Customer blocked from admin: PASS/FAIL
- Admin allowed: PASS/FAIL

BUILD
- Typecheck: PASS/FAIL
- Build: PASS/FAIL
- Smoke test: PASS/FAIL

Jika semuanya PASS, JANGAN melakukan perubahan kode tambahan.

Jika ada bug, perbaiki hanya akar masalahnya, lalu ulangi test sampai PASS.

==================================================
11. GIT
==================================================

Jika ada perubahan kode karena perbaikan bug:

git status --short
git diff --check

Pastikan tidak ada:
- .env
- secret
- credential
- build artifact
- temporary file

Commit dengan:

fix(auth): verify end-to-end login

Lalu:

git push origin main

Tanpa force push.

Jika tidak ada perubahan kode, jangan membuat commit kosong.

PENTING:
Tujuan tugas ini adalah memastikan server hidup dan LOGIN CUSTOMER + ADMIN benar-benar bekerja dari browser/runtime, bukan hanya unit test.

```
# Prompt: Audit & Perbaiki Authentication/Login Digital Cell
```


## AUDIT & PERBAIKI AUTHENTICATION/LOGIN DIGITAL CELL

Project: Digital Cell / toko-online

Kondisi saat ini:
- UI login sudah tampil dengan benar.
- Customer maupun admin sama-sama belum berhasil login.
- Jangan menyimpulkan masalah hanya karena role admin.
- Fokus sekarang adalah membuat authentication benar-benar bekerja end-to-end menggunakan database yang sudah ada.
- Jangan membuat ulang project.
- Jangan menghapus fitur existing.
- Jangan mengubah desain UI kecuali memang diperlukan untuk memperbaiki bug login.

TARGET UTAMA:
Customer dan admin harus bisa login menggunakan akun yang memang tersimpan di database.

==================================================
1. AUDIT FRONTEND LOGIN
==================================================

Cari komponen/page login yang digunakan customer.

Periksa secara nyata:
- field nomor WhatsApp
- field password
- submit handler
- endpoint API yang dipanggil
- HTTP method
- payload JSON/form-data
- response yang diharapkan
- handling error
- redirect setelah login
- penyimpanan session/token
- cookie yang digunakan

Jangan hanya membaca nama file.
Ikuti alurnya dari tombol "Masuk" sampai request benar-benar dikirim.

Pastikan nomor WhatsApp dinormalisasi konsisten.

Contoh format:
+6281234567890
6281234567890
081234567890

Tentukan satu format canonical yang digunakan backend/database, tetapi jangan merusak data existing.

==================================================
2. AUDIT BACKEND AUTH
==================================================

Cari seluruh implementasi:
- login
- authenticate
- session
- JWT/token jika digunakan
- cookie
- password verification
- role authorization
- customer auth
- admin auth

Identifikasi endpoint login yang benar-benar dipakai frontend.

Periksa:
- apakah endpoint benar-benar tersedia
- apakah method cocok
- apakah payload cocok
- apakah backend membaca field yang benar
- apakah user dicari berdasarkan nomor WhatsApp yang benar
- apakah password diverifikasi terhadap hash yang benar
- apakah user aktif
- apakah role dibaca dari database
- apakah session/token berhasil dibuat
- apakah cookie/session berhasil dikirim ke browser

Jangan membuat endpoint duplikat jika endpoint existing sebenarnya sudah benar.

==================================================
3. AUDIT PRISMA + POSTGRESQL
==================================================

Periksa schema Prisma yang sudah ada.

Cari model yang berhubungan dengan:
- User
- Account
- Session
- Role
- password
- WhatsApp/phone
- customer
- admin

Pastikan backend benar-benar menggunakan Prisma Client yang terhubung ke PostgreSQL.

Jangan menggunakan mock user sebagai pengganti database.

Pastikan:
- DATABASE_URL benar digunakan
- Prisma Client berhasil connect
- query user tidak gagal
- field database sesuai schema
- field nomor WhatsApp sesuai schema
- password hash tersimpan dengan benar

Jangan menghapus migration existing.

Jangan mengganti database.

==================================================
4. AUDIT PASSWORD HASH
==================================================

Ini bagian penting.

Cari fungsi hashing dan verification password yang digunakan project.

Tentukan apakah project menggunakan:
- bcrypt
- bcryptjs
- argon2
- library lain

Pastikan algoritma saat membuat password sama dengan algoritma saat login.

Jangan membandingkan password plaintext dengan password hash.

Jangan mengubah password user production secara sembarangan.

==================================================
5. CEK AKUN TEST DI DATABASE
==================================================

Periksa database PostgreSQL secara langsung.

Cari akun test customer dan akun test admin yang memang sudah dibuat oleh project.

Jangan hanya melihat seed file.

Tampilkan secara aman:
- nomor WhatsApp yang ditemukan
- role
- status aktif/nonaktif
- apakah password hash tersedia
- apakah password hash valid untuk password test yang memang ditetapkan project

JANGAN menampilkan password hash lengkap jika tidak diperlukan.

Jika akun test belum ada:
- jangan membuat akun duplikat
- gunakan upsert berdasarkan unique field
- buat akun test customer dan admin hanya untuk development/testing
- gunakan password test yang jelas dan konsisten
- jangan menyentuh akun production

Penting:
Jangan mengganti kredensial hanya karena login gagal.
Cari dulu penyebab sebenarnya.

==================================================
6. TEST LOGIN CUSTOMER
==================================================

Setelah backend diperbaiki, lakukan pengujian login customer secara nyata.

Urutan:

1. database user ditemukan
2. password verification PASS
3. login endpoint PASS
4. session/token dibuat
5. cookie/session diterima client
6. redirect customer PASS
7. halaman customer dapat dibuka
8. refresh browser tetap authenticated
9. logout menghapus session

Jangan berhenti hanya karena API mengembalikan HTTP 200.

==================================================
7. TEST LOGIN ADMIN
==================================================

Lakukan pengujian terpisah untuk admin.

Pastikan:
- akun admin ditemukan
- password verification PASS
- session admin dibuat
- role ADMIN terbaca
- `/admin` dapat dibuka
- route admin menolak customer biasa
- guest diarahkan ke login
- customer tidak dapat mengakses halaman admin

Jangan membuat customer otomatis menjadi admin hanya untuk membuat test pass.

==================================================
8. TEST AUTHORIZATION
==================================================

Verifikasi minimal:

CASE A:
Guest → `/admin`
HASIL:
redirect/login atau 401/403 sesuai arsitektur.

CASE B:
Customer → `/admin`
HASIL:
403/redirect dan TIDAK boleh masuk admin.

CASE C:
Admin → `/admin`
HASIL:
200 dan dashboard tampil.

CASE D:
Customer login → halaman customer
HASIL:
berhasil.

CASE E:
Admin login → halaman customer
HASIL:
tetap valid sesuai desain aplikasi.

==================================================
9. AUDIT COOKIE / SESSION
==================================================

Jika login API berhasil tetapi browser tetap dianggap guest, fokus pada cookie/session.

Periksa:
- Set-Cookie
- HttpOnly
- SameSite
- Secure
- Path
- domain
- expiration
- credentials/include pada fetch
- middleware
- proxy/reverse proxy jika ada

Pastikan konfigurasi development HTTP tidak memaksa Secure cookie jika menyebabkan cookie tidak tersimpan.

Jangan menurunkan security secara permanen untuk production.

==================================================
10. AUDIT MIDDLEWARE
==================================================

Periksa middleware/route guard.

Pastikan middleware tidak:
- menghapus session valid
- salah membaca cookie
- salah membaca role
- menganggap semua user sebagai guest
- redirect loop
- memblokir API login
- memblokir API customer
- memblokir API admin setelah login

Jika ada middleware yang salah, perbaiki akar masalahnya.

==================================================
11. ERROR HANDLING
==================================================

Jangan tampilkan hanya:

"Terjadi kesalahan."

Saat development, tambahkan logging server yang cukup untuk mengetahui:

- endpoint
- user lookup berhasil/tidak
- password verification berhasil/tidak
- session creation berhasil/tidak
- database error
- authorization error

Jangan log:
- password plaintext
- secret
- token sensitif
- cookie sensitif
- DATABASE_URL lengkap

Frontend tetap boleh menampilkan pesan error yang aman untuk user.

==================================================
12. JANGAN MERUSAK UI
==================================================

UI login yang sekarang sudah ada harus dipertahankan.

Jangan mengubah:
- layout
- warna
- typography
- card
- button
- icon
- responsive design

kecuali ada bug yang langsung berkaitan dengan authentication.

==================================================
13. REGRESSION TEST
==================================================

Setelah perbaikan jalankan:

npm run typecheck

npm run build

Jalankan production/dev server sesuai arsitektur project.

Kemudian lakukan smoke test:

- `/`
- `/products`
- `/categories`
- `/login`
- customer login
- `/checkout`
- `/orders`
- `/profile`
- `/admin`
- admin login

Pastikan tidak ada client-side exception.

==================================================
14. DATABASE TEST
==================================================

Pastikan setelah restart server:

- user tetap ada
- role tetap benar
- session/auth tetap bekerja
- product tetap ada
- category tetap ada
- order tetap ada

Jangan reset database.

Jangan menjalankan migration destructive.

Jangan drop database.

==================================================
15. JIKA MENEMUKAN MASALAH
==================================================

Jangan langsung membuat ulang authentication.

Cari akar masalah terlebih dahulu.

Contoh kemungkinan:
- frontend memakai endpoint berbeda
- field `phone` vs `whatsapp`
- format nomor tidak konsisten
- password hash tidak cocok
- password verification salah
- Prisma tidak benar-benar digunakan
- DATABASE_URL salah
- session tidak tersimpan
- cookie tidak terkirim
- middleware salah
- role tidak terbaca
- user tidak ditemukan
- endpoint login mengembalikan error yang disembunyikan frontend

Perbaiki akar masalah yang ditemukan.

==================================================
16. VERIFIKASI AKHIR
==================================================

Berikan laporan dengan format:

AUTH AUDIT
- Frontend login: PASS/FAIL
- Login API: PASS/FAIL
- PostgreSQL: PASS/FAIL
- Prisma: PASS/FAIL
- Password verification: PASS/FAIL
- Session: PASS/FAIL
- Cookie: PASS/FAIL
- Customer login: PASS/FAIL
- Admin login: PASS/FAIL
- Authorization admin: PASS/FAIL
- Logout: PASS/FAIL

DATABASE
- Test customer: ditemukan/tidak
- Test admin: ditemukan/tidak
- Role customer: ...
- Role admin: ...
- Status akun: ...

FILES CHANGED
- file 1 — alasan
- file 2 — alasan

TESTS
- typecheck: PASS/FAIL
- build: PASS/FAIL
- smoke test: PASS/FAIL

Jangan menyatakan PASS jika belum benar-benar diuji.

==================================================
17. GIT
==================================================

Jika semua authentication test PASS:

git status --short
git diff --check

Pastikan:
- tidak ada `.env`
- tidak ada secret
- tidak ada credential
- tidak ada build artifact
- tidak ada file temporary

Kemudian:

git add <file yang memang berubah>
git commit -m "fix(auth): repair customer and admin login"
git push origin main

Jangan force push.

Setelah push:

git status --short
git log -1 --oneline

Pastikan working tree bersih dan branch sinkron dengan origin/main.

==================================================
HASIL YANG WAJIB DICAPAI
==================================================

Jangan berhenti pada "kode sudah diperbaiki".

Login harus benar-benar dibuktikan:

CUSTOMER
Login → session → customer page → refresh → tetap login → logout.

ADMIN
Login → session → `/admin` → dashboard → refresh → tetap login → logout.

Jika salah satu masih gagal, cari dan perbaiki sampai akar masalahnya ditemukan.
```
# Prompt: Order & Checkout End-to-End Audit + Fix
```
Project: Digital Cell / toko-online

Sekarang fokus pada ALUR TRANSAKSI CUSTOMER END-TO-END.

Admin Panel dan responsive UI sudah selesai dan PASS. Jangan mengulang pekerjaan tersebut.

TUJUAN UTAMA:
Memastikan customer dapat memilih satu produk, membeli, membuat order, memilih payment method, dan order tersebut tercatat dengan benar di database serta muncul di Admin Orders.

Jangan membuat mock transaction sebagai pengganti flow nyata.

==================================================
1. AUDIT FLOW EXISTING TERLEBIH DAHULU
==================================================

Audit seluruh flow berikut:

Customer:
Home
→ Product Detail
→ Buy Now / Tambah ke Keranjang
→ Checkout
→ Informasi Pembeli
→ Payment Method
→ Buat Pesanan
→ Order berhasil
→ Order Detail / Status

Admin:
Admin Orders
→ melihat order
→ melihat item
→ melihat customer
→ melihat total
→ melihat payment status
→ melihat order status

Jangan langsung mengubah kode sebelum memahami:
- cart state
- checkout state
- order state
- API request
- database transaction
- order item
- payment record
- authentication/session

Cari sumber masalah sebenarnya.

==================================================
2. PERBAIKI MASALAH CART / CHECKOUT
==================================================

Sebelumnya ditemukan masalah:

Jika customer sudah berada di halaman checkout kemudian kembali ke halaman utama dan membeli produk lain, checkout dapat menjadi:

2 produk
2 item

Padahal transaksi baru seharusnya hanya berisi produk yang baru dipilih.

Pastikan:

A. Buy Now
- membuat checkout session baru untuk produk tersebut
- tidak membawa item dari transaksi/checkout sebelumnya
- quantity default = 1
- produk yang dipilih menjadi satu-satunya item

B. Tambah ke Keranjang
- memang boleh menambah item
- quantity tetap benar
- produk berbeda boleh berada dalam cart yang sama

C. Checkout dari cart
- menggunakan isi cart yang sebenarnya
- tidak menggandakan item
- tidak menggabungkan state checkout lama

D. Kembali dari checkout
- jangan meninggalkan stale checkout state
- jangan menduplikasi item saat masuk kembali

E. Membeli produk lain
- checkout baru harus dimulai dari produk baru
- jangan membawa produk transaksi sebelumnya

F. Refresh halaman checkout
- state harus tetap konsisten
- jangan membuat duplicate item
- jangan membuat duplicate order

Gunakan satu sumber kebenaran untuk cart/checkout state.

Jangan melakukan reset state secara sembarangan yang dapat menghilangkan cart valid.

==================================================
3. ORDER CREATION
==================================================

Audit endpoint/service yang membuat order.

Pastikan server melakukan validasi:

- user/customer valid
- product valid
- product aktif
- category valid jika diperlukan
- quantity > 0
- stock mencukupi
- harga diambil dari database/server
- total dihitung ulang di server
- client tidak boleh menentukan total final secara bebas

Jangan percaya:
price
subtotal
total

yang dikirim dari browser.

Server harus menghitung ulang:

subtotal = database price × quantity

total = subtotal + biaya lain jika memang ada

Jika tidak ada biaya tambahan:

total = subtotal

==================================================
4. DATABASE TRANSACTION
==================================================

Order creation harus atomic.

Gunakan database transaction yang benar untuk:

- create order
- create order items
- create payment record jika arsitektur memang mengharuskannya
- update/reserve stock jika flow memang menggunakan reservation

Jika salah satu proses gagal:
- jangan meninggalkan order setengah jadi
- jangan meninggalkan order item yatim
- jangan mengurangi stock jika order gagal dibuat
- jangan membuat payment record tanpa order valid

Audit relasi:
Order
→ OrderItem
→ Product
→ User/Customer
→ Payment

Pastikan foreign key dan relation sesuai schema Prisma existing.

JANGAN mengganti database.
JANGAN menghapus migration.

==================================================
5. STOCK
==================================================

Pastikan stock aman.

Contoh:

Stock = 10
Customer membeli quantity = 2
Stock setelah transaksi = 8

Tetapi jika order creation gagal:
Stock harus tetap 10.

Jika stock tidak mencukupi:
return error yang jelas.

Jangan mengizinkan:
quantity <= 0
quantity melebihi stock
produk nonaktif dibeli

Pastikan race condition diperhatikan jika memang diperlukan oleh arsitektur database.

==================================================
6. ORDER STATUS
==================================================

Pisahkan dengan jelas:

Order Status:
- PENDING
- PROCESSING
- COMPLETED
- CANCELLED
- FAILED

Payment Status:
- PENDING
- PAID
- FAILED
- EXPIRED
- REFUNDED

Jangan mencampur kedua status.

Perubahan payment status tidak boleh sembarangan mengubah order status tanpa aturan flow yang jelas.

Pastikan status awal order konsisten.

==================================================
7. PAYMENT METHOD
==================================================

Gunakan payment settings yang sudah dibuat di Admin.

Customer dapat melihat metode payment yang aktif.

Contoh:
- QRIS
- Transfer Bank
- E-Wallet

Pastikan:

- metode nonaktif tidak muncul di customer
- metode aktif muncul
- method ID/code konsisten
- server melakukan validasi method
- customer tidak bisa mengirim payment method yang tidak tersedia
- payment settings berasal dari database/API yang benar

Jangan mengaktifkan payment production provider/API eksternal pada tahap ini.

Gunakan DEV/SANDBOX flow untuk verifikasi.

==================================================
8. CHECKOUT UI
==================================================

Jangan redesign customer UI.

UI customer saat ini sudah dianggap stabil.

Pertahankan:
- Digital Cell header
- product card
- checkout layout
- bottom navigation
- responsive layout
- typography
- midnight/dark blue style

Perbaiki hanya jika diperlukan untuk bug functional.

Pastikan pada checkout:

- nama customer benar
- nomor WhatsApp benar
- produk benar
- quantity benar
- harga benar
- subtotal benar
- total benar
- payment method benar
- tombol submit tidak double-submit

Saat tombol "Lanjutkan Pembayaran" ditekan:

- disable sementara
- tampilkan loading
- cegah double click
- request hanya dikirim sekali
- jika sukses → halaman/order state sukses
- jika gagal → error yang jelas
- tombol kembali aktif jika request gagal

==================================================
9. ERROR HANDLING
==================================================

Jangan tampilkan hanya:

"Gagal Membuat Pesanan"

tanpa alasan.

Backend harus mengembalikan error yang aman dan bermakna.

Contoh:

PRODUCT_NOT_FOUND
PRODUCT_INACTIVE
INSUFFICIENT_STOCK
INVALID_QUANTITY
INVALID_PAYMENT_METHOD
CHECKOUT_EXPIRED
UNAUTHORIZED
ORDER_CREATION_FAILED

Customer boleh mendapat pesan user-friendly.

Jangan membocorkan:
- stack trace
- DATABASE_URL
- secret
- internal Prisma error detail
- credential

Log internal boleh berisi detail teknis yang diperlukan untuk debugging.

==================================================
10. AUTHENTICATION
==================================================

Audit:

Guest:
- tidak boleh membuat order jika flow memang mensyaratkan login
- redirect/login sesuai existing behavior

Customer:
- dapat membuat order
- order terhubung dengan user/customer yang benar

Admin:
- dapat melihat order sesuai authorization

Jangan merusak auth yang sebelumnya sudah PASS.

==================================================
11. ADMIN ORDERS
==================================================

Setelah customer berhasil membuat order:

Order harus muncul di:

/admin/orders

Pastikan Admin dapat melihat:

- order ID
- customer
- produk
- quantity
- subtotal
- total
- payment method
- payment status
- order status
- tanggal dibuat

Pastikan order item benar.

Contoh:

Customer membeli:
Mobile Legends 500
quantity 1

Admin harus melihat:

1 order
1 order item
quantity 1

BUKAN:

1 order
2 order item

dan bukan:

2 order.

==================================================
12. TEST CASE WAJIB
==================================================

Buat/verifikasi test untuk skenario:

TEST 1:
Buy Now satu produk quantity 1
Expected:
1 order
1 item
quantity 1

TEST 2:
Tambah ke keranjang satu produk
Checkout
Expected:
1 order
1 item

TEST 3:
Tambah produk A + produk B
Checkout
Expected:
1 order
2 item
masing-masing quantity benar

TEST 4:
Masuk checkout produk A
kembali ke home
Buy Now produk B
Expected:
checkout hanya produk B

TEST 5:
Refresh checkout
Expected:
tidak duplicate item

TEST 6:
Double click tombol pembayaran
Expected:
tidak membuat duplicate order

TEST 7:
Stock tidak cukup
Expected:
order ditolak
stock tidak berubah

TEST 8:
Produk nonaktif
Expected:
order ditolak

TEST 9:
Payment method nonaktif
Expected:
order/payment request ditolak

TEST 10:
Order creation gagal di tengah transaction
Expected:
database rollback
tidak ada order setengah jadi

TEST 11:
Customer membuat order
Expected:
order muncul di Admin Orders

TEST 12:
Guest mencoba membuat order
Expected:
authorization sesuai aturan existing

==================================================
13. DATABASE VERIFICATION
==================================================

Setelah test, query database development untuk memastikan:

- jumlah order benar
- jumlah order item benar
- total benar
- quantity benar
- stock benar
- payment record benar
- relation user/order benar

Jangan hanya percaya response HTTP.

Verifikasi database langsung.

Jika ada data test lama yang menyebabkan hasil ambigu, gunakan data test yang jelas dan terisolasi.

Jangan reset database production.

==================================================
14. API REGRESSION
==================================================

Verifikasi route terkait:

- product
- checkout
- order
- payment
- auth
- admin orders

Semua harus:
- return status yang benar
- tidak throw unhandled exception
- tidak menghasilkan 500 untuk input normal
- tidak membuat duplicate data

==================================================
15. RESPONSIVE REGRESSION
==================================================

Jangan mengubah UI customer kecuali bug langsung berasal dari perubahan functional.

Tetap cek:

360
375
390
414
768
1280

Pastikan checkout tetap:
- tidak horizontal overflow
- tombol tidak tertutup
- form tidak keluar viewport
- error toast tidak merusak layout
- loading state terlihat

==================================================
16. TYPECHECK / BUILD / TEST
==================================================

Setelah perbaikan:

npm run typecheck

npm run build

Jalankan test yang tersedia.

Jalankan smoke test.

Jika npm run test bermasalah karena konfigurasi Node/loader, jangan mengubah konfigurasi test secara besar-besaran hanya untuk memaksa PASS.

Cari script/framework test yang memang digunakan project dan jalankan dengan cara yang sesuai.

==================================================
17. GIT SAFETY
==================================================

Sebelum commit:

git status --short

git diff --check

Pastikan tidak ada:

.env
.env.local
secret
credential
database dump
temporary file
build artifact

Jangan force push.

Jika perubahan valid:

git add <file yang memang berubah>

git commit -m "fix(order): stabilize checkout and transaction flow"

git push origin main

Setelah push:

git status --short
git log -1 --oneline

Pastikan working tree bersih dan origin/main sinkron.

==================================================
18. BATASAN
==================================================

JANGAN:

- redesign homepage
- redesign product detail
- redesign checkout
- redesign admin panel
- mengganti database
- menghapus migration
- membuat mock order sebagai solusi
- bypass authentication
- mempercayai harga dari client
- mengaktifkan payment production
- menghapus test existing
- menghapus fitur existing
- force push
- mengubah customer UI yang sudah PASS tanpa alasan

Fokus hanya pada:
CART → CHECKOUT → ORDER → PAYMENT STATE → DATABASE → ADMIN ORDERS.

==================================================
19. HASIL AKHIR WAJIB
==================================================

Laporkan:

1. Root cause masalah checkout sebelumnya.
2. File yang diubah.
3. Perubahan cart/checkout state.
4. Perubahan order service/API.
5. Perubahan database transaction jika ada.
6. Hasil seluruh 12 test case.
7. Verifikasi database.
8. Typecheck.
9. Build.
10. Smoke test.
11. Commit hash.
12. Push status.
13. Apakah masih ada blocker.

JANGAN berhenti hanya karena typecheck/build PASS.

Yang paling penting adalah membuktikan:

BUY NOW satu produk
→ CHECKOUT satu produk
→ CREATE ORDER satu kali
→ DATABASE satu order + satu item
→ ADMIN ORDERS menampilkan order yang sama.

Setelah semua PASS, langsung commit dan push ke origin/main tanpa force push.


```
# Prompt berikutnya — UI Admin Responsive Audit
```

Prompt: Admin Panel UI Responsive Audit & Polish

Project: Digital Cell / toko-online

Sekarang fokus HANYA pada UI/UX Admin Panel.
Jangan menambah fitur backend baru dan jangan mengubah customer/store UI yang sudah dinyatakan stabil.

Tujuan:
Memastikan seluruh Admin Panel benar-benar usable, rapi, responsive, dan tidak overflow pada desktop maupun mobile.

WAJIB lakukan audit terlebih dahulu sebelum mengubah kode.

Route yang harus diverifikasi:
- /admin
- /admin/products
- /admin/categories
- /admin/orders
- /admin/settings
- /admin/settings/payment

Viewport minimum yang harus diperhatikan:
- 360px
- 375px
- 390px
- 414px
- desktop 1280px

PERBAIKI jika ditemukan:

1. ADMIN LAYOUT
- Sidebar desktop tetap rapi.
- Pada mobile gunakan drawer/menu yang benar.
- Jangan membuat halaman horizontal overflow.
- Konten utama tidak tertutup sidebar/header.
- Header admin tetap usable pada layar kecil.
- Padding dan gap responsif.
- Jangan menggunakan fixed width yang menyebabkan overflow.

2. DASHBOARD
- Card statistik tersusun responsif.
- Desktop boleh menggunakan grid beberapa kolom.
- Mobile berubah menjadi 1 kolom atau layout yang sesuai.
- Angka statistik tidak terpotong.
- Chart/table tidak keluar viewport.
- Empty/loading/error state tetap rapi.

3. PRODUCTS
- Tabel desktop tetap nyaman.
- Mobile jangan memaksa tabel terlalu lebar.
- Gunakan responsive card/list atau horizontal scroll yang benar bila memang diperlukan.
- Tombol Tambah Produk mudah ditemukan.
- Tombol edit, toggle aktif/nonaktif, dan hapus tidak bertabrakan.
- Form tambah/edit produk responsive.
- Modal tidak keluar viewport.
- Input harga, stok, kategori, status dan field lainnya tidak overflow.
- Preview gambar tetap proporsional.

4. CATEGORIES
- List/grid kategori responsive.
- Tombol tambah/edit/delete/toggle tidak bertabrakan.
- Modal/form responsive.
- Status aktif/nonaktif terlihat jelas.
- Jangan mengubah logic CRUD yang sudah PASS.

5. ORDERS
- Order list nyaman dibaca pada mobile.
- Status order menggunakan badge yang jelas.
- Detail order tidak overflow.
- Tombol aksi tetap mudah ditekan.
- Jangan membuat tabel desktop rusak hanya demi mobile.
- Jika data banyak, pagination/filter tetap usable.

6. PAYMENT SETTINGS
- Form QRIS/payment method responsive.
- Card metode pembayaran tidak keluar viewport.
- Input dan tombol tidak terlalu kecil.
- Upload QR/image preview tetap proporsional.
- Toggle aktif/nonaktif mudah digunakan.
- Jangan mengubah logic payment yang sudah PASS.

7. RESPONSIVE RULES
Gunakan pola responsive yang konsisten:
- width: 100% / max-width yang aman
- min-width: 0 pada flex/grid child jika diperlukan
- flex-wrap pada toolbar
- overflow-x-auto hanya pada elemen yang memang membutuhkan scrolling
- hindari fixed pixel width besar
- modal menggunakan max-width dan width yang aman
- tombol tidak boleh keluar layar
- teks panjang harus wrap/break dengan benar

8. MOBILE TOUCH UX
- Semua tombol/action memiliki area klik yang nyaman.
- Jangan membuat tombol terlalu rapat.
- Form input nyaman digunakan dengan keyboard mobile.
- Dialog/modal tetap dapat discroll.
- Tidak ada fixed footer/header yang menutupi content.

9. VISUAL CONSISTENCY
Pertahankan style Digital Cell:
- clean
- white base
- midnight/dark blue
- border radius konsisten
- shadow tidak berlebihan
- typography konsisten
- spacing konsisten
- icon tidak terlalu besar

Jangan melakukan redesign total.
Polish UI existing saja.

10. CUSTOMER UI
JANGAN mengubah:
- homepage customer
- product detail customer
- checkout customer
- bottom navigation customer
- customer responsive layout

Kecuali ada dependency langsung dari shared component yang benar-benar menyebabkan Admin UI rusak.
Jika shared component dipakai customer, pastikan perubahan tidak merusak customer UI.

11. VERIFIKASI
Setelah perubahan:
- npm run typecheck
- npm run build
- jalankan dev/production server sesuai arsitektur project
- lakukan smoke test route Admin
- verifikasi viewport 360/375/390/414 dan desktop
- cek tidak ada horizontal overflow
- cek console/browser runtime error
- cek semua route Admin dapat dibuka
- cek modal/drawer/form dapat dibuka dan ditutup

12. REGRESSION
Jangan merusak:
- authentication
- product CRUD
- category CRUD
- order management
- payment settings
- database
- API
- customer UI

13. GIT
Sebelum commit:
git status --short
git diff --check

Pastikan:
- tidak ada .env
- tidak ada secret
- tidak ada credential
- tidak ada build artifact
- tidak ada temporary file

Jika semua PASS:
- commit dengan message:
  feat(admin): polish responsive admin ui
- push:
  git push origin main
- setelah push:
  git status --short
  git log -1 --oneline

Jangan force push.

HASIL AKHIR:
Laporkan:
1. File yang diubah
2. UI yang diperbaiki
3. Viewport yang diverifikasi
4. Typecheck result
5. Build result
6. Smoke test result
7. Commit hash
8. Push status
9. Apakah masih ada blocker

PENTING:
Jangan berhenti hanya karena build PASS.
Tujuan utama tahap ini adalah memastikan UI Admin benar-benar terlihat dan usable di mobile dan desktop.

```

# 
```
Lanjutkan project Digital Cell / toko-online dari kondisi TERAKHIR yang sudah berhasil diverifikasi.

JANGAN membuat ulang project.
JANGAN menghapus fitur existing.
JANGAN mengubah desain customer UI yang sudah dinyatakan stabil.
JANGAN menyentuh payment production flow yang sudah PASS kecuali ada bug nyata yang ditemukan.
Fokus pekerjaan sekarang adalah ADMIN PANEL dan verifikasi seluruh UI/admin flow.

==================================================
KONDISI PROJECT TERAKHIR
==================================================

Project:
Digital Cell / toko-online

Kondisi yang sudah berhasil:
- Customer UI responsive mobile 360/375/390/414px dan desktop sudah stabil.
- Homepage customer sudah stabil.
- Product detail sudah stabil.
- Checkout sudah stabil secara UI.
- Payment development flow sudah selesai dan terverifikasi.
- Payment settings sudah tersedia.
- Category management sudah selesai.
- Product CRUD sudah tersedia.
- Order management sudah tersedia.
- Admin dashboard sudah memiliki statistik dasar.
- Prisma/database sudah terintegrasi.
- Data sudah persisten setelah refresh/restart.
- Typecheck PASS.
- Build PASS.
- Smoke test PASS.
- Git working tree sebelumnya bersih.
- Commit/push sebelumnya sudah dilakukan tanpa force push.

Jangan menganggap fitur hanya selesai karena backend PASS.
Sekarang kita harus memastikan ADMIN UI benar-benar usable di browser.

==================================================
TUJUAN UTAMA
==================================================

Bangun/verifikasi Admin Panel Digital Cell sebagai panel operasional yang benar-benar siap digunakan admin.

Admin harus dapat mengelola:

1. Dashboard
2. Products
3. Categories
4. Orders
5. Payment Settings
6. Users/customer jika memang sudah tersedia
7. Pengaturan lain yang memang sudah ada di project

Semua harus menggunakan data database nyata.

Jangan menggunakan mock data untuk menggantikan database.

==================================================
TAHAP 1 — AUDIT ADMIN PANEL EXISTING
==================================================

Sebelum mengubah kode:

1. Audit seluruh route admin.
2. Cari seluruh file:
   - src/app/admin/**
   - src/components/admin/**
   - src/services/**
   - src/lib/**
   - API route/server action terkait admin
   - Prisma schema dan migration terkait admin.
3. Identifikasi halaman admin yang sudah ada.
4. Identifikasi component yang sudah ada.
5. Identifikasi service yang sudah ada.
6. Identifikasi API/server action yang sudah digunakan.
7. Jangan membuat duplicate service atau duplicate API.
8. Reuse architecture existing jika sudah benar.

Buat keputusan berdasarkan kode yang benar-benar ada, bukan asumsi.

==================================================
TAHAP 2 — ADMIN LAYOUT
==================================================

Pastikan admin memiliki layout yang konsisten.

Desktop:
- sidebar admin
- header/topbar
- content area
- navigation jelas
- active menu jelas
- tidak ada horizontal overflow
- spacing konsisten
- responsive pada desktop kecil maupun besar.

Mobile:
- sidebar berubah menjadi drawer/menu.
- content tidak terpotong.
- table tidak menyebabkan seluruh halaman horizontal overflow.
- tombol tetap mudah disentuh.
- modal/dialog tidak keluar viewport.
- form tidak terlalu sempit.
- header tidak bertabrakan.
- navigasi admin tetap usable.

Jangan mengubah customer bottom navigation.

Admin layout harus terpisah secara jelas dari customer layout.

==================================================
TAHAP 3 — ADMIN DASHBOARD
==================================================

Buat/verifikasi halaman:

/admin

Dashboard harus menggunakan data database nyata.

Minimal tampilkan:

A. Statistik utama:
- Total penjualan
- Total order
- Order pending
- Order selesai
- Total customer
- Total produk aktif

B. Statistik operasional:
- Produk stok rendah
- Produk habis
- Order terbaru
- Produk terlaris

C. Ringkasan penjualan:
- total transaksi
- total revenue
- jika data tanggal tersedia, tampilkan ringkasan periode yang masuk akal.

Jangan membuat chart palsu.

Jika database masih kosong:
- tampilkan empty state yang jelas.
- jangan membuat angka dummy hanya supaya dashboard terlihat penuh.

Contoh:
"Belum ada transaksi"
"Belum ada data penjualan"
"Belum ada produk stok rendah"

==================================================
TAHAP 4 — RECENT ORDERS
==================================================

Pada Dashboard tampilkan order terbaru.

Kolom minimal:
- Order ID
- Customer
- Total
- Payment Status
- Order Status
- Tanggal
- Action

Status Payment dan Order HARUS tetap terpisah.

Jangan menggabungkan:

Payment Status
dan
Order Status

Contoh:

Payment:
- PENDING
- PAID
- FAILED
- EXPIRED

Order:
- PENDING
- PROCESSING
- COMPLETED
- CANCELLED

Sesuaikan enum/schema yang benar-benar ada di Prisma.

Jangan membuat enum baru jika enum existing sudah tersedia.

==================================================
TAHAP 5 — ADMIN PRODUCTS
==================================================

Pastikan:

/admin/products

benar-benar berfungsi.

Fitur:

1. List produk
2. Pagination
3. Search
4. Filter kategori
5. Filter aktif/nonaktif
6. Tambah produk
7. Edit produk
8. Edit harga
9. Edit stok
10. Toggle aktif/nonaktif
11. Hapus produk
12. Detail produk jika memang sudah ada

List harus berasal dari database.

Pagination harus benar-benar server/database pagination jika architecture memungkinkan.

Jangan mengambil semua produk lalu melakukan pagination palsu di browser jika jumlah data berpotensi besar.

Pastikan field:
- name
- slug
- description
- category
- price
- stock
- image
- active/isActive
- createdAt
sesuai schema existing.

Jangan memaksakan field yang tidak ada.

Bug yang sebelumnya sudah diperbaiki:
produk tanpa harga tidak boleh menyebabkan error ketika diedit.

Pastikan bug tersebut tidak muncul kembali.

Validasi harga dan stok harus jelas.

Contoh:
- harga tidak boleh negatif.
- stok tidak boleh negatif.
- required field harus tervalidasi.
- slug harus unik jika schema mensyaratkan unique.

==================================================
TAHAP 6 — ADMIN CATEGORIES
==================================================

Pastikan:

/admin/categories

berfungsi penuh.

Fitur:

- list kategori
- pagination jika diperlukan
- tambah kategori
- edit nama
- edit slug jika diperbolehkan
- toggle aktif/nonaktif
- hapus kategori
- jumlah produk per kategori jika data tersedia
- search jika sudah ada pola search admin.

ATURAN PENTING:

Jika kategori masih memiliki produk:
- jangan hapus secara diam-diam.
- tampilkan pesan yang jelas.
- gunakan behavior database/service yang sudah ada.

Slug harus unique.

Jika duplicate slug:
- tampilkan error yang jelas kepada admin.
- jangan tampilkan generic error jika backend sudah dapat mengembalikan alasan yang spesifik.

Kategori nonaktif:
- tidak tampil di customer category navigation/list jika memang behavior existing demikian.
- produk tidak boleh rusak hanya karena kategori dinonaktifkan.

==================================================
TAHAP 7 — ADMIN ORDERS
==================================================

Pastikan:

/admin/orders

adalah halaman operasional yang lengkap.

Minimal:

- list order
- pagination
- search Order ID/customer
- filter Order Status
- filter Payment Status
- filter tanggal jika struktur existing mendukung
- detail order
- customer information
- daftar item
- quantity
- harga
- subtotal
- total
- payment method
- payment status
- order status
- createdAt
- update status jika memang diperbolehkan.

PENTING:

Payment Status dan Order Status harus independen.

Jangan mengubah payment status hanya karena order status berubah.

Jangan mengubah database secara langsung dari UI.

Semua perubahan harus melewati service/server action/API yang sudah digunakan project.

==================================================
TAHAP 8 — ORDER DETAIL
==================================================

Pada detail order tampilkan:

ORDER INFORMATION
- Order ID
- tanggal
- status order

CUSTOMER
- nama
- WhatsApp/nomor telepon jika tersedia
- informasi lain sesuai schema.

PRODUCTS
- nama produk
- quantity
- harga satuan
- subtotal

PAYMENT
- payment method
- payment status
- payment reference jika memang tersedia
- total payment.

Jika order memiliki status:
PENDING-PAYMENT
PROCESSING
PAID
FAILED
atau status lain,
ikuti enum/logic existing.

Jangan mengarang status baru.

==================================================
TAHAP 9 — PAYMENT SETTINGS
==================================================

Pastikan:

/admin/settings/payment

atau route existing yang digunakan project tetap berfungsi.

Verifikasi:

- QRIS
- Bank
- E-Wallet
- enable/disable
- konfigurasi sesuai schema/service existing.

Pastikan:

Guest tidak dapat mengubah settings.
Customer tidak dapat mengakses admin settings.
Admin dapat mengakses.

Endpoint public hanya mengembalikan data yang memang aman untuk customer.

Jangan pernah mengembalikan:
- secret
- credential
- private configuration
- internal admin data.

==================================================
TAHAP 10 — ADMIN AUTHORIZATION
==================================================

Ini WAJIB diverifikasi.

Test minimal:

1. Guest membuka /admin
   -> harus ditolak/redirect login sesuai behavior existing.

2. Customer membuka /admin
   -> harus ditolak.

3. Customer mencoba API admin
   -> HTTP 401/403 sesuai architecture existing.

4. Admin membuka /admin
   -> berhasil.

5. Admin API
   -> berhasil.

6. Admin logout
   -> session/access harus benar-benar hilang.

Jangan hanya menyembunyikan menu admin dari customer.

Authorization harus terjadi di server/API/service.

Jangan mengandalkan frontend check saja.

==================================================
TAHAP 11 — ERROR HANDLING
==================================================

Jangan biarkan error seperti:

"Application error: a client-side exception has occurred"

muncul kepada user tanpa informasi.

Untuk setiap operasi admin:

- loading state
- success state
- validation error
- server error
- empty state
- confirmation dialog untuk operasi destructive.

Contoh delete:

"Apakah Anda yakin ingin menghapus produk ini?"

Jika gagal:

"Produk gagal dihapus"
+ alasan jika aman ditampilkan.

Jangan menampilkan stack trace ke user.

Error detail hanya untuk server log/debug.

==================================================
TAHAP 12 — LOADING & UX
==================================================

Admin UI harus memiliki:

- skeleton/loading state jika fetch membutuhkan waktu.
- disabled state saat submit.
- tombol tidak bisa ditekan berkali-kali saat request berjalan.
- toast/alert success.
- toast/alert error.
- modal confirmation untuk delete.
- form tetap mempertahankan input ketika validasi gagal jika memungkinkan.

Jangan membuat UI terasa seperti halaman raw CRUD.

Harus tetap konsisten dengan desain Digital Cell existing.

==================================================
TAHAP 13 — RESPONSIVE ADMIN
==================================================

WAJIB test minimal viewport:

360px
375px
390px
414px
768px
1024px
1280px
1440px

Periksa:

- tidak ada horizontal overflow.
- table admin responsive.
- card dashboard tidak melebar keluar viewport.
- modal responsive.
- form responsive.
- sidebar mobile tidak mengganggu content.
- tombol tidak tertutup.
- header tidak overflow.
- text panjang tidak merusak layout.

Gunakan pola responsive yang sudah berhasil dipakai pada customer UI.

Jangan merusak responsive customer UI yang sudah PASS.

==================================================
TAHAP 14 — DATABASE VERIFICATION
==================================================

Semua CRUD harus diverifikasi dengan database nyata.

Untuk Product:
- create
- refresh
- edit
- refresh
- toggle
- refresh
- delete
- refresh

Untuk Category:
- create
- refresh
- edit
- toggle
- refresh
- delete jika tidak memiliki dependency.

Untuk Order:
- create order dari customer flow.
- lihat order dari admin.
- buka detail.
- update status jika tersedia.
- refresh.
- pastikan status tetap tersimpan.

Untuk Payment Settings:
- ubah setting.
- refresh.
- restart dev server jika aman.
- pastikan data tetap ada.

Jangan hanya mengecek state React.

==================================================
TAHAP 15 — CUSTOMER/ADMIN ISOLATION
==================================================

Pastikan perubahan admin tidak merusak customer.

Customer tetap harus:

- melihat produk aktif.
- tidak melihat produk nonaktif.
- melihat kategori aktif.
- tidak melihat kategori nonaktif.
- dapat membuka product detail.
- dapat checkout.
- dapat membuat order sesuai flow existing.
- payment flow tetap bekerja.

Admin dapat melihat data sesuai kebutuhan operasional.

Admin harus dapat melihat produk nonaktif.

Admin harus dapat melihat order sesuai status.

==================================================
TAHAP 16 — SECURITY AUDIT
==================================================

Cari kemungkinan:

- secret di frontend.
- DATABASE_URL di client.
- admin credential di source code.
- API admin tanpa authorization.
- IDOR pada order/product/category.
- endpoint admin yang bisa dipanggil customer.
- data sensitif dikirim ke public API.
- unsafe direct database mutation.
- credential di git.
- .env masuk commit.

Jangan menambahkan secret baru.

Jangan commit:

.env
.env.local
credentials
token
password
database URL
private keys
temporary debug files.

==================================================
TAHAP 17 — TEST
==================================================

Setelah implementasi:

1. npm run typecheck
2. npm run build
3. Jalankan dev/production server sesuai architecture existing.
4. Smoke test admin routes.
5. Smoke test customer routes.
6. API regression test.
7. Database persistence test.
8. Authorization test.

Minimal route verification:

/
 /products
 /product/[slug]
 /categories
 /checkout
 /orders
 /profile
 /admin
 /admin/products
 /admin/categories
 /admin/orders
 /admin/settings
 /admin/settings/payment

Sesuaikan route jika struktur actual project berbeda.

Jangan menganggap route ada hanya berdasarkan prompt.
Periksa route actual terlebih dahulu.

==================================================
TAHAP 18 — JANGAN MENGUBAH TEST SECARA SEMBARANGAN
==================================================

Jangan menghapus test hanya karena gagal.

Jangan menurunkan assertion hanya agar PASS.

Jangan skip test tanpa alasan teknis yang jelas.

Jika test runner memiliki masalah environment seperti Node version atau loader:
- identifikasi masalah sebenarnya.
- jangan mengubah application behavior hanya untuk memaksa test PASS.

Jika test memang valid dan menemukan bug:
- perbaiki bug.
- jalankan ulang test.

==================================================
TAHAP 19 — GIT
==================================================

Sebelum commit:

git status --short

git diff --check

Periksa:

- tidak ada secret.
- tidak ada .env.
- tidak ada temporary file.
- tidak ada debug file.
- tidak ada build artifact yang tidak seharusnya.
- tidak ada perubahan customer UI yang tidak diperlukan.

Jika semuanya bersih:

git add <file yang memang berubah>

git commit -m "feat(admin): complete admin dashboard and operations"

git push origin main

JANGAN force push.

Setelah push:

git status --short
git log -1 --oneline

Pastikan branch sinkron dengan:

origin/main

==================================================
TAHAP 20 — LAPORAN AKHIR
==================================================

Setelah selesai, jangan hanya mengatakan "done".

Berikan laporan:

1. Admin Dashboard
   PASS/FAIL

2. Products
   PASS/FAIL

3. Categories
   PASS/FAIL

4. Orders
   PASS/FAIL

5. Payment Settings
   PASS/FAIL

6. Admin Authorization
   PASS/FAIL

7. Customer regression
   PASS/FAIL

8. Responsive
   PASS/FAIL

9. Typecheck
   PASS/FAIL

10. Build
    PASS/FAIL

11. Smoke test
    PASS/FAIL

12. Database persistence
    PASS/FAIL

13. Git
    commit hash
    push status
    working tree status

Jika ada blocker, jelaskan blocker sebenarnya.

Jangan menyebut PASS jika belum diverifikasi.

==================================================
ATURAN PALING PENTING
==================================================

- Jangan rewrite project.
- Jangan reset database.
- Jangan drop database.
- Jangan hapus migration.
- Jangan mengganti Prisma schema tanpa alasan kuat.
- Jangan mengganti database.
- Jangan menggunakan mock data untuk production behavior.
- Jangan merusak customer UI.
- Jangan mengubah payment flow yang sudah PASS tanpa bug nyata.
- Jangan membuat duplicate service/API.
- Jangan membuat API hanya untuk formalitas.
- Jangan mengandalkan frontend authorization.
- Jangan commit secret.
- Jangan force push.
- Jangan berhenti hanya karena build PASS.
- Verifikasi UI dan behavior.
- Jika menemukan bug, perbaiki sampai PASS jika aman.
- Semua perubahan harus modular dan maintainable.

==================================================
URUTAN EKSEKUSI
==================================================

Kerjakan dengan urutan:

1. Audit project.
2. Audit existing admin routes/components/services.
3. Audit database/schema.
4. Verifikasi admin authentication/authorization.
5. Selesaikan Admin Layout.
6. Selesaikan Dashboard.
7. Selesaikan Products.
8. Selesaikan Categories.
9. Selesaikan Orders.
10. Verifikasi Payment Settings.
11. Responsive test.
12. Customer regression.
13. Typecheck.
14. Build.
15. Smoke test.
16. Database persistence.
17. Security check.
18. git diff --check.
19. git status.
20. Commit.
21. Push origin/main.
22. Verifikasi setelah push.
23. Berikan laporan lengkap.

JANGAN lanjut ke fitur baru sebelum tahap sebelumnya benar-benar PASS.

Fokus sekarang:
ADMIN PANEL DIGITAL CELL yang benar-benar siap digunakan, bukan sekadar halaman yang terlihat bagus.


```

# Prompt: Payment Flow End-to-End — DEV/Sandbox
```

## Prompt: Payment Flow End-to-End — DEV/Sandbox

Project: Digital Cell / toko-online

LANJUTKAN DARI KONDISI SAAT INI.

STATUS:
- Customer UI sudah stabil.
- Mobile responsive sudah diperbaiki.
- Product management PASS.
- Category management PASS.
- Admin Orders PASS.
- Admin Payment Settings sudah PASS.
- Commit terakhir payment settings: 9767f6c.
- Build/typecheck sebelumnya PASS.
- Payment Settings sudah mendukung metode pembayaran DEV.
- Sekarang fokus hanya pada PAYMENT FLOW END-TO-END.

==================================================
ATURAN UTAMA
==================================================

MODE WAJIB:
DEV / SANDBOX ONLY.

JANGAN:
- menggunakan payment production
- menggunakan uang nyata
- menggunakan production Midtrans key
- membuat transaksi production
- reset database
- menghapus migration
- menghapus fitur existing
- force push
- mengganti database
- mengganti desain UI yang sudah PASS tanpa alasan

Jika menemukan konfigurasi production, jangan aktifkan.
Gunakan environment/configuration DEV yang sudah tersedia.

==================================================
1. AUDIT PAYMENT FLOW EXISTING
==================================================

Sebelum coding, audit:

- checkout page
- cart state
- checkout state
- order-service
- payment-service
- order API
- payment API
- Prisma schema
- Order
- OrderItem
- Payment
- PaymentMethod
- payment status
- order status
- admin orders
- customer orders
- payment settings

Cari seluruh flow:

product
→ cart
→ checkout
→ customer information
→ payment method
→ create order
→ create payment
→ payment status
→ order status
→ customer order detail

Jangan membuat flow kedua jika flow existing masih dapat diperbaiki.

Gunakan architecture/service existing.

==================================================
2. ORDER DAN PAYMENT HARUS TERPISAH
==================================================

Pastikan:

Order status != Payment status.

Contoh:

Order:
PENDING
PROCESSING
COMPLETED
CANCELLED
FAILED

Payment:
PENDING
WAITING_PAYMENT
PAID
FAILED
EXPIRED

Jika project memiliki enum berbeda, gunakan enum existing.

Jangan mengubah schema tanpa audit.

Payment gagal tidak boleh otomatis menghapus order.

Payment pending tidak boleh dianggap paid.

Order completed hanya boleh terjadi ketika aturan bisnis existing mengizinkan.

==================================================
3. CREATE ORDER
==================================================

Saat customer klik:

"Lanjutkan Pembayaran"

pastikan:

1. Validasi cart.
2. Validasi product masih aktif.
3. Validasi harga dari server/database.
4. Jangan percaya harga dari client.
5. Validasi stock.
6. Validasi customer information.
7. Validasi payment method aktif.
8. Hitung ulang subtotal di server.
9. Hitung total di server.
10. Create Order.
11. Create OrderItem.
12. Create Payment.
13. Simpan payment method yang dipilih.
14. Return order/payment information.

Semua mutation harus atomic jika memungkinkan.

Gunakan transaction database.

Jika salah satu langkah gagal:
- jangan meninggalkan order setengah jadi
- jangan meninggalkan OrderItem orphan
- jangan meninggalkan Payment orphan

==================================================
4. HARGA
==================================================

PENTING:

Harga final harus berasal dari database/server.

Jangan:

clientTotal
clientPrice
clientSubtotal

dijadikan sumber kebenaran.

Server harus:

ambil product
→ ambil harga aktual
→ hitung quantity
→ hitung subtotal
→ hitung total

Jika harga product berubah setelah cart dibuat:
gunakan harga server terbaru sesuai aturan existing.

==================================================
5. STOCK
==================================================

Validasi stock di server.

Jika stock tidak cukup:

return error 400 dengan pesan jelas.

Jangan mengurangi stock jika order/payment belum mencapai state yang memang mengharuskan pengurangan stock.

Ikuti aturan stock existing.

Jika project sebelumnya sudah menentukan:

PENDING-FAILED
atau
PAYMENT-PROCESSING
atau state lain,

ikuti implementation existing.

Jangan membuat aturan baru tanpa audit.

==================================================
6. PAYMENT METHOD
==================================================

Customer hanya boleh memilih payment method:

active = true

Server juga wajib memvalidasi ulang.

Jangan percaya:

paymentMethodId dari client

tanpa pengecekan database.

Jika payment method:

- tidak ditemukan → 404
- inactive → 400
- tidak valid → 400

Payment method yang dinonaktifkan dari Admin tidak boleh digunakan customer.

==================================================
7. DEV PAYMENT CREATION
==================================================

Karena masih DEV/Sandbox:

Setelah order dibuat, buat payment record.

Contoh:

Order:
PENDING

Payment:
WAITING_PAYMENT

Payment harus mempunyai identifier/reference yang unik.

Contoh format:

PAY-<unique-id>

Gunakan generator existing jika sudah ada.

Jangan menggunakan ID random client sebagai payment reference.

==================================================
8. PAYMENT DETAIL PAGE
==================================================

Setelah order dibuat:

customer diarahkan ke halaman payment/detail yang sesuai.

Tampilkan:

- Order ID
- Nama produk
- Quantity
- Total pembayaran
- Payment method
- Payment status
- Instruksi pembayaran

QRIS:
- QR image
- nominal
- instruksi

Bank:
- bank
- account number
- account name
- nominal
- tombol copy

E-wallet:
- provider
- account number
- account name
- nominal
- tombol copy

Gunakan data database.

Jangan hardcode payment method.

==================================================
9. DEV PAYMENT ACTION
==================================================

Buat mekanisme DEV/Sandbox untuk menguji pembayaran tanpa uang nyata.

Contoh:

"Simulasikan Pembayaran Berhasil"

atau mekanisme DEV equivalent yang sesuai architecture.

ATURAN:

- hanya tersedia ketika DEV
- tidak tersedia di production
- tidak menggunakan Midtrans production
- tidak membutuhkan payment credential nyata
- hanya mengubah state melalui backend
- wajib melakukan authorization

Flow:

WAITING_PAYMENT
→ DEV PAYMENT SUCCESS
→ PAID

Setelah payment menjadi PAID:

jalankan business logic order sesuai aturan existing.

Jangan langsung membuat order COMPLETED jika architecture membutuhkan PROCESSING terlebih dahulu.

==================================================
10. PAYMENT FAILED
==================================================

Sediakan jalur DEV untuk:

WAITING_PAYMENT
→ FAILED

Pastikan:

- payment status FAILED
- order tidak dianggap paid
- customer dapat melihat status
- tidak ada exception
- tidak menghapus order secara otomatis

Jika retry payment memang didukung architecture existing:

order yang sama dapat membuat payment attempt baru tanpa menggandakan OrderItem.

Jika retry belum didukung:
jangan implementasi kompleks secara paksa.

==================================================
11. PAYMENT EXPIRED
==================================================

Audit apakah project sudah memiliki expiry.

Jika sudah:
gunakan implementation existing.

Jika belum:
jangan membuat background worker kompleks hanya untuk tahap ini.

Pastikan status EXPIRED dapat direpresentasikan dengan benar jika dibutuhkan oleh flow.

==================================================
12. CUSTOMER ORDER PAGE
==================================================

Setelah order dibuat, customer harus dapat melihat:

- order ID
- produk
- total
- order status
- payment status
- payment method
- tanggal
- detail pembayaran jika masih pending

Pastikan refresh browser tidak kehilangan data.

Data harus berasal dari database/API, bukan state memory saja.

==================================================
13. ADMIN ORDER PAGE
==================================================

Admin Orders harus menampilkan:

- Order ID
- customer
- total
- order status
- payment status
- payment method
- createdAt

Pastikan payment status dan order status terlihat terpisah.

Jangan merusak Admin Orders yang sudah PASS.

==================================================
14. AUTHORIZATION
==================================================

Guest:

- tidak boleh melihat order customer
- tidak boleh mengubah payment
- tidak boleh mengakses admin payment action

Customer:

- hanya boleh melihat order miliknya
- tidak boleh melihat order customer lain
- tidak boleh mengubah payment milik customer lain
- tidak boleh melakukan admin action

Admin:

- boleh melihat order sesuai permission existing
- boleh melakukan admin action yang memang sudah tersedia

Jangan menggunakan ID dari client sebagai satu-satunya authorization check.

==================================================
15. DUPLICATE ORDER PROTECTION
==================================================

PENTING karena sebelumnya ada masalah checkout/cart:

Jika customer:

checkout
→ kembali ke homepage
→ membeli produk lain

jangan membuat order lama ikut masuk kembali ke cart.

Pastikan:

- cart state konsisten
- checkout state tidak menggandakan item
- order hanya dibuat saat submit checkout
- refresh checkout tidak membuat order baru
- double click tombol pembayaran tidak membuat dua order
- retry request tidak membuat duplicate order jika architecture mendukung idempotency

Gunakan protection yang sesuai dengan architecture existing.

Jangan membuat solusi frontend-only untuk duplicate order.

==================================================
16. ERROR HANDLING
==================================================

Pastikan tidak ada:

Application error
Client-side exception
HTTP 500 untuk validation
blank page
unhandled promise rejection

Mapping:

400:
validation/business error

401:
unauthorized

403:
forbidden

404:
resource tidak ditemukan

409:
duplicate/conflict jika sesuai

500:
unexpected server error

Error message harus aman dan jelas.

Jangan expose stack trace ke customer.

==================================================
17. DATABASE VERIFICATION
==================================================

Setelah melakukan test:

cek database DEV.

Verifikasi:

Order ada.

OrderItem ada.

Payment ada.

Relationship benar.

Tidak ada duplicate OrderItem.

Tidak ada orphan Payment.

Status sesuai.

Refresh halaman:
data tetap ada.

Restart development server:
data tetap ada.

Jangan reset database.

==================================================
18. TEST SCENARIO
==================================================

WAJIB lakukan test berikut.

TEST 1:
Guest buka checkout
→ diarahkan/login sesuai auth existing.

TEST 2:
Customer checkout dengan product aktif
→ Order created
→ OrderItem created
→ Payment created.

TEST 3:
Customer memilih payment method aktif
→ berhasil.

TEST 4:
Customer memilih payment method inactive
→ ditolak.

TEST 5:
Product stock tidak cukup
→ order tidak dibuat.

TEST 6:
Product tidak aktif
→ order ditolak.

TEST 7:
Harga client dimanipulasi
→ server tetap menggunakan harga database.

TEST 8:
Double click submit
→ tidak membuat duplicate order.

TEST 9:
Payment DEV success
→ Payment PAID.

TEST 10:
Payment DEV failed
→ Payment FAILED.

TEST 11:
Refresh payment page
→ status tetap benar.

TEST 12:
Customer membuka order
→ hanya order miliknya.

TEST 13:
Customer mencoba order milik user lain
→ 403/404 sesuai security architecture.

TEST 14:
Admin melihat order
→ order terlihat.

TEST 15:
Admin melihat payment status
→ payment status terpisah dari order status.

TEST 16:
Nonaktifkan payment method dari Admin
→ payment method hilang dari checkout.

TEST 17:
Aktifkan kembali
→ muncul kembali di checkout.

==================================================
19. RESPONSIVE TEST
==================================================

Verifikasi:

360px
375px
390px
414px
768px
1280px
1440px

Khusus:

checkout
payment detail
customer orders
admin orders

Tidak boleh:

- horizontal overflow
- button keluar viewport
- modal terpotong
- payment card terlalu besar
- bottom navigation menutupi content
- sticky action menutupi payment detail

Jangan mengubah desain yang sudah disetujui kecuali memang diperlukan.

==================================================
20. REGRESSION
==================================================

Pastikan semua route existing tetap bekerja:

/
 /products
 /categories
 /product/[slug]
 /checkout
 /orders
 /profile
 /admin
 /admin/products
 /admin/categories
 /admin/orders
 /admin/settings
 /admin/settings/payment

Gunakan route actual project jika berbeda.

Jalankan smoke test.

==================================================
21. TEST COMMAND
==================================================

Jalankan:

npm run typecheck

npm run build

Jalankan test suite yang kompatibel dengan Node environment project.

Jika npm run test:* existing gagal karena masalah command/tooling Node, jangan menganggap fitur gagal.

Cari test command yang memang digunakan project sebelumnya dan jalankan test tersebut.

Jangan mengganti test framework hanya untuk membuat PASS.

==================================================
22. GIT SAFETY
==================================================

Sebelum commit:

git status --short

git diff --check

Pastikan tidak ada:

.env
.env.local
secret
API key
production credential
temporary file
debug file
build artifact

Commit:

git add <file yang berubah>

git commit -m "feat(payment): complete dev payment flow"

git push origin main

JANGAN force push.

Setelah push:

git status --short
git log -1 --oneline
git status -sb

Pastikan origin/main sinkron.

==================================================
23. OUTPUT AKHIR
==================================================

Tampilkan:

1. Audit payment flow
2. File yang diubah
3. API/route yang diubah
4. Database/model yang digunakan
5. Order creation result
6. Payment creation result
7. DEV success flow
8. DEV failed flow
9. Duplicate protection
10. Authorization result
11. Responsive result
12. Typecheck result
13. Build result
14. Smoke test result
15. Database verification result
16. Commit hash
17. Push status
18. Blocker jika ada

JANGAN lanjut ke production payment setelah tahap ini.

STOP setelah seluruh DEV payment flow selesai dan terverifikasi.

```
# Prompt: Admin Payment Settings — DEV/Sandbox
```
## Prompt: Admin Payment Settings — DEV/Sandbox

Project: Digital Cell / toko-online

Lanjutkan development dari kondisi project saat ini.

STATUS SEBELUMNYA:
- Admin Categories sudah PASS.
- Admin Products sudah PASS.
- Admin Orders sudah PASS.
- Customer UI sudah stabil pada mobile.
- Checkout UI sudah tersedia.
- Payment flow sudah ada tetapi belum boleh menggunakan transaksi nyata.
- Typecheck/build/smoke test sebelumnya sudah PASS.
- Jangan merusak fitur yang sudah PASS.

TUJUAN:
Implementasikan dan rapikan halaman/settings pembayaran di Admin Panel agar admin dapat mengelola metode pembayaran untuk DEV/Sandbox.

PENTING:
Ini hanya DEV/Sandbox.
JANGAN menggunakan payment production.
JANGAN membutuhkan transaksi uang nyata.
JANGAN memasukkan Midtrans production key.
JANGAN melakukan reset database.
JANGAN mengubah database/schema secara destruktif.
JANGAN menghapus fitur existing.

==================================================
1. AUDIT TERLEBIH DAHULU
==================================================

Sebelum coding:

- Audit struktur frontend/admin.
- Cari payment-service yang sudah ada.
- Cari Prisma model yang berhubungan dengan payment/order/settings.
- Cari endpoint/API payment existing.
- Cari komponen checkout yang sudah menggunakan payment method.
- Cari konfigurasi Midtrans/payment provider.
- Cari apakah sudah ada model PaymentMethod atau Settings.
- Cari environment variable payment yang tersedia.
- Jangan membuat struktur baru jika struktur existing masih bisa digunakan.

Tampilkan secara internal hasil audit dan gunakan struktur existing sebagai dasar implementasi.

==================================================
2. ADMIN PAYMENT SETTINGS
==================================================

Buat/rapikan halaman:

/admin/settings/payment

atau gunakan route settings payment yang sudah tersedia jika project sudah memiliki route tersebut.

Admin harus dapat melihat daftar metode pembayaran.

Minimal metode:

1. QRIS
2. Transfer Bank
3. E-Wallet

Setiap metode memiliki:

- nama
- tipe
- status aktif/nonaktif
- deskripsi
- urutan
- konfigurasi yang diperlukan
- tombol Edit
- toggle Aktif/Nonaktif

UI harus konsisten dengan Admin Panel existing.

==================================================
3. QRIS
==================================================

Admin dapat:

- mengaktifkan/nonaktifkan QRIS
- upload/ganti gambar QRIS
- melihat preview QRIS
- menghapus QRIS jika diperlukan
- menyimpan perubahan

Untuk DEV:

- file boleh disimpan menggunakan mekanisme storage existing.
- jangan hardcode URL.
- jangan menyimpan secret ke frontend.
- validasi tipe file.
- validasi ukuran file.
- tampilkan error yang jelas jika upload gagal.

Jika project belum memiliki storage production, gunakan mekanisme DEV yang sudah tersedia.

Jangan membuat integrasi storage baru yang kompleks jika belum diperlukan.

==================================================
4. TRANSFER BANK
==================================================

Admin dapat menambahkan rekening bank.

Field minimal:

- bank name
- account name
- account number
- logo optional
- status aktif/nonaktif

Contoh data DEV:

BCA
Digital Cell
1234567890

Mandiri
Digital Cell
9876543210

BNI
Digital Cell
1122334455

Data contoh hanya untuk DEV.

Admin dapat:

- tambah rekening
- edit rekening
- aktif/nonaktif
- hapus rekening

Jangan hardcode data bank ke frontend.

==================================================
5. E-WALLET
==================================================

Admin dapat menambahkan metode e-wallet.

Minimal:

- provider
- account name
- account number
- logo optional
- status aktif/nonaktif

Contoh DEV:

GoPay
Digital Cell
081234567890

OVO
Digital Cell
081234567890

DANA
Digital Cell
081234567890

Data hanya untuk DEV.

Admin dapat:

- tambah
- edit
- aktif/nonaktif
- hapus

==================================================
6. SORT / ORDER
==================================================

Admin dapat mengatur urutan metode pembayaran.

Gunakan field sortOrder/order yang sesuai dengan struktur database existing.

Customer checkout harus menampilkan metode aktif berdasarkan urutan tersebut.

Metode nonaktif:

- tidak boleh muncul di checkout customer
- tetap terlihat di admin
- status harus jelas

==================================================
7. CUSTOMER CHECKOUT
==================================================

Hubungkan payment settings dengan checkout.

Checkout hanya menampilkan payment method:

WHERE active = true

Jangan menampilkan metode yang dinonaktifkan.

Jika hanya satu metode aktif:
- otomatis pilih metode tersebut jika UX existing mendukung.

Jika tidak ada metode aktif:
- tampilkan pesan yang jelas:
  "Belum ada metode pembayaran yang tersedia."

Jangan membuat checkout crash.

==================================================
8. PAYMENT DETAIL
==================================================

Setelah customer memilih metode pembayaran, tampilkan detail yang sesuai.

QRIS:
- tampilkan QRIS image
- tombol/perilaku zoom jika komponen existing mendukung
- nominal pembayaran
- instruksi pembayaran

Transfer Bank:
- nama bank
- nomor rekening
- nama pemilik
- tombol copy nomor rekening
- nominal pembayaran
- instruksi pembayaran

E-Wallet:
- provider
- nomor/account
- tombol copy
- nominal pembayaran
- instruksi pembayaran

Gunakan komponen reusable jika memungkinkan.

==================================================
9. DEV PAYMENT FLOW
==================================================

Karena masih DEV/Sandbox:

Customer dapat membuat order/payment intent tanpa transaksi uang nyata.

Gunakan status payment yang sudah ada di project.

Contoh lifecycle:

PENDING
→ WAITING_PAYMENT
→ PAID
→ FAILED
→ EXPIRED

Jangan mengubah status order secara sembarangan.

Payment status dan Order status harus tetap dipisahkan.

Jika existing project sudah memiliki enum/status berbeda, gunakan enum existing.

==================================================
10. ADMIN PAYMENT MANAGEMENT
==================================================

Tambahkan ringkasan payment di admin jika struktur existing mendukung.

Minimal admin dapat melihat:

- order ID
- customer
- total
- payment method
- payment status
- order status
- createdAt

Admin dapat melakukan aksi yang memang sudah didukung architecture existing.

Untuk DEV:

- jangan menghubungkan tombol "Confirm Paid" ke payment provider nyata.
- jika membutuhkan manual verification, buat flow DEV yang aman.

==================================================
11. VALIDATION
==================================================

Validasi:

QRIS:
- file image saja
- ukuran file dibatasi
- upload gagal → error jelas

Bank:
- bank name wajib
- account name wajib
- account number wajib

E-wallet:
- provider wajib
- account number wajib

Jangan menyimpan data invalid.

Gunakan server-side validation untuk mutation.

Jangan hanya mengandalkan client-side validation.

==================================================
12. SECURITY
==================================================

Pastikan seluruh admin mutation dilindungi requireAdmin atau mekanisme auth admin existing.

Guest/customer tidak boleh:

- menambah payment method
- edit payment method
- delete payment method
- toggle payment method
- mengubah QRIS
- mengubah rekening

Customer hanya boleh membaca payment method aktif.

Jangan expose credential/payment secret ke client.

Jangan commit:

.env
.env.local
secret
API key
production credential
payment credential

==================================================
13. DATABASE
==================================================

Gunakan Prisma/database existing.

Sebelum membuat migration:

- audit schema terlebih dahulu.
- jika model payment method sudah ada, gunakan model tersebut.
- jika belum ada dan memang diperlukan, buat model minimal dan modular.

Jangan menghapus data existing.

Jangan reset database.

Jangan menjalankan:

prisma migrate reset

Jangan menggunakan mock database sebagai pengganti database DEV.

Jika migration diperlukan, buat migration normal dan aman.

==================================================
14. UI ADMIN
==================================================

UI harus mengikuti style Admin Panel existing.

Desktop:
- table/card yang rapi
- status badge
- action menu
- modal/form yang jelas

Mobile:
- jangan membuat horizontal overflow
- table boleh berubah menjadi card/list
- tombol tidak keluar viewport
- form full width
- modal tidak terpotong

Gunakan komponen existing jika tersedia.

Jangan redesign seluruh Admin Panel.

==================================================
15. ERROR HANDLING
==================================================

Semua mutation harus memiliki error handling.

Tidak boleh ada:

- unhandled exception
- blank page
- React client-side exception
- HTTP 500 untuk validation error
- silent failure

Gunakan response error yang konsisten dengan API existing.

Validation:
400

Unauthorized:
401

Forbidden:
403

Not found:
404

Unexpected server error:
500

Jangan mengubah error 400/404 menjadi 500.

==================================================
16. CHECKOUT REGRESSION
==================================================

Pastikan perubahan payment tidak merusak:

- homepage
- category
- product detail
- cart
- checkout detail
- payment selection
- order creation
- orders page
- admin orders
- admin products
- admin categories

Khusus masalah sebelumnya:

Jika customer sudah berada di checkout lalu kembali ke halaman utama dan membeli produk lain, jangan menyebabkan cart/order menjadi duplikat secara tidak sengaja.

Pastikan state checkout/cart tetap konsisten.

==================================================
17. TEST
==================================================

Buat test yang relevan.

Minimal test:

A. Admin:
- list payment methods
- create QRIS/config
- create bank
- edit bank
- toggle bank
- delete bank
- create e-wallet
- edit e-wallet
- toggle e-wallet
- delete e-wallet

B. Customer:
- hanya melihat active payment methods
- inactive method tidak muncul
- payment detail benar
- checkout tidak crash jika payment method kosong

C. Auth:
- guest tidak dapat mutation admin
- customer tidak dapat mutation admin
- admin dapat mutation

D. Error:
- invalid bank
- invalid e-wallet
- invalid upload
- missing payment method
- nonexistent payment method

==================================================
18. RESPONSIVE TEST
==================================================

Verifikasi minimal:

360px
375px
390px
414px
768px
1280px
1440px

Pastikan:

- tidak ada horizontal overflow
- tombol tidak keluar layar
- modal tidak terpotong
- form tidak melebar
- table/list tetap usable
- checkout tetap usable

==================================================
19. BUILD VERIFICATION
==================================================

Setelah implementasi:

npm run typecheck

npm run build

Jalankan development server dan lakukan smoke test route terkait.

Verifikasi minimal:

/admin
/admin/settings/payment
/products
/product/[slug]
/checkout
/orders

Gunakan route yang benar sesuai struktur project.

Jika ada test existing:
jalankan test yang relevan.

Jangan berhenti hanya karena build PASS jika runtime masih error.

==================================================
20. GIT
==================================================

Sebelum commit:

git status --short

git diff --check

Pastikan:

- tidak ada .env
- tidak ada secret
- tidak ada credential
- tidak ada temporary test file
- tidak ada build artifact
- tidak ada debug file

Jika semua PASS:

git add <file yang memang berubah>

git commit -m "feat(admin): add payment settings"

git push origin main

JANGAN force push.

Setelah push:

git status --short
git log -1 --oneline
git status -sb

Pastikan branch sinkron dengan origin/main.

==================================================
21. OUTPUT AKHIR
==================================================

Setelah selesai, tampilkan:

1. File yang diubah
2. Database/schema yang berubah jika ada
3. API/route yang ditambahkan
4. Fitur Admin Payment Settings yang berhasil
5. Fitur Customer Checkout yang berhasil
6. Test yang dijalankan
7. Hasil typecheck
8. Hasil build
9. Hasil smoke test
10. Commit hash
11. Push status
12. Blocker jika ada

ATURAN PALING PENTING:

- DEV/Sandbox saja.
- Tidak ada transaksi uang nyata.
- Tidak ada production payment key.
- Jangan reset database.
- Jangan hapus fitur existing.
- Jangan merusak UI customer yang sudah PASS.
- Jangan merusak Admin Products/Categories/Orders.
- Jangan membuat mock sebagai pengganti database.
- Jangan force push.
- Jika menemukan error, perbaiki dan test ulang.
- Jika semuanya PASS, commit dan push.


```
# Prompt: Admin Orders — Full Implementation & Verification
```

PROMPT: ADMIN ORDERS — FULL IMPLEMENTATION & VERIFICATION

Project: Digital Cell / toko-online

STATUS SEBELUMNYA:
- Admin Categories: PASS
- Admin Products: PASS
- Admin Dashboard: PASS
- Dashboard menggunakan data nyata dari database
- Authentication admin/customer/guest sudah diverifikasi
- Responsive customer UI sudah stabil
- Typecheck/build/smoke/regression sebelumnya PASS
- Git branch main sinkron dengan origin/main

TUJUAN:
Implementasikan dan selesaikan ADMIN ORDERS secara production-ready dari sisi kode dan development environment.

Fokus:
- daftar order
- pencarian
- filter
- status
- pagination
- order detail
- customer information
- order items
- total
- update status
- validasi backend
- responsive admin UI
- integrasi database nyata

JANGAN menyentuh payment production/Midtrans production pada tahap ini.

==================================================
1. AUDIT SEBELUM IMPLEMENTASI
==================================================

Audit terlebih dahulu struktur project.

Cari dan pahami:
- admin layout
- admin navigation
- admin orders route/page
- order-service
- order repository jika ada
- Prisma schema
- Order
- OrderItem
- Product
- User/Customer
- Payment
- PaymentMethod
- status enum
- existing API/server action
- auth middleware

Jangan langsung membuat file baru sebelum memahami architecture existing.

Gunakan service/repository yang sudah ada jika memungkinkan.

Jangan membuat duplicate order service.

==================================================
2. AUTHORIZATION
==================================================

Semua halaman dan mutation admin order WAJIB protected.

Guest:
- tidak boleh mengakses admin orders
- redirect/login sesuai architecture existing

Customer:
- tidak boleh mengakses admin order management
- harus mendapatkan 401/403/redirect sesuai pola existing

Admin:
- boleh melihat dan mengelola order.

Jangan membuat endpoint yang bisa mengubah status order tanpa requireAdmin.

Jangan mempercayai role yang dikirim dari client.

Role harus diverifikasi dari session/server.

==================================================
3. ORDER LIST
==================================================

Buat/rapikan:

/admin/orders

Tampilkan order dari database nyata.

Kolom desktop minimal:

- Order ID
- Customer
- Produk
- Total
- Payment Status
- Order Status
- Created At
- Action

Jika order memiliki banyak item:
jangan membuat satu order muncul sebagai banyak row.

Satu order tetap satu row.

Produk dapat diringkas:
contoh:
"Mobile Legends 500 + 1 item"

atau format yang sesuai design existing.

==================================================
4. SEARCH
==================================================

Tambahkan search.

Search minimal dapat berdasarkan:
- Order ID
- nama customer
- nomor WhatsApp jika tersedia

Gunakan query/database filtering yang benar.

Jangan mengambil seluruh order ke browser hanya untuk search.

Jika search dilakukan server-side:
gunakan parameter URL/query yang aman.

Contoh:

/admin/orders?search=...

Search harus:
- case-insensitive jika database mendukung
- aman
- tidak menyebabkan exception
- tetap bekerja bersama filter/pagination.

==================================================
5. FILTER STATUS
==================================================

Tambahkan filter berdasarkan status yang benar-benar ada di schema.

Contoh jika enum tersedia:

Pending
Processing
Paid
Completed
Failed
Cancelled

JANGAN membuat status baru.

Gunakan enum/schema existing.

Filter harus bekerja bersama:
- search
- pagination

Contoh:

/admin/orders?status=pending
/admin/orders?status=completed

Jika status tidak valid:
jangan crash.

Gunakan fallback:
- semua order
atau
- tampilkan error validasi yang aman.

==================================================
6. FILTER PAYMENT
==================================================

Jika model Payment tersedia:

Tambahkan filter:
- Unpaid
- Pending
- Paid
- Failed
- Expired

HANYA gunakan status yang memang ada.

Jika payment status belum tersedia dalam schema:
jangan mengubah schema hanya untuk membuat filter.

==================================================
7. DATE FILTER
==================================================

Jika architecture existing mendukung:

Tambahkan:
- Today
- 7 days
- 30 days

Gunakan CreatedAt/order date existing.

Jangan mengubah database.

Jika date filter membuat UI terlalu kompleks:
prioritaskan search + status terlebih dahulu.

==================================================
8. PAGINATION
==================================================

Order list harus menggunakan pagination.

Default:
10 order/page.

Tampilkan:
- current page
- total pages
- next
- previous

Jangan fetch seluruh order jika database sudah besar.

Gunakan:
- skip/take
atau
- cursor pagination

sesuai architecture existing.

Pagination harus tetap bekerja dengan:
- search
- status filter
- payment filter
- date filter jika tersedia.

==================================================
9. ORDER DETAIL
==================================================

Buat/rapikan detail order.

Contoh:

/admin/orders/[id]

Tampilkan:

ORDER INFORMATION
- Order ID
- Created At
- Order Status

CUSTOMER
- Nama
- WhatsApp
- email jika tersedia

PRODUCTS
- Product
- Quantity
- Unit price
- Subtotal

PAYMENT
- Payment status
- Payment method
- Payment amount
- Payment reference jika aman ditampilkan

TOTAL
- subtotal
- discount jika memang ada
- fee jika memang ada
- total

Jangan tampilkan:
- passwordHash
- API key
- secret
- payment server key
- internal credential.

==================================================
10. ORDER ITEM
==================================================

Pastikan order detail menggunakan relation order -> orderItems -> product.

Jangan mengambil product berdasarkan nama.

Gunakan relation/ID yang benar.

Jika product sudah dihapus tetapi OrderItem masih memiliki snapshot:
gunakan data snapshot yang memang tersedia.

Jangan menyebabkan order lama rusak hanya karena product dihapus.

==================================================
11. UPDATE ORDER STATUS
==================================================

Admin harus bisa mengubah status order.

Contoh jika status enum mendukung:

Pending
→ Processing
→ Completed

atau flow yang memang sesuai schema.

WAJIB:
- validasi status di server
- validasi order ID
- requireAdmin
- update database
- return result aman
- refresh data setelah berhasil.

Jangan hanya mengubah status di UI.

==================================================
12. STATUS TRANSITION
==================================================

Periksa status transition yang benar-benar cocok dengan architecture.

Jangan mengizinkan perubahan status sembarangan jika business logic existing melarangnya.

Contoh:
Completed -> Pending

tidak boleh dilakukan jika memang status completed bersifat final.

Jangan membuat aturan baru tanpa memeriksa existing order-service.

Jika order-service sudah memiliki transition validation:
gunakan itu.

==================================================
13. PAYMENT STATUS
==================================================

Jangan mencampur:

Order Status

dengan

Payment Status

Jika keduanya memang field berbeda di schema:

Order Status:
- Pending
- Processing
- Completed
- Cancelled

Payment Status:
- Pending
- Paid
- Failed
- Expired

Gunakan status yang sebenarnya ada.

Jangan mengubah payment status secara otomatis hanya karena admin mengubah order status kecuali existing service memang mendesain demikian.

==================================================
14. ORDER ACTIONS
==================================================

Pada order list minimal:

View Detail

Jika status dapat diubah:
- Update Status

Jangan menambahkan:
- Delete order

kecuali architecture existing memang memiliki kebutuhan tersebut.

Order history sebaiknya tidak dihapus sembarangan.

==================================================
15. CONFIRMATION
==================================================

Update status harus memiliki confirmation UI jika perubahan bersifat penting.

Contoh:

"Ubah status order menjadi Completed?"

Tombol:
- Batal
- Konfirmasi

Jangan melakukan mutation hanya karena user memilih dropdown tanpa confirmation jika design existing memerlukan confirmation.

==================================================
16. SUCCESS / ERROR FEEDBACK
==================================================

Setelah update:

Success:
"Status order berhasil diperbarui."

Error:
"Gagal memperbarui status order. Silakan coba lagi."

Jangan tampilkan:
- Prisma error
- SQL error
- stack trace
- internal exception
- secret.

Jika order tidak ditemukan:

404 / not found yang sesuai architecture.

==================================================
17. LOADING STATE
==================================================

Tambahkan loading state untuk:
- order list
- detail
- status update

Jangan membuat halaman blank.

Gunakan skeleton/spinner sesuai design system existing.

==================================================
18. EMPTY STATE
==================================================

Jika tidak ada order:

Tampilkan:

"Belum ada pesanan"

dengan penjelasan singkat.

Jika filter menghasilkan 0:

"Pesanan tidak ditemukan"

Jangan menampilkan error server.

==================================================
19. RESPONSIVE ADMIN ORDERS
==================================================

WAJIB verifikasi:

360px
375px
390px
414px
1280px
1440px

Mobile:

Jangan menggunakan tabel yang menyebabkan horizontal overflow.

Jika tabel terlalu lebar:
ubah menjadi card/list responsive.

Setiap order card minimal menampilkan:
- Order ID
- customer
- total
- status
- waktu
- tombol detail.

Search/filter harus tetap mudah digunakan di mobile.

Desktop:
- tabel boleh digunakan
- gunakan ruang dengan baik
- jangan terlalu besar
- jangan terlalu padat.

Tidak boleh:
- horizontal page overflow
- button keluar layar
- modal keluar viewport
- dropdown terpotong.

==================================================
20. ADMIN NAVIGATION
==================================================

Pastikan:

Dashboard
Products
Categories
Orders
Users
Settings/payment jika tersedia

Orders menjadi active menu saat berada di:
- /admin/orders
- /admin/orders/[id]

Jangan membuat duplicate navigation.

==================================================
21. DATABASE PERFORMANCE
==================================================

Order list:
- gunakan pagination
- select field seperlunya
- include relation seperlunya

Hindari N+1 query.

Untuk total count:
gunakan count/aggregate.

Untuk order list:
gunakan query yang terlimit.

Untuk order detail:
gunakan satu query relation yang sesuai jika memungkinkan.

Jangan fetch seluruh product/user/order database.

==================================================
22. SECURITY
==================================================

Pastikan:

- admin authorization server-side
- mutation server-side
- ID validation
- status validation
- tidak ada mass assignment berbahaya
- customer tidak dapat mengubah order miliknya melalui admin endpoint
- customer tidak dapat mengakses order admin lainnya
- tidak ada secret di response.

Jangan menerima:

{
  role: "admin"
}

dari client sebagai bukti admin.

==================================================
23. CUSTOMER REGRESSION
==================================================

Setelah Admin Orders selesai, jangan merusak customer:

/products
/categories
/product/[slug]
/checkout
/orders
/profile

Pastikan customer order history tetap dapat bekerja sesuai architecture.

Jika customer order status mengambil data dari order yang sama:
pastikan perubahan admin terlihat setelah refresh.

==================================================
24. PAYMENT SAFETY
==================================================

PENTING:

Jangan mengaktifkan:
- Midtrans production
- transaksi uang nyata
- production server key

Jangan meminta credential payment.

Jangan mengubah payment provider.

Admin Orders hanya mengelola data order yang sudah tersedia.

Development/sandbox tetap aman.

==================================================
25. TEST DATA
==================================================

Gunakan data development yang sudah tersedia.

Jangan membuat banyak mock order hanya untuk membuat dashboard terlihat penuh.

Jika perlu satu order tambahan untuk testing:
gunakan service/API existing dan data development saja.

Jangan seed data production.

==================================================
26. TESTING
==================================================

Jalankan:

npm run typecheck

Expected:
PASS

Kemudian:

npm run build

Expected:
PASS

Jalankan development server.

Test:

1. Guest -> /admin/orders
Expected:
redirect/login

2. Customer -> /admin/orders
Expected:
403/redirect

3. Admin -> /admin/orders
Expected:
200

4. Admin -> order detail
Expected:
200

5. Search order
Expected:
hasil benar

6. Filter status
Expected:
hasil benar

7. Pagination
Expected:
berfungsi

8. Update status
Expected:
database berubah

9. Refresh
Expected:
status tetap benar

10. Invalid order ID
Expected:
404/not found

11. Invalid status
Expected:
ditolak dengan aman

12. Empty result
Expected:
empty state

13. Mobile
Expected:
tanpa overflow

==================================================
27. DATABASE VERIFICATION
==================================================

Setelah update status:

verifikasi langsung ke database/service bahwa status benar-benar berubah.

Jangan hanya mengandalkan UI.

Contoh:

Order sebelum:
PENDING

Admin update:
COMPLETED

Refresh:
COMPLETED

Database:
COMPLETED

Semua harus konsisten.

==================================================
28. ERROR / NO-500 VERIFICATION
==================================================

Smoke test:

/admin/orders
/admin/orders/[valid-id]
/admin/orders/[invalid-id]

Pastikan:
- tidak ada 500 yang tidak tertangani
- tidak ada client-side exception
- tidak ada hydration error
- tidak ada server-side exception.

Jika order ID invalid:
404/Not Found adalah expected behavior.

==================================================
29. GIT CHECK
==================================================

Sebelum commit:

git status --short

git diff --check

Pastikan tidak ada:
- .env
- secret
- credential
- database dump
- temporary test file
- build artifact.

Review diff.

==================================================
30. COMMIT
==================================================

Jika seluruh test PASS:

git add <file yang benar-benar berubah>

git commit -m "feat(admin): add order management"

Jangan membuat empty commit.

Jangan force push.

Kemudian:

git push origin main

==================================================
31. FINAL VERIFICATION
==================================================

Setelah push:

git status --short

git log -1 --oneline

git status -sb

Expected:
- working tree clean
- main sinkron dengan origin/main

==================================================
32. FINAL REPORT
==================================================

Laporkan:

1. File yang diubah
2. Order list
3. Search
4. Filter
5. Pagination
6. Order detail
7. Customer information
8. Order items
9. Payment information
10. Status update
11. Status validation
12. Authentication test
13. Responsive test
14. Database verification
15. Typecheck
16. Build
17. Smoke test
18. Regression test
19. Commit hash
20. Push status

Jika ada blocker:
jelaskan blocker sebenarnya.

Jangan mengklaim PASS jika belum dites.

ATURAN KERAS:

- Jangan reset database.
- Jangan menghapus order existing.
- Jangan mengubah Prisma schema jika tidak diperlukan.
- Jangan membuat mock data sebagai pengganti database.
- Jangan mengaktifkan payment production.
- Jangan memasukkan credential.
- Jangan merusak Admin Dashboard.
- Jangan merusak Categories/Product.
- Jangan merusak customer storefront.
- Jangan force push.
- Jangan commit secret.
- Jangan membuat empty commit.
- Jangan berhenti hanya setelah coding; wajib typecheck + build + smoke + regression.

```
# Prompt: Admin Dashboard — Full Implementation & Verification
```
PROMPT: ADMIN DASHBOARD — FULL IMPLEMENTATION & VERIFICATION

Project: Digital Cell / toko-online

Konteks:
- Admin Categories sudah selesai dan terverifikasi.
- Admin Category CRUD sudah PASS.
- Admin Product CRUD sudah PASS.
- Product aktif/nonaktif sudah bekerja.
- Database development sudah berisi data test.
- Customer UI mobile sudah diperbaiki dan responsive.
- Typecheck/build/smoke test sebelumnya PASS.
- Jangan mengulang atau merusak fitur yang sudah PASS.

TUJUAN:
Sekarang implementasikan dan rapikan ADMIN DASHBOARD menggunakan data nyata dari database development.

PENTING:
Dashboard harus benar-benar mengambil data dari backend/database.
Jangan menggunakan angka dummy/hardcoded untuk KPI.

==================================================
1. AUDIT ADMIN DASHBOARD TERLEBIH DAHULU
==================================================

Audit struktur project sebelum mengubah kode.

Cari:
- route admin dashboard
- layout admin
- middleware/auth admin
- repository/service
- Prisma schema
- model User
- model Product
- model Category
- model Order
- model OrderItem
- model Payment
- model PaymentMethod jika ada
- model Stock jika ada
- service/repository terkait
- komponen chart/statistik jika sudah ada

Gunakan architecture existing.

Jangan membuat ulang sistem yang sudah ada.

Jika dashboard sudah ada tetapi masih menggunakan mock/static data:
- ganti hanya bagian data source-nya
- pertahankan UI yang sudah bagus jika memungkinkan.

==================================================
2. SECURITY ADMIN
==================================================

Pastikan seluruh dashboard benar-benar protected.

Requirement:

Guest:
- tidak boleh melihat dashboard
- redirect ke login

Customer:
- tidak boleh membuka dashboard
- response/redirect sesuai pola auth existing

Admin:
- boleh membuka dashboard

Gunakan middleware/requireAdmin/layout admin existing.

Jangan membuat bypass authentication.

Jangan menaruh secret di client bundle.

Jangan expose:
- passwordHash
- secret
- payment credential
- API key
- environment variable

==================================================
3. KPI DASHBOARD
==================================================

Dashboard harus menampilkan minimal:

A. Total User
- jumlah user/customer yang valid
- gunakan database

B. Total Produk
- jumlah seluruh produk

C. Produk Aktif
- jumlah produk dengan status aktif

D. Produk Nonaktif
- jumlah produk nonaktif

E. Total Kategori
- jumlah kategori

F. Total Order
- jumlah order

G. Order Pending
- jumlah order yang masih pending

H. Order Berhasil
- jumlah order sukses/paid/completed sesuai enum/status existing

I. Total Pendapatan
- hitung dari order yang valid/berhasil
- jangan menghitung order gagal/cancel
- gunakan field amount/total existing
- jangan membuat asumsi nama field jika schema berbeda

J. Stok
Jika schema memiliki stock:
- total stok
- produk stok rendah
- produk habis

Jika stock bukan field/model terpisah:
- gunakan struktur stock existing.

Jangan mengubah schema hanya untuk membuat KPI.

==================================================
4. STATISTIK ORDER
==================================================

Buat section statistik order.

Minimal tampilkan:
- Pending
- Processing jika tersedia
- Paid/Success
- Completed jika tersedia
- Failed
- Cancelled

Gunakan status enum yang benar-benar ada di schema.

Jangan membuat enum/status baru hanya untuk dashboard.

Dashboard harus aman jika database kosong.

Jika database kosong:
- KPI tetap tampil 0
- chart tidak crash
- tampilkan empty state yang rapi.

==================================================
5. RECENT ORDERS
==================================================

Tambahkan tabel/list:

Recent Orders

Minimal:
- Order ID
- Customer
- Product
- Total
- Status
- Created At
- Action/detail

Ambil misalnya 5–10 order terbaru.

Customer information harus aman.

Jangan tampilkan:
- password
- passwordHash
- secret
- token
- payment credential

Jika order memiliki banyak item:
- tampilkan ringkasan item dengan benar
- jangan menyebabkan duplicate row yang membingungkan.

Tambahkan tombol:
"Lihat Semua"

arah ke halaman admin orders existing jika sudah tersedia.

==================================================
6. TOP PRODUCTS
==================================================

Buat section:
Produk Terlaris

Gunakan data order/order item yang benar-benar ada.

Tampilkan:
- nama produk
- jumlah terjual
- revenue jika datanya tersedia

Jangan menggunakan angka dummy.

Jika belum ada order:
- tampilkan empty state:
  "Belum ada data penjualan"

Jangan membuat seed baru hanya untuk dashboard.

==================================================
7. LOW STOCK
==================================================

Jika product memiliki stock:

Buat section:
Stok Menipis

Tampilkan:
- nama produk
- stok
- status

Minimal kategori:
- habis
- rendah
- tersedia

Threshold stok rendah harus mengikuti konfigurasi existing jika sudah tersedia.

Jika belum ada konfigurasi:
gunakan threshold sederhana yang tidak mengubah database/schema.

Misalnya:
stock <= 5 dianggap rendah.

Jangan mengurangi stok hanya karena membuka dashboard.

==================================================
8. QUICK ACTION
==================================================

Tambahkan Quick Actions:

- Tambah Produk
- Kelola Produk
- Kelola Kategori
- Kelola Order
- Kelola User
- Pengaturan Payment jika route tersebut sudah ada

Gunakan route existing.

Jangan membuat route palsu.

Jika route belum ada:
jangan membuat fitur baru besar-besaran.
Tampilkan action hanya untuk fitur yang memang tersedia.

==================================================
9. REFRESH DATA
==================================================

Dashboard harus bisa mendapatkan data terbaru.

Gunakan mekanisme existing yang sesuai:
- server-side fetch
- revalidation
- router refresh
- API/service

Setelah:
- tambah produk
- edit produk
- toggle produk
- delete produk
- tambah kategori
- edit kategori
- toggle kategori
- order berubah

Dashboard tidak boleh menampilkan data stale secara permanen.

Jangan membuat polling agresif.

==================================================
10. LOADING STATE
==================================================

Buat loading state yang rapi.

Saat dashboard sedang mengambil data:
- jangan tampilkan halaman kosong
- gunakan skeleton/loading state sesuai design existing.

==================================================
11. ERROR STATE
==================================================

Jika database/API error:

Jangan tampilkan:
- stack trace
- SQL error mentah
- Prisma internal error
- secret
- environment variable

Tampilkan pesan user-friendly.

Contoh:
"Data dashboard gagal dimuat. Silakan coba lagi."

Jika aman:
- tombol Retry.

==================================================
12. EMPTY DATABASE
==================================================

WAJIB dites dengan kondisi database kosong secara aman.

Dashboard harus tetap render.

Expected:
- Total User = 0
- Total Produk = 0
- Total Kategori = 0
- Total Order = 0
- Pendapatan = Rp 0
- chart/list empty state
- tidak ada NaN
- tidak ada undefined
- tidak ada hydration error
- tidak ada exception

Jangan reset database production.

Gunakan environment development/test jika perlu untuk verifikasi.

==================================================
13. RESPONSIVE UI
==================================================

Dashboard harus responsive.

WAJIB cek:
- 360px
- 375px
- 390px
- 414px
- desktop 1280px+
- desktop 1440px+

Mobile:
- tidak boleh horizontal overflow
- KPI card harus wrap menjadi 1–2 kolom sesuai ruang
- tabel boleh berubah menjadi card/list jika diperlukan
- chart tidak boleh keluar layar
- sidebar/admin navigation tidak boleh menutupi content
- tombol tetap bisa ditekan
- text tidak terpotong secara buruk

Desktop:
- gunakan ruang dengan baik
- KPI tidak terlalu besar
- tidak ada empty space berlebihan
- tabel tetap mudah dibaca.

Jangan mengubah desain customer storefront yang sudah PASS.

==================================================
14. VISUAL CONSISTENCY
==================================================

Ikuti design system Digital Cell yang sudah ada:

- clean
- white/light base
- midnight/dark navy
- rounded card
- subtle border/shadow
- typography konsisten
- spacing konsisten
- icon konsisten

Jangan membuat dashboard dengan desain berbeda total dari admin panel existing.

Jangan mengubah customer UI kecuali ada bug yang benar-benar berasal dari shared component.

==================================================
15. ADMIN NAVIGATION
==================================================

Pastikan admin navigation memiliki minimal:

Dashboard
Products
Categories
Orders
Users
Payment/Settings jika tersedia

Active menu harus jelas.

Mobile admin navigation:
- tidak boleh overflow
- menu bisa dibuka/tutup
- tidak menutupi content secara permanen.

Gunakan layout admin existing.

==================================================
16. DATA ACCESS / PERFORMANCE
==================================================

Jangan melakukan query berulang tanpa alasan.

Jika memungkinkan:
- gunakan aggregate/count/groupBy Prisma
- gunakan select field seperlunya
- jangan fetch seluruh database hanya untuk menghitung KPI
- gunakan Promise.all untuk query independen jika aman

Hindari N+1 query.

Recent orders dan top products harus memiliki limit.

Jangan membuat dashboard lambat hanya karena terlalu banyak query.

==================================================
17. PRISMA
==================================================

Gunakan Prisma/schema existing.

Jangan:
- membuat migration baru
- mengubah schema
- menghapus migration
- mengganti database
- mengganti provider database
- reset DB

Kecuali ditemukan bug kritis yang benar-benar mengharuskan perubahan schema.

Jika schema sudah cukup:
jangan menyentuh schema.

==================================================
18. API / SERVICE ARCHITECTURE
==================================================

Ikuti architecture existing.

Jika project menggunakan:
- repository
- service
- API route
- server action

ikuti pola yang sudah digunakan.

Jangan membuat semua logic dalam page.tsx.

Dashboard data harus modular.

Contoh struktur yang boleh digunakan jika cocok dengan architecture:

dashboard-service
dashboard repository
dashboard statistics helper

Tetapi jangan membuat file hanya untuk formalitas.

==================================================
19. TESTING
==================================================

Setelah implementasi:

A. Typecheck
npm run typecheck

HARUS:
PASS

B. Build
npm run build

HARUS:
PASS

C. Jalankan development server.

Verifikasi route admin dashboard.

D. Smoke test:

Guest:
- /admin/dashboard -> redirect/login

Customer:
- /admin/dashboard -> 401/403/redirect sesuai architecture

Admin:
- /admin/dashboard -> 200

E. Verifikasi KPI.

Dengan data development:
- angka harus sesuai database.

F. Verifikasi recent orders.

G. Verifikasi top products.

H. Verifikasi low stock.

I. Verifikasi empty database behavior jika aman dilakukan tanpa reset production.

==================================================
20. BROWSER / MOBILE VERIFICATION
==================================================

Verifikasi dashboard secara visual pada:

360px
375px
390px
414px
desktop

Perhatikan:

- horizontal scrollbar
- card overflow
- table overflow
- chart overflow
- sidebar
- header
- buttons
- typography
- spacing
- modal
- dropdown

Jika ada masalah responsive:
perbaiki hanya dashboard/admin component terkait.

Jangan merusak customer storefront yang sebelumnya sudah dinyatakan stabil.

==================================================
21. REGRESSION TEST
==================================================

Pastikan fitur existing tetap PASS:

Customer:
- /
- /products
- /categories
- /search
- /product/[slug]
- /checkout
- /orders
- /profile

Admin:
- /admin/dashboard
- /admin/products
- /admin/categories
- /admin/orders
- /admin/users

Pastikan:
- tidak ada 500
- tidak ada client-side exception
- tidak ada hydration error
- tidak ada authentication bypass.

==================================================
22. JANGAN MENYENTUH PAYMENT PRODUCTION
==================================================

PENTING:

Jangan mengaktifkan Midtrans production.

Jangan meminta atau membuat credential payment.

Jangan memasukkan:
- PAYMENT_PROVIDER_MIDTRANS_SERVER_KEY
- secret payment
- API key

ke source code.

Jangan melakukan transaksi uang nyata.

Payment production tetap menjadi tahap berikutnya setelah dashboard dan seluruh admin flow stabil.

==================================================
23. GIT
==================================================

Sebelum commit:

git status --short
git diff --check

Pastikan:
- working tree hanya berisi perubahan yang memang diperlukan
- tidak ada .env
- tidak ada secret
- tidak ada credential
- tidak ada build artifact
- tidak ada temporary test file

Jika ada file temporary dari testing:
hapus sebelum commit.

==================================================
24. COMMIT & PUSH
==================================================

Jika seluruh implementasi dan verifikasi PASS:

commit:

feat(admin): add real dashboard statistics

Lalu:

git push origin main

TANPA force push.

Setelah push:

git status --short
git log -1 --oneline
git status -sb

Pastikan:
- working tree bersih
- origin/main sinkron
- commit berhasil masuk.

==================================================
25. FINAL REPORT
==================================================

Setelah selesai laporkan:

1. File yang diubah
2. Dashboard features yang ditambahkan
3. KPI yang tersedia
4. Sumber data masing-masing KPI
5. Recent orders
6. Top products
7. Low stock
8. Authentication verification
9. Mobile responsive verification
10. Empty database verification
11. typecheck result
12. build result
13. smoke test result
14. git commit hash
15. push status

Jika ada blocker:
jelaskan blocker sebenarnya.

Jangan mengklaim PASS jika belum benar-benar dites.

ATURAN PALING PENTING:

- Jangan reset database.
- Jangan menghapus data existing.
- Jangan mengubah schema tanpa alasan kuat.
- Jangan membuat mock data untuk menggantikan database.
- Jangan mengaktifkan payment production.
- Jangan merusak Categories/Product yang sudah PASS.
- Jangan merusak customer UI.
- Jangan force push.
- Jangan commit secret.
- Jangan berhenti hanya karena UI terlihat benar; lakukan typecheck, build, smoke test, dan regression.


```
# Prompt: Admin Categories — Full CRUD & Verification
```
PROMPT: ADMIN CATEGORIES — FULL CRUD, STATUS, RELATION PRODUCT & VERIFICATION

Project: Digital Cell / toko-online

Konteks:
Admin Products sudah selesai dan terverifikasi:
- List products PASS
- Tambah product PASS
- Edit harga/stok PASS
- Toggle aktif/nonaktif PASS
- Hapus product PASS
- Customer hanya melihat product aktif
- PostgreSQL development sudah digunakan
- Typecheck PASS
- Build PASS
- Smoke test PASS
- Git working tree bersih
- Jangan merusak fitur Products yang sudah selesai.

Sekarang fokus hanya pada MODULE ADMIN CATEGORIES.

Jangan mengubah payment, checkout, authentication, order, database schema secara besar, atau UI customer yang sudah stabil kecuali memang diperlukan oleh integrasi category.

==================================================
1. AUDIT TERLEBIH DAHULU
==================================================

Sebelum mengubah kode:

- Audit struktur project.
- Cari seluruh implementasi category:
  - category service
  - category repository
  - Prisma model Category
  - API/server actions category
  - halaman admin categories
  - form category
  - customer category listing
  - product-category relationship
- Identifikasi route yang sudah tersedia.
- Identifikasi apakah slug dibuat otomatis atau manual.
- Identifikasi field Category yang benar-benar tersedia di Prisma schema.
- Jangan membuat field database baru jika tidak diperlukan.
- Jangan membuat migration baru hanya untuk menyelesaikan UI.
- Gunakan struktur database yang sudah ada.

Jika fitur category ternyata sudah sebagian besar tersedia, jangan rewrite.
Perbaiki dan lengkapi implementasi existing.

==================================================
2. ADMIN CATEGORY LIST
==================================================

Pastikan halaman admin categories mempunyai:

- daftar semua kategori
- nama kategori
- slug
- jumlah produk bila data tersedia
- status aktif/nonaktif
- tombol edit
- tombol aktif/nonaktif
- tombol hapus
- tombol tambah kategori
- loading state
- empty state
- error state

Pagination/search boleh digunakan jika struktur project sudah mendukung.

Jangan membuat UI terlalu kompleks.

Pastikan desktop dan mobile tetap usable.

==================================================
3. TAMBAH KATEGORI
==================================================

Buat/benahi form tambah kategori.

Minimal:

- Nama kategori
- Slug jika memang field tersebut digunakan oleh schema
- Status aktif/nonaktif jika memang didukung schema

Validasi:

- nama wajib
- nama tidak boleh kosong
- slug harus valid jika digunakan
- cegah duplicate slug
- cegah duplicate kategori yang memang tidak diperbolehkan

Setelah submit:

- data benar-benar masuk PostgreSQL development DB
- tampilkan feedback sukses
- list admin harus menampilkan kategori baru
- refresh halaman tidak boleh menghilangkan data

Jangan menggunakan mock/local-only state sebagai sumber utama.

==================================================
4. EDIT KATEGORI
==================================================

Pastikan admin dapat:

- membuka kategori
- mengubah nama
- mengubah slug jika memang diperbolehkan
- menyimpan perubahan
- melihat hasil setelah refresh

Perhatikan hubungan dengan product.

Jika slug dipakai sebagai URL customer:

- jangan menyebabkan route rusak
- jangan membuat duplicate slug
- jangan mengubah slug secara otomatis tanpa alasan

Jika kategori mempunyai product:

- edit kategori tidak boleh menghapus product
- product tetap mempunyai relasi yang benar

==================================================
5. TOGGLE AKTIF / NONAKTIF
==================================================

Implementasikan toggle category.

Ketika aktif:

- kategori muncul di customer UI sesuai aturan existing.

Ketika nonaktif:

- kategori tidak boleh muncul sebagai kategori customer aktif.

PENTING:

Jangan menghapus product hanya karena kategori dinonaktifkan.

Product yang terkait tetap aman di database.

Pastikan status yang digunakan UI berasal dari database, bukan hanya React state.

Setelah refresh:

- status tetap benar.

==================================================
6. HAPUS KATEGORI
==================================================

Audit terlebih dahulu bagaimana relation Product -> Category bekerja.

Jangan langsung menghapus kategori jika database relation dapat menyebabkan:

- foreign key error
- product ikut terhapus
- orphan data
- product menjadi rusak

Jika kategori mempunyai product:

gunakan behavior yang paling aman berdasarkan schema existing.

Prioritas:

1. Jangan menghapus product.
2. Jangan merusak order/history.
3. Jangan merusak product data.
4. Jika kategori tidak boleh dihapus ketika masih mempunyai product, tampilkan pesan yang jelas.
5. Jika schema memang mendukung pemindahan/unassign category, gunakan mekanisme tersebut.
6. Jangan mengubah schema hanya untuk memaksakan delete.

Setelah kategori berhasil dihapus:

- hilang dari admin list
- tidak muncul di customer
- product tetap aman.

==================================================
7. CUSTOMER CATEGORY
==================================================

Verifikasi customer UI.

Pastikan:

- hanya kategori aktif yang ditampilkan
- kategori nonaktif tidak muncul
- kategori yang dihapus tidak muncul
- kategori aktif tetap dapat dibuka
- slug route tetap bekerja
- product yang terkait kategori tampil benar

Jangan mengubah desain customer UI yang sudah disetujui.

Pertahankan UI Digital Cell yang sekarang.

==================================================
8. PRODUCT RELATION
==================================================

Karena Products sudah selesai, lakukan regression khusus relation.

Test minimal:

A. Buat kategori test.
B. Pastikan kategori muncul.
C. Hubungkan/gunakan kategori pada product jika fitur tersebut sudah tersedia.
D. Buka product.
E. Pastikan product masih muncul.
F. Nonaktifkan kategori.
G. Pastikan product tidak rusak.
H. Aktifkan kembali kategori.
I. Pastikan product kembali dapat diakses sesuai aturan.
J. Refresh.
K. Pastikan data tetap konsisten.

Jangan mengubah harga, stok, status, atau data product secara tidak perlu.

==================================================
9. MOBILE RESPONSIVE ADMIN
==================================================

Verifikasi halaman admin Categories pada:

- mobile sekitar 360px
- mobile sekitar 375px
- mobile sekitar 390px
- mobile sekitar 414px
- desktop

Pastikan:

- tabel tidak menyebabkan horizontal overflow yang buruk
- tombol tidak keluar layar
- form tidak melebar
- modal tidak terpotong
- action button tetap bisa ditekan
- text tidak overlap
- status badge tidak rusak
- navigation tetap usable

Gunakan pola responsive yang sudah digunakan project.

Jangan mengubah global CSS jika tidak diperlukan.

==================================================
10. ERROR HANDLING
==================================================

Pastikan error category tidak menyebabkan:

- client-side exception
- blank screen
- Next.js Application Error
- server-side 500 yang tidak tertangani

Handle minimal:

- duplicate category
- invalid slug
- category tidak ditemukan
- delete gagal karena relation
- database error
- unauthorized admin request

Untuk user/admin tampilkan pesan yang jelas.

Jangan expose stack trace atau secret ke browser.

==================================================
11. SECURITY
==================================================

Pastikan semua mutation category:

- create
- update
- toggle
- delete

hanya dapat dilakukan oleh admin.

Customer/public endpoint tidak boleh dapat melakukan mutation.

Pastikan authorization dilakukan di server-side.

Jangan hanya menyembunyikan tombol di UI.

==================================================
12. TEST
==================================================

Setelah implementasi jalankan:

- npm run typecheck
- npm run build

Kemudian jalankan development server sesuai setup project.

Lakukan smoke test:

PUBLIC:
- /categories
- category detail
- /products
- product detail
- search jika category digunakan oleh search

ADMIN:
- /admin/categories
- tambah category
- edit category
- toggle category
- delete category
- /admin/products

REGRESSION:
- product existing tetap muncul
- product aktif tetap tampil
- product nonaktif tetap tidak tampil
- category nonaktif tidak tampil ke customer
- category aktif tampil kembali setelah diaktifkan.

==================================================
13. DATABASE VERIFICATION
==================================================

Jangan hanya mengandalkan UI.

Untuk setiap operasi penting:

CREATE:
- cek data benar-benar masuk DB.

UPDATE:
- cek data berubah di DB.

TOGGLE:
- cek isActive/status berubah di DB.

DELETE:
- cek record benar-benar sesuai behavior yang ditentukan schema.

Pastikan refresh browser tidak mengembalikan data lama.

==================================================
14. GIT
==================================================

Sebelum commit:

git status --short
git diff --check

Pastikan:

- tidak ada .env
- tidak ada credential
- tidak ada secret
- tidak ada build artifact
- tidak ada temporary test file
- tidak ada screenshot/debug file
- tidak ada perubahan unrelated

Jika ada perubahan unrelated, jangan ikut commit.

==================================================
15. COMMIT
==================================================

Jika semua PASS dan memang ada perubahan kode:

commit message:

feat(admin): complete category management

Kemudian:

git push origin main

JANGAN force push.

Setelah push:

git status --short
git log -1 --oneline
git status -sb

Pastikan origin/main sinkron.

==================================================
16. HASIL AKHIR
==================================================

Berikan laporan ringkas:

1. Audit category
2. File yang diubah
3. Fitur yang diperbaiki
4. Database verification
5. Admin CRUD result
6. Customer category result
7. Product relation result
8. Mobile responsive result
9. Typecheck result
10. Build result
11. Smoke test result
12. Git commit
13. Git push
14. Blocker jika ada

PENTING:

- Jangan membuat ulang project.
- Jangan menghapus fitur existing.
- Jangan reset database.
- Jangan membuat mock data sebagai pengganti DB.
- Jangan mengubah payment.
- Jangan mengubah checkout.
- Jangan mengubah authentication.
- Jangan mengubah UI customer yang sudah disetujui kecuali bug category memang membutuhkan perubahan.
- Jangan membuat empty commit.
- Jangan force push.
- Jangan berhenti hanya karena implementasi selesai; lakukan verification sampai PASS.
- Jika ditemukan bug, perbaiki dan ulangi test.
- Jika blocker membutuhkan keputusan saya, berhenti sebelum melakukan perubahan berisiko dan laporkan blocker tersebut.


```
# Prompt: Audit Admin Dashboard & Products
```


Audit dan test Admin Dashboard + Products pada project toko-online.

Fokus hanya pada fitur yang sudah ada. Jangan membuat fitur baru yang belum diperlukan dan jangan mengubah customer UI.

1. Jalankan project dalam development mode.
2. Login sebagai admin menggunakan authentication yang sudah tersedia.
3. Test:
   - /admin/dashboard
   - /admin/products
4. Dashboard:
   - pastikan halaman bisa dibuka
   - KPI/data tampil benar
   - empty state aman jika data kosong
   - tidak ada client-side exception
5. Products:
   - daftar produk tampil
   - detail/data produk benar
   - tambah produk
   - edit produk
   - ubah harga
   - ubah stok
   - aktif/nonaktif produk
   - hapus produk
   - setelah mutation, data tetap benar setelah refresh
6. Pastikan perubahan produk benar-benar tersimpan ke database development.
7. Pastikan customer tetap bisa melihat produk aktif dan tidak bisa melihat produk yang nonaktif.
8. Test responsive admin minimal desktop dan mobile tanpa redesign.
9. Jangan mengubah:
   - payment/Midtrans
   - database schema/migration
   - authentication architecture
   - cart
   - customer UI
10. Jika ditemukan bug, perbaiki hanya bug yang diperlukan.
11. Jalankan:
   - npm run typecheck
   - npm run build
   - smoke test admin
   - git diff --check
   - git status --short
12. Jika semua PASS, commit dan push ke origin main tanpa force push.
13. Jika ada blocker, jangan commit dan tampilkan penyebabnya.

PENTING:
Jangan membuat mock data baru jika database development sudah memiliki data.
Jangan membuat empty commit.
Jangan melakukan redesign.
Fokus Dashboard + Products saja.
```
# Prompt: Perbaiki Mobile Product Action Bar
```

Perbaiki UI mobile halaman detail produk Digital Cell.

Masalah:
- Pada mobile, area harga + tombol "Tambah ke Keranjang" + "Beli Sekarang" terlalu besar/tinggi.
- Bottom action bar menutupi bagian produk terkait dan konten halaman.
- Harga dan tombol terlihat terlalu naik/menempati terlalu banyak viewport.
- Screenshot menunjukkan action area mengambil ruang berlebihan.

Tugas:
1. Audit komponen/layout halaman product detail dan CSS responsive yang mengatur:
   - harga
   - quantity jika ada
   - tombol Tambah ke Keranjang
   - tombol Beli Sekarang
   - sticky/fixed bottom action bar
2. Khusus mobile viewport 360/375/390/414px:
   - kecilkan tinggi action bar secara proporsional
   - kurangi padding dan spacing yang berlebihan
   - jangan membuat tombol terlalu tinggi
   - harga tetap jelas tetapi tidak mengambil banyak ruang
   - pastikan action bar tidak menutupi produk terkait
3. Jika action bar menggunakan position: fixed/sticky:
   - pastikan konten halaman mempunyai bottom padding yang cukup
   - jangan sampai elemen fixed menimpa konten
   - jangan membuat ruang kosong berlebihan
4. Desktop jangan diubah kecuali ada CSS bersama yang memang diperlukan.
5. Pertahankan desain Digital Cell yang sekarang: clean, white, midnight blue.
6. Jangan mengubah:
   - database
   - API
   - authentication
   - cart logic
   - payment
   - admin
7. Jangan redesign halaman. Fokus hanya pada ukuran, spacing, positioning, dan responsive behavior action bar.
8. Verifikasi dengan viewport:
   - 360x800
   - 375x812
   - 390x844
   - 414x896
   - desktop
9. Pastikan:
   - produk terkait tetap terlihat
   - tidak ada horizontal overflow
   - tidak ada konten tertutup action bar
   - tombol tetap mudah ditekan
   - harga dan tombol tetap terlihat jelas
10. Jalankan typecheck, build, dan smoke test UI.
11. Jalankan `git diff --check` dan `git status --short`.
12. Jika semua PASS, commit dan push ke origin main tanpa force push.

PENTING:
Jangan mengubah desain keseluruhan. Hanya perbaiki mobile product-detail action bar yang terlalu besar dan menutupi konten.

```
# Prompt: Perbaiki Reset Cart Saat Kembali Belanja
```
Perbaiki bug customer cart flow pada project toko-online.

Masalah yang ditemukan:
- User sudah berada di halaman checkout.
- Jika user kembali ke halaman utama lalu membeli produk lagi, cart lama masih terbawa.
- Akibatnya checkout berikutnya menjadi 2 produk / 2 item padahal user hanya ingin membeli 1 produk baru.
- State cart harus benar-benar mencerminkan produk yang sedang dipilih user.
- Jangan sampai quantity, subtotal, atau total pembayaran menjadi tidak sinkron.

Tugas:
1. Audit implementasi cart state saat:
   - tambah produk
   - buka checkout
   - kembali ke halaman utama
   - membeli produk lagi
   - refresh halaman
   - logout/login
2. Tentukan sumber state cart yang sebenarnya digunakan (client state, localStorage, session, atau lainnya).
3. Perbaiki agar ketika user keluar dari checkout untuk kembali berbelanja, cart lama tidak otomatis menggandakan transaksi baru.
4. Jangan menghapus cart secara sembarangan saat user hanya berpindah antar-step checkout.
5. Jika user memang memilih "Beli Sekarang" dari halaman produk, pastikan flow tersebut menghasilkan cart/order context yang sesuai dengan produk yang dipilih.
6. Pastikan produk yang sama tidak menjadi duplikat hanya karena navigasi/re-entry.
7. Pastikan quantity, subtotal, total dan daftar item selalu dihitung dari state cart terbaru.
8. Setelah perbaikan, test minimal:
   - beli 1 produk → checkout = 1 produk / 1 item
   - kembali ke beranda → pilih produk → checkout lagi = hanya 1 produk / 1 item
   - tambah quantity 2 → checkout = 1 produk / 2 item
   - refresh checkout → state tetap konsisten
   - kembali antar-step checkout → cart tidak hilang
9. Jangan mengubah database schema, authentication, payment gateway, admin panel, atau desain UI.
10. Jangan membuat mock payment.
11. Jalankan typecheck dan build.
12. Jalankan smoke test customer flow.
13. Jika semua PASS, commit dan push ke origin main tanpa force push.
14. Sebelum commit:
   git diff --check
   git status --short
15. Jangan commit jika ada error atau secret.

PENTING:
Fokus hanya pada bug cart/state dan konsistensi subtotal/total. Jangan melakukan redesign UI atau perubahan backend yang tidak diperlukan.


```
# Prompt: Buat Akun Test Customer
```
Buat 1 akun customer untuk testing di environment development toko-online.

Gunakan flow register yang sudah tersedia, jangan membuat akun langsung lewat database dan jangan mengubah kode.

Data test:
- Nomor WhatsApp: +6281234567890
- Password: Test12345!
- Role: customer

Setelah akun berhasil dibuat:
1. Pastikan login menggunakan akun tersebut berhasil.
2. Pastikan session/JWT terbentuk dengan benar.
3. Jangan mengubah role menjadi admin.
4. Jangan membuat akun tambahan.
5. Jangan commit atau push perubahan karena ini hanya data development.
6. Tampilkan hasil akhir dan kredensial akun test yang berhasil dibuat.


```
# 
```
Jalankan development server project toko-online sekarang.

- Pastikan berada di /root/toko-online
- Gunakan mode development saja: npm run dev
- Jangan mengubah kode, database, environment, atau konfigurasi.
- Jika ada proses lama yang memakai port 3000, hentikan proses tersebut dengan aman lalu jalankan dev server.
- Tunggu sampai server benar-benar ready.
- Tampilkan URL/port yang aktif.
- Jika terjadi error, jangan memperbaiki kode dulu; tampilkan error lengkapnya.


```
# Prompt: Diagnose Production Host Error
```

Sekarang jangan ubah fitur Facebook, UI, upload, atau architecture.

Fokus hanya memperbaiki production host error pada:

https://contentpilot.biz.id

Browser = Working
Cloudflare = Working
Host = Error / Bad Gateway

Lakukan diagnosis terlebih dahulu, jangan menebak.

Periksa:

1. Status service web Content Pilot.
2. Status API/backend.
3. Status nginx.
4. Port yang sedang digunakan oleh web dan API.
5. Apakah nginx upstream mengarah ke port/service yang benar.
6. Apakah process web/backend sedang running.
7. Apakah service crash setelah restart.
8. Cek:
   - systemctl status nginx
   - systemctl status terkait Content Pilot jika ada
   - ss -lntp
   - nginx -t
   - nginx error log
   - service/application log
9. Test endpoint dari server:
   - /health
   - /ready
   - /api/platforms
10. Test origin secara lokal menggunakan curl ke port sebenarnya.
11. Pastikan production tidak menggunakan localhost:4000 jika service sebenarnya berjalan pada port lain.
12. Pastikan nginx → upstream → application chain benar.
13. Jangan mengubah source code fitur Facebook.
14. Jangan mengubah database.
15. Jangan menghapus konfigurasi.
16. Jangan melakukan reinstall dependency kecuali benar-benar diperlukan.

Setelah menemukan penyebabnya:

- jelaskan root cause terlebih dahulu
- lakukan perbaikan minimal
- restart service yang memang diperlukan
- jalankan nginx config test
- test endpoint dari server
- test domain production melalui HTTPS
- pastikan Cloudflare kembali menunjukkan Host Working

Kemudian lakukan verification:

API HEALTH: PASS
WEB: PASS
NGINX: PASS
PRODUCTION HTTPS: PASS
CONTENTPILOT.BIZ.ID: PASS

Jika semuanya PASS, STOP.

Jangan melanjutkan ke Facebook upload sebelum host production benar-benar sehat.

```
# Prompt: UI Final Audit & Responsive Verification
```

Audit dan pastikan UI project Digital Cell/toko-online benar-benar siap sebelum lanjut ke roadmap berikutnya.

Fokus UI saja. Jangan mengubah database, Prisma, auth, payment, API, atau business logic.

1. Audit seluruh halaman customer:
   - /
   - /products
   - /categories
   - /category/[slug]
   - /product/[slug]
   - /search
   - /cart
   - /checkout
   - /orders
   - /profile
   - /favorite
   - /login
   - /register

2. Test minimal viewport:
   - 360px
   - 375px
   - 390px
   - 414px
   - desktop

3. Pastikan:
   - tidak ada horizontal overflow
   - tidak ada tombol terpotong
   - sticky bottom navigation aman
   - tombol beli/tambah keranjang tidak keluar layar
   - gambar tidak merusak layout
   - typography proporsional
   - spacing konsisten
   - header responsive
   - card produk responsive
   - modal/dropdown tidak keluar viewport
   - loading, empty dan error state tetap rapi
   - halaman tidak blank/client-side exception

4. Pertahankan desain Digital Cell yang sekarang.
   Jangan redesign total dan jangan mengubah warna/identitas visual tanpa alasan.

5. Jika menemukan masalah responsive/UI:
   perbaiki langsung secara modular dan minimal.
   Jangan menyentuh backend/database/payment.

6. Setelah perbaikan jalankan:
   - npm run typecheck
   - npm run build
   - development server
   - smoke test route utama

7. Pastikan git:
   - git diff --check
   - git status --short
   - tidak ada secret/.env/artifact

8. Jika semua PASS, commit dan push:
   fix(ui): finalize responsive customer interface

Jangan lanjut ke roadmap/payment/admin baru.
Tujuan tahap ini hanya memastikan UI customer benar-benar stabil di mobile dan desktop.

```
# Prompt berikutnya — Phase 37
```

Prompt: Phase 37 — Fix Mobile Client-Side Exception

Project: Digital Cell / toko-online

Kondisi:
- Phase sebelumnya memperbaiki responsive halaman detail produk dan sudah di-commit/push.
- Saat halaman dibuka melalui mobile browser, muncul:
  "Application error: a client-side exception has occurred"
- Jangan menganggap masalah ini hanya CSS/responsive.
- Jangan membuat redesign.
- Gunakan development mode untuk diagnosis dan testing.
- Jangan menyentuh payment production.

Tugas:

1. Audit error runtime client-side secara nyata.
2. Jalankan development server dan reproduksi halaman yang menghasilkan:
   "Application error: a client-side exception has occurred".
3. Periksa terminal/server log dan browser console/stack trace jika tersedia.
4. Identifikasi file, component, function, atau dependency yang benar-benar menyebabkan exception.
5. Audit perubahan terakhir pada commit:
   `43986ce fix(ui): fix mobile product detail responsiveness`
   terutama file yang diubah pada commit tersebut.
6. Perbaiki root cause saja.
7. Jangan menutupi error dengan try/catch kosong, suppress error, atau fallback palsu.
8. Jangan mengubah:
   - database/schema
   - Prisma
   - authentication
   - payment
   - cart logic
   - admin logic
   - API contract
   - desain utama
9. Setelah diperbaiki, pastikan halaman berikut tetap normal:
   - /products
   - /categories
   - /category/streaming
   - /product/netflix-premium-test
   - /search
   - /cart
   - /profile
10. Pastikan halaman detail produk tetap responsive pada:
   - 360px
   - 375px
   - 390px
   - 414px
   - desktop
11. Pastikan tidak ada horizontal overflow.

Verifikasi:
- npm run typecheck
- npm run build
- development server
- smoke test halaman public
- smoke test product detail
- test mobile viewport
- git diff --check
- git status --short

Jika semua PASS:
- commit perubahan
- push ke origin main
- jangan force push

Commit message:
fix(ui): resolve mobile client runtime exception

Laporan akhir harus menyebut:
- root cause exception
- file yang menyebabkan
- perubahan yang dilakukan
- hasil test
- commit hash
- push status

PENTING:
Jangan membuat perubahan jika belum menemukan root cause.
Jangan hanya mengatakan "build PASS" karena error ini terjadi saat runtime browser.

```
# 
```
Prompt: Phase 36 — Fix Mobile Responsive Product Detail

Project: Digital Cell / toko-online

Kondisi:
- Backend, authentication, product CRUD, admin panel, dan customer flow sudah PASS.
- Produk test sudah tersedia di development database.
- Saat dibuka di mobile, halaman detail produk masih mengalami horizontal overflow.
- Screenshot menunjukkan tombol "Beli Sekarang" terpotong di sisi kanan dan action bar terlalu lebar.
- Jangan membuat desain baru.

Tugas:

1. Audit halaman detail produk /product/[slug] khusus viewport mobile.
2. Perbaiki responsive layout agar:
   - tidak ada horizontal scroll/overflow.
   - seluruh konten berada dalam viewport.
   - tombol "Tambah ke Keranjang" tidak melebar keluar layar.
   - tombol "Beli Sekarang" selalu terlihat penuh.
   - action bar responsif pada layar kecil.
   - harga, badge, thumbnail, gambar utama, dan informasi produk tetap proporsional.
   - sticky/fixed bottom navigation tidak menutupi tombol/action area.
3. Gunakan CSS/Tailwind responsive yang sudah digunakan project.
4. Jangan mengubah:
   - database/schema
   - API
   - authentication
   - payment
   - cart logic
   - admin logic
   - desain desktop yang sudah benar
5. Fokus hanya pada responsive behavior.
6. Pastikan breakpoint mobile, tablet, dan desktop tetap valid.
7. Jangan menggunakan hardcoded width yang menyebabkan overflow.
8. Pastikan tombol action bisa:
   - stack/resize pada mobile jika diperlukan
   - tetap horizontal pada layar yang cukup lebar.
9. Cari juga elemen lain di halaman detail yang menyebabkan `overflow-x`.

Verifikasi wajib:
- npm run typecheck
- npm run build
- jalankan development server
- test viewport mobile sekitar 360px, 375px, 390px, 414px
- test desktop
- pastikan tidak ada horizontal overflow
- pastikan tombol "Beli Sekarang" terlihat penuh
- pastikan "Tambah ke Keranjang" tetap berfungsi
- git diff --check
- git status --short

Jika semua PASS:
- langsung commit perubahan.
- langsung push ke origin main.
- jangan force push.

Commit:
fix(ui): fix mobile product detail responsiveness

Jangan mengubah fitur selain responsive UI.


```
# Prompt: Phase 35 — Add 1 Test Product
```

Prompt: Phase 35 — Add One Test Product for Admin CRUD

Project: Digital Cell / toko-online

Kondisi:
- Admin Panel sudah terimplementasi dan terverifikasi.
- Development mode sedang digunakan untuk testing.
- Jangan ubah desain UI.
- Jangan ubah schema/migration.
- Jangan aktifkan payment production.

Tugas:

1. Gunakan database development yang sedang dipakai project.
2. Tambahkan tepat 1 produk test melalui mekanisme database/service yang memang digunakan aplikasi, bukan hardcode/mock di frontend.

Data produk:
- Nama: Netflix Premium Test
- Slug: netflix-premium-test
- Kategori: gunakan kategori Streaming yang sudah ada.
- Harga: 65000
- Stok: 50
- Status: aktif
- Deskripsi: Produk test untuk verifikasi CRUD Admin Panel.

3. Jika kategori Streaming belum ada, gunakan kategori existing yang paling sesuai. Jangan membuat perubahan schema.
4. Pastikan produk benar-benar tersimpan di database dan muncul di:
   - /products
   - /category/[slug]
   - /admin/products
5. Jangan membuat produk duplikat jika produk dengan slug tersebut sudah ada.
6. Setelah data tersedia, verifikasi Admin CRUD:
   - produk tampil
   - edit nama/harga/stok
   - simpan perubahan
   - refresh tetap tersimpan
   - jangan hapus produk test sebelum verifikasi selesai.
7. Jalankan:
   - npm run typecheck
   - npm run build
   - development server
   - smoke test produk dan admin
   - git diff --check
   - git status --short
8. Jika hanya terjadi perubahan data development dan tidak ada perubahan kode:
   - jangan membuat empty commit.
   - jangan push perubahan kode yang tidak diperlukan.
9. Jika perlu membuat seed/script kecil agar data test reproducible, buat secara modular dan aman, lalu commit + push tanpa force.
10. Jangan menyentuh Midtrans/payment production.

Laporan akhir:
- Produk berhasil dibuat atau sudah ada.
- ID/slug produk.
- Harga dan stok.
- Verifikasi halaman customer.
- Verifikasi Admin CRUD.
- typecheck/build status.
- apakah ada perubahan kode.
- commit/push hanya jika memang ada perubahan kode.

```
# Prompt berikutnya — Phase 34: Admin Panel Dev Verification
```

Prompt: Phase 34 — Admin Panel Development Verification

Project: Digital Cell / toko-online

Lanjutkan dari kondisi repository saat ini. Jangan membuat ulang project dan jangan mengubah desain customer yang sudah selesai.

Fokus tahap ini:
VERIFIKASI DAN PERBAIKI ADMIN PANEL DALAM MODE DEVELOPMENT SAJA.

Batasan:
- Jangan membutuhkan Midtrans Server Key.
- Jangan melakukan transaksi pembayaran nyata.
- Jangan mengubah schema/migration database.
- Jangan redesign UI.
- Jangan menghapus fitur existing.
- Jangan membuat mock payment production.
- Jangan force push.

Tugas:

1. Jalankan project dalam development mode.
2. Audit seluruh route admin yang sudah tersedia:
   - /admin/dashboard
   - /admin/products
   - /admin/categories
   - /admin/orders
   - /admin/users
   - /admin/payments jika sudah tersedia
3. Pastikan admin authentication/requireAdmin bekerja:
   - guest -> redirect/login
   - customer -> tidak boleh mengakses admin
   - admin -> bisa membuka seluruh halaman admin yang memang tersedia.
4. Test CRUD produk:
   - list produk
   - tambah produk
   - edit produk
   - hapus produk
   - validasi form
   - refresh halaman harus mempertahankan data database.
5. Test kategori:
   - list
   - tambah
   - edit
   - hapus
   - validasi relasi dengan produk.
6. Test orders:
   - admin dapat melihat order customer
   - detail order dapat dibuka
   - status order tampil benar
   - jangan membuat transaksi/payment baru.
7. Test users:
   - admin dapat melihat data user yang memang diizinkan
   - passwordHash/secret tidak boleh ditampilkan.
8. Periksa loading state, empty state, error state, dan tombol/form pada admin.
9. Pastikan tidak ada server-side exception/500.
10. Jalankan:
    - npm run typecheck
    - npm run build
    - development server dan smoke test route admin
    - git diff --check
    - git status --short

Jika ada bug nyata, perbaiki hanya bagian yang diperlukan.

Setelah semua PASS:
- jangan membuat empty commit;
- commit seluruh perubahan yang memang diperlukan;
- git push origin main tanpa force;
- setelah push verifikasi:
  git status --short
  git log -1 --oneline
  git status -sb

Laporan akhir harus berisi:
- route admin yang berhasil diverifikasi
- CRUD yang PASS
- authentication/authorization PASS
- typecheck/build PASS
- commit hash
- push status
- blocker jika masih ada.

Jika tidak ada perubahan kode, jangan commit.

```
# Prompt — Phase 35: Midtrans Sandbox / Dev Test
```

## Phase 35 — Midtrans Sandbox Payment Test

Project: /root/toko-online

Jangan aktifkan Midtrans production.

TUJUAN:
Aktifkan dan test payment menggunakan Midtrans Sandbox/Development terlebih dahulu.

Kondisi:
- Payment integration sudah PASS
- Checkout/order sudah PASS
- Admin Panel sudah PASS
- Security audit PASS
- Typecheck PASS
- Build PASS
- Production server PASS
- Midtrans production Server Key BELUM ADA

TUGAS:

1. Audit payment integration yang sudah ada.
2. Gunakan mode sandbox/development Midtrans.
3. Credential sandbox HARUS berasal dari environment variable.
4. Jangan hardcode Server Key/Client Key.
5. Jangan commit credential atau .env.
6. Jangan mengubah database schema/migration.
7. Jangan mengubah desain UI kecuali ada bug pada payment flow.

TEST FLOW:

Customer
→ Login
→ Pilih produk
→ Tambah ke cart
→ Checkout
→ Create Order
→ Generate payment
→ Buka payment page/checkout Midtrans Sandbox
→ Simulasikan pembayaran sandbox
→ Terima callback/webhook sandbox
→ Verifikasi status payment/order berubah benar

WAJIB VERIFIKASI:

- order dibuat dengan benar
- payment reference tersimpan benar
- status pending benar sebelum pembayaran
- callback/webhook tervalidasi
- status berubah setelah pembayaran sandbox
- order tidak bisa dibayar dua kali secara tidak valid
- user hanya dapat melihat order miliknya
- nominal pembayaran berasal dari server
- client tidak dapat memanipulasi total
- webhook tidak boleh dipercaya tanpa validasi signature
- error payment tidak menyebabkan 500
- failed/expired/cancel status ditangani dengan benar

ADMIN:
- admin dapat melihat status order/payment
- jangan membuat fitur baru yang tidak diperlukan

ENVIRONMENT:
- gunakan variable sandbox yang memang sudah didukung project
- jika variable belum jelas, audit kode dan .env.example terlebih dahulu
- jangan menebak nama credential
- jangan menampilkan nilai credential dalam laporan

REGRESSION:
- npm run typecheck
- npm run build
- production server
- payment sandbox test
- webhook/callback test
- git diff --check
- git status --short

GIT:
Jika ada perubahan kode:
- commit
- git push origin main
- jangan force push

Jika tidak ada perubahan kode:
- jangan membuat empty commit.

BATASAN KERAS:
- Sandbox/Development ONLY
- Tidak ada transaksi uang nyata
- Jangan gunakan Midtrans Production
- Jangan meminta atau memasukkan Production Server Key
- Jangan reset database
- Jangan ubah schema/migration
- Jangan redesign UI
- Jangan force push

Setelah selesai laporkan:
- payment sandbox status
- create payment status
- callback/webhook status
- order status update
- security validation
- typecheck
- build
- server
- commit/push
- blocker yang masih tersisa

```
# Phase 35 — Production Operations & Backup Readiness
```

## Phase 35 — Production Operations & Backup Readiness

Project: /root/toko-online

Lanjutkan roadmap dari kondisi terakhir.

STATUS:
- Customer flow: PASS
- Cart/Checkout/Order: PASS
- JWT/Auth/Authz: PASS
- Admin Panel: PASS
- Payment integration: PASS
- Security audit: PASS
- Typecheck: PASS
- Build: PASS
- Production server: PASS
- Payment tests: 103 PASS / 0 fail
- Security tests: 25/25 PASS
- Git clean dan sinkron origin/main
- Midtrans production hanya menunggu Server Key

TUJUAN:
Pastikan project siap secara operasional untuk production tanpa mengaktifkan payment production.

AUDIT:

1. Database Safety
- identifikasi database yang digunakan production
- pastikan DATABASE_URL hanya berasal dari environment
- jangan reset database
- jangan menghapus migration
- jangan mengubah schema jika tidak diperlukan
- pastikan migration yang ada dapat digunakan untuk deployment baru

2. Backup Readiness
- audit apakah mekanisme backup database sudah tersedia
- jika project sudah memiliki script backup, verifikasi script tersebut
- jika belum ada, buat mekanisme backup sederhana yang aman dan terdokumentasi
- backup harus tidak memasukkan credential ke Git
- jangan menjalankan destructive restore/reset
- jangan menghapus data production

3. Recovery Readiness
- dokumentasikan langkah restore database
- dokumentasikan migration deploy
- dokumentasikan cara menjalankan production server
- dokumentasikan environment variable yang dibutuhkan TANPA menuliskan nilai secret

4. Environment
- .env tetap gitignored
- .env.example hanya berisi nama variable, bukan secret
- cek DATABASE_URL
- SESSION/JWT secret
- payment environment variables
- pastikan tidak ada secret di source/client bundle

5. Production Process
- pastikan npm run build berhasil
- pastikan npm run start berhasil
- pastikan server dapat restart dengan benar
- jangan mengubah port production yang sekarang
- jangan mengubah Cloudflare/DNS
- jangan mengaktifkan payment production

6. Monitoring/Error
- audit apakah error production menghasilkan log yang berguna
- jangan membocorkan secret/password/database URL
- pastikan server-side exception tidak ditampilkan ke client

7. Documentation
Jika README.md sudah ada, gunakan README.md yang sama.
Jangan membuat README baru.

Tambahkan hanya dokumentasi yang benar-benar diperlukan:
- production setup
- database migration
- backup
- restore
- restart server
- environment variables yang diperlukan

Jangan menulis credential asli.

8. Regression
Jalankan:
- npm run typecheck
- npm run build
- production server
- smoke test public/customer/admin
- git diff --check
- git status --short

Jika ada masalah:
- perbaiki minimal
- jangan redesign UI
- jangan mengubah payment provider
- jangan reset database

GIT:
Jika ADA perubahan:
- git diff --check
- git status --short
- commit dengan message:
  chore(ops): harden production backup and recovery
- git push origin main
- jangan force push
- verifikasi origin/main sinkron

Jika TIDAK ADA perubahan:
- jangan membuat empty commit.

KARENA VPS BERISIKO MATI:
Setelah verifikasi PASS, langsung commit dan push jika memang ada perubahan.
Jangan berhenti hanya untuk laporan sebelum push.

BATASAN:
- jangan aktifkan Midtrans production
- jangan masukkan Server Key
- jangan transaksi uang nyata
- jangan reset database
- jangan ubah schema kecuali benar-benar wajib
- jangan force push

LAPORKAN:
- database readiness
- backup readiness
- recovery readiness
- environment/security
- production process
- monitoring/error handling
- dokumentasi
- typecheck
- build
- production server
- commit/push
- blocker tersisa

```
# Prompt Phase 34 — langsung push jika ada perubahan
```

## Phase 34 — Production Hardening & Final Security Audit

Project: /root/toko-online

Lanjutkan roadmap dari kondisi terakhir. Jangan mengulang fitur yang sudah PASS.

STATUS:
- Customer UI: PASS
- Catalog/category/product: PASS
- Cart: PASS
- Checkout/order: PASS
- JWT authentication: PASS
- Authorization/authz: PASS
- Admin Panel: PASS
- Admin CRUD: PASS
- Payment integration: PASS
- Midtrans production: BLOCKED hanya karena Server Key belum tersedia
- Typecheck: PASS
- Build: PASS
- Production server: PASS
- Git clean dan sinkron origin/main

TUJUAN:
Lakukan final security dan production-readiness audit tanpa redesign.

AUDIT:

1. Authentication
- JWT/session aman
- guest tidak dapat mengakses halaman protected
- customer tidak dapat mengakses admin
- admin tetap dapat mengakses admin
- login/register tidak membocorkan credential
- passwordHash tidak pernah dikirim ke client

2. Authorization
- verifikasi seluruh endpoint mutation
- customer hanya dapat mengubah data miliknya
- customer tidak dapat mengubah role
- customer tidak dapat mengakses order user lain
- admin endpoint benar-benar protected
- jangan hanya mengandalkan proteksi UI

3. Product/Cart/Order Security
- harga selalu berasal dari server/database
- client tidak dapat memanipulasi total pembayaran
- stok divalidasi server-side
- quantity divalidasi
- productId divalidasi
- order item tidak dapat dimanipulasi oleh user lain
- race condition stok tidak menimbulkan stok negatif jika architecture saat ini sudah mendukung proteksi

4. API Security
- validasi input pada endpoint penting
- method yang salah menghasilkan response yang benar
- unauthorized = 401
- forbidden = 403
- not found = 404
- jangan membocorkan stack trace/secret ke response production

5. Environment & Secrets
- .env tetap gitignored
- DATABASE_URL tidak masuk source
- JWT/session secret tidak masuk source
- Midtrans credential tidak di-hardcode
- tidak ada credential di client bundle
- tidak ada temporary/build artifact yang ter-track

6. Admin Security
- /admin dan API admin protected
- customer tidak dapat POST/PUT/DELETE admin
- user tidak dapat menaikkan role sendiri
- passwordHash dan credential sensitif tidak tampil

7. Production Error Handling
- tidak ada server-side exception untuk route utama
- tidak ada 500 pada smoke test
- error state UI tetap aman
- jangan menampilkan detail internal database/stack trace

8. Regression
Jalankan:
- npm run typecheck
- npm run build
- production server
- smoke test public routes
- smoke test customer routes
- smoke test admin routes
- authorization test guest/customer/admin
- git diff --check
- git status --short

Jika menemukan masalah keamanan/fungsional kecil:
- perbaiki secara minimal
- jangan redesign
- jangan ubah schema/database kecuali benar-benar wajib
- jangan mengubah payment provider

GIT:
Jika ADA perubahan:
- git diff --check
- git status --short
- commit:
  security: harden production access and validation
- git push origin main
- jangan force push
- verifikasi git status dan origin/main

Jika TIDAK ADA perubahan:
- jangan membuat empty commit.

Karena VPS berisiko mati:
setelah typecheck + build + production verification PASS,
langsung commit dan push jika memang ada perubahan.

JANGAN:
- jangan aktifkan payment production
- jangan membutuhkan Midtrans Server Key
- jangan melakukan transaksi uang nyata
- jangan reset database
- jangan membuat mock payment
- jangan redesign UI
- jangan force push

LAPORKAN:
- authentication
- authorization
- API security
- order/cart security
- admin security
- secrets/environment
- production error handling
- typecheck
- build
- production server
- commit/push
- blocker tersisa

```
# Prompt — Phase 33: Admin Panel Functional Audit
```

## Phase 33 — Admin Panel Functional Audit

Project: /root/toko-online

Lanjutkan roadmap dari kondisi terakhir. Jangan mengulang fitur yang sudah PASS.

STATUS:
- Customer UI: PASS
- Catalog/product/category: PASS
- Cart: PASS
- Checkout/order: PASS
- JWT authentication: PASS
- Authorization/authz: PASS
- Admin Panel: sudah diimplementasikan
- Prisma/backend: PASS
- Payment integration: PASS
- Midtrans production: BLOCKED hanya karena Server Key belum tersedia
- Typecheck: PASS
- Build: PASS
- Production server: PASS
- Git: sinkron dengan origin/main

TUJUAN:
Audit dan pastikan Admin Panel benar-benar usable menggunakan data backend nyata.

JANGAN:
- jangan membuat payment production aktif
- jangan membutuhkan Midtrans credential
- jangan mengubah schema/database
- jangan reset database
- jangan redesign customer UI
- jangan menghapus fitur existing
- jangan membuat mock data sebagai pengganti database
- jangan force push

AUDIT ADMIN PANEL:

1. Admin Dashboard
- dashboard hanya dapat diakses admin
- customer/guest tidak boleh masuk
- KPI tidak error ketika database kosong
- angka 0 ditampilkan dengan benar ketika belum ada data
- order/status breakdown tidak menghasilkan NaN/null/error
- dashboard menggunakan data backend nyata

2. Product Management
- list produk tampil dari database
- create product
- edit product
- delete product
- validasi input
- harga tersimpan benar
- stok tersimpan benar
- status aktif/nonaktif bekerja
- produk yang dihapus tidak dapat diakses dari customer
- error state dan empty state benar

3. Category Management
- list kategori dari database
- create/edit/delete jika fitur memang sudah tersedia
- slug/name tervalidasi
- kategori kosong tidak menyebabkan error
- customer catalog tetap normal setelah perubahan

4. Order Management
- admin dapat melihat order
- order customer tampil dengan benar
- order item dan total benar
- status order ditampilkan benar
- jangan menambahkan transaksi payment nyata
- jangan mengubah payment provider

5. User Management
- admin dapat melihat user jika route/fitur memang sudah dibuat
- passwordHash/credential tidak pernah ditampilkan
- customer tidak dapat mengubah role dirinya sendiri
- authorization tetap aman

6. Admin Security
Verifikasi:
- guest -> admin = 401/redirect login
- customer -> admin = 403 atau redirect yang sesuai
- admin -> admin = 200
- API admin harus tetap protected
- tidak ada endpoint admin yang bocor ke customer

7. UI Admin
Jangan redesign besar.
Hanya perbaiki bug fungsional:
- tombol tidak bekerja
- form tidak submit
- link salah
- loading state
- error state
- empty state
- mobile overflow
- route/navigation rusak
- data tidak refresh setelah mutation

8. Regression
Jalankan:
- npm run typecheck
- npm run build
- npm run start / production server
- smoke test route customer
- smoke test route admin
- authorization test guest/customer/admin
- git diff --check
- git status --short

Jika ada bug, perbaiki sampai PASS.

GIT:
Jika ada perubahan kode:
- commit dengan message:
  feat(admin): complete admin panel functional audit
- git push origin main
- jangan force push

Jika tidak ada perubahan:
- jangan membuat empty commit.

Karena VPS berisiko mati:
- setelah implementasi selesai
- typecheck PASS
- build PASS
- production server PASS
- langsung commit dan push
- jangan berhenti hanya karena dokumentasi/laporan

LAPORKAN:
- Admin Dashboard
- Product Management
- Category Management
- Order Management
- User Management
- Admin Security
- UI/functionality
- typecheck
- build
- production server
- commit hash
- push status
- blocker yang tersisa

Catatan:
Satu-satunya blocker payment production tetap Midtrans Server Key.
Jangan mencoba mengatasi blocker tersebut pada phase ini.

```
# Prompt berikutnya — langsung kerjakan & push jika ada perubahan
```

## Phase 32 — Final Customer Purchase Flow Audit

Project: /root/toko-online

Lanjutkan roadmap. Jangan mengulang pekerjaan yang sudah PASS.

STATUS SAAT INI:
- Prisma/backend: PASS
- Customer catalog: PASS
- Cart: PASS
- JWT authentication: PASS
- Orders: PASS
- Admin Panel: PASS
- Payment integration layer: PASS
- Midtrans integration: production-ready, tetapi credential production belum tersedia
- Typecheck: PASS
- Build: PASS
- Production server: PASS
- Git clean dan sinkron dengan origin/main

TUJUAN:
Audit final seluruh alur customer sebelum masuk tahap production payment activation.

Alur yang harus diverifikasi:

Homepage
→ Kategori
→ Produk
→ Detail Produk
→ Tambah ke Keranjang
→ Cart
→ Checkout
→ Login jika diperlukan
→ Create Order
→ Payment
→ Order Status

PERIKSA:

1. Produk
- harga berasal dari database/server
- stok benar
- produk nonaktif tidak dapat dibeli
- produk tidak ditemukan menampilkan state yang benar

2. Cart
- tambah produk
- ubah quantity jika memang didukung
- hapus produk
- total dihitung benar
- cart tidak boleh memanipulasi harga server

3. Checkout
- user wajib login
- order menggunakan data server-side
- stok divalidasi ulang saat create order
- total order dihitung ulang di server
- tidak bisa membeli melebihi stok
- tidak bisa memanipulasi productId/harga/total melalui request

4. Order
- order hanya milik user terkait
- user lain tidak dapat melihat order
- order berhasil dibuat
- order gagal tidak meninggalkan data yang rusak
- status order/payment konsisten

5. Payment
- jangan melakukan transaksi uang nyata
- jangan membutuhkan credential production untuk test
- jangan mengubah integration layer yang sudah PASS
- pastikan checkout siap menerima payment reference/status dari provider

6. UI
Jangan redesign.
Hanya perbaiki jika ada:
- tombol yang tidak bekerja
- link salah
- loading/error state rusak
- halaman blank
- data tidak tampil
- mobile overflow
- route salah

7. Regression test
Jalankan:
- npm run typecheck
- npm run build
- production server
- smoke test route customer utama

Pastikan tidak ada:
- server-side exception
- 500
- broken navigation
- authentication regression
- authorization regression

8. Git
Jika ADA perubahan kode:
- git diff --check
- git status --short
- commit dengan message yang sesuai
- git push origin main
- jangan force push

Jika TIDAK ADA perubahan:
- jangan membuat empty commit.

Setelah selesai laporkan:
- customer flow status
- cart status
- checkout status
- order status
- payment handoff status
- UI issues jika ada
- typecheck
- build
- production server
- commit/push status
- blocker yang masih tersisa

Jangan membuat admin panel baru.
Jangan mengubah schema/database.
Jangan mengubah payment provider.
Jangan memasukkan credential production.

```
# Prompt: Phase 31 — Production Payment Integration
```

## Phase 31 — Production Payment Integration

Project: /root/toko-online

STATUS:
- Customer flow: PASS
- JWT authentication: PASS
- Admin Panel: PASS
- Product/category/order/user management: PASS
- Typecheck: PASS
- Build: PASS
- Git clean & synced
- Jangan redesign UI customer/admin.

TUJUAN:
Lanjutkan roadmap ke tahap Production Payment dengan aman.

ATURAN PENTING:
- Audit kode payment yang SUDAH ADA terlebih dahulu.
- Jangan mengganti payment provider jika sudah ada provider yang dipilih di project.
- Jangan mengarang API/credential/provider.
- Jangan meminta atau menulis secret langsung ke source code.
- Jangan commit .env atau credential.
- Jangan melakukan transaksi uang nyata hanya untuk testing.
- Jangan reset database.
- Jangan membuat migration/schema baru kecuali benar-benar diperlukan.
- Jangan force push.
- Jika credential production belum tersedia, implementasikan integration layer dan validasi sampai batas yang aman, lalu laporkan blocker credential.

==================================================
1. AUDIT PAYMENT EXISTING
==================================================

Periksa seluruh:
- payment-service
- payment API/routes
- payment models
- order/payment relation
- webhook handler
- payment configuration
- checkout flow
- payment status handling

Gunakan kode dan schema yang sudah ada sebagai sumber utama.

Pastikan alurnya jelas:

Customer
→ Checkout
→ Create Order
→ Create Payment
→ Payment Provider
→ Payment Status
→ Webhook
→ Verify Payment
→ Update Order

==================================================
2. PAYMENT STATE MACHINE
==================================================

Audit status payment/order agar konsisten.

Minimal bedakan:
- pending
- paid/success
- failed
- expired/cancelled

Jangan sampai:
- order dibayar dua kali
- webhook menggandakan update
- payment sukses tetapi order tetap pending
- payment gagal dianggap paid

Implementasikan idempotency pada webhook jika belum ada.

==================================================
3. CHECKOUT
==================================================

Pastikan checkout:
- hanya membutuhkan user authenticated
- membaca produk dari database
- menggunakan harga server-side
- tidak mempercayai harga dari client
- memvalidasi stok
- membuat order dengan benar
- membuat payment berdasarkan total server-side

Jangan menggunakan harga yang dikirim frontend sebagai sumber kebenaran.

==================================================
4. WEBHOOK
==================================================

Audit webhook payment.

Pastikan:
- signature/webhook verification dilakukan server-side
- payload divalidasi
- event diproses idempotent
- payment tidak bisa dipalsukan melalui request client
- order hanya berubah menjadi paid setelah status payment valid
- webhook tidak membocorkan secret

Jika provider membutuhkan endpoint tertentu, ikuti dokumentasi provider yang SUDAH digunakan project.

==================================================
5. PAYMENT PROVIDER CONFIG
==================================================

Cari konfigurasi provider yang sudah ada.

Jika credential production tersedia melalui environment:
- gunakan environment variable
- jangan tampilkan nilainya
- jangan commit secret

Jika credential production BELUM tersedia:
- jangan membuat credential palsu
- jangan melakukan transaksi nyata
- jangan memaksa production activation

Tetap lakukan seluruh verifikasi code yang bisa dilakukan tanpa credential.

==================================================
6. ADMIN PAYMENT
==================================================

Hubungkan payment dengan Admin Panel yang sudah dibuat.

Admin minimal dapat melihat:
- order ID
- user
- total
- payment status
- order status
- payment provider
- waktu pembayaran
- reference/transaction ID jika tersedia

Jangan membuat tombol "force paid" yang dapat melewati verifikasi provider kecuali memang sudah menjadi bagian roadmap.

==================================================
7. SECURITY
==================================================

Pastikan:
- customer tidak dapat mengubah status payment
- customer tidak dapat mengubah total order
- customer tidak dapat mengubah order milik user lain
- webhook hanya menerima event valid
- secret hanya server-side
- admin endpoint tetap requireAdmin
- tidak ada credential di client bundle

==================================================
8. TEST TANPA UANG NYATA
==================================================

Lakukan test:
- checkout valid
- harga dimanipulasi dari client
- stok tidak cukup
- order user A tidak bisa diakses user B
- payment pending
- webhook invalid
- webhook duplicate
- payment failed
- payment success menggunakan mekanisme test/sandbox jika provider memang menyediakannya

JANGAN melakukan pembayaran uang nyata.

==================================================
9. VERIFIKASI
==================================================

Jalankan:

npm run typecheck
npm run build

Jalankan production server dan smoke test:

/
 /products
 /categories
 /search
 /checkout
 /orders
 /admin
 /admin/dashboard
 /admin/orders

Pastikan tidak ada server-side exception.

==================================================
10. GIT & PUSH
==================================================

Sebelum commit:

git status --short
git diff --check

Pastikan tidak ada:
- .env
- credential
- secret
- build artifact
- temporary file

Jika ada perubahan kode dan seluruh verifikasi PASS:

git add <file yang benar-benar berubah>
git commit -m "feat(payment): integrate production payment flow"
git push origin main

Jangan force push.

Setelah push:

git status --short
git status -sb
git log -1 --oneline

Jika tidak ada perubahan kode, jangan membuat empty commit.

==================================================
HASIL AKHIR
==================================================

Laporkan:
- payment provider yang ditemukan
- file/service payment yang diaudit
- checkout status
- webhook status
- idempotency status
- security test
- sandbox/test payment status
- typecheck
- build
- production server
- commit hash jika ada
- push status
- blocker credential jika ada

Jangan mengaktifkan transaksi production nyata jika credential/configuration belum siap.

```
# Prompt: Phase 30 — Admin Panel Final Audit
```


## Phase 30 — Final Audit & Stabilization Admin Panel

Project: Digital Cell / toko-online
Direktori: /root/toko-online

Lanjutkan dari repository SAAT INI.

STATUS:
- Phase 27 Full JWT Authentication: PASS
- Phase 28 Customer Flow: PASS
- Phase 29 Admin Panel: PASS
- Commit terakhir: c9facf3
- Admin dashboard/products/categories/orders/users sudah tersedia.

TUJUAN:
Finalisasi dan audit Admin Panel sebelum masuk tahap production payment.

PENTING:
- Jangan mengulang JWT.
- Jangan redesign customer UI.
- Jangan membuat production payment.
- Jangan reset database.
- Jangan membuat migration/schema baru kecuali benar-benar diperlukan.
- Jangan menggunakan mock data jika database nyata tersedia.
- Jangan force push.
- Jika tidak ada perubahan yang diperlukan, jangan membuat commit kosong.

==================================================
1. AUDIT SELURUH ADMIN PANEL
==================================================

Periksa:

/admin
/admin/dashboard
/admin/products
/admin/categories
/admin/orders
/admin/users

Pastikan semua route:
- hanya dapat diakses admin
- guest diarahkan/ditolak
- customer mendapat 403 atau redirect yang sesuai
- tidak ada route admin yang hanya dilindungi frontend

Audit middleware, layout admin, repository/service, dan API.

==================================================
2. DASHBOARD
==================================================

Pastikan dashboard menggunakan data database nyata.

Periksa:
- total users
- total products
- total categories
- total orders
- recent orders
- order status breakdown

Pastikan:
- database kosong → angka 0 / empty state
- tidak ada NaN
- tidak ada undefined
- tidak ada error server-side
- loading/error state ditangani

Jangan membuat transaksi dummy hanya untuk mengisi dashboard.

==================================================
3. PRODUCT MANAGEMENT
==================================================

Audit `/admin/products`.

Pastikan:
- list produk
- tambah produk
- edit produk
- hapus produk
- harga
- stok
- kategori
- status aktif/nonaktif

CRUD harus benar-benar menggunakan backend/database.

Validasi:
- nama tidak kosong
- harga valid
- stok tidak negatif
- kategori valid jika wajib
- ID produk divalidasi server-side

Setelah delete, pastikan produk benar-benar tidak dapat ditemukan lagi.

Pastikan customer tidak bisa memakai endpoint CRUD admin.

==================================================
4. CATEGORY MANAGEMENT
==================================================

Audit `/admin/categories`.

Pastikan:
- list
- create
- edit
- delete jika aman
- slug/name validation
- relasi produk tidak rusak

Jika kategori masih digunakan produk, jangan melakukan delete destruktif yang menyebabkan data rusak.

==================================================
5. ORDER MANAGEMENT
==================================================

Audit `/admin/orders`.

Pastikan admin dapat:
- melihat daftar order
- melihat customer
- melihat total
- melihat status
- melihat tanggal
- melihat item order/detail

Pastikan order berasal dari database.

Jangan mengaktifkan payment production.

Jika status order belum perlu diedit pada roadmap, jangan menambahkan logic status baru hanya untuk memenuhi UI.

==================================================
6. USER MANAGEMENT
==================================================

Audit `/admin/users`.

Minimal:
- daftar user
- email/identifier yang aman
- role
- tanggal dibuat jika tersedia

JANGAN menampilkan:
- password
- passwordHash
- secret
- token
- credential

Pastikan customer tidak dapat mengubah role dirinya sendiri menjadi admin.

Jika fitur edit/delete user belum diperlukan roadmap, jangan menambah CRUD yang tidak diperlukan.

==================================================
7. ADMIN UI / RESPONSIVE
==================================================

Audit tampilan tanpa redesign besar.

Desktop:
- sidebar
- topbar
- content
- table
- form
- dashboard cards

Mobile sekitar 390px:
- sidebar/drawer tidak merusak layout
- table dapat discroll atau responsive
- form tidak keluar layar
- tombol dapat ditekan
- navigation tetap berfungsi

Perbaiki hanya bug UI yang ditemukan.

==================================================
8. ERROR & LOADING STATE
==================================================

Pastikan setiap halaman admin penting memiliki:
- loading state
- empty state
- error state
- success feedback setelah operasi CRUD

Jangan membiarkan exception server muncul sebagai halaman error Next.js.

==================================================
9. SECURITY AUDIT
==================================================

Verifikasi:

guest:
- GET /admin → ditolak
- admin API → ditolak

customer:
- GET /admin → ditolak
- POST/PUT/DELETE admin API → 403

admin:
- admin pages → 200
- admin API → berhasil

Pastikan:
- role diperiksa server-side
- JWT/session diverifikasi
- tidak ada secret di client bundle
- passwordHash tidak dikirim ke frontend
- tidak ada endpoint privilege escalation

==================================================
10. REGRESSION TEST CUSTOMER
==================================================

Jangan merusak fitur yang sudah PASS.

Smoke test:
- /
- /categories
- /products
- /search
- /profile
- /orders
- /checkout

Pastikan:
- public route tetap 200
- protected customer route tetap protected
- customer order isolation tetap berjalan

==================================================
11. VERIFIKASI
==================================================

Jalankan:

npm run typecheck
npm run build

Kemudian jalankan production server sesuai environment project.

Lakukan smoke test HTTP untuk:
- public routes
- customer routes
- admin routes
- admin API

Jika ada browser automation yang tersedia dan aman digunakan, boleh gunakan untuk memeriksa UI.
Jika tidak tersedia, gunakan HTTP/render verification dan source inspection.

Jangan memasang tool berat hanya untuk test jika VPS berisiko kehabisan resource.

==================================================
12. GIT
==================================================

Sebelum commit:

git status --short
git diff --check

Pastikan tidak ada:
- .env
- API key
- password
- DATABASE_URL
- credential
- build artifact
- temporary file

Jika memang ada perubahan valid:

git add <hanya file yang berubah>
git commit -m "fix(admin): stabilize admin panel"
git push origin main

JANGAN force push.

Setelah push:

git status --short
git status -sb
git log -1 --oneline

Pastikan main sinkron dengan origin/main.

Jika tidak ada perubahan kode:
- jangan membuat empty commit
- cukup laporkan bahwa audit PASS dan working tree clean.

==================================================
13. HASIL AKHIR
==================================================

Laporkan:
- masalah yang ditemukan
- perbaikan yang dilakukan
- admin routes yang diverifikasi
- security test
- CRUD test
- customer regression test
- typecheck
- build
- production server
- commit hash jika ada perubahan
- push status
- working tree status

Jika semua PASS, langsung commit dan push.
Jika tidak ada perubahan, jangan commit.

BATASAN:
- Admin Panel: audit + stabilization
- Production payment: TIDAK
- Payment gateway live: TIDAK
- Cloudflare/DNS: TIDAK
- Database reset: TIDAK
- Customer redesign: TIDAK
- Force push: TIDAK
```
# Prompt: Phase 29 — Admin Panel & Dashboard
```

## Phase 29 — Admin Panel & Dashboard

Project: Digital Cell / toko-online
Direktori: /root/toko-online

Lanjutkan dari kondisi repository SAAT INI.

PENTING:
- Phase 27 Full JWT Authentication sudah PASS.
- Phase 28 Customer Account & Order Flow sudah PASS.
- Jangan mengulang pekerjaan JWT/customer flow yang sudah benar.
- Sekarang fokus khusus ADMIN PANEL.
- Jangan mengaktifkan production payment.
- Jangan membuat migration/schema baru kecuali benar-benar diperlukan.
- Jangan reset database.
- Jangan redesign halaman customer.
- Jangan force push.
- Karena VPS bisa mati, setelah implementasi dan seluruh verifikasi PASS langsung commit + push.

==================================================
1. AUDIT ADMIN YANG SUDAH ADA
==================================================

Audit terlebih dahulu:
- role/authentication admin yang sudah tersedia
- route `/admin`
- middleware/protection admin
- API yang berhubungan dengan admin
- service product/category/order/user yang sudah tersedia
- komponen UI existing yang bisa digunakan kembali

Jangan membuat duplikasi service/API jika sudah tersedia.

Jika admin route sudah ada tetapi belum lengkap, lanjutkan dari implementasi existing.

==================================================
2. ADMIN ROUTING & SECURITY
==================================================

Buat/rapikan struktur Admin Panel secara modular.

Minimal:
- `/admin`
- `/admin/dashboard`
- `/admin/products`
- `/admin/categories`
- `/admin/orders`

Semua halaman admin wajib:
- membutuhkan authentication
- membutuhkan role `admin`
- customer biasa tidak boleh masuk
- unauthorized user diarahkan/ditolak dengan benar
- jangan hanya mengandalkan pengecekan UI; protection harus server-side

Jangan memberikan akses admin hanya karena URL diketahui.

==================================================
3. ADMIN UI DESIGN
==================================================

Buat desain Admin Panel yang konsisten dengan branding Digital Cell, tetapi berbeda jelas dari customer UI.

Desain:
- clean
- modern
- profesional
- responsive
- desktop-first tetapi tetap nyaman di mobile
- warna utama tetap mengikuti Digital Cell
- sidebar desktop
- mobile navigation/drawer
- topbar
- breadcrumb/page title bila diperlukan
- card statistik
- table/list yang rapi
- loading state
- empty state
- error state

Jangan melakukan redesign pada halaman customer.

Gunakan komponen reusable agar halaman admin berikutnya mudah ditambahkan.

==================================================
4. ADMIN DASHBOARD
==================================================

Dashboard minimal menampilkan data nyata dari backend/database jika datanya tersedia:

- total products
- total categories
- total orders
- total users
- recent orders
- ringkasan order berdasarkan status jika service/schema sudah mendukung

Jangan menggunakan angka dummy jika data backend tersedia.

Jika database kosong:
- tampilkan 0
- gunakan empty state yang jelas
- jangan membuat fake transaction.

Dashboard harus aman jika sebagian data belum tersedia.

==================================================
5. PRODUCT MANAGEMENT
==================================================

Buat halaman `/admin/products`.

Minimal:
- daftar produk
- nama produk
- kategori
- harga
- stok
- status aktif/nonaktif
- aksi edit/delete jika backend memang sudah mendukung

Tambahkan:
- tombol tambah produk
- form tambah/edit yang rapi
- validasi input
- error handling
- loading state

Gunakan API/service/database existing.

Jangan membuat CRUD palsu yang hanya mengubah state frontend.

Jika endpoint CRUD tertentu belum ada, implementasikan server-side secara modular dan aman.

Pastikan customer tidak dapat menggunakan endpoint admin tersebut.

==================================================
6. CATEGORY MANAGEMENT
==================================================

Buat `/admin/categories`.

Minimal:
- daftar kategori
- nama
- slug jika digunakan
- status
- tambah
- edit
- hapus jika aman sesuai relasi database

Jangan menghapus kategori secara destruktif jika masih memiliki produk tanpa menangani relasinya dengan benar.

Gunakan database nyata.

==================================================
7. ORDER MANAGEMENT
==================================================

Buat `/admin/orders`.

Minimal:
- daftar order
- order ID
- customer
- total
- status
- tanggal
- detail order

Admin dapat melihat order customer.

Jangan mengubah flow payment production.

Jika perubahan status order belum diperlukan oleh roadmap saat ini, cukup buat read-only terlebih dahulu daripada membuat logic yang belum aman.

==================================================
8. USER / ROLE
==================================================

Jangan membuat halaman user management penuh dulu jika belum diperlukan roadmap.

Tetapi pastikan:
- role admin tersimpan/terbaca dengan benar
- customer tidak bisa elevate role sendiri
- API admin memvalidasi role server-side

Jangan membuat endpoint yang memungkinkan customer mengubah role menjadi admin.

==================================================
9. API & SECURITY
==================================================

Audit seluruh endpoint admin.

Pastikan:
- authentication diverifikasi server-side
- role admin diverifikasi server-side
- input divalidasi
- jangan expose password/hash/secret
- jangan expose DATABASE_URL
- jangan expose credential payment
- jangan menerima `userId` dari client sebagai sumber utama ownership jika bisa diambil dari session
- jangan percaya harga/total dari client untuk operasi sensitif

Jangan merusak endpoint customer yang sudah PASS.

==================================================
10. RESPONSIVE UI
==================================================

Verifikasi minimal:
- desktop
- mobile sekitar 390px width

Pastikan:
- sidebar tidak merusak layout mobile
- table tidak menyebabkan halaman rusak
- form mudah digunakan
- tombol tidak keluar layar
- navigation admin tetap dapat digunakan

Jangan menggunakan browser automation jika environment VPS tidak mendukungnya.
Gunakan HTTP/render verification dan pemeriksaan source bila perlu.

==================================================
11. DATABASE
==================================================

Gunakan Prisma/database existing.

ATURAN KERAS:
- jangan reset database
- jangan drop database
- jangan membuat migration hanya untuk kebutuhan UI
- jangan mengganti database
- jangan membuat mock database
- jangan seed transaksi palsu

Jika database belum memiliki data tertentu, tampilkan empty state.

==================================================
12. VERIFIKASI
==================================================

Jalankan:

npm run typecheck
npm run build

Kemudian jalankan production server sesuai arsitektur project.

Smoke test minimal:

PUBLIC:
- /
- /categories
- /products
- /search

CUSTOMER:
- /profile
- /orders
- /checkout

ADMIN:
- /admin
- /admin/dashboard
- /admin/products
- /admin/categories
- /admin/orders

Security test:
- guest → admin harus ditolak/login
- customer → admin harus ditolak
- customer → admin API harus ditolak
- admin → admin dashboard harus berhasil

Pastikan tidak ada server-side exception.

==================================================
13. GIT
==================================================

Sebelum commit:

git status --short
git diff --check

Pastikan tidak ada:
- `.env`
- secret
- API key
- password
- DATABASE_URL
- credential
- build artifact
- temporary file

Jika semua PASS:

git add <file yang memang berubah>
git commit -m "feat(admin): add admin dashboard and management panel"
git push origin main

JANGAN force push.

Setelah push:

git status --short
git log -1 --oneline
git status -sb

Pastikan branch sinkron dengan `origin/main`.

==================================================
14. HASIL AKHIR
==================================================

Laporkan:
- struktur Admin Panel yang dibuat
- halaman yang dibuat
- API/service yang dibuat atau digunakan kembali
- security/role protection
- hasil typecheck
- hasil build
- hasil smoke test
- responsive verification
- commit hash
- status push
- status working tree

Jika semua PASS, langsung commit dan push.

Jika ada blocker nyata:
- jangan commit kode rusak
- jangan force push
- jelaskan blocker secara jelas.

BATASAN PHASE 29:
- Admin Panel: YA
- Admin Dashboard: YA
- Product management: YA
- Category management: YA
- Order management: YA
- Production payment: TIDAK
- Payment gateway live: TIDAK
- Cloudflare/DNS: TIDAK
- Database reset: TIDAK
- Customer redesign: TIDAK

```
# Prompt: Phase 28 — Customer Account & Order Flow Finalization
```

## Phase 28 — Finalisasi Customer Account & Order Flow

Project: Digital Cell / toko-online
Direktori: /root/toko-online

Lanjutkan roadmap dari kondisi repository SAAT INI.

PENTING:
- Full JWT Authentication Phase 27 SUDAH SELESAI. Jangan mengulang atau merombak JWT yang sudah PASS.
- Jangan membuat halaman Admin sekarang.
- Jangan mengaktifkan production payment.
- Jangan mengubah schema/migration/database tanpa kebutuhan yang benar-benar terbukti.
- Jangan redesign UI.
- Jangan menggunakan mock order/payment sebagai pengganti database.
- Jangan force push.
- Jika tidak ada perubahan yang memang diperlukan, jangan membuat commit kosong.

TUJUAN PHASE 28:
Pastikan seluruh pengalaman customer setelah login benar-benar konsisten dari:
login/register → session → profile → catalog → product → cart → checkout → order → logout.

1. AUDIT KONDISI SAAT INI
Periksa implementasi yang SUDAH ADA terlebih dahulu:
- login/register
- JWT/session
- logout
- profile/account
- product/catalog
- category
- search
- product detail
- favorites
- cart
- checkout
- orders/order history
- protected routes
- user ownership/isolation

Jangan mengubah fitur yang sudah benar.

2. CUSTOMER SESSION
Pastikan:
- user yang login tetap dikenali saat berpindah halaman
- authenticated request menggunakan session/JWT yang benar
- logout benar-benar menghapus/mematikan session client
- setelah logout, halaman protected tidak dapat diakses
- user tidak dapat melihat order milik user lain
- user yang belum login tetap diarahkan ke login pada halaman protected

3. PROFILE / ACCOUNT
Audit `/profile`:
- data user ditampilkan dari backend/database
- tidak menggunakan data dummy
- state loading/error/empty ditangani dengan baik
- logout berfungsi
- jangan menambahkan fitur yang belum ada di roadmap hanya untuk memperbanyak fitur

4. ORDER HISTORY
Audit `/orders`:
- hanya menampilkan order milik user yang sedang login
- data berasal dari Prisma/backend
- status order ditampilkan dengan benar
- empty state tetap bagus jika belum ada order
- detail order tidak bocor ke user lain
- akses tanpa login harus tetap protected

5. CART → CHECKOUT → ORDER
Pastikan flow:
product → tambah ke keranjang → cart → checkout → create order

berjalan menggunakan data backend yang benar.

Periksa:
- product ID valid
- quantity valid
- stock divalidasi server-side
- total harga tidak boleh dipercaya dari client
- order hanya dibuat untuk authenticated user
- cart dibersihkan hanya setelah order berhasil
- kegagalan create order tidak boleh menghapus cart
- jangan membuat mock order

6. UI NAVIGATION
Pastikan navigation customer yang sudah ada tetap bekerja:
- Beranda
- Kategori
- Pesanan
- Favorit
- Akun

Periksa mobile bottom navigation dan desktop navigation.
Jangan redesign. Hanya perbaiki link/action yang benar-benar rusak.

7. SECURITY CHECK
Pastikan:
- API protected tetap membutuhkan JWT/session
- customer tidak dapat mengakses endpoint admin
- customer tidak dapat membuat/mengubah data produk
- order ownership diperiksa server-side
- jangan expose secret, password, DATABASE_URL, atau credential ke client
- jangan commit `.env`

8. VERIFIKASI
Jalankan sesuai environment project:

npm run typecheck
npm run build

Kemudian jalankan production server jika aman untuk environment VPS.

Lakukan smoke test HTTP minimal:
- homepage
- categories
- products
- product detail
- search
- cart
- profile
- orders
- checkout
- login/register protection

Untuk protected endpoint, pastikan hasil tanpa authentication tetap sesuai implementasi:
401 atau redirect login.

9. DATABASE
Jangan reset database.
Jangan membuat migration baru kecuali audit benar-benar membuktikan schema memang kurang dan perubahan tersebut memang bagian Phase 28.
Jangan menghapus data existing.

10. GIT
Sebelum commit:

git status --short
git diff --check

Pastikan:
- tidak ada `.env`
- tidak ada secret
- tidak ada credential
- tidak ada build artifact
- tidak ada temporary file

Jika ada perubahan valid:

git add <hanya file yang diperlukan>
git commit -m "feat(customer): finalize account and order flow"
git push origin main

JANGAN force push.

Setelah push:

git status --short
git log -1 --oneline
git status -sb

Pastikan branch sudah sinkron dengan origin/main.

11. HASIL AKHIR
Laporkan:
- file yang berubah
- fitur yang diperbaiki
- hasil typecheck
- hasil build
- hasil smoke test
- hasil git commit
- hasil push
- apakah working tree clean

Jika semua sudah benar, langsung commit dan push.
Jangan berhenti hanya setelah coding.

BATASAN:
- Tidak membuat Admin Panel.
- Tidak mengaktifkan production payment.
- Tidak mengubah Cloudflare/DNS/port.
- Tidak reset database.
- Tidak membuat migration tanpa kebutuhan.
- Tidak redesign UI.
- Jangan mengulang pekerjaan Phase 27 JWT Authentication yang sudah PASS.

```
# Prompt — Full JWT Authentication + Commit & Push
```

PROMPT: Full JWT Authentication — Phase 27

Project: Digital Cell / toko-online

Lanjutkan roadmap dari kondisi repo TERKINI. Jangan mengulang pekerjaan payment sandbox, catalog, cart, order, atau UI navigation yang sudah PASS.

Tugas utama:
1. Audit implementasi authentication yang sudah ada.
2. Implementasikan Full JWT Authentication sesuai arsitektur project dan Prisma schema yang sudah tersedia.
3. Gunakan user/database asli melalui Prisma, bukan mock user atau in-memory auth.
4. Pastikan:
   - register/login berjalan benar
   - password di-hash dengan aman
   - JWT/session tervalidasi di server
   - user identity tersedia untuk server/API yang membutuhkan authentication
   - protected route benar-benar membutuhkan user yang valid
   - user A tidak dapat melihat/mengakses order milik user B
   - logout/invalidation mengikuti arsitektur auth yang digunakan project
5. Integrasikan auth dengan flow existing:
   - /profile
   - /orders
   - /checkout
   - API yang menggunakan requireUser()
6. Jangan merusak route public seperti:
   - /
   - /categories
   - /products
   - /category/*
   - /search
   - /cart
7. Jangan mengubah desain UI kecuali memang diperlukan agar login/register/auth state berfungsi.
8. Jangan membuat mock database.
9. Jangan membuat migration baru jika schema yang ada sudah mencukupi.
10. Jangan reset database dan jangan menghapus migration.
11. Jangan mengaktifkan production payment atau menggunakan credential production.
12. Jangan mengubah Cloudflare/DNS/port.
13. Jangan memasukkan .env, secret, password, DATABASE_URL, credential, atau build artifact ke Git.

Verifikasi:
- npm run typecheck
- npm run build
- jalankan production server
- verifikasi route public tetap HTTP 200
- verifikasi protected route tanpa session → redirect/401 sesuai arsitektur
- verifikasi login menghasilkan session/JWT valid
- verifikasi user authenticated dapat mengakses profile/orders/checkout
- verifikasi isolasi user/order
- git diff --check
- git status --short

Jika semua PASS:
- langsung commit
- langsung push ke origin main
- JANGAN force push
- verifikasi:
  git status --short
  git log -1 --oneline
  git status -sb

Gunakan commit message:
feat(auth): implement full jwt authentication

Karena VPS berisiko mati, jangan berhenti setelah implementasi jika seluruh verifikasi sudah PASS. Langsung commit dan push.

Jika ada blocker nyata, jangan commit kode rusak. Laporkan blocker dan kondisi repo terakhir.

```
# Prompt: Payment Flow
```

Project: /root/toko-online

Lanjut dari kondisi repository terbaru. Cart → Checkout → Order sudah PASS.
Jangan mengulang atau merombak bagian yang sudah PASS.

Fokus:
Payment Session → Checkout Payment → Redirect → Webhook → Order Status.

Tugas:
1. Audit payment-service dan seluruh payment flow existing.
2. Pastikan order yang sudah dibuat dapat memiliki payment session yang valid.
3. Implementasikan payment flow menggunakan mekanisme sandbox/mock yang sudah tersedia.
4. Payment method yang didukung harus mengikuti backend/UI existing.
5. Pastikan payment session terhubung ke order yang benar.
6. Implementasikan redirect/payment callback sesuai arsitektur existing.
7. Implementasikan webhook verification secara aman:
   - validasi signature/token sesuai mekanisme existing
   - idempotent
   - webhook yang sama tidak boleh memproses order dua kali
   - webhook invalid harus ditolak
8. Pastikan status order berubah secara konsisten:
   pending → paid/failed/expired sesuai hasil payment.
9. Jangan mengurangi stok dua kali akibat webhook duplikat.
10. Pastikan payment failure/expiry tidak merusak order.
11. Pastikan payment status dapat ditampilkan di halaman Pesanan.
12. Tambahkan test untuk:
   - payment success
   - payment failure
   - expired payment
   - duplicate webhook
   - invalid webhook
   - webhook untuk order yang tidak ditemukan
13. Jangan menggunakan credential/payment provider production.
14. Jangan melakukan transaksi uang nyata.
15. Jangan mengaktifkan production payment.
16. Jangan mengubah schema/migration kecuali benar-benar diperlukan.
17. Jangan reset database.
18. Jangan redesign UI.

Verifikasi:
- npm run typecheck
- npm run build
- production server
- test payment session
- test success/failure/expired
- test duplicate webhook
- test invalid webhook
- verifikasi order status dan stok
- git diff --check
- git status --short

Jika semua PASS:
- git add perubahan
- git commit -m "feat(payment): complete sandbox payment flow"
- git push origin main
- git status --short
- git log -1 --oneline
- pastikan main sinkron dengan origin/main

Jangan force push.

Jika tidak ada perubahan yang diperlukan, jangan membuat empty commit.
Jika ada blocker, jangan commit perubahan rusak; laporkan blocker dan penyebabnya.
Karena VPS berisiko mati, setelah seluruh verifikasi PASS langsung commit dan push.

```
# Prompt: Cart → Checkout → Order Flow
```

Project: /root/toko-online

Lanjut dari commit terbaru. Seed catalog dan UI audit sudah PASS.

Fokus tahap ini:
Cart → Checkout → Create Order → Order Status.

Tugas:
1. Audit cart menggunakan data produk dari database.
2. Pastikan tambah produk ke cart bekerja.
3. Pastikan quantity, subtotal, total, remove item, dan empty cart bekerja.
4. Pastikan produk stok 0 tidak bisa dibeli.
5. Pastikan checkout membaca item cart yang benar.
6. Validasi checkout sebelum order dibuat.
7. Pastikan order dibuat melalui backend/Prisma, bukan hanya state frontend.
8. Pastikan OrderItem tersimpan dengan data yang benar.
9. Pastikan harga/order total menggunakan nilai yang aman dan konsisten dari database.
10. Pastikan race condition stok tidak menyebabkan stok negatif atau double order.
11. Setelah order berhasil, cart dikosongkan dengan benar.
12. Pastikan halaman Pesanan dapat membaca order yang dibuat.
13. Login-gate tetap dipertahankan jika checkout/order memang membutuhkan user.
14. Jangan menggunakan mock order sebagai pengganti database.
15. Jangan mengaktifkan payment production.
16. Jangan mengubah schema/migration kecuali benar-benar diperlukan.
17. Jangan reset database.
18. Jangan redesign UI.

Verifikasi:
- typecheck
- build
- production server
- test cart dengan produk stok tersedia
- test produk stok 0
- test checkout validation
- test create order menggunakan database
- cek order tersimpan
- cek halaman Pesanan
- git diff --check
- git status --short

Jika semua PASS:
- langsung commit
- git push origin main
- jangan force push
- verifikasi main sinkron dengan origin/main

Commit message:
feat(order): complete cart checkout and order flow

Jika ada blocker, jangan membuat commit rusak. Laporkan blocker dan penyebabnya.

```
# Prompt: Seed Data Test & Verify UI
```

Project: /root/toko-online

Lanjut dari commit terbaru. UI dan backend integration sudah PASS.

Tujuan:
Menyediakan data TEST yang aman di database VPS agar seluruh UI Digital Cell bisa diuji dengan data nyata.

Tugas:

1. Audit apakah project sudah memiliki seed script Prisma.
2. Jika belum ada, buat seed script modular dan idempotent untuk development/testing.
3. Jangan mengubah schema Prisma dan jangan membuat migration baru.
4. Gunakan data dummy/test saja, bukan credential atau data production.
5. Buat minimal:
   - beberapa kategori produk
   - beberapa produk aktif
   - produk dengan stok
   - produk habis stok
   - produk dari kategori berbeda
6. Pastikan seed bisa dijalankan ulang tanpa membuat duplicate data.
7. Jangan menghapus data existing secara massal.
8. Jangan melakukan prisma migrate reset.
9. Jangan memasukkan API key, password, payment credential, atau secret asli.
10. Setelah seed tersedia, jalankan seed pada DATABASE_URL VPS yang sedang digunakan untuk testing.
11. Verifikasi database melalui Prisma/query yang sesuai.
12. Pastikan:
   - /categories menampilkan kategori
   - /products menampilkan produk
   - klik kategori menampilkan produk kategori tersebut
   - search menemukan produk
   - product detail dapat dibuka
   - produk stok habis menampilkan status yang benar
   - cart dapat menambahkan produk
13. Jangan redesign UI.
14. Jangan mengubah payment production.
15. Jangan mengaktifkan transaksi/payment production.

Verifikasi akhir:
- npm run typecheck
- npm run build
- production server tetap berjalan
- cek HTTP route utama
- git diff --check
- git status --short
- pastikan tidak ada .env/secret/credential/build artifact

Jika semua PASS:
- git add perubahan kode yang relevan
- git commit -m "feat(seed): add test catalog data"
- git push origin main
- git status --short
- git log -1 --oneline
- pastikan main sinkron dengan origin/main

Jangan force push.
Jangan reset database.
Jangan membuat migration.
Jika seed sudah tersedia dan cukup, gunakan yang existing dan jangan membuat duplikat.
Jika ada blocker, berhenti dan laporkan blocker tanpa commit perubahan rusak.

```
# 
```

Prompt: Audit Semua Interaksi UI

Project: /root/toko-online

Lanjut dari commit terbaru. Jangan mengulang pekerjaan yang sudah PASS.

Tujuan:
Audit semua tombol, navigasi, search, filter, dark mode, hamburger menu, bottom navigation, dan checkout agar benar-benar berfungsi.

Periksa:
1. Bottom navigation:
   - Beranda
   - Kategori
   - Pesanan
   - Favorit
   - Akun

2. Header:
   - Hamburger menu
   - Dark mode
   - Search
   - Filter

3. Search:
   - input search
   - submit/search action
   - query URL
   - empty result state

4. Kategori:
   - daftar kategori
   - klik kategori
   - empty state ketika database kosong

5. Produk:
   - klik produk
   - detail produk
   - tombol beli
   - tambah ke cart
   - quantity/cart interaction

6. Checkout:
   - validasi form
   - payment method
   - submit/order flow
   - login-gated route jika memang diperlukan

7. Pesanan/Favorit/Akun:
   - pastikan redirect/login-gate benar
   - jangan membuat mock user/data

8. Responsive:
   - pastikan tidak ada tombol yang keluar layar
   - mobile layout tetap benar

Peraturan:
- Jangan redesign UI.
- Jangan membuat mock data.
- Jangan mengubah schema/migration.
- Jangan reset database.
- Jangan memasukkan credential production.
- Perbaiki hanya bug interaksi yang benar-benar ditemukan.
- Jika database kosong, anggap empty state sebagai kondisi valid, bukan bug.

Verifikasi:
- npm run typecheck
- npm run build
- jalankan production server
- cek route utama HTTP
- git diff --check
- git status --short

Jika ada perubahan kode dan semua verifikasi PASS:
- git add perubahan
- git commit -m "fix(ui): audit interactive navigation and controls"
- git push origin main
- verifikasi git status dan git log -1 --oneline

Jangan force push.
Karena VPS berisiko mati, setelah semua PASS langsung commit dan push.
Laporkan hanya bug yang ditemukan, file yang diubah, hasil verifikasi, commit hash, dan status push.

```
# Prompt berikutnya — Fix UI & Runtime Data
```

Prompt: Fix UI Runtime & Database Data

Project: /root/toko-online

Lanjutkan dari kondisi repository saat ini. Jangan mengulang pekerjaan yang sudah PASS.

Tujuan:
Pastikan UI benar-benar mengambil data dari backend/database dan tidak hanya berhasil render secara visual.

Tugas:
1. Audit halaman utama, kategori, produk, pesanan, favorit, akun, dan checkout.
2. Periksa kenapa halaman kategori saat ini menampilkan "Belum Ada Kategori".
3. Pastikan service/category/product yang dipakai frontend benar-benar terhubung ke Prisma/database.
4. Jangan mengganti schema Prisma dan jangan membuat migration baru kecuali benar-benar diperlukan.
5. Jika database kosong, jangan membuat mock data sebagai solusi. Pastikan UI menangani database kosong dengan benar.
6. Periksa semua API/server action yang dipanggil halaman tersebut dan pastikan tidak menyebabkan server-side exception.
7. Pastikan halaman tetap bisa dirender ketika database kosong.
8. Perbaiki hanya bug yang benar-benar ditemukan. Jangan redesign UI.
9. Jalankan:
   - npm run typecheck
   - npm run build
   - npm run start / production server verification sesuai konfigurasi project
   - cek HTTP homepage dan route utama
10. Jangan menjalankan test dengan flag Node yang tidak didukung VPS ini.
11. Jangan melakukan database reset, migration reset, transaksi production, atau memasukkan credential production.
12. Setelah verifikasi PASS:
   - git status --short
   - git diff --check
   - pastikan tidak ada .env, secret, credential, build artifact, atau temporary file
   - git add perubahan yang relevan
   - git commit -m "fix(ui): connect runtime data to backend"
   - git push origin main
   - git status --short
   - git log -1 --oneline
   - pastikan branch main sinkron dengan origin/main

PENTING:
- Jangan berhenti hanya karena database kosong.
- Jangan mengganti database.
- Jangan membuat mock data sebagai pengganti database.
- Jangan force push.
- Karena VPS berisiko mati, setelah perubahan sudah benar dan typecheck/build/production verification PASS, langsung commit dan push.
- Jika tidak ada perubahan kode yang diperlukan, jangan membuat commit kosong. Laporkan bahwa repository tetap bersih.
- Berikan laporan singkat: bug yang ditemukan, file yang diubah, hasil build, hasil HTTP, commit hash, dan status push.

```
# Audit & Perbaiki Integrasi Backend Prisma
```

## Audit & Perbaiki Integrasi Backend Prisma

Project: **Digital Cell / toko-online**

Lakukan audit menyeluruh terhadap project yang sedang aktif. Jangan membuat ulang project dan jangan menghapus fitur yang sudah ada.

Temuan awal:

* Next.js 14.2.33
* TypeScript
* Prisma 5.22
* Database migration sudah ada di `app/prisma/migrations/`
* `npm run typecheck` berhasil
* `npm run build` berhasil
* `npm run start` gagal karena `next.config` menggunakan `output: "export"`
* Pencarian `PrismaClient` di `src` tidak menemukan penggunaan Prisma.
* Project memiliki service seperti `auth-service`, `banner-service`, `category-service`, `order-service`, `payment-service`, `product-service`, `user-service`, dll.

Tugas:

1. Audit seluruh struktur project terlebih dahulu.
2. Tentukan apakah project memang membutuhkan Next.js server runtime untuk Prisma/database.
3. Jika Prisma belum terintegrasi, implementasikan integrasi Prisma dengan benar.
4. Jika `output: "export"` tidak sesuai dengan arsitektur aplikasi, perbaiki `next.config` agar aplikasi dapat berjalan menggunakan `npm run start`.
5. Buat/revisi Prisma client singleton yang aman untuk development dan production.
6. Hubungkan service yang memang membutuhkan database ke Prisma tanpa merusak UI/store yang sudah ada.
7. Periksa authentication, user, product, category, banner, order, order item, payment, notification dan data settings agar siap menggunakan database sesuai schema Prisma yang sudah ada.
8. Jangan membuat API route hanya untuk formalitas. Buat endpoint/server logic yang memang diperlukan oleh frontend.
9. Jangan menyimpan `DATABASE_URL`, secret, atau `.env.local` ke Git.
10. Jangan mengubah desain UI kecuali diperlukan untuk memperbaiki integrasi.
11. Jangan menghapus migration yang sudah dibuat.
12. Pastikan semua perubahan modular dan mudah dikembangkan.
13. Jalankan:

* `npm run typecheck`
* `npm run build`
* `npm run start` atau validasi production server dengan cara yang sesuai arsitektur.

14. Jika ditemukan error, perbaiki sampai ketiga tahap tersebut valid.
15. Setelah selesai, tampilkan ringkasan file yang diubah dan alasan setiap perubahan.
16. Setelah seluruh verifikasi PASS, lakukan commit dan push ke `origin main`.
17. Sebelum commit, jalankan `git status --short` dan `git diff --check`.
18. Pastikan tidak ada `.env`, secret, credential, build artifact, atau file temporary yang ikut di-commit.
19. Gunakan commit message yang jelas, misalnya `feat(backend): integrate Prisma database services`.
20. Jalankan `git push origin main` tanpa force push.
21. Setelah push, verifikasi `git status --short`, `git log -1 --oneline`, dan sinkronisasi dengan `origin/main`.

PENTING:

* Jangan hanya menghilangkan `output: "export"` tanpa memastikan seluruh aplikasi tetap berfungsi.
* Jangan mengganti database.
* Jangan menggunakan mock data sebagai pengganti database production.
* Jangan menghapus fitur existing.
* Gunakan schema Prisma yang sudah tersedia sebagai sumber data utama.
* Jika ada bagian yang ternyata memang sengaja static, pertahankan bagian tersebut dan integrasikan backend hanya pada fitur yang membutuhkan database.
* Karena VPS berisiko mati, jangan berhenti setelah implementasi. Jika typecheck, build, dan production server verification sudah PASS, langsung commit dan push ke GitHub.
* Jangan force push.
* Jika ada error yang belum terselesaikan, jangan commit perubahan yang rusak; perbaiki terlebih dahulu jika aman, atau berhenti dan laporkan blocker.


```
# Prompt: Commit & Push UI Navigation Fix
```
Prompt: Commit & Push UI Navigation Fix


Project: /root/toko-online

Perubahan UI/navigation sudah diverifikasi dan PASS.

Sebelum commit:
- git status --short
- git diff --check
- pastikan hanya perubahan yang memang terkait perbaikan BottomNav/navigation.
- jangan masukkan .env, secret, credential, build artifact, atau file temporary.

Jalankan:
npm run typecheck
npm run build

Jika semuanya PASS:
1. git add hanya file perubahan terkait.
2. git commit -m "fix(ui): repair bottom navigation routing"
3. git push origin main

Setelah push:
- git status --short
- git log -1 --oneline
- pastikan branch main sudah sinkron dengan origin/main.

Jangan membuat perubahan fitur lain.
Jangan migration/reset database.
Jangan mengubah Cloudflare/DNS/port.
Jangan mengaktifkan payment production.

Laporkan:
- commit hash
- push status
- working tree status
- build/typecheck status.

```

# 
```

Prompt: Final Manual UI Verification

Project: /root/toko-online

Server testing sudah berjalan di port 3000.

Sekarang jangan ubah kode dan jangan commit/push.

Lakukan verifikasi final terhadap UI yang sudah diperbaiki.

Cek route berikut:
- /
- /categories
- /orders
- /favorites
- /profile

Pastikan BottomNav:
- Beranda bisa diklik dan pindah ke /
- Kategori bisa diklik dan pindah ke /categories
- Pesanan bisa diklik dan pindah ke /orders
- Favorit bisa diklik dan pindah ke /favorites
- Akun bisa diklik dan pindah ke /profile
- active state sesuai halaman
- tidak ada 404 atau server-side exception

Cek juga:
- tombol hamburger
- dark mode
- search
- tombol filter
- responsive mobile

Jangan membuat fitur baru.
Jangan membuat data dummy.
Jangan mengubah database/schema/migration.
Jangan commit/push.

Jika semuanya normal, cukup laporkan PASS.
Jika ada bug nyata, laporkan route dan error sebenarnya lalu berhenti.

```
# 
```
Prompt: Full UI Menu & User Flow Audit — toko-online

Project: /root/toko-online

KONDISI:
- Aplikasi sudah berhasil berjalan di VPS pada port 3000.
- Homepage/category page sudah bisa dirender melalui browser.
- Halaman Kategori menampilkan empty state "Belum Ada Kategori".
- Jangan menganggap empty state sebagai error.
- Sekarang fokus audit seluruh UI dan navigasi yang sudah ada.

TUJUAN:
Audit semua menu, tombol, navigasi, search, filter, dan user flow dari UI yang sudah ada.

JANGAN:
- membuat fitur baru
- mengubah desain besar-besaran
- membuat database dummy hanya untuk menghilangkan empty state
- mengubah schema/migration
- mengubah Cloudflare/DNS/port
- mengaktifkan payment production
- melakukan transaksi nyata
- commit
- push

1. AUDIT BOTTOM NAV

Verifikasi satu per satu:

- Beranda
- Kategori
- Pesanan
- Favorit
- Akun

Untuk setiap menu:
- klik/buka
- pastikan route benar
- pastikan tidak 404
- pastikan tidak server error
- pastikan loading/error/empty state benar
- pastikan active navigation state benar
- pastikan tombol back/navigation tidak rusak

2. HEADER

Verifikasi:

- Logo/brand
- tombol dark mode
- tombol hamburger/menu
- search
- filter

Pastikan setiap tombol benar-benar memiliki action.

Jika tombol hanya UI placeholder, laporkan.

3. SEARCH

Uji:

- buka search
- masukkan keyword
- submit
- hasil pencarian
- empty result
- clear search
- kembali ke catalog

Jika backend belum memiliki data, jangan membuat data palsu.

4. FILTER

Audit tombol filter.

Periksa:
- filter kategori
- provider
- harga atau filter lain yang memang sudah tersedia
- apply
- reset
- close

Pastikan tidak ada tombol mati.

5. BERANDA

Audit seluruh homepage:

- banner/promo
- kategori
- produk
- produk promo
- product card
- tombol lihat semua
- tombol beli
- search
- navigasi

Setiap link harus menuju halaman/flow yang benar.

6. KATEGORI

Audit:
- daftar kategori
- empty state
- klik kategori
- halaman produk berdasarkan kategori

Jika database testing memang belum memiliki kategori:
- jangan membuat data palsu
- cukup pastikan empty state memang benar
- laporkan bahwa data belum tersedia.

7. PRODUCT FLOW

Jika ada produk:

product list
→ product card
→ product detail
→ pilih/beli
→ checkout

Pastikan tidak ada broken route.

Jika tidak ada produk karena database kosong:
laporkan dan jangan memalsukan data.

8. FAVORIT

Audit:
- buka Favorit
- empty state
- tombol favorite pada product
- add/remove favorite jika fitur memang sudah tersedia
- persistence jika backend sudah mendukung

Jangan membuat fitur baru.

9. PESANAN

Audit:
- halaman Pesanan
- empty state
- detail order jika tersedia
- status order
- tombol/detail yang tersedia

Jangan membuat transaksi nyata.

10. AKUN

Audit:
- halaman akun
- login/register jika tersedia
- profile
- logout
- menu akun yang tersedia

Jangan menggunakan credential production.

11. CHECKOUT

Audit hanya sampai batas aman:

- buka checkout
- product/order summary
- quantity
- harga
- payment method UI
- submit/order flow

JANGAN melakukan pembayaran nyata.

Pastikan payment production tetap OFF.

12. RESPONSIVE

Verifikasi minimal:

mobile sekitar 390x844
desktop sekitar 1366x768

Periksa:
- tidak overflow
- bottom navigation
- header
- card
- text
- button
- modal/drawer
- search
- checkout

13. ERROR AUDIT

Untuk setiap masalah:
- route
- tombol
- error message
- server log
- root cause
- apakah source code atau database/environment

Jangan menebak.

14. FIX

Jika menemukan BUG nyata pada source code:
- perbaiki bug tersebut
- jangan redesign
- jangan mengubah database schema
- jangan membuat migration
- jangan mengubah fitur yang tidak terkait.

Setelah fix jalankan:

npm run typecheck
npm run build

Jika server perlu restart, restart server testing di port 3000.

15. FINAL VERIFICATION

Jalankan:

git status --short
git diff --check

Jangan commit/push.

FINAL REPORT:

A. NAVIGATION
- Beranda:
- Kategori:
- Pesanan:
- Favorit:
- Akun:

B. HEADER
- Search:
- Filter:
- Dark mode:
- Hamburger:

C. FLOW
- Product:
- Product detail:
- Checkout:
- Login:
- Account:

D. RESPONSIVE
- Mobile:
- Desktop:

E. DATA
- Category data:
- Product data:

F. BUG
- ditemukan:
- diperbaiki:

G. VERIFICATION
- typecheck:
- build:
- HTTP:
- working tree:

Bedakan dengan jelas:
PASS = berfungsi
EMPTY = UI benar tetapi data belum tersedia
BLOCKED = membutuhkan database/auth/environment
FAIL = ada bug yang harus diperbaiki

Jangan commit atau push. Berhenti setelah laporan.


```
# Prompt: Siapkan Database Testing & Online UI
```

Prompt: Setup PostgreSQL Testing & Online UI Verification — toko-online

Project:
  /root/toko-online

KONDISI:
- Build PASS.
- Typecheck PASS.
- Next.js production server sudah bisa berjalan di port 3000.
- Saat dibuka melalui HTTP, homepage mengalami server-side exception.
- Root cause sudah dikonfirmasi: DATABASE_URL belum menunjuk ke PostgreSQL yang reachable dan memiliki schema aplikasi.
- Source code tidak perlu diperbaiki untuk masalah ini.

TUJUAN:
Siapkan PostgreSQL khusus TESTING di VPS ini, deploy migration Prisma yang SUDAH ADA, lalu jalankan aplikasi sampai homepage bisa dibuka normal melalui:

http://104.207.89.204:3000

==================================================
ATURAN KEAMANAN
==================================================

VPS ini adalah VPS coding/testing.

BOLEH:
- menggunakan PostgreSQL lokal VPS jika tersedia
- membuat database testing
- membuat user database testing
- mengisi DATABASE_URL untuk environment runtime testing
- menjalankan `npx prisma migrate deploy`
- menjalankan seed hanya jika project memang sudah memiliki seed resmi dan aman

DILARANG:
- `prisma migrate reset`
- `prisma db push`
- menghapus database
- menghapus migration
- membuat migration baru
- menggunakan database production
- menggunakan production payment credential
- transaksi pembayaran nyata
- mengubah Cloudflare/DNS
- mengubah port permanen
- commit
- push

Jangan menampilkan password/database credential dalam output.

==================================================
1. AUDIT POSTGRESQL
==================================================

Masuk:

cd /root/toko-online

Periksa apakah PostgreSQL tersedia:

systemctl status postgresql --no-pager

atau metode yang sesuai jika PostgreSQL menggunakan container/service lain.

Periksa database/user yang tersedia TANPA menampilkan password.

Jika PostgreSQL lokal tersedia dan aman digunakan untuk testing, gunakan PostgreSQL lokal.

Jika PostgreSQL tidak tersedia, jangan install secara agresif tanpa terlebih dahulu melaporkan kondisi tersebut.

==================================================
2. DATABASE TESTING
==================================================

Jika PostgreSQL lokal tersedia:

- buat database khusus testing untuk toko-online jika belum ada
- buat user khusus testing jika diperlukan
- jangan gunakan database production
- jangan menghapus database existing.

Nama database boleh menggunakan nama yang jelas seperti:

toko_online_test

Gunakan credential testing yang aman.

Jangan tampilkan password di final report.

==================================================
3. DATABASE_URL
==================================================

Konfigurasikan DATABASE_URL hanya untuk environment testing/runtime VPS.

Jangan commit `.env`, `.env.local`, atau credential apa pun.

Validasi hanya dengan status:

DATABASE_URL: PRESENT
DATABASE_URL: REACHABLE

Jangan print nilai DATABASE_URL.

Pastikan konfigurasi yang dipakai oleh Next.js production server sama dengan environment testing yang sedang digunakan.

==================================================
4. PRISMA MIGRATION
==================================================

Periksa:

ls app/prisma/migrations

dan schema Prisma.

Jalankan:

npx prisma migrate deploy

PENTING:

- gunakan migration yang SUDAH ADA
- jangan membuat migration baru
- jangan reset database
- jangan db push.

Jika migration gagal, STOP dan tampilkan error sebenarnya.

==================================================
5. SEED DATA
==================================================

Periksa apakah project memang memiliki seed resmi.

Jika ada seed yang memang menjadi bagian dari project:

- jalankan seed sesuai script resmi
- gunakan hanya data testing
- jangan memasukkan credential/payment production.

Jika tidak ada seed:

jangan membuat seed baru hanya untuk memalsukan hasil.

Homepage harus tetap diuji sesuai arsitektur existing.

==================================================
6. START PRODUCTION SERVER
==================================================

Pastikan build masih valid:

npm run typecheck
npm run build

Kemudian jalankan production server menggunakan environment testing.

Gunakan port 3000.

Pastikan hanya satu server testing yang aktif agar tidak terjadi port conflict.

==================================================
7. HTTP VERIFICATION
==================================================

Tes:

curl -i http://127.0.0.1:3000/

Target:
HTTP 200.

Kemudian:

curl -i http://104.207.89.204:3000/

Target:
HTTP 200.

Jika masih 500:

- ambil server log
- cari stack trace asli
- jangan menebak
- jangan mengubah source code sebelum root cause diketahui.

==================================================
8. BROWSER / UI VERIFICATION
==================================================

Setelah HTTP 200, buka:

http://104.207.89.204:3000

Verifikasi secara manual:

1. Homepage
2. Header/navigation
3. Catalog/product listing
4. Product card
5. Product detail
6. Responsive mobile layout
7. Desktop layout
8. Console/server error
9. Broken image/resource
10. Loading state
11. Empty state jika catalog memang kosong.

JANGAN melakukan transaksi/payment nyata.

Untuk checkout, cukup pastikan halaman dan flow UI dapat dirender sampai batas yang aman tanpa melakukan pembayaran nyata.

==================================================
9. JIKA DATA CATALOG KOSONG
==================================================

Jangan langsung menganggap UI rusak.

Bedakan:

- database kosong
- query error
- schema error
- UI rendering error.

Jika database berhasil tetapi catalog kosong karena belum ada seed/data:

laporkan:

"UI berhasil render, tetapi catalog kosong karena database testing belum memiliki data produk."

Jangan membuat data produk palsu kecuali project sudah memiliki mekanisme seed resmi.

==================================================
10. FINAL CHECK
==================================================

Jalankan:

git status --short
git diff --check

Pastikan tidak ada:

- `.env`
- `.env.local`
- secret
- password
- build artifact baru yang ter-track
- migration baru.

JANGAN commit/push.

==================================================
FINAL REPORT
==================================================

Tampilkan:

DATABASE
- PostgreSQL: PASS/FAIL
- Database testing: PASS/FAIL
- DATABASE_URL: PRESENT/MISSING
- Database reachable: PASS/FAIL
- Prisma migrate deploy: PASS/FAIL
- Migration baru dibuat: NO

APPLICATION
- typecheck: PASS/FAIL
- build: PASS/FAIL
- server port 3000: PASS/FAIL
- localhost HTTP: PASS/FAIL
- public HTTP: PASS/FAIL

UI
- homepage: PASS/FAIL
- catalog: PASS/FAIL
- product listing: PASS/FAIL
- product detail: PASS/FAIL
- mobile layout: PASS/FAIL
- desktop layout: PASS/FAIL
- server/browser error: ada/tidak

GIT
- working tree:
- diff check:
- commit: NO
- push: NO

STATUS:
Pilih salah satu:

1. ONLINE — homepage dan UI dapat dibuka normal
2. BLOCKED — database/environment belum siap
3. FAILED — masih ada error aplikasi

Jika ONLINE, berhenti dan tunggu instruksi berikutnya.

```
# Prompt: Fix Server-Side Exception Sebelum UI Test
```

Prompt: Diagnose & Fix Online Server-Side Exception — toko-online

Project:
  /root/toko-online

KONDISI SAAT INI:
- Build sebelumnya PASS.
- Next.js production server berhasil start.
- Server berjalan di port 3000.
- Saat dibuka melalui:
  http://104.207.89.204:3000
  browser menampilkan:

  "Application error: a server-side exception has occurred
   (see the server logs for more information)."

- Digest:
  1945587591

TUJUAN:
Cari ROOT CAUSE server-side exception tersebut dan perbaiki sampai homepage dapat dibuka normal melalui HTTP.

==================================================
ATURAN
==================================================

VPS ini hanya untuk coding/testing.

JANGAN:
- commit
- push
- migration
- prisma migrate reset
- db push
- membuat migration baru
- menghapus database
- mengganti database
- mengaktifkan production payment
- menggunakan production credential
- transaksi nyata
- mengubah Cloudflare/DNS
- mengubah port permanen
- mengubah desain UI tanpa alasan
- mengganti mock dengan data palsu hanya untuk membuat halaman tampil.

Jangan menebak penyebab error.
Cari error sebenarnya dari server log/stack trace.

==================================================
1. REPRODUCE ERROR
==================================================

Masuk:

cd /root/toko-online

Pastikan server production sedang berjalan.

Tes:

curl -i http://127.0.0.1:3000/

Kemudian:

curl -i http://104.207.89.204:3000/

Catat HTTP status dan response.

==================================================
2. PERIKSA SERVER LOG
==================================================

Cari log proses Next.js yang sedang berjalan.

Jika menggunakan terminal/background process/system process, identifikasi PID terlebih dahulu.

Periksa stdout/stderr server.

Cari:
- Error
- Exception
- Prisma error
- DATABASE_URL error
- Environment variable error
- Cannot find module
- Server Component error
- React/Next.js server error
- authentication/session error
- serialization error
- database connection error.

Jangan hanya membaca digest.
Cari stack trace asli.

Jika perlu restart server dalam mode yang memungkinkan log terlihat jelas, lakukan restart lokal/testing tanpa mengubah konfigurasi permanen.

==================================================
3. PERIKSA ENV TANPA MEMBOCORKAN SECRET
==================================================

Periksa apakah environment variable yang diperlukan tersedia.

JANGAN print nilai asli.

Minimal validasi keberadaan:
- DATABASE_URL
- SESSION_SECRET
- environment variable lain yang benar-benar digunakan homepage/server.

Jika variable tidak ada, tampilkan hanya:

DATABASE_URL: MISSING/PRESENT
SESSION_SECRET: MISSING/PRESENT

Jangan tampilkan nilainya.

==================================================
4. PERIKSA DATABASE DEPENDENCY
==================================================

Cari apakah homepage/catalog/server component memanggil Prisma/database saat render.

Audit:
- prisma client
- repositories
- product service
- category service
- banner service
- settings service
- auth/session service.

Tentukan dengan bukti:
"Error terjadi karena database tidak tersedia"
atau
"Error terjadi karena bug kode lain."

Jangan menganggap database sebagai penyebab sebelum melihat stack trace.

==================================================
5. JIKA ERROR MEMANG DATABASE
==================================================

Jika stack trace membuktikan bahwa homepage gagal karena DATABASE_URL/database belum tersedia:

JANGAN:
- membuat migration
- reset database
- db push
- menghapus schema.

Lakukan hanya diagnosis.

Periksa:
- apakah PostgreSQL service tersedia
- apakah DATABASE_URL valid secara struktur tanpa menampilkan secret
- apakah Prisma schema sesuai migration yang sudah ada
- apakah database dapat diakses.

Jika database belum siap, jangan membuat perubahan destructive.

Kemudian jelaskan secara jelas:

"Homepage membutuhkan database aktif sehingga UI belum dapat dirender pada VPS coding ini."

Tetapi sebelum menyimpulkan, periksa apakah arsitektur existing memang memungkinkan homepage tetap render ketika database kosong.

==================================================
6. JIKA ERROR ADALAH BUG KODE
==================================================

Jika stack trace menunjukkan bug kode:

- identifikasi file
- identifikasi fungsi
- jelaskan root cause
- lakukan perbaikan minimal
- jangan refactor besar
- jangan mengubah schema
- jangan menghapus fitur.

Setelah fix:

npm run typecheck
npm run build

Jika keduanya PASS, restart production server.

==================================================
7. TEST ULANG
==================================================

Test:

curl -i http://127.0.0.1:3000/

Target:
HTTP 200 untuk homepage.

Kemudian:

curl -i http://104.207.89.204:3000/

Target:
HTTP 200.

Pastikan response bukan Next.js error page.

==================================================
8. BROWSER TEST
==================================================

Setelah HTTP 200:

Buka:

http://104.207.89.204:3000

Periksa homepage.

Target:
- tidak blank
- tidak Application Error
- tidak Next.js error page
- tidak server-side exception.

Jika homepage berhasil tampil, lanjutkan hanya ke:

- catalog
- product listing
- product detail

Jangan melakukan checkout/payment dulu.

==================================================
9. GIT SAFETY
==================================================

Setelah perbaikan:

git status --short
git diff --check

Jangan commit.
Jangan push.

Jika ada perubahan source code:
laporkan file yang berubah dan alasan perubahan.

Jika tidak ada perubahan:
laporkan bahwa masalah berasal dari environment/runtime.

==================================================
FINAL REPORT
==================================================

Tampilkan:

1. ROOT CAUSE
   Jelaskan penyebab server-side exception berdasarkan log.

2. FIX
   Jelaskan apa yang diperbaiki.

3. DATABASE
   - tersedia/tidak
   - koneksi PASS/FAIL
   - migration tidak dijalankan.

4. BUILD
   - typecheck:
   - build:

5. SERVER
   - port:
   - localhost HTTP:
   - public IP HTTP:

6. BROWSER
   - homepage:
   - catalog:
   - product listing:
   - product detail:

7. GIT
   - working tree:
   - diff check:
   - commit:
   - push:

8. STATUS
   Pilih:
   - FIXED — homepage online dan siap lanjut UI test
   - BLOCKED — database/environment belum siap
   - FAILED — masih ada server-side error

BERHENTI setelah laporan.
Jangan commit/push.
Jangan migration/reset database.

```
# Prompt: Full Online + UI Test Toko-Online
```

Prompt: Full Online Runtime & UI Verification — toko-online

Project:
  /root/toko-online

TUJUAN:
Lakukan pengujian aplikasi secara nyata sampai aplikasi berjalan online melalui konfigurasi VPS yang SUDAH ADA, kemudian lakukan pemeriksaan UI/frontend secara menyeluruh.

VPS ini adalah VPS CODING/TESTING.
Jangan dianggap sebagai production.

==================================================
ATURAN MUTLAK
==================================================

JANGAN:
- commit
- push GitHub
- migration baru
- prisma migrate reset
- database reset/drop
- mengubah schema Prisma
- mengubah Cloudflare/DNS
- mengubah konfigurasi tunnel yang sudah ada
- mengubah port permanen
- memasukkan production credential
- melakukan transaksi nyata
- charge nyata
- refund nyata
- mengaktifkan production payment
- menghapus fitur existing
- mengubah desain UI hanya demi test.

Jika menemukan masalah, perbaiki hanya jika memang diperlukan agar aplikasi dapat berjalan/test.
Jangan melakukan perubahan arsitektur besar.

==================================================
1. AUDIT KONDISI AWAL
==================================================

Masuk:

cd /root/toko-online

Periksa:

git status --short
git branch -vv
git log -3 --oneline

Pastikan working tree awal bersih.

Periksa package.json dan konfigurasi Next.js untuk mengetahui:
- command development
- command production
- port
- konfigurasi output
- konfigurasi environment.

Jangan mengubah konfigurasi sebelum memahami kondisi existing.

==================================================
2. VALIDASI ENVIRONMENT
==================================================

Pastikan environment yang diperlukan tersedia.

Jangan tampilkan nilai secret.

Validasi keberadaan:
- DATABASE_URL
- SESSION_SECRET
- konfigurasi aplikasi
- konfigurasi payment sandbox/mock jika diperlukan.

Jika DATABASE_URL belum tersedia:
- jangan membuat database baru secara otomatis
- jangan migration
- catat sebagai limitation.

==================================================
3. DATABASE / PRISMA
==================================================

Lakukan hanya pemeriksaan aman:

- prisma validate
- prisma generate jika diperlukan
- koneksi/read-only database check jika database tersedia.

Jangan:
- migrate reset
- db push
- membuat migration
- mengubah schema.

Pastikan aplikasi tidak diam-diam menggunakan mock catalog jika database service memang sudah diintegrasikan.

==================================================
4. TYPECHECK DAN BUILD
==================================================

Jalankan:

npm run typecheck
npm run build

Jika gagal:
- identifikasi error sebenarnya
- perbaiki minimal
- ulangi test.

Jangan mengubah UI hanya untuk menghilangkan error.

==================================================
5. START APLIKASI SEBENARNYA
==================================================

Jalankan production runtime menggunakan command project yang benar.

Contoh jika sesuai package.json:

npm run start

Gunakan port existing project, misalnya 3000.

Jangan mengganti port permanen.

Pastikan:
- server berhasil boot
- tidak crash
- tidak ada fatal runtime error
- tidak ada Prisma fatal error
- tidak ada error environment fatal.

==================================================
6. LOCAL HTTP TEST
==================================================

Setelah server hidup, test dari VPS:

curl -I http://127.0.0.1:3000/
curl -I http://127.0.0.1:3000/api/products

Jika endpoint tersedia, test juga:
- catalog
- product detail
- cart/checkout endpoint yang relevan
- invoice/order endpoint yang relevan.

Catat HTTP status.

Target:
- halaman utama: 200
- endpoint valid: 200/expected status
- tidak ada 500.

==================================================
7. ONLINE ACCESS
==================================================

Gunakan konfigurasi online/tunnel yang SUDAH ADA.

Pertama identifikasi:
- apakah Cloudflare Tunnel sudah aktif
- hostname/domain yang sudah dikonfigurasi
- service/port yang sedang diarahkan.

JANGAN membuat tunnel baru.
JANGAN mengubah DNS.
JANGAN mengganti hostname.
JANGAN mengubah konfigurasi Cloudflare jika tidak diperlukan.

Pastikan tunnel mengarah ke aplikasi yang sedang berjalan.

Setelah itu lakukan HTTP test melalui URL online yang memang sudah tersedia.

Contoh:

curl -I https://DOMAIN-YANG-SUDAH-ADA/

Jika URL tidak dapat diketahui dari konfigurasi existing, tampilkan URL/hostname yang ditemukan dan berhenti sebelum mengubah konfigurasi.

==================================================
8. UI / FRONTEND TEST
==================================================

Ini bagian WAJIB.

Jangan hanya menggunakan curl.

Gunakan browser automation yang tersedia di environment, misalnya Playwright jika sudah tersedia.

Jangan install dependency besar hanya untuk test kecuali memang sudah ada di project.

Buka URL online aplikasi.

Periksa minimal halaman:

1. Homepage
2. Catalog/product listing
3. Category/filter jika tersedia
4. Product detail
5. Cart
6. Checkout
7. Invoice/order detail jika dapat diakses tanpa transaksi nyata.

==================================================
9. PEMERIKSAAN VISUAL UI
==================================================

Periksa secara nyata:

- halaman dapat dirender
- tidak blank screen
- tidak hydration error
- tidak React error
- tidak Next.js error page
- navbar/header
- logo/branding
- product card
- gambar produk
- harga
- stock
- kategori
- tombol beli
- cart
- checkout
- form input
- responsive mobile
- responsive desktop.

Karena aplikasi akan digunakan dari HP, WAJIB test viewport mobile sekitar:

390 x 844

Kemudian test desktop sekitar:

1366 x 768

Pastikan:
- tidak horizontal overflow yang tidak semestinya
- tombol tidak keluar layar
- teks tidak bertabrakan
- product card tidak rusak
- checkout masih usable
- navbar tidak rusak.

==================================================
10. BROWSER CONSOLE
==================================================

Saat melakukan UI test, periksa:

- browser console error
- failed network request
- HTTP 404
- HTTP 500
- hydration error
- JavaScript runtime error
- image loading failure
- API request failure.

Bedakan warning biasa dengan error yang benar-benar merusak aplikasi.

==================================================
11. USER FLOW TANPA TRANSAKSI NYATA
==================================================

Simulasikan user flow:

Homepage
→ Catalog
→ pilih produk
→ Product Detail
→ Add to Cart
→ Cart
→ Checkout

Isi hanya data dummy/testing jika form membutuhkan input.

JANGAN:
- submit payment nyata
- membuat transaksi provider nyata
- mengirim webhook provider nyata
- melakukan charge.

Jika checkout membutuhkan payment submission, berhenti tepat sebelum tindakan yang membuat transaksi nyata.

==================================================
12. DATA BACKEND
==================================================

Pastikan UI benar-benar mengambil data dari backend/database jika memang sudah diintegrasikan.

Verifikasi:
- product list
- product detail
- stock
- price
- category.

Jangan mengganti data server dengan mock hanya untuk membuat UI terlihat bagus.

Jika database kosong, catat:
"UI berhasil dirender tetapi data database kosong."

Jangan membuat data production palsu.

==================================================
13. PERFORMANCE DASAR
==================================================

Periksa apakah:
- halaman utama cepat merespons
- API tidak 500
- tidak ada request yang looping
- tidak ada server crash
- tidak ada memory/error fatal.

Tidak perlu melakukan benchmark berat.

==================================================
14. SETELAH TEST
==================================================

Pastikan aplikasi tetap berjalan jika memang runtime test membutuhkan akses online.

Jangan mematikan server/tunnel jika itu diperlukan agar saya dapat membuka URL online untuk pemeriksaan manual.

Jika server harus dijalankan sebagai background process untuk mempertahankan akses online, gunakan metode yang SUDAH digunakan project/VPS.

Jangan mengubah deployment architecture.

==================================================
15. FINAL GIT CHECK
==================================================

Setelah semua test:

git status --short
git diff --check

Jika ada perubahan file akibat test/build:
- identifikasi
- jangan commit
- jangan push.

Working tree sebaiknya tetap bersih.

==================================================
FINAL REPORT
==================================================

Tampilkan laporan:

A. SERVER
- Node:
- npm:
- Next.js:
- Production server:
- Port:
- Status:

B. DATABASE
- Prisma:
- PostgreSQL:
- Connection:
- Schema unchanged:

C. ONLINE
- Existing domain/tunnel:
- Online HTTP status:
- URL yang dapat dibuka:

D. API
- GET /
- GET /api/products
- endpoint lain:
- status/error:

E. UI
- Homepage:
- Catalog:
- Category:
- Product detail:
- Cart:
- Checkout:
- Invoice:
- Mobile 390x844:
- Desktop 1366x768:

F. BROWSER
- Console errors:
- Failed requests:
- Hydration errors:
- Runtime errors:

G. USER FLOW
- Homepage → Catalog: PASS/FAIL
- Catalog → Product: PASS/FAIL
- Product → Cart: PASS/FAIL
- Cart → Checkout: PASS/FAIL
- Payment: SKIPPED (no real transaction)

H. SECURITY
- Production payment: OFF
- Production credential: NOT USED
- Real transaction: NONE
- Webhook production: NONE

I. GIT
- Working tree:
- git diff --check:
- Commit:
- Push:

J. KESIMPULAN

Pilih:

PASS
= online dan UI dapat digunakan, tidak ada blocker.

PASS WITH WARNINGS
= online/UI berjalan tetapi ada limitation yang tidak menghalangi testing.

FAIL
= ada masalah yang membuat aplikasi belum layak dipindahkan.

PENTING:
Jangan commit/push.
Jangan migration.
Jangan reset database.
Jangan transaksi nyata.
Jangan ubah Cloudflare/DNS/port.
Jangan berhenti hanya setelah build PASS.

Test harus benar-benar sampai:
SERVER → ONLINE → BROWSER → UI → USER FLOW.

```
# Prompt: Test Final Toko-Online di VPS Coding
```
Prompt: Final Runtime Test — toko-online

Project:
  /root/toko-online

TUJUAN:
Lakukan pengujian menyeluruh terhadap aplikasi toko-online pada VPS coding ini sebelum project dipindahkan ke VPS production/deployment.

PENTING:
VPS ini HANYA untuk coding/testing.
Jangan mengaktifkan production payment.
Jangan menggunakan credential production.
Jangan melakukan transaksi nyata.
Jangan mengubah Cloudflare, DNS, atau konfigurasi port.
Jangan reset/drop database.
Jangan membuat migration baru kecuali benar-benar diperlukan untuk memperbaiki error yang sudah ada — dan jika migration ternyata diperlukan, BERHENTI dan laporkan, jangan menjalankannya.
Jangan commit atau push GitHub.

==================================================
1. CEK GIT
==================================================

Jalankan:

git status --short
git branch -vv
git log -3 --oneline

Pastikan:
- working tree bersih
- branch main sinkron dengan origin/main
- tidak ada perubahan kode yang tidak diketahui.

==================================================
2. CEK ENVIRONMENT
==================================================

Periksa environment tanpa menampilkan nilai secret.

Validasi keberadaan variable yang dibutuhkan seperti:
- DATABASE_URL
- SESSION_SECRET
- konfigurasi aplikasi lainnya

JANGAN tampilkan nilai asli secret/API key/password/DATABASE_URL.

Pastikan tidak ada:
- production credential
- API key production
- payment provider production credential

==================================================
3. CEK DEPENDENCY
==================================================

Jalankan pemeriksaan dependency yang aman.

Pastikan:
- node_modules tersedia
- Prisma tersedia
- Next.js tersedia
- package-lock/npm lock sesuai project

Jangan melakukan upgrade dependency secara otomatis.

==================================================
4. PRISMA / DATABASE
==================================================

Validasi koneksi Prisma/PostgreSQL menggunakan database testing yang sudah dikonfigurasi.

Jalankan pemeriksaan Prisma yang tidak mengubah schema.

Gunakan:
- prisma validate
- prisma generate jika diperlukan
- pemeriksaan koneksi/read-only

JANGAN:
- prisma migrate reset
- db push
- menghapus database
- membuat migration
- mengubah schema

Jika database tidak tersedia, jangan membuat database baru secara otomatis.
Laporkan error dengan jelas.

==================================================
5. TYPECHECK
==================================================

Jalankan:

npm run typecheck

Hasil harus PASS.

Jika gagal:
- identifikasi file dan error
- perbaiki hanya jika error berasal dari kode project
- ulangi typecheck

==================================================
6. PRODUCTION BUILD
==================================================

Jalankan:

npm run build

Pastikan production build berhasil.

Jika gagal:
- identifikasi root cause
- perbaiki secara minimal
- ulangi build

Jangan mengubah arsitektur hanya untuk membuat build terlihat PASS.

==================================================
7. START APPLICATION
==================================================

Jalankan aplikasi production secara lokal sesuai konfigurasi project.

Gunakan port project yang SUDAH ADA.
Jangan mengubah port permanen.

Validasi bahwa:
- Next.js server berhasil start
- tidak crash
- tidak ada error Prisma saat startup
- tidak ada error environment fatal

==================================================
8. HTTP SMOKE TEST
==================================================

Setelah server aktif, lakukan smoke test endpoint yang memang sudah tersedia.

Minimal periksa:

GET /
GET /api/products

Jika endpoint health/metrics memang sudah ada, periksa juga endpoint tersebut.

Untuk setiap endpoint catat:
- HTTP status
- response berhasil/gagal
- error jika ada

Jangan membuat endpoint baru hanya untuk pengujian.

==================================================
9. DATABASE-BACKED FLOW
==================================================

Verifikasi secara read-only bahwa:

- catalog/product dapat membaca database
- category dapat membaca database jika endpoint tersedia
- product detail menggunakan data server/database
- service repository dapat terhubung ke Prisma
- tidak terjadi fallback diam-diam ke mock catalog legacy

Jangan membuat order/payment nyata.

==================================================
10. PAYMENT SAFETY CHECK
==================================================

Pastikan:

- payment production tetap OFF
- provider payment tidak dipanggil
- tidak ada credential production
- tidak ada transaksi nyata
- webhook production tidak dikirim
- tidak ada charge/refund nyata

Jika ada payment flow yang bisa diuji secara mock/sandbox tanpa transaksi nyata, boleh lakukan hanya jika memang sudah tersedia.

==================================================
11. FRONTEND SMOKE TEST
==================================================

Validasi halaman utama dan flow penting yang tersedia:

- homepage
- catalog
- product detail
- cart
- checkout
- invoice/order detail jika tersedia

Pastikan tidak ada error runtime JavaScript yang jelas.

Jangan mengubah desain UI.

==================================================
12. API / SERVER ERROR CHECK
==================================================

Periksa log runtime selama smoke test.

Cari:
- Prisma error
- database connection error
- unhandled exception
- TypeScript/runtime error
- 404 yang tidak semestinya
- 500
- authentication error yang tidak semestinya

Bedakan:
- error yang memang expected karena fitur production belum aktif
- error nyata yang harus diperbaiki

==================================================
13. FINAL VERIFICATION
==================================================

Setelah pengujian selesai:

Jalankan:

git status --short
git diff --check

Jangan commit/push.

Jika ada perubahan file akibat test/build yang tidak seharusnya masuk Git, jangan commit.
Identifikasi file tersebut.

==================================================
HASIL AKHIR
==================================================

Buat laporan dengan format:

A. Environment
- Node:
- npm:
- Next.js:
- Prisma:
- PostgreSQL:
- Environment: PASS/FAIL

B. Git
- branch:
- working tree:
- origin/main:
- perubahan file:

C. Database
- Prisma validate:
- Database connection:
- Schema unchanged:

D. Build
- typecheck:
- build:
- production start:

E. HTTP Smoke Test
- GET /
- GET /api/products
- endpoint lain yang tersedia

F. Application Flow
- catalog:
- product detail:
- cart:
- checkout:
- invoice/order:

G. Payment Safety
- production payment:
- real transaction:
- production credentials:
- webhook production:

H. Final Status
Pilih salah satu:
- PASS — siap dipindahkan ke VPS deployment
- PASS WITH WARNINGS — bisa dipindahkan tetapi ada catatan
- FAIL — jangan pindahkan sebelum masalah diperbaiki

PENTING:
Jangan commit.
Jangan push.
Jangan migration.
Jangan reset database.
Jangan transaksi nyata.
Jangan production payment.
Jangan ubah Cloudflare/DNS/port.

Berhenti setelah laporan final.


```
# Prompt: Commit & Push Final README
```


Prompt: Commit & Push Final README

Project: /root/toko-online

KONDISI:
- Audit deployment documentation selesai.
- Hanya README.md yang berubah.
- Tidak ada perubahan source code.
- Tidak ada perubahan Prisma schema.
- Tidak ada migration baru.
- Tidak ada perubahan database.
- Tidak ada perubahan Cloudflare/DNS/port.
- Production payment tetap OFF.
- Tidak ada production credential.
- git diff --check sudah PASS.

TUGAS:

1. Jalankan:
   git status --short

2. Pastikan perubahan hanya:
   M README.md

3. Jalankan:
   git diff --check

4. Periksa diff README.md untuk memastikan:
   - tidak ada secret
   - tidak ada API key
   - tidak ada password
   - tidak ada DATABASE_URL asli
   - tidak ada credential production
   - tidak ada file temporary/build artifact.

5. Jika semua aman, commit README.md dengan message:

   docs: add VPS deployment and migration guide

6. Push ke branch main:

   git push origin main

7. Setelah push selesai, verifikasi:

   git status --short
   git log -1 --oneline
   git branch -vv

8. Pastikan:
   - working tree bersih
   - origin/main sudah berada pada commit terbaru
   - push berhasil
   - tidak menggunakan force push.

JANGAN:
- mengubah source code
- membuat migration
- reset/drop database
- mengaktifkan production payment
- mengubah Cloudflare/DNS/port
- melakukan transaksi nyata
- force push
- membuat commit tambahan jika tidak diperlukan.

FINAL REPORT:
Tampilkan:
- commit hash
- commit message
- hasil push
- status working tree
- status branch terhadap origin/main
- file yang masuk commit.

SELESAI setelah verifikasi.
```
# Prompt: Final VPS Migration Documentation
```

Prompt: Final VPS Migration Documentation

Project: /root/toko-online

KONDISI TERAKHIR:

Final audit sudah selesai.

Hasil:
- Working tree bersih.
- Branch main sinkron dengan origin/main.
- Tidak ada perubahan file setelah audit.
- Typecheck PASS.
- Build PASS.
- git diff --check PASS.
- Tidak ada secret/API key/credential production yang ter-track.
- Tidak ada migration baru.
- Tidak ada reset/drop database.
- Production payment tetap OFF.
- Tidak ada perubahan Cloudflare/DNS/port.
- VPS saat ini hanya digunakan untuk development/testing.

Project akan dipindahkan ke VPS baru setelah coding selesai.

TUJUAN:

Siapkan dokumentasi migrasi dan deployment VPS yang lengkap di README.md/root documentation yang sudah ada.

Jangan membuat README kedua jika README.md sudah tersedia.

Jangan mengubah kode aplikasi kecuali ditemukan dokumentasi command yang salah dan harus dikoreksi.

==================================================
1. AUDIT README EXISTING
==================================================

Baca README.md yang sekarang.

Jangan menghapus informasi existing yang masih valid.

Jika README hampir kosong, lengkapi secara terstruktur.

Gunakan satu README.md utama.

==================================================
2. PROJECT OVERVIEW
==================================================

Dokumentasikan:

- nama project
- stack
- Next.js
- TypeScript
- Prisma
- PostgreSQL
- frontend/backend architecture
- payment integration status
- development vs production environment.

Jangan memasukkan secret.

==================================================
3. REQUIREMENTS VPS BARU
==================================================

Dokumentasikan requirement:

- Ubuntu/Linux yang sesuai
- Node.js version yang benar berdasarkan package.json/lockfile
- npm/package manager
- Git
- PostgreSQL
- process manager jika memang diperlukan
- Cloudflare Tunnel/reverse proxy jika memang digunakan.

Jangan mengarang versi.

Ambil versi dari:

package.json
lockfile
engines
config project.

==================================================
4. CLONE PROJECT
==================================================

Dokumentasikan langkah:

git clone
cd toko-online

Gunakan repository URL yang benar berdasarkan git remote existing.

Jangan memasukkan credential/token ke README.

==================================================
5. INSTALL DEPENDENCIES
==================================================

Dokumentasikan command install yang benar berdasarkan lockfile.

Prioritaskan reproducible install.

Jika package-lock.json tersedia, gunakan npm ci.

Jika tidak tersedia, gunakan command yang sesuai.

==================================================
6. ENVIRONMENT
==================================================

Dokumentasikan environment variables yang dibutuhkan.

Gunakan placeholder saja.

Contoh:

DATABASE_URL="postgresql://USER:PASSWORD@HOST:5432/DATABASE"

Jangan pernah memasukkan nilai asli.

Pisahkan:

Development
Staging/Sandbox
Production

Jika variable tertentu hanya diperlukan production, tandai dengan jelas.

==================================================
7. DATABASE / PRISMA
==================================================

Dokumentasikan proses database VPS baru.

PENTING:

Jangan menggunakan:

prisma migrate reset

Jangan drop database.

Untuk deployment database existing gunakan migration deployment yang aman.

Dokumentasikan:

- install PostgreSQL
- create database/user jika memang diperlukan
- set DATABASE_URL
- prisma generate
- prisma migrate deploy
- verification Prisma.

Jelaskan bahwa migration existing harus digunakan sebagai sumber schema database.

Jangan membuat migration baru.

==================================================
8. BUILD
==================================================

Dokumentasikan:

npm run typecheck
npm run build

Jika package.json memiliki script khusus, gunakan script existing.

Jangan mengarang command.

==================================================
9. START PRODUCTION
==================================================

Audit package.json untuk memastikan command production yang benar.

Dokumentasikan command yang benar untuk menjalankan aplikasi production.

Pastikan dokumentasi sesuai dengan arsitektur Next.js saat ini.

Jangan mengubah next.config hanya demi dokumentasi.

Jika aplikasi menggunakan:

npm run start

dokumentasikan.

Jika menggunakan command lain, dokumentasikan command tersebut.

==================================================
10. PROCESS MANAGER
==================================================

Periksa apakah project membutuhkan:

systemd
PM2
Docker
atau process manager lain.

Jangan menambahkan dependency baru hanya untuk dokumentasi.

Jika systemd/PM2 memang diperlukan untuk production, dokumentasikan contoh konfigurasi tanpa secret.

==================================================
11. CLOUDFLARE / DOMAIN
==================================================

Dokumentasikan secara konseptual:

Domain
→ Cloudflare
→ Tunnel/reverse proxy
→ VPS
→ Next.js application

Jangan mengubah DNS.

Jangan mengubah Cloudflare Tunnel.

Jangan mengubah port pada VPS development.

Gunakan nilai placeholder jika port/domain belum seharusnya ditulis permanen.

==================================================
12. PAYMENT
==================================================

Dokumentasikan dengan sangat jelas:

PRODUCTION PAYMENT = OFF selama development.

Untuk VPS baru:

- gunakan sandbox/mock
- jangan masukkan production credentials sebelum siap
- jangan melakukan transaksi nyata saat setup
- webhook production belum diaktifkan.

Dokumentasikan requirement activation production secara umum saja.

Jangan memasukkan secret.

==================================================
13. SECURITY CHECKLIST
==================================================

Tambahkan checklist:

- .env tidak di-commit
- DATABASE_URL tidak di Git
- payment secret tidak di Git
- production credential tidak di Git
- SSH key aman
- PostgreSQL tidak dibuka public jika tidak diperlukan
- firewall
- Cloudflare/reverse proxy
- application runs as non-root jika memungkinkan.

Jangan mengubah VPS sekarang.

==================================================
14. BACKUP / ROLLBACK
==================================================

Dokumentasikan:

- backup database sebelum migration production
- backup environment configuration secara aman
- Git commit sebagai rollback point
- cara rollback aplikasi melalui Git.

Jangan menyimpan backup secret ke repository.

==================================================
15. MIGRATION CHECKLIST
==================================================

Buat checklist yang bisa diikuti saat pindah VPS:

[ ] Provision VPS
[ ] Install required runtime
[ ] Clone repository
[ ] Configure environment
[ ] Install dependencies
[ ] Configure PostgreSQL
[ ] Set DATABASE_URL
[ ] npm run typecheck
[ ] prisma generate
[ ] prisma migrate deploy
[ ] npm run build
[ ] Start application
[ ] Health check
[ ] Verify products
[ ] Verify checkout
[ ] Verify order
[ ] Verify invoice
[ ] Verify payment sandbox
[ ] Configure reverse proxy/Cloudflare
[ ] Final security check
[ ] Enable production payment only when explicitly ready

==================================================
16. TROUBLESHOOTING
==================================================

Tambahkan troubleshooting singkat untuk:

- DATABASE_URL missing
- Prisma connection failure
- prisma generate failure
- migration failure
- build failure
- port already in use
- application not starting
- Cloudflare cannot reach origin.

Jangan memberikan perintah destructive sebagai solusi default.

==================================================
17. KNOWN REMAINING WORK
==================================================

Dokumentasikan remaining work yang memang ditemukan audit:

- Full JWT auth Phase 27 masih TODO/out of current scope.
- Production payment activation belum dilakukan.
- Production credentials belum dikonfigurasi.
- Real payment transaction belum dilakukan.
- Production VPS deployment belum dilakukan.

Jangan membuat seolah-olah fitur tersebut sudah selesai.

==================================================
18. IMPORTANT RULE
==================================================

Jangan:

- mengubah database
- membuat migration
- menghapus migration
- reset database
- mengubah Cloudflare
- mengubah DNS
- mengubah port
- mengaktifkan production payment
- memasukkan secret
- membuat fitur baru
- membuat README kedua.

Setelah README selesai:

Jalankan:

git diff --check
git status --short
git diff --stat

Jangan commit.
Jangan push.

Jika hanya README.md yang berubah, tampilkan perubahan tersebut.

==================================================
FINAL REPORT
==================================================

Laporkan:

1. README diperbarui atau tidak.
2. Section yang ditambahkan.
3. Command deployment yang didokumentasikan.
4. Requirement VPS baru.
5. Database/Prisma deployment procedure.
6. Environment variables yang diperlukan tanpa secret.
7. Payment production status.
8. Remaining work.
9. git diff --check.
10. git status.
11. File yang berubah.

BERHENTI setelah laporan.

Jangan commit/push.

```
# Prompt: Final Production Readiness Audit
```

Prompt: Final Production Readiness Audit — Toko Online

Project: /root/toko-online
Branch: main

KONDISI:

Tahap checkout/order hardening sudah selesai.

Commit dan push terakhir SUDAH BERHASIL.
Working tree bersih.
Tidak ada migration baru.
Tidak ada perubahan Cloudflare/DNS/port.
Production payment masih OFF.
VPS ini hanya digunakan untuk development/testing.

SEKARANG LAKUKAN FINAL AUDIT PROJECT SEBELUM PROJECT DIPINDAHKAN KE VPS BARU.

PENTING:
Ini AUDIT dan perbaikan kecil hanya jika benar-benar diperlukan.
Jangan menambahkan fitur baru.
Jangan redesign UI.
Jangan mengubah arsitektur besar.
Jangan melakukan deployment production dari VPS ini.

==================================================
1. GIT / REPOSITORY AUDIT
==================================================

Periksa:

git status --short
git branch -vv
git log -5 --oneline
git remote -v
git diff --check

Pastikan:

- working tree clean
- branch main sinkron dengan origin/main
- tidak ada uncommitted change
- tidak ada conflict marker
- tidak ada secret
- tidak ada API key
- tidak ada credential production
- tidak ada .env tracked
- tidak ada node_modules tracked
- tidak ada build artifact yang salah tracked
- tidak ada temporary file.

Jangan melakukan force push.

==================================================
2. PROJECT STRUCTURE AUDIT
==================================================

Audit struktur project secara menyeluruh.

Cari:

- frontend
- backend/server
- Prisma
- database
- authentication
- product
- category
- order
- checkout
- payment
- webhook
- notification
- invoice
- admin
- repository/service layer
- environment configuration.

Pastikan tidak ada duplicate implementation yang tidak diperlukan.

Jangan menghapus file hanya karena terlihat tidak digunakan sebelum memastikan semua import/reference.

==================================================
3. DATABASE / PRISMA AUDIT
==================================================

Periksa:

- prisma/schema.prisma
- prisma/migrations
- Prisma client
- repository/service database layer
- DATABASE_URL usage.

Pastikan:

- Prisma schema valid.
- Migration existing tetap utuh.
- Tidak ada migration yang hilang.
- Tidak ada prisma migrate reset.
- Tidak ada database drop.
- Tidak ada database destructive operation.
- Tidak ada mock database yang menggantikan database production.

Jalankan hanya validasi yang aman.

Jika DATABASE_URL tidak tersedia di VPS development, jangan membuat database palsu hanya untuk menghilangkan error.

==================================================
4. API / SERVER AUDIT
==================================================

Audit endpoint yang digunakan frontend.

Minimal periksa:

- products
- product detail
- categories
- checkout
- order
- invoice
- payment
- payment webhook
- authentication
- admin endpoint.

Pastikan:

- public endpoint hanya membuka data yang memang public.
- endpoint customer/admin terlindungi.
- order ownership diperiksa server-side.
- price dihitung server-side.
- stock diperiksa server-side.
- quantity divalidasi server-side.
- payment status tidak dapat dipalsukan frontend.

Jangan membuat endpoint dummy.

==================================================
5. CHECKOUT AUDIT
==================================================

Pastikan flow:

Product
→ Checkout
→ Validation
→ Stock validation
→ Order
→ Payment session
→ Payment status
→ Invoice/order status

tetap konsisten.

Periksa:

- inactive product
- invalid product
- quantity
- stock
- price tampering
- total tampering
- duplicate checkout
- retry
- timeout.

Jangan membuat checkout system baru.

==================================================
6. ORDER LIFECYCLE AUDIT
==================================================

Periksa status:

PENDING
PAID
FAILED
EXPIRED
CANCELLED

Pastikan status transition aman.

Tidak boleh terjadi regression seperti:

PAID → PENDING

PAID → FAILED

PAID → EXPIRED

Pastikan expiry/failure/cancel tidak menyebabkan:

- duplicate restock
- duplicate notification
- duplicate payment update
- duplicate order processing.

Pastikan mekanisme idempotency existing tetap digunakan.

==================================================
7. PAYMENT AUDIT
==================================================

Production payment HARUS tetap OFF.

Jangan:

- memasukkan production credential
- melakukan transaksi nyata
- melakukan refund nyata
- melakukan cancel nyata
- mengaktifkan production provider.

Audit saja:

- payment session
- redirect
- status polling
- webhook
- signature validation
- idempotency
- retry handling.

Pastikan webhook tidak dapat dipanggil melalui path lain untuk melewati protection.

==================================================
8. FRONTEND AUDIT
==================================================

Periksa:

- product listing
- product detail
- checkout
- invoice
- order list
- order detail
- payment status.

Pastikan tidak ada:

- mock product
- mock order
- hardcoded payment status
- hardcoded price
- hardcoded stock
- fake success state.

Frontend harus menggunakan server/database untuk data dinamis.

Jangan mengubah desain UI.

==================================================
9. AUTHORIZATION / SECURITY AUDIT
==================================================

Audit:

- authentication
- authorization
- customer ownership
- admin access
- order access
- invoice access
- payment access.

Tes minimal:

User A tidak boleh membaca order User B.

Customer tidak boleh mengakses endpoint admin.

Client tidak boleh menentukan final price.

Client tidak boleh menentukan payment success.

Client tidak boleh menentukan stock.

Jika authentication masih memiliki bagian TODO yang memang berada di luar scope project saat ini, jangan membuat sistem auth baru. Dokumentasikan.

==================================================
10. ENVIRONMENT AUDIT
==================================================

Periksa:

- .env.example
- environment variable references
- README/documentation.

Pastikan variable yang diperlukan terdokumentasi tanpa nilai secret.

Contoh:

DATABASE_URL=...
PAYMENT_PROVIDER_KEY=...
PAYMENT_WEBHOOK_SECRET=...

Jangan memasukkan nilai asli.

Pastikan tidak ada secret tertulis di source code.

==================================================
11. BUILD / TYPECHECK
==================================================

Jalankan:

npm run typecheck
npm run build

Jika keduanya PASS, jangan melakukan perubahan hanya untuk alasan kosmetik.

Jika gagal:

- identifikasi error sebenarnya
- perbaiki hanya jika error berasal dari project
- jalankan ulang verification.

Jangan melakukan migration reset.

==================================================
12. PRODUCTION START VALIDATION
==================================================

Karena project akan dipindahkan ke VPS baru, periksa apakah aplikasi memang membutuhkan:

npm run start

atau command production lain sesuai package.json.

Jangan mengubah Cloudflare/DNS/port pada VPS ini.

Jika start membutuhkan DATABASE_URL atau service eksternal yang memang belum tersedia di VPS development, dokumentasikan requirement tersebut.

Jangan membuat workaround palsu.

==================================================
13. VPS MIGRATION CHECKLIST
==================================================

Buat laporan khusus untuk VPS baru:

A. Runtime:
- Node.js version
- npm version
- package manager
- required system packages.

B. Application:
- install dependencies
- environment variables
- build command
- start command.

C. Database:
- PostgreSQL requirement
- DATABASE_URL
- migration deployment command yang aman.

D. Payment:
- sandbox/mock configuration
- production activation requirement.

E. Cloudflare:
- domain
- DNS
- tunnel
- port
- reverse proxy.

JANGAN mengubah konfigurasi tersebut sekarang.

Hanya dokumentasikan apa yang nanti harus dilakukan di VPS baru.

==================================================
14. README
==================================================

Jika README sudah ada, jangan membuat README baru.

Tambahkan hanya informasi migration/deployment yang benar-benar diperlukan jika memang belum terdokumentasi.

Jika README sudah cukup lengkap, jangan mengubahnya.

==================================================
15. FINAL VERIFICATION
==================================================

Setelah audit/perbaikan kecil selesai:

git status --short

git diff --check

git diff --stat

Jika ada perubahan:

JANGAN commit/push.

Berhenti dan laporkan perubahan agar bisa saya review.

Jika tidak ada perubahan:

working tree harus tetap clean.

==================================================
FINAL REPORT
==================================================

Berikan laporan dengan format:

1. Repository status
2. Commit terakhir
3. Branch status
4. Prisma/database status
5. API/server status
6. Checkout status
7. Order lifecycle status
8. Payment status
9. Authentication/authorization status
10. Frontend status
11. Security status
12. Typecheck result
13. Build result
14. Production start readiness
15. VPS migration requirements
16. Remaining issues
17. Apakah ada perubahan file
18. Apakah perlu commit baru

PENTING:

Jangan commit.
Jangan push.
Jangan migration.
Jangan reset database.
Jangan mengaktifkan production payment.
Jangan mengubah Cloudflare/DNS/port.
Jangan membuat fitur baru.

Berhenti setelah final report.

```
# Prompt: Commit & Push Final Checkout Hardening
```

Prompt: Commit & Push Final Checkout Hardening

Project: /root/toko-online
Branch: main

Pekerjaan hardening checkout/order sudah selesai dan sudah diverifikasi.

Sekarang lakukan FINAL GIT COMMIT & PUSH.

WAJIB:

1. Periksa:
   git status --short

2. Periksa:
   git diff --check

3. Periksa:
   git diff --stat

4. Pastikan tidak ada:
   - .env
   - .env.*
   - API key
   - secret
   - credential production
   - node_modules
   - build artifact
   - temporary file
   - conflict marker

5. Pastikan perubahan yang akan di-commit hanya perubahan project yang memang berasal dari tahap checkout/order hardening.

6. Jangan melakukan:
   - prisma migrate reset
   - database reset
   - database drop
   - migration baru
   - perubahan schema
   - perubahan Cloudflare
   - perubahan DNS
   - perubahan port
   - production payment activation
   - transaksi payment nyata.

7. Jalankan verification terakhir yang ringan:
   - git diff --check
   - jika typecheck/build sudah PASS pada tahap sebelumnya, jangan mengulang build yang mahal kecuali ada perubahan setelah verification.

8. Stage perubahan:

git add .

9. Tampilkan staged summary:

git diff --cached --stat

10. Commit dengan message:

fix(payment): harden checkout order lifecycle

11. Setelah commit berhasil, periksa:

git status --short
git log -1 --oneline

12. Push ke remote branch main:

git push origin main

13. Setelah push, verifikasi:

git status --short
git log -1 --oneline
git branch -vv

14. Pastikan:
   - push berhasil
   - working tree bersih
   - branch main sinkron dengan origin/main
   - tidak ada force push
   - tidak ada migration
   - tidak ada production credential
   - tidak ada perubahan Cloudflare/DNS/port.

Jika git push gagal karena masalah authentication/network, JANGAN memaksa dan JANGAN force push. Laporkan error sebenarnya.

Jika ada file mencurigakan/secret sebelum commit, BERHENTI dan jangan commit.

FINAL REPORT:

- Commit hash
- Commit message
- Push status
- Branch status
- Working tree status
- Jumlah file yang di-commit
- Ringkasan perubahan
- Konfirmasi tidak ada migration
- Konfirmasi production payment tetap OFF
- Konfirmasi tidak ada secret/API key yang ikut ter-commit

Jangan membuat perubahan fitur tambahan.
Selesai setelah push berhasil.

```
# Prompt berikutnya — Final Hardening Checkout & Order
```

Prompt: Final Checkout & Order Hardening

Project: /root/toko-online
Branch: main

KONDISI TERAKHIR:

Audit checkout end-to-end sebelumnya sudah selesai.

File yang saat ini berubah antara lain:
- app/src/components/checkout/CheckoutContent.tsx
- app/src/components/invoice/InvoiceContent.tsx
- app/src/components/invoice/InvoiceStatusBadge.tsx

git diff --check bersih.
Tidak ada secret/API key/credential production.
Tidak ada migration database.
Tidak ada perubahan Cloudflare/DNS/port.
Belum ada commit/push.

Dari audit terakhir ditemukan beberapa remaining item:

1. Restock pada order EXPIRED/FAILED/CANCELLED belum dikembalikan.
2. Ownership/order access sudah ada di server-side, tetapi frontend order list/detail masih bergantung pada session cookie/middleware existing.
3. Terdapat dua checkout schema:
   - lib/checkout-schema
   - lib/validations/checkout-schema
   Salah satunya tampaknya legacy/stale.
4. Payment method mapping frontend saat ini hanya menampilkan:
   - QRIS
   - bank transfer
   - e-wallet
   sementara backend memiliki dukungan metode lain, tetapi jangan memperluas UI di luar desain existing.
5. Production payment tetap belum diaktifkan.

TUJUAN:

Lakukan final hardening terhadap checkout/order flow yang sudah ada.

Jangan membuat ulang arsitektur.
Jangan redesign UI.
Jangan mengaktifkan payment production.

==================================================
1. AUDIT CURRENT WORKTREE
==================================================

Sebelum coding:

- baca git status
- baca git diff
- identifikasi semua file yang sudah berubah
- pahami perubahan yang sudah dibuat oleh tahap sebelumnya.

Jangan membuang perubahan existing.

Pertahankan perubahan checkout/invoice yang sudah benar.

==================================================
2. RESTOCK ORDER FAILURE / EXPIRY
==================================================

Audit bagaimana stock sekarang dikurangi ketika order dibuat/payment dimulai.

Tentukan secara pasti:

- kapan stock dikurangi
- apakah stock dianggap reserved
- bagaimana order menjadi EXPIRED
- bagaimana order menjadi FAILED
- bagaimana order menjadi CANCELLED
- apakah stock sudah pernah dikembalikan.

Jika arsitektur existing memang melakukan stock deduction/reservation pada checkout, implementasikan mekanisme kompensasi yang aman untuk:

EXPIRED
FAILED
CANCELLED

Tetapi:

JANGAN langsung membuat cron/job baru jika lifecycle existing sudah memiliki tempat yang tepat untuk melakukan ini.

Gunakan helper/service existing jika tersedia.

Pastikan restock idempotent.

Contoh:

Order diproses dua kali:

EXPIRED callback
+
expiry scheduler

tidak boleh membuat stock kembali 2x.

Gunakan status transition atau marker existing untuk memastikan hanya satu proses yang melakukan restock.

Jangan mengubah schema jika tidak benar-benar diperlukan.

Jika schema existing tidak menyediakan cara aman untuk membedakan stock yang sudah dikembalikan, jangan membuat migration otomatis.

Laporkan kebutuhan schema sebagai remaining work.

==================================================
3. ORDER STATUS TRANSITION
==================================================

Audit seluruh perubahan status order.

Pastikan transition tidak dapat regress.

Contoh valid:

PENDING → PAID
PENDING → FAILED
PENDING → EXPIRED
PENDING → CANCELLED

Contoh yang harus ditolak:

PAID → PENDING
PAID → FAILED
PAID → EXPIRED

FAILED → PAID hanya jika provider reconciliation yang valid memang mengizinkan dan arsitektur existing mendukungnya.

Jangan mengubah status hanya berdasarkan frontend.

Semua status penting harus berasal dari server/database/provider verification.

==================================================
4. PAYMENT STATUS + ORDER STATUS
==================================================

Pastikan payment status dan order status tetap konsisten.

Audit:

- create payment
- payment pending
- payment success
- payment failed
- payment expired
- payment cancelled
- webhook
- redirect
- status polling.

Redirect frontend tidak boleh menjadi sumber kebenaran.

Webhook harus tetap menggunakan mekanisme idempotency yang sudah dibuat.

Jangan membuat webhook mechanism kedua.

==================================================
5. DUPLICATE CHECKOUT
==================================================

Audit ulang double-click/retry.

Skenario:

User klik Checkout.
Request berjalan.
User klik lagi.
Network timeout.
User retry.

Pastikan tidak terjadi:

- duplicate order
- duplicate payment session
- duplicate stock deduction.

Gunakan mekanisme idempotency existing.

Jangan membuat sistem idempotency kedua jika mekanisme existing sudah cukup.

==================================================
6. ORDER OWNERSHIP
==================================================

Audit:

- order list
- order detail
- invoice
- payment status.

Pastikan user tidak dapat membaca order milik user lain hanya dengan mengganti:

/orders/:id

atau parameter order ID/reference.

Server harus melakukan ownership check.

Jangan hanya menyembunyikan order di frontend.

Jika auth/session middleware existing sudah menyediakan user identity, gunakan itu.

Jangan membuat authentication system baru.

Jika authentication penuh masih merupakan TODO existing, dokumentasikan saja.

==================================================
7. FRONTEND ORDER LIST
==================================================

Periksa:

InvoiceListContent.tsx
InvoiceContent.tsx
InvoiceStatusBadge.tsx

Pastikan:

- data berasal dari server.
- tidak menggunakan mock order.
- status ditampilkan sesuai server.
- loading state benar.
- empty state benar.
- error state benar.
- order yang bukan milik user tidak muncul.
- tidak ada hardcoded payment status.

Pertahankan desain existing.

==================================================
8. DUPLICATE CHECKOUT SCHEMA
==================================================

Audit dua file:

lib/checkout-schema
lib/validations/checkout-schema

Tentukan:

- mana yang benar-benar digunakan.
- mana yang legacy.
- apakah keduanya memiliki aturan validasi berbeda.
- apakah ada import yang masih memakai file legacy.

Jangan menghapus file secara membabi buta.

Jika salah satu memang tidak digunakan dan aman dihapus, hapus hanya setelah memastikan:

- tidak ada import.
- tidak ada dynamic import.
- tidak ada test yang menggunakannya.
- tidak ada build dependency.
- tidak ada reference di dokumentasi.

Jika keduanya masih diperlukan untuk alasan kompatibilitas, jangan hapus.

Tujuannya adalah satu source of truth untuk checkout validation.

==================================================
9. PAYMENT METHOD MAPPING
==================================================

Audit mapping payment method.

Frontend saat ini menggunakan:

- QRIS
- bank transfer
- e-wallet.

Backend mungkin memiliki dukungan metode lain.

Jangan menambahkan payment method baru hanya karena backend mendukungnya.

Pastikan mapping existing:

frontend value
→ backend value
→ provider value

konsisten.

Jika metode yang tidak ditampilkan UI memang sengaja berada di luar desain, pertahankan.

Jangan redesign checkout.

==================================================
10. SECURITY AUDIT
==================================================

Periksa kembali:

- price tampering
- total tampering
- quantity tampering
- order ID enumeration
- unauthorized invoice access
- payment status spoofing
- duplicate checkout
- webhook replay
- webhook duplicate callback
- secret leakage.

Pastikan client tidak dapat menentukan:

- final price
- final total
- payment status
- order ownership
- stock result.

Server/database tetap source of truth.

==================================================
11. TESTS
==================================================

Tambahkan atau perbaiki test hanya jika memang diperlukan.

Minimal verifikasi:

A. Checkout valid.

B. Invalid product.

C. Product inactive.

D. Insufficient stock.

E. Invalid quantity.

F. Price tampering.

G. Total tampering.

H. Duplicate checkout.

I. Payment session failure.

J. Payment pending.

K. Payment success.

L. Payment failure.

M. Payment expiry.

N. Cancellation.

O. Duplicate expiry processing tidak double-restock.

P. Unauthorized order access.

Q. Existing webhook idempotency.

R. Existing payment lifecycle tests.

Gunakan mock/stub provider.

JANGAN melakukan transaksi payment nyata.

==================================================
12. BUILD VERIFICATION
==================================================

Jalankan:

npm run typecheck

npm run build

Jika tersedia test command yang relevan, jalankan test checkout/payment/order.

Jangan menjalankan migration reset.

Jangan mengubah database production.

Jika server perlu dijalankan untuk smoke test, gunakan environment testing/sandbox yang tersedia.

==================================================
13. DATABASE RULES
==================================================

Jangan:

- prisma migrate reset
- drop database
- menghapus migration
- membuat database baru
- mengganti PostgreSQL
- mengganti Prisma.

Jika perubahan schema benar-benar wajib:

BERHENTI sebelum membuat migration.

Laporkan kebutuhan migration.

==================================================
14. PRODUCTION PAYMENT
==================================================

Tetap OFF.

Jangan:

- memasukkan MIDTRANS_SERVER_KEY production
- memasukkan client key production
- mengaktifkan production adapter
- melakukan transaksi nyata
- melakukan refund nyata
- melakukan cancel nyata.

Sandbox/mock tetap digunakan.

==================================================
15. GIT
==================================================

Setelah semua pekerjaan:

git status --short

git diff --check

git diff --stat

Pastikan:

- tidak ada secret
- tidak ada .env tracked
- tidak ada node_modules tracked
- tidak ada build artifact yang tidak seharusnya
- tidak ada temporary file
- tidak ada conflict marker.

PENTING:

JANGAN commit.
JANGAN push.

Berhenti setelah verification.

==================================================
FINAL REPORT
==================================================

Laporkan secara jelas:

1. File yang diubah.
2. Restock behavior.
3. Status transition.
4. Idempotency behavior.
5. Order ownership.
6. Checkout schema yang dipakai.
7. Apakah schema duplicate dihapus atau dipertahankan dan alasannya.
8. Payment method mapping.
9. Security findings.
10. Tests yang dijalankan.
11. npm run typecheck.
12. npm run build.
13. Apakah membutuhkan migration.
14. Remaining work.
15. git status.
16. git diff --check.

Jangan commit/push.

Berhenti setelah laporan.

```
# Prompt berikutnya — Checkout End-to-End
```

Prompt: Checkout End-to-End Integration & Verification

Project: /root/toko-online
Branch: main

KONDISI TERAKHIR:

Payment session, checkout integration, redirect payment flow, server-side status handling, webhook verification, webhook idempotency, transaction safety, status transition, dan concurrency handling sudah selesai.

Commit terakhir:
fix(payment): harden webhook idempotency

Git:
- main sudah ter-push ke origin/main
- working tree sebelumnya bersih
- Jangan membuat commit atau push pada tahap ini.

TUJUAN:

Sekarang audit dan selesaikan alur checkout secara END-TO-END dari frontend sampai backend/database.

Fokus:
Product → Cart → Checkout → Order → Payment Session → Payment Status → Redirect/Result.

Tetap menggunakan Prisma/PostgreSQL sebagai sumber data utama.

PENTING:
Ini masih environment coding/testing.

JANGAN:
- mengaktifkan payment production
- memasukkan credential Midtrans production
- melakukan transaksi nyata
- mengubah Cloudflare
- mengubah DNS
- mengubah port VPS
- melakukan deployment production
- membuat migration tanpa kebutuhan yang benar-benar terbukti
- melakukan prisma migrate reset
- menghapus migration existing
- mengganti PostgreSQL dengan database lain
- mengganti database dengan mock/static data.

==================================================
1. AUDIT FRONTEND CHECKOUT
==================================================

Baca implementasi frontend yang sudah ada.

Identifikasi:
- product listing
- product detail
- cart
- checkout page/component
- customer information
- order summary
- payment selection
- payment button
- loading state
- error state
- success state
- redirect/payment result page.

Jangan membuat UI baru jika komponen existing sudah dapat digunakan.

Pertahankan desain/UI existing.

Pastikan frontend tidak lagi menggunakan data hardcoded jika data tersebut seharusnya berasal dari backend/database.

==================================================
2. AUDIT PRODUCT → CART
==================================================

Pastikan product yang dipilih berasal dari database/server.

Saat user memasukkan produk ke cart:

- product ID harus valid
- quantity harus valid
- product harus masih aktif
- product harus tersedia
- harga yang ditampilkan harus berasal dari server
- frontend tidak boleh menjadi sumber kebenaran harga.

Jangan mempercayai:
- price dari client
- total dari client
- stock dari client
- active status dari client.

==================================================
3. CHECKOUT SERVER-SIDE VALIDATION
==================================================

Audit endpoint/server action checkout yang sudah ada.

Server harus melakukan validasi ulang:

- product existence
- active status
- stock
- quantity
- current price
- total
- customer information
- order data.

Harga dan total harus dihitung ulang di server.

Jangan menggunakan total yang dikirim frontend sebagai sumber kebenaran.

Jika data tidak valid:
- return error yang sesuai
- jangan membuat order
- jangan membuat payment session
- jangan mengurangi stock.

==================================================
4. ORDER CREATION
==================================================

Pastikan order dibuat secara transactional.

Urutan logika harus aman:

1. Validasi request.
2. Ambil product dari database.
3. Validasi active status.
4. Validasi stock.
5. Validasi quantity.
6. Hitung subtotal server-side.
7. Hitung total server-side.
8. Buat order.
9. Buat order item.
10. Buat payment/session jika memang flow existing membutuhkannya.
11. Commit transaction.

Jangan membuat order setengah jadi.

Jika salah satu operasi gagal:
- transaction rollback.
- tidak boleh ada order orphan.
- tidak boleh ada order item orphan.
- jangan mengurangi stock secara permanen jika payment/order creation gagal.

Gunakan transaction Prisma yang sesuai dengan schema existing.

==================================================
5. PAYMENT SESSION
==================================================

Audit integration payment session yang sudah dibuat pada tahap sebelumnya.

Pastikan:

- payment session dibuat setelah order valid.
- amount berasal dari server.
- order reference berasal dari server.
- customer/order information tidak dapat dimanipulasi client.
- payment URL/token hanya berasal dari provider adapter existing.
- sandbox/mock tetap menjadi default.

Jangan mengubah adapter provider menjadi production.

Jika provider tidak dikonfigurasi:
- sistem harus memberikan response/error yang aman.
- jangan crash.
- jangan membuat transaksi nyata.

==================================================
6. CHECKOUT RETRY
==================================================

Audit kemungkinan user menekan tombol checkout berkali-kali.

Pastikan double-click/retry tidak menyebabkan:

- duplicate order
- duplicate payment
- duplicate stock deduction.

Gunakan mekanisme existing jika sudah tersedia.

Jika idempotency key/order reference sudah ada, gunakan kembali.

Jangan membuat mekanisme kedua yang bertentangan dengan idempotency implementation sebelumnya.

==================================================
7. PAYMENT RESULT / REDIRECT
==================================================

Audit halaman setelah user kembali dari payment provider.

Pastikan frontend:

- tidak langsung menganggap payment PAID hanya karena redirect berhasil.
- mengambil status dari backend.
- backend/database menjadi sumber kebenaran.
- status pending tetap pending.
- status paid hanya muncul jika backend sudah memvalidasi status.
- status failed/cancelled ditampilkan sesuai status server.

Jangan mempercayai query parameter client sebagai bukti pembayaran.

==================================================
8. ORDER DETAIL
==================================================

Pastikan customer dapat melihat informasi order yang benar sesuai flow existing.

Periksa:

- order ID/reference
- product
- quantity
- price
- total
- payment status
- order status.

Jangan membocorkan data sensitif.

Jangan membuat endpoint publik yang memungkinkan user melihat order milik user lain hanya dengan mengganti ID.

Jika authentication/customer ownership sudah tersedia, gunakan mekanisme tersebut.

Jika ownership belum tersedia, dokumentasikan sebagai remaining work dan jangan membuat sistem authentication baru di luar scope.

==================================================
9. STATUS CONSISTENCY
==================================================

Audit hubungan:

Payment status
↕
Order status

Pastikan status tidak dapat regress secara sembarangan.

Contoh:

PENDING → PAID
PENDING → FAILED
PENDING → CANCELLED

Tetapi callback lama tidak boleh mengubah:

PAID → PENDING
PAID → FAILED

dan status final tidak boleh diturunkan hanya karena request frontend.

Gunakan helper/status transition yang sudah ada jika tersedia.

==================================================
10. STOCK SAFETY
==================================================

Audit stock handling.

Pastikan stock tidak:

- berkurang dua kali
- menjadi negatif
- berkurang karena request checkout gagal
- berkurang hanya karena frontend membuka checkout.

Jika arsitektur existing memang menggunakan reservation atau stock deduction pada tahap tertentu, pertahankan pola tersebut dan verifikasi bahwa retry aman.

Jangan membuat inventory architecture baru jika belum diperlukan.

==================================================
11. ERROR HANDLING
==================================================

Pastikan semua failure penting ditangani:

- product tidak ditemukan
- product inactive
- stock habis
- quantity invalid
- price berubah
- database error
- payment provider unavailable
- payment session gagal
- webhook belum dikonfigurasi
- invalid order
- unauthorized order access.

Error response harus konsisten dengan API architecture existing.

Jangan expose:
- DATABASE_URL
- API key
- provider secret
- stack trace sensitif
- internal credentials.

==================================================
12. TESTING
==================================================

Buat/perbaiki test hanya jika memang diperlukan.

Minimal verifikasi:

A. Product valid → checkout berhasil.

B. Product tidak ditemukan → checkout ditolak.

C. Product inactive → checkout ditolak.

D. Stock tidak cukup → checkout ditolak.

E. Quantity invalid → checkout ditolak.

F. Client mengirim price palsu → server tetap menggunakan harga database.

G. Client mengirim total palsu → server menghitung ulang.

H. Checkout request duplicate → tidak menghasilkan transaksi/order duplicate.

I. Payment session gagal → database tidak berada pada keadaan setengah jadi.

J. Payment redirect pending → backend tetap menentukan status.

K. Payment redirect paid → hanya backend/provider status yang menentukan PAID.

L. Order milik user lain → tidak dapat diakses.

M. Existing webhook idempotency tetap lulus.

N. Existing payment tests tetap lulus.

==================================================
13. BUILD & TYPECHECK
==================================================

Setelah implementasi jalankan:

npm run typecheck

npm run build

Jalankan test payment/order/checkout yang relevan.

Jika server dapat dijalankan tanpa mengganggu process yang sudah aktif, lakukan smoke test endpoint yang relevan.

Jangan mematikan process yang bukan milik task ini.

==================================================
14. DATABASE
==================================================

Gunakan schema Prisma existing sebagai sumber kebenaran.

Sebelum mengubah schema:

- periksa apakah field/relation yang diperlukan sebenarnya sudah tersedia.
- jangan membuat migration hanya karena ingin merapikan schema.
- jangan menghapus migration.
- jangan reset database.
- jangan membuat database baru.

Jika ternyata ada kebutuhan schema yang benar-benar tidak dapat diselesaikan tanpa migration:

JANGAN menjalankan migration production.

Laporkan:
- field/relation yang diperlukan
- alasan
- file schema yang perlu diubah
- migration yang nantinya diperlukan.

==================================================
15. UI
==================================================

Jangan melakukan redesign.

Pertahankan:
- layout
- warna
- styling
- komponen
- navigasi.

Perubahan UI hanya diperbolehkan jika diperlukan agar checkout benar-benar terhubung dengan backend.

==================================================
16. SECURITY
==================================================

Audit secara khusus:

- client price tampering
- client total tampering
- client quantity tampering
- order ID enumeration
- unauthorized order access
- duplicate checkout
- duplicate payment
- payment status spoofing
- redirect parameter spoofing.

Server/database harus menjadi source of truth.

==================================================
17. SCOPE YANG TIDAK BOLEH DIKERJAKAN
==================================================

Jangan mengerjakan:

- production Midtrans activation
- production credentials
- real payment
- real cancel/refund
- dedicated providerEventId migration
- email delivery
- coupon engine
- reviews
- analytics
- address management
- favorites
- admin user management
- production VPS deployment
- Cloudflare/DNS migration
- UI redesign.

Jika menemukan masalah di luar scope, catat sebagai FUTURE/REMAINING WORK.

==================================================
18. FINAL VERIFICATION
==================================================

Sebelum selesai jalankan:

git status --short

git diff --check

Pastikan tidak ada:

- secret
- API key
- production credential
- .env tracked
- temporary files
- build artifacts yang tidak seharusnya di-track.

JANGAN commit.
JANGAN push.

==================================================
LAPORAN AKHIR
==================================================

Tampilkan:

A. File yang diubah.

B. Perubahan frontend checkout.

C. Perubahan backend checkout.

D. Validasi server-side yang ditambahkan/diperbaiki.

E. Status order/payment flow.

F. Idempotency/retry behavior.

G. Stock safety.

H. Security findings.

I. Test yang dijalankan dan hasilnya.

J. Hasil npm run typecheck.

K. Hasil npm run build.

L. Apakah membutuhkan Prisma migration.

M. Remaining work/future.

N. Git status.

Berhenti setelah laporan.

Jangan commit atau push sampai saya memberikan instruksi berikutnya.

```
# Prompt — Commit & Push
```
Prompt: Commit and Push Payment Idempotency Hardening

Project: /root/toko-online

Hasil tahap sebelumnya sudah selesai dan seluruh verifikasi lulus.

Lakukan hanya proses finalisasi Git:

1. Jalankan:
   git status --short
   git diff --check

2. Pastikan tidak ada:
   - secret
   - API key
   - credential production
   - .env
   - file sementara
   - hasil build yang tidak seharusnya di-track.

3. Review perubahan yang memang berasal dari tahap:
   Payment Idempotency & Webhook Hardening.

4. Jangan mengubah kode lagi kecuali diperlukan untuk menghapus file sementara/secrets yang tidak sengaja masuk.

5. Jika working tree berisi perubahan yang benar, lakukan:

git add .
git commit -m "fix(payment): harden webhook idempotency"

6. Push:

git push origin main

7. Setelah push selesai, verifikasi:

git status --short
git log -1 --oneline
git branch -vv

8. Pastikan:
- commit berhasil
- origin/main sudah menunjuk ke commit terbaru
- working tree bersih
- push berhasil

JANGAN:
- membuat migration
- menjalankan prisma migrate reset
- mengubah database
- menjalankan transaksi payment nyata
- memasukkan credential production
- mengubah Cloudflare/DNS/port
- mengubah UI
- melakukan pekerjaan fitur baru.

Jika tidak ada perubahan untuk di-commit, jangan membuat empty commit. Laporkan bahwa working tree sudah clean.

Setelah selesai tampilkan:
- commit hash
- commit message
- hasil push
- status working tree.

Berhenti setelah itu.


```
# Prompt berikutnya — Payment Idempotency & Webhook Hardening
```

Prompt: Payment Idempotency & Webhook Hardening

Project: /root/toko-online
Branch: main

Konteks:
Payment session, checkout integration, redirect payment flow, server-side status handling, existing webhook verification, dan payment testing sudah selesai dan sudah di-commit/push.

Commit terakhir:
feat(payment): complete payment session and checkout integration

Sekarang lanjutkan tahap berikutnya dengan fokus HANYA pada reliability dan idempotency payment/webhook.

PENTING:
Project ini masih lingkungan coding/testing. Jangan mengaktifkan payment production, jangan memasukkan credential Midtrans production, jangan melakukan transaksi nyata, dan jangan mengubah Cloudflare/DNS/port/VPS deployment.

TUJUAN:
Pastikan webhook/payment callback yang sama dapat diterima berkali-kali tanpa menyebabkan order/payment berubah secara tidak aman atau diproses dua kali.

TUGAS:

1. AUDIT IMPLEMENTASI SAAT INI
- Baca seluruh payment flow yang sudah ada.
- Identifikasi:
  - payment creation
  - payment session
  - checkout
  - redirect
  - webhook
  - status update
  - retry handling
  - notification/event handling
- Jangan membuat ulang payment system.
- Gunakan arsitektur yang sudah ada.

2. IDEMPOTENCY WEBHOOK
- Audit mekanisme idempotency webhook yang sekarang.
- Tentukan identifier paling aman yang tersedia dari provider callback saat ini.
- Pastikan callback yang sama tidak menyebabkan:
  - order dibuat ulang
  - payment dibuat ulang
  - status berubah berkali-kali secara tidak valid
  - stock dikurangi dua kali
  - notification dikirim berkali-kali
  - side effect checkout dijalankan dua kali.

3. PROVIDER EVENT ID
Saat ini dedicated providerEventId/externalId masih ditunda karena membutuhkan perubahan schema.

Implementasikan hanya jika memang dapat dilakukan dengan aman menggunakan Prisma schema yang sudah ada.

Jika membutuhkan migration/schema change:
- Jangan membuat migration secara otomatis tanpa verifikasi.
- Jangan menghapus migration existing.
- Jangan reset database.
- Jangan menggunakan `prisma migrate reset`.
- Dokumentasikan dengan jelas bahwa migration diperlukan.
- Jika arsitektur saat ini sudah memiliki identifier unik yang cukup untuk idempotency, jangan memaksakan field baru.

4. TRANSACTION SAFETY
Pastikan proses webhook menggunakan Prisma transaction jika beberapa perubahan database harus terjadi sebagai satu unit.

Perhatikan khusus:
- payment status
- order status
- stock/inventory
- payment event
- notification

Jangan membuat partial update yang dapat meninggalkan database dalam keadaan tidak konsisten.

5. STATUS TRANSITION
Audit status transition.

Pastikan webhook tidak dapat melakukan transition mundur secara sembarangan.

Contoh:
- PAID tidak boleh kembali menjadi PENDING karena callback lama.
- CANCELLED/FAILED tidak boleh menghapus status PAID yang sudah final.
- Callback duplicate untuk status yang sama harus menjadi no-op.
- Callback out-of-order harus ditangani dengan aman.

Gunakan status enum/schema yang benar-benar ada di project.
Jangan mengarang status baru jika tidak diperlukan.

6. CONCURRENCY
Audit kemungkinan dua webhook datang hampir bersamaan.

Contoh:
- webhook A dan webhook B datang pada waktu hampir sama.
- keduanya membaca payment sebagai PENDING.
- keduanya mencoba mengubah payment/order.

Pastikan implementasi tidak menyebabkan:
- double stock deduction
- double order completion
- duplicate payment processing.

Gunakan mekanisme Prisma/PostgreSQL yang sesuai dengan schema dan pola transaction yang sudah ada.

7. RETRY TEST
Buat test untuk skenario:

A. webhook valid pertama
B. webhook valid yang sama dikirim kedua kali
C. webhook valid yang sama dikirim berkali-kali
D. webhook dengan status berbeda datang setelah status final
E. webhook datang out-of-order
F. dua callback diproses secara concurrent jika test environment memungkinkan
G. malformed payload
H. invalid signature
I. webhook provider belum dikonfigurasi

Expected:
- tidak ada duplicate transaction
- tidak ada duplicate stock deduction
- tidak ada invalid status regression
- tidak ada uncaught exception
- response HTTP tetap sesuai contract endpoint yang sudah ada.

8. JANGAN MENGUBAH UI
Tidak perlu mengubah:
- halaman catalog
- product detail
- cart
- checkout UI
- styling
- layout
- komponen frontend

Kecuali benar-benar diperlukan untuk memperbaiki bug akibat integrasi backend.

9. JANGAN MENGGANTI DATABASE
Tetap gunakan PostgreSQL + Prisma.

Jangan:
- membuat mock database baru
- mengganti SQLite
- membuat JSON database
- membuat static payment store
- membuat database kedua.

10. JANGAN MENGAKTIFKAN PRODUCTION PAYMENT
Tetap sandbox/mock.

Jangan:
- memasukkan PAYMENT_PROVIDER_MIDTRANS_SERVER_KEY production
- menggunakan client key production
- menjalankan transaksi nyata
- mengaktifkan production adapter
- mengubah sandbox menjadi production.

11. VALIDASI

Setelah implementasi jalankan:

npm run typecheck
npm run build

Kemudian jalankan test payment yang relevan.

Jika ada test khusus payment/webhook, jalankan semuanya.

Jika server dapat dijalankan tanpa mengganggu process existing, lakukan smoke test endpoint yang relevan.

Minimal verifikasi:
- valid webhook
- duplicate webhook
- invalid signature
- malformed payload
- status transition
- idempotency.

12. JAGA PERUBAHAN TETAP TERBATAS

Jangan melakukan pekerjaan di luar scope seperti:
- production deployment
- Cloudflare
- DNS
- VPS configuration
- email
- coupon
- reviews
- analytics
- address management
- refund production
- payment provider activation.

Jangan menghapus fitur existing.

Jangan melakukan refactor besar yang tidak diperlukan.

13. GIT

JANGAN commit.
JANGAN push GitHub.

Setelah semua selesai tampilkan:

A. File yang diubah
B. Apa yang diperbaiki
C. Mekanisme idempotency yang digunakan
D. Apakah membutuhkan perubahan Prisma schema/migration
E. Hasil semua test
F. Risiko atau pekerjaan yang masih tersisa

Berhenti setelah laporan selesai.

```
# Prompt: Final Payment Integration Audit + Commit & Push
```

PROMPT — FINAL PAYMENT INTEGRATION AUDIT, CLEANUP, TEST, COMMIT & PUSH

Project: Digital Cell / toko-online

Lanjutkan dari seluruh pekerjaan payment integration yang sudah dikerjakan sebelumnya.

JANGAN mengulang implementasi yang sudah selesai. Audit kondisi repository saat ini terlebih dahulu dan lanjutkan hanya bagian yang memang masih diperlukan.

==================================================
CURRENT STATUS
==================================================

Payment integration saat ini sudah memiliki:

- Prisma/PostgreSQL sebagai sumber data utama.
- Catalog/product/order menggunakan data server/database.
- Checkout melakukan validasi server-side.
- Harga, stock, active status, quantity divalidasi di server.
- Order ownership protection sudah tersedia.
- Payment service sudah menggunakan database.
- Payment provider abstraction sudah tersedia.
- Midtrans adapter sudah tersedia.
- Create-payment/session flow sudah terhubung.
- Normalized payment response sudah tersedia.
- Redirect-based payment flow sudah terhubung.
- Payment status polling/reconciliation sudah tersedia.
- Payment webhook exact-path sudah tersedia.
- Webhook signature-aware.
- Payment callback tidak mempercayai client sebagai sumber status final.
- Duplicate payment/callback protection sudah diperiksa.
- Idempotency sudah diperiksa menggunakan mekanisme/schema existing.
- Transactional order/payment handling sudah menggunakan Prisma transaction sesuai architecture existing.
- Race condition antara webhook dan polling sudah diperiksa.
- Test payment menggunakan mock/stub, bukan transaksi nyata.
- TypeScript check berhasil.
- Production build berhasil.
- Cloudflare/DNS/port tidak diubah.
- Prisma schema tidak diubah pada tahap sebelumnya.
- Tidak ada production payment credential yang digunakan.
- Tidak ada transaksi Midtrans nyata.

HASIL TERAKHIR YANG SUDAH DIVERIFIKASI:

- Snap token flow tidak digunakan karena normalized response menggunakan redirectUrl.
- Redirect-based Snap/payment flow sudah diimplementasikan.
- Return/checkout status diverifikasi kembali melalui server-side status.
- Production Midtrans belum diaktifkan.
- Cancel/refund real flow belum diimplementasikan.
- Provider externalId/providerEventId dedicated belum dibuat karena membutuhkan schema migration.
- Payment provider real belum dipanggil.
- Test menggunakan mock/stub.
- Tidak ada migration baru.
- Tidak ada commit/push pada tahap sebelumnya.

==================================================
TUJUAN TAHAP INI
==================================================

Lakukan FINAL AUDIT terhadap seluruh perubahan payment integration.

Tujuan utama:

1. Pastikan implementasi yang sudah dibuat benar.
2. Pastikan tidak ada regression.
3. Pastikan checkout/payment flow konsisten.
4. Pastikan test dan build lulus.
5. Bersihkan perubahan yang tidak diperlukan jika ditemukan.
6. Jangan membuat migration baru.
7. Jangan melakukan transaksi payment nyata.
8. Setelah semua valid, LANGSUNG commit dan push perubahan ke GitHub.

==================================================
1. AUDIT WORKTREE
==================================================

Mulai dengan:

git status --short
git diff --stat
git diff

Audit perubahan yang belum di-commit.

Jangan menghapus perubahan existing yang memang bagian dari project.

Pisahkan perubahan menjadi:

- payment integration;
- checkout;
- backend;
- frontend;
- tests;
- konfigurasi;
- perubahan unrelated.

Jika ada perubahan unrelated yang memang berasal dari pekerjaan sebelumnya dan dibutuhkan project, jangan hapus.

Jika ada perubahan sementara/debug yang jelas tidak diperlukan, bersihkan.

==================================================
2. AUDIT PAYMENT FLOW
==================================================

Trace flow lengkap:

Customer
→ Checkout
→ Create Order
→ Create Payment
→ Provider Adapter
→ Payment Session
→ Redirect
→ Provider
→ Return
→ Backend Verification
→ Payment Status
→ Order Status

Pastikan tidak ada bagian yang:

- menggunakan mock sebagai production source;
- mengambil harga dari frontend;
- mengambil ownership dari frontend;
- mengubah payment status langsung dari browser;
- menganggap HTTP 200 sebagai PAID;
- membuat duplicate order saat retry;
- membuat duplicate payment tanpa alasan;
- membocorkan credential provider.

Jika menemukan bug nyata, perbaiki.

Jangan melakukan refactor besar yang tidak diperlukan.

==================================================
3. CHECKOUT
==================================================

Pastikan checkout:

- menggunakan product dari database;
- mengambil harga dari database;
- memvalidasi stock;
- memvalidasi active status;
- memvalidasi quantity;
- menjaga ownership;
- membuat order secara transactional;
- tidak percaya total dari client;
- tidak membuat order duplicate karena double click;
- tidak merusak cart existing.

Pastikan frontend tidak dapat memanipulasi:

- price;
- total;
- payment amount;
- order ownership;
- payment status.

==================================================
4. PAYMENT SESSION
==================================================

Pastikan create payment/session:

- menerima order yang valid;
- mengambil total dari server;
- memastikan order masih payable;
- menggunakan provider abstraction existing;
- menggunakan Midtrans adapter existing;
- menghasilkan normalized response;
- tidak mengirim secret/provider credential ke client.

Jika response menggunakan:

redirectUrl

pastikan frontend menggunakannya dengan benar.

Jangan mengganti flow menjadi Snap token jika architecture existing sudah menggunakan redirect.

Jangan membuat dua metode payment flow sekaligus hanya untuk memperbanyak kode.

==================================================
5. FRONTEND
==================================================

Audit checkout/payment UI.

Pastikan:

- tombol Bayar memiliki loading state;
- double click dicegah;
- error ditampilkan secara aman;
- redirectUrl digunakan dengan benar;
- customer tidak melihat internal error;
- payment status diambil dari backend;
- customer dapat kembali ke order detail;
- UI tidak menganggap payment sukses hanya karena redirect berhasil.

Jangan redesign UI.

Jangan mengubah styling yang tidak berhubungan dengan task.

==================================================
6. RETURN / STATUS
==================================================

Pastikan ketika customer kembali dari provider:

frontend melakukan server-side status verification.

Jangan melakukan:

setPaymentStatus("PAID")

berdasarkan query parameter atau return URL saja.

Status authoritative harus tetap berasal dari backend/database/provider reconciliation.

Pastikan status:

- PENDING
- PAID
- FAILED
- EXPIRED
- CANCELLED

atau enum existing project ditangani sesuai schema yang sudah ada.

Jangan mengubah enum Prisma.

==================================================
7. WEBHOOK
==================================================

Audit webhook existing.

Pastikan:

- exact path tetap;
- method POST;
- signature verification tetap;
- invalid payload ditolak;
- provider yang tidak dikonfigurasi ditangani aman;
- duplicate callback tidak membuat duplicate update;
- webhook tidak membuat duplicate order;
- webhook tidak mempercayai amount dari client;
- webhook tetap transactional;
- concurrent webhook/polling aman.

Jangan mengubah webhook menjadi endpoint public tanpa verification.

==================================================
8. IDEMPOTENCY
==================================================

Audit idempotency yang sudah ada.

Pastikan retry request tidak menghasilkan duplicate payment/order secara tidak sengaja.

Gunakan mekanisme existing.

JANGAN membuat migration hanya untuk idempotency.

JANGAN menambahkan providerEventId/externalId column pada tahap ini.

Jika provider event ID masih membutuhkan schema migration:

biarkan sebagai remaining work dan jangan implementasikan migration.

==================================================
9. TEST
==================================================

Jalankan test yang relevan dengan payment integration.

WAJIB menggunakan mock/stub.

JANGAN melakukan:

- real Midtrans transaction;
- real charge;
- real settlement;
- real refund;
- real cancel;
- production webhook.

Minimal verifikasi:

1. create payment success;
2. create payment failure;
3. invalid order;
4. unauthorized order;
5. already paid order;
6. duplicate request;
7. invalid provider response;
8. provider unavailable;
9. normalized response;
10. redirectUrl;
11. payment status polling;
12. webhook signature validation;
13. duplicate webhook;
14. concurrent payment state update;
15. secret leakage prevention.

Jika test existing sudah mencakup sebagian besar kasus tersebut, jangan membuat duplicate test.

==================================================
10. TYPECHECK & BUILD
==================================================

Jalankan:

npm run typecheck

kemudian:

npm run build

Jika project memiliki test command resmi yang relevan:

jalankan.

Perbaiki error yang disebabkan oleh perubahan payment integration.

Jangan mengejar warning yang tidak berhubungan dengan task jika build tetap valid.

Jika build gagal karena unrelated pre-existing issue:

verifikasi penyebabnya sebelum melakukan perubahan besar.

==================================================
11. DATABASE
==================================================

Database development ini hanya digunakan untuk coding/testing.

JANGAN:

- prisma migrate reset;
- prisma db push;
- membuat migration baru;
- menghapus migration;
- mengubah Prisma schema;
- menghapus data existing;
- memasukkan production data.

Jika migration status hanya menunjukkan migration yang memang belum diterapkan pada database development:

jangan membuat migration baru hanya untuk membuat status terlihat bersih.

==================================================
12. PRODUCTION PAYMENT
==================================================

WAJIB TETAP DISABLED.

Jangan memasukkan:

- Midtrans production server key;
- Midtrans client key production;
- production webhook secret;
- credential provider asli.

Jangan melakukan transaksi nyata.

Production activation akan dilakukan setelah project dipindahkan ke VPS production.

==================================================
13. CLOUDFLARE / DNS / PORT
==================================================

JANGAN mengubah:

- Cloudflare Tunnel;
- DNS;
- domain;
- port;
- firewall;
- VPS networking;
- systemd service;
- production deployment.

VPS saat ini hanya untuk coding dan verification.

Project akan dipindahkan ke VPS lain setelah development selesai.

==================================================
14. UNRELATED FEATURES
==================================================

Jangan mengimplementasikan fitur di luar scope seperti:

- real refund;
- real cancel provider;
- email delivery;
- reviews;
- ratings;
- coupon engine;
- analytics;
- address management;
- favorites;
- inventory reservation system;
- production deployment;
- Cloudflare migration;
- admin system baru.

Jika fitur tersebut masih tercatat sebagai remaining work:

biarkan.

==================================================
15. FINAL CLEANUP
==================================================

Setelah test dan build selesai:

- hapus debug console/log sementara jika memang tidak diperlukan;
- jangan menghapus logging production yang memang dibutuhkan;
- jangan menghapus test yang valid;
- jangan mengubah architecture hanya untuk memperpendek kode;
- pastikan TypeScript tetap bersih;
- pastikan tidak ada secret di tracked files.

Periksa:

git diff --check

==================================================
16. FINAL VERIFICATION
==================================================

Jalankan:

git status --short
git diff --stat
git diff --check

Pastikan hanya perubahan yang memang ingin disimpan.

Tampilkan daftar file yang akan di-commit.

==================================================
17. COMMIT
==================================================

SETELAH seluruh verifikasi berhasil, langsung commit.

Gunakan commit message yang jelas, misalnya:

feat(payment): complete payment session integration

Jika perubahan final ternyata lebih cocok menggunakan message lain, pilih Conventional Commit yang sesuai.

Jangan membuat commit kosong.

==================================================
18. PUSH
==================================================

Setelah commit berhasil:

push branch aktif ke remote origin.

Gunakan branch yang sedang aktif.

Jangan membuat branch baru kecuali branch existing memang tidak dapat dipush.

Sebelum push pastikan:

git branch --show-current
git remote -v

Kemudian push branch aktif.

Jika push ditolak karena remote memiliki commit baru:

JANGAN force push.

Lakukan fetch dan evaluasi kondisi repository terlebih dahulu.

Jangan menghapus history Git.

==================================================
19. SETELAH PUSH
==================================================

Verifikasi:

git status --short
git log -1 --oneline

Pastikan working tree bersih atau hanya berisi perubahan yang memang tidak terkait.

Pastikan commit berhasil berada di branch yang dipush.

==================================================
20. FINAL REPORT
==================================================

Setelah commit dan push berhasil, tampilkan laporan:

1. Payment integration status
2. Checkout status
3. Webhook status
4. Idempotency status
5. Frontend payment status
6. Test result
7. Typecheck result
8. Build result
9. Files changed
10. Commit hash
11. Commit message
12. Branch
13. Push result
14. Remaining work

Remaining work wajib dipisahkan:

DONE
- Payment session integration
- Redirect payment flow
- Server-side status verification
- Existing webhook verification
- Idempotency verification
- Mock payment testing
- Build/typecheck

NOT IMPLEMENTED / FUTURE
- Real Midtrans production activation
- Production credentials
- Real cancel/refund
- Dedicated providerEventId/externalId migration jika memang masih dibutuhkan
- Production VPS deployment

Jangan menyatakan production payment sudah aktif.

==================================================
FINAL RULES
==================================================

WAJIB:

- audit dulu;
- perbaiki hanya jika diperlukan;
- test;
- typecheck;
- build;
- git diff --check;
- commit;
- push;
- verifikasi push.

DILARANG:

- real payment;
- production credential;
- migration baru;
- Prisma schema change;
- migrate reset;
- db push;
- force push;
- mengubah Cloudflare;
- mengubah DNS;
- mengubah port;
- menghapus fitur existing;
- menghapus migration;
- membuat mock sebagai pengganti database production.

Jika semua valid, JANGAN berhenti hanya setelah build.

LANJUTKAN sampai COMMIT dan PUSH berhasil.

Berikan commit hash dan hasil push sebagai laporan akhir.

```
# Prompt: Payment Session & Checkout Completion
```

PROMPT — COMPLETE PAYMENT SESSION AND CHECKOUT FLOW

Project: Digital Cell / toko-online

Lanjutkan dari seluruh pekerjaan payment integration sebelumnya.

STATUS YANG SUDAH SELESAI

- Prisma/PostgreSQL menjadi sumber data utama.
- Catalog/product/order sudah terhubung ke backend database.
- Payment service sudah menggunakan database.
- Payment provider abstraction sudah tersedia.
- Midtrans adapter sudah tersedia.
- createPayment sudah terhubung ke core payment flow.
- Server-side status polling/reconciliation sudah terhubung.
- Payment webhook sudah exact-path dan signature-aware.
- Payment amount berasal dari server/database.
- Ownership protection sudah diterapkan.
- Payment state transition sudah diperketat.
- Order/payment consistency sudah diperiksa.
- Idempotency protection sudah diterapkan menggunakan mekanisme/schema existing.
- Duplicate callback protection sudah diuji.
- Race condition webhook/polling sudah diperiksa.
- Tidak ada migration baru.
- Tidak ada transaksi Midtrans production.
- Tidak ada production credential.
- Tidak ada perubahan Cloudflare/DNS/port.
- Tidak ada commit/push.

REMAINING WORK UTAMA

Create-session/payment session sudah tersedia di adapter/core, tetapi frontend masih belum sepenuhnya menggunakan hasil payment gateway seperti:

- redirectUrl
- Snap token
- payment reference
- atau response provider lain

sesuai implementasi existing.

TUJUAN TAHAP INI

Selesaikan alur checkout dari order sampai customer mendapatkan payment session yang benar.

TARGET:

Customer checkout
↓
Order valid
↓
Payment dibuat
↓
Payment provider dipilih
↓
Payment session dibuat
↓
Normalized payment response
↓
Frontend menerima response
↓
Customer dapat melanjutkan pembayaran
↓
Backend tetap menjadi sumber status payment

Jangan mengaktifkan transaksi production.

==================================================
1. AUDIT CHECKOUT EXISTING
==================================================

Audit terlebih dahulu:

- checkout page
- checkout component
- order creation
- payment creation
- payment service
- payment provider interface
- Midtrans adapter
- payment API
- order API
- existing payment UI.

Cari flow existing secara menyeluruh.

Jangan membuat duplicate checkout flow jika sudah ada.

Jangan membuat endpoint baru jika endpoint existing dapat digunakan dengan benar.

==================================================
2. TRACE FULL FLOW
==================================================

Trace flow aktual:

Frontend checkout
→ API
→ authentication
→ order
→ payment
→ provider
→ response
→ frontend.

Temukan titik dimana flow sekarang berhenti.

Secara khusus periksa apakah:

createPayment()

sudah benar-benar menghasilkan payment session provider.

Periksa apakah hasil adapter seperti:

- token
- redirect URL
- reference
- transaction ID

sudah diteruskan sampai frontend.

==================================================
3. NORMALIZED PAYMENT RESPONSE
==================================================

Pastikan backend tidak mengirim raw Midtrans response langsung ke frontend.

Gunakan normalized response sesuai abstraction existing.

Contoh konsep:

{
  paymentId,
  orderId,
  provider,
  status,
  redirectUrl,
  snapToken,
  reference
}

Gunakan hanya field yang benar-benar tersedia.

Jangan menambahkan field hanya untuk mengikuti contoh.

Jika project menggunakan nama field berbeda, pertahankan nama existing.

Jangan expose:

- server key
- secret
- signature secret
- authorization header
- internal adapter configuration.

==================================================
4. CREATE SESSION
==================================================

Pastikan create-session benar-benar dipanggil pada titik yang tepat.

Flow:

1. User checkout.
2. Backend validasi session/user.
3. Backend mengambil order dari database.
4. Backend validasi ownership.
5. Backend memastikan order masih payable.
6. Backend mengambil total dari database.
7. Backend memastikan payment existing tidak menyebabkan duplicate charge/session.
8. Payment service resolve provider.
9. Adapter membuat payment session.
10. Local payment record disimpan/update sesuai architecture existing.
11. Backend mengembalikan normalized response.

Jangan membuat payment berdasarkan amount yang dikirim frontend.

==================================================
5. PAYMENT SESSION RETRY
==================================================

Audit apa yang terjadi jika user:

- menekan tombol bayar dua kali;
- refresh halaman;
- kembali dari payment gateway;
- membuka checkout di tab lain;
- gagal menyelesaikan payment lalu mencoba lagi.

Gunakan payment/order state existing.

Jangan membuat order baru hanya karena customer melakukan retry.

Jangan membuat duplicate payment session jika existing payment/session masih valid menurut architecture.

Jika provider memang memerlukan session baru setelah expiry:

ikuti rule provider adapter yang sudah ada.

==================================================
6. FRONTEND PAYMENT HANDLING
==================================================

Hubungkan frontend ke normalized response.

Jika response memiliki:

snapToken

gunakan integration Snap yang memang sudah tersedia di project.

Jika response memiliki:

redirectUrl

gunakan redirect URL tersebut.

Jika keduanya tersedia:

ikuti architecture existing dan gunakan flow yang paling tepat.

Jangan hardcode:

- payment URL
- Snap token
- transaction ID
- provider endpoint.

Jangan menaruh credential provider di frontend.

==================================================
7. CLIENT STATE
==================================================

Frontend harus memiliki state yang jelas:

- idle
- creating payment
- payment ready
- payment failed
- payment completed/pending sesuai kebutuhan.

Saat tombol Bayar ditekan:

- disable duplicate submit;
- tampilkan loading;
- panggil backend;
- tangani error;
- restore button jika gagal.

Jangan menganggap HTTP 200 berarti payment sudah PAID.

==================================================
8. PAYMENT STATUS
==================================================

Setelah payment session dibuat:

frontend tidak boleh menentukan status pembayaran sendiri.

Status authoritative tetap dari backend.

Gunakan:

payment status endpoint
atau
existing reconciliation mechanism

yang sudah tersedia.

Jangan membuat frontend mengubah status order.

Jangan membuat:

POST status = PAID

dari browser.

==================================================
9. RETURN / CALLBACK FLOW
==================================================

Audit apakah provider mengembalikan customer ke:

- success URL
- pending URL
- error URL

Jika infrastructure existing sudah memilikinya:

gunakan existing implementation.

Jika belum:

buat minimal integration yang sesuai architecture existing.

Tetapi status final tetap diverifikasi ke backend.

Customer kembali ke halaman:

bukan berarti payment otomatis dianggap berhasil.

Frontend harus melakukan server-side verification.

==================================================
10. PAYMENT DETAIL PAGE
==================================================

Audit halaman order/payment detail existing.

Pastikan setelah checkout customer dapat melihat:

- order ID
- product/order information
- total
- payment status
- provider
- payment action jika masih payable.

Jangan redesign UI.

Hanya hubungkan data backend jika memang diperlukan.

==================================================
11. ERROR HANDLING
==================================================

Handle minimal:

A. Order tidak ditemukan
B. Order bukan milik user
C. Order sudah dibayar
D. Order expired
E. Payment configuration belum tersedia
F. Provider tidak tersedia
G. Provider adapter error
H. Session creation error
I. Invalid payment state
J. Network/provider timeout
K. Duplicate payment attempt

Gunakan HTTP status dan error structure existing.

Jangan expose raw provider error kepada customer jika mengandung internal details.

==================================================
12. SECURITY
==================================================

Pastikan:

- authentication tetap aktif;
- ownership tetap diverifikasi;
- amount tidak dipercaya dari client;
- provider tidak bisa dipilih secara bebas jika tidak diizinkan server;
- credential tetap server-side;
- raw provider response tidak bocor;
- internal error tidak bocor ke frontend.

Jangan melemahkan auth guard existing.

==================================================
13. DATABASE
==================================================

JANGAN mengubah schema Prisma.

JANGAN:

- membuat migration;
- prisma db push;
- prisma migrate reset;
- menghapus migration;
- membuat kolom baru.

Gunakan model existing.

Jika create-session membutuhkan field database yang belum tersedia:

STOP dan laporkan kebutuhan schema.

Jangan workaround dengan mengubah schema diam-diam.

==================================================
14. MOCK PAYMENT TEST
==================================================

Semua test payment session harus menggunakan mock/stub.

Jangan:

- menggunakan Midtrans production;
- menggunakan production server key;
- melakukan charge nyata;
- membuat transaksi customer nyata.

Test minimal:

1. Successful create session.
2. Missing provider configuration.
3. Invalid order.
4. Unauthorized order.
5. Already paid order.
6. Duplicate request.
7. Provider adapter failure.
8. Invalid provider response.
9. Normalized response.
10. No secret leakage.

==================================================
15. FRONTEND TEST
==================================================

Test bahwa frontend:

- memanggil endpoint yang benar;
- menampilkan loading;
- tidak double-submit;
- menangani payment session;
- membuka Snap/redirect jika response valid;
- menangani error;
- tidak menganggap HTTP 200 sebagai PAID.

Jika automated browser test infrastructure sudah ada:

gunakan yang existing.

Jangan menambahkan framework testing besar hanya untuk task ini.

==================================================
16. API TEST
==================================================

Verifikasi endpoint checkout/payment menggunakan test-safe environment.

Pastikan:

POST payment

menghasilkan response yang sesuai.

Jika provider belum dikonfigurasi:

harus menghasilkan configuration error yang aman.

Jangan mengaktifkan real provider hanya agar test PASS.

==================================================
17. BUILD
==================================================

Jalankan:

npm run typecheck
npm run build

Jika ada test script payment yang relevan:

jalankan juga.

Perbaiki error yang disebabkan oleh perubahan task ini.

Jangan memperbaiki unrelated issue dengan scope besar.

==================================================
18. RUNTIME
==================================================

Jika server sedang berjalan:

jangan mematikan process yang bukan milik task.

Jangan mengubah:

- port
- Cloudflare
- DNS
- tunnel
- production configuration.

Development VPS ini hanya digunakan untuk coding dan verification.

Production deployment akan dilakukan di VPS lain setelah project selesai.

==================================================
19. NO REAL PAYMENT
==================================================

WAJIB.

Jangan:

- menggunakan production credential;
- membuat transaksi Midtrans nyata;
- charge customer;
- melakukan settlement;
- refund nyata;
- mengaktifkan production provider.

Sandbox/mock boleh digunakan jika architecture existing memang mendukungnya, tetapi jangan mengubah environment production.

==================================================
20. GIT
==================================================

Jangan:

git commit
git push

Jangan reset worktree.

Jangan menghapus perubahan existing.

Di akhir jalankan:

git status --short

dan laporkan perubahan tracked/untracked.

==================================================
21. FINAL REPORT
==================================================

Setelah selesai tampilkan:

A. Checkout flow sebelum perubahan
B. Checkout flow setelah perubahan
C. Payment session integration
D. Backend API
E. Frontend integration
F. Payment status verification
G. Retry/double-submit handling
H. Security
I. Test results
J. Build/typecheck results
K. Files changed
L. Database/schema status
M. Remaining work

Khusus Remaining Work:

Pisahkan dengan jelas:

DONE
PARTIALLY DONE
NOT IMPLEMENTED

Jangan mengklaim real payment sudah selesai.

Jika Snap token/redirect URL masih membutuhkan production configuration:

jelaskan bahwa code integration selesai tetapi activation production belum dilakukan.

==================================================
STOP CONDITION
==================================================

Setelah selesai:

- jangan commit;
- jangan push;
- jangan migration;
- jangan transaksi nyata;
- jangan production credential;
- jangan ubah Cloudflare;
- jangan ubah DNS;
- jangan ubah port.

Berhenti setelah laporan verifikasi lengkap.

```
# Prompt: Payment Idempotency & State Consistency
```


PROMPT — PAYMENT IDEMPOTENCY & ORDER/PAYMENT STATE CONSISTENCY

Project: Digital Cell / toko-online

Lanjutkan dari hasil audit payment adapter terakhir.

STATUS SAAT INI

- Payment provider adapter sudah terintegrasi.
- Midtrans adapter sudah diverifikasi.
- createPayment sudah terhubung ke core.
- Server-side status polling/reconciliation sudah tersedia.
- Payment webhook sudah exact-path dan signature-aware.
- Payment amount berasal dari server/database.
- Ownership protection sudah diterapkan.
- Tidak ada transaksi Midtrans production.
- Tidak ada production credential.
- Test menggunakan mock/stub.
- Prisma/PostgreSQL tetap menjadi sumber data utama.
- Prisma schema TIDAK boleh diubah pada tahap ini.
- Migration TIDAK boleh dibuat.
- Cancel/refund nyata belum menjadi scope.
- Full provider-event idempotency belum selesai karena dedicated provider event ID belum tersedia di schema.

TUJUAN TAHAP INI

Perkuat consistency antara:

Order
↓
Payment
↓
Provider callback/status
↓
Order status transition

tanpa mengubah Prisma schema.

==================================================
1. AUDIT STATE MACHINE EXISTING
==================================================

Audit enum/status yang sudah ada untuk:

- Order
- Payment
- Payment provider status

Jangan membuat enum baru jika existing enum masih dapat digunakan.

Petakan status provider ke status internal secara terpusat.

Pastikan tidak ada status transition yang dilakukan langsung dari route tanpa melalui service/domain logic.

==================================================
2. PAYMENT STATE TRANSITION
==================================================

Buat atau rapikan centralized payment transition logic.

Rules:

PENDING → PAID
PENDING → FAILED
PENDING → EXPIRED

sesuai status yang memang tersedia di schema.

Jangan mengizinkan:

PAID → PENDING
PAID → FAILED

atau regression status lain yang tidak valid.

Jika status existing berbeda, gunakan status existing dan dokumentasikan mapping-nya.

==================================================
3. ORDER STATE TRANSITION
==================================================

Audit hubungan payment status dengan order status.

Jika payment berhasil:

Payment
→ PAID

Order
→ status sukses/paid yang sesuai existing architecture.

Jika payment gagal/expired:

Order hanya boleh berubah jika transition tersebut memang valid menurut order-service.

Jangan bypass centralized order transition logic.

==================================================
4. ATOMIC UPDATE
==================================================

Jika webhook/status reconciliation mengubah:

- payment
- order

pastikan perubahan dilakukan secara atomic menggunakan Prisma transaction jika diperlukan.

Jangan membuat:

payment berhasil
tetapi order gagal diperbarui.

Jika transaction gagal:

jangan mengembalikan success palsu.

==================================================
5. IDEMPOTENCY TANPA MIGRATION
==================================================

Karena schema tidak boleh berubah:

gunakan field existing yang memang tersedia untuk membantu mencegah duplicate processing.

Prioritaskan:

- payment ID
- order ID
- provider reference
- PaymentWebhookEvent unique key jika memang sudah ada.

Jangan membuat migration baru.

Jangan menambah kolom hanya untuk idempotency.

Jika full provider-event idempotency benar-benar membutuhkan:

providerEventId

atau dedicated event table:

JANGAN IMPLEMENT MIGRATION.

Cukup:

1. dokumentasikan kebutuhan tersebut;
2. gunakan protection yang bisa dilakukan dengan schema existing;
3. lanjutkan bagian lain yang tidak membutuhkan schema change.

==================================================
6. DUPLICATE WEBHOOK
==================================================

Buat test:

Webhook A
→ payment PENDING → PAID

Webhook A dikirim lagi.

Expected:

- tidak membuat payment baru;
- tidak membuat order baru;
- tidak menurunkan status;
- tidak menggandakan side effect;
- response tetap aman.

Gunakan data mock.

Jangan kirim webhook ke provider nyata.

==================================================
7. OUT-OF-ORDER WEBHOOK
==================================================

Test sequence:

PAID
↓
PENDING

Expected:

PENDING tidak boleh menurunkan PAID.

Test:

FAILED
↓
PAID

Tentukan apakah transition tersebut valid berdasarkan existing order/payment architecture.

Jangan mengarang business rule baru.

Jika ambiguous:

ikuti state transition logic existing dan laporkan.

==================================================
8. STATUS RECONCILIATION
==================================================

Audit status polling yang sudah tersedia.

Pastikan reconciliation juga menggunakan centralized transition logic.

Jangan membuat polling mempunyai aturan status sendiri.

Flow harus:

provider status
→ provider mapper
→ internal payment status
→ payment transition
→ order transition

bukan:

provider status
→ langsung update database.

==================================================
9. CONCURRENT REQUEST
==================================================

Audit kemungkinan dua proses berjalan bersamaan:

Webhook
+
Status polling

keduanya mencoba mengubah payment yang sama.

Pastikan hasil akhir konsisten.

Jangan menggunakan global lock yang dapat menghambat seluruh payment system.

Gunakan transaction/database-safe logic yang sesuai dengan schema existing.

Jika race condition tidak dapat sepenuhnya diselesaikan tanpa migration:

jangan mengubah schema.

Dokumentasikan limitation.

==================================================
10. CREATE PAYMENT DUPLICATION
==================================================

Audit kemungkinan user menekan:

"Bayar"

dua kali.

Jangan otomatis membuat dua payment provider session jika order masih memiliki payment yang valid.

Gunakan payment/order record existing untuk menentukan apakah payment existing masih dapat digunakan.

Jika existing architecture memang membuat payment baru untuk retry:

pastikan retry tersebut tidak membuat order baru.

Jangan mengubah business rule tanpa alasan.

==================================================
11. OWNERSHIP
==================================================

Pastikan semua payment operation customer memvalidasi:

- authenticated user
- order ownership
- payment ownership/order relation.

User A tidak boleh:

- melihat payment User B;
- polling payment User B;
- memicu payment User B;
- memanipulasi status User B.

Admin flow tetap mengikuti authorization existing.

==================================================
12. AMOUNT INTEGRITY
==================================================

Pastikan idempotency tidak membuka celah amount manipulation.

Amount harus tetap berasal dari:

Order database

bukan:

request body.

Test:

client amount = 1000
database amount = 50000

Expected:

server menggunakan 50000.

Jangan percaya:

amount
price
total
currency

dari client jika database sudah memiliki authoritative value.

==================================================
13. WEBHOOK SECURITY
==================================================

Pertahankan:

- exact path;
- POST only;
- signature validation;
- payload validation;
- provider validation.

Pastikan duplicate/idempotency handling dilakukan SETELAH request lolos validation/security yang diperlukan.

Jangan menjadikan idempotency sebagai bypass authentication/signature validation.

==================================================
14. SIDE EFFECT SAFETY
==================================================

Audit apakah payment success memicu:

- order update;
- stock reduction;
- notification;
- invoice;
- callback lain.

Jangan menjalankan side effect dua kali akibat duplicate webhook.

Jika side effect belum diimplementasikan:

JANGAN membuatnya sekarang.

Cukup pastikan payment/order state layer siap.

==================================================
15. TEST MATRIX
==================================================

Buat test menggunakan mock/stub.

Minimal:

A. PENDING → PAID
B. PENDING → FAILED
C. PENDING → EXPIRED jika tersedia
D. PAID → PENDING harus ditolak
E. Duplicate PAID callback
F. Duplicate FAILED callback
G. Out-of-order callback
H. Webhook + polling race simulation
I. Double create-payment request
J. Unauthorized payment access
K. Cross-user payment access
L. Amount manipulation
M. Invalid provider status
N. Invalid webhook signature
O. Invalid payload
P. Provider configuration missing

Tidak boleh menggunakan credential production.

Tidak boleh membuat transaksi nyata.

==================================================
16. NO SCHEMA CHANGE
==================================================

WAJIB:

Jangan menjalankan:

- prisma migrate dev
- prisma migrate deploy
- prisma migrate reset
- prisma db push
- DROP DATABASE
- migration baru.

Jangan mengubah:

app/prisma/schema.prisma

Jika ditemukan kebutuhan schema untuk idempotency penuh:

STOP pada bagian tersebut dan dokumentasikan:

- field yang dibutuhkan;
- alasan;
- lokasi penggunaan;
- risiko jika belum ada.

Tetapi lanjutkan test dan hardening yang bisa dilakukan tanpa schema change.

==================================================
17. BUILD & VERIFICATION
==================================================

Jalankan:

npm run typecheck
npm run build

Kemudian jalankan test payment yang relevan.

Jika server development sedang aktif:

jangan menghentikan process yang bukan bagian task.

Jangan mengubah:

- Cloudflare
- DNS
- tunnel
- port
- deployment configuration.

==================================================
18. GIT
==================================================

JANGAN:

git commit
git push

Jangan reset worktree.

Jangan menghapus perubahan existing.

Di akhir tampilkan:

git status --short

==================================================
19. FINAL REPORT
==================================================

Laporkan:

A. Existing payment states
B. Existing order states
C. State transition rules
D. Idempotency mechanism yang berhasil diterapkan
E. Duplicate webhook handling
F. Concurrent webhook/polling handling
G. Double create-payment handling
H. Ownership protection
I. Amount integrity
J. Test results
K. Files changed
L. Schema status
M. Remaining limitations

Khusus Remaining Limitations:

Jika full provider-event idempotency masih membutuhkan schema field/event table, jelaskan secara eksplisit.

Jangan mengklaim idempotency FULL jika sebenarnya hanya menggunakan existing payment/order/reference protection.

==================================================
STOP CONDITION
==================================================

Setelah selesai:

- jangan commit;
- jangan push;
- jangan migration;
- jangan transaksi nyata;
- jangan production credential;
- jangan ubah Cloudflare;
- jangan ubah DNS;
- jangan ubah port.

Berhenti dan tampilkan laporan hasil verifikasi lengkap.
```
# Prompt: Integrasi Payment Adapter ke Core
```
PROMPT — INTEGRATE PAYMENT ADAPTER INTO CORE PAYMENT FLOW

Project: Digital Cell / toko-online

STATUS SAAT INI

Payment foundation dan adapter Midtrans sudah diaudit.

Hasil tahap sebelumnya:

- Adapter Midtrans sudah tersedia.
- Test adapter sudah ditambahkan.
- Verifikasi adapter berhasil.
- Tidak ada API key Midtrans production.
- Tidak ada transaksi payment nyata.
- Tidak ada perubahan database/migration.
- Cancel/refund nyata belum diimplementasikan.
- Create-session dan status-polling adapter belum dipanggil dari core.
- Legacy webhook repository masih memiliki implementation tertentu dan harus tetap diaudit sebelum digunakan.
- Project masih development-only.
- Database Prisma/PostgreSQL tetap menjadi sumber data utama.
- npm run typecheck/build sebelumnya berhasil.

TUJUAN TAHAP INI

Hubungkan payment adapter dengan core payment service secara modular TANPA melakukan transaksi payment nyata.

Arsitektur target:

Frontend
  ↓
Payment API
  ↓
Payment Service
  ↓
Payment Provider Adapter
  ↓
Midtrans Adapter
  ↓
[DEVELOPMENT / NO REAL API CALL]

Jangan langsung mengaktifkan payment production.

==================================================
1. AUDIT SEBELUM CODING
==================================================

Audit terlebih dahulu:

- payment-service
- payment-provider interface
- Midtrans adapter
- payment API
- order-service
- payment model Prisma
- AppSettings
- existing webhook
- existing payment UI.

Cari apakah sudah ada:

- create payment
- payment session
- status polling
- provider selection
- provider configuration
- webhook processing.

Jangan membuat duplicate abstraction jika interface sudah tersedia.

==================================================
2. PROVIDER INTERFACE
==================================================

Jika belum ada abstraction yang tepat, buat interface modular.

Konsep:

PaymentProviderAdapter

dengan operasi yang memang dibutuhkan core.

Contoh konsep:

- createPayment
- getPaymentStatus
- handleWebhook

Tetapi gunakan nama/type sesuai arsitektur existing.

Jangan membuat interface terlalu besar.

Jangan menambahkan:

refund
cancel
settlement
dispute

jika belum dibutuhkan core.

==================================================
3. MIDTRANS ADAPTER
==================================================

Hubungkan Midtrans adapter ke interface existing.

Adapter bertanggung jawab atas:

- request formatting
- response mapping
- provider-specific status mapping
- signature validation
- provider error normalization.

Core payment service TIDAK boleh mengetahui detail API Midtrans.

Jangan menaruh kode Midtrans langsung di:

- order-service
- checkout route
- frontend
- generic payment service.

==================================================
4. PROVIDER SELECTION
==================================================

Payment service harus dapat menentukan provider secara terkontrol.

Jangan percaya provider dari client tanpa validasi.

Jika provider berasal dari database/settings:

ambil dari server.

Jika provider belum dikonfigurasi:

payment harus tetap aman dan tidak melakukan API call nyata.

Jangan fallback diam-diam ke provider lain.

==================================================
5. DEVELOPMENT SAFETY
==================================================

WAJIB.

Karena environment sekarang DEVELOPMENT:

Jangan melakukan HTTP request ke Midtrans production.

Jangan menggunakan:

- MIDTRANS_SERVER_KEY nyata
- MIDTRANS_CLIENT_KEY nyata
- production merchant credential
- production payment session.

Jika adapter membutuhkan credential:

gunakan configuration validation dan fail-safe.

Jika credential tidak tersedia:

return error/configuration state yang jelas.

Jangan membuat fake "PAID".

==================================================
6. CREATE PAYMENT FLOW
==================================================

Integrasikan flow:

POST payment
 ↓
authenticate
 ↓
load order
 ↓
verify ownership
 ↓
verify order payable
 ↓
calculate amount from DB
 ↓
create local payment record
 ↓
resolve provider
 ↓
provider adapter
 ↓
return safe payment response.

Payment amount HARUS berasal dari database order.

Jangan menerima amount dari frontend sebagai sumber kebenaran.

==================================================
7. PAYMENT RECORD
==================================================

Pastikan local payment dibuat/di-update secara aman.

Gunakan Prisma transaction jika diperlukan.

Payment record harus tetap konsisten dengan:

- order
- amount
- currency jika schema memilikinya
- provider
- status
- external reference jika tersedia.

Jangan mengubah Prisma schema tanpa approval.

Jika ada kebutuhan field baru:

STOP dan laporkan.

==================================================
8. PROVIDER RESPONSE MAPPING
==================================================

Jangan expose raw Midtrans response langsung ke frontend.

Buat normalized internal response.

Contoh konsep:

{
  provider,
  paymentId,
  status,
  redirectUrl,
  reference
}

Gunakan field yang benar-benar tersedia di project.

Jangan expose:

- server key
- secret
- internal signature
- raw authorization header.

==================================================
9. PAYMENT STATUS
==================================================

Integrasikan status provider ke internal payment status.

Contoh:

provider pending
→ local PENDING

provider success
→ local PAID

provider failed
→ local FAILED

Tetapi gunakan enum/status yang memang sudah tersedia.

Jangan membuat status baru hanya karena nama status Midtrans berbeda.

Buat mapper khusus provider.

==================================================
10. STATUS POLLING
==================================================

Adapter memiliki status-polling capability.

Tetapi core hanya boleh memanggilnya jika:

- payment valid
- ownership valid
- provider valid
- payment masih berada pada state yang dapat diperiksa.

Jika polling membutuhkan credential dan credential tidak tersedia:

jangan melakukan request.

Return configuration error yang aman.

Jangan membuat infinite polling.

==================================================
11. WEBHOOK
==================================================

Audit webhook existing sebelum menghubungkannya ke adapter.

Pastikan webhook:

- exact path
- POST only
- payload validation
- provider validation
- signature validation
- payment lookup
- amount/reference validation jika tersedia
- transaction saat update payment + order.

Jangan membuat catch-all webhook.

Jangan menghapus implementation legacy sebelum memahami dependencynya.

Jika legacy webhook masih digunakan:

integrasikan secara bertahap.

==================================================
12. WEBHOOK IDEMPOTENCY
==================================================

Jika schema existing memiliki external transaction ID/event ID:

gunakan untuk mencegah duplicate callback.

Jika tidak ada field tersebut:

jangan membuat migration.

Gunakan state transition protection yang sudah tersedia.

Jika idempotency penuh membutuhkan schema change:

STOP dan laporkan kebutuhan migration.

==================================================
13. ORDER UPDATE
==================================================

Jika payment provider mengkonfirmasi sukses:

update payment dan order dalam transaction.

Jangan:

payment = PAID
tetapi order tetap state yang tidak konsisten.

Gunakan order transition validator existing.

Jangan bypass order-service jika project memang sudah memiliki centralized order transition logic.

==================================================
14. CUSTOMER PAYMENT API
==================================================

Audit endpoint payment customer.

Pastikan:

- authentication
- ownership
- validation
- no client-side amount trust
- no arbitrary status update
- no arbitrary provider selection.

Request seperti:

{
  "status": "PAID"
}

harus ditolak jika berasal dari customer.

Customer tidak boleh mengonfirmasi pembayaran sendiri.

==================================================
15. ADMIN
==================================================

Jika admin membutuhkan payment information:

gunakan service existing.

Admin dapat melihat:

- order
- payment
- amount
- provider
- status
- reference
- timestamps.

Jangan expose secret provider.

Jangan menambahkan refund system.

==================================================
16. FRONTEND
==================================================

Jangan redesign UI.

Jika payment page sudah ada:

hubungkan ke normalized payment response.

Pastikan frontend tidak menganggap:

HTTP 200 create payment = payment sukses.

HTTP 200 hanya berarti request berhasil diproses.

Status payment harus berasal dari backend.

==================================================
17. TEST MODE
==================================================

Buat test untuk:

A. Provider interface
B. Midtrans adapter
C. Provider status mapping
D. Invalid configuration
E. Invalid credential state
F. Payment amount integrity
G. Ownership protection
H. Invalid status transition
I. Webhook validation
J. Duplicate webhook handling jika dapat dilakukan tanpa schema change
K. No-secret-leak verification.

Semua test harus menggunakan:

mock HTTP/client
atau
credential stub.

Jangan menggunakan credential Midtrans nyata.

Jangan melakukan HTTP request ke Midtrans production.

==================================================
18. NEGATIVE TEST
==================================================

Wajib test:

1. User tidak login.
2. User mencoba payment order milik user lain.
3. Amount dimanipulasi.
4. Provider tidak tersedia.
5. Provider configuration kosong.
6. Invalid payment state.
7. Invalid webhook signature.
8. Invalid webhook payload.
9. Webhook endpoint dengan method GET.
10. Webhook extra path.
11. Duplicate callback.
12. Provider mengembalikan unknown status.
13. Payment sudah PAID tetapi callback pending datang lagi.

Pastikan tidak ada state regression.

==================================================
19. NO DATABASE MIGRATION
==================================================

JANGAN:

- prisma migrate reset
- DROP DATABASE
- delete migration
- membuat migration baru
- seed payment dummy
- mengubah schema Prisma.

Jika integrasi membutuhkan perubahan schema:

STOP.

Laporkan field yang diperlukan dan alasan.

==================================================
20. NO PRODUCTION PAYMENT
==================================================

PENTING:

Tahap ini hanya integrasi kode.

DILARANG:

- transaksi Midtrans nyata
- API production nyata
- credential production
- charge customer
- settlement nyata
- refund nyata.

Midtrans hanya boleh diuji menggunakan mock/stub.

==================================================
21. CODE QUALITY
==================================================

Pastikan:

- modular
- provider-agnostic core
- no duplicated payment logic
- no PrismaClient per request
- no secret logging
- no raw provider response leakage
- no hardcoded credentials
- no hardcoded production URL
- no client-side authorization.

Jangan membuat satu file payment raksasa.

==================================================
22. VERIFICATION
==================================================

Jalankan:

npm run typecheck

npm run build

Kemudian test endpoint yang aman tanpa transaksi nyata.

Verifikasi:

- payment creation logic
- ownership
- amount integrity
- provider adapter
- status mapping
- webhook validation.

Jika runtime sedang aktif:

jangan mematikan process yang bukan milik task ini.

Jangan mengubah:

- Cloudflare
- DNS
- tunnel
- port
- production configuration.

==================================================
23. GIT
==================================================

Jangan:

git commit

git push

Jangan menghapus perubahan worktree yang sudah ada.

Setelah selesai tampilkan:

git status --short

Tetapi jangan commit.

==================================================
24. FINAL REPORT
==================================================

Berikan laporan:

A. Provider architecture
B. Payment core integration
C. Midtrans adapter integration
D. Payment lifecycle
E. Webhook integration
F. Security
G. Test results
H. Database/schema status
I. Files changed
J. Remaining work.

Khusus Remaining Work jelaskan:

- production credentials
- real Midtrans API activation
- real payment testing
- cancel/refund
- create-session jika belum terhubung
- status polling jika belum terhubung
- idempotency migration jika memang dibutuhkan.

Jangan mengklaim fitur sebagai selesai jika sebenarnya belum di-wire ke core.

SETELAH SELESAI:

Berhenti.

Jangan commit.
Jangan push.
Jangan melakukan transaksi nyata.
Jangan membuat migration.
Jangan mengubah Cloudflare/DNS/port.

Laporkan hasil lengkap untuk diverifikasi sebelum tahap berikutnya.


```
# 
```
PROMPT: Implementasi Adapter Midtrans

Lanjutkan project toko-online dari hasil audit provider terakhir.

Fokus tahap ini HANYA pada IMPLEMENTASI ADAPTER MIDTRANS di atas provider abstraction yang sudah dibuat.

Kondisi terakhir:
- Payment expiry scheduler production AKTIF dan jangan diubah.
- Payment expiry worker PASS.
- Webhook idempotency PASS.
- Create payment idempotency sudah tersedia.
- Provider abstraction foundation sudah dibuat.
- Network boundary/mock provider sudah tersedia.
- Build/typecheck/lint sebelumnya PASS.
- Gap terakhir:
  - belum ada adapter Midtrans;
  - registry production masih kosong;
  - credential production belum dihubungkan;
  - network request production belum digunakan;
  - cancellation/refund nyata belum tersedia;
  - durable provider operation store belum tersedia.
- Jangan mengubah systemd scheduler.

TUJUAN:
Implementasikan adapter Midtrans secara modular menggunakan contract/provider abstraction yang sudah ada, tanpa mengubah core payment lifecycle.

TUGAS:

1. AUDIT PROVIDER FOUNDATION
- Baca implementation provider contract, registry, credential abstraction, network client, webhook normalization, dan mock provider yang baru dibuat.
- Jangan membuat abstraction kedua jika yang existing sudah sesuai.
- Gunakan struktur project existing.

2. ADAPTER MIDTRANS
Buat adapter Midtrans yang mengimplementasikan contract existing.

Minimal siapkan:
- create payment;
- get/check transaction status;
- webhook verification/parsing;
- mapping status Midtrans → internal payment status;
- mapping error Midtrans → internal provider error.

Cancellation/refund:
- jika contract/capability sudah tersedia tetapi belum aman untuk production, tandai unsupported.
- JANGAN membuat fake refund/cancel success.

3. CREDENTIAL
Gunakan environment/config abstraction existing.

Credential Midtrans harus:
- tidak hardcode;
- tidak masuk Git;
- tidak muncul di log;
- dapat dibedakan antara sandbox/production jika architecture existing mendukung.

Jangan meminta atau memasukkan credential nyata.

4. NETWORK
Gunakan network boundary yang sudah dibuat.
Jangan langsung menggunakan fetch/axios dari core payment service.

Pastikan:
- timeout;
- HTTP error;
- network error;
- response parsing;
- provider error mapping.

5. REGISTRY
Daftarkan Midtrans pada provider registry existing.

Behavior:
- provider ID/name jelas;
- adapter dapat ditemukan registry;
- capability tersedia;
- provider disabled/credential tidak tersedia menghasilkan error yang jelas;
- jangan fallback diam-diam ke provider lain.

6. WEBHOOK
Hubungkan webhook Midtrans ke normalized internal event.

Pastikan:
- signature/security verification menggunakan abstraction existing;
- order/transaction identifier dipetakan dengan benar;
- status event dipetakan ke internal state;
- duplicate webhook tetap aman menggunakan idempotency existing;
- event tidak boleh mengubah terminal state secara ilegal.

Jangan merusak legacy webhook parsing yang sudah ada.

7. CORE PAYMENT
Integrasikan adapter hanya melalui provider abstraction.

Core payment service TIDAK boleh:
- import SDK Midtrans langsung;
- mengetahui detail response Midtrans;
- mengetahui credential Midtrans;
- memiliki logic status Midtrans.

8. SDK / DEPENDENCY
Jika project sudah memiliki dependency Midtrans, gunakan yang existing.

Jika belum:
- audit apakah SDK benar-benar diperlukan;
- jangan menambahkan dependency besar tanpa alasan;
- jika HTTP API existing sudah cukup, gunakan network abstraction yang sudah tersedia.

9. TEST MOCK
Jangan melakukan transaksi Midtrans nyata.

Tambahkan test menggunakan mock HTTP/provider untuk:
- create payment success;
- create payment error;
- status pending;
- status paid;
- status expired/cancelled sesuai mapping yang valid;
- invalid response;
- network timeout;
- HTTP error;
- invalid webhook;
- valid webhook;
- duplicate webhook;
- unsupported refund/cancel jika belum tersedia.

10. SECURITY
Pastikan:
- secret tidak masuk log;
- secret tidak masuk test fixture;
- signature verification tidak dilewati;
- jangan membuat mode "always valid" di production;
- jangan hardcode sandbox credential.

11. PRODUCTION SAFETY
JANGAN:
- melakukan transaksi payment nyata;
- menggunakan API key nyata;
- mengubah database production;
- mengubah migration existing kecuali benar-benar diperlukan;
- mengubah systemd timer/service;
- restart/kill web process;
- mengubah port;
- mengubah frontend;
- mengubah expiry worker;
- mengubah create idempotency;
- commit;
- push GitHub;
- membuat README baru.

12. VERIFICATION
Jalankan:
- seluruh payment tests;
- Midtrans adapter tests;
- webhook regression tests;
- create idempotency tests;
- expiry tests;
- typecheck;
- lint;
- build;
- git diff --check.

Warning existing:
src/components/ui/select.tsx
jangan disentuh.

13. LAPORAN AKHIR

Tampilkan:
1. File adapter Midtrans yang dibuat/diubah.
2. Contract yang digunakan.
3. Registry yang digunakan.
4. Credential source tanpa menampilkan secret.
5. Endpoint/API abstraction yang digunakan.
6. Mapping status Midtrans → internal status.
7. Mapping webhook.
8. Capability cancel/refund.
9. Test yang ditambahkan dan hasilnya.
10. Typecheck/lint/build/git diff --check.
11. Gap provider yang masih tersisa.
12. Konfirmasi systemd payment expiry scheduler tetap AKTIF dan tidak diubah.
13. Konfirmasi TIDAK ada transaksi Midtrans nyata.
14. Jangan commit atau push.

BERHENTI setelah adapter Midtrans dan seluruh verification selesai.


```
# belum mengintegrasikan provider nyata
```
Lanjutkan project toko-online dari kondisi terakhir.

Fokus tahap ini: IMPLEMENTASI PAYMENT PROVIDER ABSTRACTION FOUNDATION berdasarkan hasil audit terakhir.

Kondisi saat ini:
- Payment expiry scheduler production SUDAH AKTIF dan berjalan normal.
- Systemd timer payment expiry tetap aktif.
- Payment expiry worker sudah selesai dan teruji.
- Webhook idempotency sudah selesai.
- Create payment idempotency sudah ditangani.
- Typecheck/build/lint sebelumnya PASS dengan warning ARIA existing di src/components/ui/select.tsx.
- Payment lifecycle dasar sudah tersedia.
- Audit terakhir menemukan gap utama pada provider abstraction:
  - belum ada adapter Midtrans/Xendit/provider lain;
  - registry provider masih kosong;
  - credential provider belum diabstraksikan;
  - network request boundary belum tersedia;
  - provider cancellation belum tersedia;
  - provider refund belum tersedia;
  - belum ada outbox/Saga/durable provider operation record;
  - belum ada mapping webhook provider-specific untuk lifecycle baru;
  - provider nyata belum digunakan.

TUJUAN:
Bangun FOUNDATION provider abstraction yang modular dan aman sehingga provider nyata seperti Midtrans/Xendit dapat ditambahkan kemudian tanpa mengubah core payment lifecycle.

PENTING:
JANGAN mengintegrasikan provider payment nyata pada tahap ini.
JANGAN menggunakan credential/API key provider nyata.
JANGAN melakukan transaksi uang nyata.
JANGAN mengubah systemd scheduler yang sudah aktif.

1. AUDIT ARSITEKTUR EXISTING

Cari dan pahami:
- model Payment;
- model Order;
- payment service;
- payment repository;
- webhook handler;
- create payment flow;
- payment status/state transition;
- konfigurasi/environment provider yang sudah ada;
- struktur module/service existing.

Jangan melakukan refactor besar jika tidak diperlukan.

2. BUAT PROVIDER CONTRACT

Buat interface/type contract modular untuk payment provider.

Minimal konsep operation:
- create payment;
- get/check payment status;
- cancellation jika provider mendukung;
- refund jika provider mendukung;
- webhook verification/parsing.

Gunakan capability-based design jika cocok sehingga provider yang tidak mendukung refund/cancel tidak dipaksa mengimplementasikan method palsu.

Core payment domain tidak boleh bergantung langsung kepada SDK provider tertentu.

3. BUAT PROVIDER ADAPTER LAYER

Buat struktur modular, misalnya:

payment/
  providers/
    core/
    registry/
    adapters/

Gunakan nama folder/file yang mengikuti struktur project existing jika berbeda.

Adapter harus bertanggung jawab atas:
- request ke provider;
- mapping response provider ke internal payment model;
- mapping provider error ke internal error;
- credential/config abstraction;
- webhook event mapping.

Jangan menaruh logic provider-specific langsung di controller payment.

4. PROVIDER REGISTRY

Implementasikan registry/factory provider.

Contoh konsep:
- provider name/id;
- provider adapter;
- capabilities;
- enabled/disabled state.

Core payment service cukup meminta provider berdasarkan identifier.

Jika provider belum tersedia:
- return error internal yang jelas;
- jangan crash;
- jangan fallback diam-diam ke provider lain.

Registry jangan menggunakan hardcoded credential.

5. CREDENTIAL ABSTRACTION

Buat abstraction untuk credential provider.

Credential dapat berasal dari environment/configuration existing.

Ketentuan:
- jangan menampilkan secret di log;
- jangan hardcode API key;
- jangan menyimpan secret dalam source code;
- jangan membuat credential provider nyata;
- jangan mengubah production secret.

Untuk tahap ini cukup buat interface/config abstraction dan mock/test provider.

6. NETWORK BOUNDARY

Buat abstraction untuk HTTP/network client yang digunakan adapter.

Tujuannya:
- provider adapter tidak langsung bergantung kepada global fetch/axios tanpa boundary;
- mudah dibuat mock;
- timeout dapat dikontrol;
- error network dapat dipetakan secara konsisten.

Minimal dukung:
- request;
- timeout;
- response status;
- response body;
- network error.

Jangan melakukan request ke provider nyata.

7. MOCK PROVIDER

Buat mock/fake provider untuk testing.

Mock provider harus dapat mensimulasikan:
- create success;
- create failure;
- status success;
- status pending;
- cancellation unsupported;
- refund unsupported;
- webhook event.

Jangan menggunakan provider payment nyata.

8. PROVIDER OPERATION RECORD

Audit apakah payment provider operation membutuhkan durable record.

Jika memang diperlukan untuk menjaga retry/idempotency:
- buat abstraction/model/service yang modular;
- gunakan stable operation key;
- jangan menggunakan in-memory state;
- jangan membuat schema besar yang belum diperlukan.

Jika database schema baru benar-benar diperlukan:
- buat migration non-destructive;
- jangan reset database;
- jangan menghapus data existing.

Jangan membuat outbox/Saga kompleks jika belum diperlukan oleh current scope. Cukup siapkan abstraction/foundation yang jelas.

9. CANCELLATION DAN REFUND

Jangan membuat refund provider nyata.

Buat contract/capability saja jika diperlukan.

Behavior harus jelas:
- provider yang tidak mendukung refund → PAYMENT_PROVIDER_OPERATION_UNSUPPORTED;
- provider yang tidak mendukung cancellation → PAYMENT_PROVIDER_OPERATION_UNSUPPORTED;
- jangan pura-pura sukses.

Core state machine tidak boleh menganggap refund berhasil hanya karena adapter belum tersedia.

10. WEBHOOK ABSTRACTION

Buat provider webhook abstraction yang dapat:
- verify signature melalui adapter;
- parse provider event;
- menghasilkan internal normalized event;
- menjaga webhook idempotency existing.

Jangan merusak PaymentWebhookEvent atau mekanisme idempotency yang sudah ada.

Provider-specific event tidak boleh tersebar di business logic.

11. TESTING

Buat test minimal untuk:

Provider registry:
- provider ditemukan;
- provider tidak ditemukan;
- provider disabled;
- capability detection.

Mock provider:
- create success;
- create failure;
- status mapping;
- unsupported cancellation;
- unsupported refund.

Network boundary:
- success;
- HTTP error;
- timeout/network error.

Webhook:
- valid normalized event;
- invalid signature;
- unknown event;
- duplicate event tetap aman melalui idempotency existing.

Provider operation:
- operation key stabil;
- retry tidak membuat duplicate operation jika abstraction memang sudah digunakan.

12. INTEGRATION DENGAN CORE PAYMENT

Integrasikan foundation hanya pada titik yang memang diperlukan.

Core payment flow harus tetap:
CREATE
→ PENDING
→ provider operation
→ webhook/status update
→ PAID/COMPLETED
atau
→ EXPIRED/CANCELLED/FAILED sesuai state machine existing.

Jangan mengubah aturan expiry.

Jangan mengubah systemd timer.

Jangan mengubah frontend.

13. DATABASE

Sebelum membuat migration:
- audit schema existing;
- pastikan benar-benar diperlukan.

Jika tidak diperlukan, jangan membuat migration.

Jika diperlukan:
- migration harus non-destructive;
- jangan reset database;
- jangan mengubah data production;
- jangan menghapus kolom existing.

14. VERIFICATION

Jalankan:
- seluruh payment tests;
- provider abstraction tests;
- webhook regression tests;
- expiry tests;
- create idempotency tests;
- typecheck;
- lint;
- build;
- git diff --check.

Warning existing:
src/components/ui/select.tsx
jangan disentuh karena di luar scope dan tidak menyebabkan build gagal.

15. PRODUCTION SAFETY

JANGAN:
- stop systemd timer;
- restart systemd timer;
- disable scheduler;
- restart/kill web process;
- mengubah port;
- mengubah database production;
- menjalankan migration destructive;
- menghapus data;
- mengubah frontend;
- menggunakan credential provider nyata;
- melakukan transaksi payment nyata;
- commit;
- push GitHub;
- membuat README baru.

Jika menemukan bug pada payment lifecycle existing:
- perbaiki hanya jika benar-benar diperlukan agar provider abstraction aman;
- tambahkan regression test;
- jangan melakukan refactor besar tanpa alasan.

SETELAH SELESAI LAPORKAN:

1. Struktur provider abstraction yang dibuat.
2. Contract/interface yang dibuat.
3. Provider registry.
4. Credential abstraction.
5. Network boundary.
6. Mock provider.
7. Provider operation abstraction/record jika dibuat.
8. Webhook normalization.
9. File yang berubah.
10. Migration yang dibuat jika ada.
11. Test yang ditambahkan.
12. Hasil semua test.
13. Hasil typecheck.
14. Hasil lint.
15. Hasil build.
16. Hasil git diff --check.
17. Gap provider yang masih tersisa.
18. Konfirmasi systemd payment expiry scheduler tetap AKTIF dan tidak diubah.
19. Konfirmasi TIDAK ada provider payment nyata yang dipanggil.

JANGAN COMMIT.
JANGAN PUSH GITHUB.
BERHENTI SETELAH IMPLEMENTASI DAN VERIFIKASI SELESAI.


```
# 
```
Lanjutkan project toko-online dari kondisi terakhir.

Fokus tahap ini HANYA pada PAYMENT PROVIDER ADAPTER FOUNDATION / ABSTRAKSI PROVIDER.

Jangan mengintegrasikan provider payment nyata pada tahap ini.

Kondisi saat ini:
- Payment create idempotency sudah selesai.
- Webhook idempotency sudah selesai.
- Payment expiry worker sudah selesai.
- Production payment expiry scheduler/systemd timer sudah AKTIF dan berjalan normal.
- Jangan mengubah atau mematikan scheduler tersebut.
- Payment expiry tests PASS.
- Worker tests PASS.
- Webhook regression tests PASS.
- Typecheck/build sebelumnya PASS.
- Audit menemukan gap:
  - belum ada adapter Midtrans/Xendit/provider lain;
  - belum ada network call provider;
  - belum ada credential provider abstraction;
  - belum ada provider cancellation nyata;
  - belum ada provider refund nyata;
  - belum ada outbox/Saga untuk koordinasi remote side effect dengan local transaction;
  - payment provider registry production masih kosong.
- Jangan mengimplementasikan provider nyata pada tahap ini.

Tujuan:
Membuat fondasi abstraksi provider payment yang modular sehingga provider seperti Midtrans, Xendit, atau provider lain nantinya dapat ditambahkan tanpa mengubah core payment lifecycle.

TUGAS:

1. Audit arsitektur payment saat ini.
   Temukan:
   - model Payment;
   - model Order;
   - payment service;
   - create payment flow;
   - webhook flow;
   - expiry worker;
   - status/state transition;
   - repository/database layer;
   - konfigurasi provider jika sudah ada.

   Jangan mengubah behavior existing sebelum memahami flow.

2. Buat interface/provider contract yang bersifat generic.

   Minimal dukung operasi konseptual:
   - create/initiate payment;
   - get payment/status;
   - cancellation;
   - refund.

   Gunakan interface/type yang tidak bergantung pada Midtrans, Xendit, atau provider tertentu.

   Contoh konsep:
   PaymentProviderAdapter
   PaymentProviderRequest
   PaymentProviderResult
   PaymentProviderStatus
   PaymentProviderError

   Sesuaikan nama dengan arsitektur project existing.

3. Provider adapter harus modular.

   Struktur harus memungkinkan nantinya:
   - MidtransAdapter
   - XenditAdapter
   - Provider lain

   ditambahkan tanpa mengubah core payment service secara besar.

   Jangan membuat semua provider logic di satu file besar.

4. Buat provider registry/resolver.

   Registry harus dapat:
   - mencari provider berdasarkan provider code;
   - memastikan provider yang diminta terdaftar;
   - menolak provider yang tidak tersedia dengan error yang jelas;
   - tidak menyimpan credential provider secara hardcoded.

   Jangan membuat provider dummy seolah-olah provider nyata sudah aktif.

5. Credential abstraction.

   Buat abstraction/config contract untuk credential provider.

   Ketentuan:
   - credential tidak boleh hardcoded;
   - credential tidak boleh masuk Git;
   - credential tidak boleh ditampilkan dalam log;
   - credential harus dapat berasal dari environment/configuration yang sesuai deployment;
   - core payment service tidak boleh mengetahui detail secret provider.

   Jangan membuat atau memasukkan API key nyata.

6. Pisahkan core payment lifecycle dari provider adapter.

   Core payment harus hanya bergantung pada interface provider.

   Jangan membuat:
   if provider === "midtrans"
   if provider === "xendit"

   berulang di seluruh payment service.

   Provider-specific behavior harus berada di adapter masing-masing.

7. Status mapping.

   Buat mapping generic dari status provider ke status internal project.

   Pastikan:
   - pending tidak menjadi paid secara sembarangan;
   - success hanya menghasilkan transition yang valid;
   - failed/cancelled tidak dapat menghidupkan kembali payment terminal secara ilegal;
   - expired tetap mengikuti expiry worker;
   - refund/cancel tidak boleh mengubah state secara sembarangan.

   Jangan mengubah state machine existing jika tidak diperlukan.

8. Network boundary.

   Buat abstraction untuk network call provider tanpa melakukan HTTP request ke provider nyata.

   Jika diperlukan:
   - buat HTTP client interface;
   - timeout;
   - error normalization;
   - response validation;
   - jangan retry sembarangan untuk operasi yang memiliki side effect.

   Jangan memanggil API Midtrans/Xendit/provider nyata.

9. Idempotency untuk operasi provider.

   Pastikan desain adapter mendukung stable operation key/idempotency key.

   Minimal konsep harus dapat menangani:
   - create payment retry;
   - cancellation retry;
   - refund retry.

   Jangan menganggap retry HTTP otomatis aman.

10. Cancellation dan refund.

   Jangan mengimplementasikan remote cancellation/refund nyata.

   Hanya siapkan contract/interface dan state boundary.

   Jika flow cancellation/refund belum tersedia secara aman:
   - jangan membuat fake success;
   - jangan mengubah payment menjadi REFUNDED hanya karena method dipanggil;
   - return error/status yang jelas seperti unsupported/not implemented sesuai pola project.

11. Outbox/Saga.

   Audit apakah project sudah mempunyai outbox/event/job infrastructure.

   Jangan langsung membuat sistem Saga besar.

   Jika belum ada:
   - dokumentasikan boundary yang diperlukan untuk remote side effect;
   - jangan membuat database migration besar hanya untuk tahap ini;
   - jangan mengubah transaction architecture existing tanpa kebutuhan nyata.

   Pastikan desain tidak mengklaim transaksi database lokal dan HTTP provider sebagai satu atomic transaction.

12. Testing.

   Buat unit test untuk provider abstraction:

   - provider registry menemukan provider yang terdaftar;
   - provider yang tidak terdaftar ditolak;
   - credential tidak bocor ke log/error;
   - request contract tervalidasi;
   - response provider dapat dipetakan ke internal status;
   - unsupported cancellation ditangani dengan benar;
   - unsupported refund ditangani dengan benar;
   - stable operation/idempotency key diteruskan ke adapter;
   - provider error dinormalisasi;
   - timeout/error network dapat dipetakan dengan aman.

   Gunakan fake/mock adapter.

   JANGAN melakukan HTTP request ke provider nyata.

13. Regression verification.

   Pastikan perubahan tidak merusak:
   - create payment idempotency;
   - webhook idempotency;
   - payment expiry;
   - payment expiry worker;
   - order state;
   - authorization existing.

14. Jalankan verification:

   - seluruh test payment;
   - provider abstraction tests;
   - webhook regression tests;
   - expiry tests;
   - typecheck;
   - lint;
   - build;
   - git diff --check.

   Warning existing berikut jangan disentuh:
   src/components/ui/select.tsx

15. Production safety.

   PENTING:
   - Jangan mengubah systemd payment expiry timer.
   - Jangan disable scheduler.
   - Jangan restart/kill web process.
   - Jangan mengubah port.
   - Jangan reset database.
   - Jangan migration destructive.
   - Jangan mengubah frontend.
   - Jangan memanggil provider payment nyata.
   - Jangan memasukkan API key/provider credential.
   - Jangan membuat fake payment success.
   - Jangan membuat fake refund success.
   - Jangan commit.
   - Jangan push GitHub.
   - Jangan membuat README baru.

16. Arsitektur harus tetap modular.

   Provider adapter, registry, contract, credential abstraction, dan error mapping harus berada pada module/file yang jelas.

   Jangan membuat satu file payment besar yang menampung seluruh provider logic.

Setelah selesai tampilkan:

1. Arsitektur provider abstraction yang dibuat.
2. Interface/contract yang dibuat.
3. Provider registry/resolver.
4. Credential abstraction.
5. Status mapping.
6. Network boundary.
7. Cancellation/refund boundary.
8. Apakah outbox/Saga sudah tersedia atau masih menjadi gap.
9. File yang berubah.
10. Migration yang dibuat jika ada.
11. Test yang ditambahkan.
12. Hasil seluruh test.
13. Typecheck/lint/build/git diff --check.
14. Konfirmasi systemd payment expiry scheduler tetap aktif dan tidak diubah.
15. Gap yang masih tersisa sebelum provider nyata dapat diintegrasikan.

Berhenti setelah provider abstraction foundation selesai dan tervalidasi.

Jangan commit atau push.


```
# Prompt: Payment Provider Registry & Operation Boundary
```
Lanjutkan project toko-online dari hasil implementasi Payment Provider Cancellation & Refund Abstraction terakhir.

Fokus tahap ini HANYA pada PAYMENT PROVIDER REGISTRY dan OPERATION BOUNDARY.

Kondisi saat ini:
- Payment expiry scheduler production SUDAH AKTIF dan berjalan normal.
- Payment expiry worker sudah selesai.
- Create payment idempotency sudah selesai.
- Webhook idempotency sudah selesai.
- Cancellation/refund abstraction sudah dibuat.
- Provider nyata belum diintegrasikan.
- Belum ada adapter Midtrans/Xendit/provider nyata.
- Refund nyata belum tersedia.
- Cancellation provider nyata belum tersedia.
- Endpoint refund harus tetap menolak operation jika tidak ada provider operation yang tersedia.
- Jangan membuat fake provider success.

Tujuan:
Merapikan boundary antara core payment lifecycle dan provider adapter agar provider nyata nantinya dapat ditambahkan tanpa mengubah core payment logic.

Tugas:

1. Audit abstraction provider yang baru dibuat.
   - Temukan interface/type provider.
   - Temukan cancellation operation.
   - Temukan refund operation.
   - Temukan capability detection.
   - Temukan payment provider registry jika sudah ada.
   - Pastikan core payment service tidak mengetahui detail HTTP/API provider.

2. Buat atau rapikan provider registry modular.

Registry minimal harus dapat:
- register provider;
- mendapatkan provider berdasarkan provider ID;
- mengetahui capability provider;
- menolak provider yang tidak terdaftar;
- tidak membuat provider palsu.

Contoh capability:
- CREATE_PAYMENT
- CANCEL_PAYMENT
- REFUND_PAYMENT
- WEBHOOK

Gunakan type/interface yang sesuai architecture existing.
Jangan memaksakan nama tersebut jika project sudah mempunyai terminology sendiri.

3. Provider resolution:

Buat satu boundary yang jelas untuk:
- mencari provider berdasarkan payment.provider;
- memastikan provider terdaftar;
- memastikan operation yang diminta didukung;
- mengembalikan error yang konsisten jika provider/operation tidak tersedia.

Jika provider tidak terdaftar:
- jangan fallback diam-diam ke provider lain;
- jangan menjalankan operation;
- jangan mengubah status payment.

Jika capability tidak didukung:
- jangan menjalankan operation;
- jangan mengubah status payment menjadi sukses/refunded/cancelled;
- gunakan error seperti PAYMENT_PROVIDER_OPERATION_UNSUPPORTED atau equivalent existing.

4. Cancellation:

Pastikan flow:

PENDING
→ local cancellation hanya jika business rule mengizinkan.

Jika provider cancellation nyata belum tersedia:
- jangan mengklaim provider transaction telah cancelled;
- jangan membuat network request;
- jangan mengubah payment menjadi provider-cancelled secara palsu.

Jika nanti adapter provider tersedia:
- core service cukup memanggil abstraction;
- hasil provider menentukan transition berikutnya.

5. Refund:

Pastikan flow:

PAID/COMPLETED
→ request refund
→ resolve provider
→ cek capability REFUND_PAYMENT
→ jalankan adapter refund jika tersedia
→ hanya setelah provider benar-benar sukses, core boleh melakukan transition REFUNDED.

Jika provider belum tersedia:
- endpoint/service harus menolak operation dengan error yang jelas;
- payment tetap PAID/COMPLETED;
- jangan mengubah menjadi REFUNDED.

6. Jangan membuat:
- adapter Midtrans;
- adapter Xendit;
- adapter provider lain;
- API credential;
- HTTP request ke provider;
- fake provider;
- fake refund SUCCESS;
- fake cancellation SUCCESS.

Tahap ini hanya boundary/registry.

7. Idempotency operation:

Audit operation cancellation/refund.

Pastikan operation dapat mempunyai stable operation key/reference sehingga nanti provider side effect dapat dikaitkan dengan local operation.

Jangan membuat duplicate refund jika request yang sama diulang.

Jika architecture existing sudah mempunyai idempotency layer yang cukup:
- gunakan kembali;
- jangan membuat sistem kedua yang duplikat.

8. Error handling:

Gunakan error yang dapat dibedakan minimal untuk:
- provider tidak ditemukan;
- operation tidak didukung;
- provider belum dikonfigurasi;
- provider operation gagal;
- invalid payment state;
- duplicate operation.

Jangan membocorkan credential atau detail secret provider.

9. Testing:

Tambahkan test untuk:

- provider registry register;
- provider lookup berhasil;
- provider tidak ditemukan;
- capability CREATE_PAYMENT tersedia/tidak;
- capability CANCEL_PAYMENT tersedia/tidak;
- capability REFUND_PAYMENT tersedia/tidak;
- cancellation pada provider yang tidak tersedia;
- refund pada provider yang tidak tersedia;
- refund PAID tetap PAID ketika provider operation unsupported;
- cancellation PENDING tetap aman ketika provider operation unsupported;
- invalid payment state ditolak;
- duplicate refund operation tidak menghasilkan duplicate business effect;
- provider adapter mock dapat dipanggil melalui registry;
- core payment service tidak bergantung pada implementasi HTTP provider.

Gunakan mock adapter/interface saja.

10. Database:

Jangan membuat migration jika registry dapat dibuat tanpa perubahan schema.

Jika stable operation record benar-benar sudah dibutuhkan oleh architecture:
- gunakan model/table existing jika memungkinkan;
- migration harus non-destructive;
- jangan reset database;
- jangan mengubah data payment production.

11. Scheduler:

Jangan menyentuh:
- toko-online-payment-expiry.service;
- toko-online-payment-expiry.timer;
- scheduler configuration;
- worker expiry;
- web process.

Pastikan scheduler tetap aktif dan tidak berubah.

12. Jangan menyentuh:
- frontend;
- payment expiry logic;
- webhook idempotency;
- create payment idempotency;
- production database;
- port;
- deployment process.

Warning existing:
src/components/ui/select.tsx

Warning ARIA tersebut jangan diperbaiki karena di luar scope.

13. Verification:

Jalankan:
- seluruh payment tests yang relevan;
- cancellation tests;
- refund abstraction tests;
- provider registry tests;
- webhook regression tests;
- payment expiry tests;
- typecheck;
- lint;
- build;
- git diff --check.

14. Jangan commit atau push GitHub.

Setelah selesai tampilkan:

1. Struktur provider abstraction saat ini.
2. Provider registry yang dibuat/dirapikan.
3. Capability yang tersedia.
4. Cara provider resolution bekerja.
5. Behavior cancellation.
6. Behavior refund.
7. Error yang digunakan.
8. Test yang ditambahkan dan hasilnya.
9. File yang berubah.
10. Hasil typecheck/lint/build.
11. Konfirmasi payment expiry scheduler tetap ACTIVE dan tidak diubah.
12. Gap yang tersisa sebelum provider nyata dapat diintegrasikan.

PENTING:
Jangan mengintegrasikan provider nyata pada tahap ini.
Jangan membuat fake provider.
Jangan membuat fake refund.
Jangan mengklaim transaksi provider berhasil.
Jangan commit.
Jangan push GitHub.


```
# Prompt: Payment Provider Cancellation & Refund Abstraction
```


Lanjutkan project toko-online dari hasil audit Payment Flow terakhir.

Fokus tahap ini HANYA pada PAYMENT PROVIDER CANCELLATION/REFUND ABSTRACTION.

Jangan mengintegrasikan provider pembayaran nyata pada tahap ini.

Kondisi saat ini:
- Payment create idempotency sudah selesai.
- Webhook idempotency sudah selesai.
- Payment expiry worker sudah selesai.
- Payment expiry scheduler production aktif dan berjalan normal.
- Payment cancel flow lokal sudah tersedia.
- PENDING dapat dibatalkan secara aman.
- Payment yang sudah PAID/COMPLETED tidak boleh langsung menjadi CANCELLED.
- Refund provider nyata belum tersedia.
- Provider cancellation nyata belum tersedia.
- Jangan mengubah systemd payment expiry scheduler.

Tujuan:
Membuat abstraction/interface provider yang jelas untuk cancellation dan refund agar provider nyata dapat ditambahkan kemudian tanpa mengubah core payment lifecycle.

Tugas:

1. Audit architecture provider payment yang sekarang.
   - Cari type/interface provider.
   - Cari repository/service payment.
   - Cari create payment/session.
   - Cari webhook processing.
   - Cari cancel flow.
   - Cari status transition.
   - Jangan mengubah provider nyata karena belum tersedia.

2. Buat abstraction provider jika memang belum ada.

Minimal dukung konsep:
- create payment/session;
- cancel payment jika provider mendukung;
- refund payment jika payment sudah sukses dan provider mendukung.

Gunakan interface/type yang modular.

Jangan membuat implementasi provider palsu yang seolah-olah benar-benar melakukan refund.

3. Cancellation capability:

Provider harus dapat menyatakan apakah cancellation didukung.

Contoh konsep:
- supportsCancellation
- supportsRefund

Jangan hardcode behavior provider di core payment service.

Jika provider tidak mendukung cancellation:
- core payment tetap dapat melakukan local cancellation hanya jika business state memang masih PENDING;
- jangan mengklaim provider transaction sudah dibatalkan.

4. Refund capability:

Untuk payment PAID/COMPLETED:
- jangan mengubah payment menjadi REFUNDED hanya karena endpoint refund dipanggil;
- REFUNDED hanya boleh terjadi setelah provider refund berhasil, ketika provider nyata sudah tersedia.

Untuk sekarang:
- buat interface/service boundary;
- jangan melakukan network request;
- jangan menggunakan credential provider;
- jangan membuat fake SUCCESS refund.

5. State transition:

Pertahankan aturan existing:

PENDING → CANCELLED

PAID/COMPLETED → REFUNDED hanya setelah refund provider berhasil.

Jangan izinkan:

PAID → CANCELLED

COMPLETED → CANCELLED

REFUNDED → CANCELLED

CANCELLED → PAID

kecuali architecture existing memang memiliki aturan khusus yang sudah terbukti valid.

6. Webhook compatibility:

Pastikan abstraction tidak merusak webhook.

Jika webhook success datang setelah local cancellation:
- conditional lifecycle tetap berlaku;
- jangan mengubah payment secara ilegal;
- jangan menghapus idempotency protection.

Jika provider refund nantinya menghasilkan webhook:
- architecture harus dapat menerima event tersebut;
- tetapi jangan implementasikan provider nyata sekarang.

7. Testing:

Tambahkan test untuk:

- provider mendukung cancellation;
- provider tidak mendukung cancellation;
- provider mendukung refund;
- provider tidak mendukung refund;
- PENDING local cancellation tetap aman;
- PAID tidak berubah menjadi CANCELLED;
- COMPLETED tidak berubah menjadi CANCELLED;
- refund belum tersedia tidak menghasilkan REFUNDED;
- provider error tidak menghasilkan REFUNDED;
- provider success nantinya dapat menghasilkan REFUNDED melalui abstraction;
- duplicate refund request tidak boleh menghasilkan duplicate business effect jika idempotency layer existing dapat digunakan;
- webhook lifecycle tetap aman.

Gunakan mock provider/interface saja.

8. Jangan membuat:
- payment provider nyata;
- credential provider;
- HTTP call ke provider;
- refund uang nyata;
- cancellation transaksi nyata.

9. Jangan mengubah:
- frontend;
- payment expiry worker;
- systemd timer;
- webhook idempotency;
- create payment idempotency;
- database production;
- port;
- deployment process.

10. Migration:
- Jangan membuat migration jika abstraction dapat dibuat tanpa perubahan database.
- Jika perubahan database benar-benar diperlukan, jelaskan dahulu alasannya dan gunakan migration non-destructive.
- Jangan reset database.

11. Verification:

Jalankan:
- payment tests;
- cancel tests;
- refund abstraction tests;
- webhook regression tests;
- expiry tests;
- typecheck;
- lint;
- build;
- git diff --check.

Warning existing berikut jangan disentuh:

src/components/ui/select.tsx

12. Jangan commit atau push GitHub.

Setelah selesai tampilkan:

1. Provider interface/type yang dibuat.
2. Cancellation capability.
3. Refund capability.
4. Service yang menggunakan abstraction.
5. State transition yang didukung.
6. State transition yang ditolak.
7. Test yang ditambahkan dan hasilnya.
8. File yang berubah.
9. Hasil typecheck/lint/build.
10. Konfirmasi systemd payment expiry scheduler tetap aktif dan tidak diubah.
11. Gap yang masih tersisa untuk provider nyata.

PENTING:
Jangan membuat provider palsu.
Jangan mengklaim refund berhasil tanpa provider nyata.
Jangan mengubah payment menjadi REFUNDED hanya untuk membuat test PASS.
Jangan commit.
Jangan push GitHub.
```
# Prompt: Payment Cancel Flow
```

Lanjutkan project toko-online dari kondisi terakhir.

Fokus tahap ini HANYA pada PAYMENT CANCEL FLOW dan STATE TRANSITION.

Kondisi saat ini:
- Payment expiry scheduler production SUDAH AKTIF dan berjalan normal.
- Jangan mengubah, mematikan, restart, atau mengonfigurasi ulang scheduler tersebut.
- Payment expiry worker sudah selesai.
- Webhook idempotency sudah selesai.
- Create payment idempotency sudah dikerjakan.
- Payment expiry tests sebelumnya PASS.
- Worker tests sebelumnya PASS.
- Webhook regression tests sebelumnya PASS.
- Typecheck dan build sebelumnya PASS.
- Jangan mengerjakan refund provider nyata pada tahap ini.
- Jangan mengerjakan authorization integration test PostgreSQL pada tahap ini.

Tujuan:
Pastikan payment dapat dibatalkan secara aman tanpa merusak lifecycle payment dan tanpa race condition dengan webhook atau expiry worker.

TUGAS:

1. AUDIT PAYMENT STATE

Cari seluruh model, enum, service, repository, endpoint, dan helper yang berkaitan dengan payment/order status.

Identifikasi state yang benar-benar digunakan project, misalnya:
- PENDING
- PAID
- COMPLETED
- EXPIRED
- CANCELLED
- FAILED
- REFUNDED

Jangan mengarang state baru jika tidak diperlukan.

Buat mapping state transition yang benar-benar digunakan source code saat ini.

2. AUDIT CANCEL ENDPOINT/FLOW

Cari apakah sudah terdapat:
- cancel payment endpoint;
- cancel order endpoint;
- cancel service;
- repository method;
- admin cancel operation;
- user cancel operation.

Jika cancel flow sudah ada:
- audit implementasinya;
- jangan membuat endpoint duplicate.

Jika belum ada:
- implementasikan cancel flow minimal sesuai architecture existing.

3. ATURAN CANCEL

Payment yang masih PENDING dapat berubah menjadi:

PENDING → CANCELLED

Payment yang sudah:
- PAID
- COMPLETED
- EXPIRED
- REFUNDED

tidak boleh dibatalkan secara sembarangan.

Jika FAILED memang merupakan terminal state di project, jangan mengubahnya menjadi CANCELLED tanpa alasan bisnis yang jelas.

Gunakan state transition yang atomic dan berdasarkan database source of truth.

4. OWNERSHIP

Pastikan user hanya dapat membatalkan payment/order miliknya sendiri.

Jangan menggunakan payment ID/order ID sebagai satu-satunya security boundary.

Jika endpoint membutuhkan authentication:
- gunakan mekanisme authorization existing;
- jangan membuat sistem authentication baru.

Admin operation tetap mengikuti authorization admin existing.

5. RACE CONDITION

Pastikan cancel aman jika terjadi bersamaan dengan:

- webhook payment success;
- payment expiry worker;
- retry request cancel;
- duplicate cancel request.

Contoh kondisi:

Request A:
PENDING → CANCELLED

Request B:
PENDING → PAID

Tidak boleh menghasilkan state yang tidak konsisten.

Gunakan conditional database update atau transaction sesuai architecture Prisma/PostgreSQL existing.

Jangan menggunakan in-memory lock sebagai sumber kebenaran.

6. IDEMPOTENT CANCEL

Jika cancel request dikirim dua kali:

- request pertama berhasil CANCELLED;
- request kedua tidak boleh menghasilkan efek samping tambahan;
- jangan membuat error internal;
- response harus mengikuti behavior API existing yang paling konsisten.

Tidak boleh:
- membuat record baru;
- mengurangi stock dua kali;
- mengubah order dua kali;
- memicu efek provider dua kali.

7. ORDER CONSISTENCY

Jika cancel payment juga mengubah order:

- audit hubungan payment ↔ order;
- pastikan perubahan status order dan payment tidak meninggalkan partial state;
- gunakan transaction jika memang keduanya harus berubah atomically.

Jangan mengubah stock/balance kecuali memang sudah menjadi behavior existing yang diperlukan.

Jangan menambahkan business logic baru yang tidak diperlukan.

8. REFUND

Jangan mengimplementasikan provider refund nyata.

Jika payment sudah PAID/COMPLETED dan bisnis membutuhkan refund:
- jangan mengubahnya langsung menjadi CANCELLED;
- tampilkan bahwa refund masih merupakan gap jika memang belum tersedia.

Jangan menggunakan fake refund sebagai pengganti provider nyata.

9. TESTING

Tambahkan regression tests minimal untuk:

- PENDING → CANCELLED berhasil;
- CANCELLED → CANCELLED/retry aman;
- PAID tidak dapat dibatalkan;
- COMPLETED tidak dapat dibatalkan;
- EXPIRED tidak dapat dibatalkan;
- REFUNDED tidak dapat dibatalkan;
- FAILED mengikuti aturan state existing;
- user A tidak dapat membatalkan payment user B;
- duplicate cancel request aman;
- cancel concurrent dengan payment success aman;
- cancel concurrent dengan expiry worker aman;
- order/payment tidak mengalami partial update.

Jika PostgreSQL integration test infrastructure belum tersedia:
- jangan membuat infrastructure besar;
- gunakan test layer existing;
- verifikasi conditional update/transaction secara static;
- laporkan bahwa HTTP concurrency PostgreSQL belum dapat diverifikasi langsung.

10. TEST YANG HARUS DIJALANKAN

Jalankan:

- payment/cancel tests;
- payment expiry tests;
- webhook regression tests;
- create payment idempotency tests jika tersedia;
- typecheck;
- lint;
- build;
- git diff --check.

Jangan memperbaiki warning UI existing:

src/components/ui/select.tsx

kecuali warning tersebut menyebabkan test/build gagal.

11. BATASAN KERAS

JANGAN:
- mengubah systemd payment expiry scheduler;
- mematikan scheduler;
- restart/kill process web;
- mengubah port;
- mengubah frontend/UI;
- mengubah webhook idempotency kecuali benar-benar diperlukan untuk kompatibilitas cancel;
- mengubah expiry worker;
- mengintegrasikan payment provider nyata;
- mengintegrasikan refund provider;
- membuat authorization integration PostgreSQL besar;
- reset database;
- migration destructive;
- menghapus data production;
- commit;
- push GitHub;
- membuat README baru.

Pertahankan arsitektur modular project existing.

12. PERBAIKI HANYA JIKA DIPERLUKAN

Jika ditemukan bug pada cancel/state transition:
- perbaiki hanya bagian yang diperlukan;
- tambahkan regression test;
- jangan melakukan refactor besar;
- jangan mengubah behavior payment lain yang sudah PASS.

SETELAH SELESAI, TAMPILKAN:

1. State payment yang ditemukan.
2. State transition yang valid.
3. State transition yang ditolak.
4. Endpoint/service cancel yang digunakan.
5. Authorization/ownership yang diterapkan.
6. Cara race condition dicegah.
7. File yang berubah.
8. Test yang ditambahkan.
9. Hasil seluruh test.
10. Hasil typecheck.
11. Hasil lint.
12. Hasil build.
13. Hasil git diff --check.
14. Gap payment lifecycle yang masih tersisa.
15. Konfirmasi bahwa systemd payment expiry scheduler tetap aktif dan TIDAK DIUBAH.

Jangan commit atau push GitHub.

```
# Authorization Payment
```

Lanjutkan project toko-online dari hasil audit Payment Flow End-to-End terakhir.

Fokus tahap ini HANYA pada AUTHORIZATION DAN OWNERSHIP PAYMENT/ORDER.

Jangan mengubah systemd payment expiry scheduler yang sudah aktif.

Tujuan:
Pastikan user tidak dapat membaca atau mengubah payment/order milik user lain, dan endpoint admin/webhook tetap menggunakan authorization boundary yang benar.

Tugas:

1. Audit semua endpoint terkait:
   - create payment/order
   - get payment/order
   - payment status/session
   - cancel jika tersedia
   - admin payment operation jika tersedia
   - webhook provider

2. Audit ownership:
   - User A tidak boleh GET payment milik User B.
   - User A tidak boleh mengubah/cancel payment User B.
   - Payment ID/order ID saja tidak boleh menjadi security boundary.
   - Pastikan query database selalu mempertimbangkan authenticated user/owner sesuai architecture existing.

3. Audit authorization:
   - User biasa tidak boleh menjalankan admin-only operation.
   - Admin operation harus memiliki authorization check yang benar.
   - Webhook provider tidak boleh dipaksa melewati user authorization biasa.
   - Jangan mengubah authentication architecture secara besar.

4. Tambahkan integration/unit tests sesuai infrastructure existing:
   - User A dapat mengakses payment miliknya.
   - User B tidak dapat mengakses payment User A.
   - User B tidak dapat mengubah/cancel payment User A.
   - Anonymous request ditolak jika endpoint membutuhkan authentication.
   - User biasa ditolak dari admin operation.
   - Admin dapat menjalankan operation yang memang diizinkan.
   - Webhook tetap dapat diproses melalui mekanisme authentication/signature yang existing.

5. Jika integration test database penuh belum tersedia:
   - gunakan test/helper infrastructure existing;
   - jangan membuat PostgreSQL infrastructure besar;
   - jangan menggunakan database production;
   - jangan reset database development.

6. Jika ditemukan authorization bug:
   - perbaiki hanya bug yang diperlukan;
   - tambahkan regression test;
   - jangan refactor besar.

7. Jangan menyentuh:
   - frontend/UI;
   - payment expiry worker;
   - systemd scheduler;
   - webhook idempotency kecuali diperlukan untuk authorization compatibility;
   - payment provider nyata;
   - refund provider.

8. Jalankan:
   - authorization tests;
   - payment tests terkait;
   - webhook regression tests;
   - expiry tests;
   - typecheck;
   - lint;
   - build;
   - git diff --check.

Warning berikut jangan diperbaiki:
src/components/ui/select.tsx

PENTING:
- Jangan restart/kill web process.
- Jangan mengubah port.
- Jangan reset database.
- Jangan migration destructive.
- Jangan commit.
- Jangan push GitHub.
- Jangan membuat README baru.
- Systemd payment expiry scheduler harus tetap aktif dan tidak disentuh.

Setelah selesai tampilkan:
1. Endpoint yang diaudit.
2. Ownership rule yang diterapkan.
3. Authorization rule yang diterapkan.
4. Test yang ditambahkan.
5. Bug yang ditemukan/diperbaiki.
6. File yang berubah.
7. Hasil test/typecheck/lint/build.
8. Gap payment lifecycle yang masih tersisa.
9. Konfirmasi scheduler payment expiry tetap aktif.

Jangan commit atau push.

```
# Prompt: Idempotency Retention & Cleanup
```

Lanjutkan project toko-online dari hasil audit Payment Flow End-to-End terakhir.

Fokus tahap ini HANYA pada RETENTION DAN CLEANUP PAYMENT CREATE IDEMPOTENCY RECORD.

Kondisi saat ini:
- Duplicate create payment/order idempotency sudah diimplementasikan.
- Idempotency key sudah persisted di database.
- Duplicate request tidak membuat payment/order ganda.
- Concurrent duplicate request sudah memiliki protection.
- Webhook idempotency sudah selesai.
- Payment expiry worker sudah selesai.
- Production systemd payment expiry scheduler SUDAH AKTIF dan harus tetap aktif.
- Gap audit saat ini:
  "Idempotency record belum memiliki retention/cleanup policy; record disimpan permanen untuk menjamin replay konsisten."

Tujuan:
Mencegah tabel idempotency record tumbuh tanpa batas, tetapi tetap menjaga keamanan retry/replay dalam window yang wajar dan tidak menyebabkan duplicate payment/order.

Tugas:

1. Audit implementasi idempotency record yang sekarang.
   - Temukan model/schema database.
   - Temukan endpoint/service create payment.
   - Temukan bagaimana request lama direplay.
   - Temukan field timestamp yang tersedia.
   - Tentukan apakah record menyimpan request fingerprint/hash, response/reference, status, atau metadata lain.

2. Tentukan retention policy yang aman berdasarkan architecture existing.
   - Jangan menghapus record terlalu cepat.
   - Jangan mengubah behavior duplicate request selama retention window.
   - Gunakan timestamp database yang sudah tersedia atau tambahkan field timestamp jika memang diperlukan.
   - Jangan menggunakan in-memory cache sebagai pengganti persisted idempotency.

3. Implementasikan cleanup secara modular.
   - Buat service/job khusus untuk membersihkan idempotency record yang sudah melewati retention period.
   - Cleanup harus menggunakan query database yang aman dan efisien.
   - Gunakan batch deletion jika jumlah record berpotensi besar.
   - Jangan melakukan full table scan yang tidak perlu.
   - Jika memungkinkan, gunakan index pada field timestamp yang digunakan cleanup.

4. PENTING:
   - Jangan membuat cleanup yang dapat menghapus idempotency record yang masih dibutuhkan untuk replay.
   - Jangan menghapus record berdasarkan status saja.
   - Jangan menghapus record yang masih berada dalam retention window.
   - Jangan menghapus Order/Payment hanya karena idempotency record dibersihkan.
   - Cleanup idempotency record tidak boleh mengubah status payment/order.

5. Tentukan behavior setelah idempotency record sudah expired dari retention:
   - Request dengan key lama boleh dianggap sebagai request baru HANYA setelah retention window benar-benar lewat.
   - Pastikan behavior tersebut tidak memungkinkan duplicate business operation untuk payment yang masih aktif.
   - Jika desain existing tidak dapat menjamin hal tersebut, jangan memaksakan cleanup. Laporkan alasan dan buat policy yang lebih aman.

6. Race condition:
   - Cleanup yang berjalan bersamaan dengan create request harus aman.
   - Jangan sampai cleanup menghapus record yang sedang PROCESSING.
   - Jangan sampai request retry yang sedang diproses kehilangan idempotency protection.
   - Gunakan conditional query/transaction sesuai PostgreSQL + Prisma architecture existing.

7. Scheduler:
   - JANGAN mengubah systemd payment expiry scheduler yang sudah aktif.
   - Jangan mematikan/restart timer payment expiry.
   - Jangan mengubah interval expiry payment.
   - Jika cleanup membutuhkan scheduler sendiri, JANGAN langsung mengaktifkan production scheduler baru pada tahap ini.
   - Buat cleanup worker/command modular yang siap dijadwalkan kemudian.
   - Jangan menggunakan scheduler yang sama dengan expiry worker kecuali architecture existing memang secara jelas mendukungnya.

8. Testing:
   Tambahkan test:
   - idempotency record masih dalam retention → tetap dapat replay;
   - record sudah melewati retention → dapat dibersihkan;
   - PROCESSING record tidak dibersihkan;
   - cleanup tidak mengubah Order/Payment;
   - cleanup aman ketika create request berjalan bersamaan;
   - duplicate request selama retention tetap tidak membuat duplicate;
   - idempotency key berbeda tetap normal;
   - cleanup dijalankan dua kali → aman/idempotent;
   - tidak ada duplicate business effect.

9. Database:
   - Migration hanya jika diperlukan.
   - Jangan reset database.
   - Jangan migration destructive.
   - Jangan menghapus data production.
   - Jangan menjalankan cleanup terhadap database production untuk testing.
   - Pastikan index yang diperlukan benar-benar dibuat melalui migration.

10. Jangan mengintegrasikan:
   - payment provider nyata;
   - refund provider;
   - authorization integration test;
   - PostgreSQL HTTP concurrency infrastructure baru.

11. Jalankan verification:
   - idempotency tests;
   - payment tests;
   - webhook regression tests;
   - expiry tests;
   - typecheck;
   - lint;
   - build;
   - git diff --check.

12. Warning UI berikut jangan disentuh:
   src/components/ui/select.tsx

PENTING:
- Jangan mengubah frontend.
- Jangan mengubah webhook idempotency kecuali diperlukan untuk kompatibilitas.
- Jangan mengubah expiry worker.
- Jangan mengubah payment expiry systemd timer.
- Jangan restart/kill web process.
- Jangan reset database.
- Jangan commit.
- Jangan push GitHub.
- Jangan membuat README baru.
- Pertahankan arsitektur modular existing.

Jika retention cleanup ternyata berisiko terhadap replay payment:
- jangan memaksakan implementasi;
- pertahankan record permanen;
- jelaskan risiko storage growth;
- berikan rekomendasi retention yang aman untuk tahap berikutnya.

Setelah selesai tampilkan:
1. Retention period yang dipilih dan alasannya.
2. Model/table idempotency yang digunakan.
3. Field timestamp yang digunakan.
4. Cleanup service/worker yang dibuat.
5. Index/migration yang dibuat.
6. Cara mencegah cleanup terhadap PROCESSING record.
7. Cara race condition dicegah.
8. Test yang ditambahkan dan hasilnya.
9. Typecheck/lint/build.
10. Konfirmasi systemd payment expiry scheduler tetap aktif dan tidak diubah.
11. Gap payment lifecycle yang masih tersisa.

Jangan commit atau push.

```
# 
```
Lanjutkan project toko-online dari hasil audit Payment Flow End-to-End terakhir.

Fokus tahap ini HANYA pada DUPLICATE CREATE PAYMENT/ORDER IDEMPOTENCY.

Jangan mengubah systemd payment expiry scheduler yang saat ini sudah aktif.

Kondisi:

* Payment expiry scheduler production aktif dan berjalan normal.
* Webhook idempotency sudah selesai.
* Payment expiry worker sudah selesai.
* Payment expiry tests 10/10 PASS.
* Worker tests 3/3 PASS.
* Webhook regression 3/3 PASS.
* Typecheck/build sebelumnya PASS.
* Audit menemukan duplicate create request BELUM memiliki idempotency key yang persisted.
* Jangan mengerjakan authorization integration test, refund provider, atau PostgreSQL HTTP concurrency integration pada tahap ini.

Tujuan:
Jika client mengirim request CREATE PAYMENT yang sama lebih dari sekali karena retry/network timeout/double-click, sistem tidak boleh membuat order/payment baru secara tidak sengaja.

Tugas:

1. Audit endpoint create payment/order yang sekarang.

   * Temukan controller/route.
   * Temukan service/repository.
   * Temukan model Order dan Payment.
   * Tentukan identifier request idempotency yang paling tepat.
   * Jangan membuat identifier berdasarkan timestamp saja.

2. Implementasikan persisted idempotency key untuk create request.

   * Client harus dapat mengirim idempotency key.
   * Key harus disimpan di database.
   * Buat unique constraint/index pada scope yang tepat.
   * Pertimbangkan ownership/user agar user berbeda tidak saling collision.
   * Jangan menggunakan in-memory Map/cache sebagai source of truth.

3. Tentukan behavior ketika key yang sama dikirim ulang:

   * Jika request pertama sudah berhasil membuat payment/order, request berikutnya harus mengembalikan hasil yang sama atau reference yang sama.
   * Jangan membuat payment/order kedua.
   * Jangan membuat payment session kedua.
   * Jangan mengurangi stock/balance dua kali.
   * Jangan mengirim efek samping provider dua kali.

4. Tangani concurrent duplicate request.

   * Dua request dengan idempotency key sama yang masuk hampir bersamaan hanya boleh menghasilkan satu business operation.
   * Jangan hanya melakukan:
     find → jika tidak ada → create.
   * Gunakan unique constraint + transaction/atomic operation sesuai Prisma/PostgreSQL architecture existing.

5. Pastikan request dengan key berbeda:

   * tetap dapat membuat payment/order berbeda apabila memang request bisnisnya berbeda;
   * tidak dianggap duplicate.

6. Validasi request idempotency key.

   * Tentukan format dan panjang yang aman.
   * Tolak key kosong/invalid sesuai pola validation existing.
   * Jangan menyimpan data sensitif sebagai idempotency key.
   * Jangan log key jika dapat dikaitkan dengan data sensitif.

7. Pastikan replay behavior aman.

   * Jika request pertama SUCCESS lalu client retry, kembalikan result yang konsisten.
   * Jika request pertama gagal sebelum operation selesai, retry harus dapat diproses sesuai desain.
   * Jangan membuat record idempotency yang permanen sebagai SUCCESS jika business transaction sebenarnya gagal.

8. Database:

   * Tambahkan migration hanya jika diperlukan.
   * Jangan reset database.
   * Jangan menghapus data existing.
   * Unique constraint harus benar-benar berada di PostgreSQL.
   * Jangan membuat migration destructive.

9. Testing minimal:

   * create payment dengan idempotency key baru → berhasil;
   * request identik dengan key sama → tidak membuat duplicate;
   * request key sama setelah request pertama sukses → result/reference konsisten;
   * key berbeda → dapat membuat operation berbeda;
   * invalid/empty key → ditolak sesuai validation;
   * duplicate concurrent request → hanya satu business operation;
   * retry setelah failure → dapat diproses kembali sesuai desain;
   * tidak ada double stock/order/payment effect.

10. Jangan membutuhkan payment provider nyata.

    * Gunakan mock/helper/test fixture existing.
    * Jangan menggunakan credential production.

11. Jika integration PostgreSQL test infrastructure belum tersedia:

    * gunakan test layer yang aman dan helper/transaction behavior yang tersedia;
    * jangan mereset database development;
    * jangan membuat setup PostgreSQL besar hanya untuk tahap ini;
    * tetap pastikan unique constraint/migration diverifikasi secara static.

12. Jalankan:

    * seluruh test payment yang relevan;
    * create payment idempotency tests;
    * webhook regression tests;
    * expiry tests;
    * typecheck;
    * lint;
    * build;
    * git diff --check.

13. Warning UI ini jangan disentuh:
    `src/components/ui/select.tsx`

PENTING:

* Jangan mengubah systemd timer/service production.
* Jangan mematikan atau restart scheduler.
* Jangan restart/kill web process.
* Jangan mengubah frontend.
* Jangan mengubah webhook idempotency kecuali benar-benar diperlukan untuk kompatibilitas.
* Jangan mengubah expiry worker.
* Jangan mengintegrasikan provider payment nyata.
* Jangan mengimplementasikan refund provider.
* Jangan membuat authorization integration test pada tahap ini.
* Jangan reset database.
* Jangan migration destructive.
* Jangan commit.
* Jangan push GitHub.
* Jangan membuat README baru.
* Pertahankan arsitektur modular existing.

Jika menemukan bug kritis pada create payment:

* perbaiki hanya yang diperlukan untuk idempotency;
* tambahkan regression test;
* jangan melakukan refactor besar yang tidak diperlukan.

Setelah selesai tampilkan:

1. Endpoint create yang diperbaiki.
2. Idempotency key disimpan di model/table apa.
3. Unique constraint yang digunakan.
4. Behavior replay request.
5. Cara race condition dicegah.
6. File yang berubah.
7. Migration yang dibuat jika ada.
8. Test yang ditambahkan dan hasilnya.
9. Typecheck/lint/build.
10. Konfirmasi systemd payment expiry timer tetap aktif dan tidak diubah.
11. Gap payment lifecycle yang masih tersisa.

Jangan commit atau push.



```
# 
```
Lanjutkan project toko-online dari kondisi terakhir.

Payment expiry production scheduler SUDAH AKTIF dan jangan diubah lagi.

Fokus tahap berikutnya hanya pada AUDIT PAYMENT FLOW END-TO-END. Jangan menambahkan fitur baru sebelum audit selesai.

Kondisi yang sudah selesai:

* Webhook idempotency sudah diimplementasikan.
* Payment expiry worker sudah diimplementasikan.
* Legacy expiresAt sudah ditangani.
* Production systemd timer sudah aktif.
* Payment expiry tests 10/10 PASS.
* Worker tests 3/3 PASS.
* Webhook regression tests 3/3 PASS.
* Typecheck PASS.
* Build PASS.
* git diff --check PASS.

Tujuan:
Pastikan seluruh lifecycle payment konsisten dari pembuatan payment sampai terminal state.

Audit flow berikut:

1. CREATE PAYMENT

   * Periksa endpoint create payment.
   * Pastikan order/payment dibuat dengan status awal yang benar.
   * Pastikan nominal, product/order reference, user ownership, provider metadata, dan expiresAt konsisten.
   * Pastikan payment baru tidak dapat dibuat dengan data invalid.
   * Pastikan tidak terjadi duplicate order/payment akibat retry request create.

2. GET PAYMENT

   * Periksa endpoint get payment/session.
   * Pastikan user hanya dapat melihat payment miliknya.
   * Pastikan response tidak membocorkan credential/provider secret.
   * Pastikan status yang dikembalikan berasal dari database source of truth.
   * Pastikan expired payment tidak tetap terlihat sebagai pending apabila worker atau state transition sudah menandainya EXPIRED.

3. WEBHOOK

   * Audit webhook signature/authentication.
   * Audit provider/external transaction ID.
   * Audit idempotency key.
   * Pastikan duplicate webhook tidak mengulangi business effect.
   * Pastikan event berbeda untuk transaction berbeda tetap diproses.
   * Pastikan webhook tidak dapat mengubah payment secara ilegal dari terminal state.

4. PAYMENT SUCCESS

   * Audit transition:
     PENDING → PAID/COMPLETED.
   * Pastikan order hanya diselesaikan satu kali.
   * Pastikan stock/balance/business effect tidak dilakukan dua kali.
   * Pastikan webhook retry aman.
   * Pastikan payment yang sudah EXPIRED tidak dapat sembarangan menjadi PAID tanpa aturan bisnis yang valid.

5. PAYMENT EXPIRY

   * Audit transition:
     PENDING → EXPIRED.
   * Pastikan scheduler production yang sudah aktif tidak perlu diubah.
   * Pastikan worker tidak mengubah PAID/COMPLETED menjadi EXPIRED.
   * Pastikan concurrent webhook success dan expiry worker aman.

6. CANCEL / REFUND

   * Cari semua status CANCELLED, REFUNDED, FAILED, atau equivalent.
   * Audit semua state transition.
   * Pastikan refund tidak dapat dilakukan dua kali.
   * Pastikan payment terminal state tidak dapat dipindahkan secara ilegal.
   * Jika refund belum benar-benar diimplementasikan, JANGAN mengimplementasikan provider refund pada tahap ini. Hanya laporkan gap.

7. OWNERSHIP / AUTHORIZATION

   * Audit bahwa user A tidak dapat membaca atau mengubah payment/order user B.
   * Audit admin-only operation.
   * Pastikan webhook provider tidak menggunakan user authorization biasa.
   * Pastikan payment ID/order ID tidak menjadi satu-satunya security boundary.

8. DATABASE CONSISTENCY

   * Audit transaction boundary.
   * Cari operasi yang mengubah payment + order + stock secara bersamaan.
   * Pastikan partial update tidak menyebabkan state bisnis tidak konsisten.
   * Jangan melakukan migration baru kecuali benar-benar diperlukan untuk memperbaiki bug yang ditemukan.

9. RETRY / IDEMPOTENCY
   Buat test untuk:

   * duplicate create request;
   * duplicate webhook;
   * webhook retry setelah processing failure;
   * expiry worker dijalankan berulang;
   * concurrent success vs expiry;
   * refund retry jika refund flow sudah tersedia.

10. TESTING
    Jalankan seluruh test payment yang relevan.
    Tambahkan test hanya untuk bug/gap yang benar-benar ditemukan.
    Jangan membuat integration test PostgreSQL besar jika infrastructure test database belum tersedia.
    Jangan menyentuh database production.

11. Jalankan:

* payment tests;
* webhook tests;
* expiry tests;
* authorization tests jika tersedia;
* typecheck;
* lint;
* build;
* git diff --check.

12. Warning UI berikut jangan disentuh:
    `src/components/ui/select.tsx`

PENTING:

* Jangan mengubah systemd timer production.
* Jangan mematikan scheduler.
* Jangan restart/kill web process.
* Jangan mengubah frontend.
* Jangan mengintegrasikan provider payment nyata.
* Jangan membuat refund provider nyata.
* Jangan reset database.
* Jangan migration destructive.
* Jangan commit.
* Jangan push GitHub.
* Jangan membuat README baru.
* Pertahankan arsitektur modular.

Jika menemukan bug kritis:

* perbaiki hanya jika perubahan aman dan berada dalam scope payment lifecycle;
* tambahkan regression test;
* jangan melakukan perubahan besar tanpa alasan.

Setelah selesai tampilkan:

1. Flow payment saat ini dari CREATE sampai terminal state.
2. State transition yang valid.
3. State transition yang berisiko/bug.
4. Bug yang ditemukan dan diperbaiki.
5. File yang berubah.
6. Test yang ditambahkan.
7. Hasil semua test/typecheck/lint/build.
8. Gap yang masih tersisa.
9. Konfirmasi bahwa systemd payment expiry timer tetap aktif dan tidak diubah.

Jangan commit atau push.



```
# 
```
Lanjutkan project toko-online dari kondisi terakhir.

Semua konfigurasi production environment dan systemd payment expiry worker sudah tervalidasi. Sekarang lakukan FINAL ACTIVATION scheduler production.

Kondisi:

* Deployment path: `/root/toko-online/app`
* systemd service payment expiry sudah tersedia.
* systemd timer sudah tersedia.
* Production environment sudah dikonfirmasi.
* Worker manual smoke test berhasil.
* Payment expiry tests 10/10 PASS.
* Worker tests 3/3 PASS.
* Webhook regression 3/3 PASS.
* Typecheck PASS.
* Build PASS.
* git diff --check PASS.
* Service dan timer masih disabled/inactive.
* Jangan mengubah logic payment, webhook idempotency, atau expiry worker.

Tugas:

1. Sebelum aktivasi, lakukan final read-only verification:

   * `systemctl cat toko-online-payment-expiry.service`
   * `systemctl cat toko-online-payment-expiry.timer`
   * `systemctl status toko-online-payment-expiry.service`
   * `systemctl status toko-online-payment-expiry.timer`
   * pastikan EnvironmentFile yang digunakan benar;
   * jangan tampilkan secret.

2. Pastikan service dan timer valid:

   * `systemd-analyze verify`
   * pastikan dependency dan WorkingDirectory benar;
   * pastikan command worker benar.

3. Aktifkan scheduler production:

   * `systemctl daemon-reload`
   * `systemctl enable --now toko-online-payment-expiry.timer`

   HANYA timer yang perlu di-enable/start.
   Jangan menjalankan web service toko-online.

4. Setelah timer aktif:

   * cek `systemctl status toko-online-payment-expiry.timer`;
   * cek `systemctl list-timers`;
   * pastikan timer memiliki NEXT RUN yang valid;
   * pastikan service belum mengalami restart loop.

5. Lakukan satu verifikasi execution.

   * Jalankan service secara manual SATU KALI jika diperlukan untuk memastikan production execution.
   * Pastikan DATABASE_URL production digunakan.
   * Pastikan worker berhasil connect ke database.
   * Pastikan worker selesai normal.
   * Jangan membuat payment baru.
   * Jangan mengubah data payment hanya untuk testing.

6. Periksa log:

   * `journalctl -u toko-online-payment-expiry.service -n 100 --no-pager`
   * pastikan tidak ada error;
   * jangan tampilkan credential/secret dalam laporan.

7. Pastikan scheduler tidak overlapping:

   * timer hanya memanggil service;
   * service one-shot selesai sebelum execution berikutnya;
   * worker tetap aman jika execution sebelumnya masih berjalan.

8. Jalankan verification akhir:

   * payment expiry tests;
   * worker tests;
   * webhook regression tests;
   * typecheck;
   * lint;
   * build;
   * git diff --check.

9. Warning berikut tetap jangan disentuh:
   `src/components/ui/select.tsx`
   karena bukan bagian scope.

PENTING:

* Jangan mengubah frontend.
* Jangan mengubah webhook idempotency.
* Jangan mengubah expiry logic.
* Jangan migration.
* Jangan reset database.
* Jangan mengubah port.
* Jangan kill/restart process web existing.
* Jangan membuat scheduler kedua.
* Jangan membuat cron tambahan.
* Jangan commit.
* Jangan push GitHub.
* Jangan menampilkan secret.

Jika aktivasi gagal:

* jangan melakukan perubahan destruktif;
* tampilkan error systemd yang sebenarnya;
* jangan mematikan process web existing;
* jangan menebak konfigurasi.

Setelah selesai tampilkan laporan:

1. Service status.
2. Timer status.
3. NEXT RUN.
4. LAST RUN jika sudah ada.
5. Hasil manual worker execution.
6. Log worker tanpa secret.
7. Semua test/build verification.
8. Apakah scheduler production sudah benar-benar aktif.

Berhenti setelah verifikasi selesai.:::



```
# 
```

Lanjutkan project toko-online dari hasil audit terakhir.

Fokus tahap ini pada FINALISASI ENVIRONMENT PRODUCTION DAN INSTALASI UNIT SYSTEMD PAYMENT EXPIRY.

Kondisi terakhir:

* Deployment path: `/root/toko-online/app`
* Systemd direkomendasikan sebagai process manager.
* Scheduler belum aktif.
* Screenshot verification menunjukkan:

  * `toko-online-payment-expiry.timer` belum ter-install;
  * `toko-online-payment-expiry.service` belum ter-install;
  * file `/etc/systemd/system/toko-online-payment-expiry.service` belum ada;
  * file `/etc/systemd/system/toko-online-payment-expiry.timer` belum ada.
* `/etc/toko-online/toko-online.env` belum dibuat karena source `DATABASE_URL` production belum dikonfirmasi.
* Web process existing JANGAN disentuh.

Tujuan:
Menentukan source `DATABASE_URL` production secara aman, lalu memasang service + timer systemd tanpa mengaktifkannya.

Tugas:

1. Audit process toko-online yang sedang berjalan secara READ-ONLY.

   * Cari PID/process `next-server` yang merupakan toko-online.
   * Tentukan working directory dan port.
   * Jangan kill/restart process.

2. Identifikasi environment yang sedang digunakan process production.

   * Periksa environment process melalui mekanisme Linux yang tersedia, misalnya `/proc/<PID>/environ`.
   * Jangan pernah print nilai `DATABASE_URL`, password, API key, token, cookie, atau secret.
   * Hanya laporkan:

     * apakah `DATABASE_URL` tersedia;
     * hostname/database yang sudah dimasking;
     * atau fingerprint/hash non-reversible untuk perbandingan.

3. Bandingkan dengan environment project:

   * `.env.local`
   * `.env`
   * `.env.production`
   * file environment deployment lain jika ada.

   Tujuan hanya untuk menentukan apakah salah satu file tersebut benar-benar merupakan source production.

4. Jika `DATABASE_URL` dari process production dapat dipastikan sama dengan salah satu source environment:

   * gunakan source tersebut sebagai dasar `/etc/toko-online/toko-online.env`;
   * jangan tampilkan nilainya;
   * jangan menyalin secret yang tidak diperlukan worker;
   * minimal masukkan variable yang memang diperlukan worker.

5. Jika tidak dapat dipastikan:

   * JANGAN menebak;
   * JANGAN membuat DATABASE_URL palsu;
   * JANGAN memasang service yang dapat terhubung ke database yang salah;
   * berhenti dan laporkan bahwa source production perlu dikonfirmasi.

6. Jika source production berhasil dipastikan:
   Buat:
   `/etc/toko-online/`

   lalu:
   `/etc/toko-online/toko-online.env`

   Ketentuan:

   * permission ketat;
   * tidak berada di Git;
   * tidak mencetak isi file;
   * jangan memasukkan secret yang tidak dibutuhkan worker.

7. Tentukan user systemd berdasarkan deployment existing.

   * Jangan otomatis mengganti ownership project.
   * Jangan membuat user baru tanpa kebutuhan.
   * Jika production existing berjalan sebagai root dan tidak aman untuk dipindahkan pada tahap ini, gunakan root untuk worker dan laporkan sebagai technical debt.
   * Jangan mengubah user web process existing.

8. Setelah environment valid, buat DAN INSTALL unit:
   `/etc/systemd/system/toko-online-payment-expiry.service`

   dan:
   `/etc/systemd/system/toko-online-payment-expiry.timer`

   Service harus:

   * WorkingDirectory `/root/toko-online/app`;
   * menggunakan command worker production yang sudah diverifikasi;
   * menggunakan `EnvironmentFile=/etc/toko-online/toko-online.env`;
   * tidak menyimpan secret langsung dalam unit;
   * Type=oneshot jika worker memang one-shot;
   * logging melalui journalctl;
   * aman dijalankan berulang;
   * tidak menjalankan web server.

9. Timer:

   * memanggil service worker;
   * menggunakan interval yang sudah ditentukan oleh aturan expiry existing;
   * tidak membuat overlapping worker;
   * jangan mengubah expiry duration;
   * jangan menggunakan setInterval.

10. Setelah unit dibuat:
    Jalankan:

* `systemd-analyze verify /etc/systemd/system/toko-online-payment-expiry.service`
* `systemd-analyze verify /etc/systemd/system/toko-online-payment-expiry.timer`

Jika valid, lakukan `systemctl daemon-reload` hanya agar systemd membaca unit baru.

11. PENTING:
    JANGAN:

* `systemctl enable`
* `systemctl start`
* `systemctl restart`
* `systemctl stop`
* `systemctl enable --now`
* mengaktifkan timer
* mengaktifkan service

Pada tahap ini status harus tetap DISABLED/NOT STARTED.

12. Validasi worker secara manual:

* gunakan environment production yang sudah dikonfirmasi;
* jalankan worker secara manual;
* pastikan database connection berhasil;
* pastikan worker scan database;
* pastikan worker exit normal;
* jangan membuat payment baru;
* jangan mengubah data payment hanya untuk testing.

13. Pastikan file environment aman:

* cek permission;
* cek owner;
* cek bahwa file tidak tracked Git;
* jangan tampilkan isi secret.

14. Jalankan verification project:

* payment expiry tests;
* webhook regression tests;
* typecheck;
* lint;
* build;
* git diff --check.

Warning berikut tetap jangan disentuh:
`src/components/ui/select.tsx`
ARIA warning tersebut di luar scope.

PENTING:

* Jangan mengubah frontend.
* Jangan mengubah webhook idempotency.
* Jangan mengubah expiry logic.
* Jangan migration.
* Jangan reset database.
* Jangan mengubah port.
* Jangan kill/restart process web existing.
* Jangan commit.
* Jangan push GitHub.
* Jangan membuat README baru.
* Jangan menampilkan secret.

Jika `DATABASE_URL` production tidak bisa diverifikasi, berhenti sebelum membuat environment file dan unit yang aktif. Tampilkan hanya blocker tersebut.

Jika berhasil, tampilkan:

1. Process production yang menjadi sumber environment.
2. Source environment yang terverifikasi, tanpa secret.
3. User systemd.
4. Permission `/etc/toko-online/toko-online.env`.
5. Service path.
6. Timer path.
7. Hasil `systemd-analyze verify`.
8. Hasil manual worker.
9. Hasil test/typecheck/lint/build.
10. Status service dan timer — HARUS DISABLED/NOT STARTED.
11. Command yang nanti digunakan untuk mengaktifkan scheduler setelah deployment owner menyetujui.

Berhenti setelah konfigurasi terpasang dan tervalidasi.:::


```
# 
```

Lanjutkan project toko-online dari hasil konfigurasi systemd terakhir.

Fokus tahap ini hanya pada MENYIAPKAN PRODUCTION ENVIRONMENT UNTUK SYSTEMD PAYMENT EXPIRY WORKER.

Kondisi saat ini:

* Deployment path: `/root/toko-online/app`
* systemd service payment expiry sudah dibuat.
* systemd timer sudah dibuat.
* Service dan timer BELUM enable/start.
* File `/etc/toko-online/toko-online.env` belum dibuat.
* Environment production belum dipisahkan secara eksplisit dari `.env.local`.
* Jangan mengubah logic payment, webhook idempotency, atau expiry worker.

Tugas:

1. Audit environment existing secara read-only.

   * Periksa `.env`, `.env.local`, `.env.production`, dan environment lain yang relevan.
   * Identifikasi variable yang benar-benar diperlukan oleh payment expiry worker.
   * Minimal pastikan kebutuhan `DATABASE_URL`.
   * Jangan pernah menampilkan nilai secret/API key/password ke output.

2. Tentukan sumber environment production yang benar.

   * Jangan menggunakan `.env.local` sebagai production environment secara otomatis.
   * Jangan menyalin seluruh `.env.local` jika berisi secret yang tidak diperlukan worker.
   * Gunakan hanya variable yang memang diperlukan oleh worker.
   * Jangan hardcode secret ke source code.

3. Buat directory environment systemd jika belum ada:
   `/etc/toko-online/`

4. Buat environment file:
   `/etc/toko-online/toko-online.env`

   Ketentuan:

   * permission ketat;
   * owner sesuai user service;
   * tidak masuk Git;
   * tidak berisi credential yang tidak diperlukan worker;
   * `DATABASE_URL` harus berasal dari environment production yang benar.
   * Jika DATABASE_URL production belum dapat diidentifikasi dengan aman, JANGAN menebak dan jangan membuat nilai palsu.

5. Audit user service.

   * Tentukan user yang seharusnya menjalankan worker berdasarkan permission `/root/toko-online/app`, Node.js, Prisma, dan deployment existing.
   * Jangan membuat user baru tanpa alasan.
   * Jika project memang hanya dapat dijalankan sebagai root karena struktur deployment saat ini, laporkan sebagai technical debt dan jangan mengubah ownership project secara massal.

6. Update systemd unit agar:

   * menggunakan `EnvironmentFile=/etc/toko-online/toko-online.env`;
   * tidak menyimpan secret langsung di unit file;
   * WorkingDirectory tetap `/root/toko-online/app`;
   * command worker tetap menggunakan script production yang sudah diverifikasi;
   * logging tetap melalui journalctl.

7. Jangan enable/start service atau timer pada tahap ini.

8. Validasi:

   * `systemd-analyze verify`;
   * validasi environment tanpa mencetak secret;
   * jalankan worker manual menggunakan environment production jika aman;
   * pastikan database connection berhasil;
   * pastikan worker exit normal;
   * jangan membuat perubahan payment nyata hanya untuk testing.

9. Jalankan:

   * payment expiry tests;
   * webhook regression tests;
   * typecheck;
   * lint;
   * build;
   * git diff --check.

10. Pastikan `/etc/toko-online/toko-online.env` tidak berada di repository dan tidak akan ikut commit.

JANGAN:

* enable systemd;
* start systemd service;
* enable timer;
* start timer;
* kill/restart process web existing;
* mengubah port;
* reset database;
* migration;
* mengubah frontend;
* mengubah webhook idempotency;
* mengubah expiry logic;
* commit;
* push GitHub;
* menampilkan secret di output;
* membuat README baru.

Jika `DATABASE_URL` production belum dapat ditentukan:

* berhenti sebelum membuat environment palsu;
* tampilkan variable apa yang dibutuhkan;
* tampilkan dari mana environment tersebut seharusnya berasal;
* jangan mengaktifkan scheduler.

Setelah selesai tampilkan:

1. Environment source yang ditemukan tanpa nilai rahasia.
2. File environment yang dibuat.
3. User service yang dipilih.
4. Permission file environment.
5. Perubahan systemd unit.
6. Hasil `systemd-analyze verify`.
7. Hasil manual worker smoke test.
8. Hasil test/typecheck/lint/build.
9. Pastikan service dan timer tetap DISABLED/NOT STARTED.

Berhenti setelah environment production siap dan tervalidasi.


```
# 
```
Lanjutkan project toko-online dari hasil audit deployment VPS terakhir.

Fokus hanya pada MEMBUAT KONFIGURASI SYSTEMD UNTUK PAYMENT EXPIRY WORKER.

Kondisi:

* Deployment path yang direkomendasikan: `/root/toko-online/app`
* Process manager yang direkomendasikan: systemd.
* Scheduler production belum aktif.
* Payment expiry worker sudah selesai dan seluruh test sebelumnya PASS.
* Jangan mengubah logic payment, webhook idempotency, atau expiry worker.

Tugas:

1. Audit ulang secara read-only:

   * pastikan `/root/toko-online/app` benar-benar project production;
   * pastikan script worker expiry tersedia;
   * pastikan Node.js/npm yang digunakan project;
   * pastikan Prisma/database client tersedia;
   * jangan tampilkan nilai secret.

2. Buat systemd service khusus untuk payment expiry worker.

   * Gunakan nama service yang jelas, misalnya `toko-online-payment-expiry.service`.
   * WorkingDirectory harus menggunakan deployment path yang sudah diverifikasi.
   * Gunakan command worker yang benar dari package.json/project.
   * Gunakan environment production yang benar.
   * Jangan hardcode API key/password/DATABASE_URL ke repository atau unit file jika environment file existing dapat digunakan.
   * Jalankan sebagai user yang sesuai dengan deployment existing; jangan otomatis menggunakan root jika tidak diperlukan.

3. Service harus:

   * berjalan satu kali untuk menjalankan expiry worker lalu exit normal jika worker memang designed sebagai one-shot job;
   * aman dipanggil ulang;
   * tidak membuat duplicate worker;
   * menangani SIGTERM/SIGINT;
   * memiliki restart behavior yang tidak menyebabkan infinite rapid restart;
   * logging dapat dilihat melalui `journalctl`.

4. Jika worker membutuhkan build/compile terlebih dahulu:

   * gunakan artifact/build existing;
   * jangan membuat proses TypeScript compiler permanen jika production seharusnya menjalankan JavaScript hasil build.
   * Jangan mengubah arsitektur build tanpa alasan.

5. Buat konfigurasi scheduler berikutnya secara terpisah hanya jika diperlukan.

   * Jika menggunakan systemd timer, buat `toko-online-payment-expiry.timer`.
   * Timer harus memanggil service worker secara berkala.
   * Jangan gunakan `setInterval`.
   * Gunakan interval yang sesuai dengan aturan expiry existing.
   * Pastikan timer tidak membuat overlapping execution.
   * `Persistent=true` hanya jika memang sesuai kebutuhan deployment.

6. PENTING:

   * Buat unit/service dan timer sebagai konfigurasi, tetapi JANGAN enable/start service atau timer production pada tahap ini.
   * Jangan stop/restart process toko-online yang sedang berjalan.
   * Jangan mengubah port.
   * Jangan mengubah reverse proxy.
   * Jangan mengubah DNS/Cloudflare.
   * Jangan mengubah database.

7. Validasi konfigurasi secara aman:

   * `systemd-analyze verify` terhadap unit yang dibuat.
   * Validasi command worker secara manual tanpa mengaktifkan timer.
   * Jika aman, lakukan dry-run worker menggunakan environment production.
   * Pastikan database connection berhasil.
   * Pastikan worker exit normal.
   * Jangan membuat perubahan data payment nyata hanya untuk testing.

8. Jalankan:

   * payment expiry tests;
   * webhook regression tests;
   * typecheck;
   * lint;
   * build;
   * git diff --check.

9. Warning berikut tetap jangan disentuh:
   `src/components/ui/select.tsx`
   ARIA warning tersebut di luar scope.

PENTING:

* Jangan enable systemd service.
* Jangan start systemd service.
* Jangan enable timer.
* Jangan start timer.
* Jangan kill/restart process existing.
* Jangan reset database.
* Jangan menjalankan migration.
* Jangan mengubah frontend.
* Jangan mengubah webhook idempotency.
* Jangan mengubah expiry logic.
* Jangan commit.
* Jangan push GitHub.
* Jangan membuat README baru.
* Jangan menaruh secret di repository.

Setelah selesai tampilkan:

1. Nama service.
2. Nama timer jika dibuat.
3. File/unit yang dibuat.
4. Command worker yang digunakan.
5. Environment source yang digunakan tanpa menampilkan secret.
6. Hasil `systemd-analyze verify`.
7. Hasil manual worker smoke test.
8. Hasil test/typecheck/lint/build.
9. Status: harus tetap DISABLED/NOT STARTED.
10. Command yang nantinya diperlukan untuk mengaktifkan scheduler production.

Berhenti setelah konfigurasi dan validasi. Jangan mengaktifkan scheduler.



```
# 
```

Lanjutkan project toko-online dari kondisi terakhir.

Fokus tahap ini hanya pada AUDIT DAN STANDARDISASI DEPLOYMENT VPS. Jangan mengaktifkan scheduler production dan jangan mematikan process existing pada tahap ini.

Kondisi yang sudah diketahui:

* Typecheck PASS.
* Lint PASS dengan warning UI existing.
* Build PASS.
* git diff --check PASS.
* Payment expiry worker sudah selesai dan sudah diuji.
* Scheduler production belum aktif.
* Tidak ditemukan konfigurasi PM2/systemd deployment yang jelas.
* Root crontab kosong.
* Terdapat beberapa process `next-server` unmanaged pada port berbeda.
* Environment production belum teridentifikasi dengan jelas.
* Jangan mengubah logic payment/expiry/webhook.

Tugas:

1. Audit process yang sedang berjalan di VPS.

   * Identifikasi setiap process `next-server`.
   * Catat PID, command line, working directory, user, port, dan waktu process jika tersedia.
   * Tentukan process mana yang kemungkinan merupakan toko-online.
   * Jangan kill, restart, stop, atau modify process apa pun.

2. Audit port.

   * Cari port yang digunakan oleh toko-online.
   * Cari process yang listen pada port tersebut.
   * Bedakan antara toko-online, service lain, dan process orphan/duplicate.
   * Jangan mengubah port existing.

3. Audit deployment path.

   * Tentukan directory project toko-online yang benar.
   * Periksa apakah process production dijalankan dari directory tersebut.
   * Periksa package.json scripts yang tersedia.
   * Periksa apakah ada `.env`, `.env.production`, environment file, ecosystem config, systemd unit, Docker config, atau konfigurasi deployment lain.
   * Jangan menampilkan secret/API key/database password ke output.

4. Audit environment production.

   * Pastikan apakah `DATABASE_URL` tersedia untuk process production.
   * Jangan mencetak nilai rahasia.
   * Hanya tampilkan apakah variable tersedia atau tidak.
   * Tentukan dari mana environment production seharusnya dimuat berdasarkan deployment existing.

5. Audit process manager.
   Periksa secara read-only:

   * PM2;
   * systemd;
   * supervisor;
   * Docker;
   * cron;
   * service manager lain yang relevan.

   Jangan menginstal atau mengaktifkan service baru pada tahap ini.

6. Audit apakah terdapat duplicate deployment.

   * Beberapa `next-server` mungkin merupakan process lama/duplicate.
   * Jangan menyimpulkan process mana yang aman dimatikan hanya berdasarkan nama.
   * Jangan melakukan cleanup otomatis.
   * Jika ditemukan duplicate, tampilkan bukti dan rekomendasi saja.

7. Setelah audit, buat rancangan deployment standar yang aman untuk toko-online.

Target akhirnya:

* satu deployment web toko-online yang jelas;
* satu lokasi project production yang jelas;
* environment production yang jelas;
* satu process manager yang jelas;
* payment expiry worker dapat dijalankan sebagai process/job terpisah;
* scheduler nantinya dapat memanggil worker tanpa mengganggu web server.

8. Jika belum ada process manager yang digunakan:

   * jangan langsung menginstal/menjalankan PM2;
   * buat hanya konfigurasi yang diperlukan untuk deployment standar;
   * jangan mengganti process existing;
   * jelaskan command yang diperlukan untuk migrasi setelah mapping sudah dikonfirmasi.

9. Buat konfigurasi deployment hanya jika aman dan tidak mengaktifkan service.

   * Jika membuat ecosystem config atau deployment config, gunakan environment variable reference, bukan secret hardcoded.
   * Jangan memasukkan API key/password/DATABASE_URL literal ke file repository.
   * Pastikan worker dan web server dapat dipisahkan.

10. Verifikasi setelah perubahan:

* typecheck;
* lint;
* build;
* git diff --check.

JANGAN:

* kill process;
* stop/restart service existing;
* mengubah port;
* mengaktifkan PM2;
* mengaktifkan systemd;
* membuat cron aktif;
* menjalankan migration;
* reset database;
* mengubah frontend;
* mengubah webhook idempotency;
* mengubah payment expiry logic;
* commit;
* push GitHub.

Setelah selesai tampilkan laporan:

1. Process toko-online yang ditemukan:

   * PID
   * port
   * directory
   * command
   * status

2. Process duplicate/orphan yang ditemukan, tanpa mematikannya.

3. Process manager yang tersedia.

4. Sumber environment production yang ditemukan, tanpa menampilkan secret.

5. Deployment path yang direkomendasikan.

6. Konfigurasi yang dibuat jika ada.

7. Langkah berikutnya yang diperlukan untuk mengaktifkan deployment secara aman.

8. Hasil typecheck/lint/build/git diff --check.

Berhenti setelah audit dan standardisasi. Jangan mengaktifkan production scheduler pada tahap ini.


```
# 
```

Lanjutkan project toko-online dari kondisi terakhir.

Sekarang fokus pada FINALISASI PRODUCTION SCHEDULER di VPS untuk Payment Expiry Worker.

Kondisi saat ini:

* Payment expiry worker sudah selesai.
* Worker runner sudah diuji: 3 tests passed.
* Webhook regression: 3 tests passed.
* Manual worker smoke test PASS.
* Worker dapat start, scan, finish, dan disconnect secara normal.
* `expiresAt` sudah tersedia dan legacy backfill sudah selesai.
* Scheduler production belum aktif.
* Deployment menggunakan VPS.
* Jangan mengubah logic bisnis expiry yang sudah selesai.

Tugas:

1. Audit environment VPS terlebih dahulu.

   * Periksa apakah PM2 tersedia dan digunakan.
   * Periksa systemd service yang relevan.
   * Periksa cron yang sudah ada.
   * Periksa bagaimana aplikasi toko-online saat ini dijalankan.
   * Jangan membuat duplicate process untuk web server yang sudah berjalan.

2. Pilih scheduler berdasarkan infrastructure yang benar-benar ditemukan.
   Prioritas:

   * gunakan PM2 jika aplikasi existing memang dikelola PM2;
   * gunakan systemd jika deployment existing menggunakan systemd;
   * gunakan cron hanya jika itu pilihan paling sederhana dan aman.

3. Jangan langsung mengaktifkan scheduler production sebelum konfigurasi diverifikasi.
   Pastikan:

   * `DATABASE_URL` tersedia untuk worker;
   * environment production yang benar digunakan;
   * worker dapat dijalankan manual menggunakan environment production;
   * worker tidak memakai database development secara tidak sengaja.

4. Worker production harus berjalan sebagai process terpisah dari web server jika architecture existing mengharuskannya.

   * Jangan menjalankan worker di frontend.
   * Jangan membuat dua scheduler berbeda.
   * Jangan menggunakan `setInterval` sebagai satu-satunya production scheduler.

5. Jika PM2 digunakan:

   * tambahkan konfigurasi worker ke ecosystem configuration existing jika memang ada;
   * gunakan environment yang benar;
   * pastikan restart tidak membuat duplicate worker;
   * gunakan graceful shutdown yang sudah tersedia.

6. Jika systemd digunakan:

   * buat service worker yang modular;
   * gunakan environment production dengan aman;
   * restart policy harus wajar;
   * pastikan SIGTERM/SIGINT ditangani dengan benar.

7. Jika cron digunakan:

   * panggil worker runner secara berkala;
   * gunakan absolute path;
   * pastikan environment `DATABASE_URL` tersedia;
   * cegah overlapping worker jika worker sebelumnya belum selesai;
   * logging diarahkan ke lokasi yang jelas.

8. Interval:

   * gunakan interval yang sesuai dengan aturan expiry existing;
   * scheduler hanya menjalankan worker;
   * jangan mengubah durasi expiry payment.

9. Verifikasi deployment:

   * jalankan worker manual menggunakan environment production;
   * pastikan database connection berhasil;
   * pastikan worker dapat scan database;
   * pastikan worker exit normal;
   * pastikan scheduler dapat menjalankannya;
   * cek process/service status;
   * cek log worker.

10. Jika ditemukan bahwa konfigurasi deployment belum cukup untuk mengaktifkan scheduler dengan aman:

* jangan menebak;
* jangan mengubah service production;
* tampilkan command yang diperlukan dan konfigurasi yang masih kurang.

11. Jalankan verification:

* test payment expiry;
* webhook regression test;
* typecheck;
* build;
* status scheduler/process;
* manual worker smoke test.

Jangan memperbaiki warning:
`src/components/ui/select.tsx`
karena warning tersebut di luar scope.

PENTING:

* Jangan reset database.
* Jangan mengubah frontend.
* Jangan mengubah webhook idempotency.
* Jangan mengintegrasikan payment provider nyata.
* Jangan membuat README baru.
* Jangan membuat scheduler kedua.
* Jangan commit.
* Jangan push GitHub.
* Jangan menghapus process/service production yang sudah ada.
* Jangan menjalankan migration destructive.

Setelah selesai tampilkan:

* scheduler yang ditemukan/dipilih;
* konfigurasi yang dibuat;
* interval;
* command worker;
* status process/service;
* hasil log manual;
* hasil test/build;
* apakah scheduler sudah aktif atau masih menunggu konfigurasi manual;
* masalah yang masih tersisa.


```
# 
```

Lanjutkan project toko-online dari kondisi terakhir.

Fokus tahap ini hanya pada INTEGRASI SCHEDULER PRODUCTION untuk menjalankan Payment Expiry Worker yang sudah selesai dibuat.

Kondisi saat ini:

* Payment expiry worker sudah tersedia dan modular.
* Worker sudah diuji dan idempotent.
* `expiresAt` sudah tersedia untuk payment baru.
* Backfill/policy legacy sudah selesai.
* PostgreSQL `expiresAt = NULL` count sudah 0.
* Typecheck PASS.
* Build PASS.
* Lint PASS dengan warning UI existing.
* Scheduler production belum aktif.
* Jangan mengubah logic expiry worker kecuali memang diperlukan agar scheduler dapat memanggilnya dengan aman.

Tugas:

1. Audit struktur project dan cara deployment existing.

   * Cari entrypoint server.
   * Cari package scripts.
   * Cari process manager/deployment configuration jika sudah ada.
   * Cari apakah project menggunakan cron, systemd, PM2, BullMQ, node-cron, atau scheduler lain.
   * Jangan menambahkan scheduler baru jika infrastructure yang sesuai sudah tersedia.

2. Pilih mekanisme scheduler paling sederhana dan sesuai architecture existing.

Jika project menggunakan process manager:

* integrasikan worker melalui mekanisme process manager yang sudah digunakan.

Jika project tidak memiliki scheduler:

* buat entrypoint worker terpisah yang dapat menjalankan expiry job.
* Worker harus dapat dijalankan secara berkala oleh cron/systemd/process manager.
* Jangan membuat scheduler yang bergantung pada browser/frontend.
* Jangan menggunakan setInterval sebagai satu-satunya mekanisme production scheduling jika deployment dapat menjalankan lebih dari satu instance.

3. Pastikan worker aman apabila terdapat lebih dari satu process/instance.

   * Dua worker yang berjalan bersamaan tidak boleh menyebabkan payment berubah dua kali.
   * Gunakan conditional database update/transaction yang sudah digunakan expiry worker.
   * Jangan menggunakan global/in-memory mutex sebagai protection utama.

4. Tentukan interval scheduler yang masuk akal berdasarkan expiry payment yang sudah ada.

   * Jangan mengubah aturan expiry payment.
   * Scheduler hanya bertugas memanggil worker.
   * Jangan membuat expiry lebih cepat/lambat dari aturan bisnis yang sudah ditentukan.

5. Tambahkan logging yang cukup:

   * worker mulai;
   * jumlah payment yang ditemukan;
   * jumlah payment yang berhasil di-expire;
   * jumlah yang dilewati karena state sudah berubah;
   * error database jika terjadi.

   Jangan log credential, payment secret, API key, signature, atau data sensitif.

6. Tambahkan graceful shutdown jika entrypoint worker berjalan sebagai process terpisah.

   * Tangani SIGTERM/SIGINT.
   * Jangan meninggalkan transaction/database connection terbuka.
   * Pastikan process dapat dihentikan oleh PM2/systemd/container dengan aman.

7. Tambahkan script package.json yang jelas, misalnya sesuai pola project existing:

   * command untuk menjalankan expiry worker;
   * command untuk menjalankan test expiry.

   Jangan menghapus script existing.

8. Testing:

   * worker tetap dapat dijalankan secara manual;
   * scheduler/entrypoint memanggil worker yang benar;
   * worker kedua yang berjalan bersamaan tetap aman;
   * payment expired berubah menjadi EXPIRED;
   * PAID/COMPLETED tidak berubah;
   * worker kedua tidak menghasilkan duplicate effect.

9. Jalankan verification:

   * test payment expiry;
   * test scheduler/worker jika tersedia;
   * typecheck;
   * lint;
   * build.

10. Jangan memperbaiki warning:
    `src/components/ui/select.tsx`
    karena warning tersebut tidak berkaitan dengan scope scheduler.

PENTING:

* Jangan commit.
* Jangan push GitHub.
* Jangan reset database.
* Jangan mengubah frontend/UI.
* Jangan mengintegrasikan provider payment nyata.
* Jangan mengubah webhook idempotency.
* Jangan membuat README baru.
* Jangan menginstal dependency baru jika tidak benar-benar diperlukan.
* Jangan mengaktifkan cron/systemd production secara langsung dari prompt ini jika environment deployment belum pasti.
* Jika deployment menggunakan VPS tetapi konfigurasi process manager belum diketahui, buat entrypoint/script yang siap dipanggil dan jelaskan command deployment yang diperlukan, tanpa mengubah service production.

Setelah selesai tampilkan:

* mekanisme scheduler yang dipilih;
* interval yang digunakan;
* file yang berubah;
* command untuk menjalankan worker;
* cara deployment production;
* hasil test/typecheck/lint/build;
* masalah yang masih tersisa.

Jangan commit atau push.


```
# 
```
Lanjutkan project toko-online dari hasil implementasi Payment Session Expiry Worker.

Fokus tahap ini hanya pada penanganan DATA PAYMENT LAMA yang masih memiliki `expiresAt = null`.

Kondisi saat ini:

* Payment expiry worker sudah dibuat.
* Worker dapat mencari payment yang sudah melewati expiry.
* State transition expiry sudah dibuat aman.
* Test expiry sudah tersedia.
* Scheduler belum aktif dan akan ditangani pada tahap deployment.
* Beberapa data/payment lama dapat memiliki `expiresAt = null`.

Tugas:

1. Audit seluruh penggunaan `expiresAt`.

   * Model/schema database.
   * Create payment/session.
   * Get payment/session.
   * Repository/service.
   * Expiry worker.
   * Semua query yang berkaitan dengan payment pending.

2. Tentukan policy yang aman untuk data lama dengan `expiresAt = null`.

   * Jangan mengarang expiry berdasarkan timestamp yang tidak jelas.
   * Jangan langsung mengubah semua data production tanpa mengetahui sumber waktu yang benar.
   * Jangan membuat payment lama otomatis EXPIRED jika tidak dapat dibuktikan bahwa expiry-nya sudah lewat.

3. Cari apakah payment lama mempunyai field timestamp lain yang dapat digunakan secara valid, misalnya:

   * createdAt;
   * payment creation time;
   * provider/session creation time;
   * atau field equivalent.

4. Jika terdapat sumber waktu yang valid dan aturan expiry project sudah jelas:

   * buat migration/backfill yang deterministik;
   * hanya isi `expiresAt` untuk record yang dapat dihitung dengan aman;
   * jangan menimpa `expiresAt` yang sudah memiliki nilai;
   * jangan mengubah status payment secara sembarangan.

5. Jika tidak ada informasi yang cukup untuk menghitung expiry:

   * jangan membuat nilai expiry palsu;
   * buat policy eksplisit untuk legacy record;
   * worker harus mengabaikan `expiresAt = null` dengan aman;
   * dokumentasikan bahwa record tersebut membutuhkan policy/manual handling terpisah.

6. Pastikan payment baru selalu mendapatkan `expiresAt`.

   * Audit semua jalur pembuatan payment/session.
   * Jangan sampai ada endpoint/service baru yang masih membuat payment pending tanpa expiry.
   * Jika ada jalur yang belum memberikan expiry, perbaiki pada source of truth-nya.

7. Testing:

   * payment baru selalu memiliki `expiresAt`;
   * payment lama dengan `expiresAt` valid diproses sesuai expiry;
   * payment lama dengan `expiresAt = null` tidak salah menjadi EXPIRED;
   * `expiresAt` yang sudah ada tidak tertimpa;
   * worker tetap idempotent;
   * payment PAID/COMPLETED tetap aman.

8. Jalankan:

   * test payment expiry;
   * test payment/session;
   * typecheck;
   * lint;
   * build.

9. Jangan menyentuh:

   * frontend/UI;
   * payment provider nyata;
   * scheduler production;
   * webhook idempotency kecuali diperlukan untuk kompatibilitas;
   * database production.

PENTING:

* Jangan commit.
* Jangan push GitHub.
* Jangan reset database.
* Jangan membuat README baru.
* Jangan membuat expiry palsu untuk legacy payment.
* Pertahankan arsitektur modular existing.

Setelah selesai, tampilkan:

* policy yang dipilih untuk `expiresAt = null`;
* apakah migration/backfill diperlukan;
* file yang berubah;
* hasil test;
* masalah yang masih tersisa.



```
# 
```
Lanjutkan project toko-online dari kondisi terakhir setelah implementasi webhook idempotency.

Fokus tahap ini hanya pada PAYMENT SESSION EXPIRY dan WORKER/JOB PROCESSING.

Kondisi saat ini:

* Webhook idempotency sudah diimplementasikan.
* Model PaymentWebhookEvent dan unique constraint sudah tersedia.
* Duplicate webhook sudah diuji.
* PostgreSQL concurrency integration test belum tersedia karena belum ada isolated PostgreSQL test database.
* Jangan mengubah bagian tersebut pada tahap ini.

Tugas:

1. Audit terlebih dahulu payment session yang sudah ada.

   * Cari model/schema payment session.
   * Cari field expiry/expiresAt atau equivalent.
   * Cari status payment yang tersedia.
   * Cari endpoint create/get payment session.
   * Cari service yang menangani perubahan status payment.

2. Implementasikan mekanisme expiry yang modular.

   * Payment session yang melewati waktu expiry harus berubah menjadi EXPIRED.
   * Jangan mengubah payment yang sudah PAID/COMPLETED menjadi EXPIRED.
   * Jangan mengubah REFUNDED/CANCELLED atau terminal state lain secara tidak semestinya.
   * Gunakan waktu server/database yang konsisten dengan arsitektur project.
   * Hindari pengecekan expiry hanya di frontend.

3. Buat worker/job yang aman dijalankan berulang.

   * Worker harus mencari payment session yang sudah expired.
   * Proses harus idempotent.
   * Menjalankan worker dua kali tidak boleh menghasilkan efek samping tambahan.
   * Gunakan update/transaction yang aman terhadap race condition.
   * Jangan menggunakan in-memory state sebagai sumber kebenaran.
   * Jangan membuat worker yang hanya berjalan sekali saat startup tanpa mekanisme scheduler yang sesuai existing project.

4. Jika project sudah mempunyai scheduler/job infrastructure:

   * Integrasikan menggunakan infrastructure tersebut.
   * Jangan membuat scheduler kedua yang tidak diperlukan.

   Jika belum ada scheduler:

   * Buat worker/service modular yang dapat dipanggil scheduler/process manager di kemudian hari.
   * Jangan menambahkan dependency scheduler besar tanpa alasan.

5. Pastikan race condition dengan webhook:

   * Jika worker mencoba EXPIRED ketika webhook payment datang hampir bersamaan, state transition harus aman.
   * Worker tidak boleh mengubah payment menjadi EXPIRED setelah payment sudah berhasil menjadi PAID.
   * Gunakan conditional update atau transaction sesuai ORM/database existing.

6. Testing:
   Tambahkan test untuk:

   * session belum expired → tetap pending;
   * session tepat/benar-benar melewati expiry → menjadi EXPIRED;
   * session sudah PAID → tidak berubah menjadi EXPIRED;
   * session CANCELLED/REFUNDED → tidak berubah secara salah;
   * worker dijalankan dua kali → hasil tetap sama;
   * session yang sudah expired tidak diproses berulang kali;
   * kondisi race antara expiry worker dan payment success bila dapat diuji dengan aman.

7. Jangan menggunakan provider payment nyata.

   * Tidak perlu credential provider.
   * Tidak perlu transaksi uang nyata.
   * Gunakan mock/helper/test fixture yang sesuai architecture existing.

8. Jalankan verification:

   * test payment/session;
   * test webhook terkait jika diperlukan untuk memastikan tidak regression;
   * typecheck;
   * lint;
   * build.

9. Jangan memperbaiki warning UI:
   `src/components/ui/select.tsx`
   kecuali warning tersebut menyebabkan build/test gagal.

10. Setelah selesai tampilkan laporan:

* file yang berubah;
* model/status yang digunakan;
* bagaimana expiry ditentukan;
* bagaimana worker dijalankan;
* bagaimana race condition dicegah;
* hasil seluruh test;
* masalah yang masih tersisa.

PENTING:

* Jangan commit.
* Jangan push GitHub.
* Jangan reset database.
* Jangan mengubah frontend/UI.
* Jangan mengintegrasikan provider payment nyata.
* Jangan membuat README baru.
* Jangan mengubah webhook idempotency yang sudah selesai kecuali diperlukan agar expiry worker aman.
* Pertahankan arsitektur modular project existing.



```

# PostgreSQL untuk dua HTTP request concurrent
```


Lanjutkan project toko-online dari hasil implementasi webhook idempotency sebelumnya.

Fokus tahap ini hanya pada VERIFIKASI CONCURRENCY WEBHOOK DENGAN DATABASE PostgreSQL yang digunakan project. Jangan mengimplementasikan fitur payment baru.

Kondisi saat ini:

* Webhook idempotency sudah memiliki 3 test yang passed.
* Duplicate concurrent secara helper/claim behavior sudah diuji.
* Event berbeda tetap dapat diproses.
* Processing failure masih dapat di-retry.
* Duplicate claim ditolak secara atomic.
* Belum ada integration test langsung terhadap PostgreSQL untuk dua HTTP request concurrent.

Tugas:

1. Audit implementasi webhook idempotency yang baru dibuat.

   * Temukan endpoint HTTP webhook.
   * Temukan service/handler idempotency.
   * Temukan schema/model database yang digunakan untuk menyimpan claim/event.
   * Pastikan unique constraint/index benar-benar berada di database, bukan hanya pengecekan di application memory.

2. Buat integration test PostgreSQL hanya jika test infrastructure project sudah mendukung database test.

   * Jangan mengubah database production.
   * Jangan menggunakan credential production.
   * Jangan mereset database development yang sedang digunakan.
   * Jika project belum mempunyai PostgreSQL test infrastructure yang aman, jangan memaksakan setup besar. Laporkan bahwa integration test belum dapat dijalankan dan lakukan static/database-level verification yang tersedia.

3. Jika integration test PostgreSQL dapat dibuat dengan aman, uji:

   * dua HTTP webhook identik dikirim hampir bersamaan;
   * hanya satu request yang memperoleh claim;
   * hanya satu request yang boleh menjalankan efek pemrosesan;
   * request lainnya diperlakukan sebagai duplicate;
   * tidak terjadi duplicate payment/order/stock update;
   * event berbeda tetap dapat diproses;
   * apabila processing pertama gagal dan status memungkinkan retry, webhook berikutnya dapat memproses ulang.

4. Pastikan atomicity benar-benar dijamin database:

   * gunakan unique constraint/index;
   * gunakan transaction atau atomic insert/upsert yang sesuai ORM/database existing;
   * jangan mengganti dengan lock global atau in-memory mutex;
   * jangan membuat solusi yang hanya bekerja pada single-process server.

5. Periksa state idempotency:

   * PROCESSING
   * COMPLETED
   * FAILED atau state equivalent jika memang digunakan project.

   Pastikan crash/retry tidak menyebabkan event dianggap COMPLETED sebelum seluruh efek bisnis selesai.

6. Jalankan:

   * test webhook terkait;
   * integration test PostgreSQL jika tersedia;
   * typecheck;
   * lint;
   * build.

7. Jangan memperbaiki warning UI yang tidak berkaitan dengan payment/webhook pada tahap ini, termasuk warning:
   `src/components/ui/select.tsx`
   kecuali warning tersebut menyebabkan build/test gagal.

8. Setelah selesai tampilkan:

   * hasil test;
   * apakah concurrency PostgreSQL berhasil diverifikasi;
   * jumlah request concurrent yang diuji;
   * hasil claim;
   * file yang berubah;
   * migration/schema yang digunakan;
   * masalah yang masih tersisa.

PENTING:

* Jangan commit.
* Jangan push GitHub.
* Jangan menghapus/reset database.
* Jangan mengubah frontend.
* Jangan implement expiry worker.
* Jangan implement provider payment nyata.
* Jangan membuat README baru.
* Pertahankan struktur dan arsitektur project existing.

```

# 
```
Lanjutkan project toko-online dari kondisi kode saat ini. Fokus hanya pada implementasi WEBHOOK IDEMPOTENCY untuk payment webhook. Jangan mengubah fitur payment lain yang belum diminta dan jangan melakukan commit/push GitHub.

Tujuan:
Webhook dari payment provider tidak boleh diproses dua kali apabila provider mengirim event/transaction yang sama berulang kali. Sistem harus aman terhadap retry webhook, duplicate delivery, dan request concurrent.

Tugas:

1. Audit terlebih dahulu struktur payment/webhook yang sudah ada.

   * Cari endpoint webhook payment.
   * Cari service/controller handler webhook.
   * Cari model/schema database payment/transaction.
   * Cari field provider transaction ID, event ID, reference ID, atau identifier webhook yang sudah tersedia.
   * Gunakan arsitektur existing project dan jangan membuat sistem paralel yang tidak diperlukan.

2. Implementasikan idempotency dengan identifier yang paling tepat dari provider.
   Prioritas:

   * event ID unik jika provider menyediakannya;
   * jika tidak ada, gunakan kombinasi provider + provider transaction ID/reference ID + event/type yang memang menjamin event dapat dibedakan.
   * Jangan menggunakan timestamp sebagai identifier utama.
   * Identifier harus memiliki unique constraint/index di database.

3. Pastikan race condition aman.

   * Dua webhook identik yang masuk hampir bersamaan hanya boleh menghasilkan satu pemrosesan.
   * Jangan hanya melakukan pengecekan "find lalu process" tanpa unique constraint/atomic operation.
   * Gunakan transaction/atomic database operation sesuai ORM/database yang sudah digunakan project.

4. Tentukan behavior duplicate webhook:

   * Jika event sudah berhasil diproses, jangan ulangi perubahan status/payment/stock/order.
   * Return HTTP response yang sesuai dengan pola existing API.
   * Jangan menganggap duplicate sebagai payment baru.
   * Logging harus membedakan webhook baru dengan duplicate webhook.
   * Jangan membocorkan credential atau signature sensitif ke log.

5. Jika webhook pertama gagal sebelum pemrosesan selesai:

   * Jangan membuat sistem menganggap event sudah sukses hanya karena request pertama tercatat.
   * Status idempotency/event harus memungkinkan retry provider diproses kembali apabila memang diperlukan.
   * Pastikan desain tidak menyebabkan payment stuck hanya karena proses pertama crash.

6. Integrasikan dengan state transition payment yang sudah ada.

   * Jangan mengubah payment menjadi PAID hanya karena webhook duplicate.
   * Jangan mengurangi stock/order balance dua kali.
   * Jangan membuat refund/settlement dua kali.
   * Hormati validasi status transition yang sudah ada.

7. Database:

   * Tambahkan migration/schema change jika diperlukan.
   * Gunakan unique index/constraint yang benar.
   * Jangan menghapus data existing.
   * Jangan mereset database development.
   * Pastikan migration dapat dijalankan pada environment existing.

8. Testing:
   Tambahkan atau sesuaikan test untuk minimal:

   * webhook valid pertama → diproses sekali;
   * webhook identik kedua → tidak diproses ulang;
   * duplicate dengan request berurutan;
   * duplicate/concurrent request bila test architecture memungkinkan;
   * event berbeda untuk transaction berbeda → tetap diproses;
   * event gagal diproses → retry masih dapat diproses;
   * tidak ada double update pada payment/order/stock.

   Jangan membuat test yang membutuhkan credential/provider production nyata.

9. Jalankan verification yang relevan:

   * typecheck/build;
   * lint jika tersedia;
   * test yang berkaitan dengan webhook/payment;
   * syntax validation.

   Jangan mengubah konfigurasi global hanya agar test lulus.

10. Setelah selesai, lakukan audit singkat terhadap perubahan:

* file yang diubah;
* migration yang dibuat;
* mekanisme unique/idempotency yang digunakan;
* bagaimana race condition dicegah;
* hasil test/build;
* apakah ada masalah yang belum terselesaikan.

PENTING:

* Jangan melakukan commit.
* Jangan melakukan push GitHub.
* Jangan menghapus atau mereset database.
* Jangan mengubah UI/frontend.
* Jangan mengimplementasikan expiry worker atau payment provider nyata pada tahap ini.
* Jangan membuat file README baru. Jika dokumentasi memang diperlukan, gunakan README.md yang sudah ada.
* Pertahankan modularitas dan pola arsitektur project yang sekarang.
* Jangan melakukan perubahan di luar scope webhook idempotency.

Setelah implementasi selesai, berhenti dan tampilkan laporan hasil audit serta file yang berubah.:::



```
# Prompt: Payment System Foundation
```
PROMPT — PAYMENT SYSTEM FOUNDATION & PAYMENT LIFECYCLE

Project: Digital Cell / toko-online

KONDISI TERKINI

Tahap sebelumnya sudah selesai dan diverifikasi:

- Prisma/PostgreSQL menjadi sumber data utama.
- Catalog menggunakan database.
- Admin catalog/product management sudah terhubung database.
- Product price dan stock berasal dari database.
- Checkout melakukan validasi server-side.
- Order dibuat menggunakan transaction.
- Stock handling sudah atomic sesuai implementasi existing.
- Order ownership protection aktif.
- Order status transition tervalidasi.
- Payment dipisahkan dari order.
- Payment webhook sudah memiliki exact-path dan signature-aware foundation.
- Authentication tetap server-side.
- Authorization tetap server-side.
- npm run typecheck PASS.
- npm run build PASS.
- Runtime development sehat.
- Database development boleh kosong.
- Tidak ada transaksi payment nyata.
- VPS ini hanya DEVELOPMENT dan nantinya dipindahkan ke VPS production.

CATATAN:

Ada kemungkinan idempotency key untuk duplicate order belum tersedia karena membutuhkan perubahan schema/migration. Jangan memaksakan perubahan schema pada tahap ini.

==================================================
TUJUAN
==================================================

Sekarang bangun PAYMENT SYSTEM FOUNDATION yang aman dan modular.

Flow target:

ORDER
 ↓
PAYMENT CREATION
 ↓
PENDING PAYMENT
 ↓
PAYMENT STATUS
 ↓
PAYMENT CONFIRMATION
 ↓
ORDER STATUS UPDATE

Payment provider nyata BELUM diintegrasikan.

Tahap ini fokus pada:

- payment model/service existing
- payment lifecycle
- payment status
- payment/order relationship
- payment amount integrity
- payment authorization
- payment webhook foundation
- admin payment visibility
- security
- idempotent state transition jika dapat dilakukan tanpa migration.

==================================================
1. AUDIT PAYMENT EXISTING
==================================================

Audit terlebih dahulu seluruh:

- payment-service
- payment API
- payment webhook
- payment UI
- order payment relation
- payment status
- payment settings
- admin payment page
- provider configuration
- AppSettings terkait payment.

Cari:

- mock payment
- fake payment success
- hardcoded payment status
- localStorage payment
- dummy payment provider
- client-side payment confirmation
- endpoint yang mengubah payment status tanpa authorization
- payment amount dari browser yang dipercaya server.

Jangan langsung membuat sistem baru sebelum memahami kode existing.

==================================================
2. PRISMA SCHEMA
==================================================

Audit model Prisma yang berkaitan dengan:

- Order
- Payment
- User
- OrderItem
- AppSettings
- PaymentMethod
- provider configuration jika tersedia.

Gunakan schema existing.

Jika schema sudah cukup:

JANGAN ubah schema.

Jika schema tidak cukup untuk kebutuhan minimum:

STOP sebelum migration.

Laporkan:

- model
- field
- relation
- alasan
- dampak.

Jangan membuat migration tanpa approval.

==================================================
3. PAYMENT LIFECYCLE
==================================================

Gunakan status existing jika tersedia.

Jika enum/status sudah ada, jangan membuat enum baru.

Lifecycle harus jelas.

Contoh konsep:

PENDING
→ PAID
→ FAILED

atau:

PENDING
→ EXPIRED

atau:

PENDING
→ CANCELLED

Gunakan status yang memang sudah tersedia di project.

Jangan memaksakan status baru jika tidak diperlukan.

==================================================
4. ORDER VS PAYMENT STATUS
==================================================

Pastikan status order dan payment tidak dicampur.

Contoh:

Order:
PENDING

Payment:
PENDING

Setelah payment confirmed:

Payment:
PAID

Order:
PAID / PROCESSING

Gunakan status existing sesuai arsitektur.

Customer tidak boleh mengubah status tersebut melalui browser.

==================================================
5. CREATE PAYMENT
==================================================

Implementasikan payment creation server-side.

Saat membuat payment:

1. Authenticate user.
2. Ambil order dari database.
3. Pastikan order milik user.
4. Pastikan order masih dapat dibayar.
5. Ambil total order dari database.
6. Jangan percaya amount dari request.
7. Create payment menggunakan amount dari order.
8. Set initial payment status sesuai schema.
9. Return payment information yang aman.

Jangan menerima:

amount dari client sebagai sumber kebenaran.

Client hanya boleh mengirim:

order identifier
dan data yang memang diperlukan.

==================================================
6. PAYMENT AMOUNT INTEGRITY
==================================================

WAJIB.

Payment amount harus berasal dari:

DATABASE ORDER TOTAL

Bukan:

- cart total
- frontend total
- hidden input
- query parameter
- request body amount.

Server harus memastikan:

payment.amount === order.total

sesuai representasi numeric yang digunakan schema.

Jangan menggunakan floating point secara sembarangan jika project menggunakan Decimal.

Gunakan tipe/utility existing untuk money calculation.

==================================================
7. PAYMENT OWNERSHIP
==================================================

User hanya boleh membuat atau melihat payment untuk order miliknya.

Server harus:

- membaca session
- mengambil order
- memverifikasi ownership
- baru mengakses payment.

Jangan percaya:

userId dari request body.

Jangan percaya:

customerId dari frontend.

Admin boleh mengakses payment sesuai authorization admin.

==================================================
8. PAYMENT STATUS TRANSITION
==================================================

Status payment tidak boleh diubah bebas.

Contoh:

PENDING → PAID

boleh jika kondisi valid.

Tetapi:

PAID → PENDING

tidak boleh sembarangan.

PAID → FAILED

jangan diizinkan kecuali lifecycle existing memang mendukung.

Buat transition validation menggunakan logic/service yang modular.

Jangan menyebarkan status transition logic ke banyak API route.

==================================================
9. PAYMENT WEBHOOK FOUNDATION
==================================================

Audit webhook existing.

Pastikan:

- exact endpoint path
- method POST
- payload validation
- provider identification
- signature/token validation jika provider dikonfigurasi
- tidak ada auth user biasa yang dibutuhkan untuk callback provider
- callback tidak dapat digunakan sebagai endpoint umum.

Jika provider belum dikonfigurasi:

webhook harus gagal dengan status yang sesuai.

Jangan membuat webhook menerima callback palsu tanpa validasi.

==================================================
10. WEBHOOK SECURITY
==================================================

Webhook harus memiliki:

- exact path
- method restriction
- payload validation
- provider validation
- signature/token verification sesuai mekanisme existing
- replay/idempotency handling jika schema existing memungkinkan.

Jangan membuat:

/api/payments/webhook/:anything

sebagai catch-all.

Jangan menerima webhook di endpoint umum.

Jangan log:

- API secret
- webhook secret
- authorization token
- full sensitive payload.

==================================================
11. WEBHOOK STATE UPDATE
==================================================

Jika webhook valid menyatakan payment berhasil:

Server harus:

1. menemukan payment
2. memverifikasi payment/order relationship
3. memastikan amount/reference sesuai jika provider menyediakan
4. memastikan status saat ini masih dapat diproses
5. update payment
6. update order sesuai lifecycle
7. menggunakan transaction jika lebih dari satu database write.

Jika webhook dikirim dua kali:

hasil akhirnya harus tetap konsisten.

Jangan membuat order/payment berubah dua kali secara tidak aman.

==================================================
12. DUPLICATE WEBHOOK
==================================================

Audit apakah schema existing memiliki:

- provider transaction ID
- webhook event ID
- external reference
- payment reference.

Jika sudah ada:

gunakan sebagai idempotency key.

Jika belum ada dan membutuhkan migration:

JANGAN membuat migration.

Laporkan:

"Webhook idempotency membutuhkan schema change."

Tetap buat logic seaman mungkin menggunakan state transition yang tersedia.

==================================================
13. PAYMENT CONFIRMATION
==================================================

PENTING:

Jangan membuat endpoint seperti:

POST /api/payments/:id/confirm

yang memungkinkan customer mengatakan:

"payment saya sudah dibayar"

dan server langsung mengubah status menjadi PAID.

Customer tidak boleh menentukan payment success.

Payment success harus berasal dari mekanisme payment yang tervalidasi.

Jika provider belum ada:

payment tetap PENDING.

==================================================
14. PAYMENT METHODS
==================================================

Audit existing payment method architecture.

Jika project sudah memiliki payment methods:

gunakan existing.

Contoh:

- QRIS
- transfer bank
- e-wallet
- manual payment

Tetapi jangan mengintegrasikan provider nyata dulu.

Jika UI sudah memiliki payment method selector:

hubungkan ke backend secara aman.

Jangan membuat payment method baru tanpa schema/config yang sesuai.

==================================================
15. MANUAL PAYMENT FOUNDATION
==================================================

Jika project memang sudah memiliki flow manual payment:

pastikan foundation-nya aman.

Contoh:

Order:
PENDING

Payment:
PENDING

Customer melakukan pembayaran manual.

Tetapi:

PAID tidak boleh ditentukan oleh client.

Admin verification atau provider callback menjadi sumber perubahan status.

Jangan membuat admin verification UI baru jika di luar scope existing.

Cukup pastikan backend siap.

==================================================
16. PAYMENT ADMIN VIEW
==================================================

Jika admin payment UI existing tersedia:

hubungkan dengan database.

Admin dapat melihat:

- payment ID
- order ID
- amount
- method
- status
- createdAt
- updatedAt
- external reference jika ada.

Jangan expose secret.

Jangan expose webhook secret.

Jangan expose provider API key.

Jangan membuat refund system.

==================================================
17. PAYMENT API
==================================================

Audit endpoint existing.

Minimal logic:

CREATE PAYMENT
GET PAYMENT
WEBHOOK

Gunakan struktur API existing.

Jangan membuat endpoint hanya untuk formalitas.

Pastikan private payment endpoint membutuhkan authentication.

Webhook menggunakan mekanisme authentication/signature khusus provider.

==================================================
18. ERROR HANDLING
==================================================

Gunakan status HTTP yang sesuai.

400:
invalid payment request

401:
unauthenticated

403:
unauthorized

404:
order/payment tidak ditemukan

409:
payment state conflict

422:
invalid provider payload jika pola project menggunakan 422

500:
unexpected error

Webhook invalid:

gunakan response yang konsisten dengan implementation existing.

Jangan mengembalikan secret atau stack trace.

==================================================
19. SECURITY AUDIT
==================================================

Periksa:

- IDOR payment
- payment amount manipulation
- order ownership
- status manipulation
- webhook bypass
- webhook catch-all
- replay webhook
- duplicate callback
- secret leakage
- provider token leakage
- mass assignment.

Pastikan request seperti:

{
  "status": "PAID"
}

tidak dapat digunakan customer untuk membayar order secara palsu.

==================================================
20. DATABASE TRANSACTION
==================================================

Jika webhook sukses membutuhkan:

Payment update
+
Order update

gunakan transaction.

Pastikan tidak terjadi kondisi:

Payment = PAID
Order = PENDING

karena salah satu update gagal.

Atau:

Payment = PENDING
Order = PROCESSING

tanpa alasan valid.

Gunakan transaction sesuai Prisma architecture existing.

==================================================
21. FRONTEND PAYMENT PAGE
==================================================

Audit payment page existing.

Jangan redesign.

Pastikan halaman mengambil:

- order
- total
- payment status
- payment method

dari server.

Jangan percaya total dari client.

Jika payment masih PENDING:

tampilkan status pending.

Jangan menampilkan "berhasil" hanya karena endpoint create payment berhasil.

CREATE PAYMENT ≠ PAYMENT SUCCESS.

==================================================
22. PAYMENT POLLING / REFRESH
==================================================

Jika frontend existing melakukan polling:

audit agar tidak terlalu agresif.

Jangan membuat infinite polling.

Jika belum ada polling:

tidak wajib menambahkan sistem polling kompleks pada tahap ini.

Cukup pastikan GET payment status aman.

==================================================
23. NO REAL PROVIDER
==================================================

PENTING:

Jangan:

- menghubungkan Midtrans production
- menghubungkan Xendit production
- menghubungkan QRIS production
- menghubungkan bank API production
- menggunakan API key payment production
- mengirim uang
- membuat transaksi finansial nyata.

Tahap ini hanya foundation.

Provider nyata akan dilakukan pada tahap terpisah setelah backend payment stabil.

==================================================
24. TESTING
==================================================

Jalankan:

npm run typecheck

npm run build

Kemudian test:

1. Create payment untuk order valid.
2. Create payment untuk order milik user lain.
3. Create payment dengan amount manipulasi.
4. Create payment untuk order yang sudah tidak payable.
5. GET payment milik user.
6. GET payment milik user lain.
7. POST webhook method kosong.
8. POST malformed JSON.
9. POST invalid payload.
10. POST webhook tanpa signature/token.
11. POST webhook ke exact path.
12. POST ke /api/payments/webhook/extra.
13. Duplicate webhook jika dapat diuji tanpa provider nyata.
14. Payment state transition invalid.
15. npm run typecheck.
16. npm run build.

Jangan membuat transaksi payment nyata.

Jika database development kosong:

jangan membuat dummy transaction hanya untuk mendapatkan status PASS.

==================================================
25. DATABASE SAFETY
==================================================

JANGAN:

- prisma migrate reset
- DROP DATABASE
- delete migration
- recreate database
- seed dummy payment
- seed dummy order
- menghapus data
- mengganti DATABASE_URL.

Jika migration diperlukan:

STOP.

Jelaskan kebutuhan migration dan tunggu approval.

==================================================
26. MODULAR ARCHITECTURE
==================================================

Jangan membuat satu file besar.

Pisahkan sesuai struktur existing:

- payment service
- payment validation
- payment status transition
- webhook handler
- API/server action
- database access.

Gunakan Prisma client singleton existing.

Jangan membuat PrismaClient baru di setiap request.

Jangan menduplikasi authentication.

Jangan menduplikasi order calculation.

==================================================
27. FINAL REPORT
==================================================

Setelah selesai berikan laporan:

A. PAYMENT

- Create payment
- Payment amount
- Payment status
- Payment/order relationship
- Payment ownership

B. WEBHOOK

- Exact path
- Method restriction
- Payload validation
- Signature/token validation
- State transition
- Duplicate webhook handling

C. SECURITY

- IDOR
- Amount manipulation
- Status manipulation
- Secret leakage
- Authorization

D. DATABASE

- Prisma models digunakan
- Transaction
- Schema changed atau tidak
- Migration changed atau tidak

E. FRONTEND

- Payment page
- Payment status
- Order/payment data source

F. TEST

- typecheck
- build
- webhook tests
- authorization tests
- payment validation tests

G. FILES CHANGED

Tampilkan setiap file yang diubah dan alasan perubahan.

H. REMAINING

Tampilkan fitur yang sengaja belum dikerjakan.

PENTING:

- Jangan commit.
- Jangan push GitHub.
- Jangan reset database.
- Jangan membuat migration tanpa approval.
- Jangan mengubah Cloudflare.
- Jangan mengubah DNS.
- Jangan mengubah port.
- Jangan mengubah UI di luar kebutuhan payment.
- Jangan membuat mock payment sebagai pengganti database.
- Jangan menggunakan payment provider production.
- Jangan melakukan transaksi finansial nyata.

Setelah selesai, BERHENTI dan berikan laporan lengkap.


```

# Prompt: Order & Checkout Management — Database Backend
```
PROMPT — ORDER & CHECKOUT BACKEND INTEGRATION

Project: Digital Cell / toko-online

KONDISI TERKINI

Tahap sebelumnya sudah selesai:

- Prisma/PostgreSQL menjadi sumber data utama.
- Catalog tidak lagi menggunakan mock sebagai sumber data.
- Product listing menggunakan database.
- Product detail menggunakan data server.
- Admin catalog/product management sudah terhubung ke database.
- Category/product/stock/price/status menggunakan backend.
- Authentication dan authorization server-side tetap digunakan.
- Checkout sudah memiliki validasi server-side untuk:
  - product
  - price
  - stock
  - active status
  - quantity
- Order menggunakan database transaction dan ownership protection.
- Payment webhook sudah exact-path dan signature-aware.
- npm run typecheck PASS.
- npm run build PASS.
- Runtime development sehat.
- Database development boleh kosong.
- VPS ini HANYA DEVELOPMENT.
- Nantinya project akan dipindahkan ke VPS production.

Jangan mengubah Cloudflare, DNS, port runtime, atau deployment.

==================================================
TUJUAN TAHAP INI
==================================================

Fokus tahap ini adalah memastikan CORE ORDER dan CHECKOUT benar-benar menggunakan Prisma/PostgreSQL secara konsisten.

Flow yang harus menjadi sumber kebenaran:

PRODUCT DATABASE
      ↓
PRODUCT DETAIL
      ↓
CART
      ↓
CHECKOUT
      ↓
SERVER VALIDATION
      ↓
CREATE ORDER
      ↓
ORDER ITEMS
      ↓
ORDER STATUS
      ↓
PAYMENT STATUS
      ↓
ORDER DETAIL

Jangan mengerjakan payment provider nyata pada tahap ini.

==================================================
1. AUDIT ORDER SYSTEM
==================================================

Audit terlebih dahulu seluruh kode yang berhubungan dengan:

- order-service
- checkout-service
- cart
- order API
- order detail
- order history
- order status
- payment status
- order item
- customer/buyer data
- admin order management.

Cari:

- mock order
- fake order
- localStorage sebagai sumber order
- hardcoded order
- dummy payment
- fake success response
- client-side order creation
- total harga yang dipercaya dari browser.

Jangan langsung mengubah kode sebelum memahami arsitektur existing.

==================================================
2. PRISMA SCHEMA
==================================================

Gunakan Prisma schema existing.

Audit model:

- Order
- OrderItem
- Product
- Category
- User/customer
- Payment jika tersedia
- model lain yang berhubungan dengan checkout.

Pastikan hubungan antar-model dipahami.

JANGAN mengubah Prisma schema jika tidak diperlukan.

Jika schema existing sudah cukup:

- gunakan schema tersebut.

Jika schema tidak cukup:

STOP sebelum migration.

Laporkan:

- model yang kurang
- field yang kurang
- relation yang kurang
- alasan kebutuhan perubahan.

Jangan membuat migration tanpa approval.

==================================================
3. CART
==================================================

Audit cart existing.

Cart boleh berada di client sebagai state sementara.

Tetapi:

CART CLIENT BUKAN SUMBER KEBENARAN TRANSAKSI.

Saat checkout:

Server HARUS mengambil ulang:

- product
- price
- stock
- active status
- quantity limit
- product availability

dari database.

Jangan mempercayai:

- price dari client
- subtotal dari client
- total dari client
- product name dari client
- stock dari client.

Client hanya boleh mengirim identifier dan quantity yang diperlukan.

==================================================
4. CHECKOUT VALIDATION
==================================================

Implementasikan/rapikan validasi checkout server-side.

Untuk setiap item:

1. Cari product dari database.
2. Pastikan product tersedia.
3. Pastikan product active.
4. Pastikan quantity valid.
5. Pastikan stock mencukupi.
6. Ambil harga terbaru dari database.
7. Hitung subtotal di server.

Kemudian:

server menghitung total order sendiri.

Jangan menerima:

subtotal
total
discount
final price

sebagai nilai terpercaya dari browser.

==================================================
5. CREATE ORDER
==================================================

Create order harus dilakukan di server.

Gunakan Prisma transaction jika schema existing mendukung.

Minimal flow:

BEGIN TRANSACTION

- validasi semua product
- validasi stock
- ambil harga database
- hitung subtotal
- hitung total
- create Order
- create OrderItem
- update stock jika desain inventory existing memang menggunakan pengurangan stock saat order
- commit

Jika terjadi error:

ROLLBACK.

Jangan menghasilkan order setengah jadi.

==================================================
6. STOCK CONCURRENCY
==================================================

Periksa race condition.

Contoh:

User A membeli stock terakhir.
User B membeli product yang sama hampir bersamaan.

Jangan hanya:

READ STOCK
↓
CHECK
↓
UPDATE

tanpa mempertimbangkan concurrent checkout.

Gunakan transaction/atomic operation sesuai kemampuan schema dan database existing.

Jangan membuat inventory engine baru.

Gunakan mekanisme sederhana yang aman.

Jika implementasi existing belum memungkinkan atomic stock update:

laporkan keterbatasannya.

==================================================
7. ORDER NUMBER / IDENTIFIER
==================================================

Audit identifier order existing.

Jika sudah tersedia:

gunakan sistem existing.

Jika belum ada tetapi schema sudah memiliki identifier yang sesuai:

gunakan field tersebut.

Jangan membuat sistem nomor order baru jika tidak diperlukan.

Pastikan identifier tidak mudah menyebabkan collision.

Jangan expose database internal ID secara tidak perlu.

==================================================
8. ORDER STATUS
==================================================

Audit status order existing.

Jangan membuat status baru jika enum/status existing sudah cukup.

Pastikan lifecycle order konsisten.

Contoh konsep:

PENDING
→ PAID
→ PROCESSING
→ COMPLETED

atau status yang memang sudah digunakan project.

Jangan memaksakan status di atas jika schema existing memiliki sistem berbeda.

Yang penting:

- status valid
- transition aman
- tidak bisa dimanipulasi customer melalui request.

Customer tidak boleh bebas mengubah:

- paid
- completed
- processing
- cancelled

melalui endpoint client.

==================================================
9. PAYMENT STATUS
==================================================

Payment belum menjadi fokus integrasi provider nyata.

Tetapi order harus memiliki pemisahan yang jelas antara:

ORDER STATUS

dan

PAYMENT STATUS

jika schema existing mendukungnya.

Jangan menganggap:

"checkout berhasil"

berarti:

"payment berhasil".

Checkout hanya membuat order.

Payment confirmation dilakukan oleh payment system/webhook pada tahap berikutnya.

==================================================
10. ORDER OWNERSHIP
==================================================

Pastikan user hanya dapat melihat order miliknya sendiri.

Contoh:

GET /api/orders/:id

Server harus:

1. membaca session user
2. mengambil order
3. memastikan ownership
4. baru mengembalikan data.

Jangan percaya:

userId dari URL
userId dari body
userId dari query
customerId dari browser.

Jika admin memiliki akses:

gunakan authorization admin server-side.

==================================================
11. ORDER DETAIL
==================================================

Order detail harus berasal dari database.

Tampilkan data yang memang tersedia:

- order identifier
- status
- payment status jika ada
- products
- quantity
- price
- subtotal
- total
- buyer information jika sesuai ownership
- createdAt
- updatedAt

Jangan membuat data dummy.

Jika order belum ada:

return empty/not-found state yang benar.

==================================================
12. ORDER HISTORY
==================================================

Jika UI sudah memiliki halaman riwayat order:

hubungkan dengan database.

User harus melihat:

HANYA order miliknya.

Gunakan pagination jika arsitektur existing sudah mendukungnya.

Jangan mengambil semua order lalu melakukan filtering hanya di browser.

Filtering ownership harus terjadi server-side.

==================================================
13. ADMIN ORDER VIEW
==================================================

Jika admin UI existing memiliki order management:

hubungkan ke database.

Admin dapat melihat order sesuai authorization.

Admin dapat melihat:

- order
- customer
- items
- total
- payment status
- order status.

Jangan menambahkan fitur payment provider.

Jangan membuat refund system.

Jangan membuat settlement system.

==================================================
14. API SECURITY
==================================================

Audit endpoint order.

Pastikan:

GET private order
→ authentication required

CREATE order
→ authentication sesuai arsitektur existing

UPDATE order
→ authorization required

DELETE order
→ jangan diizinkan sembarangan.

Jangan membuat:

?admin=true

atau:

{ "role": "admin" }

sebagai authorization.

Jangan percaya data privilege dari client.

==================================================
15. INPUT VALIDATION
==================================================

Validasi semua input:

- product ID
- quantity
- order ID
- customer data
- notes jika tersedia.

Rules:

quantity:
- integer
- > 0
- batas maksimum sesuai kebutuhan existing.

String:
- trim
- panjang maksimum
- tidak menerima input berlebihan.

ID:
- format valid
- resource harus benar-benar ada.

Jika project sudah menggunakan validation library:

gunakan yang existing.

Jangan menambahkan dependency baru tanpa kebutuhan.

==================================================
16. PRICE INTEGRITY
==================================================

Ini WAJIB.

Pastikan user tidak dapat:

- mengubah harga menjadi 0
- mengubah harga menjadi negatif
- mengirim harga lama
- mengirim total palsu
- memanipulasi discount
- memanipulasi subtotal.

Server harus selalu:

DATABASE PRICE
↓
SERVER CALCULATION
↓
ORDER SNAPSHOT/TOTAL

Jika schema existing memiliki snapshot harga pada OrderItem:

gunakan mekanisme tersebut.

Jangan mengubah schema tanpa approval.

==================================================
17. TRANSACTION SAFETY
==================================================

Jika create order membutuhkan beberapa operasi database:

gunakan transaction.

Contoh:

create Order
+
create OrderItems
+
stock update

harus konsisten.

Jika salah satu gagal:

rollback.

Jangan meninggalkan:

Order ada tetapi OrderItem tidak ada.

atau:

Stock berkurang tetapi Order gagal dibuat.

==================================================
18. ERROR HANDLING
==================================================

Gunakan status HTTP yang tepat.

400:
invalid checkout

401:
unauthenticated

403:
unauthorized

404:
product/order tidak ditemukan

409:
stock conflict / duplicate / state conflict

422:
validation failure jika pola project menggunakan 422

500:
unexpected error.

Jangan mengembalikan:

- DATABASE_URL
- Prisma stack trace
- internal server path
- secret
- credentials.

==================================================
19. FRONTEND CHECKOUT
==================================================

Audit checkout UI existing.

Jangan redesign.

Pertahankan:

- layout
- warna
- typography
- component structure.

Perbaiki hanya integrasi data.

Pastikan:

Loading
Error
Success
Empty cart
Out of stock
Product unavailable

memiliki state yang benar.

Setelah order berhasil:

UI harus menggunakan order ID/identifier yang diberikan server.

Jangan membuat order ID palsu di frontend.

==================================================
20. DUPLICATE SUBMISSION
==================================================

Audit kemungkinan user menekan tombol checkout berkali-kali.

Contoh:

User klik:

"Bayar / Buat Pesanan"

beberapa kali dengan cepat.

Pastikan tidak mudah membuat duplicate order.

Gunakan mekanisme existing jika sudah tersedia.

Jika idempotency belum ada dan schema/API belum mendukung:

jangan membuat sistem kompleks tanpa kebutuhan.

Laporkan risiko dan solusi yang diperlukan.

==================================================
21. NO REAL PAYMENT
==================================================

PENTING:

Jangan:

- menghubungkan payment provider baru
- mengirim payment nyata
- mengirim webhook provider nyata
- membuat transaksi finansial nyata
- menggunakan API key payment production.

Tahap ini hanya:

ORDER
+
CHECKOUT
+
DATABASE
+
VALIDATION

==================================================
22. TESTING
==================================================

Setelah implementasi jalankan:

1. npm run typecheck

2. npm run build

3. Pastikan runtime development sehat.

Kemudian test read-only terlebih dahulu.

Test:

- GET products
- GET product detail
- GET orders
- GET order detail
- unauthorized access
- ownership protection
- invalid product
- invalid quantity
- unavailable product
- insufficient stock
- manipulated price
- manipulated total.

Jika database development kosong:

JANGAN membuat dummy transaction hanya untuk testing.

Gunakan test yang aman sesuai data existing.

==================================================
23. DATABASE SAFETY
==================================================

PENTING:

Jangan:

- prisma migrate reset
- DROP DATABASE
- delete migration
- recreate database
- seed dummy transaction
- menghapus data existing
- mengganti DATABASE_URL.

Database development saat ini boleh kosong.

==================================================
24. FILE MODULARITY
==================================================

Jangan membuat satu file besar.

Pisahkan logic sesuai struktur existing:

- service
- validation
- API/server action
- database
- UI.

Gunakan module existing jika sudah tersedia.

Jangan menduplikasi:

- Prisma client
- auth logic
- validation logic
- order calculation.

==================================================
25. FINAL VERIFICATION
==================================================

Setelah selesai, berikan laporan:

A. CHECKOUT

- Client cart
- Server validation
- Price validation
- Stock validation
- Product status validation

B. ORDER

- Order creation
- Order items
- Transaction
- Ownership
- Status

C. SECURITY

- Authentication
- Authorization
- IDOR protection
- Price manipulation protection
- Stock manipulation protection
- Duplicate submission handling

D. DATABASE

- Prisma models digunakan
- Transaction digunakan atau tidak
- Migration berubah atau tidak
- Schema berubah atau tidak

E. TEST

- npm run typecheck
- npm run build
- runtime
- API tests
- security tests

F. FILES CHANGED

Tampilkan setiap file yang diubah beserta alasannya.

G. REMAINING

Tampilkan fitur yang belum dikerjakan.

PENTING:

- Jangan commit.
- Jangan push GitHub.
- Jangan reset database.
- Jangan membuat migration tanpa approval.
- Jangan mengubah Cloudflare.
- Jangan mengubah port.
- Jangan mengubah UI yang tidak diperlukan.
- Jangan membuat mock data.
- Jangan membuat transaksi payment nyata.
- Jangan mengerjakan payment provider production.

Setelah selesai, BERHENTI dan berikan laporan lengkap.


```
# Prompt: Implementasi Admin Catalog & Manajemen Produk
```

PROMPT — ADMIN CATALOG & PRODUCT MANAGEMENT

Project: Digital Cell / toko-online

KONDISI TERKINI

Core integration sudah selesai dan diverifikasi.

Status:
- Next.js runtime berjalan.
- Prisma/PostgreSQL menjadi sumber data utama.
- /api/products menggunakan database.
- Catalog tidak menggunakan mock data.
- Product detail menggunakan data server.
- Checkout melakukan validasi harga, stock, active status, dan quantity di server.
- Order menggunakan database transaction dan ownership protection.
- Payment webhook tetap exact-path dan signature-aware.
- Authentication tetap aktif.
- Prisma schema tidak diubah pada tahap sebelumnya.
- Migration existing tidak dihapus/reset.
- npm run typecheck PASS.
- npm run build PASS.
- Development runtime sehat.
- Database development boleh tetap kosong.
- VPS ini hanya untuk DEVELOPMENT dan nantinya project dipindahkan ke VPS production.

TUJUAN

Tahap berikutnya adalah membangun dan menghubungkan ADMIN CATALOG MANAGEMENT dengan backend database yang sudah ada.

Fokus utama:

1. Categories
2. Products
3. Product stock
4. Product status
5. Product pricing
6. Product images
7. Basic product management
8. Admin authorization

Jangan mengubah UI storefront yang sudah ada kecuali memang diperlukan agar data admin yang baru dibuat dapat tampil dengan benar.

==================================================
1. AUDIT ADMIN EXISTING
==================================================

Sebelum coding:

Audit seluruh folder/file yang berhubungan dengan:

- /admin
- admin dashboard
- category management
- product management
- product forms
- stock management
- settings terkait catalog
- authentication admin
- API admin
- server actions admin

Cari:

- mock data
- hardcoded products
- hardcoded categories
- local JSON
- localStorage yang digunakan sebagai database
- placeholder CRUD
- fake success response
- endpoint yang belum terhubung database.

Jangan langsung membuat file baru sebelum memahami struktur existing.

==================================================
2. ADMIN AUTHORIZATION
==================================================

Pastikan halaman dan endpoint admin benar-benar protected.

Rules:

- User biasa tidak boleh mengakses admin management.
- Authentication harus diverifikasi di server.
- Authorization harus dilakukan server-side.
- Jangan hanya menyembunyikan tombol admin di frontend.
- Jangan percaya role yang dikirim dari browser.
- Jangan membuat bypass authentication.

Jika sistem role/admin existing sudah tersedia:

- gunakan sistem tersebut.
- jangan membuat sistem role baru.

Jika admin authorization belum lengkap tetapi schema existing mendukungnya:

- integrasikan menggunakan schema existing.

Jika membutuhkan perubahan Prisma schema:

STOP sebelum membuat migration.

Laporkan:
- model yang kurang
- field yang diperlukan
- alasan
- perubahan yang direncanakan

Jangan membuat migration otomatis tanpa approval.

==================================================
3. CATEGORY MANAGEMENT
==================================================

Implementasikan CRUD category jika schema Prisma sudah mendukung.

Admin harus dapat:

- melihat kategori
- membuat kategori
- mengubah kategori
- mengaktifkan/nonaktifkan kategori
- menghapus kategori jika aman

Validasi:

- nama wajib
- slug harus valid
- slug harus unik jika schema mensyaratkannya
- jangan membuat duplicate category
- jangan menghapus kategori yang masih digunakan produk tanpa handling yang benar.

Jika delete tidak aman karena foreign key:

gunakan pendekatan yang sesuai schema existing.

Jangan menghapus data secara paksa.

==================================================
4. PRODUCT MANAGEMENT
==================================================

Admin harus dapat mengelola produk menggunakan database.

Field yang tersedia di schema dapat mencakup:

- name
- slug
- description
- category
- price
- discount
- stock
- image
- status
- active
- sold count
- metadata

Gunakan HANYA field yang benar-benar tersedia pada Prisma schema.

Jangan menambahkan field hanya karena frontend menginginkannya.

Admin harus dapat:

- melihat daftar produk
- search produk
- filter kategori
- filter status
- membuat produk
- mengubah produk
- mengaktifkan/nonaktifkan produk
- mengubah harga
- mengubah stock
- melihat detail produk.

==================================================
5. PRODUCT PRICE
==================================================

Harga adalah data sensitif terhadap checkout.

Rules:

- harga disimpan di database.
- frontend admin hanya mengirim input.
- server melakukan validasi.
- checkout tetap mengambil harga dari database.
- jangan percaya total dari browser.

Jika ada discount:

- validasi discount di server.
- jangan sampai discount menghasilkan nilai negatif.
- jangan sampai total checkout dapat dimanipulasi client.

Jangan mengubah aturan checkout yang sudah berhasil.

==================================================
6. STOCK MANAGEMENT
==================================================

Stock harus berasal dari database.

Admin dapat:

- melihat stock
- mengubah stock
- mengaktifkan/nonaktifkan produk berdasarkan status existing
- melihat produk habis stock.

Rules:

- stock tidak boleh negatif.
- quantity harus integer valid.
- checkout tetap melakukan validasi ulang.
- jangan mengandalkan stock dari frontend.

Jangan membuat sistem inventory kompleks jika schema existing belum mendukungnya.

Implementasikan hanya kebutuhan dasar yang aman.

==================================================
7. PRODUCT IMAGE
==================================================

Audit cara frontend saat ini menyimpan product image.

Jika sistem existing sudah memiliki:

- URL image
- path image
- storage provider

gunakan mekanisme existing.

Jangan menambahkan image hosting baru.

Jika image upload belum tersedia:

jangan membuat sistem storage baru hanya untuk prompt ini.

Untuk sementara gunakan mekanisme image yang sudah didukung schema/application.

==================================================
8. ADMIN API / SERVER ACTION
==================================================

Gunakan pola arsitektur existing.

Jangan membuat API route hanya untuk formalitas.

Untuk setiap operasi:

CREATE:
- validate input
- authorize admin
- write database
- return result

UPDATE:
- authorize admin
- validate ID
- validate input
- update database

DELETE:
- authorize admin
- cek dependency
- lakukan operasi aman

READ:
- authorize admin jika data memang private
- gunakan pagination jika daftar besar.

Jangan expose secret/database information.

==================================================
9. VALIDATION
==================================================

Semua input admin harus divalidasi server-side.

Validasi:

- string kosong
- panjang string
- number
- integer
- price
- stock
- category ID
- product ID
- slug
- status.

Jika project sudah menggunakan validation library:

gunakan library existing.

Jangan menambahkan dependency baru jika tidak diperlukan.

==================================================
10. ERROR HANDLING
==================================================

Gunakan HTTP status yang benar.

400:
invalid input

401:
belum login

403:
bukan admin

404:
resource tidak ditemukan

409:
duplicate/conflict

500:
unexpected server error

Jangan mengembalikan stack trace ke frontend.

Error message harus aman dan mudah dipahami.

==================================================
11. FRONTEND ADMIN
==================================================

Hubungkan UI admin existing dengan backend.

Jangan redesign.

Pertahankan:

- layout
- warna
- typography
- sidebar
- navigation
- component style.

Perbaiki hanya bagian yang diperlukan agar CRUD bekerja.

Pastikan loading state tersedia.

Pastikan error state tersedia.

Pastikan empty state tersedia.

Pastikan setelah:

CREATE
UPDATE
DELETE

data UI diperbarui dengan benar.

==================================================
12. STORE FRONTEND
==================================================

Setelah admin catalog selesai:

verifikasi storefront.

Flow:

Admin membuat product
        ↓
PostgreSQL
        ↓
Product API
        ↓
Storefront
        ↓
Product detail
        ↓
Cart
        ↓
Checkout

Tidak boleh ada mock data yang mengambil alih data database.

Jika database kosong:

storefront harus tetap menampilkan empty state dengan benar.

==================================================
13. SECURITY AUDIT
==================================================

Periksa:

- IDOR
- admin authorization
- product update authorization
- category update authorization
- price manipulation
- stock manipulation
- mass assignment
- hidden admin endpoints
- secret leakage.

Jangan mempercayai:

- role dari request body
- userId dari request body
- admin=true dari browser.

Gunakan session/authentication server-side.

==================================================
14. DATABASE SAFETY
==================================================

PENTING:

Jangan:

- prisma migrate reset
- DROP DATABASE
- delete migration
- recreate database
- mengganti DATABASE_URL
- mengganti PostgreSQL
- menghapus data existing.

Database development saat ini boleh kosong.

Jangan membuat seed data otomatis.

Jangan membuat dummy product hanya agar UI terlihat berisi.

==================================================
15. TESTING
==================================================

Setelah implementasi:

1. npm run typecheck

2. npm run build

3. Pastikan runtime tetap berjalan.

4. Test read endpoint product.

5. Test category read.

6. Test admin authorization tanpa login.

7. Test admin authorization dengan user non-admin jika memungkinkan.

8. Test validation invalid product.

9. Test validation invalid stock.

10. Test validation invalid price.

11. Test duplicate category/product slug jika applicable.

12. Test create/update/delete menggunakan database development hanya jika aman.

Jangan melakukan transaksi payment nyata.

Jangan menjalankan load test.

Jangan menjalankan stress test.

==================================================
16. JANGAN UBAH SCOPE
==================================================

Jangan mengerjakan:

- email delivery
- notification system
- reviews
- favorites
- coupon engine kompleks
- payment provider integration baru
- analytics kompleks
- production deployment
- Cloudflare production migration.

Fokus hanya:

ADMIN
→ CATEGORY
→ PRODUCT
→ STOCK
→ PRICE
→ STATUS
→ DATABASE
→ STOREFRONT INTEGRATION

==================================================
17. PRISMA SCHEMA
==================================================

Gunakan schema Prisma yang sudah ada.

Jika schema sudah mencukupi:

JANGAN ubah schema.

Jika schema ternyata tidak mencukupi:

BERHENTI sebelum migration.

Tampilkan:

- model yang kurang
- field yang kurang
- alasan
- dampak ke frontend/backend
- migration yang diperlukan.

Jangan membuat migration tanpa approval.

==================================================
18. FINAL REPORT
==================================================

Setelah selesai tampilkan:

A. ADMIN

Category:
- READ
- CREATE
- UPDATE
- DELETE

Product:
- READ
- CREATE
- UPDATE
- DELETE

Stock:
- READ
- UPDATE

Price:
- READ
- UPDATE

Status:
- READ
- UPDATE

B. STOREFRONT

- Product listing
- Search
- Category filter
- Product detail
- Stock
- Price

C. SECURITY

- Authentication
- Admin authorization
- Input validation
- IDOR protection
- Price protection
- Stock protection

D. DATABASE

- Prisma connection
- Models used
- Migration status
- Schema changed atau tidak

E. TEST

- typecheck
- build
- runtime
- endpoint tests
- authorization tests

F. FILES CHANGED

Tampilkan setiap file yang diubah dan alasan perubahan.

G. REMAINING

Tampilkan fitur yang belum dikerjakan.

PENTING:

Jangan commit.
Jangan push GitHub.
Jangan reset database.
Jangan membuat migration tanpa approval.
Jangan mengubah UI tanpa alasan teknis.
Jangan membuat mock data.
Jangan membuat dummy transaction.

Setelah semua selesai, BERHENTI dan berikan laporan.

```
# Prompt: Integrasi Core Backend & Frontend Digital Cell
```
Prompt: Integrasi Core Backend & Frontend Digital Cell

Project: Digital Cell / toko-online

KONDISI SAAT INI

Project sudah melewati audit database dan runtime development.

Status yang sudah diverifikasi:
- Next.js production runtime aktif pada port 3000.
- localhost:3000/ -> HTTP 200.
- localhost:3000/api/products -> HTTP 200.
- PostgreSQL terhubung melalui Prisma.
- Semua migration Prisma yang diperlukan sudah diterapkan.
- Prisma schema valid.
- Database saat ini masih kosong.
- /api/products sudah menggunakan Prisma/PostgreSQL, bukan mock/static data.
- Payment webhook sudah menggunakan POST.
- Payment webhook tidak melewati login user biasa.
- Bypass authentication hanya berlaku pada exact webhook path.
- Webhook memiliki validasi payload/signature sesuai implementasi.
- /api/payments/webhook berhasil diverifikasi.
- npm run typecheck -> PASS.
- npm run build -> PASS.
- Cloudflare Tunnel tetap berjalan untuk kebutuhan development/testing.
- VPS ini hanya DEVELOPMENT sementara, bukan production.
- Tidak perlu melakukan optimasi production VPS.
- Nantinya project akan dipindahkan ke VPS production baru.

HASIL AUDIT SERVICE SEBELUMNYA

Service inti sudah terhubung atau disiapkan untuk Prisma/PostgreSQL.

Service tambahan seperti:
- favorites
- reviews
- addresses
- coupons
- admin user management
- email delivery
- notifications
- reports
- legacy auth integration

belum semuanya menjadi prioritas dan tidak boleh dipaksakan untuk dibuat jika belum diperlukan oleh flow utama.

TUJUAN TAHAP INI

Sekarang fokus pada integrasi CORE E-COMMERCE.

Jangan membuat ulang project.
Jangan mengubah desain UI yang sudah ada.
Jangan mengganti database.
Jangan menggunakan mock data sebagai pengganti database.
Gunakan Prisma/PostgreSQL sebagai sumber data utama untuk data dinamis.

==================================================
TAHAP 1 — AUDIT FRONTEND TERHADAP BACKEND
==================================================

1. Audit seluruh frontend yang berhubungan dengan:
   - products
   - categories
   - product detail
   - cart
   - checkout
   - orders
   - payment
   - account/user

2. Cari:
   - fetch()
   - axios
   - server actions
   - API client
   - hardcoded product
   - hardcoded category
   - local JSON
   - dummy array
   - placeholder response
   - localStorage yang digunakan sebagai pengganti database

3. Buat mapping:

   FRONTEND FEATURE
   -> API/SERVER LOGIC
   -> SERVICE
   -> PRISMA MODEL

4. Jangan langsung mengubah kode sebelum memahami mapping tersebut.

==================================================
TAHAP 2 — PRODUCT
==================================================

Pastikan product catalog benar-benar berasal dari PostgreSQL.

Implementasikan/verifikasi:

- daftar produk
- pagination
- category filter
- search
- product detail
- stock/status produk
- price
- promo/discount jika schema mendukung
- sold count jika schema mendukung
- product image
- active/inactive

Rules:

- Jangan membuat dummy product.
- Jika database kosong, UI harus menampilkan empty state yang benar.
- Jangan memasukkan seed data otomatis.
- Jangan mengubah Prisma schema jika model existing sudah mencukupi.
- Gunakan query Prisma yang sesuai.
- Hindari N+1 query.
- Validasi pagination dan search input.

Pastikan:

GET /api/products

tetap HTTP 200.

==================================================
TAHAP 3 — CATEGORY
==================================================

Audit category flow.

Pastikan kategori:

- berasal dari database jika memang dinamis
- dapat digunakan untuk filter produk
- tidak bergantung pada hardcoded category list
- memiliki empty state jika tidak ada kategori

Jika API category belum ada tetapi frontend memang membutuhkannya:

- buat endpoint yang diperlukan saja
- gunakan Prisma
- jangan membuat endpoint formalitas yang tidak digunakan frontend.

==================================================
TAHAP 4 — PRODUCT DETAIL
==================================================

Pastikan ketika user membuka produk:

- product diambil berdasarkan identifier yang benar
- data berasal dari PostgreSQL
- produk tidak ditemukan menghasilkan response yang sesuai
- produk inactive tidak dapat dibeli
- stock diperiksa sebelum checkout
- harga berasal dari database

Jangan percaya harga yang dikirim oleh browser.

Harga final checkout harus diverifikasi ulang dari database/server.

==================================================
TAHAP 5 — CART
==================================================

Audit implementasi cart.

Tentukan apakah cart saat ini:

- hanya client-side state
- localStorage
- database
- atau kombinasi keduanya.

Jangan mengubah arsitektur cart secara besar-besaran jika belum diperlukan.

Yang penting:

- quantity divalidasi
- product identifier divalidasi
- harga tidak dipercaya dari client
- product inactive tidak dapat ditambahkan
- product yang sudah tidak tersedia ditangani dengan benar.

Jika cart memang sengaja client-side, pertahankan.

==================================================
TAHAP 6 — CHECKOUT
==================================================

Ini bagian penting.

Pastikan checkout menggunakan server-side validation.

Saat checkout:

1. Client mengirim product identifier dan quantity.
2. Server mengambil product dari PostgreSQL.
3. Server memeriksa:
   - product exists
   - active
   - stock
   - quantity valid
4. Server mengambil harga terbaru dari database.
5. Server menghitung subtotal.
6. Server menghitung discount jika memang tersedia.
7. Server menghitung total final.
8. Server membuat order menggunakan Prisma.
9. Order item dibuat berdasarkan data database.
10. Snapshot checkout yang sudah tersedia harus digunakan sesuai schema.

Jangan menerima:
- total harga dari client
- harga produk dari client
- discount final dari client
sebagai sumber kebenaran.

Gunakan database sebagai source of truth.

==================================================
TAHAP 7 — ORDER
==================================================

Audit order-service.

Pastikan order:

- dibuat melalui Prisma
- memiliki order identifier
- memiliki status
- memiliki total
- memiliki customer information sesuai schema
- memiliki order items
- memiliki snapshot data checkout jika model mendukungnya

Gunakan transaction Prisma jika pembuatan order membutuhkan beberapa operasi database yang harus atomik.

Jangan membuat order dummy saat testing.

Untuk testing:

- gunakan database kosong
- gunakan read-only verification jika memungkinkan
- jangan membuat transaksi nyata tanpa instruksi eksplisit.

==================================================
TAHAP 8 — PAYMENT
==================================================

Audit payment-service setelah webhook selesai.

Pastikan:

- payment terkait dengan order yang benar
- payment status tidak dapat diubah sembarang dari frontend
- callback provider masuk melalui webhook
- webhook melakukan validasi signature/secret
- status order/payment diperbarui secara transactional jika diperlukan
- duplicate webhook aman/idempotent
- retry provider tidak membuat payment/order duplikat

Jangan menghubungkan provider payment nyata hanya untuk testing.

Jangan mengirim pembayaran nyata.

Jangan membuat mock payment yang terlihat seperti transaksi sukses.

==================================================
TAHAP 9 — AUTH & USER
==================================================

Audit auth-service dan user-service.

Pastikan:

- authentication tetap menggunakan mekanisme existing
- user session tetap berjalan
- endpoint protected tetap protected
- webhook tetap menjadi pengecualian exact path saja
- user tidak dapat mengakses order milik user lain
- user ID diambil dari session/server authentication, bukan dipercaya dari request body.

Jangan mengganti sistem authentication secara besar-besaran.

==================================================
TAHAP 10 — ERROR HANDLING
==================================================

Periksa response API.

Gunakan status HTTP yang masuk akal:

200:
successful read/update

201:
successful creation

400:
invalid input

401:
unauthenticated

403:
authenticated tetapi tidak memiliki akses

404:
resource tidak ditemukan

409:
conflict seperti stock berubah atau duplicate operation

500:
unexpected server error

Jangan mengembalikan stack trace atau secret ke browser.

==================================================
TAHAP 11 — SECURITY
==================================================

Audit:

- authentication
- authorization
- input validation
- price manipulation
- quantity manipulation
- IDOR
- order ownership
- payment webhook
- secret exposure
- DATABASE_URL exposure
- environment variables
- API error leakage

Pastikan:

- DATABASE_URL tidak masuk client bundle.
- secret tidak dikirim ke frontend.
- webhook secret tidak dikirim ke frontend.
- user hanya dapat melihat order miliknya sendiri.
- admin endpoint tetap protected.

==================================================
TAHAP 12 — PERFORMANCE UNTUK VPS DEVELOPMENT
==================================================

VPS ini hanya development dan memiliki RAM terbatas.

Jangan menjalankan proses berat secara paralel.

Jangan:
- menjalankan banyak Next.js instance
- menjalankan build berulang-ulang tanpa kebutuhan
- menjalankan migration reset
- menjalankan database seed besar
- menjalankan load test
- menjalankan stress test
- menjalankan browser automation berat.

Gunakan pemeriksaan ringan terlebih dahulu.

==================================================
TAHAP 13 — TESTING
==================================================

Setelah implementasi:

1. Jalankan typecheck.
2. Jika typecheck PASS, lanjutkan build satu kali.
3. Pastikan production build PASS.
4. Pastikan runtime development tetap berjalan.
5. Verifikasi:

GET /
GET /api/products

6. Verifikasi endpoint category jika memang tersedia.
7. Verifikasi product detail dengan data kosong tanpa membuat dummy data.
8. Verifikasi checkout validation menggunakan request invalid yang aman.
9. Verifikasi payment webhook dengan payload test yang tidak memicu transaksi nyata.

Jangan membuat data dummy.

Jika database kosong, hasil seperti:

data: []
total: 0

adalah hasil yang valid.

==================================================
TAHAP 14 — JANGAN MENGUBAH HAL DI LUAR SCOPE
==================================================

Jangan mengubah:

- UI design
- warna
- layout
- branding Digital Cell
- Cloudflare DNS
- Cloudflare Tunnel
- port development
- VPS configuration
- database provider
- migration lama

kecuali perubahan tersebut benar-benar diperlukan untuk memperbaiki core integration.

Jangan melakukan:
- prisma migrate reset
- database drop
- database replacement
- migration deletion
- Git commit
- Git push

==================================================
TAHAP 15 — HASIL AKHIR
==================================================

Sebelum berhenti, berikan laporan:

A. FRONTEND
- Product
- Category
- Product detail
- Cart
- Checkout
- Order
- Payment
- Account

Status masing-masing:
CONNECTED / PARTIAL / NOT CONNECTED

B. BACKEND
- Product service
- Category service
- Order service
- Payment service
- User service
- Auth service

Status masing-masing.

C. DATABASE
- Prisma connection
- Migration
- Model yang digunakan
- Apakah schema berubah

D. SECURITY
- Authentication
- Authorization
- Webhook
- Price validation
- Order ownership

E. TEST
- typecheck
- build
- GET /
- GET /api/products
- endpoint lain yang diverifikasi

F. FILE CHANGED
Tampilkan:
- file yang diubah
- perubahan utama
- alasan perubahan

G. REMAINING
Tampilkan fitur yang sengaja belum dikerjakan dan alasannya.

PENTING:

Kerjakan secara bertahap.
Jangan melakukan perubahan besar tanpa audit terlebih dahulu.
Jika menemukan masalah yang membutuhkan perubahan Prisma schema, BERHENTI dan laporkan terlebih dahulu sebelum membuat migration baru.

Jika semua schema yang ada sudah mencukupi, jangan membuat schema baru.

Jika ada fitur yang belum dibutuhkan oleh core flow, jangan dipaksakan.

Jangan commit atau push GitHub.

Berhenti setelah laporan akhir diberikan agar hasil dapat diverifikasi sebelum tahap berikutnya.


```

# Prompt: Verifikasi Production Server
```

Prompt: Verifikasi Production Server

Fokus hanya pada production runtime.

1. Audit proses Next.js yang sedang berjalan dan port yang digunakan.
2. Jangan mematikan proses yang bukan dibuat oleh task ini.
3. Jangan mengubah port secara sembarangan.
4. Tentukan port production yang aman digunakan aplikasi.
5. Verifikasi aplikasi production dapat diakses secara lokal melalui localhost.
6. Pastikan / dan /api/products merespons HTTP 200.
7. Pastikan Cloudflare Tunnel tetap tidak terganggu.
8. Jangan mengubah DNS atau konfigurasi Cloudflare.
9. Jangan mengubah database.
10. Jangan mengubah UI.
11. Jangan commit atau push GitHub.

Jika npm start tidak bisa dijalankan karena port sedang digunakan, jangan kill proses. Identifikasi proses yang menggunakan port tersebut dan laporkan.

Jalankan typecheck/build hanya jika diperlukan, jangan melakukan pekerjaan berat yang tidak perlu.

Laporkan:
- proses Next.js aktif
- port yang digunakan
- hasil localhost /
- hasil localhost /api/products
- apakah production runtime sehat.

```
# Prompt: Verifikasi Payment Webhook
```

Verifikasi hasil implementasi payment webhook yang baru.

1. Periksa route /api/payments/webhook.
2. Pastikan method POST tersedia.
3. Pastikan exact path webhook melewati bypass auth yang benar.
4. Kirim POST test aman tanpa transaksi nyata dan tanpa memanggil provider payment nyata.
5. Pastikan request tanpa signature/secret ditolak jika validasi signature memang diwajibkan.
6. Pastikan request test yang valid dapat mencapai handler webhook.
7. Jangan membuat order/payment baru.
8. Jangan mengubah database schema.
9. Jangan mengubah UI.
10. Jalankan npm run typecheck dan npm run build.
11. Laporkan HTTP status dan hasil masing-masing test.
12. Jangan commit atau push GitHub.

```
# 
```

Prompt: Perbaiki Payment Webhook

Fokus hanya pada payment webhook.

Temuan:
GET /api/payments/webhook -> HTTP 401
Pesan: ditangani auth guard catch-all; webhook belum memiliki implementation.

Tugas:
1. Audit route /api/payments/webhook dan auth guard/middleware yang memblokirnya.
2. Tentukan method webhook yang benar (POST jika sesuai arsitektur).
3. Webhook harus dapat menerima callback payment tanpa login user biasa.
4. Jangan mematikan authentication global.
5. Buat pengecualian hanya untuk route webhook yang diperlukan.
6. Tambahkan validasi signature/secret webhook jika provider payment mendukungnya.
7. Jangan memproses transaksi nyata atau mengirim pembayaran sungguhan.
8. Jangan membuat mock payment.
9. Simpan/update status order menggunakan Prisma sesuai schema yang sudah ada.
10. Jangan mengubah UI atau database schema jika tidak benar-benar diperlukan.
11. Jalankan typecheck dan build.
12. Verifikasi endpoint webhook dengan request aman tanpa transaksi nyata.
13. Jangan commit/push GitHub.

Laporkan file yang diubah dan hasil verifikasi.

```
# 
```

Prompt: Audit Service Database Digital Cell

Migration Prisma sudah berhasil dan database sekarang sinkron.

Hasil verifikasi:
- Semua migration berhasil diterapkan.
- Prisma schema valid.
- PostgreSQL terkoneksi.
- /api/products HTTP 200.
- /api/products menggunakan Prisma/PostgreSQL.
- Database masih kosong dan tidak boleh diisi mock data.

Sekarang lakukan audit tahap berikutnya.

1. Audit semua service backend yang berhubungan dengan database:
   - product-service
   - category-service
   - user-service
   - auth-service
   - order-service
   - payment-service
   - banner-service
   - notification/service terkait
   - settings/config service jika menggunakan database

2. Cari apakah masih ada:
   - mock data
   - static data sebagai pengganti database
   - local JSON sebagai database
   - hardcoded product/category/order
   - service yang belum menggunakan Prisma padahal seharusnya menggunakan database.

3. Cocokkan setiap service dengan model yang tersedia di Prisma schema.

4. Jangan mengubah UI.

5. Jangan membuat ulang project.

6. Jangan menghapus fitur existing.

7. Jangan menghapus migration.

8. Jika menemukan service yang belum terhubung ke database:
   - perbaiki secara modular
   - gunakan Prisma singleton yang sudah ada
   - gunakan model Prisma yang sesuai
   - jangan membuat database/model baru tanpa alasan.

9. Untuk setiap service, tentukan status:
   CONNECTED / PARTIAL / NOT CONNECTED / STATIC

10. Perbaiki hanya bagian yang memang diperlukan agar backend menggunakan PostgreSQL.

11. Setelah perubahan:
   - npm run typecheck
   - npm run build
   - verifikasi GET /api/products
   - verifikasi endpoint database lain yang aman dilakukan tanpa membuat transaksi nyata.

12. Jangan membuat data dummy hanya untuk testing.

13. Jangan menjalankan migrate reset.

14. Jangan commit atau push GitHub.

Berikan laporan:
- service yang sudah terhubung
- service yang belum terhubung
- file yang diubah
- masalah yang ditemukan
- hasil typecheck
- hasil build
- hasil endpoint verification.

```
# 
```

Prompt: Terapkan Migration Prisma

Hasil audit sebelumnya:
- DATABASE_URL tersedia
- Koneksi Prisma berhasil
- /api/products sudah membaca PostgreSQL
- Database saat ini kosong
- Migration yang belum diterapkan:
  20260818090000_add_order_checkout_snapshot

Sekarang fokus hanya pada migration database.

1. Pastikan DATABASE_URL menunjuk ke database Digital Cell yang benar.
2. Periksa isi migration 20260818090000_add_order_checkout_snapshot sebelum menjalankannya.
3. Pastikan migration hanya melakukan perubahan yang sesuai dengan Prisma schema.
4. Jika target database benar dan aman, jalankan:
   npx prisma migrate deploy
5. Jangan gunakan prisma migrate reset.
6. Jangan menghapus database.
7. Jangan menghapus migration lama.
8. Setelah migration selesai, jalankan verifikasi Prisma read-only.
9. Pastikan kolom buyerName, buyerPhone, buyerEmail, dan notes sudah tersedia pada tabel Order.
10. Verifikasi /api/products tetap mengembalikan HTTP 200.
11. Jangan mengubah UI, Cloudflare, port, atau fitur lain.
12. Jangan commit atau push ke GitHub.

Laporkan:
- migration sebelum dijalankan
- hasil migrate deploy
- migration setelah dijalankan
- status schema database
- hasil /api/products
- apakah database sekarang sinkron dengan Prisma schema.

```
# 
```



```
# 
```


Prompt: Verifikasi Database Prisma

Jangan mengubah UI, Cloudflare, port, atau konfigurasi server yang tidak diperlukan.

Fokus hanya pada database Prisma.

1. Audit schema Prisma dan migration yang sudah ada.
2. Periksa apakah DATABASE_URL tersedia pada environment aplikasi yang sedang berjalan.
3. Jangan membuat atau menggunakan database dummy.
4. Jangan menjalankan prisma migrate terhadap database production tanpa memastikan DATABASE_URL benar.
5. Jika DATABASE_URL belum tersedia, jangan memaksa migration. Laporkan dengan jelas file/environment mana yang membutuhkan DATABASE_URL.
6. Jika DATABASE_URL tersedia, lakukan verifikasi koneksi Prisma secara aman dan read-only terlebih dahulu.
7. Verifikasi apakah /api/products benar-benar mengambil data dari database atau masih menggunakan mock/static data.
8. Jangan commit atau push.
9. Setelah selesai, laporkan:
   - status DATABASE_URL
   - status koneksi Prisma
   - sumber data /api/products
   - migration yang sudah ada
   - apakah database siap digunakan.

Jangan memperbaiki hal lain di luar scope ini.
```
# 
```



```
# Prompt: Integrasi Prisma dan Backend Toko Online
```
## Audit & Perbaiki Integrasi Backend Prisma

Project: Digital Cell / toko-online

Lakukan audit menyeluruh terhadap project yang sedang aktif. Jangan membuat ulang project dan jangan menghapus fitur yang sudah ada.

Temuan awal:
- Next.js 14.2.33
- TypeScript
- Prisma 5.22
- Database migration sudah ada di app/prisma/migrations/
- npm run typecheck berhasil
- npm run build berhasil
- npm run start gagal karena next.config menggunakan output: "export"
- Pencarian PrismaClient di src tidak menemukan penggunaan Prisma.
- Project memiliki service seperti auth-service, banner-service, category-service, order-service, payment-service, product-service, user-service, dll.

Tugas:

1. Audit seluruh struktur project terlebih dahulu.
2. Tentukan apakah project memang membutuhkan Next.js server runtime untuk Prisma/database.
3. Jika Prisma belum terintegrasi, implementasikan integrasi Prisma dengan benar.
4. Jika output: "export" tidak sesuai dengan arsitektur aplikasi, perbaiki next.config agar aplikasi dapat berjalan menggunakan npm run start.
5. Buat/revisi Prisma client singleton yang aman untuk development dan production.
6. Hubungkan service yang memang membutuhkan database ke Prisma tanpa merusak UI/store yang sudah ada.
7. Periksa authentication, user, product, category, banner, order, order item, payment, notification dan data settings agar siap menggunakan database sesuai schema Prisma yang sudah ada.
8. Jangan membuat API route hanya untuk formalitas. Buat endpoint/server logic yang memang diperlukan oleh frontend.
9. Jangan menyimpan DATABASE_URL, secret, atau .env.local ke Git.
10. Jangan mengubah desain UI kecuali diperlukan untuk memperbaiki integrasi.
11. Jangan menghapus migration yang sudah dibuat.
12. Pastikan semua perubahan modular dan mudah dikembangkan.
13. Jalankan:
   - npm run typecheck
   - npm run build
   - npm run start atau validasi production server dengan cara yang sesuai arsitektur.
14. Jika ditemukan error, perbaiki sampai ketiga tahap tersebut valid.
15. Setelah selesai, tampilkan ringkasan file yang diubah dan alasan setiap perubahan.

PENTING:
- Jangan hanya menghilangkan output: "export" tanpa memastikan seluruh aplikasi tetap berfungsi.
- Jangan mengganti database.
- Jangan menggunakan mock data sebagai pengganti database production.
- Jangan menghapus fitur existing.
- Gunakan schema Prisma yang sudah tersedia sebagai sumber data utama.
- Jika ada bagian yang ternyata memang sengaja static, pertahankan bagian tersebut dan integrasikan backend hanya pada fitur yang membutuhkan database.
- Setelah perubahan selesai, jangan commit atau push ke GitHub. Berhenti dan laporkan hasilnya agar bisa diverifikasi terlebih dahulu.


```
# 
```

Lakukan audit terhadap semua perubahan working tree yang baru saja dibuat.

Jangan commit dan jangan push.

Fokus utama:
1. Periksa apakah ada file existing yang kehilangan fitur atau logic akibat perubahan.
2. Audit 33 file yang berubah, terutama file service:
   - auth-service.ts
   - banner-service.ts
   - category-service.ts
   - order-service.ts
   - payment-service.ts
   - product-service.ts
   - report-service.ts
   - settings-service.ts
   - user-service.ts
3. Pastikan perubahan bukan sekadar mengganti mock/static logic tanpa mempertahankan fitur existing.
4. Pastikan Prisma benar-benar digunakan oleh service yang membutuhkan database.
5. Periksa app/src/app/api/[...path]/ dan pastikan endpoint yang dibuat memang diperlukan dan aman.
6. Periksa authentication/session agar tetap bekerja.
7. Pastikan tidak ada API route, server logic, atau service yang rusak.
8. Pastikan tidak ada data production yang diganti mock data.
9. Pastikan DATABASE_URL dan SESSION_SECRET tidak masuk Git.
10. Jangan menghapus migration atau fitur existing.

Gunakan git diff untuk membandingkan perubahan dengan HEAD.

Jika menemukan regression atau logic yang hilang, langsung perbaiki.

Setelah selesai jalankan:
npm run typecheck
npm run build

Jangan commit/push.

Terakhir laporkan:
- file yang memang perlu diubah
- fitur yang dipulihkan
- regression yang ditemukan
- hasil typecheck
- hasil build

```
# 
```

PROMPT — Phase 26.1 Task 2: Configure Supabase PostgreSQL Safely

Kita sedang melanjutkan project /root/toko-online.

Tujuan:
Konfigurasi Prisma agar terhubung ke Supabase PostgreSQL dengan aman berdasarkan connection string yang SUDAH tersedia di environment lokal.

PENTING:
- Jangan membuat-buat DATABASE_URL.
- Jangan mengubah password/credential.
- Jangan menampilkan credential ke output.
- Jangan commit .env.local atau file secret.
- Jangan menghapus data/database.
- Jangan menjalankan migration sebelum koneksi benar-benar tervalidasi.
- Jangan mengubah schema Prisma pada task ini kecuali memang diperlukan untuk kompatibilitas konfigurasi.
- Jangan upgrade/downgrade dependency.
- Jangan melakukan refactor yang tidak berhubungan.

LANGKAH 1 — AUDIT ENV

Periksa:
- .env.local
- .env
- .env.example
- prisma/schema.prisma
- .gitignore
- package.json

Pastikan:
- DATABASE_URL tersedia.
- DIRECT_URL tersedia jika konfigurasi Prisma membutuhkannya.
- .env.local tetap untracked/ignored.
- Tidak ada credential hardcoded di source code.
- Jangan pernah print nilai lengkap DATABASE_URL/DIRECT_URL.

Untuk validasi URL, hanya tampilkan metadata aman seperti:
- scheme
- hostname
- port
- apakah database name tersedia
- apakah URL berhasil diparse

Jangan tampilkan username/password/query secret.

LANGKAH 2 — PERBAIKI KONFIGURASI PRISMA

Gunakan connection string Supabase yang sudah diberikan di .env.local.

Target arsitektur:
- DATABASE_URL = connection pooler Supabase untuk runtime/application.
- DIRECT_URL = direct PostgreSQL connection untuk Prisma migration bila konfigurasi Prisma project memang menggunakan directUrl.

Periksa versi Prisma yang sedang terpasang sebelum menentukan syntax konfigurasi.

Jangan mengganti URL dengan placeholder.

Jika prisma/schema.prisma sudah benar, jangan ubah.

Jika perlu perubahan konfigurasi, lakukan perubahan paling kecil dan jelaskan alasannya.

LANGKAH 3 — VALIDASI

Jalankan pemeriksaan yang aman terlebih dahulu:

1. Validasi Prisma schema.
2. Validasi environment/configuration.
3. Jalankan:
   npx prisma migrate status

JANGAN menjalankan:
- prisma migrate dev
- prisma migrate deploy
- prisma db push
- reset database
- seed

sampai koneksi database benar-benar terbukti valid dan target database sudah dikonfirmasi.

Jika muncul:
- invalid port number
- P1001
- P1000
- authentication error
- SSL error
- connection timeout
- URL parsing error

STOP dan analisis root cause. Jangan mencoba password atau URL acak.

LANGKAH 4 — BUILD/LINT

Setelah konfigurasi valid, jalankan verifikasi yang relevan:
- TypeScript check
- npm run lint
- npm run build

Jangan memperbaiki error unrelated secara besar-besaran pada task ini.

LANGKAH 5 — GIT SAFETY CHECK

Sebelum commit:
- git status --short
- git diff --check
- pastikan .env.local tidak masuk staging
- pastikan tidak ada credential di diff
- tampilkan file yang berubah
- tampilkan ringkasan perubahan

Jika semua verification PASS:
buat SATU commit checkpoint dengan message:

chore(database): configure Supabase PostgreSQL connection

Kemudian push ke origin/main jika authentication GitHub tersedia.

Jika push gagal karena credential/network:
- jangan force push
- jangan reset
- jangan mengubah remote
- jangan membuat commit tambahan
- pertahankan commit lokal sebagai backup
- laporkan error push dengan jelas.

OUTPUT AKHIR WAJIB:

1. DATABASE_URL valid/tidak valid — tanpa credential.
2. DIRECT_URL valid/tidak valid — tanpa credential.
3. Prisma schema PASS/FAIL.
4. prisma migrate status PASS/FAIL.
5. lint PASS/FAIL.
6. build PASS/FAIL.
7. git diff --check PASS/FAIL.
8. commit hash jika dibuat.
9. push PASS/FAIL.
10. Jika ada blocker, jelaskan blocker dan NEXT STEP paling aman.

JANGAN lanjut ke Task 3 (Prisma schema/migration) sebelum Task 2 benar-benar PASS.

```
# Phase 26.1 Task 4
```

# Prompt: Phase 26.1 Task 4 — Initial Database Migration

Lanjutkan development repository `/root/toko-online`.

Kita sekarang berada di:

Phase 26 — Database
Phase 26.1 — Database Setup
Task 4 — Run Initial Database Migration

IMPORTANT:
- Repository sudah memiliki commit backup dan sudah di-push ke GitHub.
- Jangan menghapus pekerjaan yang sudah ada.
- Jangan melakukan reset/revert.
- Jangan mengubah fitur yang tidak berkaitan dengan database.
- Jangan upgrade/downgrade dependency.
- Jangan menghapus credentials.
- Jangan menampilkan isi credential/secret ke output.
- `.env.local` harus tetap tidak masuk Git.
- Jangan pernah commit DATABASE_URL asli.
- Jangan menjalankan migration ke production secara otomatis jika target database belum dapat dipastikan aman.

TUJUAN:
Menyelesaikan Phase 26.1 Task 4 dengan aman berdasarkan Prisma schema yang sudah dibuat pada Task 3.

==================================================
1. AUDIT KONDISI SAAT INI
==================================================

Sebelum mengubah apa pun, periksa:

- git status
- git branch -vv
- git log -5 --oneline --decorate
- package.json
- prisma/schema.prisma
- prisma.config.* jika ada
- .env.example
- .env.local hanya untuk membaca keberadaan variable, JANGAN CETAK NILAI SECRET
- konfigurasi Prisma
- scripts database di package.json

Pastikan:
- working tree awalnya clean atau jelaskan jika tidak;
- schema Prisma valid;
- DATABASE_URL tersedia atau tidak;
- provider database yang digunakan;
- migration directory sudah ada atau belum;
- apakah database sudah pernah memiliki migration.

Jangan mengarang DATABASE_URL.

==================================================
2. VALIDASI PRISMA
==================================================

Jalankan validasi Prisma yang sesuai dengan versi Prisma yang benar-benar digunakan repository.

Minimal:

- Prisma schema validation
- Prisma generate jika diperlukan
- pemeriksaan konfigurasi datasource
- pemeriksaan migration state

Gunakan command yang kompatibel dengan versi Prisma yang sudah terpasang.

Jangan mengubah package.json atau lockfile hanya karena ingin memakai command Prisma versi lain.

Jika command gagal karena konfigurasi Prisma, perbaiki hanya konfigurasi yang memang diperlukan.

==================================================
3. PERIKSA DATABASE TARGET
==================================================

Sebelum migration, tentukan dengan jelas database target.

Kemungkinan provider:
- Supabase PostgreSQL
- PostgreSQL biasa
- provider PostgreSQL lain

Jangan memilih provider berdasarkan tebakan.

DATABASE_URL harus berasal dari environment/secret manager yang memang tersedia.

Jika DATABASE_URL tidak tersedia:
- JANGAN membuat credential palsu.
- JANGAN mengarang connection string.
- JANGAN menjalankan migration.
- Berhenti pada tahap konfigurasi dan laporkan bahwa database connection belum tersedia.

Jika DATABASE_URL tersedia:
- jangan tampilkan nilainya;
- hanya tampilkan informasi non-secret seperti provider/host yang aman bila diperlukan.

==================================================
4. BEDAKAN LOCAL / STAGING / PRODUCTION
==================================================

Sebelum menjalankan migration, tentukan apakah DATABASE_URL menunjuk ke:

- local development database,
- staging/test database,
- atau production database.

Jika tidak dapat dipastikan, JANGAN menjalankan destructive atau irreversible migration.

Untuk database production:
- jangan menggunakan reset;
- jangan menggunakan prisma migrate reset;
- jangan menghapus database;
- jangan drop table;
- jangan force migration;
- jangan melakukan perubahan schema di luar migration.

Jika migration terhadap production memang diperlukan tetapi target belum terkonfirmasi:
STOP dan laporkan kondisi tersebut.

==================================================
5. BUAT INITIAL MIGRATION
==================================================

Jika database target aman untuk migration dan schema sudah valid:

Buat initial migration berdasarkan `prisma/schema.prisma`.

Gunakan workflow Prisma yang sesuai dengan versi repository.

Migration harus:
- berasal dari schema aktual;
- reproducible;
- tersimpan di `prisma/migrations/`;
- tidak mengandung credential;
- tidak menghapus data existing tanpa alasan yang jelas;
- tidak melakukan perubahan di luar kebutuhan schema.

Jika migration directory sudah memiliki migration sebelumnya:
JANGAN membuat migration duplicate.
Audit migration yang sudah ada terlebih dahulu.

==================================================
6. JANGAN MENGHANCURKAN DATA
==================================================

Sangat penting:

JANGAN menjalankan:

- prisma migrate reset
- DROP DATABASE
- DROP TABLE secara manual
- delete existing records
- truncate existing tables

kecuali memang diperlukan dan telah mendapat instruksi eksplisit.

Jika schema Prisma tidak cocok dengan database existing dan migration berpotensi merusak data:
STOP.

Laporkan:
- tabel yang konflik;
- perubahan schema;
- potensi kehilangan data;
- solusi migration yang aman.

==================================================
7. JALANKAN MIGRATION DENGAN AMAN
==================================================

Jika target database sudah terverifikasi aman:

- jalankan initial migration;
- cek hasil migration;
- cek migration status;
- pastikan schema database sesuai dengan Prisma schema.

Jangan menggunakan reset.

Jika Prisma meminta confirmation atau migration berpotensi destructive:
JANGAN bypass confirmation.
STOP dan laporkan.

==================================================
8. VERIFIKASI PROJECT
==================================================

Setelah migration berhasil, jalankan:

- TypeScript check
- lint
- build
- Prisma validation/generate
- migration status

Pastikan tidak ada regression pada:

Phase 25:
- API

Phase 27:
- authentication

Phase 28:
- caching

Phase 29:
- SEO/metadata/PWA

Jangan mengubah fitur-fitur tersebut.

Jika build gagal:
- cari root cause;
- perbaiki hanya jika berkaitan langsung dengan perubahan Task 4;
- jangan melakukan refactor besar.

==================================================
9. GIT AUDIT
==================================================

Setelah semua verification PASS:

Jalankan:

git status
git diff --check
git diff --stat

Pastikan hanya file yang memang berkaitan dengan Task 4 yang berubah.

Periksa terutama:
- prisma/schema.prisma
- prisma/migrations/*
- package.json
- package-lock.json
- konfigurasi Prisma

Jika package-lock berubah tanpa alasan yang diperlukan:
jangan commit perubahan tersebut.

Pastikan:
- `.env.local` tidak tracked;
- DATABASE_URL tidak masuk commit;
- credential tidak masuk commit;
- tidak ada secret di migration;
- tidak ada file temporary/debug.

==================================================
10. COMMIT CHECKPOINT
==================================================

Jika:

- Prisma validation PASS
- migration PASS
- migration status PASS
- TypeScript PASS
- lint PASS
- build PASS
- git diff --check PASS
- tidak ada secret
- working tree hanya berisi perubahan Task 4

buat checkpoint commit.

Commit message:

feat(database): run initial prisma migration

Setelah commit:

git log -1 --oneline --decorate
git status --short --branch

==================================================
11. PUSH BACKUP
==================================================

Setelah commit berhasil, push ke:

origin main

Gunakan authentication yang memang sudah dikonfigurasi.

Jangan force push.

Jangan:

git push --force
git reset --hard
git push --force-with-lease

Jika push gagal karena authentication:
- JANGAN mengubah commit;
- JANGAN membuat commit duplicate;
- jangan force push;
- laporkan commit hash lokal yang sudah tersimpan sebagai backup.

==================================================
12. HASIL AKHIR
==================================================

Berikan laporan ringkas:

### Database
- Provider:
- DATABASE_URL tersedia: YES/NO
- Target: local/staging/production/unknown
- Prisma validation:
- Migration:
- Migration status:

### Verification
- TypeScript:
- Lint:
- Build:
- git diff --check:

### Git
- Commit hash:
- Commit message:
- Push:
- Working tree:

### Remaining
Sebutkan task berikutnya berdasarkan ROADMAP.md.

IMPORTANT:
Jangan menyatakan PASS jika command benar-benar belum dijalankan.

Jangan membuat asumsi bahwa migration production aman.

Jika ada blocker yang membutuhkan credential/provider/database decision, STOP pada titik tersebut dan laporkan blocker secara jelas.

Kerjakan Task 4 saja. Jangan lanjut ke Task 5 sebelum Task 4 benar-benar terverifikasi dan checkpoint commit berhasil.

```
# Prompt — Phase 26.1 Task 3: Prisma Schema
```
Lanjutkan project Digital Cell dari titik terakhir.

KONDISI SAAT INI:
- DATABASE_URL sudah tersedia di .env.local.
- Jangan tampilkan nilai DATABASE_URL atau credential apa pun.
- Working tree sebelumnya CLEAN.
- Phase 27 Auth harus tetap utuh.
- Phase 28 Caching harus tetap utuh.
- Phase 29 SEO/Metadata/PWA harus tetap utuh.
- Jangan mengerjakan Phase 25 dulu.
- Sekarang fokus hanya Phase 26.1 Task 3 — Prisma Schema.

TUJUAN:
Menyelesaikan konfigurasi Prisma schema secara aman sebagai checkpoint sebelum migration.

ATURAN KESELAMATAN:
1. JANGAN menjalankan:
   - prisma migrate
   - prisma migrate dev
   - prisma migrate deploy
   - prisma db push
   - prisma db reset
   - seed
   - DROP/DELETE/TRUNCATE database
2. Jangan mengubah database remote.
3. Jangan mengubah credential.
4. Jangan menampilkan secret.
5. Jangan mengubah dependency/version kecuali benar-benar diperlukan oleh konfigurasi Prisma existing.
6. Jangan melakukan refactor besar.
7. Jangan menyentuh Phase 27, 28, atau 29 kecuali hanya untuk regression check.
8. Jangan menghapus file existing.
9. Jangan membuat fake/mock database.
10. Jangan membuat data production baru.

LANGKAH 1 — AUDIT EXISTING PRISMA

Periksa:
- package.json
- package-lock.json
- prisma/
- prisma/schema.prisma jika sudah ada
- seluruh penggunaan PrismaClient
- konfigurasi Next.js yang berkaitan dengan database
- environment variable yang digunakan Prisma.

Tentukan apakah project memang menggunakan Prisma dan versi Prisma yang sedang terpasang.

LANGKAH 2 — DATABASE SOURCE

Pastikan datasource Prisma menggunakan:

url = env("DATABASE_URL")

Jangan hardcode connection string.

Jika Prisma schema sudah benar, jangan mengubahnya tanpa alasan.

Jika datasource belum ada atau salah, perbaiki hanya bagian yang diperlukan.

LANGKAH 3 — MODEL SCHEMA

Audit model/schema existing.

PENTING:
Jangan mengarang model database berdasarkan asumsi.

Jangan membuat schema besar baru jika kebutuhan model belum jelas.

Jika repository sudah mempunyai definisi model yang jelas, pertahankan model tersebut dan hanya perbaiki konfigurasi yang diperlukan untuk Task 3.

Jika model database belum ditentukan, jangan memaksakan desain database. Catat sebagai blocker/next step untuk Task berikutnya.

LANGKAH 4 — VALIDASI TANPA MENYENTUH DATABASE

Gunakan validasi Prisma yang tidak melakukan migration atau perubahan database.

Validasi:
- syntax schema
- datasource
- generator
- model definitions
- environment variable reference.

Kemudian jalankan:
npm run lint
npm run build
git diff --check

Jika ada error:
- perbaiki hanya error yang berhubungan langsung dengan Task 3;
- jangan melakukan perubahan besar;
- ulangi validasi.

LANGKAH 5 — PROTEKSI CREDENTIAL

Periksa apakah repository mempunyai .gitignore.

Pastikan:
.env
.env.local
.env.*.local

tidak dapat masuk commit.

Jangan pernah commit .env.local.

Jika .gitignore belum ada atau belum mengecualikan .env.local, tambahkan aturan minimal yang diperlukan.

Verifikasi bahwa .env.local bukan tracked file.

Jangan tampilkan isi .env.local.

LANGKAH 6 — REGRESSION CHECK

Pastikan perubahan tidak merusak:

- Phase 27 Auth
- Phase 28 Caching
- Phase 29 SEO/Metadata/PWA

Gunakan TypeScript/lint/build sebagai regression verification.

LANGKAH 7 — COMMIT CHECKPOINT

Jika:
- Prisma schema valid
- TypeScript PASS
- Lint PASS
- Build PASS
- git diff --check PASS
- credential aman
- regression tidak bermasalah

maka buat SATU checkpoint commit:

feat(database): configure Prisma schema

Sebelum commit:
git status --short
git diff --check

Setelah commit:
git status --short --branch
git log -1 --oneline --decorate

LANGKAH 8 — PUSH

Jika GitHub authentication tersedia, push ke origin/main.

Jika push gagal karena authentication:
- JANGAN membuat commit tambahan.
- JANGAN force push.
- Pertahankan commit lokal.
- Laporkan hash commit lokal.

HASIL AKHIR

Berikan laporan:

1. Prisma version
2. Schema status
3. datasource status
4. DATABASE_URL status tanpa menampilkan secret
5. .gitignore/credential protection status
6. TypeScript
7. Lint
8. Build
9. git diff --check
10. Phase 27 regression
11. Phase 28 regression
12. Phase 29 regression
13. files changed
14. commit hash
15. push status
16. working tree status
17. blocker jika ada
18. next step yang tepat

PENTING:
Jangan lanjut ke Task 4 atau migration.
Berhenti setelah Phase 26.1 Task 3 selesai dan tervalidasi.


```
# 
```
DATABASE_URL Supabase PostgreSQL sudah tersedia di .env.local.

Lanjutkan Phase 26.1 Task 2 dari titik terakhir.

PENTING:
- Jangan tampilkan atau bocorkan DATABASE_URL/credential.
- Jangan mengubah nilai credential.
- Jangan membuat database baru.
- Jangan menjalankan migration.
- Jangan menjalankan prisma db push.
- Jangan menjalankan prisma migrate.
- Jangan menjalankan seed.
- Jangan reset database.
- Jangan mengubah Phase 27 Auth.
- Jangan mengubah Phase 28 Caching.
- Jangan mengubah Phase 29 SEO/Metadata/PWA.

TUJUAN:
Validasi bahwa konfigurasi Prisma/database connection dapat membaca DATABASE_URL Supabase dengan benar.

LANGKAH:

1. Audit package.json dan package-lock.json.
2. Pastikan ORM yang digunakan project adalah Prisma dan gunakan konfigurasi existing.
3. Periksa prisma/schema.prisma atau konfigurasi Prisma yang sudah ada.
4. Pastikan datasource menggunakan environment variable DATABASE_URL, bukan hardcoded URL.
5. Jika konfigurasi sudah benar, jangan ubah file tanpa alasan.
6. Jalankan validasi Prisma yang TIDAK mengubah database.
7. Jalankan:
   npm run lint
   npm run build
   git diff --check

8. Pastikan:
   - TypeScript PASS
   - Prisma/configuration validation PASS
   - Lint PASS
   - Build PASS
   - git diff --check PASS

9. Regression check:
   - Phase 27 Auth tetap PASS
   - Phase 28 Caching tetap PASS
   - Phase 29 SEO/Metadata/PWA tetap PASS

10. Jika semuanya PASS dan memang ada perubahan konfigurasi yang perlu disimpan:
   buat SATU commit:

   feat(database): configure Supabase PostgreSQL connection

11. Pastikan .env.local/.env tidak masuk commit.

12. Setelah commit:
   git status --short --branch
   git log -1 --oneline --decorate

13. Push ke origin/main jika authentication GitHub tersedia.
   Jika push gagal karena credential:
   jangan membuat commit tambahan, jangan force push, dan laporkan commit hash lokal.

JANGAN lanjut ke Phase 26.1 Task 3/schema.
Berhenti setelah Task 2 selesai dan berikan laporan lengkap.

LAPORAN:
- Prisma status
- DATABASE_URL configuration status (tanpa menampilkan secret)
- files changed
- TypeScript
- Prisma validation
- Lint
- Build
- regression Phase 27/28/29
- commit hash
- push status
- working tree status
- next step: Phase 26.1 Task 3 jika Task 2 PASS


```
# Prompt — Phase 26.1.2 Supabase PostgreSQL Connection
```
Lanjutkan project /root/toko-online.

MODEL: GPT-5.6 Luna

KEPUTUSAN PROVIDER:
Gunakan SUPABASE POSTGRESQL sebagai database production project.

PENTING:
Jangan membuat database baru di VPS.
Jangan membuat provider database lain.
Jangan menggunakan SQLite sebagai production database.
Jangan menggunakan MongoDB.
Jangan mengganti architecture existing.
Jangan mengubah Phase 27 Auth, Phase 28 Caching, atau Phase 29 SEO/Metadata/PWA.

==================================================
STATUS PROJECT
==================================================

Repository:
 /root/toko-online

Status terakhir:
- Working tree CLEAN.
- Phase 27 Auth: PASS.
- Phase 28 Caching: PASS.
- Phase 29 SEO/Metadata/PWA: PASS.
- Phase 25 API belum dilanjutkan karena membutuhkan database.
- Phase 26 database menjadi prerequisite untuk Phase 25 API.

Git checkpoint terakhir sudah dibuat lokal dan sudah dipush jika credential tersedia.

Jangan reset/revert/force-push.

==================================================
TUJUAN TASK
==================================================

Kerjakan HANYA:

Phase 26.1 Task 2
Configure database connection string menggunakan Supabase PostgreSQL.

Jangan langsung mengerjakan:
- schema lengkap
- migration
- seed
- repository
- API products
- API categories
- order repository
- payment repository

Task berikutnya akan dikerjakan terpisah setelah connection configuration terbukti benar.

==================================================
STEP 1 — AUDIT EXISTING DATABASE STACK
==================================================

Sebelum mengubah apa pun, periksa:

package.json
package-lock.json
.env
.env.example
.env.local jika ada
source code database yang sudah ada.

Cari dependency:

- prisma
- @prisma/client
- drizzle-orm
- drizzle-kit
- pg
- postgres
- @supabase/supabase-js
- database adapter lain.

Cari juga folder/file:

prisma/
drizzle/
src/lib/db
src/lib/database
src/server/db
src/services/database
src/repositories
database
migrations

Tentukan apakah project sudah memiliki database ORM atau database client.

Jangan install dependency baru sebelum mengetahui stack existing.

==================================================
STEP 2 — SUPABASE CONNECTION
==================================================

Gunakan Supabase PostgreSQL sebagai database.

Connection string harus berasal dari environment variable.

Prioritas:

DATABASE_URL

Jika ORM yang dipakai membutuhkan DIRECT_URL terpisah, gunakan:

DIRECT_URL

Tetapi jangan membuat variable tambahan tanpa alasan teknis.

JANGAN pernah hardcode:

- hostname
- username
- password
- database password
- connection string
- API key
- service role key

ke source code.

==================================================
STEP 3 — ENVIRONMENT SAFETY
==================================================

Periksa .gitignore.

Pastikan file berikut tidak akan masuk commit:

.env
.env.local
.env.production
file credential lainnya.

Jika `.env.example` perlu diperbarui, hanya masukkan placeholder:

DATABASE_URL=""

atau format placeholder yang sesuai stack.

Jangan pernah memasukkan credential Supabase asli ke `.env.example`.

Jangan print credential lengkap ke terminal.

Jika perlu menunjukkan environment variable saat audit, masking value.

Contoh:

DATABASE_URL=postgresql://user:****@host/db

==================================================
STEP 4 — KONFIGURASI ORM
==================================================

Jika project menggunakan Prisma:

- gunakan DATABASE_URL dari environment;
- gunakan konfigurasi Prisma yang sesuai versi Prisma existing;
- jangan membuat schema lengkap pada task ini;
- jangan membuat migration;
- jangan menjalankan reset database;
- jangan menjalankan db push terhadap production.

Jika project menggunakan Drizzle:

- gunakan environment variable yang sesuai;
- jangan membuat schema lengkap;
- jangan menjalankan migration production.

Jika belum ada ORM:

JANGAN memilih ORM secara asal pada task ini.

Berikan laporan dan berhenti jika connection layer belum memiliki dasar architecture yang cukup.

==================================================
STEP 5 — VALIDASI CONNECTION
==================================================

Setelah konfigurasi siap, lakukan validasi yang AMAN.

Validasi:

1. TypeScript
2. lint
3. build
4. git diff --check

Jika ORM memiliki command untuk memvalidasi konfigurasi tanpa mengubah database, gunakan command tersebut.

Contoh prinsip:
- validasi schema/configuration;
- jangan migration;
- jangan reset;
- jangan db push;
- jangan seed.

Tujuan hanya memastikan aplikasi dapat membaca konfigurasi database dengan benar.

==================================================
STEP 6 — JANGAN MERUSAK EXISTING FEATURE
==================================================

Setelah perubahan, pastikan:

Phase 27 Auth:
PASS

Phase 28 Caching:
PASS

Phase 29 SEO/Metadata/PWA:
PASS

Existing:
- product UI tetap;
- category UI tetap;
- banner tetap;
- admin tetap;
- payment flow tetap;
- order flow tetap;
- authentication tetap;
- middleware tetap.

Jangan melakukan refactor yang tidak berhubungan dengan database connection.

==================================================
STEP 7 — JIKA ERROR
==================================================

Jika TypeScript/lint/build gagal:

Cari ROOT CAUSE.

Perbaiki hanya error yang disebabkan oleh perubahan task ini.

Jangan:
- menghapus fitur;
- menonaktifkan TypeScript;
- menonaktifkan lint;
- mengubah tsconfig secara sembarangan;
- menghapus middleware;
- menghapus auth;
- menghapus route;
- menghapus component;
- membuat workaround palsu.

Jika error ternyata berasal dari baseline existing dan bukan task ini:

Jangan menyentuhnya.

Laporkan sebagai pre-existing issue.

==================================================
STEP 8 — GIT CHECKPOINT
==================================================

Jika semua verification PASS:

jalankan:

git status --short --branch
git diff --check
git diff --stat
git log -1 --oneline --decorate

Periksa bahwa perubahan hanya berkaitan dengan database connection configuration.

Jika aman, buat SATU commit:

feat(database): configure Supabase PostgreSQL connection

Jangan commit:
- .env
- .env.local
- credentials
- password
- API key
- service role key
- database dump

==================================================
STEP 9 — PUSH BACKUP
==================================================

Setelah commit berhasil:

cek:

git status --short --branch
git log -1 --oneline --decorate

Jika GitHub authentication tersedia, push commit ke origin/main.

Jika push gagal karena authentication:

JANGAN membuat commit tambahan.
JANGAN mengubah kode.
JANGAN force push.

Cukup laporkan:
- commit hash lokal;
- branch;
- status working tree;
- alasan push gagal.

Commit lokal tetap dianggap sebagai checkpoint backup sampai remote berhasil menerima.

==================================================
LAPORAN AKHIR
==================================================

Berikan laporan ringkas tetapi lengkap:

## Database Stack
- ORM/client yang digunakan:
- versi:
- existing database layer:

## Supabase
- provider: Supabase PostgreSQL
- DATABASE_URL configuration: PASS/NOT READY
- DIRECT_URL: digunakan/tidak diperlukan
- credentials: tidak ditampilkan

## Files Changed
Daftar file yang benar-benar berubah.

## Verification
- TypeScript:
- Lint:
- Build:
- git diff --check:

## Regression
- Phase 27 Auth:
- Phase 28 Caching:
- Phase 29 SEO/Metadata/PWA:

## Git
- HEAD:
- commit:
- push:
- working tree:

## NEXT STEP

Jika Task 26.1.2 PASS:
→ rekomendasikan lanjut Phase 26.1 Task 3 — Prisma schema.

Jika Task 26.1.2 belum bisa dilakukan karena DATABASE_URL/credential Supabase belum tersedia:
→ STOP dan jelaskan apa yang masih diperlukan.

JANGAN membuat schema atau migration sebelum connection configuration benar-benar siap.

==================================================
ATURAN PALING PENTING
==================================================

1. Supabase PostgreSQL adalah provider yang dipilih.
2. Jangan membuat database di VPS.
3. Jangan hardcode credentials.
4. Jangan menampilkan secret.
5. Jangan migration pada task ini.
6. Jangan seed pada task ini.
7. Jangan mengerjakan Phase 25 dulu.
8. Jangan merusak Phase 27/28/29.
9. Jangan reset/revert/force-push.
10. Jika PASS, buat commit checkpoint.
11. Jika bisa, push ke GitHub.
12. Jika push gagal, pertahankan commit lokal.
13. Jangan membuat perubahan di luar scope task.


```
# Phase 26.1 Task 2 → connection string → schema → migration → seed → repositories → kembali Phase 25 API.
```
PROMPT — Phase 26 Database Provider Audit & Safe Preparation

Lanjutkan dari audit terakhir.

Repository:
 /root/toko-online

Model:
 GPT-5.6 Luna

STATUS TERAKHIR:
- Working tree: CLEAN
- HEAD lokal: 0f95e04
- Commit terakhir:
  feat(settings): complete admin password change
- Phase 27 Auth: PASS
- Phase 28 Caching: PASS
- Phase 29 SEO/Metadata/PWA: PASS
- Phase 25 belum dilanjutkan karena dependency database.
- Push GitHub sebelumnya gagal hanya karena credential HTTPS tidak tersedia.
- Jangan membuat commit baru hanya karena push gagal.
- Jangan reset/revert/force-push.
- Jangan menghapus data, credentials, .env, database, atau perubahan existing.

HASIL AUDIT SEBELUMNYA:
Phase 25 Task 1 — GET /api/products memiliki dependency:
1. Database/repository layer belum siap.
2. Route Handler tidak boleh menggunakan pola `export default` yang tidak kompatibel.
3. Backend authentication/JWT harus menggunakan infrastructure auth yang sudah ada.
4. Phase 26 Database merupakan prerequisite nyata.

NEXT STEP DARI AUDIT:
Phase 26.1 Task 2 — Configure database connection string.

TAPI:
JANGAN LANGSUNG MEMBUAT DATABASE.
JANGAN MEMILIH PROVIDER SECARA ASAL.
JANGAN MEMBUAT DATABASE BARU.
JANGAN MENGGANTI ARCHITECTURE EXISTING.

==================================================
TUJUAN
==================================================

Lakukan audit khusus untuk menentukan database provider yang PALING SESUAI berdasarkan repository yang sudah ada.

Provider yang mungkin:
- PostgreSQL
- Supabase PostgreSQL
- Neon PostgreSQL
- provider PostgreSQL lain
- database existing yang sudah digunakan project

Prioritas:
1. Gunakan database/provider yang sudah tersedia.
2. Gunakan environment variable yang sudah ada jika memang valid.
3. Jangan meminta user membuat akun/provider baru jika sebenarnya project sudah memiliki database.
4. Jangan memasukkan credential asli ke source code.
5. Jangan mengubah `.env` tanpa kepastian.
6. Jangan membuat schema/migration sebelum provider dipastikan.

==================================================
1. AUDIT PACKAGE.JSON
==================================================

Periksa:

package.json
package-lock.json

Cari dependency:

- prisma
- @prisma/client
- drizzle-orm
- drizzle-kit
- pg
- postgres
- @supabase/supabase-js
- @neondatabase/serverless
- mongodb
- mongoose
- sqlite
- better-sqlite3
- database adapter lainnya.

Jelaskan dependency database yang BENAR-BENAR sudah terpasang.

Jangan install dependency baru pada tahap audit.

==================================================
2. AUDIT ENVIRONMENT
==================================================

Periksa:

.env
.env.local
.env.example
environment references di source code

Cari nama variable seperti:

DATABASE_URL
DIRECT_URL
POSTGRES_URL
POSTGRES_PRISMA_URL
POSTGRES_URL_NON_POOLING
SUPABASE_URL
SUPABASE_ANON_KEY
SUPABASE_SERVICE_ROLE_KEY
NEON_DATABASE_URL
DB_URL
DB_HOST
DB_USER
DB_PASSWORD
DB_NAME

PENTING:

- Jangan menampilkan secret/token/password/API key lengkap pada laporan.
- Masking semua credential.
- Jangan menghapus atau mengubah env.
- Jangan commit `.env`.
- Jangan mencetak secret ke terminal jika tidak diperlukan.

Tentukan apakah DATABASE_URL atau credential database sudah tersedia.

==================================================
3. AUDIT SOURCE CODE
==================================================

Cari seluruh penggunaan:

DATABASE_URL
process.env.*
database client
PrismaClient
Drizzle
Pool
postgres()
createClient()
supabase
repository
database service
data access layer

Gunakan grep/search yang aman.

Cari juga apakah project sudah memiliki:

- `lib/db`
- `lib/database`
- `services/database`
- `repositories`
- `server/db`
- `prisma/`
- `drizzle/`
- `migrations/`

Jelaskan struktur aktual.

==================================================
4. AUDIT EXISTING DATA
==================================================

Cari bagaimana data berikut saat ini disimpan:

- products
- categories
- banners
- users
- orders
- payments
- settings
- notifications
- admin users

Tentukan apakah data saat ini berasal dari:

- API
- JSON
- local file
- in-memory
- mock
- database
- service layer

Jangan mengubah data.

==================================================
5. AUDIT AUTH
==================================================

Phase 27 sudah PASS.

Periksa infrastructure auth existing:

- JWT
- jose
- cookie
- middleware
- user/session
- admin authentication

Tentukan data user/admin apa yang nantinya membutuhkan database.

JANGAN membuat sistem auth baru.

==================================================
6. AUDIT NEXT.JS VERSION
==================================================

Periksa versi Next.js.

Tentukan apakah project menggunakan:

- App Router
- Route Handlers
- Server Components
- Server Actions

Pastikan rekomendasi database kompatibel dengan architecture tersebut.

==================================================
7. TENTUKAN PROVIDER
==================================================

Setelah seluruh audit selesai, buat keputusan:

### CASE A
Jika database provider sudah tersedia:
→ gunakan provider tersebut.

### CASE B
Jika dependency dan environment jelas menunjukkan PostgreSQL:
→ rekomendasikan PostgreSQL yang sesuai dengan architecture existing.

### CASE C
Jika Supabase sudah digunakan:
→ gunakan Supabase, jangan membuat PostgreSQL kedua.

### CASE D
Jika Neon sudah digunakan:
→ gunakan Neon.

### CASE E
Jika tidak ada database/provider sama sekali:
→ JANGAN membuat database.
→ JANGAN install dependency.
→ JANGAN mengarang DATABASE_URL.
→ berhenti pada audit dan laporkan apa yang harus disediakan user.

==================================================
8. TASK 26.1 TASK 2
==================================================

Jika provider dan DATABASE_URL SUDAH tersedia dan architecture sudah jelas:

Implementasikan konfigurasi connection string SECARA MINIMAL.

Ketentuan:

- gunakan environment variable;
- jangan hardcode credential;
- jangan commit `.env`;
- jangan mengubah auth;
- jangan mengubah caching;
- jangan mengubah SEO/metadata/PWA;
- jangan mengubah product UI;
- jangan mengerjakan migration/schema/seed sekaligus.

Hanya kerjakan connection configuration yang memang diperlukan.

Jika provider belum tersedia:
STOP setelah audit.
Jangan coding.

==================================================
9. VERIFICATION
==================================================

Jika ada perubahan:

npm run lint
npm run build
git diff --check

Pastikan:

- TypeScript PASS
- Lint PASS
- Build PASS
- Phase 27 auth tetap PASS
- Phase 28 caching tetap PASS
- Phase 29 SEO/metadata/PWA tetap PASS

Jika ada error:
perbaiki hanya root cause task ini.

Jangan melakukan refactor besar.

==================================================
10. GIT CHECKPOINT
==================================================

Jika dan HANYA JIKA semua verification PASS:

git status --short --branch
git diff --stat
git diff --check
git log -1 --oneline --decorate

Jika perubahan memang layak menjadi checkpoint:

buat SATU commit:

feat(database): configure database connection

Gunakan message yang sesuai dengan perubahan aktual.

Jangan commit `.env`, credentials, secret, database dump, atau file sensitif.

Push hanya jika credential GitHub tersedia.

Jika push gagal:
- jangan mengulang coding;
- jangan membuat commit tambahan;
- laporkan commit hash lokal.

==================================================
11. LAPORAN AKHIR
==================================================

Berikan laporan:

## Database Audit
- database dependency
- database provider
- env configuration
- existing database layer
- existing data source
- auth dependency

## Decision
- provider yang dipilih
- alasan
- apakah sudah tersedia atau belum

## Implementation
Jika ada:
- file berubah
- perubahan

Jika tidak ada:
- jelaskan kenapa berhenti.

## Verification
- TypeScript
- Lint
- Build
- git diff --check

## Regression
- Phase 27
- Phase 28
- Phase 29

## Git
- HEAD
- commit
- working tree
- push status

## NEXT STEP
Tentukan task berikutnya berdasarkan kondisi aktual.

ATURAN UTAMA:
Jangan membuat database/provider baru tanpa alasan.
Jangan mengarang credential.
Jangan hardcode DATABASE_URL.
Jangan mengerjakan schema sebelum connection provider jelas.
Jangan melompat langsung ke Phase 25.
Jangan mengejar progress roadmap jika dependency belum siap.
Prioritaskan architecture yang benar dan checkpoint Git yang aman.


```
# Phase 26 Database
```

Lanjutkan dari hasil audit terakhir.

Repository: /root/toko-online
Model: GPT-5.6 Luna

KONDISI TERAKHIR:
- Working tree: CLEAN
- HEAD: 0f95e04
- Commit: feat(settings): complete admin password change
- Local main dan origin/main tracking berada pada commit yang sama.
- GitHub push/ls-remote mungkin tidak tersedia karena credential environment, tetapi JANGAN mengubah atau membuat commit baru hanya karena masalah authentication.
- Phase 27 Auth: PASS
- Phase 28 Caching: PASS
- Phase 29 SEO/Metadata/PWA: PASS
- Phase 25 belum selesai.

HASIL AUDIT PHASE 25:
Task berikutnya:
- Phase 25 Task 1 — GET /api/products

Namun task tersebut memiliki dependency/blocker nyata:
1. `export` tidak kompatibel dengan runtime Next.js Route Handlers.
2. Database/repository layer belum tersedia dengan benar.
3. Backend authentication/JWT yang dapat digunakan oleh protected API belum tersedia.
4. Memaksakan implementasi API sekarang berisiko membuat architecture workaround dan regression.

TUJUAN:
Jangan memaksakan Phase 25 Task 1 hanya agar roadmap terlihat maju.

Sekarang lakukan AUDIT DEPENDENCY untuk Phase 25 dan Phase 26.

==================================================
1. VALIDASI GIT
==================================================

Jalankan:

git status --short --branch
git branch -vv
git log -5 --oneline --decorate
git diff --check

Jangan:
- reset
- revert
- force push
- git clean
- hapus database
- hapus credentials
- mengubah commit 0f95e04

==================================================
2. BACA ROADMAP
==================================================

Baca:
- ROADMAP.md
- CHANGELOG.md
- package.json

Identifikasi:
- seluruh task Phase 25;
- seluruh task Phase 26;
- dependency Phase 25 terhadap Phase 26;
- apakah Phase 26 memang dirancang sebagai prerequisite API;
- task Phase 26 mana yang paling dasar dan harus dikerjakan terlebih dahulu.

Jangan menebak.

==================================================
3. AUDIT DATABASE ARCHITECTURE
==================================================

Periksa seluruh codebase untuk mencari:

- Prisma / Drizzle / SQLite / PostgreSQL / MongoDB atau database lain;
- schema;
- migrations;
- database client;
- repository;
- service;
- model;
- seed;
- connection handling;
- environment variables;
- existing data access;
- product/category/banner/order/payment tables atau collections.

Jawab secara konkret:

1. Database apa yang sebenarnya digunakan?
2. Apakah database sudah aktif?
3. Apakah schema sudah tersedia?
4. Apakah migration sudah tersedia?
5. Apakah repository/service layer sudah tersedia?
6. Bagaimana productService saat ini memperoleh data?
7. Apakah API dapat memakai service yang sama?
8. Apakah ada data mock/local JSON yang masih digunakan?
9. Apakah ada bagian database yang sudah dibuat tetapi belum terhubung?
10. Apakah Phase 26 memang wajib diselesaikan sebelum Phase 25?

==================================================
4. AUDIT AUTH DEPENDENCY
==================================================

Jangan membuat auth baru.

Periksa implementation Phase 27 yang sudah PASS.

Cari:
- middleware;
- JWT;
- session;
- cookie;
- jose;
- login;
- logout;
- `/api/auth/*`;
- admin authorization;
- helper untuk membaca current user/session.

Tentukan apakah API Phase 25 dapat menggunakan infrastructure tersebut.

Jika infrastructure sudah ada:
- gunakan yang existing.

Jika belum ada:
- jelaskan dependency-nya.

Jangan membuat authentication duplicate.

==================================================
5. AUDIT NEXT.JS ROUTE HANDLER
==================================================

Periksa semua `app/api/**/route.ts`.

Cari penggunaan:

- `export default`
- `GET`
- `POST`
- `PATCH`
- `PUT`
- `DELETE`
- `OPTIONS`

Tentukan pattern Route Handler yang benar untuk versi Next.js project ini.

Jangan mengubah seluruh API hanya karena satu task.

Jika `export default` memang salah:
- tentukan file mana yang benar-benar affected;
- jangan langsung refactor massal.

==================================================
6. TENTUKAN URUTAN YANG AMAN
==================================================

Buat dependency graph sederhana:

Database
↓
Repository / Data Access
↓
Service
↓
Authentication / Authorization
↓
API Route Handler
↓
Client/Admin

Tentukan bagian mana yang sudah tersedia dan bagian mana yang masih missing.

Jika Phase 26 Database adalah prerequisite nyata:
- jangan mengerjakan Phase 25 dengan mock;
- jangan membuat fake repository;
- jangan membuat API hardcoded;
- jangan mengubah roadmap secara sembarangan.

Pilih TASK PALING KECIL DAN PALING FUNDAMENTAL yang memang diperlukan untuk membuka blocker.

==================================================
7. JANGAN CODING DULU JIKA AUDIT BELUM SELESAI
==================================================

Tahap pertama harus audit.

Setelah audit dependency selesai, jika ada prerequisite Phase 26 yang jelas dan aman:

- implementasikan hanya prerequisite tersebut;
- jangan langsung mengerjakan seluruh Phase 26;
- jangan mengerjakan Phase 25 sekaligus;
- jangan melompat beberapa phase.

Jika prerequisite belum jelas:
- berhenti dan laporkan.

==================================================
8. VERIFICATION
==================================================

Jika ada perubahan kode:

WAJIB jalankan:

npm run lint
npm run build
git diff --check

Kemudian verifikasi behavior yang berkaitan.

Pastikan:
- Phase 27 auth tetap PASS;
- Phase 28 caching tetap PASS;
- Phase 29 SEO/metadata/PWA tetap PASS.

Jika build gagal:
- perbaiki root cause;
- jangan commit.

==================================================
9. GIT CHECKPOINT
==================================================

Jika perubahan berhasil dan semua verification PASS:

git status
git diff --stat
git diff --check

Pastikan hanya perubahan task ini.

Update ROADMAP.md dan CHANGELOG.md jika memang diperlukan.

Buat SATU commit:

feat(database): <deskripsi task sebenarnya>

atau gunakan scope yang sesuai dengan task sebenarnya.

Jangan membuat commit kosong.

Setelah commit:

git log -1 --oneline --decorate
git status --short --branch

Push hanya jika authentication GitHub tersedia.

Jika push gagal karena authentication:
- jangan mengulang coding;
- jangan membuat commit tambahan;
- laporkan hash commit lokal sebagai checkpoint.

==================================================
LAPORAN AKHIR
==================================================

Berikan:

## Dependency Audit
- Phase 25 blocker
- Phase 26 dependency
- database status
- auth status
- API architecture status

## Task Dikerjakan
- task
- alasan
- file berubah

## Verification
- TypeScript
- Lint
- Build
- git diff --check

## Regression
- Phase 27
- Phase 28
- Phase 29

## Git
- HEAD
- commit
- origin/main
- working tree
- push status

## Next Step
Tentukan task paling aman berikutnya berdasarkan dependency aktual.

PENTING:
Jangan mengejar angka progress roadmap.
Prioritaskan architecture yang benar, build yang PASS, dan checkpoint Git yang aman.

```
# Phase 25 API, Audit → Implementasi → Verify → Commit → Push
```

Lanjutkan development repository Digital Cell di:

/root/toko-online

MODEL: GPT-5.6 Luna

==================================================
KONDISI REPOSITORY
==================================================

Repository sudah melewati:

- Phase 27 Authentication: PASS / INTACT
- Phase 28 Caching: PASS / INTACT
- Phase 29 SEO / Metadata / PWA: PASS / COMPLETE
- TypeScript/build baseline sudah diperbaiki
- Working tree terakhir sudah CLEAN
- Commit backup terbaru:
  0f95e04
  feat(settings): complete admin password change

PENTING:
Commit 0f95e04 adalah checkpoint lokal yang harus dipertahankan.

JANGAN:
- git reset --hard
- git clean -fd
- force push
- menghapus commit
- menghapus database
- menghapus credentials
- menghapus perubahan existing
- mengganti dependency secara massal
- upgrade/downgrade framework tanpa kebutuhan task

GitHub digunakan sebagai backup jika VPS mati.

==================================================
TUJUAN UTAMA
==================================================

Lanjutkan project sesuai ROADMAP.md.

Target sekarang adalah:

PHASE 25 — API

JANGAN langsung coding.

Pertama-tama lakukan audit repository dan tentukan dengan tepat:

1. Phase 25 terdiri dari task apa saja.
2. Task mana yang sudah selesai.
3. Task mana yang belum selesai.
4. Task mana yang partially implemented.
5. Dependency setiap task.
6. Task pertama yang paling aman untuk dikerjakan sekarang.

Jangan menebak task berdasarkan percakapan sebelumnya.

ROADMAP.md + kondisi source code saat ini adalah source of truth.

==================================================
STEP 1 — VALIDASI GIT
==================================================

Sebelum membaca/mengubah source:

Jalankan:

git status
git status --short --branch
git branch -vv
git log -5 --oneline --decorate
git diff --check
git remote -v

Periksa:

- branch harus main;
- apakah HEAD sudah sama dengan origin/main;
- apakah local main ahead/behind;
- apakah working tree clean;
- apakah ada perubahan existing yang bukan pekerjaan task ini.

Jika local HEAD masih lebih maju daripada origin/main:

JANGAN membuat commit baru hanya untuk itu.

Jika credentials GitHub tersedia:
- push checkpoint yang sudah ada.

Jika push gagal karena authentication:
- JANGAN mengubah kode;
- JANGAN membuat commit kosong;
- laporkan bahwa backup GitHub belum berhasil.

Jika repository sudah sama dengan origin/main:
- lanjutkan audit.

==================================================
STEP 2 — BACA DOKUMENTASI
==================================================

Baca:

- ROADMAP.md
- CHANGELOG.md
- package.json

Kemudian cari dokumentasi yang berkaitan dengan:

- Phase 25
- API
- admin API
- product API
- category API
- banner API
- order API
- payment API
- authentication middleware
- service layer
- database layer

Jangan membuat dokumentasi baru jika dokumentasi existing sudah cukup.

==================================================
STEP 3 — AUDIT PHASE 25
==================================================

Lakukan audit menyeluruh terhadap implementasi Phase 25.

Cari seluruh:

- app/api/**
- src/app/api/**
- services
- repositories
- database access
- authentication helper
- validation/schema
- request/response types
- admin API
- public API
- mutation API
- route handlers

Untuk setiap API yang relevan, periksa:

1. HTTP method.
2. Authentication requirement.
3. Authorization requirement.
4. Input validation.
5. Error handling.
6. Response format.
7. Database operation.
8. Service-layer usage.
9. Cache interaction jika ada.
10. Cache invalidation jika mutation.
11. Ownership/access control.
12. Sensitive data exposure.
13. Type safety.
14. Empty state.
15. Not-found behavior.
16. Conflict behavior.
17. Concurrent request safety jika relevan.

Jangan hanya mencari apakah route file ada.

Pastikan implementasinya benar-benar bekerja.

==================================================
STEP 4 — CARI REGRESSION DARI PHASE 27–29
==================================================

Phase 25 tidak boleh merusak:

PHASE 27:
- middleware
- JWT
- session
- login
- logout
- protected routes
- admin authorization

PHASE 28:
- unstable_cache
- public cache tags
- revalidateTag
- product cache
- category cache
- banner cache
- tidak ada user/session data dalam public cache

PHASE 29:
- metadataBase
- canonical URL
- OpenGraph
- Twitter Card
- sitemap
- robots
- manifest
- JSON-LD

Jangan mengubah sistem tersebut kecuali Phase 25 memang memiliki dependency langsung.

==================================================
STEP 5 — AUDIT EXISTING IMPLEMENTATION
==================================================

Sebelum menulis kode baru:

Cari apakah functionality Phase 25 sudah sebagian ada.

Gunakan pencarian codebase untuk:

- route handler
- service
- validation
- database function
- related components
- existing API calls

Jika sudah ada:

JANGAN membuat duplicate.

Pertahankan implementation yang benar dan hanya lengkapi bagian yang kurang.

Jika ada dua implementation untuk fungsi yang sama:

- jangan langsung menghapus salah satunya;
- tentukan mana yang menjadi canonical implementation;
- pahami dependency;
- ubah seminimal mungkin.

==================================================
STEP 6 — IMPLEMENTASI
==================================================

Setelah audit selesai, kerjakan:

HANYA TASK PHASE 25 YANG PALING AWAL DAN SUDAH SIAP.

Jangan mengerjakan Phase 26 atau phase berikutnya.

Implementasi harus:

- TypeScript strict/type-safe;
- tidak menambahkan implicit any;
- tidak memakai any tanpa alasan kuat;
- mengikuti architecture existing;
- memakai service layer yang sudah ada;
- memakai validation yang sudah ada jika tersedia;
- mengikuti response pattern existing;
- mengikuti authentication pattern existing;
- tidak membocorkan password/token/credential;
- tidak mengembalikan field sensitif ke client;
- tidak hardcode data database;
- tidak menghapus functionality existing.

Jika API bersifat protected:
- gunakan authentication/authorization yang sudah ada;
- jangan membuat authentication system baru.

Jika API melakukan mutation:
- pastikan cache invalidation sesuai pattern Phase 28;
- jangan memasukkan user-specific data ke public cache.

Jika task membutuhkan schema/database:
- audit schema/migration terlebih dahulu;
- jangan mengubah database secara destruktif;
- jangan menghapus data existing.

==================================================
STEP 7 — VERIFICATION SETIAP TASK
==================================================

Setelah implementasi, WAJIB jalankan:

npm run lint

npm run build

git diff --check

Jika project memiliki test command yang relevan, jalankan.

Kemudian lakukan runtime verification terhadap API yang diubah.

Minimal verifikasi:

- expected success response;
- unauthorized response;
- forbidden response jika applicable;
- invalid input;
- not found;
- duplicate/conflict jika applicable;
- mutation success;
- database result;
- cache invalidation jika applicable.

Jangan menyatakan PASS hanya karena TypeScript compile.

==================================================
STEP 8 — REGRESSION TEST
==================================================

Setelah Phase 25 task selesai, verifikasi:

Homepage:
- HTTP 200

Products:
- HTTP 200

Categories:
- HTTP 200

Product detail:
- HTTP 200 untuk valid slug

Category detail:
- HTTP 200 untuk valid slug

Protected admin route:
- authenticated request berhasil

Unauthenticated protected route:
- redirect/401 sesuai behavior existing

API products:
- HTTP 200

API categories:
- HTTP 200

API banners:
- HTTP 200

API orders:
- authentication tetap benar

Phase 27:
- PASS

Phase 28:
- PASS

Phase 29:
- PASS

==================================================
STEP 9 — JIKA ADA ERROR
==================================================

Jika lint/build/test gagal:

JANGAN langsung membuat workaround.

Lakukan:

1. baca error lengkap;
2. cari root cause;
3. tentukan apakah error:
   - pre-existing,
   - berasal dari task ini,
   - atau berasal dari environment;
4. perbaiki root cause;
5. jalankan verification kembali.

Jika error berasal dari perubahan task:
- WAJIB diperbaiki sebelum commit.

Jika error pre-existing:
- jangan mengklaim project PASS tanpa menjelaskan error tersebut.

Jika perubahan menyebabkan regression:
- rollback hanya perubahan task tersebut secara aman;
- jangan menyentuh baseline commit sebelumnya;
- kemudian perbaiki dengan pendekatan yang benar.

==================================================
STEP 10 — DOKUMENTASI
==================================================

Jika Phase 25 task berhasil:

Update ROADMAP.md secara akurat.

Jangan menandai task selesai jika verification belum PASS.

Update CHANGELOG.md:

- tanggal;
- task;
- perubahan utama;
- file penting;
- verification;
- regression status.

Jangan mengubah histori dokumentasi lama secara tidak perlu.

==================================================
STEP 11 — GIT CHECKPOINT
==================================================

SEBELUM COMMIT:

Jalankan:

git status
git diff --stat
git diff --check

Periksa setiap file yang berubah.

Pastikan:

- hanya perubahan Phase 25;
- tidak ada .env;
- tidak ada credential;
- tidak ada database dump;
- tidak ada file temporary;
- tidak ada build artifact;
- tidak ada perubahan unrelated.

Jika ada perubahan unrelated:
- jangan hapus;
- pisahkan dari scope task;
- jangan masukkan ke commit jika tidak diperlukan.

==================================================
STEP 12 — COMMIT
==================================================

Jika dan hanya jika:

- TypeScript PASS
- Lint PASS
- Build PASS
- Runtime verification PASS
- Regression PASS
- git diff --check PASS
- dokumentasi sesuai
- perubahan scope jelas

buat SATU commit untuk task tersebut.

Format:

feat(api): complete phase 25 task <nomor>

Gunakan nomor/nama task sebenarnya dari ROADMAP.md.

JANGAN membuat commit jika verification gagal.

==================================================
STEP 13 — PUSH BACKUP
==================================================

Setelah commit:

Jalankan:

git status
git log -1 --oneline --decorate

Kemudian:

git push origin main

Jika push berhasil:

git status --short --branch

Pastikan:

- local main = origin/main
- working tree CLEAN

Jika push gagal karena authentication:

- JANGAN mengubah kode;
- JANGAN membuat commit kedua;
- jangan force push;
- laporkan commit hash yang belum ter-push.

==================================================
ATURAN VPS BACKUP
==================================================

Karena VPS sebelumnya pernah mati, setiap milestone yang benar-benar PASS harus mempunyai commit.

Namun jangan membuat commit setiap perubahan kecil.

Gunakan checkpoint:

1 task selesai
→ verification PASS
→ dokumentasi update
→ commit
→ push

Dengan demikian jika VPS mati:

git clone repository

atau:

git pull origin main

dapat mengembalikan seluruh progress sampai checkpoint terakhir.

==================================================
BATASAN KERAS
==================================================

JANGAN:

- mengerjakan beberapa Phase sekaligus;
- melompat ke Phase 26;
- mengubah Phase 27 tanpa alasan;
- mengubah caching Phase 28 tanpa dependency;
- mengubah SEO Phase 29 tanpa dependency;
- upgrade dependency massal;
- install package baru tanpa alasan;
- menghapus file;
- menghapus database;
- menghapus credentials;
- mengganti architecture;
- mengganti framework;
- membuat duplicate service;
- membuat duplicate API;
- membuat fake/mock implementation untuk production code;
- menandai task selesai hanya karena file sudah dibuat;
- commit ketika build gagal;
- push force;
- reset ke commit lama.

==================================================
JIKA PHASE 25 TERNYATA SUDAH SELESAI
==================================================

Jika setelah audit ditemukan bahwa seluruh Phase 25 memang sudah benar-benar selesai:

JANGAN membuat perubahan palsu.

Verifikasi seluruh Phase 25.

Jika semua PASS:
- update ROADMAP hanya jika status dokumentasinya belum sesuai;
- update CHANGELOG bila diperlukan;
- jangan membuat commit kosong;
- laporkan bahwa Phase 25 sudah complete;
- identifikasi Phase berikutnya.

Tetapi JANGAN langsung mengerjakan Phase berikutnya dalam run yang sama kecuali roadmap secara eksplisit menyatakan task tersebut bagian dari Phase 25.

==================================================
FORMAT LAPORAN AKHIR
==================================================

Berikan laporan:

## Phase 25 Audit
- task yang ditemukan
- task yang sudah selesai
- task yang belum selesai
- task yang dipilih
- alasan pemilihan

## Implementation
- file berubah
- perubahan utama
- dependency yang digunakan

## Verification
- TypeScript: PASS/FAIL
- Lint: PASS/FAIL
- Build: PASS/FAIL
- git diff --check: PASS/FAIL
- Runtime API: PASS/FAIL

## Regression
- Phase 27 auth: PASS/FAIL
- Phase 28 caching: PASS/FAIL
- Phase 29 SEO/metadata/PWA: PASS/FAIL

## Git
- commit hash
- commit message
- push: SUCCESS/FAIL
- HEAD
- origin/main
- working tree: CLEAN/DIRTY

## Remaining
- Phase 25 task berikutnya
- blocker jika ada
- Phase berikutnya setelah Phase 25 selesai

PENTING:
Jangan hanya memberikan rencana.

Jika task aman untuk dikerjakan, IMPLEMENTASIKAN, VERIFIKASI, COMMIT, DAN PUSH.

Jika ada blocker nyata, berhenti sebelum merusak baseline dan jelaskan blocker tersebut.

```
# Prompt — Lanjut Development Aman Setelah Baseline
```

Lanjutkan development repository Digital Cell di /root/toko-online.

MODEL: GPT-5.6 Luna

KONDISI BASELINE:
- Repository sudah diaudit.
- Phase 27 — Authentication: INTACT.
- Phase 28 — Caching: INTACT.
- Phase 29 — SEO/Metadata/PWA: COMPLETE dan sudah diverifikasi.
- TypeScript: PASS.
- Lint: PASS, hanya 2 warning pre-existing/non-blocking.
- Build: PASS.
- Working tree sudah CLEAN.
- Commit baseline terbaru: 90bafa8 — "fix: restore clean build baseline"
- Commit sudah berhasil di-push ke origin/main.
- GitHub sekarang menjadi backup utama jika VPS mati.
- JANGAN menganggap pekerjaan yang ada di commit tersebut hilang.
- JANGAN melakukan reset, revert, force push, atau menghapus commit baseline.

TUJUAN:
Lanjutkan development dari kondisi repository SAAT INI dengan mengikuti ROADMAP.md secara ketat.

ATURAN UTAMA:
1. Baca ROADMAP.md terlebih dahulu secara menyeluruh.
2. Baca CHANGELOG.md untuk memahami pekerjaan terakhir.
3. Periksa git status, branch, HEAD, dan commit terbaru.
4. Tentukan task/fase berikutnya yang benar-benar belum selesai.
5. Jangan mengerjakan task yang sudah ditandai selesai.
6. Jangan melompati dependency antar-task.
7. Jangan membuat roadmap baru.
8. Jangan mengubah requirement produk yang sudah ada.
9. Jangan melakukan refactor besar jika tidak diperlukan oleh task.
10. Pertahankan arsitektur dan pola kode yang sudah digunakan project.

PROSEDUR WAJIB SEBELUM CODING:

A. VALIDASI REPOSITORY
Jalankan:
- git status
- git branch -vv
- git log -5 --oneline --decorate
- git diff --check

Pastikan:
- branch main
- working tree bersih sebelum mulai
- origin/main sesuai dengan baseline yang sudah dipush

Jika working tree ternyata tidak bersih:
- JANGAN menghapus perubahan.
- Audit perubahan terlebih dahulu.
- Pisahkan perubahan existing/pre-existing dengan perubahan yang dibuat pada task ini.

B. BACA ROADMAP
Baca:
- ROADMAP.md
- CHANGELOG.md
- package.json
- konfigurasi Next.js/TypeScript yang relevan

Cari task pertama yang:
- belum selesai,
- tidak blocked,
- dependency-nya sudah tersedia,
- dan paling aman dikerjakan setelah Phase 29.

Jangan menebak nomor task dari percakapan.
Gunakan isi repository sebagai source of truth.

C. AUDIT SCOPE TASK
Sebelum mengubah file:
- identifikasi file yang akan diubah;
- identifikasi service/component/API yang sudah tersedia;
- pahami pattern existing;
- cari implementasi serupa yang sudah ada;
- pastikan tidak membuat duplicate component/service/helper;
- pastikan tidak mengubah behavior Phase 27/28/29.

Jika task ternyata sudah sebagian diimplementasikan:
- jangan membuat ulang;
- lanjutkan bagian yang belum selesai;
- pertahankan implementasi yang sudah benar.

IMPLEMENTASI:

Kerjakan task berikutnya secara lengkap, bukan sekadar membuat placeholder.

Ketentuan:
- gunakan TypeScript yang type-safe;
- jangan menggunakan implicit any;
- jangan menggunakan `any` kecuali benar-benar diperlukan dan diberi alasan;
- gunakan service/repository yang sudah ada;
- jangan bypass architecture hanya agar build cepat;
- jangan hardcode data yang seharusnya berasal dari database/API/config;
- gunakan Tailwind/design token yang sudah ada;
- jangan membuat warna random/hardcoded jika project sudah mempunyai token;
- pertahankan responsive behavior;
- pertahankan accessibility;
- pertahankan loading/error/empty state jika relevan;
- pertahankan authentication/protected route;
- jangan memasukkan credential, secret, token, password, atau data pribadi ke source code;
- jangan menghapus data/database;
- jangan mengubah migration/schema tanpa kebutuhan task yang jelas.

REGRESSION PROTECTION:

Setelah implementasi, WAJIB jalankan:

1. npm run lint
2. npm run build
3. git diff --check

Jika tersedia test/script lain yang relevan, jalankan juga.

Periksa kembali minimal:
- homepage
- products
- categories
- product detail
- category detail
- admin route yang relevan
- authentication/protected route
- API yang disentuh task
- caching Phase 28
- metadata/SEO Phase 29

Pastikan:
- tidak ada regression;
- tidak ada route yang tiba-tiba 500;
- tidak ada TypeScript error;
- tidak ada build error;
- tidak ada perubahan authentication;
- tidak ada user data masuk ke cache publik.

JIKA TERJADI ERROR:

Jangan berhenti pada error pertama dan jangan melakukan workaround asal.

Lakukan:
1. baca error lengkap;
2. identifikasi root cause;
3. perbaiki root cause;
4. jalankan ulang TypeScript/build/lint;
5. verifikasi kembali route terkait.

Jika error berasal dari kode pre-existing:
- jangan mengklaim task gagal tanpa investigasi;
- jelaskan apakah error tersebut memang pre-existing atau akibat perubahan task ini.

Jika perubahan task menyebabkan regression:
- perbaiki sampai baseline kembali PASS.
- jangan lanjut ke task berikutnya sebelum build kembali PASS.

GIT SAFETY / BACKUP:

Setelah task selesai dan SEMUA verification PASS:

1. Periksa:
   git status
   git diff --stat
   git diff --check

2. Pastikan hanya file yang memang berkaitan dengan task yang berubah.

3. Update ROADMAP.md sesuai status sebenarnya.

4. Update CHANGELOG.md dengan:
   - versi/entry berikutnya yang konsisten;
   - tanggal;
   - task yang dikerjakan;
   - file penting yang berubah;
   - hasil verification.

5. Buat SATU commit untuk milestone/task ini.

Format commit:
   feat(<scope>): complete <task>

JANGAN membuat commit jika:
- TypeScript gagal;
- lint gagal karena error baru;
- build gagal;
- regression belum selesai;
- perubahan belum jelas;
- working tree berisi perubahan unrelated.

SETELAH COMMIT:

Jalankan:
git status
git log -1 --oneline --decorate

Kemudian push:
git push origin main

Setelah push:
- pastikan push berhasil;
- pastikan local HEAD sama dengan origin/main;
- jalankan git status --short --branch.

TARGET AKHIR:
Working tree harus kembali CLEAN dan commit task terbaru harus sudah tersimpan di GitHub.

JANGAN:
- force push;
- git reset --hard;
- git clean -fd;
- menghapus file yang tidak berkaitan;
- menghapus credentials;
- menghapus database;
- mengubah package version tanpa alasan;
- upgrade dependency secara massal;
- mengganti framework;
- mengganti arsitektur;
- mengerjakan beberapa fase sekaligus;
- membuat fitur baru di luar ROADMAP.

PENTING:
Jika task berikutnya terlalu besar atau mempunyai dependency yang belum siap, JANGAN memaksakan implementasi.

Dalam kondisi tersebut:
- jelaskan blocker;
- jangan merusak baseline;
- jangan membuat perubahan setengah jadi;
- jangan commit pekerjaan yang belum selesai.

FORMAT LAPORAN AKHIR:

Tampilkan laporan ringkas namun lengkap:

## Task
- nomor dan nama task
- alasan task ini dipilih

## Files Changed
- daftar file
- fungsi/perubahan utama

## Verification
- TypeScript: PASS/FAIL
- Lint: PASS/FAIL
- Build: PASS/FAIL
- git diff --check: PASS/FAIL
- route/API yang diverifikasi

## Regression
- Phase 27 auth: PASS/FAIL
- Phase 28 caching: PASS/FAIL
- Phase 29 SEO/metadata/PWA: PASS/FAIL

## Git
- commit hash
- commit message
- push: SUCCESS/FAIL
- working tree: CLEAN/DIRTY
- local HEAD vs origin/main

## Remaining
- task berikutnya yang belum dikerjakan
- blocker jika ada

Jangan berhenti hanya karena implementasi sudah selesai.
Pastikan verification selesai, dokumentasi diperbarui, commit dibuat, dan commit berhasil di-push ke GitHub.

```
# Prompt: Final Regression Audit + Fix + Commit Backup
```

Prompt: Final Regression Audit — Fix All Errors Before Commit

Kita sedang memulihkan baseline repository Digital Cell.
JANGAN mengerjakan Phase 25–29 atau fitur baru apa pun.

Tujuan utama sekarang hanya:
1. Audit kondisi working tree.
2. Temukan seluruh regression/error yang menghalangi baseline.
3. Perbaiki semuanya dengan perubahan minimal.
4. Pastikan TypeScript, lint, build, dan diff semuanya PASS.
5. Baru setelah benar-benar PASS, commit dan push ke origin/main sebagai backup.

KONDISI VERIFIKASI TERAKHIR:

- Lint: PASS
- git diff --check: PASS
- Error invoice sebelumnya sudah tidak muncul.
- Build sekarang berhenti pada:
  src/app/(shop)/components/admin/BannerEditContent.tsx
  Error: Button tidak memiliki prop `asChild`.
- Jangan menganggap ini satu-satunya error. Setelah diperbaiki, jalankan build ulang dan periksa error berikutnya sampai benar-benar bersih.
- Working tree memiliki perubahan existing yang harus dipertahankan.
- package-lock.json memiliki perubahan existing.
- Ada perubahan sebelumnya pada layout.tsx, search page, invoice page, dan file lain yang sudah ada di working tree.
- Jangan menghapus perubahan existing hanya untuk membuat build PASS.

ATURAN KERJA:

1. MULAI DENGAN AUDIT, BUKAN LANGSUNG EDIT.

Jalankan:

git status --short --branch
git diff --check
git diff --stat
git diff --name-only

Kemudian periksa diff untuk memahami perubahan existing.

JANGAN:
- git reset --hard
- git checkout -- .
- git restore .
- menghapus package-lock.json
- menghapus database
- menghapus .env
- menghapus credentials
- force push
- upgrade/downgrade dependency tanpa alasan kuat
- mengerjakan fitur baru
- mengubah behavior bisnis yang tidak berkaitan dengan error

2. PERBAIKI ERROR CURRENT BUILD

Periksa:

src/app/(shop)/components/admin/BannerEditContent.tsx

Cari penggunaan Button dengan prop:

asChild

Periksa implementasi Button yang sebenarnya digunakan repository.
Jangan asal mengganti kode.

Tentukan apakah:
- Button memang wrapper shadcn yang seharusnya mendukung `asChild`,
- import Button salah,
- versi/implementasi Button berbeda,
- atau penggunaan `asChild` memang tidak kompatibel dengan Button repository saat ini.

Gunakan pola yang konsisten dengan komponen lain di repository.

Jika `asChild` memang tidak didukung, ubah penggunaan tersebut dengan solusi paling kecil dan aman tanpa merusak UI/behavior.

JANGAN membuat refactor besar.

3. SETELAH MEMPERBAIKI ERROR TERSEBUT:

Jalankan:

npx tsc --noEmit

Jika FAIL:
- perbaiki error TypeScript yang nyata.
- jangan memakai `any` sebagai jalan pintas.
- jangan mematikan strict checking.
- jangan menambahkan @ts-ignore/@ts-expect-error hanya untuk menyembunyikan error.
- perbaiki root cause.

Setelah TypeScript PASS, jalankan:

npm run lint

Jika FAIL:
- perbaiki semua error blocking.
- warning non-blocking boleh tetap jika memang pre-existing.
- jangan mengubah konfigurasi lint hanya untuk menghilangkan error.

Setelah lint PASS, jalankan:

npm run build

PENTING:
Build harus dijalankan sampai selesai.

Jika build menemukan error baru:
- audit error tersebut,
- perbaiki root cause,
- ulangi TypeScript,
- ulangi lint,
- ulangi build.

Jangan berhenti setelah error pertama saja.

Ulangi sampai:

TypeScript = PASS
Lint = PASS
Build = PASS

4. JAGA REGRESSION

Selama memperbaiki baseline, pastikan jangan merusak:

- authentication Phase 27
- middleware
- JWT/session
- protected routes
- payment/order flow
- admin routes
- product management
- category management
- banner management
- reports
- existing API
- caching Phase 28
- product/category data
- existing UI behavior

Jangan mengerjakan Phase 25–29.

5. SETELAH SEMUA PASS:

Jalankan:

git diff --check
git status --short --branch
git diff --stat

Periksa diff secara menyeluruh.

Pastikan:
- tidak ada credentials
- tidak ada .env
- tidak ada secret
- tidak ada database yang tidak sengaja berubah
- tidak ada file besar/temp/debug
- tidak ada perubahan dependency yang tidak diperlukan
- tidak ada perubahan Phase 25–29
- tidak ada perubahan yang hanya dibuat untuk menyembunyikan error

6. JIKA SEMUA VERIFIKASI PASS:

Buat SATU commit untuk menyimpan baseline yang sudah diperbaiki.

Commit message:

fix: restore clean build baseline

Kemudian:

git status --short --branch
git log -1 --oneline --decorate

7. PUSH KE GITHUB UNTUK BACKUP VPS:

Jalankan:

git push origin main

JANGAN force push.

Setelah push berhasil, verifikasi:

git status --short --branch
git log -1 --oneline --decorate
git ls-remote --heads origin main

Pastikan commit lokal dan origin/main menunjuk ke commit yang sama.

8. JIKA ADA MASALAH:

Jika TypeScript, lint, atau build masih FAIL:
- JANGAN commit.
- JANGAN push.
- Laporkan error lengkap dan file/fungsi penyebabnya.

Jika ada perubahan existing yang tidak jelas:
- JANGAN menghapusnya.
- Laporkan terlebih dahulu.

Jika build PASS tetapi git diff mengandung perubahan yang mencurigakan:
- JANGAN commit.
- STOP dan laporkan.

9. LAPORAN AKHIR WAJIB:

Tampilkan:

=== BASELINE VERIFICATION ===

TypeScript: PASS/FAIL
Lint: PASS/FAIL
Build: PASS/FAIL
git diff --check: PASS/FAIL

=== FILES FIXED ===
Daftar file yang benar-benar diperbaiki.

=== ROOT CAUSE ===
Jelaskan penyebab error BannerEditContent dan error lain jika ditemukan.

=== REGRESSION CHECK ===
Auth: PASS/FAIL
Admin: PASS/FAIL
Payment/Order: PASS/FAIL
Product: PASS/FAIL
Category: PASS/FAIL
Banner: PASS/FAIL
Reports: PASS/FAIL
Caching: PASS/FAIL

=== GIT ===
Commit: <hash>
Commit message: fix: restore clean build baseline
Push: PASS/FAIL
origin/main: <hash>
Working tree: CLEAN/NOT CLEAN

PENTING TERAKHIR:

Jangan menyatakan "aman" hanya karena satu error sudah diperbaiki.

"Aman untuk commit" hanya jika:
- TypeScript PASS
- Lint PASS
- Build PASS
- git diff --check PASS
- diff sudah diaudit
- tidak ada secret/credential yang ikut
- tidak ada fitur Phase 25–29 yang dikerjakan
- commit berhasil
- push origin/main berhasil

Jika salah satu gagal, jangan commit dan jangan push.

```

# Prompt sekarang — Final Baseline Fix + Commit + Push
```

Prompt: Final Baseline Fix and Backup

Lanjutkan dari hasil verification terakhir.

KONDISI:
- Lint: PASS
- git diff --check: PASS
- Build: FAIL
- Error build:
  src/app/(shop)/invoice/page.tsx
  `orders` memiliki implicit any[].
- Phase 25–29 BELUM dikerjakan.
- Jangan mengerjakan fitur Phase 25–29.
- package-lock.json sudah memiliki perubahan dari pekerjaan sebelumnya. Jangan menghapus/revert perubahan tersebut tanpa alasan.

TUGAS:

1. Periksa src/app/(shop)/invoice/page.tsx.
2. Temukan penyebab `orders` menjadi implicit any[].
3. Perbaiki TYPE ERROR tersebut dengan perubahan minimal dan mengikuti type/service/model yang sudah ada di repository.
4. Jangan menggunakan `any` sebagai solusi.
5. Jangan mengubah behavior invoice/order.
6. Jangan refactor file lain kecuali benar-benar diperlukan oleh type fix ini.

SETELAH FIX WAJIB JALANKAN:

npx tsc --noEmit
npm run lint
npm run build
git diff --check

Jika semua PASS:
1. Periksa `git status`.
2. Periksa `git diff --stat`.
3. Periksa `git diff` untuk memastikan tidak ada perubahan mencurigakan.
4. Pastikan credentials, .env, database, dan data tidak tersentuh.
5. Buat SATU commit khusus untuk seluruh baseline regression fixes yang saat ini memang sudah ada di working tree.

Gunakan commit message:

fix: restore clean build baseline

6. Setelah commit berhasil, jalankan:
   git status --short --branch
   git log -1 --oneline --decorate

7. PUSH commit ke origin/main sebagai backup VPS:
   git push origin main

8. Setelah push, verifikasi:
   git status --short --branch
   git log -1 --oneline --decorate
   git ls-remote --heads origin main

PENTING:
- Jangan mengerjakan Phase 25–29.
- Jangan membuat commit jika tsc/lint/build masih gagal.
- Jangan force push.
- Jangan reset --hard.
- Jangan menghapus perubahan existing.
- Jangan mengubah dependency/version kecuali memang diperlukan untuk memperbaiki error yang sedang diperiksa.
- Jika build masih gagal setelah perbaikan, STOP dan laporkan error baru. Jangan commit/push.

LAPORAN AKHIR WAJIB:
- file yang diperbaiki
- ringkasan perubahan
- TypeScript: PASS/FAIL
- Lint: PASS/FAIL
- Build: PASS/FAIL
- git diff --check: PASS/FAIL
- commit hash
- push: PASS/FAIL
- origin/main hash
- working tree clean atau tidak

```
# 
```
Prompt: Fix Baseline TypeScript/Lint/Build Errors

Lanjutkan dari hasil audit terakhir.

JANGAN mengerjakan Phase 25–29.
JANGAN melakukan refactor besar.
JANGAN upgrade/downgrade dependency.
JANGAN menghapus data atau credentials.

Ada 2 masalah yang harus diperbaiki:

1. LINT
File:
src/app/layout.tsx

Error:
@next/next/no-before-interactive-script-outside-document

Periksa penggunaan Script dengan strategy="beforeInteractive".
Perbaiki sesuai aturan Next.js dan struktur App Router yang digunakan repository.
Jangan mengubah behavior yang tidak terkait.

2. BUILD
File:
src/app/(shop)/search/page.tsx

Error:
await digunakan di fungsi yang belum async.

Periksa fungsi yang menggunakan await dan perbaiki dengan cara paling minimal dan sesuai arsitektur existing.
Jangan mengubah behavior search.

SETELAH FIX:
1. npx tsc --noEmit
2. npm run lint
3. npm run build
4. git diff --check
5. git diff --stat
6. Periksa diff agar hanya berisi fix untuk dua masalah di atas.

JANGAN COMMIT jika salah satu verification masih FAIL.

Jika semua PASS:
- buat SATU commit khusus baseline regression fix;
- jangan mengerjakan task Phase 25–29;
- laporkan commit hash dan hasil semua verification.

Jika masih FAIL:
- JANGAN commit;
- laporkan error yang tersisa dan file penyebabnya.


```
# Prompt — Regression Fix Before Phase 25–29
```
Lanjutkan dari audit sebelumnya.

KERJAKAN HANYA REGRESSION FIX yang sudah ditemukan. Jangan mengerjakan Phase 25–29 dulu.

1. Periksa package.json dan package-lock.json.
2. Regenerate package-lock.json dengan package manager yang memang digunakan repository.
3. Jalankan npm ci untuk memastikan clean install.
4. Perbaiki JSX escape `\"` yang valid/bermasalah pada:
   - BannerTable.tsx
   - CategoryTable.tsx
   Jangan mengubah behavior/UI selain memperbaiki syntax.
5. Jalankan:
   - npx tsc --noEmit
   - npm run lint
   - npm run build
6. Periksa git diff dengan teliti.
7. Pastikan tidak ada perubahan tidak terkait.
8. Jangan mengerjakan fitur Phase 25–29.
9. Jangan menghapus credentials/data.
10. Jika semua PASS, buat SATU commit khusus regression fix.

Format laporan:
- file yang diperbaiki
- dependency/lockfile yang berubah
- hasil TypeScript
- hasil Lint
- hasil Build
- commit hash
- apakah aman melanjutkan audit Phase berikutnya


```
# Prompt: Full Repository Audit — Phase 23–29 Recovery
```
Lakukan AUDIT MENYELURUH repository toko-online saat ini.

KONDISI:
- Repository: /root/toko-online
- Branch: main
- Commit HEAD saat ini: 5c9f0a8
- origin/main juga berada di 5c9f0a8
- Working tree harus diperlakukan sebagai baseline resmi.
- VPS sebelumnya mati sehingga pekerjaan Phase 28–29 yang pernah dikerjakan mungkin tidak lagi ada di repository.
- Tujuan audit adalah menentukan kondisi kode SEBENARNYA sebelum kita melanjutkan development.

ATURAN PENTING:
1. JANGAN mengubah source code.
2. JANGAN membuat commit.
3. JANGAN push.
4. JANGAN upgrade/downgrade dependency.
5. JANGAN menghapus file, database, credentials, atau data.
6. Jangan memperbaiki masalah apa pun.
7. Audit dan laporan saja.
8. Jangan menganggap ROADMAP.md benar hanya karena task tertulis [x]. Verifikasi status berdasarkan source code dan git history.

AUDIT GIT:
- Periksa git status.
- Periksa HEAD, origin/main, branch dan tag.
- Periksa commit history yang relevan.
- Cari apakah pernah ada commit Phase 23, 24, 25, 26, 27, 28, atau 29 di repository/history.
- Cari branch/tag/commit yang mungkin masih menyimpan pekerjaan Phase 27–29.
- Tentukan commit terakhir yang benar-benar berisi implementasi masing-masing Phase.

AUDIT ROADMAP:
Baca ROADMAP.md secara menyeluruh, terutama Phase 23–29.

Untuk setiap Phase 23, 24, 25, 26, 27, 28, 29:
- daftar task;
- status menurut ROADMAP;
- status sebenarnya berdasarkan source code;
- commit yang mengimplementasikannya jika ditemukan;
- apakah task benar-benar selesai, partial, atau hilang;
- file utama yang terkait.

AUDIT SOURCE CODE:
Verifikasi langsung source code untuk:
- admin/settings
- reports
- order/payment
- authentication/session/JWT/RBAC
- loading.tsx
- error.tsx
- next/image
- image configuration
- Suspense
- next/dynamic
- caching / unstable_cache
- revalidateTag
- generateStaticParams
- metadataBase
- generateMetadata
- canonical URL
- OpenGraph
- Twitter Card
- sitemap
- robots.txt
- JSON-LD Product
- PWA manifest

Khusus Phase 27:
Pastikan apakah:
- JWT benar-benar digunakan;
- HTTP-only cookie benar-benar digunakan;
- session benar-benar berasal dari cookie;
- protected routes benar-benar protected;
- RBAC benar-benar aktif;
- tidak ada MOCK_USER/mock token/mock authentication;
- tidak ada hardcoded user ID pada production flow.

Khusus Phase 28:
Pastikan apakah:
- image optimization benar;
- next/image sudah digunakan pada target components;
- remotePatterns memang diperlukan atau tidak;
- loading states benar-benar ada;
- error states benar-benar ada;
- Suspense boundary benar;
- next/dynamic benar;
- caching hanya diterapkan pada public data;
- data user/session tidak tercache;
- revalidateTag benar-benar meng-invalidasi cache;
- generateStaticParams product/category benar.

Khusus Phase 29:
Periksa apakah implementasi berikut benar-benar ada:
- metadataBase
- themeColor viewport
- Product generateMetadata
- Category generateMetadata
- Homepage metadata
- sitemap
- robots.txt
- Product JSON-LD
- manifest.ts

CARI PEKERJAAN YANG HILANG:
Bandingkan ROADMAP dengan source code dan git history.

Buat kategori:
A. SUDAH ADA DAN TERVERIFIKASI
B. ADA SEBAGIAN / PARTIAL
C. TERTULIS DI ROADMAP TAPI TIDAK ADA DI CODE
D. TIDAK ADA DAN MEMANG BELUM DIKERJAKAN
E. PERNAH ADA DI COMMIT/HISTORY TAPI SEKARANG HILANG DARI HEAD

Untuk kategori E, cari commit yang menyimpannya jika memungkinkan.

AUDIT REGRESSION:
Pastikan Phase sebelumnya tidak rusak.
Minimal verifikasi:
- TypeScript
- lint
- build
- auth/protected route
- API products
- API categories
- API banners
- orders/payment jika relevan

Jangan melakukan perubahan untuk memperbaiki error.
Jika command build/lint mahal atau lama, tetap prioritaskan audit statis terlebih dahulu lalu jalankan verification yang aman.

HASIL AKHIR WAJIB:

1. BASELINE REPOSITORY
   - HEAD
   - origin/main
   - working tree
   - commit terakhir

2. PHASE STATUS TABLE
   Buat tabel:
   Phase | ROADMAP | SOURCE CODE | GIT HISTORY | STATUS

3. TASK RECOVERY TABLE
   Task | Phase | Status | Evidence/File | Commit jika ada

4. PHASE 27 SECURITY STATUS

5. PHASE 28 PERFORMANCE STATUS

6. PHASE 29 SEO/PWA STATUS

7. MISSING/LOST WORK
   Jelaskan secara spesifik pekerjaan mana yang hilang akibat VPS mati.

8. REGRESSION STATUS

9. ROOT CAUSE / FINDINGS
   Pisahkan:
   - Critical
   - High
   - Medium
   - Low
   - Documentation-only

10. RECOMMENDED NEXT STEP
   Tentukan SATU task pertama yang paling aman untuk dikerjakan setelah audit.

PENTING:
Jangan langsung membuat prompt implementasi.
Jangan memperbaiki kode.
Jangan commit.
Audit harus selesai terlebih dahulu dan berikan laporan lengkap.


```

# Prompt: Phase 29 — Task 6 + 7 + 9 Final
```


Prompt: Phase 29 — Final Tasks 6, 7, 9

Lanjutkan repository toko-online / Digital Cell dari kondisi saat ini.

STATUS SAAT INI:
Phase 27 = selesai dan harus tetap intact.
Phase 28 = selesai dan harus tetap intact.
Phase 29:
- Task 1 = DONE
- Task 2 = DONE
- Task 3 = DONE
- Task 4 = DONE
- Task 5 = DONE
- Task 8 = DONE
- Task 6 = TODO
- Task 7 = TODO
- Task 9 = TODO

Target: selesaikan Task 6, 7, dan 9 sekaligus sehingga Phase 29 menjadi 9/9 = 100%.

PENTING:
- Jangan mengubah fitur yang sudah selesai.
- Jangan merusak Phase 27 authentication.
- Jangan merusak Phase 28 caching.
- Jangan mengubah database/schema tanpa kebutuhan.
- Jangan membuat fake data.
- Jangan membuat asset/image palsu.
- Gunakan pola dan konfigurasi yang sudah ada di repository.
- Sebelum mengubah file, audit implementasi yang sudah ada.
- Jika suatu bagian sebenarnya sudah benar, jangan ubah tanpa alasan.
- Jangan melakukan refactor besar di luar scope task.
- Setelah semua selesai jalankan TypeScript, lint, build, dan runtime verification.

TASK 6 — robots.ts

Implementasikan robots.txt menggunakan pola Next.js App Router yang sesuai dengan versi project.

Requirement:
- Buat/sempurnakan app/robots.ts jika belum ada.
- Gunakan siteConfig.url sebagai base URL.
- Public routes yang boleh di-crawl:
  /
  /products
  /categories
  /product/*
  /category/*
- Jangan allow:
  /admin/*
  /api/*
  /auth/*
  /profile
  /orders/*
  /checkout
  /invoice/*
  /payment/*
  /favorites/*
  /search/*
  /cart/*
- Sitemap harus menunjuk ke URL absolute:
  ${siteConfig.url}/sitemap.xml
- Jangan hardcode domain.
- Pastikan format robots sesuai Next.js MetadataRoute.Robots.

TASK 7 — Product JSON-LD

Tambahkan structured data JSON-LD untuk halaman detail produk:
app/src/app/(shop)/product/[slug]/page.tsx

Requirement:
- Gunakan data product yang benar dari product service/repository.
- Jangan hardcode produk.
- JSON-LD harus menggunakan schema.org Product.
- Sertakan data yang memang tersedia dan valid, misalnya:
  name
  description
  image jika tersedia dan valid
  sku jika tersedia
  offers jika harga/stok tersedia.
- Jangan membuat nilai palsu jika field tidak tersedia.
- Pastikan JSON-LD aman untuk React/Next.js dan tidak menyebabkan XSS.
- Gunakan JSON.stringify untuk serialisasi.
- Tambahkan script type="application/ld+json".
- Pastikan metadata Phase 29 Task 1 yang sudah ada tetap bekerja.
- Jika produk tidak ditemukan, jangan menghasilkan structured data palsu.

TASK 9 — manifest.ts

Implementasikan Web App Manifest menggunakan Next.js MetadataRoute.Manifest.

Requirement:
- Buat app/manifest.ts jika belum ada.
- Gunakan konfigurasi project yang sudah tersedia, terutama siteConfig.
- Gunakan nama aplikasi Digital Cell.
- Gunakan short_name yang sesuai.
- Gunakan start_url "/".
- Gunakan display "standalone".
- Gunakan theme/background color yang konsisten dengan theme project.
- Jangan mengarang file icon yang belum ada.
- Karena public/ sebelumnya diketahui belum memiliki asset icon yang valid, jangan membuat referensi icon palsu.
- Jika icon belum tersedia, manifest tetap harus valid tanpa icon atau gunakan hanya asset yang benar-benar ada.
- Jangan membuat fake PNG/SVG.

VERIFIKASI WAJIB:

1. TypeScript:
   npx tsc --noEmit

2. Lint:
   npm run lint
   atau command lint yang memang digunakan repository.

3. Production build:
   npm run build

4. Runtime:
   - GET /robots.txt harus 200 dan isinya benar.
   - GET /sitemap.xml harus tetap 200.
   - Product detail harus 200.
   - Product detail harus memiliki JSON-LD Product.
   - GET /manifest.webmanifest atau endpoint manifest yang sesuai harus 200.
   - Protected routes tetap redirect ke login.
   - API products/categories tetap 200.
   - Pastikan Phase 28 cache tetap intact.

5. Periksa regresi:
   - Phase 27 authentication tidak berubah.
   - Phase 28 caching tidak berubah.
   - Task 1/2/3/4/5/8 Phase 29 tetap intact.

SETELAH SELESAI:

Berikan laporan singkat:
- Task 6 status
- Task 7 status
- Task 9 status
- file yang diubah
- TypeScript result
- Lint result
- Build result
- runtime result
- regression result Phase 27/28
- apakah Phase 29 sekarang 9/9 = 100%
- masalah tersisa jika ada.

Jika semua PASS, nyatakan dengan jelas:

"PHASE 29 — 100% COMPLETE (9/9)"

Jangan lanjut ke Phase 30.
```

# 
```
Phase 29 — Task 5: Dynamic Sitemap

Kerjakan hanya Task 5 Phase 29: implementasi/update sitemap.ts pada repository toko-online.

Tujuan:
Buat sitemap.xml dinamis berdasarkan data publik yang benar-benar tersedia dari database/service.

Ketentuan:
1. Audit terlebih dahulu struktur route publik dan service yang sudah ada.
2. Gunakan data dari productService dan categoryService yang sudah digunakan Phase 28.
3. Sitemap harus mencakup:
   - /
   - /products
   - /categories
   - /products/[slug] untuk produk aktif/publik
   - /category/[slug] untuk kategori aktif/publik
4. Jangan masukkan route:
   - /admin/*
   - /api/*
   - /auth/*
   - /profile
   - /orders/*
   - /checkout
   - /invoice/*
   - /payment/*
   - /favorites/*
   - /search/*
   - route private lainnya.
5. Gunakan URL absolute berdasarkan siteConfig.url / metadataBase yang sudah dibuat pada Phase 29 Task 8.
6. Jangan hardcode slug produk atau kategori.
7. Gunakan service/database yang sudah ada, jangan membuat akses database baru jika service existing dapat digunakan.
8. Perhatikan caching Phase 28:
   - data publik boleh menggunakan caching yang sudah ada;
   - jangan pernah memasukkan data user/session ke cache.
9. Jangan mengubah authentication Phase 27.
10. Jangan mengubah caching Phase 28 selain jika benar-benar diperlukan untuk sitemap.
11. Jangan mengerjakan Task 6, 7, atau 9.
12. Jangan melakukan refactor besar.

Setelah implementasi:
- jalankan TypeScript check;
- jalankan lint;
- jalankan production build;
- verifikasi endpoint /sitemap.xml;
- pastikan XML valid;
- pastikan URL yang dihasilkan absolute;
- pastikan produk/kategori dari database muncul;
- pastikan route private tidak muncul;
- pastikan Phase 27 auth tetap intact;
- pastikan Phase 28 caching tetap intact.

Laporkan:
- file yang berubah;
- implementasi yang dilakukan;
- jumlah URL sitemap;
- contoh URL yang dihasilkan;
- hasil TypeScript;
- hasil lint;
- hasil build;
- hasil runtime /sitemap.xml;
- masalah yang masih tersisa jika ada.

Jangan mengklaim selesai sebelum benar-benar diverifikasi.


``
# 
```
Prompt: Phase 29 Task 3 — Homepage OG + Twitter Card

Kerjakan HANYA Phase 29 Task 3.

Tujuan:
Lengkapi metadata SEO homepage Digital Cell, terutama Open Graph dan Twitter Card, dengan menggunakan metadataBase dari Phase 29 Task 8.

Scope:
1. Audit:
   app/src/app/(shop)/page.tsx
   dan layout/metadata homepage yang sudah ada.
2. Tambahkan atau lengkapi generateMetadata untuk homepage jika memang diperlukan.
3. Pastikan metadata homepage mencakup:
   - title
   - description
   - Open Graph
   - og:title
   - og:description
   - og:type = website
   - og:url
   - og:image jika tersedia dan valid
   - Twitter Card
   - twitter:title
   - twitter:description
   - twitter:image jika tersedia dan valid
4. Gunakan metadataBase dari Phase 29 Task 8.
5. Jangan hardcode domain baru.
6. Gunakan konfigurasi/data project yang sudah ada.
7. Jangan membuat gambar dummy hanya untuk memenuhi metadata.
8. Jangan mengubah UI homepage.
9. Jangan mengubah auth Phase 27.
10. Jangan mengubah caching Phase 28.
11. Jangan mengerjakan Task 5, 6, 7, atau 9.
12. Jangan melakukan refactor besar.

Verifikasi:
- TypeScript
- Lint
- production build
- runtime homepage HTTP 200
- periksa HTML homepage dan pastikan OG tags ada
- periksa Twitter Card tags
- pastikan URL metadata menjadi absolute melalui metadataBase
- pastikan Phase 27 auth tetap intact
- pastikan Phase 28 caching tetap intact

Setelah selesai:
1. Tampilkan file yang berubah.
2. Tampilkan metadata homepage yang dihasilkan.
3. Tampilkan hasil verifikasi HTML/meta tags.
4. Tampilkan hasil TypeScript/Lint/Build.
5. Pastikan Task 3 benar-benar 100% selesai.
6. Update ROADMAP.md dan CHANGELOG.md sesuai pola project.

Jika menemukan masalah di luar scope Task 3, jangan diperbaiki. Laporkan saja.


``
# 
```


Prompt: Phase 29 Task 2 — Category generateMetadata

Kerjakan HANYA Phase 29 Task 2.

Tujuan:
Selesaikan metadata SEO untuk halaman detail category menggunakan generateMetadata dan metadataBase yang sudah tersedia.

Scope:
1. Audit:
   app/src/app/(shop)/category/[slug]/page.tsx
   dan category service yang digunakan.
2. Lengkapi generateMetadata untuk category detail.
3. Pastikan mencakup:
   - title
   - description
   - Open Graph
   - og:title
   - og:description
   - canonical URL
   - Twitter Card
   - twitter:title
   - twitter:description
4. Gunakan data category dari database/service, bukan hardcode/mock.
5. Gunakan metadataBase dari Phase 29 Task 8 untuk URL absolut.
6. Tangani category yang tidak ditemukan dengan graceful fallback.
7. Jangan mengubah generateStaticParams dari Phase 28.
8. Jangan mengubah caching Phase 28.
9. Jangan mengubah auth/middleware Phase 27.
10. Jangan mengerjakan Task 3, 5, 6, 7, atau 9.
11. Jangan melakukan refactor besar.

Verifikasi:
- TypeScript
- Lint
- production build
- test category valid
- test slug category tidak ditemukan
- verifikasi canonical URL absolute
- verifikasi Open Graph
- verifikasi Twitter Card
- pastikan Phase 27 auth intact
- pastikan Phase 28 caching intact

Setelah selesai:
- tampilkan file yang berubah;
- tampilkan metadata yang dihasilkan;
- hasil TypeScript/Lint/Build;
- hasil test valid dan invalid;
- pastikan Task 2 benar-benar 100% selesai;
- update ROADMAP.md dan CHANGELOG.md sesuai pola project.

Jika menemukan masalah di luar scope Task 2, jangan diperbaiki. Laporkan saja.
```
# 
```
Prompt: Phase 29 Task 4 — themeColor + viewport

Kerjakan HANYA Phase 29 Task 4.

Tujuan:
Tambahkan metadata themeColor dan viewport sesuai standar Next.js terbaru tanpa mengubah behavior aplikasi.

Scope:
1. Audit app/src/app/layout.tsx.
2. Tambahkan konfigurasi themeColor/viewport pada metadata sesuai API Next.js yang digunakan project.
3. Gunakan nilai yang sudah konsisten dengan tema Digital Cell/project.
4. Jangan mengubah metadataBase yang sudah selesai pada Task 8.
5. Jangan mengerjakan Task 1, 2, 3, 5, 6, 7, atau 9.
6. Jangan mengubah auth, API, database, caching, routing, atau komponen bisnis.

Verifikasi:
- TypeScript
- Lint
- production build
- pastikan Phase 27 auth tetap intact
- pastikan Phase 28 caching tetap intact
- pastikan metadataBase dari Task 8 tetap ada

Setelah selesai:
- tampilkan file yang berubah;
- tampilkan perubahan;
- hasil TypeScript/Lint/Build;
- update ROADMAP.md dan CHANGELOG.md sesuai pola project;
- nyatakan apakah Task 4 benar-benar 100% selesai.

Jangan melakukan refactor besar.
Jika menemukan masalah di luar Task 4, jangan diperbaiki. Laporkan saja.


```

# Prompt Audit Phase 29
```

Prompt: Audit Phase 29 — Toko Online

Lakukan AUDIT SAJA pada project toko-online yang sedang aktif di /root/toko-online.

JANGAN mengubah kode.
JANGAN membuat file baru.
JANGAN memperbaiki bug.
JANGAN menghapus data.
JANGAN menjalankan refactor.
JANGAN mulai mengerjakan Phase 29.

Konteks:
- Phase 26 sudah selesai 100%.
- Phase 27 Authentication sudah selesai 100%.
- Phase 28 Performance sudah selesai 100% (12/12).
- Phase 28 terakhir diverifikasi:
  TypeScript PASS
  Lint PASS, hanya 2 warning pre-existing
  Build PASS 52/52 pages
  Runtime utama PASS.
- Phase 28 Task 12 generateStaticParams category/[slug] sudah COMPLETE.
- Jangan mengulang pekerjaan Phase 26, 27, atau 28 yang sudah selesai.

Tujuan audit:
Tentukan secara akurat apa isi resmi Phase 29 berdasarkan kondisi repository SAAT INI.

Periksa:

1. ROADMAP.md
   - Temukan definisi resmi Phase 29.
   - Catat semua task/subtask Phase 29.
   - Hitung total task Phase 29.
   - Tandai mana yang sudah selesai, partial, deferred, atau belum dikerjakan.

2. CHANGELOG.md
   - Cari perubahan terakhir.
   - Cocokkan dengan ROADMAP.
   - Deteksi task yang sudah dikerjakan tetapi roadmap belum diperbarui.

3. Git status dan commit terakhir
   - Tampilkan branch aktif.
   - Tampilkan status working tree.
   - Identifikasi pekerjaan terbaru setelah Phase 28.

4. Source code
   - Audit file yang berkaitan dengan Phase 29.
   - Jangan hanya percaya checklist ROADMAP.
   - Pastikan implementasi benar-benar ada.

5. Cari TODO / FIXME / placeholder / mock yang berkaitan dengan Phase 29.

6. Audit dependency Phase 29 terhadap Phase 27 authentication dan Phase 28 performance.
   - Jangan sampai implementasi Phase 29 merusak auth.
   - Jangan sampai caching/session user rusak.
   - Jangan sampai protected routes berubah perilakunya.
   - Jangan meng-cache data private/user-specific.

7. Quality check:
   - TypeScript
   - ESLint
   - production build
   Hanya jalankan check, jangan memperbaiki error.

8. Bandingkan:
   ROADMAP → CHANGELOG → commit terakhir → source code → hasil build.

HASIL YANG WAJIB DIBERIKAN:

A. Status Phase 29:
   - Belum mulai / sedang berjalan / sebagian selesai / selesai.

B. Daftar seluruh task Phase 29 dalam tabel:
   Task | Status | File terkait | Bukti implementasi

C. Task yang ternyata sudah selesai tetapi belum tercatat di ROADMAP.

D. Task yang ditandai selesai tetapi implementasinya belum lengkap.

E. Masalah/bug yang ditemukan.

F. Dependency atau risiko terhadap Phase 27 dan Phase 28.

G. Urutan pengerjaan Phase 29 yang paling aman.

H. Tentukan dengan jelas:
   "Phase 29 siap dikerjakan"
   atau
   "Phase 29 belum siap, ada gap yang harus diselesaikan terlebih dahulu."

PENTING:
Audit ini READ-ONLY.
Jangan melakukan perubahan apa pun.
Setelah audit selesai, berhenti dan tunggu instruksi berikutnya.

```
# Prompt — Phase 28 Task 12
```

Lanjutkan Phase 28 dari posisi terakhir.

STATUS:
- Task 1–11 sudah COMPLETE.
- Task 10 caching COMPLETE.
- Task 11 cache tags/revalidation COMPLETE.
- Categories mutation handler gap adalah pre-existing dan JANGAN diperbaiki dalam Task 12.
- TypeScript PASS.
- Lint PASS, hanya 2 warning pre-existing.
- Build PASS 46/46.

Sekarang kerjakan HANYA:

TASK 12 — generateStaticParams untuk category/[slug]

Audit terlebih dahulu file:

app/src/app/(shop)/category/[slug]/page.tsx

Tujuan:
Tambahkan generateStaticParams untuk category/[slug] jika memang aman dan sesuai dengan arsitektur project.

Persyaratan:
1. Gunakan sumber data kategori yang sudah tersedia di service/repository.
2. Jangan membuat data kategori baru/hardcode.
3. Pastikan slug yang dihasilkan berasal dari database/data kategori nyata.
4. Jangan melakukan static generation terhadap data user/session-specific.
5. Jangan mengubah authentication atau protected routes.
6. Jangan mengubah Prisma schema.
7. Jangan mengubah API contract.
8. Jangan mengubah caching Task 10–11 kecuali benar-benar diperlukan untuk generateStaticParams.
9. Pertahankan dynamic behavior untuk kategori yang tidak termasuk hasil generateStaticParams.
10. Periksa apakah halaman category/[slug] membutuhkan dynamicParams atau konfigurasi lain agar behavior existing tidak rusak.
11. Jangan mengerjakan perubahan Phase 29 atau task lain di luar scope.

VERIFIKASI:
1. TypeScript check.
2. Lint.
3. Production build.
4. Pastikan category/[slug] berhasil di-generate sesuai data yang tersedia.
5. Test minimal satu category slug yang valid.
6. Test slug kategori yang tidak valid tetap menghasilkan behavior/error yang benar.
7. Pastikan homepage, products, orders, profile, admin tetap tidak regression.
8. Pastikan authentication Phase 27 tetap intact.

SETELAH SELESAI, LAPORKAN:
- file yang diubah;
- implementasi generateStaticParams;
- sumber data yang digunakan;
- jumlah/daftar slug yang terdeteksi jika aman dilaporkan;
- konfigurasi dynamicParams jika diubah;
- hasil TypeScript;
- hasil lint;
- hasil build;
- hasil runtime;
- status Task 12: COMPLETE/PARTIAL;
- masalah yang masih tersisa.

JANGAN lanjut ke Phase 29 sebelum laporan Task 12 selesai.

```

# Phase 28 — Task 10 + 11: caching.
```
Lanjutkan Phase 28 dari posisi terakhir.

STATUS:
- Task 1–4 COMPLETE
- Task 5–6 COMPLETE
- Task 8–9 COMPLETE
- TypeScript PASS
- Lint PASS, hanya 2 warning pre-existing
- Build PASS 46/46
- Runtime PASS
- Phase 26 dan Phase 27 tetap intact

Sekarang kerjakan HANYA:

TASK 10 — Caching
TASK 11 — Cache tags / revalidation

AUDIT TERLEBIH DAHULU sebelum mengubah kode.

Tujuan utama:
Mengoptimalkan caching untuk DATA PUBLIK tanpa pernah melakukan cache terhadap data yang bergantung pada session/JWT/user.

WAJIB DIPERHATIKAN:
- products, categories, banners = data publik → kandidat caching.
- orders, payments, profile, checkout, user-specific data = JANGAN di-cache sebagai shared/public cache.
- Jangan cache response berdasarkan user/JWT secara global.
- Jangan menyebabkan user A melihat data user B.
- Jangan mengubah authentication Phase 27.

TASK 10:
1. Audit seluruh server fetch/service/repository yang mengambil:
   - products
   - categories
   - banners
   - orders
   - payments
   - profile
2. Tentukan mana yang aman untuk cache.
3. Terapkan caching hanya pada data publik yang memang aman.
4. Gunakan mekanisme caching Next.js yang sesuai dengan versi project.
5. Jangan meng-cache data session/user.
6. Jangan mengubah API contract atau Prisma schema.

TASK 11:
1. Tambahkan cache tags/revalidation untuk data publik yang tepat.
2. Pastikan mutation admin dapat melakukan invalidasi cache yang relevan.
3. Contoh:
   - perubahan product → invalidate product cache
   - perubahan category → invalidate category cache
   - perubahan banner → invalidate banner cache
4. Jangan melakukan invalidasi global jika tidak diperlukan.
5. Jangan menambahkan cache tag pada orders/payments/profile jika data tersebut bersifat user/session-specific.
6. Pastikan setelah mutation, data terbaru dapat muncul tanpa harus menunggu cache stale yang tidak perlu.

WAJIB AUDIT:
- Cari semua penggunaan unstable_cache/cache/revalidate/revalidateTag/revalidatePath yang sudah ada.
- Jangan membuat duplicate caching layer.
- Periksa apakah project menggunakan API route atau direct service/repository sehingga caching ditempatkan di layer yang benar.
- Pastikan caching tidak merusak Phase 26 order/payment flow.
- Pastikan protected routes tetap dynamic/session-aware.

VERIFIKASI:
1. TypeScript check.
2. Lint.
3. Production build.
4. Test homepage/products/categories.
5. Test admin mutation untuk product/category/banner jika memungkinkan.
6. Pastikan perubahan public data melakukan invalidasi yang benar.
7. Test orders/profile/payment tetap mengambil data berdasarkan session user.
8. Pastikan user-specific data tidak masuk shared cache.
9. Pastikan authentication Phase 27 tetap intact.

PENTING:
- Jangan mengerjakan Task 12.
- Jangan mengubah Prisma schema.
- Jangan mengubah JWT/cookie/auth middleware.
- Jangan refactor besar.
- Jangan memperbaiki 2 warning lint pre-existing.
- Jika caching tidak aman pada suatu area, JANGAN dipaksakan. Laporkan dan biarkan dynamic.

LAPORAN:
- Semua area yang diaudit.
- Data yang diberi caching.
- Data yang sengaja TIDAK di-cache dan alasannya.
- Cache tags yang dibuat.
- Mutation yang melakukan invalidasi.
- File yang diubah.
- TypeScript result.
- Lint result.
- Build result.
- Runtime result.
- Bukti bahwa orders/payments/profile tidak terkena shared cache.
- Status Task 10: COMPLETE/PARTIAL.
- Status Task 11: COMPLETE/PARTIAL.
- Masalah yang masih tersisa.

BERHENTI setelah Task 10 + 11 selesai.
Jangan lanjut Task 12.


```
# Prompt — Phase 28 Task 8 + 9
```

Lanjutkan Phase 28 dari posisi terakhir.

Status:
- Task 1–4 COMPLETE
- Task 5 loading.tsx COMPLETE
- Task 6 error.tsx COMPLETE
- TypeScript PASS
- Lint PASS, hanya 2 warning pre-existing
- Build PASS 46/46
- Runtime PASS
- Phase 26 dan Phase 27 tetap intact

Sekarang kerjakan HANYA TASK 8 + TASK 9.

TASK 8 — Suspense boundaries
TASK 9 — next/dynamic untuk komponen yang tepat

AUDIT DULU sebelum mengubah kode.

1. Cari komponen/page yang melakukan server fetch atau rendering yang berpotensi menahan seluruh halaman.
2. Cari komponen berat/client component yang tidak perlu dimuat pada initial render.
3. Cari penggunaan Suspense yang sudah ada agar tidak membuat nested/double loading yang buruk.
4. Identifikasi route yang benar-benar membutuhkan Suspense atau dynamic import.

Prioritaskan area:
- homepage
- products
- categories
- profile
- orders
- admin
- FAQ/instructions/footer-heavy sections
- komponen interaktif/berat yang tidak diperlukan untuk initial viewport

TASK 8:
- Tambahkan <Suspense> hanya pada boundary yang memang memberikan manfaat.
- Gunakan fallback yang konsisten dengan loading UI Phase 28.
- Jangan membuat Suspense hanya untuk memenuhi task.
- Jangan menyebabkan double skeleton/flash.
- Jangan mengubah business logic atau API.

TASK 9:
- Gunakan next/dynamic hanya untuk komponen yang memang layak lazy-load.
- Prioritaskan komponen berat/non-critical atau client-only yang tidak dibutuhkan initial viewport.
- Jangan dynamic-import komponen yang seharusnya langsung tersedia untuk SEO/initial render.
- Jangan menggunakan ssr:false kecuali memang diperlukan dan sudah dibuktikan.
- Pertahankan behavior dan tampilan existing.

PENTING:
- Jangan ubah Prisma/schema/database.
- Jangan ubah authentication/JWT/cookie Phase 27.
- Jangan ubah API contract.
- Jangan mengerjakan Task 10–12.
- Jangan melakukan refactor besar.
- Jangan memperbaiki warning pre-existing yang tidak berkaitan.

VERIFIKASI:
1. TypeScript check.
2. Lint.
3. Production build.
4. Runtime homepage.
5. Runtime products/categories.
6. Runtime admin dengan auth.
7. Pastikan protected routes Phase 27 tetap protected.
8. Pastikan tidak ada hydration error atau regression.

LAPORAN:
- hasil audit sebelum perubahan;
- komponen yang diberi Suspense;
- komponen yang diberi next/dynamic;
- alasan setiap perubahan;
- komponen yang sengaja TIDAK diubah dan alasannya;
- TypeScript result;
- Lint result;
- Build result;
- Runtime result;
- status Task 8: COMPLETE/PARTIAL;
- status Task 9: COMPLETE/PARTIAL;
- masalah tersisa.

Berhenti setelah Task 8 + 9. Jangan lanjut Task 10–12.

```
# Prompt — Phase 28 Task 5 + 6
```

Lanjutkan Phase 28 setelah Task 3 dan Task 4.

Status sebelumnya:
- Task 2 — COMPLETE
- Task 3 (img → next/image) — COMPLETE, 4/4
- Task 4 priority — COMPLETE
- Task 4 blurDataURL — DEFERRED karena tidak ada sumber blur yang valid
- TypeScript PASS
- Lint PASS, hanya 2 warning pre-existing
- Build PASS 46/46
- Phase 26 dan Phase 27 tidak boleh mengalami regression.

Sekarang kerjakan HANYA:

TASK 5 — Loading states
TASK 6 — Error boundaries

Tujuan:
Menambahkan loading.tsx dan error.tsx pada route groups/pages yang memang membutuhkan, tanpa mengubah business logic.

LANGKAH 1 — AUDIT DULU
Cari seluruh route di app/src/app dan tentukan:
- route yang melakukan server fetch / dynamic data;
- route yang membutuhkan loading UI;
- route yang membutuhkan error boundary;
- route yang sudah memiliki loading.tsx/error.tsx.

Jangan membuat file secara membabi buta.

TASK 5 — loading.tsx
Tambahkan loading.tsx hanya pada route yang memang membutuhkan loading state.

Prioritaskan:
- products
- categories
- profile
- orders
- checkout
- admin routes yang melakukan server/data loading

Gunakan UI loading yang konsisten dengan desain existing:
- jangan membuat redesign;
- gunakan skeleton/spinner sederhana;
- jangan mengubah layout utama;
- jangan mengubah API atau service layer.

TASK 6 — error.tsx
Tambahkan error.tsx pada route groups yang memang membutuhkan error boundary.

Persyaratan:
- gunakan pola Next.js App Router yang benar;
- harus berupa Client Component jika diperlukan oleh error boundary;
- tampilkan pesan error yang aman untuk user;
- jangan membocorkan stack trace, credential, token, database detail, atau informasi sensitif;
- sediakan tombol retry menggunakan mekanisme Next.js yang sesuai;
- jangan mengubah authentication/authorization Phase 27.

PENTING:
- Jangan mengubah Prisma schema.
- Jangan mengubah API contract.
- Jangan mengubah JWT/session.
- Jangan mengubah Phase 26.
- Jangan mengubah Phase 27.
- Jangan mengerjakan Task 7+ sebelum Task 5 dan 6 selesai.
- Jangan memperbaiki warning lama yang tidak berkaitan.

SETELAH SELESAI:
1. Jalankan TypeScript check.
2. Jalankan lint.
3. Jalankan production build.
4. Verifikasi route utama tetap HTTP 200.
5. Verifikasi protected route tetap membutuhkan authentication.
6. Jika memungkinkan, uji error boundary secara aman.
7. Pastikan tidak ada regression.

LAPORKAN:
- route yang diaudit;
- file loading.tsx yang dibuat/diubah;
- file error.tsx yang dibuat/diubah;
- alasan setiap file dibuat;
- hasil TypeScript;
- hasil lint;
- hasil build;
- hasil runtime test;
- status Task 5: COMPLETE/PARTIAL;
- status Task 6: COMPLETE/PARTIAL;
- masalah yang masih tersisa.

Jangan lanjut ke Task 7+ sampai laporan ini selesai.

```

# Prompt — Phase 28 Task 3 + 4
```

Lanjutkan pengerjaan Phase 28 pada repository toko-online.

Task 2 sudah 100% selesai dan sudah diverifikasi:
- next.config.mjs sudah benar
- tidak ada remote image hostname yang diperlukan
- TypeScript PASS
- Lint PASS
- Build PASS 46/46 pages
- runtime image optimizer PASS

Sekarang kerjakan HANYA:

TASK 3 — Migrasi image ke next/image
TASK 4 — Optimasi blurDataURL + priority

Sebelum mengubah kode:
1. Audit seluruh penggunaan <img> di app/src.
2. Identifikasi 4 komponen yang masih menggunakan <img>:
   - BannerTable
   - DraggableBannerList
   - ProductImageUpload
   - BannerSlide
3. Pastikan sumber image tetap kompatibel dengan data/database yang sekarang.
4. Jangan mengubah behavior bisnis, API, database, authentication, atau Phase 26/27.

TASK 3:
- Ganti <img> yang relevan menjadi next/image.
- Gunakan width/height atau fill sesuai layout sebenarnya.
- Jangan menggunakan remotePatterns jika memang tidak diperlukan.
- Pertahankan object-fit, aspect ratio, className, loading behavior, dan tampilan existing.
- Untuk image yang berasal dari URL eksternal, jangan menebak hostname. Audit dulu sumber URL dan hanya ubah konfigurasi jika benar-benar diperlukan.
- Hindari perubahan yang tidak terkait.

TASK 4:
- Audit image utama pada homepage, product detail, banner, dan image yang terlihat di initial viewport.
- Tambahkan priority hanya pada image yang benar-benar membutuhkan prioritas loading.
- Gunakan blurDataURL/placeholder="blur" hanya jika sumber blurDataURL valid dan tersedia.
- Jangan membuat blurDataURL palsu.
- Jangan menambahkan priority ke semua image.
- Jangan menyebabkan warning Next.js mengenai priority, fill, width/height, atau image optimization.

Setelah selesai:
1. Jalankan TypeScript check.
2. Jalankan lint.
3. Jalankan production build.
4. Jika memungkinkan lakukan runtime smoke test pada homepage dan halaman yang terkena perubahan.
5. Pastikan tidak ada regression Phase 26 dan Phase 27.
6. Jangan mengerjakan Task 5+ sebelum Task 3 dan 4 selesai.

LAPORKAN:
- file yang diubah
- jumlah <img> yang berhasil dimigrasikan
- image mana yang diberi priority dan alasannya
- image mana yang menggunakan blurDataURL dan sumbernya
- apakah next.config.mjs perlu berubah
- hasil TypeScript
- hasil lint
- hasil build
- hasil runtime test
- status Task 3: COMPLETE/PARTIAL
- status Task 4: COMPLETE/PARTIAL
- masalah yang masih tersisa

Jika menemukan masalah yang membutuhkan perubahan di luar scope Task 3/4, jangan langsung mengerjakannya. Laporkan terlebih dahulu.

```
# 
```
PROMPT — PHASE 28 TASK 2: IMAGE CONFIGURATION

Project: toko-online

Implementasikan HANYA Phase 28 Task 2.

Tujuan:
Konfigurasi image handling Next.js agar seluruh penggunaan next/image untuk gambar lokal maupun remote dapat berjalan dengan benar.

Sebelum mengubah kode:
1. Audit next.config.mjs yang sekarang.
2. Cari seluruh penggunaan <Image> / next/image di project.
3. Identifikasi hostname/domain remote image yang benar-benar digunakan.
4. Jangan menebak hostname.
5. Jangan mengubah konfigurasi yang tidak berkaitan dengan image.

Implementasi:
- Tambahkan konfigurasi images yang diperlukan pada next.config.mjs.
- Gunakan konfigurasi modern yang kompatibel dengan versi Next.js project.
- Pastikan remotePatterns hanya berisi hostname/source yang memang digunakan project.
- Jangan menggunakan konfigurasi terlalu permisif seperti wildcard semua domain jika tidak diperlukan.
- Jangan mengubah UI atau business logic.
- Jangan mengubah API/authentication.
- Jangan mengubah database.
- Jangan mengerjakan Task 3 dan seterusnya.

Setelah selesai:
1. Jalankan TypeScript check.
2. Jalankan lint.
3. Jalankan production build.
4. Pastikan tidak ada regression.

LAPORKAN:
- File yang diubah.
- Konfigurasi image yang ditambahkan.
- Semua hostname remote image yang terdeteksi.
- Hasil TypeScript.
- Hasil lint.
- Hasil build.
- Apakah Phase 28 Task 2 100% selesai.

PENTING:
Jangan mengklaim selesai jika test/build gagal.
Jangan mengerjakan task Phase 28 lainnya.
Jangan commit atau push.


```
# 
```
PROMPT — AUDIT ROADMAP PHASE 28

Project: toko-online

Phase 26 dan Phase 27 sudah selesai dan terverifikasi.

Jangan mengubah kode.
Jangan membuat fitur.
Jangan commit.
Jangan push.

Sekarang audit repository dan dokumentasi proyek untuk menentukan:

1. Apa definisi resmi PHASE 28?
2. Apa saja task Phase 28?
3. File apa yang terkait?
4. Apakah ada dependency dari Phase 27?
5. Apakah ada task Phase 28 yang sebenarnya sudah dikerjakan?
6. Apakah ada pekerjaan Phase 26/27 yang belum selesai tetapi tercatat untuk Phase 28?
7. Apa urutan implementasi Phase 28 yang paling aman?

Periksa:
- README.md
- roadmap/project documentation
- TODO
- task list
- komentar Phase 28 di source code
- git history jika relevan

Jangan mengarang task Phase 28.

HASIL:
Buat tabel:

| Task Phase 28 | Status | File terkait | Keterangan |
|---|---|---|---|

Kemudian berikan:
- definisi resmi Phase 28
- daftar task lengkap
- task yang sudah selesai
- task yang belum selesai
- urutan pengerjaan yang direkomendasikan

Hanya AUDIT. Jangan melakukan perubahan kode.


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
