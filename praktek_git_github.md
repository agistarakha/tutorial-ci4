# Praktek: Git & GitHub Workflow

## Workflow yang Akan Kita Lakukan

**Urutan kerja:**
1. ✅ Buat project CodeIgniter 4 dulu (sudah dilakukan di `praktek_codeigniter4.md`)
2. ✅ Init Git di local project
3. ✅ Buat remote repository di GitHub
4. ✅ Setup Personal Access Token untuk autentikasi
5. ✅ Connect local ke remote dan push (menggunakan PAT)

---

## 0. Setup Personal Access Token (Untuk Autentikasi Push/Pull)

**Penting:** GitHub tidak lagi menerima password untuk Git operations (push/pull). Anda perlu Personal Access Token untuk autentikasi saat push/pull ke remote repository.

### Langkah 1: Buat Personal Access Token di GitHub

1. Login ke https://github.com/
2. Klik foto profil di kanan atas → **Settings**
3. Scroll ke bawah, klik **Developer settings** (di sidebar kiri)
4. Klik **Personal access tokens** → **Tokens (classic)**
5. Klik **Generate new token** → **Generate new token (classic)**
6. Isi form:
   - **Note**: "Device Management Project" (atau nama lain)
   - **Expiration**: Pilih durasi (90 days, atau sesuai kebutuhan)
   - **Scopes**: Centang minimal:
     - ✅ `repo` (Full control of private repositories)
     - ✅ `workflow` (jika pakai GitHub Actions)
7. Klik **Generate token** di bawah
8. **PENTING:** Copy token yang muncul (hanya muncul sekali!)
   ```
   ghp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
   ```
9. Simpan token di tempat yang aman (jangan share ke publik!)

**Mengapa perlu Personal Access Token?**
- GitHub tidak lagi menerima password untuk Git operations (push/pull)
- Personal Access Token lebih aman daripada password
- Token bisa di-revoke kapan saja jika terkompromi
- Diperlukan untuk autentikasi saat push/pull ke remote repository

**Tips Keamanan:**
- Jangan commit token ke repository
- Jangan share token di public
- Set expiration date yang wajar
- Revoke token jika tidak digunakan lagi
- Gunakan token dengan scope minimal yang diperlukan

**Kapan menggunakan PAT?**
- Saat `git push` ke remote repository
- Saat `git pull` dari remote repository
- Saat melakukan operasi Git lainnya yang memerlukan autentikasi

---

## 1. Init Git di Local Project

**Pastikan:** Project CodeIgniter 4 sudah dibuat di folder `device-management`.

### Langkah 1: Masuk ke Folder Project

Buka Command Prompt atau PowerShell:
```bash
cd C:\xampp\htdocs\device-management
# atau
cd C:\laragon\www\device-management
```

### Langkah 2: Inisialisasi Git

```bash
# Init Git repository di folder project
git init
```

**Output yang muncul:**
```
Initialized empty Git repository in C:/xampp/htdocs/device-management/.git/
```

### Langkah 3: Setup Git Config (Jika Belum)

Jika belum setup Git config sebelumnya:
```bash
git config --global user.name "Nama Anda"
git config --global user.email "email@example.com"
```

### Langkah 4: Buat .gitignore

Buat file `.gitignore` di root project untuk mengabaikan file yang tidak perlu di-commit:

**File:** `.gitignore`

```gitignore
# CodeIgniter 4
/writable/
/vendor/
.env
.env.*
!.env.example

# OS Files
.DS_Store
Thumbs.db
desktop.ini

# IDE
.vscode/
.idea/
*.sublime-project
*.sublime-workspace

# Logs
*.log
/writable/logs/*.log

# Cache
/writable/cache/*
/writable/session/*
/writable/uploads/*

# Backup
*.bak
*.swp
*.swo
*~
```

### Langkah 5: Add dan Commit Pertama Kali

```bash
# Tambahkan semua file ke staging
git add .

# Commit pertama kali
git commit -m "Initial commit: Setup CodeIgniter 4 project"
```

**Catatan:** File `.env` tidak akan ter-commit karena sudah di-ignore oleh `.gitignore`.

---

## 2. Buat Remote Repository di GitHub

Setelah project di local sudah di-init dan di-commit, baru kita buat repository di GitHub.

