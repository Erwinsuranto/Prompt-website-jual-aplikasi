# Prompt-website-jual-aplikasi




# Prompt 6 — Feature Cards (4 Keunggulan)
```



Task: Implement Home Page - Features section only.

Reference:
Use the attached Digital Cell homepage screenshot.

Requirements:

Build only the Features section located below the Promo Banner.

Create a reusable FeaturesSection component.

Include exactly four feature cards:

- Aman & Terpercaya
- Proses Cepat
- Layanan 24/7
- Harga Terbaik

Each card must contain:
- Icon
- Title
- Short description

Requirements:
- Match spacing, radius, shadows and layout from the reference.
- Mobile-first responsive.
- Desktop responsive.
- Reusable FeatureCard component.
- Use only Tailwind design tokens.
- No hardcoded hex colours.
- No backend.
- No API.
- UI only.

Do not modify:
- Header
- Hero
- Search
- Categories
- Featured Products
- Promo Banner

After completion:
1. Verify there are no TypeScript/build errors if possible.
2. Update ROADMAP.md by marking only this Features section task completed.
3. Update CHANGELOG.md.
4. Commit changes.
5. Stop and wait for my approval.


```

# Prompt 5 — Promo Banner
```



Task: Implement Home Page - Promo Banner section only.

Reference:
Use the attached Digital Cell homepage screenshot.

Requirements:

- Build only the promotional banner below the Featured Products section.
- Match the reference design as closely as possible.
- Create a reusable PromoBanner component.
- Include:
  - Discount badge.
  - Main promotional title.
  - Subtitle.
  - Decorative illustration/background.
  - Circular arrow button.
- Mobile-first responsive.
- Framer Motion animation.
- No business logic.
- No API.
- No backend.
- Mock content only.

Do not modify:
- Header
- Hero
- Search Bar
- Categories
- Featured Products

After completion:
1. Verify there are no TypeScript/build errors if possible.
2. Update ROADMAP.md by marking only this Promo Banner task completed.
3. Update CHANGELOG.md.
4. Commit changes.
5. Stop and wait for my approval.


```

