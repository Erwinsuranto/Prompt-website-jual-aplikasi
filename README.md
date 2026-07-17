# Prompt-website-jual-aplikasi






# 
```

Create a new file named ROADMAP.md in the root of the repository.

This roadmap will be the single source of truth for the project.

Requirements:

- Analyze the entire repository structure before creating the roadmap.
- Analyze the current implementation status.
- Compare the implementation with the target Digital Cell e-commerce application.
- Divide the roadmap into logical phases.
- Break every phase into small implementation tasks.
- Every task must use Markdown checkboxes:
  - [ ] Not started
  - [x] Completed
- Number every task.
- Group tasks by feature.
- Mark already completed tasks automatically.
- Leave unfinished tasks unchecked.
- Do not mark a task completed unless it is fully implemented.

The roadmap should include at least:

1. Foundation
2. Layout
3. Home Page
4. Categories
5. Product List
6. Product Detail
7. Search
8. Shopping Cart
9. Checkout
10. Payment
11. Invoice
12. User Authentication
13. User Profile
14. Favorites
15. Order History
16. Notifications
17. Admin Dashboard
18. Product Management
19. Category Management
20. Banner Management
21. Order Management
22. Payment Management
23. Reports
24. Settings
25. API
26. Database
27. Authentication
28. Performance
29. SEO
30. Testing
31. Deployment

After every future implementation:

- Automatically update ROADMAP.md.
- Mark completed tasks as [x].
- Never mark incomplete work as completed.
- Add new tasks if new requirements are discovered.
- Keep numbering consistent.
- Never remove completed history.
- Preserve all previous progress.

ROADMAP.md must always represent the real implementation status of the repository.




```

# Prompt pertama yang bisa Anda berikan ke Genie Code
```



Implement the mobile-first layout based on the provided UI design.

Requirements:
- Use Next.js 14 App Router.
- Use shadcn/ui and Tailwind CSS.
- Create the global layout only.
- Build a responsive header with logo, dark mode toggle, and menu button.
- Build a responsive bottom navigation with Home, Categories, Orders, Favorites, and Account.
- Create reusable layout components.
- Do not implement business logic yet.
- Do not create fake data.
- Preserve the existing project structure.


```

#
```
# Prompt 01 — Project Structure (Digital Cell)

## Tujuan

Buat struktur project terlebih dahulu sebelum membuat desain UI.

Jangan membuat halaman atau komponen visual dulu.

Fokus hanya membuat struktur project yang rapi, scalable, dan mudah dikembangkan.

Project ini adalah website penjualan produk digital bernama **Digital Cell**.

Website harus mobile-first tetapi tetap responsive untuk tablet dan desktop.


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



# Struktur Folder


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



# Ketentuan

Jangan membuat desain UI terlebih dahulu.

Jangan membuat gambar.

Jangan membuat banner.

Jangan membuat icon.

Jangan membuat ilustrasi.

Fokus hanya membuat struktur project yang profesional, bersih, scalable, dan siap digunakan untuk pengembangan UI pada tahap berikutnya.

Pastikan struktur mudah dikembangkan hingga ratusan produk dan memiliki pemisahan yang jelas antara Shop, Admin, API, Components, dan Utilities.


```
