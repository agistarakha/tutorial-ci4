# Tutorial CodeIgniter 4 - Coding di Era Modern

📚 **Buku Panduan Lengkap** untuk belajar pengembangan web modern dengan CodeIgniter 4, Git & GitHub, dan AI Coding Assistant.

## 📋 Tentang Tutorial Ini

Tutorial ini dibuat untuk mempelajari pengembangan web modern dengan fokus pada:
- **CodeIgniter 4** - PHP Framework MVC yang ringan dan mudah dipelajari
- **Git & GitHub** - Version control dan kolaborasi
- **AI Coding Assistant** - Penggunaan AI untuk mempercepat development
- **API Development** - Membuat dan mengkonsumsi API
- **Best Practices** - Praktik terbaik dalam pengembangan web

## 🎯 Apa yang Akan Dipelajari?

1. ✅ Setup environment development (Git, VS Code, XAMPP/Laragon)
2. ✅ Git & GitHub workflow dengan Personal Access Token
3. ✅ Setup dan konfigurasi CodeIgniter 4
4. ✅ CRUD lengkap dengan relasi database (One-to-Many)
5. ✅ Membuat dan mengkonsumsi API
6. ✅ Penggunaan AI untuk mempercepat development

## 📖 Daftar Isi

### 1. [Setup Tools dan Environment](praktek_setup.md)
   - Install Git, VS Code, XAMPP/Laragon
   - Setup Composer
   - Buat akun GitHub
   - Verifikasi instalasi

### 2. [Git & GitHub Workflow](praktek_git_github.md)
   - Setup Personal Access Token
   - Init Git di local project
   - Buat remote repository di GitHub
   - Connect local ke remote dengan PAT
   - Git commands dasar (add, commit, push, pull)
   - Workflow Git untuk development

### 3. [Setup CodeIgniter 4](praktek_codeigniter4.md)
   - Install CodeIgniter 4 via Composer
   - Konfigurasi database dan environment (.env)
   - Struktur folder CodeIgniter 4
   - Testing instalasi

### 4. [CRUD dengan Relasi Database](praktek_crud.md)
   - Setup Layout Template
   - Buat Home Controller dan View
   - Device Type CRUD (Master Data)
   - Device CRUD dengan relasi ke Device Type
   - Migration, Model, Controller, Views lengkap
   - Penjelasan relasi database (One-to-Many)

### 5. [Membuat dan Mengkonsumsi API](praktek_api.md)
   - Setup API Controller di CodeIgniter 4
   - Membuat endpoint GET, POST, PUT, DELETE
   - Response format JSON
   - Testing dengan Postman/curl
   - Mengkonsumsi API lokal dan publik

### 6. [Penggunaan AI untuk Development](praktek_ai.md)
   - Struktur tabel dengan relasi
   - Prompt AI yang efektif untuk generate code
   - Contoh prompt untuk Migration, Model, Controller, Views
   - Best practices menggunakan AI tools

## 🚀 Quick Start

### Prerequisites
- Windows OS
- Internet connection
- Akun GitHub

### Langkah Awal
1. Ikuti [Setup Tools dan Environment](praktek_setup.md) untuk install semua tools yang diperlukan
2. Lanjut ke [Git & GitHub Workflow](praktek_git_github.md) untuk setup Git
3. Setup project dengan [Setup CodeIgniter 4](praktek_codeigniter4.md)
4. Mulai coding dengan [CRUD dengan Relasi Database](praktek_crud.md)

## 📁 Struktur Project

Setelah mengikuti tutorial, project Anda akan memiliki struktur:

```
device-management/
├── app/
│   ├── Controllers/
│   │   ├── HomeController.php
│   │   ├── DeviceTypeController.php
│   │   ├── DeviceController.php
│   │   └── Api/
│   │       ├── DeviceApiController.php
│   │       └── DeviceTypeApiController.php
│   ├── Models/
│   │   ├── DeviceTypeModel.php
│   │   └── DeviceModel.php
│   ├── Views/
│   │   ├── layout.php
│   │   ├── home/
│   │   ├── device_types/
│   │   └── devices/
│   └── Database/
│       └── Migrations/
├── public/
├── writable/
└── .gitignore
```

## 🛠️ Tech Stack

- **Backend**: PHP 8.1+, CodeIgniter 4
- **Database**: MySQL/MariaDB
- **Frontend**: Bootstrap 5 (CDN)
- **Version Control**: Git, GitHub
- **Tools**: VS Code, Composer, XAMPP/Laragon
- **AI Tools**: Cursor, ChatGPT, Gemini, Claude

## 📝 Catatan Penting

- Semua code snippets lengkap dan bisa langsung copy-paste
- Mengikuti best practices CodeIgniter 4
- Include error handling dan validation
- Menggunakan layout template untuk konsistensi UI
- Personal Access Token untuk autentikasi GitHub

## 🤝 Kontribusi

Jika menemukan kesalahan atau ingin menambahkan konten, silakan buat Issue atau Pull Request.

## 📄 License

Tutorial ini bebas digunakan untuk keperluan pembelajaran.

## 🔗 Link Berguna

- [CodeIgniter 4 Documentation](https://codeigniter.com/user_guide/)
- [Git Documentation](https://git-scm.com/doc)
- [GitHub Documentation](https://docs.github.com/)
- [Bootstrap 5 Documentation](https://getbootstrap.com/docs/5.3/)

## 📧 Support

Jika ada pertanyaan atau butuh bantuan, silakan buat Issue di repository ini.

---

**Selamat Belajar! 🎉**

*Tutorial ini dibuat untuk memudahkan pembelajaran pengembangan web modern dengan CodeIgniter 4.*

