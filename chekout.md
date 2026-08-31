




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
Lanjutkan project toko-online dari kondisi repository saat ini.

FOKUS TASK:
Audit dan sempurnakan flow REGISTRASI + AUTHENTIKASI CUSTOMER. Jangan mengubah atau mengulang fitur payment yang sudah selesai.

ATURAN:
1. Baca repository dan implementasi auth yang sudah ada terlebih dahulu.
2. Jangan menebak struktur project.
3. Pertahankan fitur yang sudah PASS.
4. Jangan gunakan Apple Login.
5. Google OAuth yang sudah ada jangan dihapus; audit dan perbaiki hanya jika ditemukan masalah nyata.
6. Jangan membuat mock/fake authentication.
7. Jangan menyimpan password plaintext.
8. Jangan menambahkan dependency baru jika sebenarnya tidak diperlukan.

YANG HARUS DIAUDIT:
- Registrasi customer dengan email/password.
- Validasi email.
- Validasi password dan konfirmasi password.
- Email yang sudah terdaftar harus ditolak dengan pesan yang jelas.
- Login email/password.
- Logout.
- Session/cookie authentication tetap aman dan konsisten.
- Customer yang belum login tidak boleh mengakses halaman/aksi yang membutuhkan akun.
- Customer yang sudah login tidak perlu kembali ke halaman login secara tidak semestinya.
- Google OAuth tetap berfungsi.
- Callback Google harus membuat/menghubungkan akun customer dengan benar tanpa membuat akun duplikat.
- Error authentication harus ditampilkan dengan jelas di frontend.
- Redirect setelah login/register harus menuju halaman yang benar.
- Refresh browser tidak boleh membuat session valid tiba-tiba hilang.
- Pastikan route/API auth tidak menyebabkan redirect loop atau 500.
- Pastikan customer dan admin tetap terpisah; jangan sampai auth customer membuka akses admin.

UX:
- Form mobile-first dan responsive.
- Loading state pada submit.
- Tombol submit tidak bisa diklik berkali-kali saat request berjalan.
- Pesan error/sukses jelas.
- Jangan menampilkan detail error internal/server kepada customer.
- Tampilan harus konsisten dengan desain toko-online yang sekarang.

VERIFIKASI WAJIB:
- Jalankan typecheck.
- Jalankan build.
- Jalankan test yang relevan.
- Lakukan browser E2E untuk:
  1. Register customer baru.
  2. Register dengan email yang sudah ada.
  3. Login berhasil.
  4. Login password salah.
  5. Logout.
  6. Protected route tanpa login.
  7. Protected route setelah login.
  8. Google OAuth jika environment credential tersedia.
  9. Refresh setelah login.
  10. Pastikan tidak ada redirect loop, hydration error, uncaught exception, atau API 500.

DATA TEST:
Gunakan data test yang aman dan jangan merusak data production.

SETELAH SELESAI:
- Perbaiki semua bug nyata yang ditemukan.
- Jangan melakukan perubahan di luar scope auth kecuali benar-benar diperlukan untuk memperbaiki flow.
- Simpan hasil audit/hasil kerja ke folder repository yang memang digunakan untuk hasil kerja AI agar pemeriksaan berikutnya tidak perlu screenshot.
- Tulis ringkasan file yang diubah, hasil test, hasil E2E, blocker jika ada, dan pekerjaan lanjutan.
- Jika semua PASS dan working tree bersih setelah commit, push ke origin/main.
- Jangan membuat commit kosong.
```
# 
```
## LANJUTKAN DIGITAL CELL — PAYMENT FLOW END-TO-END

Project:
Digital Cell / toko-online

STATUS YANG SUDAH SELESAI:
- Login customer: sudah ada
- Login admin + role authorization: sudah ada
- Admin Panel: sudah ada
- Product CRUD: sudah ada
- Category CRUD: sudah ada
- Category selector sudah menampilkan nama kategori, bukan ID database
- Responsive Admin Panel: sudah diaudit
- Cart: sudah ada
- Checkout navigation: SUDAH DIPERBAIKI
- Checkout dapat dibuka dari cart
- Direct `/checkout`: sudah PASS
- Refresh checkout: sudah PASS
- Quantity dan total: sudah PASS
- Product inactive handling: sudah PASS
- Typecheck/build/test sebelumnya PASS

