# Changelog

## v1.2.6 — Cloudflare Pages Migration

- تحويل Netlify Functions إلى Cloudflare Pages Functions.
- إضافة `functions/api/lookup-player.ts`.
- إضافة `functions/api/drive-image.ts`.
- تحديث مسار البحث في الواجهة إلى `/api/lookup-player`.
- تحديث مسار بروكسي صور Google Drive إلى `/api/drive-image`.
- إضافة `public/_redirects` لدعم SPA fallback وبعض روابط Netlify القديمة.
- إضافة `wrangler.toml` وملف `CLOUDFLARE_DEPLOYMENT.md`.
- حذف `netlify.toml` ومجلد `netlify/functions`.

## v1.2.5 - Database Speed Boost

- Added optional Supabase performance SQL: `supabase/sql/007_speed_indexes_academy_people.sql`.
- Added a fast partial index on `public.academy_people(unique_number, updated_at, created_at)` for active records.
- Added read-only RPC `public.academy_lookup_person_fast(text)` for faster scan lookup.
- Updated Netlify lookup function to use the RPC first, with automatic fallback to direct table lookup if the SQL has not been run yet.
- Reduced server-side lookup timeout for faster failure/retry behavior.
- No player data insert/update/delete operations are performed.

## v1.2.4 - Scan Performance + Compact Cards

- Faster scan response with client-side lookup cache and in-flight request deduplication.
- Faster Netlify lookup function with read-only exact unique_number query and short server timeout.
- Added server-side short TTL cache for repeated scans during the same session.
- Reduced USB scanner idle submit delay from 360ms to 110ms.
- Reduced keyboard scanner buffer idle delay to 90ms.
- Optimized mobile camera scanning resolution/frame pacing for smoother QR detection.
- Improved Google Drive image proxy cache and requested smaller image size for cards.
- Made player result cards more compact so player details fit better without scrolling.
- No SQL required and no database write operations.

## v1.2.3 — Supabase lowercase table fix + true read-only

- تغيير مصدر القراءة إلى `public.academy_people` لأن Supabase/PostgREST أظهر أن الجدول الفعلي بالأحرف الصغيرة.
- تعطيل أي كتابة اختيارية في `scan_logs`؛ النسخة الآن تقرأ فقط من جدول اللاعبين ولا تعدّل قاعدة البيانات.
- استمرار دعم صور Google Drive عبر Function `drive-image`.

## v1.2.2 — Google Drive images fix

- إضافة Netlify Function جديدة: `netlify/functions/drive-image.ts`.
- تحويل روابط Google Drive تلقائيًا إلى Image Proxy داخلي بدل عرض رابط Drive مباشرة.
- دعم أكثر من صيغة Google Drive: `open?id=`, `uc?export=view&id=`, `file/d/...`, و `lh3.googleusercontent.com/d/...`.
- قراءة رابط الصورة من `image_url`، ولو فاضي يتم محاولة استخراجه من `raw_payload`.
- إضافة fallback داخل الكارت لتجربة Google Thumbnail و `lh3` لو البروكسي فشل.
- لا يوجد أي SQL أو تعديل مطلوب على قاعدة البيانات.

## v1.2.1 — academy_people read-only hard fix

- تثبيت اسم جدول البحث داخل Netlify Function على `academy_people` فقط.
- حذف الاعتماد على أي Environment Variable قديمة ممكن تكون قيمتها `players`.
- حذف مجلد `.netlify` القديم من المشروع حتى لا يتم نشر Function قديمة.
- حذف ملفات SQL القديمة من النسخة لتجنب أي لخبطة؛ لا يوجد أي تعديل مطلوب على قاعدة البيانات.
- ربط حقول جدول `academy_people` مباشرة بالكارت.
- دعم استخراج الرقم من `UN` داخل QR payload.
