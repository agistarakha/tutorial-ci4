# Praktek: Penggunaan AI untuk Development

## Pendahuluan

Dalam praktek ini, kita akan menggunakan AI (Cursor/ChatGPT/Gemini) untuk membuat fitur Tenant dengan relasi ke Device. Satu prompt akan menghasilkan semua yang dibutuhkan.

---

## Relasi Database

**Struktur Relasi:**
- **Tenant** (1) → **Devices** (Many)
- **Device Type** (1) → **Devices** (Many)
- Jadi: Tenant → Device → Device Type

---

## Prompt Lengkap untuk AI

Copy prompt berikut ke Cursor/ChatGPT/Gemini:

```
Buat fitur Tenant lengkap untuk Device Management System CodeIgniter 4 dengan requirements berikut:

1. MIGRATION - Buat tabel tenants:
   - id: INT, Primary Key, Auto Increment
   - name: VARCHAR(100), required
   - email: VARCHAR(100), unique, required
   - phone: VARCHAR(20), nullable
   - address: TEXT, nullable
   - status: ENUM('active', 'inactive'), default 'active'
   - created_at: DATETIME, nullable
   - updated_at: DATETIME, nullable

2. MIGRATION - Update tabel devices:
   - Tambahkan kolom tenant_id: INT, Foreign Key ke tenants.id dengan CASCADE
   - Letakkan setelah kolom device_type_id

3. MODEL - Buat TenantModel:
   - Table: tenants
   - Allowed fields: name, email, phone, address, status
   - Validation:
     * name: required, min_length 3, max_length 100
     * email: required, valid_email, is_unique
     * status: required, in_list[active,inactive]
   - Use timestamps: true
   - Method: getAllWithDeviceCount() untuk join dengan devices dan hitung jumlah devices per tenant

4. MODEL - Update DeviceModel:
   - Tambahkan tenant_id di allowedFields
   - Tambahkan validation untuk tenant_id (required, integer)
   - Update method getAllWithType() untuk juga join dengan tenants dan return tenant_name

5. CONTROLLER - Buat TenantController CRUD:
   - Methods: index, new, create, show, edit, update, delete
   - index(): Tampilkan semua tenants dengan device count
   - show($id): Tampilkan detail tenant dengan list devices milik tenant
   - Handle validation errors dengan flashdata
   - Return views dengan data yang diperlukan

6. VIEWS - Buat views untuk Tenant (gunakan layout yang sudah ada di app/Views/layout.php):
   - index.php: Tabel dengan kolom ID, Name, Email, Phone, Status, Device Count, Actions
   - create.php: Form dengan field name, email, phone, address, status (dropdown)
   - edit.php: Form sama seperti create dengan data existing
   - show.php: Card detail tenant + tabel devices milik tenant
   - Gunakan extend('layout') dan section('content')
   - Tampilkan flashdata untuk success/error messages

7. VIEWS - Update form Device (create.php dan edit.php):
   - Tambahkan dropdown untuk memilih tenant
   - Load tenants dari TenantModel di controller
   - Tampilkan di form sebagai select dengan label "Tenant"
   - Letakkan setelah dropdown Device Type

8. ROUTES - Tambahkan routes untuk Tenant:
   - GET /tenants → TenantController::index
   - GET /tenants/new → TenantController::new
   - POST /tenants/create → TenantController::create
   - GET /tenants/{id} → TenantController::show
   - GET /tenants/{id}/edit → TenantController::edit
   - POST /tenants/{id}/update → TenantController::update
   - GET /tenants/{id}/delete → TenantController::delete

Gunakan format CodeIgniter 4 standar dengan namespace yang benar.
Mirip dengan struktur DeviceController dan DeviceModel yang sudah ada di project.
```

---

## Langkah-langkah Setelah Generate Code

### 1. Review Code yang Dihasilkan

Pastikan semua file sudah dibuat:
- ✅ Migration: `CreateTenantsTable.php`
- ✅ Migration: `AddTenantIdToDevices.php`
- ✅ Model: `TenantModel.php`
- ✅ Model: `DeviceModel.php` (updated)
- ✅ Controller: `TenantController.php`
- ✅ Views: `tenants/index.php`, `create.php`, `edit.php`, `show.php`
- ✅ Views: `devices/create.php`, `edit.php` (updated)
- ✅ Routes: sudah ditambahkan di `Routes.php`

### 2. Run Migration

```bash
php spark migrate
```

### 3. Test Fitur

1. **Test Tenant CRUD:**
   - Buka: http://localhost/device-management/public/tenants
   - Tambah beberapa tenant
   - Test Edit dan Delete

2. **Test Device dengan Tenant:**
   - Buka: http://localhost/device-management/public/devices/new
   - Pastikan dropdown Tenant muncul
   - Tambah device dengan memilih tenant
   - Test Edit device

3. **Test Relasi:**
   - Buka detail tenant
   - Pastikan devices milik tenant muncul di halaman detail

---

## Tips Menggunakan Prompt AI

### Struktur Prompt yang Efektif:

1. **Spesifik**: Sebutkan teknologi dan versi
   - ✅ "CodeIgniter 4"
   - ❌ "Framework PHP"

2. **Detail Requirements**: Sebutkan semua field dan validasi
   - ✅ "name: VARCHAR(100), required, min_length 3"
   - ❌ "name field"

3. **Context**: Berikan context tentang project
   - ✅ "Mirip dengan DeviceController yang sudah ada"
   - ❌ "Buat controller"

4. **Urutan**: Urutkan dari database → model → controller → view
   - Lebih mudah untuk AI memahami alur

5. **Format Output**: Sebutkan format yang diinginkan
   - ✅ "Gunakan extend('layout') dan section('content')"
   - ❌ "Gunakan layout"

---

## Troubleshooting

### Jika AI tidak menghasilkan semua file:
- **Solusi**: Copy prompt lagi dan tambahkan "Lengkapi semua file yang belum dibuat"

### Jika ada error setelah generate:
- **Solusi**: Copy error message dan minta AI untuk fix dengan prompt:
  ```
  Fix error berikut di CodeIgniter 4:
  [paste error message]
  ```

### Jika relasi tidak bekerja:
- **Solusi**: Pastikan migration sudah di-run dengan `php spark migrate`
- Cek foreign key constraint di database

---

## Best Practices

1. **Review Code**: Jangan langsung copy-paste, review dulu
2. **Test Selalu**: Test setiap fitur setelah generate
3. **Understand**: Pahami code yang di-generate
4. **Iterate**: Jika kurang, buat prompt lanjutan
5. **Version Control**: Commit code yang sudah di-review dan tested

---

## Selesai!

Setelah selesai, Anda sudah memiliki:
- ✅ Tabel tenants dengan relasi ke devices
- ✅ CRUD Tenant lengkap
- ✅ Form Device yang bisa memilih tenant
- ✅ Relasi database yang bekerja dengan baik

**Selamat coding dengan AI! 🚀**
