# Praktek: Penggunaan AI untuk Development

## 1. Struktur Tabel Tenant

### Relasi Database
- **Tenant** (1) → **Devices** (Many)
- Satu Tenant bisa punya banyak Devices
- Device sudah punya relasi ke Device Type
- Jadi struktur: Tenant → Device → Device Type

### Struktur Tabel `tenants`

**Field:**
- `id` - INT, Primary Key, Auto Increment
- `name` - VARCHAR(100) - Nama tenant/perusahaan
- `email` - VARCHAR(100) - Email tenant
- `phone` - VARCHAR(20) - Nomor telepon
- `address` - TEXT - Alamat tenant
- `status` - ENUM('active', 'inactive') - Status tenant
- `created_at` - DATETIME
- `updated_at` - DATETIME

### Update Tabel `devices`

Tambahkan field `tenant_id` sebagai foreign key ke tabel `tenants`:
- `tenant_id` - INT, Foreign Key ke `tenants.id`

---

## 2. Prompt AI untuk Generate Migration

### Prompt untuk Cursor/ChatGPT/Gemini:

```
Buat migration CodeIgniter 4 untuk tabel tenants dengan struktur berikut:
- id: INT, Primary Key, Auto Increment
- name: VARCHAR(100), required
- email: VARCHAR(100), unique, required
- phone: VARCHAR(20), nullable
- address: TEXT, nullable
- status: ENUM('active', 'inactive'), default 'active'
- created_at: DATETIME, nullable
- updated_at: DATETIME, nullable

Gunakan format CodeIgniter 4 Migration dengan namespace App\Database\Migrations.
```

### Prompt untuk Update Migration Devices:

```
Buat migration CodeIgniter 4 untuk menambahkan kolom tenant_id ke tabel devices:
- tenant_id: INT, Foreign Key ke tenants.id dengan CASCADE
- Setelah kolom device_type_id

Gunakan format CodeIgniter 4 Migration dengan namespace App\Database\Migrations.
```

### Contoh Output yang Diharapkan:

**File:** `app/Database/Migrations/YYYY-MM-DD-HHMMSS_CreateTenantsTable.php`

```php
<?php

namespace App\Database\Migrations;

use CodeIgniter\Database\Migration;

class CreateTenantsTable extends Migration
{
    public function up()
    {
        $this->forge->addField([
            'id' => [
                'type'           => 'INT',
                'constraint'     => 11,
                'unsigned'       => true,
                'auto_increment' => true,
            ],
            'name' => [
                'type'       => 'VARCHAR',
                'constraint' => '100',
            ],
            'email' => [
                'type'       => 'VARCHAR',
                'constraint' => '100',
                'unique'     => true,
            ],
            'phone' => [
                'type'       => 'VARCHAR',
                'constraint' => '20',
                'null'       => true,
            ],
            'address' => [
                'type' => 'TEXT',
                'null' => true,
            ],
            'status' => [
                'type'       => 'ENUM',
                'constraint' => ['active', 'inactive'],
                'default'    => 'active',
            ],
            'created_at' => [
                'type' => 'DATETIME',
                'null' => true,
            ],
            'updated_at' => [
                'type' => 'DATETIME',
                'null' => true,
            ],
        ]);
        $this->forge->addKey('id', true);
        $this->forge->addUniqueKey('email');
        $this->forge->createTable('tenants');
    }

    public function down()
    {
        $this->forge->dropTable('tenants');
    }
}
```

---

## 3. Prompt AI untuk Generate Model dengan Relasi

### Prompt untuk Cursor/ChatGPT/Gemini:

```
Buat Model CodeIgniter 4 untuk Tenant dengan:
- Nama class: TenantModel
- Table: tenants
- Allowed fields: name, email, phone, address, status
- Validation rules:
  - name: required, min_length 3, max_length 100
  - email: required, valid_email, is_unique
  - status: required, in_list[active,inactive]
- Use timestamps: true
- Tambahkan method getAllWithDevices() untuk mengambil tenant beserta devices-nya menggunakan join

Gunakan namespace App\Models dan extend CodeIgniter\Model.
```

