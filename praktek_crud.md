# Praktek: CRUD Device Type & Device dengan Relasi

## Konsep Database Relationship

**One-to-Many Relationship:**
- 1 Device Type → Many Devices
- Contoh: Device Type "Server" bisa punya banyak Device (Server 1, Server 2, dll)

---

## Spark CLI Commands untuk CRUD

CodeIgniter 4 menyediakan Spark CLI untuk membuat file-file CRUD dengan cepat:

```bash
# Buat Migration
php spark make:migration CreateDeviceTypesTable

# Buat Model
php spark make:model DeviceTypeModel

# Buat Controller
php spark make:controller DeviceTypeController

# Run Migration
php spark migrate

# Rollback Migration
php spark migrate:rollback
```

**Catatan:** Setelah file dibuat dengan Spark, kita perlu mengedit isinya sesuai kebutuhan.

---

## 1. Setup Layout dan Home

### 1.1 Buat Layout Template

Sebelum membuat views, kita buat layout template dulu agar tidak perlu menulis HTML berulang-ulang.

#### Langkah 1: Buat Folder Layout (jika belum ada)

```bash
# Folder akan dibuat otomatis saat membuat file
```

#### Langkah 2: Buat Layout Template

**File:** `app/Views/layout.php`

Buat file baru di `app/Views/layout.php` dengan isi:

```php
<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title><?= isset($title) ? esc($title) . ' - Device Management' : 'Device Management' ?></title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
    <?= $this->renderSection('styles') ?>
</head>
<body>
    <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
        <div class="container">
            <a class="navbar-brand" href="<?= base_url() ?>">Device Management</a>
            <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNav">
                <span class="navbar-toggler-icon"></span>
            </button>
            <div class="collapse navbar-collapse" id="navbarNav">
                <ul class="navbar-nav ms-auto">
                    <li class="nav-item">
                        <a class="nav-link" href="<?= base_url() ?>">Home</a>
                    </li>
                    <li class="nav-item">
                        <a class="nav-link" href="<?= base_url('device-types') ?>">Device Types</a>
                    </li>
                    <li class="nav-item">
                        <a class="nav-link" href="<?= base_url('devices') ?>">Devices</a>
                    </li>
                </ul>
            </div>
        </div>
    </nav>

    <div class="container mt-4">
        <?php if (session()->getFlashdata('success')): ?>
            <div class="alert alert-success alert-dismissible fade show" role="alert">
                <?= session()->getFlashdata('success') ?>
                <button type="button" class="btn-close" data-bs-dismiss="alert"></button>
            </div>
        <?php endif; ?>

        <?php if (session()->getFlashdata('errors')): ?>
            <div class="alert alert-danger alert-dismissible fade show" role="alert">
                <ul class="mb-0">
                    <?php foreach (session()->getFlashdata('errors') as $error): ?>
                        <li><?= $error ?></li>
                    <?php endforeach; ?>
                </ul>
                <button type="button" class="btn-close" data-bs-dismiss="alert"></button>
            </div>
        <?php endif; ?>

        <?= $this->renderSection('content') ?>
    </div>

    <footer class="mt-5 py-4 bg-light">
        <div class="container text-center">
            <p class="text-muted mb-0">&copy; <?= date('Y') ?> Device Management System</p>
        </div>
    </footer>

    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
    <?= $this->renderSection('scripts') ?>
</body>
</html>
```

**Penjelasan:**
- `renderSection('content')`: Tempat konten utama dari setiap view
- `renderSection('styles')`: Tempat CSS tambahan (opsional)
- `renderSection('scripts')`: Tempat JavaScript tambahan (opsional)
- Flash messages sudah di-handle di layout, tidak perlu ditulis ulang di setiap view

### 1.2 Buat Home Controller dan View

#### Langkah 1: Buat Home Controller dengan Spark

```bash
php spark make:controller HomeController
```

**Output yang muncul:**
```
Created file: app/Controllers/HomeController.php
```

#### Langkah 2: Edit Home Controller

**File:** `app/Controllers/HomeController.php`

Buka file yang baru dibuat dan ganti isinya dengan:

```php
<?php

namespace App\Controllers;

class HomeController extends BaseController
{
    public function index()
    {
        $data = [
            'title' => 'Home',
        ];
        return view('home/index', $data);
    }
}
```

#### Langkah 3: Buat Home View

**File:** `app/Views/home/index.php`

Buat folder `home` di `app/Views/` jika belum ada, lalu buat file `index.php`:

```php
<?= $this->extend('layout') ?>

<?= $this->section('content') ?>
<div class="row">
    <div class="col-md-12">
        <div class="jumbotron bg-light p-5 rounded mb-4">
            <h1 class="display-4">Selamat Datang</h1>
            <p class="lead">Device Management System</p>
            <hr class="my-4">
            <p>Sistem manajemen device untuk mengelola device types dan devices dengan relasi database.</p>
        </div>

        <div class="row">
            <div class="col-md-6 mb-4">
                <div class="card">
                    <div class="card-body">
                        <h5 class="card-title">Device Types</h5>
                        <p class="card-text">Kelola tipe-tipe device seperti Server, Router, Switch, dll.</p>
                        <a href="<?= base_url('device-types') ?>" class="btn btn-primary">Kelola Device Types</a>
                    </div>
                </div>
            </div>
            <div class="col-md-6 mb-4">
                <div class="card">
                    <div class="card-body">
                        <h5 class="card-title">Devices</h5>
                        <p class="card-text">Kelola devices dengan relasi ke device types.</p>
                        <a href="<?= base_url('devices') ?>" class="btn btn-primary">Kelola Devices</a>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>
<?= $this->endSection() ?>
```

### 1.3 Setup Routes untuk Home

**File:** `app/Config/Routes.php`

Tambahkan di dalam function `setRoutes()`:

```php
// Home Route (Root)
$routes->get('/', 'HomeController::index');
```

---

## 2. Device Type CRUD (Master Data)

### 2.1 Buat Migration untuk Tabel `device_types`

#### Langkah 1: Buat Migration File dengan Spark

Buka Command Prompt atau PowerShell di folder project, lalu jalankan:

```bash
cd C:\xampp\htdocs\device-management
# atau
cd C:\laragon\www\device-management

# Buat migration file
php spark make:migration CreateDeviceTypesTable
```

**Output yang muncul:**
```
Created file: app/Database/Migrations/YYYY-MM-DD-HHMMSS_CreateDeviceTypesTable.php
```

**Catatan:** `YYYY-MM-DD-HHMMSS` akan diganti dengan timestamp saat ini.

#### Langkah 2: Edit File Migration

Buka file yang baru dibuat di `app/Database/Migrations/YYYY-MM-DD-HHMMSS_CreateDeviceTypesTable.php` dan ganti isinya dengan:

```php
<?php

namespace App\Database\Migrations;

use CodeIgniter\Database\Migration;

class CreateDeviceTypesTable extends Migration
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
            'description' => [
                'type' => 'TEXT',
                'null' => true,
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
        $this->forge->createTable('device_types');
    }

    public function down()
    {
        $this->forge->dropTable('device_types');
    }
}
```

**Penjelasan:**
- `up()`: Method yang dijalankan saat migration di-run
- `down()`: Method yang dijalankan saat migration di-rollback
- `addField()`: Menambahkan kolom ke tabel
- `addKey()`: Menambahkan primary key
- `createTable()`: Membuat tabel di database

### 2.2 Run Migration

```bash
php spark migrate
```

### 2.3 Buat Model `DeviceTypeModel`

#### Langkah 1: Buat Model File dengan Spark

```bash
php spark make:model DeviceTypeModel
```

**Output yang muncul:**
```
Created file: app/Models/DeviceTypeModel.php
```

#### Langkah 2: Edit File Model

Buka file yang baru dibuat di `app/Models/DeviceTypeModel.php` dan ganti isinya dengan:

```php
<?php

namespace App\Models;

use CodeIgniter\Model;

class DeviceTypeModel extends Model
{
    protected $table            = 'device_types';
    protected $primaryKey       = 'id';
    protected $useAutoIncrement = true;
    protected $returnType       = 'array';
    protected $useSoftDeletes   = false;
    protected $protectFields    = true;
    protected $allowedFields    = ['name', 'description'];

    // Dates
    protected $useTimestamps = true;
    protected $dateFormat    = 'datetime';
    protected $createdField  = 'created_at';
    protected $updatedField  = 'updated_at';

    // Validation
    protected $validationRules      = [
        'name' => 'required|min_length[3]|max_length[100]',
    ];
    protected $validationMessages   = [
        'name' => [
            'required' => 'Nama device type wajib diisi',
            'min_length' => 'Nama minimal 3 karakter',
            'max_length' => 'Nama maksimal 100 karakter',
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
}
```

### 2.4 Buat Controller `DeviceTypeController`

#### Langkah 1: Buat Controller File dengan Spark

```bash
php spark make:controller DeviceTypeController
```

**Output yang muncul:**
```
Created file: app/Controllers/DeviceTypeController.php
```

#### Langkah 2: Edit File Controller

Buka file yang baru dibuat di `app/Controllers/DeviceTypeController.php` dan ganti isinya dengan:

```php
<?php

namespace App\Controllers;

use App\Models\DeviceTypeModel;
use CodeIgniter\RESTful\ResourceController;

class DeviceTypeController extends ResourceController
{
    protected $modelName = DeviceTypeModel::class;
    protected $format    = 'json';

    /**
     * Menampilkan semua device types
     */
    public function index()
    {
        $model = new DeviceTypeModel();
        $data = [
            'title' => 'Device Types',
            'deviceTypes' => $model->findAll(),
        ];
        return view('device_types/index', $data);
    }

    /**
     * Menampilkan form create
     */
    public function new()
    {
        $data = [
            'title' => 'Tambah Device Type',
        ];
        return view('device_types/create', $data);
    }

    /**
     * Menyimpan device type baru
     */
    public function create()
    {
        $model = new DeviceTypeModel();
        
        $data = [
            'name' => $this->request->getPost('name'),
            'description' => $this->request->getPost('description'),
        ];

        if (!$model->insert($data)) {
            return redirect()->back()
                ->with('errors', $model->errors())
                ->withInput();
        }

        return redirect()->to('/device-types')
            ->with('success', 'Device type berhasil ditambahkan');
    }

    /**
     * Menampilkan detail device type
     */
    public function show($id = null)
    {
        $model = new DeviceTypeModel();
        $deviceType = $model->find($id);
        
        if (!$deviceType) {
            throw \CodeIgniter\Exceptions\PageNotFoundException::forPageNotFound();
        }

        $data = [
            'title' => 'Detail Device Type',
            'deviceType' => $deviceType,
        ];
        return view('device_types/show', $data);
    }

    /**
     * Menampilkan form edit
     */
    public function edit($id = null)
    {
        $model = new DeviceTypeModel();
        $deviceType = $model->find($id);
        
        if (!$deviceType) {
            throw \CodeIgniter\Exceptions\PageNotFoundException::forPageNotFound();
        }

        $data = [
            'title' => 'Edit Device Type',
            'deviceType' => $deviceType,
        ];
        return view('device_types/edit', $data);
    }

    /**
     * Update device type
     */
    public function update($id = null)
    {
        $model = new DeviceTypeModel();
        $deviceType = $model->find($id);
        
        if (!$deviceType) {
            throw \CodeIgniter\Exceptions\PageNotFoundException::forPageNotFound();
        }

        $data = [
            'name' => $this->request->getPost('name'),
            'description' => $this->request->getPost('description'),
        ];

        if (!$model->update($id, $data)) {
            return redirect()->back()
                ->with('errors', $model->errors())
                ->withInput();
        }

        return redirect()->to('/device-types')
            ->with('success', 'Device type berhasil diupdate');
    }

    /**
     * Hapus device type
     */
    public function delete($id = null)
    {
        $model = new DeviceTypeModel();
        $deviceType = $model->find($id);
        
        if (!$deviceType) {
            throw \CodeIgniter\Exceptions\PageNotFoundException::forPageNotFound();
        }

        $model->delete($id);
        return redirect()->to('/device-types')
            ->with('success', 'Device type berhasil dihapus');
    }
}
```

### 2.5 Buat Routes untuk Device Types

**File:** `app/Config/Routes.php`

Tambahkan di dalam function `setRoutes()` setelah Home Route:

```php
// Device Types Routes
$routes->group('device-types', function($routes) {
    $routes->get('/', 'DeviceTypeController::index');
    $routes->get('new', 'DeviceTypeController::new');
    $routes->post('create', 'DeviceTypeController::create');
    $routes->get('(:num)', 'DeviceTypeController::show/$1');
    $routes->get('(:num)/edit', 'DeviceTypeController::edit/$1');
    $routes->post('(:num)/update', 'DeviceTypeController::update/$1');
    $routes->get('(:num)/delete', 'DeviceTypeController::delete/$1');
});
```

### 2.6 Buat Views

**Penting:** Semua views akan menggunakan layout template yang sudah dibuat di Section 1.5. Kita hanya perlu fokus pada konten utama saja!

**Cara Kerja Layout Template:**
- Setiap view menggunakan `<?= $this->extend('layout') ?>` untuk extend layout
- Konten utama dimasukkan ke dalam `<?= $this->section('content') ?>`
- Flash messages (success/error) sudah di-handle di layout, tidak perlu ditulis ulang
- HTML boilerplate (DOCTYPE, head, body, navbar, footer) sudah ada di layout

**Keuntungan:**
- Tidak perlu menulis HTML berulang-ulang
- Konsisten di semua halaman
- Mudah di-maintain (ubah layout sekali, semua halaman ikut berubah)

#### View: Index (List)

**File:** `app/Views/device_types/index.php`

Buat folder `device_types` di `app/Views/` jika belum ada, lalu buat file `index.php`:

```php
<?= $this->extend('layout') ?>

<?= $this->section('content') ?>
<div class="row">
    <div class="col-md-12">
        <h1 class="mb-4"><?= $title ?></h1>

        <a href="<?= base_url('device-types/new') ?>" class="btn btn-primary mb-3">Tambah Device Type</a>

        <table class="table table-striped">
            <thead>
                <tr>
                    <th>ID</th>
                    <th>Nama</th>
                    <th>Deskripsi</th>
                    <th>Aksi</th>
                </tr>
            </thead>
            <tbody>
                <?php if (empty($deviceTypes)): ?>
                    <tr>
                        <td colspan="4" class="text-center">Tidak ada data</td>
                    </tr>
                <?php else: ?>
                    <?php foreach ($deviceTypes as $type): ?>
                        <tr>
                            <td><?= $type['id'] ?></td>
                            <td><?= esc($type['name']) ?></td>
                            <td><?= esc($type['description']) ?></td>
                            <td>
                                <a href="<?= base_url('device-types/' . $type['id']) ?>" class="btn btn-sm btn-info">Detail</a>
                                <a href="<?= base_url('device-types/' . $type['id'] . '/edit') ?>" class="btn btn-sm btn-warning">Edit</a>
                                <a href="<?= base_url('device-types/' . $type['id'] . '/delete') ?>" class="btn btn-sm btn-danger" onclick="return confirm('Yakin hapus?')">Hapus</a>
                            </td>
                        </tr>
                    <?php endforeach; ?>
                <?php endif; ?>
            </tbody>
        </table>
    </div>
</div>
<?= $this->endSection() ?>
```

#### View: Create

**File:** `app/Views/device_types/create.php`

```php
<?= $this->extend('layout') ?>

<?= $this->section('content') ?>
<div class="row">
    <div class="col-md-8">
        <h1 class="mb-4"><?= $title ?></h1>

        <form action="<?= base_url('device-types/create') ?>" method="post">
            <?= csrf_field() ?>
            
            <div class="mb-3">
                <label for="name" class="form-label">Nama Device Type <span class="text-danger">*</span></label>
                <input type="text" class="form-control" id="name" name="name" 
                       value="<?= old('name') ?>" required>
            </div>

            <div class="mb-3">
                <label for="description" class="form-label">Deskripsi</label>
                <textarea class="form-control" id="description" name="description" rows="3"><?= old('description') ?></textarea>
            </div>

            <button type="submit" class="btn btn-primary">Simpan</button>
            <a href="<?= base_url('device-types') ?>" class="btn btn-secondary">Batal</a>
        </form>
    </div>
</div>
<?= $this->endSection() ?>
```

#### View: Edit

**File:** `app/Views/device_types/edit.php`

```php
<?= $this->extend('layout') ?>

<?= $this->section('content') ?>
<div class="row">
    <div class="col-md-8">
        <h1 class="mb-4"><?= $title ?></h1>

        <form action="<?= base_url('device-types/' . $deviceType['id'] . '/update') ?>" method="post">
            <?= csrf_field() ?>
            
            <div class="mb-3">
                <label for="name" class="form-label">Nama Device Type <span class="text-danger">*</span></label>
                <input type="text" class="form-control" id="name" name="name" 
                       value="<?= old('name', $deviceType['name']) ?>" required>
            </div>

            <div class="mb-3">
                <label for="description" class="form-label">Deskripsi</label>
                <textarea class="form-control" id="description" name="description" rows="3"><?= old('description', $deviceType['description']) ?></textarea>
            </div>

            <button type="submit" class="btn btn-primary">Update</button>
            <a href="<?= base_url('device-types') ?>" class="btn btn-secondary">Batal</a>
        </form>
    </div>
</div>
<?= $this->endSection() ?>
```

#### View: Show (Detail)

**File:** `app/Views/device_types/show.php`

```php
<?= $this->extend('layout') ?>

<?= $this->section('content') ?>
<div class="row">
    <div class="col-md-8">
        <h1 class="mb-4"><?= $title ?></h1>

        <div class="card">
            <div class="card-header">
                <h5 class="mb-0">Detail Device Type</h5>
            </div>
            <div class="card-body">
                <dl class="row">
                    <dt class="col-sm-3">ID</dt>
                    <dd class="col-sm-9"><?= $deviceType['id'] ?></dd>

                    <dt class="col-sm-3">Nama</dt>
                    <dd class="col-sm-9"><?= esc($deviceType['name']) ?></dd>

                    <dt class="col-sm-3">Deskripsi</dt>
                    <dd class="col-sm-9"><?= esc($deviceType['description'] ?? '-') ?></dd>

                    <dt class="col-sm-3">Dibuat</dt>
                    <dd class="col-sm-9"><?= $deviceType['created_at'] ? date('d/m/Y H:i', strtotime($deviceType['created_at'])) : '-' ?></dd>

                    <dt class="col-sm-3">Diupdate</dt>
                    <dd class="col-sm-9"><?= $deviceType['updated_at'] ? date('d/m/Y H:i', strtotime($deviceType['updated_at'])) : '-' ?></dd>
                </dl>
            </div>
            <div class="card-footer">
                <a href="<?= base_url('device-types/' . $deviceType['id'] . '/edit') ?>" class="btn btn-warning">Edit</a>
                <a href="<?= base_url('device-types') ?>" class="btn btn-secondary">Kembali</a>
                <a href="<?= base_url('device-types/' . $deviceType['id'] . '/delete') ?>" class="btn btn-danger" onclick="return confirm('Yakin hapus?')">Hapus</a>
            </div>
        </div>
    </div>
</div>
<?= $this->endSection() ?>
```

