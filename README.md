# MCDC Academy Player Scanner

نسخة Read-Only تقرأ بيانات اللاعبين مباشرة من جدول Supabase الحالي:

```text
public.academy_people
```

لأعلى سرعة أثناء الاسكان، شغّل SQL رقم `007` مرة واحدة. لا يتم إنشاء جدول `players` ولا تعديل بيانات اللاعبين.

## مصدر بيانات اللاعب

Cloudflare Pages Function الموجودة في:

```text
functions/api/lookup-player.ts
```

تبحث فقط في جدول `academy_people` عن طريق الحقل:

```text
unique_number
```

والحقول المعروضة في الكارت مربوطة كالتالي:

| من جدول academy_people | في الكارت |
|---|---|
| academy_name / church_name | اسم الأكاديمية |
| full_name_ar | الاسم العربي |
| full_name_en | الاسم الإنجليزي |
| title_raw / description_raw / member_type | الصفة |
| player_number / shirt_number_raw | رقم اللاعب |
| unique_number | الرقم المميز |
| image_url | صورة اللاعب |

## Environment Variables المطلوبة في Cloudflare Pages

```env
SUPABASE_URL=https://YOUR_PROJECT.supabase.co
SUPABASE_SERVICE_ROLE_KEY=YOUR_SERVICE_ROLE_KEY
ALLOWED_ORIGIN=*
```

> لا يوجد متغير باسم `ACADEMY_PEOPLE_TABLE` في هذه النسخة. اسم الجدول مثبت داخل الكود حتى لا يرجع المشروع بالخطأ إلى `players`.

## سبب خطأ public.ACADEMY_PEOPLE

لو ظهر خطأ مثل:

```text
Could not find the table 'public.ACADEMY_PEOPLE' in the schema cache - Perhaps you meant the table 'public.academy_people'
```

فهذا معناه أن Supabase شايف اسم الجدول بالأحرف الصغيرة. هذه النسخة تستخدم `public.academy_people` مباشرة.

## سبب خطأ public.players

لو ظهر خطأ مثل:

```text
Could not find the table 'public.players' in the schema cache
```

فهذا معناه غالبًا أن النسخة المنشورة ما زالت تستخدم Function قديمة أو Deploy قديم. هذه النسخة لا تحتوي على Netlify Functions ولا تبحث في `players` نهائيًا.

## التشغيل محليًا

```bash
npm install
npm run dev
```

للتجربة الأقرب لـ Cloudflare، شغّل build ثم Pages dev:

```bash
npm run build
npx wrangler pages dev dist
```

## Build

```bash
npm run build
```

## Cloudflare deployment

راجع الملف:

```text
CLOUDFLARE_DEPLOYMENT.md
```

## Performance SQL

لتسريع البحث أثناء الاسكان، شغّل مرة واحدة:

```text
supabase/sql/007_speed_indexes_academy_people.sql
```

هذا SQL ينشئ Index + Read-only RPC فقط، ولا يغيّر بيانات اللاعبين.