### Contoh Output yang Diharapkan:

**File:** `app/Models/TenantModel.php`

```php
<?php

namespace App\Models;

use CodeIgniter\Model;

class TenantModel extends Model
{
    protected $table            = 'tenants';
    protected $primaryKey       = 'id';
    protected $useAutoIncrement = true;
    protected $returnType       = 'array';
    protected $useSoftDeletes   = false;
    protected $protectFields    = true;
    protected $allowedFields    = ['name', 'email', 'phone', 'address', 'status'];

    // Dates
    protected $useTimestamps = true;
    protected $dateFormat    = 'datetime';
    protected $createdField  = 'created_at';
    protected $updatedField  = 'updated_at';

    // Validation
    protected $validationRules      = [
        'name' => 'required|min_length[3]|max_length[100]',
        'email' => 'required|valid_email|is_unique[tenants.email,id,{id}]',
        'status' => 'required|in_list[active,inactive]',
    ];
    protected $validationMessages   = [
        'name' => [
            'required' => 'Nama tenant wajib diisi',
            'min_length' => 'Nama minimal 3 karakter',
            'max_length' => 'Nama maksimal 100 karakter',
        ],
        'email' => [
            'required' => 'Email wajib diisi',
            'valid_email' => 'Format email tidak valid',
            'is_unique' => 'Email sudah digunakan',
        ],
        'status' => [
            'required' => 'Status wajib dipilih',
            'in_list' => 'Status tidak valid',
        ],
    ];
    protected $skipValidation       = false;
    protected $cleanValidationRules = true;

    // Callbacks
    protected $allowCallbacks = true;
    protected $beforeInsert   = [];
    protected $afterInsert    = [];
    protected $beforeUpdate   = [];
    protected $afterUpdate    = [];
    protected $beforeFind     = [];
    protected $afterFind      = [];
    protected $beforeDelete   = [];
    protected $afterDelete    = [];

    /**
     * Ambil semua tenants dengan jumlah devices
     */
    public function getAllWithDeviceCount()
    {
        return $this->select('tenants.*, COUNT(devices.id) as device_count')
                    ->join('devices', 'devices.tenant_id = tenants.id', 'left')
                    ->groupBy('tenants.id')
                    ->findAll();
    }

    /**
     * Ambil tenant dengan devices-nya
     */
    public function getWithDevices($id)
    {
        $tenant = $this->find($id);
        if (!$tenant) {
            return null;
        }

        $deviceModel = new \App\Models\DeviceModel();
        $tenant['devices'] = $deviceModel->where('tenant_id', $id)->getAllWithType();

        return $tenant;
    }
}
```

---

## 4. Prompt AI untuk Generate Controller CRUD

### Prompt untuk Cursor/ChatGPT/Gemini:

```
Buat Controller CodeIgniter 4 untuk Tenant CRUD dengan:
- Nama class: TenantController
- Extend ResourceController atau BaseController
- Methods:
  - index(): Tampilkan semua tenants
  - new(): Tampilkan form create
  - create(): Simpan tenant baru
  - show($id): Tampilkan detail tenant dengan devices-nya
  - edit($id): Tampilkan form edit
  - update($id): Update tenant
  - delete($id): Hapus tenant
- Gunakan TenantModel
- Return view dengan data yang diperlukan
- Handle validation errors dan success messages
- Gunakan flashdata untuk pesan

Gunakan namespace App\Controllers.
```

### Contoh Output yang Diharapkan:

**File:** `app/Controllers/TenantController.php`

