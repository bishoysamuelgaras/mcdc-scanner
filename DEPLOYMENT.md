# Deployment — Cloudflare Pages v1.2.6

## Environment Variables

في Cloudflare Pages أضف القيم التالية من:

```text
Workers & Pages → Project → Settings → Variables and Secrets
```

```env
SUPABASE_URL=...
SUPABASE_SERVICE_ROLE_KEY=...
ALLOWED_ORIGIN=*
```

بعد ما الموقع يثبت، ممكن تخلي `ALLOWED_ORIGIN` يساوي رابط الدومين النهائي بدل `*`.

## Database performance SQL

لتسريع الاسكان لأقصى درجة، شغّل الملف ده مرة واحدة في Supabase SQL Editor:

```text
supabase/sql/007_speed_indexes_academy_people.sql
```

الملف لا يغيّر بيانات اللاعبين. هو يعمل Index + Read-only RPC فقط.

## Deploy steps

> مهم: المشروع يحتوي على Cloudflare Pages Functions. لا ترفع `dist` فقط من Drag & Drop، لأن الداتا والصور هتتعطل. استخدم GitHub integration أو Wrangler CLI.

1. ارفع المشروع على GitHub.
2. في Cloudflare اختار Workers & Pages → Create application → Pages.
3. Connect to Git واختار الريبو.
4. استخدم:

```text
Framework preset: Vite
Build command: npm run build
Build output directory: dist
```

5. أضف Environment Variables.
6. اعمل Deploy جديد.
7. افتح الموقع وجرب Scan.

## Build commands

```bash
npm install
npm run build
```

## Notes

- مصدر البيانات هو `public.academy_people` فقط.
- لا يوجد استخدام لجدول `players`.
- الصور تظهر عن طريق Google Drive proxy الداخلي على Cloudflare:

```text
/api/drive-image
```
