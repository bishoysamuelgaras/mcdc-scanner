# Database Notes — v1.2.6

مصدر بيانات اللاعبين ما زال نفس الجدول الموجود عندك:

```text
public.academy_people
```

النسخة دي لا تغيّر بيانات اللاعبين ولا تضيف/تعدل/تحذف أي صفوف. التعديل الاختياري في قاعدة البيانات هدفه السرعة فقط.

## SQL المطلوب لتسريع الاسكان

شغّل الملف التالي مرة واحدة من Supabase SQL Editor:

```text
supabase/sql/007_speed_indexes_academy_people.sql
```

الملف يعمل الآتي:

1. إنشاء Index سريع على `unique_number` للسجلات النشطة فقط.
2. إنشاء Read-only RPC باسم:

```text
public.academy_lookup_person_fast(text)
```

3. تشغيل `ANALYZE` لتحديث إحصائيات PostgreSQL.
4. عمل reload لـ PostgREST schema cache حتى الـ RPC يظهر مباشرة.

## مصدر القراءة داخل المشروع

القراءة تتم من:

```text
functions/api/lookup-player.ts
```

المسار السريع بعد تشغيل SQL:

```text
Cloudflare Pages Function → Supabase RPC academy_lookup_person_fast → public.academy_people
```

لو الـ SQL لم يتم تشغيله بعد، المشروع يعمل Fallback تلقائيًا إلى القراءة المباشرة من الجدول:

```text
Cloudflare Pages Function → REST exact lookup → public.academy_people
```

## الحقول المستخدمة

| Field | Usage |
|---|---|
| academy_name / church_name | اسم الأكاديمية |
| full_name_ar | الاسم العربي |
| full_name_en | الاسم الإنجليزي |
| title_raw / description_raw / member_type | الصفة |
| player_number / shirt_number_raw | رقم اللاعب |
| unique_number | الرقم المميز المستخدم في البحث |
| image_url | رابط صورة اللاعب |
| raw_payload | fallback لاستخراج `UN` أو `image_url` عند الحاجة |
| is_active | عرض السجلات النشطة فقط |
| updated_at / created_at | اختيار آخر سجل لو فيه تكرار لنفس الرقم |

## Read-only behavior

- لا يتم تعديل أي بيانات لاعب.
- لا يتم إنشاء جدول `players`.
- لا يتم الكتابة في `scan_logs`.
- SQL الموجود فقط Index + Function قراءة.