# Prompt 4 — Featured Products (Produk Populer)
```




Task: Implement Home Page - Featured Products section only.

Reference:
Use the attached Digital Cell homepage design image.

Requirements:

- Build only the "Produk Populer" section.
- Match the provided design as closely as possible.
- Create reusable ProductCard component.
- Each card must include:
  - Product image
  - Product title
  - Product subtitle
  - Rating
  - Sold count
  - Price
  - Badge (Promo / Ready / New / Terlaris)
  - Add-to-cart button
- Horizontal scrolling on mobile.
- Responsive grid on tablet and desktop.
- Use mock product data only.
- Framer Motion hover/tap animations.
- Do not implement product detail or cart logic.
- Do not modify Hero, Search Bar, or Categories.

After completion:
1. Verify there are no TypeScript or build errors if the environment supports it.
2. Update ROADMAP.md by marking only the Featured Products task as completed.
3. Update CHANGELOG.md.
4. Commit the changes.
5. Stop and wait for my approval.

```
# Prompt 3 – Categories
```


Task: Implement Home Page - Categories Section only.

Reference:
Use the attached Digital Cell homepage design as the UI reference.

Requirements:

- Build only the Categories section.
- Match the provided design as closely as possible.
- Horizontal category list.
- Rounded icon cards.
- Category icon.
- Category title.
- Active category state.
- Horizontal scrolling on mobile if needed.
- Responsive for tablet and desktop.
- Reusable CategoryCard component.
- Mock data only.
- Smooth hover/tap animation using Framer Motion.
- Use design tokens already defined in the project.
- Do not modify Hero Section or Search Bar.
- Do not implement Product Cards yet.

After completion:
1. Verify there are no TypeScript/build errors if the environment supports it.
2. Update ROADMAP.md by marking only the Categories task as completed.
3. Update CHANGELOG.md.
4. Commit the changes.
5. Stop and wait for my approval.



```
# Prompt 2 – Search Bar
```


Task: Implement Home Page - Search Bar only.

Reference:
Use the attached Digital Cell homepage design image.

Requirements:

- Build only the Search Bar section.
- Match the design as closely as possible.
- Rounded search input.
- Search icon on the left.
- Filter button on the right.
- Proper spacing below Hero Banner.
- Mobile-first and fully responsive.
- Reusable component.
- No search functionality yet (UI only).
- Do not modify Hero Section.
- Do not implement Categories or Product Cards yet.

After completion:
1. Verify there are no build or TypeScript errors if the environment supports it.
2. Update ROADMAP.md by marking only the Search Bar task as completed.
3. Update CHANGELOG.md.
4. Commit changes.
5. Stop and wait for my approval.



```
# Prompt 1 — Hero Section
```




Task: Implement Home Page - Hero Section only.

Reference:
Use the attached Digital Cell homepage design image as the visual reference.

Requirements:
- Match the design as closely as possible.
- Build only the Hero Section.
- Include:
  - Top Header (Logo, Dark Mode button, Menu button)
  - Hero Banner / Slider
  - Slider indicators
  - CTA button ("Belanja Sekarang")
- Mobile-first and fully responsive.
- Reusable components.
- Use Next.js 14, TypeScript, Tailwind CSS, shadcn/ui, and Framer Motion.
- Use placeholder/mock images and data where needed.
- Do not implement Search Bar, Categories, Product Cards, or other homepage sections yet.
- Do not implement backend logic.

After the Hero Section is fully completed:
1. Verify there are no TypeScript or build errors.
2. Update ROADMAP.md by marking only this task as completed.
3. Update CHANGELOG.md.
4. Commit the changes.
5. Stop and wait for my next instruction.

```
# 
```
Create a new file named SPEC.md in the project root.

This document is the permanent project specification and must always be read before implementing any feature.

Requirements:

# Project

Name: Digital Cell

Type:
Modern mobile-first digital product marketplace.

Main Goal:
Build a production-ready e-commerce application for selling digital products and services.

Tech Stack

- Next.js 14 App Router
- React
- TypeScript
- Tailwind CSS
- shadcn/ui
- Framer Motion
- Zustand
- React Hook Form
- Zod

Coding Rules

- TypeScript Strict Mode.
- No duplicated code.
- Reusable components.
- Modular architecture.
- Clean code.
- Strong typing.
- Never use "any" unless absolutely necessary.
- Keep components small and maintainable.
- Always separate UI, business logic and API.

Project Structure

Always follow the existing folder structure.

Do not create unnecessary folders.

Prefer reusable components.

Design Rules

- Mobile-first.
- Responsive for tablet and desktop.
- Follow the provided Digital Cell design.
- White background.
- Blue primary color.
- Rounded modern cards.
- Soft shadows.
- Smooth animations.
- Dark mode support.
- Accessibility friendly.

Performance

- Lazy loading where appropriate.
- Image optimization.
- Dynamic imports when useful.
- Keep bundle size small.

Component Rules

Create reusable components.

Never duplicate UI.

Prefer composition.

Naming

PascalCase for Components.

camelCase for functions.

kebab-case only for route folders.

Git Workflow

Every implementation must:

1. Update ROADMAP.md
2. Update CHANGELOG.md
3. Commit changes
4. Stop and wait for user approval

Never continue automatically.

Memory

Before every implementation:

- Read SPEC.md
- Read ROADMAP.md
- Read CHANGELOG.md

Always follow these files.

If there is any conflict:

SPEC.md has highest priority.

ROADMAP.md is second.

Existing code is third.

Documentation

Every new feature must include:

- Clear comments where necessary.
- Strong typing.
- Proper folder placement.
- No unused imports.
- No dead code.

Goal

Maintain a production-quality codebase that is scalable, maintainable and easy for future AI sessions to continue.





```
# 
```


Update the project workflow to make ROADMAP.md the single source of truth.

Rules:

1. Before every implementation:
   - Read ROADMAP.md.
   - Analyze the current repository.
   - Find the first unchecked task ([ ]).
   - Only implement that task unless I explicitly request another one.

2. During implementation:
   - Follow the existing architecture and coding style.
   - Reuse existing components whenever possible.
   - Do not break existing functionality.
   - Keep the project modular and scalable.

3. After implementation:
   - Mark completed tasks as [x].
   - Update the phase progress.
   - Update the overall progress percentage.
   - Update the Progress Summary table.
   - Add the completed work to a Changelog section with today's date.
   - If new subtasks are discovered, automatically insert them into the appropriate phase.

4. Never:
   - Mark incomplete work as completed.
   - Skip roadmap steps.
   - Delete completed history.
   - Reorder completed tasks.

5. Always preserve:
   - Existing code.
   - Existing architecture.
   - Existing UI.
   - Existing functionality.

6. If implementation requires more than one session:
   - Stop after completing the current roadmap task.
   - Update ROADMAP.md.
   - Wait for my next instruction.

ROADMAP.md must always represent the real implementation status of the repository.



```

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