```php
<?php

namespace App\Controllers;

use App\Models\TenantModel;
use CodeIgniter\RESTful\ResourceController;

class TenantController extends ResourceController
{
    protected $modelName = TenantModel::class;
    protected $format    = 'json';

    /**
     * Menampilkan semua tenants
     */
    public function index()
    {
        $model = new TenantModel();
        $data = [
            'title' => 'Tenants',
            'tenants' => $model->getAllWithDeviceCount(),
        ];
        return view('tenants/index', $data);
    }

    /**
     * Menampilkan form create
     */
    public function new()
    {
        $data = [
            'title' => 'Tambah Tenant',
        ];
        return view('tenants/create', $data);
    }

    /**
     * Menyimpan tenant baru
     */
    public function create()
    {
        $model = new TenantModel();
        
        $data = [
            'name' => $this->request->getPost('name'),
            'email' => $this->request->getPost('email'),
            'phone' => $this->request->getPost('phone'),
            'address' => $this->request->getPost('address'),
            'status' => $this->request->getPost('status'),
        ];

        if (!$model->insert($data)) {
            return redirect()->back()
                ->with('errors', $model->errors())
                ->withInput();
        }

        return redirect()->to('/tenants')
            ->with('success', 'Tenant berhasil ditambahkan');
    }

    /**
     * Menampilkan detail tenant
     */
    public function show($id = null)
    {
        $model = new TenantModel();
        $tenant = $model->getWithDevices($id);
        
        if (!$tenant) {
            throw \CodeIgniter\Exceptions\PageNotFoundException::forPageNotFound();
        }

        $data = [
            'title' => 'Detail Tenant',
            'tenant' => $tenant,
        ];
        return view('tenants/show', $data);
    }

    /**
     * Menampilkan form edit
     */
    public function edit($id = null)
    {
        $model = new TenantModel();
        $tenant = $model->find($id);
        
        if (!$tenant) {
            throw \CodeIgniter\Exceptions\PageNotFoundException::forPageNotFound();
        }

        $data = [
            'title' => 'Edit Tenant',
            'tenant' => $tenant,
        ];
        return view('tenants/edit', $data);
    }

    /**
     * Update tenant
     */
    public function update($id = null)
    {
        $model = new TenantModel();
        $tenant = $model->find($id);
        
        if (!$tenant) {
            throw \CodeIgniter\Exceptions\PageNotFoundException::forPageNotFound();
        }

        $data = [
            'name' => $this->request->getPost('name'),
            'email' => $this->request->getPost('email'),
            'phone' => $this->request->getPost('phone'),
            'address' => $this->request->getPost('address'),
            'status' => $this->request->getPost('status'),
        ];

        if (!$model->update($id, $data)) {
            return redirect()->back()
                ->with('errors', $model->errors())
                ->withInput();
        }

        return redirect()->to('/tenants')
            ->with('success', 'Tenant berhasil diupdate');
    }

    /**
     * Hapus tenant
     */
    public function delete($id = null)
    {
        $model = new TenantModel();
        $tenant = $model->find($id);
        
        if (!$tenant) {
            throw \CodeIgniter\Exceptions\PageNotFoundException::forPageNotFound();
        }

        $model->delete($id);
        return redirect()->to('/tenants')
            ->with('success', 'Tenant berhasil dihapus');
    }
}
```

---

## 5. Prompt AI untuk Generate Views dengan Bootstrap

### Prompt untuk Cursor/ChatGPT/Gemini:

```
Buat View CodeIgniter 4 untuk Tenant CRUD dengan Bootstrap 5 CDN:
1. Index view: Tabel dengan kolom ID, Name, Email, Phone, Status, Device Count, Actions
2. Create view: Form dengan field name, email, phone, address, status (dropdown active/inactive)
3. Edit view: Form sama seperti create tapi dengan data existing
4. Show view: Card dengan detail tenant dan tabel devices milik tenant

Gunakan Bootstrap 5 CDN untuk styling.
Tampilkan flashdata untuk success/error messages.
Gunakan form validation untuk menampilkan errors.
```

### Contoh Output yang Diharapkan:

#### View: Index

**File:** `app/Views/tenants/index.php`