Jangan mengulang pekerjaan tersebut kecuali ditemukan regresi.

APPLE LOGIN TIDAK DIPERLUKAN.
Jangan implementasikan Apple OAuth.

GOOGLE AUTH:
- Jangan menghapus Google Auth yang sudah ada.
- Jangan membuat authentication baru jika existing implementation masih dapat digunakan.
- Untuk tugas ini fokus utama adalah PAYMENT FLOW.

==================================================
TUJUAN UTAMA
==================================================

Sekarang sempurnakan sistem pembayaran Digital Cell agar alurnya:

Customer
→ Cart
→ Checkout
→ Pilih metode pembayaran
→ Konfirmasi order
→ Order dibuat
→ Payment dibuat
→ Customer mendapatkan instruksi pembayaran
→ Customer dapat melihat status pembayaran
→ Admin melihat payment/order
→ Admin melakukan verifikasi jika metode membutuhkan verifikasi manual
→ Payment status berubah
→ Order status mengikuti business rule
→ Customer mendapatkan status terbaru

Gunakan database nyata yang digunakan aplikasi.

Jangan memakai mock payment sebagai pengganti database.

==================================================
1. AUDIT PAYMENT EXISTING
==================================================

Sebelum coding, audit seluruh implementasi payment yang sudah ada.

Cari:
- payment-service
- payment API
- payment model/schema
- payment methods
- payment gateway
- order-service
- checkout service
- payment settings
- admin payment page
- customer payment page
- upload payment proof
- payment verification
- webhook jika ada

Jangan membuat duplicate implementation jika fitur existing sudah tersedia.

Buat peta singkat:

CHECKOUT
↓
ORDER
↓
PAYMENT
↓
PAYMENT STATUS
↓
ADMIN VERIFICATION / GATEWAY
↓
ORDER STATUS

Identifikasi bagian yang belum terhubung.

==================================================
2. AUDIT DATABASE PAYMENT
==================================================

Periksa schema database yang sudah ada.

Pastikan relation:

Order
→ OrderItem

Order
→ Payment

Customer
→ Order

Payment
→ Order

sesuai schema existing.

Periksa field seperti:
- id
- orderId
- amount
- method
- status
- reference
- proof/bukti
- createdAt
- updatedAt
- expiresAt jika ada

Jangan mengubah schema secara destruktif.

Jangan:
- DROP DATABASE
- reset database
- delete seluruh payment
- delete order production

Jika schema memang kurang field yang benar-benar dibutuhkan, tambahkan migration yang aman dan backward-compatible.

==================================================
3. PAYMENT METHODS
==================================================

Audit metode pembayaran yang saat ini tersedia.

Kelompokkan menjadi:

A. MANUAL PAYMENT
Contoh:
- transfer bank
- QRIS manual
- e-wallet manual

B. AUTOMATIC PAYMENT GATEWAY
Jika memang sudah ada provider/gateway di project.

Jangan mengarang provider yang belum dikonfigurasi.

Admin harus dapat mengaktifkan/nonaktifkan metode pembayaran yang memang tersedia.

Customer hanya melihat metode yang:
- active
- valid
- memiliki konfigurasi lengkap

==================================================
4. CHECKOUT → CREATE ORDER
==================================================

Audit tombol:

"Bayar"
atau
"Pesan"
atau
"Konfirmasi Pesanan"

Pastikan satu kali submit menghasilkan:

1 Order
1 atau sesuai jumlah item OrderItem
1 Payment

Jangan membuat duplicate ketika:
- user double-click;
- refresh;
- retry;
- back lalu submit lagi.

Gunakan transaction/database mechanism yang sesuai.

