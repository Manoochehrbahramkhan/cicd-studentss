
# Git Bisect و Git Cherry-Pick

## 📌 1. Git Bisect
`git bisect` به شما کمک می‌کند تا یک کامیت خاص که باعث ایجاد باگ شده را به‌صورت دودویی (binary search) پیدا کنید.

### 🎯 کاربرد:
پیدا کردن دقیق اولین commit که باعث شکسته شدن build یا اضافه شدن یک باگ شده.

### ✅ گام‌های استفاده:

```bash
git bisect start                   # شروع فرایند bisect
git bisect bad                     # مشخص کردن وضعیت فعلی (bad)
git bisect good <commit-hash>     # مشخص کردن آخرین commit سالم
```

سپس Git بین good و bad جستجوی دودویی انجام می‌دهد و شما را روی یک commit قرار می‌دهد. شما باید بررسی کنید که آن commit "سالم" یا "خراب" است و یکی از این‌ها را وارد کنید:

```bash
git bisect good
# یا
git bisect bad
```

در نهایت، پس از پیدا شدن commit مشکل‌دار:

```bash
git bisect reset  # بازگشت به حالت اولیه
```

### 🧪 مثال عملی:
```bash
git bisect start
git bisect bad
git bisect good 9d21e4f
# حالا Git شما را به commit وسط می‌برد...
```

---

## 📌 2. Git Cherry-Pick
`git cherry-pick` برای اعمال یک commit خاص از یک branch دیگر به شاخه فعلی استفاده می‌شود.

### 🎯 کاربرد:
انتقال یک یا چند commit خاص از یک feature branch به شاخه اصلی بدون merge کردن کل آن.

### ✅ گام‌های استفاده:

```bash
git checkout main
git cherry-pick <commit-hash>
```

### ⚠️ نکته:
اگر conflict ایجاد شد، ابتدا conflict را حل کن سپس:

```bash
git add .
git cherry-pick --continue
```

### 🧪 مثال عملی:
فرض کن یک feature خاص روی branch زیر توسعه داده شده:
```bash
git log --oneline feature-branch
# خروجی:
# a3c8f20 افزودن سیستم لاگ
# b23fa11 اصلاح query دیتابیس ← این کامیت مفید است
# 91bd7d9 اضافه‌کردن فایل README

# حالا فقط کامیت اصلاح query را به master می‌خواهیم بیاوریم:
git checkout master
git cherry-pick b23fa11
```

---

## ✅ تفاوت اصلی:
| ابزار               | کاربرد اصلی                                      |
|---------------------|---------------------------------------------------|
| `git bisect`        | یافتن کامیت خراب از بین مجموعه‌ای از کامیت‌ها    |
| `git cherry-pick`   | انتقال کامیت خاص از یک شاخه به شاخه‌ای دیگر     |

---

تهیه شده برای آموزش حرفه‌ای Git در تیم‌های DevOps 👨‍💻