```php
<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title><?= $title ?></title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
    <div class="container mt-5">
        <div class="row">
            <div class="col-md-12">
                <h1 class="mb-4"><?= $title ?></h1>
                
                <?php if (session()->getFlashdata('success')): ?>
                    <div class="alert alert-success alert-dismissible fade show" role="alert">
                        <?= session()->getFlashdata('success') ?>
                        <button type="button" class="btn-close" data-bs-dismiss="alert"></button>
                    </div>
                <?php endif; ?>

                <a href="<?= base_url('tenants/new') ?>" class="btn btn-primary mb-3">Tambah Tenant</a>

                <table class="table table-striped">
                    <thead>
                        <tr>
                            <th>ID</th>
                            <th>Nama</th>
                            <th>Email</th>
                            <th>Phone</th>
                            <th>Status</th>
                            <th>Jumlah Device</th>
                            <th>Aksi</th>
                        </tr>
                    </thead>
                    <tbody>
                        <?php if (empty($tenants)): ?>
                            <tr>
                                <td colspan="7" class="text-center">Tidak ada data</td>
                            </tr>
                        <?php else: ?>
                            <?php foreach ($tenants as $tenant): ?>
                                <tr>
                                    <td><?= $tenant['id'] ?></td>
                                    <td><?= esc($tenant['name']) ?></td>
                                    <td><?= esc($tenant['email']) ?></td>
                                    <td><?= esc($tenant['phone'] ?? '-') ?></td>
                                    <td>
                                        <span class="badge <?= $tenant['status'] == 'active' ? 'bg-success' : 'bg-secondary' ?>">
                                            <?= ucfirst($tenant['status']) ?>
                                        </span>
                                    </td>
                                    <td><?= $tenant['device_count'] ?? 0 ?></td>
                                    <td>
                                        <a href="<?= base_url('tenants/' . $tenant['id']) ?>" class="btn btn-sm btn-info">Detail</a>
                                        <a href="<?= base_url('tenants/' . $tenant['id'] . '/edit') ?>" class="btn btn-sm btn-warning">Edit</a>
                                        <a href="<?= base_url('tenants/' . $tenant['id'] . '/delete') ?>" class="btn btn-sm btn-danger" onclick="return confirm('Yakin hapus?')">Hapus</a>
                                    </td>
                                </tr>
                            <?php endforeach; ?>
                        <?php endif; ?>
                    </tbody>
                </table>
            </div>
        </div>
    </div>
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```

---

## 6. Tips Prompt AI yang Efektif

### Struktur Prompt yang Baik:

1. **Spesifik**: Sebutkan teknologi, framework, versi
   - ❌ "Buat CRUD"
   - ✅ "Buat CRUD CodeIgniter 4 untuk tabel tenants"

2. **Detail Requirements**: Sebutkan field, validasi, relasi
   - ✅ "Dengan field name, email, phone. Email harus unique. Ada relasi one-to-many ke devices."

3. **Format Output**: Sebutkan format yang diinginkan
   - ✅ "Gunakan namespace App\Models, extend CodeIgniter\Model"

4. **Context**: Berikan context tentang project
   - ✅ "Ini bagian dari Device Management System. Sudah ada DeviceModel dan DeviceTypeModel."

5. **Contoh**: Berikan contoh jika perlu
   - ✅ "Mirip dengan DeviceController yang sudah ada"

### Contoh Prompt Lengkap:

```
Buat CRUD lengkap CodeIgniter 4 untuk Tenant dengan requirements berikut:

1. Migration:
   - Tabel: tenants
   - Field: id, name (VARCHAR 100), email (VARCHAR 100 unique), phone (VARCHAR 20 nullable), address (TEXT nullable), status (ENUM active/inactive), timestamps

2. Model:
   - Nama: TenantModel
   - Validation: name required min 3, email required valid_email unique, status required
   - Method: getAllWithDeviceCount() untuk join dengan devices

3. Controller:
   - Nama: TenantController
   - Methods: index, new, create, show, edit, update, delete
   - Handle validation errors dengan flashdata

4. Views (Bootstrap 5):
   - Index: tabel dengan kolom ID, Name, Email, Phone, Status, Device Count, Actions
   - Create/Edit: form dengan semua field
   - Show: detail tenant + tabel devices milik tenant

Gunakan format CodeIgniter 4 standar dengan namespace yang benar.
Mirip dengan struktur DeviceController dan DeviceModel yang sudah ada.
```

---