Harga final harus dihitung server-side.

Jangan mempercayai:
- price dari browser;
- subtotal dari browser;
- total dari browser.

Backend harus mengambil product dari database dan menghitung ulang:

quantity
×
harga database

kemudian total order.

==================================================
5. STOCK
==================================================

Jika product memiliki stok:

Saat order dibuat:
- validasi stok;
- jangan izinkan quantity melebihi stok;
- jangan biarkan stok menjadi negatif.

Tentukan berdasarkan business rule existing apakah stok dikurangi:
- saat order dibuat;
- saat payment berhasil;
- atau saat order diproses.

Jangan mengubah aturan existing tanpa audit.

Yang paling penting:
stock tidak boleh berkurang dua kali karena retry/double submit.

==================================================
6. MANUAL PAYMENT FLOW
==================================================

Jika project menggunakan pembayaran manual, buat flow lengkap:

CHECKOUT
→ pilih metode
→ buat order
→ payment PENDING
→ tampilkan instruksi pembayaran

Customer harus dapat melihat:

- nomor order;
- total;
- metode pembayaran;
- rekening/QRIS/e-wallet sesuai metode;
- nominal yang harus dibayar;
- batas waktu jika ada;
- status pembayaran.

Jika upload bukti pembayaran tersedia/diinginkan:

Customer:
→ upload bukti
→ payment status menjadi `WAITING_VERIFICATION`
→ admin melihat bukti
→ approve/reject

Pastikan file upload:
- divalidasi tipe;
- divalidasi ukuran;
- tidak dapat digunakan untuk upload file berbahaya;
- tidak menyimpan credential;
- memiliki nama/file ID yang aman.

Jangan menyimpan file sensitif di public directory tanpa proteksi.

==================================================
7. PAYMENT STATUS
==================================================

Gunakan enum/status yang SUDAH ada di project.

Jangan membuat banyak status baru jika tidak diperlukan.

Minimal secara konsep harus dapat membedakan:

PENDING
WAITING_VERIFICATION
PAID
FAILED
EXPIRED
CANCELLED

Jika nama enum existing berbeda, gunakan enum existing.

Pastikan transition valid.

Contoh:

PENDING
→ WAITING_VERIFICATION
→ PAID

atau:

PENDING
→ EXPIRED

atau:

PENDING
→ CANCELLED

Jangan izinkan:

PAID
→ PENDING

kecuali memang ada mekanisme refund/reversal yang benar.

==================================================
8. ORDER STATUS VS PAYMENT STATUS
==================================================

Ini WAJIB dipisahkan.

Contoh:

Payment:
PAID

Order:
PROCESSING

atau:

Payment:
PENDING

Order:
PENDING_PAYMENT

Gunakan business rule existing.

Jangan membuat payment status dan order status menjadi satu field.

Audit semua tempat yang mengubah kedua status tersebut.

==================================================
9. ADMIN PAYMENT VERIFICATION
==================================================

Jika manual payment digunakan, Admin Panel harus memiliki area untuk:

- daftar payment pending;
- nomor order;
- customer;
- nominal;
- metode;
- waktu pembayaran;
- bukti pembayaran;
- status.

Admin dapat:

APPROVE
→ payment PAID

REJECT
→ payment FAILED/REJECTED sesuai enum existing

Pastikan customer tidak dapat memanggil endpoint approve/reject.

Authorization wajib server-side.

==================================================
10. ADMIN ORDER MANAGEMENT
==================================================

Setelah payment berhasil:

Admin harus melihat order:

- Order ID
- customer
- product
- quantity
- total
- payment method
- payment status
- order status
- created date

Admin dapat mengubah order status hanya sesuai transition yang valid.

Jangan izinkan status invalid.

==================================================
11. CUSTOMER ORDER PAGE
==================================================

Customer harus dapat membuka:

/orders

dan melihat:

- order;
- produk;
- jumlah;
- total;
- status order;
- status payment;
- metode pembayaran;
- waktu order.