---

## 3. Device CRUD (dengan Relasi ke Device Type)

### 3.1 Buat Migration untuk Tabel `devices`

#### Langkah 1: Buat Migration File dengan Spark

Pastikan migration `device_types` sudah di-run terlebih dahulu, lalu buat migration untuk `devices`:

```bash
# Pastikan di folder project
cd C:\xampp\htdocs\device-management
# atau
cd C:\laragon\www\device-management

# Buat migration file
php spark make:migration CreateDevicesTable
```

**Output yang muncul:**
```
Created file: app/Database/Migrations/YYYY-MM-DD-HHMMSS_CreateDevicesTable.php
```

#### Langkah 2: Edit File Migration

Buka file yang baru dibuat di `app/Database/Migrations/YYYY-MM-DD-HHMMSS_CreateDevicesTable.php` dan ganti isinya dengan:

```php
<?php

namespace App\Database\Migrations;

use CodeIgniter\Database\Migration;

class CreateDevicesTable extends Migration
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
            'device_type_id' => [
                'type'       => 'INT',
                'constraint' => 11,
                'unsigned'   => true,
            ],
            'name' => [
                'type'       => 'VARCHAR',
                'constraint' => '100',
            ],
            'specification' => [
                'type' => 'TEXT',
                'null' => true,
            ],
            'status' => [
                'type'       => 'ENUM',
                'constraint' => ['active', 'inactive', 'maintenance'],
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
        $this->forge->addForeignKey('device_type_id', 'device_types', 'id', 'CASCADE', 'CASCADE');
        $this->forge->createTable('devices');
    }

    public function down()
    {
        $this->forge->dropTable('devices');
    }
}
```

**Penjelasan:**
- `device_type_id`: Foreign key ke tabel `device_types`
- `addForeignKey()`: Menambahkan foreign key constraint dengan CASCADE (jika device_type dihapus, devices terkait juga terhapus)
- `status`: ENUM dengan default value 'active'

### 2.2 Run Migration

```bash
php spark migrate
```

### 3.3 Buat Model `DeviceModel` dengan Relasi

#### Langkah 1: Buat Model File dengan Spark

```bash
php spark make:model DeviceModel
```

**Output yang muncul:**
```
Created file: app/Models/DeviceModel.php
```

#### Langkah 2: Edit File Model

Buka file yang baru dibuat di `app/Models/DeviceModel.php` dan ganti isinya dengan:

```php
<?php

namespace App\Models;

use CodeIgniter\Model;

class DeviceModel extends Model
{
    protected $table            = 'devices';
    protected $primaryKey       = 'id';
    protected $useAutoIncrement = true;
    protected $returnType       = 'array';
    protected $useSoftDeletes   = false;
    protected $protectFields    = true;
    protected $allowedFields    = ['device_type_id', 'name', 'specification', 'status'];

    // Dates
    protected $useTimestamps = true;
    protected $dateFormat    = 'datetime';
    protected $createdField  = 'created_at';
    protected $updatedField  = 'updated_at';

    // Validation
    protected $validationRules      = [
        'device_type_id' => 'required|integer',
        'name' => 'required|min_length[3]|max_length[100]',
        'status' => 'required|in_list[active,inactive,maintenance]',
    ];
    protected $validationMessages   = [
        'device_type_id' => [
            'required' => 'Device type wajib dipilih',
            'integer' => 'Device type harus berupa angka',
        ],
        'name' => [
            'required' => 'Nama device wajib diisi',
            'min_length' => 'Nama minimal 3 karakter',
            'max_length' => 'Nama maksimal 100 karakter',
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
     * Ambil semua devices dengan relasi device type
     */
    public function getAllWithType()
    {
        return $this->select('devices.*, device_types.name as device_type_name')
                    ->join('device_types', 'device_types.id = devices.device_type_id')
                    ->findAll();
    }

    /**
     * Ambil device dengan relasi device type
     */
    public function getWithType($id)
    {
        return $this->select('devices.*, device_types.name as device_type_name')
                    ->join('device_types', 'device_types.id = devices.device_type_id')
                    ->find($id);
    }
}
```

### 3.4 Buat Controller `DeviceController`

#### Langkah 1: Buat Controller File dengan Spark

```bash
php spark make:controller DeviceController
```

**Output yang muncul:**
```
Created file: app/Controllers/DeviceController.php
```

#### Langkah 2: Edit File Controller

Buka file yang baru dibuat di `app/Controllers/DeviceController.php` dan ganti isinya dengan:

