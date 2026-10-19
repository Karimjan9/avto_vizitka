# Avto Vizitka

Avto loyihasining vizitka sayti. Platforma backendidan alohida Laravel ilovasi va Git repozitoriysi.

- Git: https://github.com/Karimjan9/avto_vizitka.git
- Stek: Laravel 12, Blade, oddiy JavaScript, Vite 7, Tailwind CSS 4, Axios.
- Talablar: PHP 8.2+, Composer 2 va Node.js 24 LTS / npm.
- Lokal manzil: http://127.0.0.1:8000
- Vite: http://127.0.0.1:5173

## Ishga tushirish

Shu papkada PowerShell orqali:

```powershell
powershell -NoProfile -ExecutionPolicy Bypass -File .\dev.ps1
```

Laravel serveri, navbat ishchisi va Vite birga ishga tushadi. To'xtatish: `Ctrl+C`.
Skript mavjud bo'lsa `../.tools` ichidagi lokal Node.js va Composer'dan foydalanadi.

Oddiy terminalda Node.js 24 LTS faol bo'lsa, `composer dev` ham ishlaydi.

## Yangi klondan o'rnatish

```powershell
powershell -NoProfile -ExecutionPolicy Bypass -File .\dev.ps1 setup
```

Bu buyruq Composer va npm paketlarini o'rnatadi, mavjud bo'lmasa `.env` va SQLite bazasini yaratadi, ilova kalitini generatsiya qiladi, migratsiyalarni bajaradi va frontendni yig'adi. Kalit generatsiyasi sababli `setup` yangi lokal o'rnatish uchun mo'ljallangan.

## Tekshirish va yig'ish

```powershell
powershell -NoProfile -ExecutionPolicy Bypass -File .\dev.ps1 test
powershell -NoProfile -ExecutionPolicy Bypass -File .\dev.ps1 build
```

Vite'siz, yig'ilgan CSS/JS bilan ishlatish:

```powershell
php artisan serve --host=127.0.0.1 --port=8000
```

## Tuzilma

- `routes/web.php` — web marshrutlar.
- `resources/views/layouts/app.blade.php` — asosiy Blade layout, CSRF va Vite ulanishi.
- `resources/views/home.blade.php` — bosh sahifa.
- `resources/js/app.js` — JavaScript kirish nuqtasi.
- `resources/css/app.css` — Tailwind va uslublar.
- `app/Http/Controllers` — kontrollerlar.
- `database/migrations` — baza migratsiyalari.

Har bir loyihaning `.env`, `APP_KEY`, SQLite bazasi, sessiya cookie nomi va kesh prefiksi alohida. Vaqt zonasi `Asia/Tashkent`, asosiy til `uz`, tarjima topilmasa `en` ishlatiladi. `.env`, baza, `vendor`, `node_modules` va build fayllari Git'ga kiritilmaydi; ikkala lock fayli saqlanadi.

XAMPP/MySQL kerak bo'lsa `.env` ichida `DB_CONNECTION=mysql`, `DB_HOST`, `DB_PORT`, `DB_DATABASE`, `DB_USERNAME`, `DB_PASSWORD` ni sozlang va `php artisan migrate` ni bajaring. Vizitka va platforma uchun alohida bazalar yarating.

Apache virtual host'ining `DocumentRoot` qiymati ushbu loyihaning `public` papkasiga yo'naltirilishi kerak.

Production server, baza, HTTPS, zaxiralar va keyingi deploy tartibi: [deploy/README.md](deploy/README.md).
