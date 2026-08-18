
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