```php
<?php

namespace App\Controllers;

use App\Models\DeviceModel;
use App\Models\DeviceTypeModel;
use CodeIgniter\RESTful\ResourceController;

class DeviceController extends ResourceController
{
    protected $modelName = DeviceModel::class;
    protected $format    = 'json';

    /**
     * Menampilkan semua devices
     */
    public function index()
    {
        $model = new DeviceModel();
        $data = [
            'title' => 'Devices',
            'devices' => $model->getAllWithType(),
        ];
        return view('devices/index', $data);
    }

    /**
     * Menampilkan form create
     */
    public function new()
    {
        $deviceTypeModel = new DeviceTypeModel();
        $data = [
            'title' => 'Tambah Device',
            'deviceTypes' => $deviceTypeModel->findAll(),
        ];
        return view('devices/create', $data);
    }

    /**
     * Menyimpan device baru
     */
    public function create()
    {
        $model = new DeviceModel();
        
        $data = [
            'device_type_id' => $this->request->getPost('device_type_id'),
            'name' => $this->request->getPost('name'),
            'specification' => $this->request->getPost('specification'),
            'status' => $this->request->getPost('status'),
        ];

        if (!$model->insert($data)) {
            return redirect()->back()
                ->with('errors', $model->errors())
                ->withInput();
        }

        return redirect()->to('/devices')
            ->with('success', 'Device berhasil ditambahkan');
    }

    /**
     * Menampilkan detail device
     */
    public function show($id = null)
    {
        $model = new DeviceModel();
        $device = $model->getWithType($id);
        
        if (!$device) {
            throw \CodeIgniter\Exceptions\PageNotFoundException::forPageNotFound();
        }

        $data = [
            'title' => 'Detail Device',
            'device' => $device,
        ];
        return view('devices/show', $data);
    }

    /**
     * Menampilkan form edit
     */
    public function edit($id = null)
    {
        $model = new DeviceModel();
        $device = $model->find($id);
        
        if (!$device) {
            throw \CodeIgniter\Exceptions\PageNotFoundException::forPageNotFound();
        }

        $deviceTypeModel = new DeviceTypeModel();
        $data = [
            'title' => 'Edit Device',
            'device' => $device,
            'deviceTypes' => $deviceTypeModel->findAll(),
        ];
        return view('devices/edit', $data);
    }

    /**
     * Update device
     */
    public function update($id = null)
    {
        $model = new DeviceModel();
        $device = $model->find($id);
        
        if (!$device) {
            throw \CodeIgniter\Exceptions\PageNotFoundException::forPageNotFound();
        }

        $data = [
            'device_type_id' => $this->request->getPost('device_type_id'),
            'name' => $this->request->getPost('name'),
            'specification' => $this->request->getPost('specification'),
            'status' => $this->request->getPost('status'),
        ];

        if (!$model->update($id, $data)) {
            return redirect()->back()
                ->with('errors', $model->errors())
                ->withInput();
        }

        return redirect()->to('/devices')
            ->with('success', 'Device berhasil diupdate');
    }

    /**
     * Hapus device
     */
    public function delete($id = null)
    {
        $model = new DeviceModel();
        $device = $model->find($id);
        
        if (!$device) {
            throw \CodeIgniter\Exceptions\PageNotFoundException::forPageNotFound();
        }

        $model->delete($id);
        return redirect()->to('/devices')
            ->with('success', 'Device berhasil dihapus');
    }
}
```

### 3.5 Tambahkan Routes untuk Devices

**File:** `app/Config/Routes.php`

Tambahkan di dalam function `setRoutes()`:

```php
// Devices Routes
$routes->group('devices', function($routes) {
    $routes->get('/', 'DeviceController::index');
    $routes->get('new', 'DeviceController::new');
    $routes->post('create', 'DeviceController::create');
    $routes->get('(:num)', 'DeviceController::show/$1');
    $routes->get('(:num)/edit', 'DeviceController::edit/$1');
    $routes->post('(:num)/update', 'DeviceController::update/$1');
    $routes->get('(:num)/delete', 'DeviceController::delete/$1');
});
```

### 3.6 Buat Views untuk Devices

Semua views menggunakan layout template yang sama, jadi lebih simpel!

#### View: Index (List)

**File:** `app/Views/devices/index.php`

Buat folder `devices` di `app/Views/` jika belum ada, lalu buat file `index.php`:

```php
<?= $this->extend('layout') ?>

<?= $this->section('content') ?>
<div class="row">
    <div class="col-md-12">
        <h1 class="mb-4"><?= $title ?></h1>

        <a href="<?= base_url('devices/new') ?>" class="btn btn-primary mb-3">Tambah Device</a>
        <a href="<?= base_url('device-types') ?>" class="btn btn-secondary mb-3">Kelola Device Types</a>

        <table class="table table-striped">
            <thead>
                <tr>
                    <th>ID</th>
                    <th>Device Type</th>
                    <th>Nama Device</th>
                    <th>Spesifikasi</th>
                    <th>Status</th>
                    <th>Aksi</th>
                </tr>
            </thead>
            <tbody>
                <?php if (empty($devices)): ?>
                    <tr>
                        <td colspan="6" class="text-center">Tidak ada data</td>
                    </tr>
                <?php else: ?>
                    <?php foreach ($devices as $device): ?>
                        <tr>
                            <td><?= $device['id'] ?></td>
                            <td><?= esc($device['device_type_name']) ?></td>
                            <td><?= esc($device['name']) ?></td>
                            <td><?= esc($device['specification']) ?></td>
                            <td>
                                <?php
                                $badgeClass = [
                                    'active' => 'bg-success',
                                    'inactive' => 'bg-secondary',
                                    'maintenance' => 'bg-warning'
                                ];
                                $badge = $badgeClass[$device['status']] ?? 'bg-secondary';
                                ?>
                                <span class="badge <?= $badge ?>"><?= ucfirst($device['status']) ?></span>
                            </td>
                            <td>
                                <a href="<?= base_url('devices/' . $device['id']) ?>" class="btn btn-sm btn-info">Detail</a>
                                <a href="<?= base_url('devices/' . $device['id'] . '/edit') ?>" class="btn btn-sm btn-warning">Edit</a>
                                <a href="<?= base_url('devices/' . $device['id'] . '/delete') ?>" class="btn btn-sm btn-danger" onclick="return confirm('Yakin hapus?')">Hapus</a>
                            </td>
                        </tr>
                    <?php endforeach; ?>
                <?php endif; ?>
            </tbody>
        </table>
    </div>
</div>
<?= $this->endSection() ?>
```

#### View: Create

**File:** `app/Views/devices/create.php`

```php
<?= $this->extend('layout') ?>

<?= $this->section('content') ?>
<div class="row">
    <div class="col-md-8">
        <h1 class="mb-4"><?= $title ?></h1>

        <form action="<?= base_url('devices/create') ?>" method="post">
            <?= csrf_field() ?>
            
            <div class="mb-3">
                <label for="device_type_id" class="form-label">Device Type <span class="text-danger">*</span></label>
                <select class="form-select" id="device_type_id" name="device_type_id" required>
                    <option value="">Pilih Device Type</option>
                    <?php foreach ($deviceTypes as $type): ?>
                        <option value="<?= $type['id'] ?>" <?= old('device_type_id') == $type['id'] ? 'selected' : '' ?>>
                            <?= esc($type['name']) ?>
                        </option>
                    <?php endforeach; ?>
                </select>
            </div>

            <div class="mb-3">
                <label for="name" class="form-label">Nama Device <span class="text-danger">*</span></label>
                <input type="text" class="form-control" id="name" name="name" 
                       value="<?= old('name') ?>" required>
            </div>

            <div class="mb-3">
                <label for="specification" class="form-label">Spesifikasi</label>
                <textarea class="form-control" id="specification" name="specification" rows="3"><?= old('specification') ?></textarea>
            </div>

            <div class="mb-3">
                <label for="status" class="form-label">Status <span class="text-danger">*</span></label>
                <select class="form-select" id="status" name="status" required>
                    <option value="active" <?= old('status') == 'active' ? 'selected' : '' ?>>Active</option>
                    <option value="inactive" <?= old('status') == 'inactive' ? 'selected' : '' ?>>Inactive</option>
                    <option value="maintenance" <?= old('status') == 'maintenance' ? 'selected' : '' ?>>Maintenance</option>
                </select>
            </div>

            <button type="submit" class="btn btn-primary">Simpan</button>
            <a href="<?= base_url('devices') ?>" class="btn btn-secondary">Batal</a>
        </form>
    </div>
</div>
<?= $this->endSection() ?>
```

#### View: Edit

**File:** `app/Views/devices/edit.php`

