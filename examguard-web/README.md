# AI ExamGuard — نسخة الويب

موقع ثابت (Static) بيشغّل كاميرا المتصفح ويكشف الهاتف المحمول باستخدام YOLO11n عن طريق ONNX Runtime Web.
كل المعالجة بتتم على جهاز المستخدم، فمفيش سيرفر ولا رفع فيديو، وينفع يتنشر على أي استضافة مجانية.

## محتويات المجلد

```
examguard-web/
├── index.html
└── model/
    └── yolo11n.onnx      ← الموديل بعد التحويل
```

## تحويل الموديل (لو الملف مش موجود)

```bash
pip install ultralytics onnx
yolo export model=yolo11n.pt format=onnx imgsz=640 opset=12 simplify=True
```

انقل الملف الناتج `yolo11n.onnx` إلى مجلد `model/`.
لو الجهاز بطيء، استخدم `imgsz=416` — الموقع بيقرأ حجم الإدخال تلقائياً من الموديل.

## تجربة محلية

الكاميرا بتشتغل على `localhost` من غير HTTPS:

```bash
cd examguard-web
python -m http.server 8000
```

افتح http://localhost:8000

## النشر المجاني (اختر واحدة)

لازم الرابط يكون HTTPS عشان المتصفح يسمح بالكاميرا، وكل الاستضافات دي بتوفره.

**1) Cloudflare Pages (الأسهل، رفع بالسحب والإفلات)**
1. سجّل في dash.cloudflare.com ← Workers & Pages ← Create ← Pages ← Upload assets.
2. اسحب مجلد `examguard-web` كله وانشر.

**2) Netlify**
1. app.netlify.com/drop
2. اسحب المجلد، وبيطلعلك رابط فوراً.

**3) GitHub Pages**
1. أنشئ Repository وارفع الملفات (الموديل حوالي 10MB، مسموح).
2. Settings ← Pages ← Branch: main ← Save.

**4) Hugging Face Spaces (Static)**
1. Create Space ← SDK: Static.
2. ارفع الملفات في الـ Space.

## الانتقال لموديل مخصص (best.pt)

1. حوّله: `yolo export model=best.pt format=onnx imgsz=640 opset=12`
2. حط الناتج في `model/best.onnx` وغيّر `MODEL_URL` في `index.html`.
3. أنشئ `model/classes.json` بأسماء الكلاسات بالترتيب، مثلاً: `["phone", "person"]`.
   لو اسم كلاس الهاتف مختلف، أضفه في `PHONE_CLASS_NAMES` داخل `index.html`.

## ملاحظات

- أول تحميل بياخد ثواني لأن المتصفح بينزل الموديل ومكتبة ONNX.
- لو الـ FPS قليل: صدّر الموديل بحجم أصغر (416 أو 320).
- الصوت ما بيشتغل إلا بعد ضغط «تشغيل الكاميرا» (قيد من المتصفحات).
- موديل COCO الجاهز بيكشف `cell phone`، لكن دقته في الإضاءة الضعيفة أو الهاتف المخفي جزئياً محدودة، ومع التدريب المخصص هتتحسن.