### Langkah-langkah:
1. Login ke https://github.com/
2. Klik tombol **+** di kanan atas → **New repository**
3. Isi form:
   - **Repository name**: `device-management` (atau nama lain)
   - **Description**: "Device Management System dengan CodeIgniter 4"
   - **Visibility**: Public atau Private (pilih sesuai kebutuhan)
   - **JANGAN** centang "Initialize this repository with a README"
   - **JANGAN** pilih license atau .gitignore (karena kita sudah punya di local)
4. Klik **Create repository**

### Setelah Repository Dibuat
GitHub akan menampilkan halaman dengan instruksi. **Copy URL repository** yang muncul, contoh:
```
https://github.com/username/device-management.git
```

---

## 3. Connect Local ke Remote dan Push

Kembali ke Command Prompt/PowerShell di folder project.

### Langkah 1: Tambahkan Remote Repository

**Opsi A: Remote URL Biasa (Akan Diminta Token Saat Push/Pull)**

```bash
# Tambahkan remote repository (ganti URL dengan URL repository Anda)
git remote add origin https://github.com/username/device-management.git

# Verifikasi remote sudah ditambahkan
git remote -v
```

**Opsi B: Remote URL dengan PAT (Lebih Praktis - Tidak Perlu Input Token Setiap Kali)**

```bash
# Tambahkan remote repository dengan PAT di URL
# Format: https://TOKEN@github.com/username/repo.git
git remote add origin https://ghp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx@github.com/username/device-management.git

# Verifikasi remote sudah ditambahkan
git remote -v
```

**Output yang muncul:**
```
origin  https://ghp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx@github.com/username/device-management.git (fetch)
origin  https://ghp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx@github.com/username/device-management.git (push)
```

**Catatan:**
- Ganti `ghp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` dengan Personal Access Token Anda
- Ganti `username` dengan username GitHub Anda
- Ganti `device-management` dengan nama repository Anda
- Dengan cara ini, Anda tidak perlu memasukkan token setiap kali push/pull

**Jika Sudah Menambahkan Remote Tanpa PAT, Bisa Update URL:**

```bash
# Update remote URL dengan PAT
git remote set-url origin https://ghp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx@github.com/username/device-management.git

# Verifikasi perubahan
git remote -v
```

### Langkah 2: Rename Branch ke Main (Jika Perlu)

Jika Git menggunakan branch `master`, rename ke `main`:
```bash
# Rename branch master ke main
git branch -M main
```

### Langkah 3: Push ke GitHub

```bash
# Push local repository ke GitHub
git push -u origin main
```

**Jika sudah menambahkan PAT di remote URL (Opsi B di Langkah 1):**
- Push akan langsung berjalan tanpa diminta autentikasi
- Token sudah tersimpan di remote URL

**Jika belum menambahkan PAT di remote URL (Opsi A di Langkah 1):**
- **Saat diminta autentikasi:**
  - **Username**: Masukkan username GitHub Anda
  - **Password**: Masukkan **Personal Access Token** yang sudah dibuat di Section 0 (bukan password GitHub!)

**Rekomendasi:** Gunakan Opsi B (PAT di remote URL) agar lebih praktis dan tidak perlu input token setiap kali push/pull.

**Output yang muncul:**
```
Enumerating objects: XX, done.
Counting objects: 100% (XX/XX), done.
Delta compression using up to X threads
Compressing objects: 100% (XX/XX), done.
Writing objects: 100% (XX/XX), done.
To https://github.com/username/device-management.git
 * [new branch]      main -> main
Branch 'main' set up to track 'remote/origin/main'.
```

### Langkah 4: Verifikasi di GitHub

Buka repository di GitHub, pastikan semua file sudah ter-upload dengan benar.

**Catatan:** Setelah push pertama kali, Git akan menyimpan credential. Untuk push/pull selanjutnya, Anda mungkin tidak perlu memasukkan token lagi (tergantung konfigurasi Git credential helper).

---

## 4. Git Commands Dasar

### 3.1 Status - Cek Perubahan
```bash
git status
```
Menampilkan file-file yang berubah atau belum di-track.

### 3.2 Add - Tambahkan File ke Staging
```bash
# Tambahkan semua file
git add .

# Tambahkan file spesifik
git add nama-file.php

# Tambahkan beberapa file
git add file1.php file2.php
```

### 3.3 Commit - Simpan Perubahan
```bash
git commit -m "Pesan commit yang deskriptif"

# Contoh:
git commit -m "Initial commit: Setup CodeIgniter 4"
git commit -m "Add Device Type CRUD"
git commit -m "Fix bug di DeviceController"
```

