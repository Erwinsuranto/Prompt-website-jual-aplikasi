# Prompt-website-jual-aplikasi



#
```
# Prompt 01 — Project Structure (Digital Cell)

## Tujuan

Buat struktur project terlebih dahulu sebelum membuat desain UI.

Jangan membuat halaman atau komponen visual dulu.

Fokus hanya membuat struktur project yang rapi, scalable, dan mudah dikembangkan.

Project ini adalah website penjualan produk digital bernama **Digital Cell**.

Website harus mobile-first tetapi tetap responsive untuk tablet dan desktop.

---

# Teknologi

- Next.js App Router
- React
- TypeScript
- Tailwind CSS
- shadcn/ui
- Framer Motion
- Zustand
- React Hook Form
- Zod

---

# Struktur Folder

```
src/
│
├── app/
│   ├── (shop)/
│   │   ├── page.tsx
│   │   ├── category/
│   │   ├── product/
│   │   ├── checkout/
│   │   ├── invoice/
│   │   ├── search/
│   │   └── profile/
│   │
│   ├── admin/
│   │   ├── dashboard/
│   │   ├── products/
│   │   ├── categories/
│   │   ├── orders/
│   │   ├── payments/
│   │   ├── users/
│   │   ├── banners/
│   │   ├── reports/
│   │   └── settings/
│   │
│   ├── auth/
│   ├── api/
│   └── layout.tsx
│
├── components/
│   ├── layout/
│   ├── navbar/
│   ├── sidebar/
│   ├── hero/
│   ├── search/
│   ├── category/
│   ├── product/
│   ├── checkout/
│   ├── payment/
│   ├── invoice/
│   ├── admin/
│   ├── ui/
│   └── common/
│
├── hooks/
├── lib/
├── services/
├── store/
├── types/
├── utils/
├── styles/
├── assets/
└── config/
```

---

# Routing

/

/products

/product/[slug]

/category/[slug]

/search

/cart

/checkout

/invoice

/profile

/login

/register

/admin

/admin/products

/admin/orders

/admin/categories

/admin/payments

/admin/users

/admin/settings

---

# Design System

Buat folder khusus untuk:

- Colors
- Typography
- Icons
- Buttons
- Inputs
- Cards
- Badges
- Dialog
- Modal
- Toast
- Skeleton Loading
- Animation
- Theme

---

# Ketentuan

Jangan membuat desain UI terlebih dahulu.

Jangan membuat gambar.

Jangan membuat banner.

Jangan membuat icon.

Jangan membuat ilustrasi.

Fokus hanya membuat struktur project yang profesional, bersih, scalable, dan siap digunakan untuk pengembangan UI pada tahap berikutnya.

Pastikan struktur mudah dikembangkan hingga ratusan produk dan memiliki pemisahan yang jelas antara Shop, Admin, API, Components, dan Utilities.


```
