# Project Context

التطبيق عبارة عن React + Cloudflare Pages Functions + Supabase لقراءة QR الخاص باللاعب وعرض بياناته.

المصدر الرسمي الحالي للبيانات:

```text
public.academy_people
```

لا يتم استخدام جدول `players` في البحث.

الملف المسؤول عن البحث:

```text
functions/api/lookup-player.ts
```

الجدول مثبت داخل الكود:

```ts
const ACADEMY_PEOPLE_TABLE = 'academy_people';
```

السبب: منع أي Environment Variable قديم أو Function cache من إرجاع المشروع للبحث في `players`.

## Image loading

صور اللاعبين يتم قراءتها من `image_url` في جدول `academy_people`.
لو الرابط غير موجود في العمود، يحاول السيرفر استخراج `image_url` من `raw_payload`.

روابط Google Drive لا تُعرض مباشرة داخل `<img>` لأن Drive قد يرجع صفحة HTML بدل الصورة. لذلك المشروع يستخدم:

```text
/api/drive-image?id=GOOGLE_DRIVE_FILE_ID
```

هذه Function تقرأ الصورة من Google Drive وتحولها إلى Image Response مناسبة للعرض داخل الكارت.
لا يوجد أي تعديل مطلوب في قاعدة البيانات.

## v1.2.6 Notes

This version continues to read from `public.academy_people` but migrates serverless functions from Netlify Functions to Cloudflare Pages Functions. It also keeps the optional Supabase speed migration: `supabase/sql/007_speed_indexes_academy_people.sql`. The SQL creates a partial lookup index and a read-only RPC `academy_lookup_person_fast`. The Cloudflare function tries the RPC first and falls back to direct table lookup if the SQL has not been run. No player data is inserted, updated, or deleted.
