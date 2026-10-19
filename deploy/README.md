# Avto Vizitka — production

- Domen: `https://showcase.buxoroavto.uz`.
- Server: `169.58.252.161`, Ubuntu 24.04, Nginx, PHP 8.3 FPM, MariaDB.
- Ilova: `/var/www/avto-showcase/current` — aktiv release'ga symlink.
- Release'lar: `/var/www/avto-showcase/releases/<UTC-release-id>`.
- Doimiy fayllar: `/var/www/avto-showcase/shared/.env` va `shared/storage`.
- Linux foydalanuvchisi: `avto_showcase`.
- MySQL bazasi: `avto_showcase`; foydalanuvchi: `avto_showcase_usr@127.0.0.1`.

Production `.env` serverda `0600` huquq bilan saqlanadi. Parollar va `APP_KEY` Git yoki release arxiviga kiritilmaydi. Yangi baza foydalanuvchisi faqat o'z bazasiga kira oladi. `client_demo` CRM bazasi bilan ulanish yo'q.

## Yangi release

Lokal `dev.ps1 build` bilan frontendni yig'ing. Manba va `public/build` fayllarini yangi `.tar.gz` arxivga kiriting. Arxivda `.env`, `.git`, `vendor`, `node_modules`, `storage`, `bootstrap/cache`, `public/hot` va SQLite bazalari bo'lmasin.

Arxivni SSH/SFTP orqali serverga yuklang, so'ng root sifatida:

```bash
bash /var/www/avto-showcase/current/deploy/release.sh \
  /root/avto-deploy/showcase-new.tar.gz \
  /var/www/avto-showcase/current/deploy/site.conf \
  20261018T150000Z
```

Oxirgi argument har release uchun yangi UTC vaqt identifikatori bo'lishi kerak. Skript Composer paketlarini `--no-dev` bilan ilova foydalanuvchisi nomidan o'rnatadi, migratsiya va Laravel keshlarini tayyorlaydi, `current` symlink'ini almashtiradi hamda PHP FPM va faqat shu loyihaning worker'ini yangilaydi. Frontend serverda qayta yig'ilmaydi.

Oldingi release yo'li `/var/www/avto-showcase/previous-release` faylida saqlanadi. Kodni qaytarish uchun `current` symlink'ini oldingi release'ga almashtiring, PHP FPM'ni reload qiling va worker'ni restart qiling. Migratsiyalar avtomatik orqaga qaytarilmaydi; baza o'zgarishlarini alohida tekshiring.

## Xizmatlar

- Nginx: `/etc/nginx/sites-available/showcase.buxoroavto.uz`.
- PHP pool: `/etc/php/8.3/fpm/pool.d/avto-showcase.conf`.
- Queue: Supervisor'dagi `avto-showcase-worker`.
- Scheduler: `/etc/cron.d/avto-applications`, har daqiqada ilova foydalanuvchisi nomidan.
- Laravel loglari: `/var/www/avto-showcase/shared/storage/logs`.
- Nginx loglari: `/var/log/nginx/avto-showcase.*.log`.
- Health endpoint: `https://showcase.buxoroavto.uz/up`.

```bash
supervisorctl status avto-showcase-worker
nginx -t
systemctl status php8.3-fpm
```

## HTTPS

2026-10-19 kuni tekshirildi: `showcase` A yozuvi `169.58.252.161` ga yo'naltirilgan, HTTPS orqali `/up` `200 OK` qaytaradi. Joriy sertifikat 2027-01-16 gacha amal qiladi.

Let's Encrypt sertifikati o'rnatilgan. HTTP so'rovlari HTTPS'ga yo'naltiriladi. Sertifikatlarni mavjud `certbot.timer` yangilaydi; Nginx reload hook `/etc/letsencrypt/renewal-hooks/deploy/50-avto-nginx-reload` ichida.

```bash
certbot renew --cert-name showcase.buxoroavto.uz --dry-run --no-random-sleep-on-renew
```

## Zaxiralar

`/usr/local/sbin/avto-backup` har kuni server vaqti bilan 03:15 da ikkala yangi loyihaning bazasi, `.env` va `storage/app` fayllarini `/var/backups/avto` ga saqlaydi. Saqlash muddati 14 kun, papka va fayllar faqat root uchun ochiq. Birinchi zaxira deploy vaqtida olindi. Bu server ichidagi zaxira; boshqa serverga nusxalash sozlanmagan.

Server sozlamalarining dastlabki nusxasi `/root/avto-deploy/backups` ichida. Yangi loyihalarning kirish ma'lumotlari `/root/avto-deployment-credentials.json` ichida `0600` huquq bilan saqlanadi.