## 7. Update Device Model untuk Relasi Tenant

### Prompt untuk Update DeviceModel:

```
Update DeviceModel untuk menambahkan relasi ke Tenant:
- Tambahkan tenant_id di allowedFields
- Tambahkan validation untuk tenant_id (required, integer)
- Update method getAllWithType() menjadi getAllWithTypeAndTenant() yang join dengan device_types dan tenants
- Return data dengan device_type_name dan tenant_name
```

### Update Device Migration:

**File:** `app/Database/Migrations/YYYY-MM-DD-HHMMSS_AddTenantIdToDevices.php`

```php
<?php

namespace App\Database\Migrations;

use CodeIgniter\Database\Migration;

class AddTenantIdToDevices extends Migration
{
    public function up()
    {
        $fields = [
            'tenant_id' => [
                'type'       => 'INT',
                'constraint' => 11,
                'unsigned'   => true,
                'after'      => 'device_type_id',
            ],
        ];
        $this->forge->addColumn('devices', $fields);
        $this->forge->addForeignKey('tenant_id', 'tenants', 'id', 'CASCADE', 'CASCADE');
    }

    public function down()
    {
        $this->forge->dropForeignKey('devices', 'devices_tenant_id_foreign');
        $this->forge->dropColumn('devices', 'tenant_id');
    }
}
```

---

## 8. Workflow Menggunakan AI

### Langkah-langkah:

1. **Rencanakan Struktur**
   - Tentukan tabel, field, relasi
   - Buat daftar requirements

2. **Buat Prompt**
   - Gunakan struktur prompt yang efektif
   - Spesifik dan detail

3. **Generate Code dengan AI**
   - Copy prompt ke Cursor/ChatGPT/Gemini
   - Review code yang dihasilkan

4. **Review & Test**
   - Cek syntax dan logic
   - Test di local environment
   - Fix jika ada error

5. **Iterate**
   - Jika ada yang kurang, buat prompt lanjutan
   - Atau edit manual jika perlu

### Contoh Workflow Lengkap:

```
1. Prompt: "Buat migration untuk tabel tenants..."
   → Dapat migration file

2. Prompt: "Buat model TenantModel dengan relasi..."
   → Dapat model file

3. Prompt: "Buat controller TenantController CRUD..."
   → Dapat controller file

4. Prompt: "Buat views Bootstrap 5 untuk tenant..."
   → Dapat view files

5. Test & Fix manual jika perlu
```

---

## 9. Tools AI yang Direkomendasikan

### 1. Cursor (Agent Based)
- **Kelebihan**: Terintegrasi di editor, context-aware
- **Cara Pakai**: Select code → Ctrl+K → Tulis prompt
- **Best For**: Generate code langsung di file

### 2. ChatGPT (Chat Based)
- **Kelebihan**: Conversational, bisa diskusi
- **Cara Pakai**: Chat di web/app
- **Best For**: Planning, brainstorming, complex logic

### 3. GitHub Copilot (Agent Based)
- **Kelebihan**: Autocomplete cerdas
- **Cara Pakai**: Install extension di VS Code
- **Best For**: Quick code completion

### 4. Claude (Chat Based)
- **Kelebihan**: Good at long context
- **Cara Pakai**: Chat di web
- **Best For**: Long code generation, documentation

---

## 10. Best Practices Menggunakan AI

1. **Jangan Langsung Copy-Paste**: Review code dulu
2. **Test Selalu**: Jangan percaya 100%, test dulu
3. **Understand the Code**: Pahami code yang di-generate
4. **Iterate**: Jika kurang, minta perbaikan
5. **Combine**: Gabungkan AI dengan pengetahuan manual
6. **Security**: Jangan share sensitive data ke AI
7. **Version Control**: Commit code yang sudah di-review

---

## Selesai!

Setelah menggunakan AI untuk generate Tenant CRUD, Anda sudah memahami:
- Cara membuat prompt yang efektif
- Workflow menggunakan AI untuk development
- Struktur relasi database yang kompleks
- Best practices menggunakan AI tools

**Selamat coding dengan AI! 🚀**

