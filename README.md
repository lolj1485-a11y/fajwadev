# fajwadev — فجوة للبحث والتطوير

## النشر على Render

ملف `render.yaml` يجهّز خدمة Python بخادم Gunicorn على خطة Free المجانية
حسب اختيار المالك، دون قرص مدفوع. طلبات SQLite مؤقتة وقد تُفقد عند
إعادة التشغيل أو النشر. قد تتوقف الخدمة عند عدم الاستخدام.
تم رفع حزمة المصدر إلى https://github.com/lolj1485-a11y/fajwadev.

1. أنشئ حساب GitHub وارفع ملفات المشروع إلى مستودع، دون `.env` أو `instance/` أو `.venv/`.
2. سجّل الدخول إلى Render، ثم اختر **New → Blueprint** واربط المستودع الذي يحتوي على `render.yaml`.
3. اختر Free بسعر 0 دولار، دون إضافة قرص. سيظهر رابط `https://….onrender.com` بعد نجاح البناء.
4. يتولد `SECRET_KEY` تلقائياً، وتكون `COOKIE_SECURE=1` مضبوطة في الملف. تستخدم قاعدة البيانات المسار الافتراضي المؤقت.
5. أضف إعدادات البريد وواتساب من `.env.example` في **Environment** عند توفرها.
   حساب الإدارة يحتاج `FAJWA_ADMIN_PASSWORD_HASH` بالصيغة التي يستخدمها `verify_password`؛ دونها يبقى تسجيل دخول الإدارة غير مفعّل.

يمكن إنشاء Web Service يدوياً بدلاً من Blueprint بالقيم التالية:

```text
Build Command: python -m zipfile -e fajwadev_Render_Ready.zip . && pip install -r requirements.txt
Start Command: gunicorn app:app --bind 0.0.0.0:$PORT --workers 1 --threads 4 --timeout 120
Health Check Path: /healthz
Plan: Free ($0/month)
```

أضف `SECRET_KEY` عشوائياً وثابتاً و`COOKIE_SECURE=1` في إعدادات الخدمة اليدوية.

## النطاق ومحركات البحث

اسم الموقع وخدمة Render هو `fajwadev`، والنطاق المقترح `fajwadev.com`.
لم يظهر سجل تسجيل للنطاق في فحص Verisign بتاريخ 2026-10-06؛
يجب تأكيد توفره وسعره عند شركة التسجيل ثم شراؤه قبل ربطه.
لم يُشترَ النطاق ولم تُنشأ خدمة Render بعد.

بعد النشر وامتلاك النطاق:

1. أضف `fajwadev.com` من **Settings → Custom Domains** في Render.
2. طبّق سجلات DNS المطلوبة التي تعرضها لوحة Render لدى شركة تسجيل النطاق، ثم اضغط **Verify**.
3. بعد عمل HTTPS على النطاق، اضبط `PUBLIC_SITE_URL=https://fajwadev.com` في **Environment** وأعد النشر.
   قبل ذلك يستخدم الموقع عنوان Render تلقائياً في الروابط الأساسية وخريطة الموقع.
4. أثبت ملكية النطاق في Google Search Console، وأرسل `https://fajwadev.com/sitemap.xml` واطلب فهرسة الصفحة الرئيسية.

المشروع يتضمن `robots.txt` و`sitemap.xml` وروابط canonical وبيانات Organization
ووصف الصفحات. صفحات الإدارة تمنع فهرستها. ظهور الموقع وتوقيته وترتيبه
تحدده محركات البحث؛ إعداد الموقع لا يضمن ظهوره لكل بحث.

المراجع: https://render.com/docs/deploy-flask وhttps://render.com/docs/custom-domains
وhttps://render.com/docs/disks.

## تشغيل الموقع

```powershell
pip install -r requirements.txt
python app.py
```

ثم افتح:

`http://127.0.0.1:5000`

## التحكم بالورشة

افتح الملف `app.py` وابحث عن:

```python
WORKSHOP_FORM_URL="..."
```

- إذا وضعت رابط الفورمة: تظهر الورشة وزر **التقديم على الورشة**.
- إذا حذفت الرابط وأصبح:

```python
WORKSHOP_FORM_URL=""
```

ستظهر للزائر رسالة **لا توجد ورش حالياً**.

يمكنك أيضًا تعديل:

```python
WORKSHOP_TITLE="..."
WORKSHOP_DESCRIPTION="..."
```

ثم أعد تشغيل الموقع.


## إرسال الطلبات إلى البريد والواتساب

عند إرسال الزبون نموذج **اطلب خدمة** يتم حفظ الطلب في قاعدة البيانات، ثم يحاول الموقع إرسال إشعار إلى:

- البريد الرسمي: `Fajwa.rd1@gmail.com`
- واتساب الشركة/المسؤول عبر **WhatsApp Business Cloud API**

### إعداد الإيميل

انسخ `.env.example` إلى ملف باسم `.env` ثم ضع **Gmail App Password** في:

```text
FAJWA_EMAIL_PASSWORD=...
```

لا تضع كلمة مرور Gmail العادية داخل الموقع.

### إعداد واتساب

في Meta WhatsApp Business Platform أنشئ قالب رسالة باسم افتراضي:

```text
new_service_request
```

ويكون نص القالب مثلاً:

```text
لديك طلب خدمة جديد من موقع فجوة:
{{1}}
```

ثم ضع في `.env`:

```text
WHATSAPP_TO=9647747755799
WHATSAPP_PHONE_NUMBER_ID=رقم Phone Number ID من Meta
WHATSAPP_ACCESS_TOKEN=رمز الوصول من Meta
WHATSAPP_API_VERSION=إصدار Graph API الذي تستخدمه Meta
WHATSAPP_TEMPLATE_NAME=new_service_request
WHATSAPP_TEMPLATE_LANGUAGE=ar
```

> ملاحظة: الإرسال التلقائي خارج نافذة محادثة WhatsApp يعتمد على قالب معتمد من Meta. اسم القالب واللغة يجب أن يطابقا القالب الموجود في حساب WhatsApp Business.

### تشغيل محلياً

```powershell
pip install -r requirements.txt
python app.py
```

يفتح الموقع على:

`http://127.0.0.1:5000`

وللاختبار من هاتف على نفس الشبكة يمكن استخدام عنوان الكمبيوتر، مثل:

`http://192.168.0.115:5000`
