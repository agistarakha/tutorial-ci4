# Praktek: Setup CodeIgniter 4

## 1. Install CodeIgniter 4 via Composer

### Pastikan Composer Terinstall
```bash
composer --version
```

### Install CodeIgniter 4
Buka Command Prompt/PowerShell di folder project:
```bash
cd C:\xampp\htdocs\device-management
# atau
cd C:\laragon\www\device-management

# Install CodeIgniter 4
composer create-project codeigniter4/appstarter .
```

**Catatan:** Tanda titik (.) di akhir berarti install di folder saat ini.

### Jika Ada Konfirmasi
Jika ditanya "Do you want to remove the existing VCS (.git, .svn..)?" → Pilih **No** (kecuali folder benar-benar kosong).

### Tunggu Proses Install
Composer akan mendownload semua dependencies. Proses ini bisa beberapa menit.

---

## 2. Struktur Folder CodeIgniter 4

Setelah install, struktur folder akan seperti ini:

```
device-management/
├── app/                    # Folder utama aplikasi
│   ├── Config/            # File konfigurasi
│   ├── Controllers/       # Controller files
│   ├── Models/           # Model files
│   ├── Views/            # View files (HTML)
│   ├── Database/         # Migration & Seeder
│   │   ├── Migrations/   # Database migrations
│   │   └── Seeds/        # Database seeders
│   └── ...
├── public/                # Folder public (web root)
│   ├── index.php         # Entry point aplikasi
│   ├── .htaccess         # Apache configuration
│   └── ...
├── writable/             # Folder untuk file yang bisa ditulis
│   ├── cache/           # Cache files
│   ├── logs/            # Log files
│   ├── session/         # Session files
│   └── uploads/         # Uploaded files
├── vendor/               # Dependencies (dari Composer)
├── .env                  # Environment configuration (buat dari .env.example)
├── .env.example          # Template environment
├── composer.json         # Composer dependencies
└── spark                 # CLI tool CodeIgniter 4
```

---

## 3. Setup Environment (.env)

### Copy File .env
```bash
# Di Windows Command Prompt
copy .env.example .env

# Atau di PowerShell
Copy-Item .env.example .env
```

### Edit File .env
Buka file `.env` dengan text editor atau VS Code.

### Konfigurasi Database
Cari bagian `database` dan edit:

```env
#--------------------------------------------------------------------
# DATABASE
#--------------------------------------------------------------------

database.default.hostname = localhost
database.default.database = device_management
database.default.username = root
database.default.password = 
database.default.DBDriver = MySQLi
database.default.DBPrefix = 
database.default.port = 3306
```

**Penjelasan:**
- `hostname`: Host database (biasanya localhost)
- `database`: Nama database (buat dulu di phpMyAdmin)
- `username`: Username database (default XAMPP/Laragon: root)
- `password`: Password database (default XAMPP/Laragon: kosong)
- `DBDriver`: Driver database (MySQLi untuk MySQL)
- `port`: Port database (default MySQL: 3306)

### Konfigurasi App
Cari bagian `app` dan edit jika perlu:

```env
#--------------------------------------------------------------------
# APP
#--------------------------------------------------------------------

app.baseURL = 'http://localhost/device-management/public/'
# atau jika pakai Laragon:
# app.baseURL = 'http://device-management.test/'

app.timezone = 'Asia/Jakarta'
app.locale = 'id'
```

**Catatan:** Sesuaikan `baseURL` dengan setup local Anda.

---

## 4. Buat Database

### Via phpMyAdmin
1. Buka: http://localhost/phpmyadmin
2. Klik tab **Databases**
3. Masukkan nama database: `device_management`
4. Pilih collation: `utf8mb4_general_ci`
5. Klik **Create**

### Via Command Line (Alternatif)
```bash
# Login ke MySQL
mysql -u root -p

# Buat database
CREATE DATABASE device_management CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;

# Exit
exit;
```

---

## 5. Test Installasi

### Buka Browser
Buka URL sesuai konfigurasi `.env`:
- XAMPP: http://localhost/device-management/public/
- Laragon: http://device-management.test/

### Harus Muncul
Halaman welcome CodeIgniter 4 dengan pesan:
```
Welcome to CodeIgniter 4
```

Jika muncul error, cek:
1. Apache & MySQL sudah running
2. File `.env` sudah dibuat dan dikonfigurasi
3. Database sudah dibuat
4. `baseURL` di `.env` sudah benar

---

## 6. Setup Folder Permissions (Jika Error)

### Windows
Biasanya tidak perlu setup permissions di Windows, tapi jika ada error:

1. Klik kanan folder `writable/`
2. Properties → Security
3. Edit permissions untuk folder `writable/` dan subfoldernya
4. Berikan Full Control untuk User

### Via Command (PowerShell as Administrator)
```powershell
icacls "writable" /grant Users:F /T
```

---

## 7. Install via Spark CLI (Opsional)

CodeIgniter 4 punya CLI tool bernama `spark`:

### Test Spark
```bash
php spark
```

### Contoh Commands Spark
```bash
# Buat controller
php spark make:controller DeviceController

# Buat model
php spark make:model DeviceModel

# Buat migration
php spark make:migration CreateDeviceTypesTable

# Run migration
php spark migrate

# Rollback migration
php spark migrate:rollback
```

---

## 8. Verifikasi Installasi

### Checklist
- [ ] CodeIgniter 4 terinstall via Composer
- [ ] File `.env` sudah dibuat dan dikonfigurasi
- [ ] Database `device_management` sudah dibuat
- [ ] Halaman welcome CodeIgniter 4 muncul di browser
- [ ] Tidak ada error di browser atau log

### Cek Log (Jika Ada Error)
File log ada di: `writable/logs/log-YYYY-MM-DD.log`

Buka file log terbaru untuk melihat error detail.

---

## 9. Konfigurasi Tambahan

### Enable Error Display (Development)
Di file `.env`, pastikan:
```env
CI_ENVIRONMENT = development
```

### Disable Error Display (Production)
```env
CI_ENVIRONMENT = production
```

### Timezone
```env
app.timezone = 'Asia/Jakarta'
```

### Locale
```env
app.locale = 'id'
```

---

## 10. Commit ke Git

Setelah setup selesai, commit ke Git:

```bash
git add .
git commit -m "Initial setup: Install CodeIgniter 4 and configure database"
git push origin main
```

**Pastikan `.env` sudah di-ignore** (ada di `.gitignore`), jangan commit file `.env` yang berisi password!

---

## Troubleshooting

### Error: "Class 'CodeIgniter\CLI\CLI' not found"
```bash
# Update Composer dependencies
composer update
```

### Error: "Database connection failed"
- Cek MySQL sudah running
- Cek konfigurasi di `.env` sudah benar
- Cek database sudah dibuat
- Test koneksi manual di phpMyAdmin

### Error: "404 Not Found"
- Cek `baseURL` di `.env` sudah benar
- Pastikan mengakses via `public/` folder
- Cek `.htaccess` ada di folder `public/`

### Error: "Permission denied" di writable/
- Set permissions folder `writable/` dan subfoldernya
- Atau jalankan sebagai Administrator

---

## Selesai!

Setelah CodeIgniter 4 setup, lanjut ke: **praktek_crud.md**