Customer tidak boleh melihat:
- password;
- payment secret;
- admin notes yang bersifat internal;
- database ID yang tidak perlu;
- gateway credential.

Jika order membutuhkan pembayaran:

Customer harus mendapatkan tombol/entry:

"Bayar Sekarang"

Jika sudah PAID:

jangan tampilkan tombol bayar lagi.

==================================================
12. PAYMENT EXPIRATION
==================================================

Jika payment memiliki expiry:

Pastikan payment yang sudah expired tidak dapat dibayar kembali tanpa membuat flow baru sesuai business rule.

Jangan menggunakan timer frontend sebagai source of truth.

Server harus menentukan expiry berdasarkan waktu database/server.

==================================================
13. IDEMPOTENCY / DOUBLE PAYMENT
==================================================

Ini sangat penting.

Test:

- double click tombol bayar;
- refresh setelah create order;
- request dikirim dua kali;
- browser back;
- buka checkout pada dua tab;
- retry request.

Pastikan tidak terjadi:

- duplicate order;
- duplicate order item;
- duplicate payment;
- double stock deduction.

Gunakan unique constraint/idempotency mechanism yang sesuai jika memang diperlukan.

==================================================
14. PAYMENT GATEWAY
==================================================

Jika project sudah memiliki integrasi payment gateway:

Audit:
- API credential;
- endpoint;
- callback;
- webhook;
- signature verification;
- status synchronization.

Jangan menaruh secret di frontend.

Jangan log:
- API key;
- secret;
- signature secret;
- token.

Webhook harus diverifikasi.

Jangan percaya status pembayaran hanya berdasarkan request dari browser.

Jika gateway belum dikonfigurasi:
JANGAN membuat credential palsu.

Buat integration layer yang siap dikonfigurasi, tetapi gunakan metode manual/dev yang memang tersedia untuk testing.

==================================================
15. GOOGLE AUTH REGRESSION
==================================================

Jangan mengerjakan Apple.

Pastikan Google Auth existing tidak rusak akibat perubahan payment.

Jika Google login sudah dikonfigurasi:

Google login
→ session
→ customer account
→ cart
→ checkout
→ order

Jika Google Auth belum memiliki credential production:
- jangan membuat credential palsu;
- jangan memblokir login password/WhatsApp;
- laporkan bahwa production credential masih diperlukan.

==================================================
16. SECURITY
==================================================

Audit payment API.

Customer tidak boleh:

- mengubah amount menjadi Rp 1;
- mengubah order owner;
- mengubah payment status menjadi PAID;
- approve payment;
- mengakses payment customer lain;
- mengubah order customer lain.

Server harus mengambil:

customerId
dari session/authenticated user.

Bukan dari request body yang dapat dimanipulasi.

==================================================
17. DATA CONSISTENCY
==================================================

Bandingkan:

Checkout UI
vs
Order API
vs
Payment API
vs
Database

Pastikan:

harga sama;
quantity sama;
subtotal benar;
total benar;
order relation benar;
payment relation benar.

Jika terdapat perbedaan, perbaiki source of truth.

==================================================
18. TEST DENGAN DATA TEST
==================================================

Gunakan produk dan akun test yang sudah ada.

Jangan menghapus data production.

Buat transaksi test yang jelas.

Test minimal:

TEST 1:
Customer
→ product
→ cart
→ checkout
→ manual payment
→ order dibuat
→ payment pending

TEST 2:
Customer
→ upload/submit bukti jika fitur tersedia
→ waiting verification

TEST 3:
Admin
→ payment pending
→ lihat detail
→ approve
→ payment PAID

TEST 4:
Customer
→ refresh orders
→ status PAID terlihat

TEST 5:
Customer mencoba approve payment
→ ditolak

TEST 6:
Customer mencoba melihat order customer lain
→ ditolak

TEST 7:
double submit
→ tidak membuat duplicate

TEST 8:
invalid amount
→ ditolak server

TEST 9:
invalid orderId
→ ditolak