**Tips Pesan Commit:**
- Gunakan kalimat yang jelas dan deskriptif
- Gunakan bahasa Inggris atau Indonesia konsisten
- Contoh format: "Action: Description" (Add: Device Type model, Fix: Validation error)

### 3.4 Push - Upload ke GitHub
```bash
# Push ke branch main (atau master)
git push origin main

# Jika pertama kali, mungkin perlu set upstream:
git push -u origin main
```

**Jika diminta autentikasi:**
- **Username**: Masukkan username GitHub Anda
- **Password**: Masukkan **Personal Access Token** (bukan password GitHub!)

**Atau gunakan token di URL:**
```bash
# Push dengan token (ganti TOKEN dengan token Anda)
git push https://ghp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx@github.com/username/repo.git main
```

### 3.5 Pull - Download dari GitHub
```bash
git pull origin main
```
Mengambil perubahan terbaru dari GitHub dan merge ke local.

### 3.6 Log - Lihat History Commit
```bash
# Lihat semua commit
git log

# Lihat commit dengan format singkat
git log --oneline

# Lihat commit dengan grafik
git log --oneline --graph --all
```

---

## 5. Workflow Git untuk Development

### Workflow Standar:

```bash
# 1. Cek status
git status

# 2. Tambahkan file yang berubah
git add .

# 3. Commit dengan pesan yang jelas
git commit -m "Deskripsi perubahan"

# 4. Push ke GitHub
git push origin main
```

### Workflow dengan Branch (Advanced):

```bash
# Buat branch baru untuk fitur
git checkout -b feature/device-crud

# Kerjakan perubahan
# ... edit file ...

# Commit perubahan
git add .
git commit -m "Add Device CRUD"

# Push branch baru ke GitHub
git push origin feature/device-crud

# Kembali ke branch main
git checkout main

# Merge branch ke main
git merge feature/device-crud

# Push main
git push origin main
```

---

## 6. Contoh Workflow Lengkap

### Skenario: Menambahkan Fitur Baru

```bash
# 1. Pastikan di branch main dan up-to-date
git checkout main
git pull origin main

# 2. Buat branch baru untuk fitur
git checkout -b feature/add-device-type

# 3. Kerjakan fitur
# ... buat migration, model, controller, views ...

# 4. Cek perubahan
git status

# 5. Tambahkan semua perubahan
git add .

# 6. Commit
git commit -m "Add Device Type CRUD with migration, model, controller, and views"

# 7. Push branch ke GitHub
git push origin feature/add-device-type

# 8. (Opsional) Buat Pull Request di GitHub untuk review

# 9. Setelah selesai, merge ke main
git checkout main
git merge feature/add-device-type
git push origin main

# 10. Hapus branch lokal (opsional)
git branch -d feature/add-device-type
```

---

## 7. Troubleshooting

### Error: "fatal: not a git repository"
```bash
# Pastikan sudah di folder project yang sudah di-init
git init
```

### Error: "fatal: remote origin already exists"
```bash
# Cek remote yang ada
git remote -v

# Hapus remote lama (jika perlu)
git remote remove origin

# Tambahkan remote baru
git remote add origin https://github.com/username/repo.git
```

### Error: "failed to push some refs"
```bash
# Pull dulu sebelum push
git pull origin main --rebase

# Lalu push lagi
git push origin main
```

### Undo Last Commit (belum di-push)
```bash
# Batalkan commit terakhir, tapi tetap simpan perubahan
git reset --soft HEAD~1

# Atau batalkan commit dan perubahan
git reset --hard HEAD~1
```

### Undo Last Commit (sudah di-push)
```bash
# Buat commit baru yang membatalkan commit sebelumnya
git revert HEAD
git push origin main
```

---

## 8. Best Practices

1. **Commit Sering**: Commit setiap kali fitur kecil selesai
2. **Pesan Commit Jelas**: Gunakan pesan yang deskriptif
3. **Pull Sebelum Push**: Selalu pull sebelum push untuk menghindari conflict
4. **Gunakan Branch**: Untuk fitur besar, gunakan branch terpisah
5. **Jangan Commit File Sensitif**: Jangan commit `.env` dengan password
6. **Gunakan .gitignore**: Abaikan file yang tidak perlu di-version control

---

## Selesai!

Setelah Git & GitHub setup, lanjut ke: **praktek_codeigniter4.md**