```php
<?= $this->extend('layout') ?>

<?= $this->section('content') ?>
<div class="row">
    <div class="col-md-8">
        <h1 class="mb-4"><?= $title ?></h1>

        <form action="<?= base_url('devices/' . $device['id'] . '/update') ?>" method="post">
            <?= csrf_field() ?>
            
            <div class="mb-3">
                <label for="device_type_id" class="form-label">Device Type <span class="text-danger">*</span></label>
                <select class="form-select" id="device_type_id" name="device_type_id" required>
                    <option value="">Pilih Device Type</option>
                    <?php foreach ($deviceTypes as $type): ?>
                        <option value="<?= $type['id'] ?>" 
                                <?= old('device_type_id', $device['device_type_id']) == $type['id'] ? 'selected' : '' ?>>
                            <?= esc($type['name']) ?>
                        </option>
                    <?php endforeach; ?>
                </select>
            </div>

            <div class="mb-3">
                <label for="name" class="form-label">Nama Device <span class="text-danger">*</span></label>
                <input type="text" class="form-control" id="name" name="name" 
                       value="<?= old('name', $device['name']) ?>" required>
            </div>

            <div class="mb-3">
                <label for="specification" class="form-label">Spesifikasi</label>
                <textarea class="form-control" id="specification" name="specification" rows="3"><?= old('specification', $device['specification']) ?></textarea>
            </div>

            <div class="mb-3">
                <label for="status" class="form-label">Status <span class="text-danger">*</span></label>
                <select class="form-select" id="status" name="status" required>
                    <option value="active" <?= old('status', $device['status']) == 'active' ? 'selected' : '' ?>>Active</option>
                    <option value="inactive" <?= old('status', $device['status']) == 'inactive' ? 'selected' : '' ?>>Inactive</option>
                    <option value="maintenance" <?= old('status', $device['status']) == 'maintenance' ? 'selected' : '' ?>>Maintenance</option>
                </select>
            </div>

            <button type="submit" class="btn btn-primary">Update</button>
            <a href="<?= base_url('devices') ?>" class="btn btn-secondary">Batal</a>
        </form>
    </div>
</div>
<?= $this->endSection() ?>
```

#### View: Show (Detail)

**File:** `app/Views/devices/show.php`

```php
<?= $this->extend('layout') ?>

<?= $this->section('content') ?>
<div class="row">
    <div class="col-md-8">
        <h1 class="mb-4"><?= $title ?></h1>

        <div class="card">
            <div class="card-header">
                <h5 class="mb-0">Detail Device</h5>
            </div>
            <div class="card-body">
                <dl class="row">
                    <dt class="col-sm-3">ID</dt>
                    <dd class="col-sm-9"><?= $device['id'] ?></dd>

                    <dt class="col-sm-3">Device Type</dt>
                    <dd class="col-sm-9"><?= esc($device['device_type_name']) ?></dd>

                    <dt class="col-sm-3">Nama Device</dt>
                    <dd class="col-sm-9"><?= esc($device['name']) ?></dd>

                    <dt class="col-sm-3">Spesifikasi</dt>
                    <dd class="col-sm-9"><?= esc($device['specification'] ?? '-') ?></dd>

                    <dt class="col-sm-3">Status</dt>
                    <dd class="col-sm-9">
                        <?php
                        $badgeClass = [
                            'active' => 'bg-success',
                            'inactive' => 'bg-secondary',
                            'maintenance' => 'bg-warning'
                        ];
                        $badge = $badgeClass[$device['status']] ?? 'bg-secondary';
                        ?>
                        <span class="badge <?= $badge ?>"><?= ucfirst($device['status']) ?></span>
                    </dd>

                    <dt class="col-sm-3">Dibuat</dt>
                    <dd class="col-sm-9"><?= $device['created_at'] ? date('d/m/Y H:i', strtotime($device['created_at'])) : '-' ?></dd>

                    <dt class="col-sm-3">Diupdate</dt>
                    <dd class="col-sm-9"><?= $device['updated_at'] ? date('d/m/Y H:i', strtotime($device['updated_at'])) : '-' ?></dd>
                </dl>
            </div>
            <div class="card-footer">
                <a href="<?= base_url('devices/' . $device['id'] . '/edit') ?>" class="btn btn-warning">Edit</a>
                <a href="<?= base_url('devices') ?>" class="btn btn-secondary">Kembali</a>
                <a href="<?= base_url('devices/' . $device['id'] . '/delete') ?>" class="btn btn-danger" onclick="return confirm('Yakin hapus?')">Hapus</a>
            </div>
        </div>
    </div>
</div>
<?= $this->endSection() ?>
```

---

## 4. Testing CRUD

### Test Device Type CRUD
1. Buka: http://localhost/device-management/public/device-types
2. Tambah beberapa device type (Server, Router, Switch, dll)
3. Test Edit dan Delete

### Test Device CRUD
1. Buka: http://localhost/device-management/public/devices
2. Tambah device dengan memilih device type
3. Test Edit dan Delete
4. Pastikan relasi device type muncul dengan benar

---

## 5. Penjelasan Relasi Database

### One-to-Many Relationship
- **Device Type** (1) → **Devices** (Many)
- Satu Device Type bisa punya banyak Devices
- Foreign key: `device_type_id` di tabel `devices`
- Constraint: `CASCADE` untuk update dan delete

### Join Query
Di Model, kita menggunakan join untuk mengambil data device beserta nama device type:
```php
$this->select('devices.*, device_types.name as device_type_name')
     ->join('device_types', 'device_types.id = devices.device_type_id')
```

---

## Selesai!

Setelah CRUD selesai, lanjut ke: **praktek_api.md**

