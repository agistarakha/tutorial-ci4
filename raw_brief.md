# PPT KOMINFO - Coding di Era Modern

## Timeline Presentasi
**09.30 – 11.30 (120 menit)**: Coding di era modern (Tools pemrograman dengan AI, Git dan Github, Code Igniter V4, API, Upload ke real server)

---

## 1. Pengembangan Software Web Saat Ini

*Visual: Diagram software development di tengah dengan komponen-komponen yang menghubungkan: Bahasa Pemrograman (HTML, CSS, JS, PHP), Frameworks (CodeIgniter 4, Laravel, Bootstrap), Source Control (Git, Github), AI Coding Assistant (Cursor, Github Copilot, ChatGPT, Gemini, Claude)*

### 1.1 Bahasa Pemrograman

**A. Front End**
- **HTML** (HyperText Markup Language) - Struktur halaman web
- **CSS** (Cascading Style Sheets) - Styling dan layout
- **JavaScript (JS)** - Interaktivitas dan logika di browser

**B. Backend**
- **PHP** - Server-side scripting untuk web development
- **JavaScript (Node.js)** - JavaScript di server-side

### 1.2 Frameworks

**A. Front End**
- **Bootstrap** - CSS framework untuk responsive design dan komponen UI siap pakai

**B. Backend**
- **CodeIgniter 4** - PHP framework MVC yang ringan dan mudah dipelajari
- **Laravel** - PHP framework modern dengan fitur lengkap

### 1.3 Source Control

**Konsep:**
- **Git** - Version control system untuk tracking perubahan code di local
- **GitHub/GitLab** - Remote repository untuk menyimpan code di cloud
- **Workflow**: Develop di local (Git) → Push ke remote (GitHub/GitLab) → Collaborate dengan tim

**Manfaat:**
- Tracking perubahan code
- Backup code di cloud
- Kolaborasi tim
- Rollback jika ada error

### 1.4 AI Coding Assistant

**A. Chat Based** (Berbasis percakapan)
- **ChatGPT** - https://chatgpt.com
- **Gemini** - https://gemini.com
- **Claude** - https://claude.ai

**B. Agent Based** (Terintegrasi di IDE)
- **Cursor** - AI-powered code editor
- **Windsurf** - AI coding assistant
- **VS Code Copilot** - GitHub Copilot extension untuk VS Code

**Kegunaan:**
- Generate code dari natural language
- Debugging dan error fixing
- Code review dan optimization
- Dokumentasi otomatis

---

## 2. Praktek

### 2.1 Alat yang Dibutuhkan

**Tools yang perlu diinstall:**
- **Visual Studio Code** → https://code.visualstudio.com/
- **Git** → https://git-scm.com/install/
- **Buat Akun GitHub** → https://github.com/

**Pilih salah satu:**
- **XAMPP** → https://www.apachefriends.org/download.html
  - Sudah include PHP, MySQL, Apache
  - Perlu install Composer terpisah → http://getcomposer.org/
- **Laragon** → https://github.com/leokhoa/laragon/releases/tag/6.0.0
  - Sudah include PHP, MySQL, Apache, Composer
  - Lebih mudah untuk Windows

**Detail step-by-step setup:** Lihat `praktek_setup.md`

### 2.2 Git & GitHub

**Yang akan dipelajari:**
- Membuat repository di GitHub
- Clone repository ke local
- Git commands dasar (add, commit, push, pull)
- Workflow Git untuk development

**Detail step-by-step:** Lihat `praktek_git_github.md`

### 2.3 Inisialisasi Project CodeIgniter 4

**Yang akan dilakukan:**
- Install CodeIgniter 4 via Composer
- Konfigurasi database
- Setup environment (.env)
- Memahami struktur folder CodeIgniter 4

**Detail step-by-step:** Lihat `praktek_codeigniter4.md`

### 2.4 Yang Akan Kita Buat

#### A. CRUD (Create, Read, Update, Delete)

**Project: Device Management System**

**1. Device Type CRUD** (Master Data)
- Tabel: `device_types`
- Field: id, name (Server, Router, Switch, dll), description
- CRUD lengkap dengan Bootstrap UI

**2. Device CRUD** (dengan relasi ke Device Type)
- Tabel: `devices`
- Field: id, device_type_id (foreign key), name, specification, status
- Relasi: One Device Type → Many Devices
- CRUD lengkap dengan dropdown untuk memilih Device Type

**Konsep yang dipelajari:**
- Database relationship (One-to-Many)
- Foreign key
- Join query
- Form validation

**Detail step-by-step dengan code lengkap:** Lihat `praktek_crud.md`

#### B. Mengkonsumsi dan Membuat API

**1. Membuat API di CodeIgniter 4**
- Setup API Controller
- Endpoint: GET, POST, PUT, DELETE
- Response format JSON
- Testing dengan Postman/curl

**2. Mengkonsumsi API**
- Konsumsi API lokal (dari project sendiri)
- Konsumsi API publik (contoh: REST Countries API)
- Menggunakan cURL atau JavaScript fetch

**Detail step-by-step dengan code lengkap:** Lihat `praktek_api.md`

### 2.5 Penggunaan AI untuk Development

**Praktek:**
- Membuat relasi baru: **Tenant** → **Device**
- Struktur tabel Tenant
- Menggunakan AI untuk generate:
  - Migration file
  - Model dengan relasi
  - Controller CRUD
  - Views dengan Bootstrap

**Contoh prompt AI yang efektif untuk:**
- Cursor
- ChatGPT
- Gemini

**Detail step-by-step dengan contoh prompt:** Lihat `praktek_ai.md`

---

## 3. Referensi File Praktik

Untuk praktik detail dengan code snippets lengkap, lihat file-file berikut:

1. **praktek_setup.md** - Setup tools dan environment
2. **praktek_git_github.md** - Git & GitHub workflow
3. **praktek_codeigniter4.md** - Setup CodeIgniter 4
4. **praktek_crud.md** - CRUD Device Type & Device dengan relasi
5. **praktek_api.md** - Membuat dan mengkonsumsi API
6. **praktek_ai.md** - Penggunaan AI untuk development

---

## 4. Tips Presentasi

- **09.30 - 10.00**: Teori (30 menit) - Pengembangan Software Web Saat Ini
- **10.00 - 10.30**: Setup & Git (30 menit) - Tools dan Git workflow
- **10.30 - 11.00**: CodeIgniter 4 & CRUD (30 menit) - Setup project dan CRUD dasar
- **11.00 - 11.30**: API & AI (30 menit) - API dan penggunaan AI

**Sisakan waktu untuk Q&A dan troubleshooting!**