TEST 10:
expired payment jika fitur tersedia
→ tidak dapat dibayar lagi secara ilegal

==================================================
19. RESPONSIVE PAYMENT UI
==================================================

Test:

360px
375px
390px
414px
768px
1280px
1440px

Pastikan:

- payment method tidak overflow;
- QR code/rekening tidak keluar container;
- order summary tidak keluar layar;
- tombol bayar tidak tertutup;
- modal tetap dapat digunakan;
- upload proof responsive;
- admin payment table dapat digunakan di mobile;
- tidak ada horizontal overflow.

Jangan merusak UI customer yang sudah PASS.

==================================================
20. ERROR HANDLING
==================================================

Gunakan pesan yang jelas:

- payment method tidak tersedia;
- payment sudah dibayar;
- order sudah dibatalkan;
- payment expired;
- nominal tidak valid;
- stok tidak cukup;
- bukti pembayaran invalid;
- unauthorized;
- forbidden.

Jangan menampilkan stack trace.

==================================================
21. TESTING
==================================================

Jalankan:

npm run typecheck
npm run build
npm run test

Jika project memiliki test tambahan untuk payment:

jalankan juga.

Kemudian lakukan browser E2E pada server yang benar-benar sedang digunakan.

Pastikan:

- 0 uncaught exception;
- 0 hydration error;
- 0 API 500;
- tidak ada redirect loop;
- tidak ada duplicate order;
- tidak ada duplicate payment.

==================================================
22. DATABASE VERIFICATION
==================================================

Setelah test transaction selesai, query database dan pastikan:

Order count sesuai;
OrderItem count sesuai;
Payment count sesuai;
relasi benar;
total benar;
status benar;
stock benar.

Jangan hanya percaya UI.

==================================================
23. GIT SAFETY
==================================================

Sebelum commit:

git status --short
git diff --check

Jangan commit:

.env
.env.local
API key
payment secret
OAuth secret
password
token
cookie
build artifact
temporary files

Jika tidak ada perubahan kode yang diperlukan:
jangan membuat commit kosong.

Jika ada perubahan valid:

git add <file yang benar-benar berubah>

git commit -m "feat(payment): complete payment flow"

git push origin main

Jangan force push.

Setelah push:

git status --short
git log -1 --oneline
git status -sb

Pastikan working tree bersih dan branch sinkron dengan origin/main.

==================================================
HASIL AKHIR WAJIB
==================================================

Laporkan:

PAYMENT
- Existing payment audit: PASS/FAIL
- Payment method: PASS/FAIL
- Checkout → Order: PASS/FAIL
- Order → Payment: PASS/FAIL
- Manual payment: PASS/FAIL
- Payment verification: PASS/FAIL
- Customer order status: PASS/FAIL
- Admin payment management: PASS/FAIL
- Order/payment status separation: PASS/FAIL
- Duplicate payment protection: PASS/FAIL
- Stock integrity: PASS/FAIL
- Server-side validation: PASS/FAIL
- Authorization: PASS/FAIL
- Responsive payment UI: PASS/FAIL

DATABASE
- Order integrity: PASS/FAIL
- OrderItem integrity: PASS/FAIL
- Payment integrity: PASS/FAIL
- Relation integrity: PASS/FAIL

AUTH
- WhatsApp login regression: PASS/FAIL
- Google Auth regression: PASS/FAIL
- Apple Auth: NOT REQUIRED

TEST
- Typecheck: PASS/FAIL
- Build: PASS/FAIL
- Automated tests: X PASS / X FAIL
- Browser E2E: PASS/FAIL
- Console errors: PASS/FAIL

GIT
- Commit: ...
- Push: PASS/FAIL
- Working tree: CLEAN/NOT CLEAN

BLOCKER:
Tuliskan hanya blocker nyata yang masih membutuhkan konfigurasi/manual action.

PENTING:
Jangan menganggap payment selesai hanya karena tombol "Bayar" bekerja.
Harus dibuktikan sampai Order + Payment + Database + Admin Verification + Customer Status.
```
