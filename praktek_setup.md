# Praktek: Setup Tools dan Environment

## 1. Install Git

### Windows
1. Download Git dari: https://git-scm.com/download/win
2. Jalankan installer
3. Pilih opsi default (Next, Next, Next)
4. Pilih "Git from the command line and also from 3rd-party software"
5. Finish instalasi

### Verifikasi Install
Buka Command Prompt atau PowerShell, ketik:
```bash
git --version
```
Harus muncul: `git version 2.x.x` atau lebih baru

### Setup Git (Pertama Kali)
```bash
git config --global user.name "Nama Anda"
git config --global user.email "email@example.com"
```

### Cek Konfigurasi
```bash
git config --list
```

---

## 2. Install Visual Studio Code

### Windows
1. Download dari: https://code.visualstudio.com/
2. Jalankan installer
3. Pilih "Add to PATH" saat instalasi
4. Finish instalasi

### Extension yang Disarankan
Setelah install VS Code, buka Extensions (Ctrl+Shift+X) dan install:
- **PHP Intelephense** - Autocomplete untuk PHP
- **GitLens** - Visualisasi Git history
- **Bracket Pair Colorizer** - Warna bracket matching
- **Prettier** - Code formatter

---

## 3. Install XAMPP atau Laragon

### Opsi A: XAMPP

#### Install XAMPP
1. Download dari: https://www.apachefriends.org/download.html
2. Pilih versi PHP 8.1 atau 8.2
3. Jalankan installer
4. Install ke folder default: `C:\xampp`
5. Finish instalasi

#### Install Composer (untuk XAMPP)
1. Download Composer dari: https://getcomposer.org/download/
2. Pilih "Composer-Setup.exe"
3. Jalankan installer
4. Saat diminta PHP executable, pilih: `C:\xampp\php\php.exe`
5. Finish instalasi

#### Verifikasi Composer
Buka Command Prompt, ketik:
```bash
composer --version
```

#### Start XAMPP
1. Buka XAMPP Control Panel
2. Start **Apache** dan **MySQL**
3. Buka browser: http://localhost
4. Buka phpMyAdmin: http://localhost/phpmyadmin

### Opsi B: Laragon (Lebih Mudah untuk Windows)

#### Install Laragon
1. Download dari: https://github.com/leokhoa/laragon/releases/tag/6.0.0
2. Pilih file `.exe` terbaru
3. Jalankan installer
4. Install ke folder default: `C:\laragon`
5. Finish instalasi

#### Start Laragon
1. Buka Laragon
2. Klik **Start All**
3. Buka browser: http://localhost
4. Buka phpMyAdmin: http://localhost/phpmyadmin

**Catatan:** Laragon sudah include Composer, tidak perlu install terpisah!

---

## 4. Buat Akun GitHub

1. Buka: https://github.com/
2. Klik **Sign up**
3. Isi:
   - Username
   - Email
   - Password
4. Verifikasi email
5. Selesai!

---

## 5. Verifikasi Semua Tools

### Checklist Instalasi
- [ ] Git terinstall → `git --version`
- [ ] VS Code terinstall → Buka aplikasi VS Code
- [ ] XAMPP/Laragon terinstall → Apache & MySQL running
- [ ] Composer terinstall → `composer --version` (jika pakai XAMPP)
- [ ] Akun GitHub dibuat → Login ke github.com

### Test PHP
Buat file `test.php` di folder `C:\xampp\htdocs\` atau `C:\laragon\www\`:
```php
<?php
phpinfo();
?>
```

Buka browser: http://localhost/test.php
Harus muncul informasi PHP.

---

## 6. Setup Folder Project

### Buat Folder Project
```bash
# Buka Command Prompt atau PowerShell
cd C:\xampp\htdocs
# atau jika pakai Laragon:
cd C:\laragon\www

# Buat folder project
mkdir device-management
cd device-management
```

**Catatan:** Folder ini akan digunakan untuk project CodeIgniter 4 nanti.

---

## Troubleshooting

### Git tidak dikenali di Command Prompt
- Restart Command Prompt setelah install Git
- Atau restart komputer

### Composer tidak dikenali
- Pastikan sudah install Composer
- Restart Command Prompt
- Cek PATH environment variable

### Apache tidak bisa start
- Cek port 80 dan 443 tidak digunakan aplikasi lain
- Buka XAMPP Control Panel → Config → httpd.conf
- Ubah port jika perlu

### MySQL tidak bisa start
- Cek port 3306 tidak digunakan
- Stop aplikasi lain yang pakai MySQL

---

## Selesai!

Setelah semua tools terinstall, lanjut ke: **praktek_git_github.md**

