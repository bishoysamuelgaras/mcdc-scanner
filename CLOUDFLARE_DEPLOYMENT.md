# Cloudflare Pages Deployment — v1.2.6

هذه النسخة تم تحويلها من Netlify إلى Cloudflare Pages.

## المهم

المشروع ليس Static فقط. كان يحتوي على Netlify Functions، وتم تحويلها إلى Cloudflare Pages Functions:

```text
functions/api/lookup-player.ts
functions/api/drive-image.ts
```

المسارات الجديدة:

```text
/api/lookup-player
/api/drive-image
```

تم تحديث الواجهة لتستخدم `/api/lookup-player`، وتم تحديث بروكسي صور Google Drive ليستخدم `/api/drive-image`.

## Cloudflare Pages settings

عند إنشاء مشروع Cloudflare Pages من GitHub:

```text
Framework preset: Vite
Build command: npm run build
Build output directory: dist
Root directory: /
```

Cloudflare سيقرأ مجلد `functions` من جذر المشروع ويشغّل Pages Functions تلقائيًا.

## Environment Variables / Secrets

في Cloudflare:

```text
Workers & Pages → اختر المشروع → Settings → Variables and Secrets
```

أضف الآتي في Production وأيضًا Preview لو هتجرب Preview deployments:

```env
SUPABASE_URL=https://YOUR_PROJECT.supabase.co
SUPABASE_SERVICE_ROLE_KEY=YOUR_SERVICE_ROLE_KEY
ALLOWED_ORIGIN=*
```

بعد ما الدومين النهائي يشتغل، الأفضل تغيير:

```env
ALLOWED_ORIGIN=https://your-domain.com
```

بدل `*`.

> مهم جدًا: `SUPABASE_SERVICE_ROLE_KEY` لازم يكون Secret/Runtime variable على Cloudflare، ولا يتم وضعه داخل كود الواجهة أو داخل `.env` مرفوع على GitHub.

## Supabase SQL

لتسريع البحث أثناء الاسكان، شغّل مرة واحدة:

```text
supabase/sql/007_speed_indexes_academy_people.sql
```

هذا SQL يعمل Index + Read-only RPC فقط، ولا يغيّر بيانات اللاعبين.

## Deploy using Dashboard / GitHub

> مهم: لا تستخدم Drag & Drop للـ `dist` من Dashboard في هذا المشروع، لأن Cloudflare Drag & Drop لا يرفع مجلد `functions`. استخدم GitHub integration أو Wrangler CLI.

1. ارفع هذا المشروع على GitHub.
2. افتح Cloudflare Dashboard.
3. Workers & Pages → Create application → Pages.
4. Connect to Git.
5. اختار الريبو.
6. استخدم الإعدادات:

```text
Build command: npm run build
Build output directory: dist
```

7. أضف Environment Variables من Settings → Variables and Secrets.
8. اعمل Deploy جديد بعد إضافة المتغيرات.
9. جرّب:

```text
https://YOUR_DOMAIN/api/lookup-player
```

لو فتحتها GET طبيعي، المفروض ترد `Method not allowed.` وهذا معناه أن الـ Function موجودة.

## Deploy using Wrangler CLI

شغّل الأمر من جذر المشروع، وليس من داخل `dist`، حتى يقرأ Wrangler مجلد `functions` ويرفعه مع الموقع.

```bash
npm install
npm run build
npx wrangler pages deploy dist --project-name mcdc-player-scanner
```

أو استخدم الاختصار الموجود في `package.json`:

```bash
npm run cloudflare:deploy
```

بعدها أضف Secrets من Cloudflare Dashboard أو بالأوامر:

```bash
npx wrangler pages secret put SUPABASE_URL --project-name mcdc-player-scanner
npx wrangler pages secret put SUPABASE_SERVICE_ROLE_KEY --project-name mcdc-player-scanner
npx wrangler pages secret put ALLOWED_ORIGIN --project-name mcdc-player-scanner
```

## Files changed from Netlify

- Removed `netlify/functions`.
- Removed `netlify.toml`.
- Added `functions/api/lookup-player.ts`.
- Added `functions/api/drive-image.ts`.
- Added `public/_redirects` for SPA fallback.
- Added `wrangler.toml`.
- Updated `src/lib/api.ts` from `/.netlify/functions/lookup-player` to `/api/lookup-player`.
- Updated documentation from Netlify to Cloudflare.
