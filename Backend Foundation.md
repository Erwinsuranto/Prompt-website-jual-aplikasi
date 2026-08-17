








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
