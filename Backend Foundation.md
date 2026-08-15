









# 
```



``

# 
```



``


# 
```



``

# 
```



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
