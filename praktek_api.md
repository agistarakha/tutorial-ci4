# Praktek: Membuat dan Mengkonsumsi API

## Pendahuluan

Setelah membuat CRUD untuk Device Types dan Devices di **praktek_crud.md**, sekarang kita akan:
1. Membuat API untuk Device Types dan Devices
2. Menggunakan API Device Types untuk dropdown di form Device (mengganti cara lama)
3. Mengkonsumsi API publik Netbox untuk menampilkan list devices dari Netbox

---

## 1. Membuat API Lokal

### 1.1 Buat API Controller untuk Device Types

**File:** `app/Controllers/Api/DeviceTypeApiController.php`

Buat folder `Api` di `app/Controllers/` jika belum ada, lalu buat file:

```php
<?php

namespace App\Controllers\Api;

use App\Controllers\BaseController;
use App\Models\DeviceTypeModel;
use CodeIgniter\API\ResponseTrait;

class DeviceTypeApiController extends BaseController
{
    use ResponseTrait;

    protected $model;

    public function __construct()
    {
        $this->model = new DeviceTypeModel();
    }

    /**
     * GET /api/device-types
     * Ambil semua device types (untuk dropdown)
     */
    public function index()
    {
        $deviceTypes = $this->model->findAll();
        
        return $this->respond([
            'status' => 'success',
            'message' => 'Data berhasil diambil',
            'data' => $deviceTypes
        ], 200);
    }

    /**
     * GET /api/device-types/{id}
     * Ambil device type berdasarkan ID
     */
    public function show($id = null)
    {
        $deviceType = $this->model->find($id);
        
        if (!$deviceType) {
            return $this->failNotFound('Device type tidak ditemukan');
        }

        return $this->respond([
            'status' => 'success',
            'message' => 'Data berhasil diambil',
            'data' => $deviceType
        ], 200);
    }
}
```

**Cara Buat:**
```bash
# Buat folder Api jika belum ada
mkdir app/Controllers/Api

# Buat file DeviceTypeApiController.php
```

### 1.2 Buat API Controller untuk Devices

**File:** `app/Controllers/Api/DeviceApiController.php`

```php
<?php

namespace App\Controllers\Api;

use App\Controllers\BaseController;
use App\Models\DeviceModel;
use CodeIgniter\API\ResponseTrait;

class DeviceApiController extends BaseController
{
    use ResponseTrait;

    protected $model;

    public function __construct()
    {
        $this->model = new DeviceModel();
    }

    /**
     * GET /api/devices
     * Ambil semua devices dengan relasi device type
     */
    public function index()
    {
        $devices = $this->model->getAllWithType();
        
        return $this->respond([
            'status' => 'success',
            'message' => 'Data berhasil diambil',
            'data' => $devices
        ], 200);
    }

    /**
     * GET /api/devices/{id}
     * Ambil device berdasarkan ID
     */
    public function show($id = null)
    {
        $device = $this->model->getWithType($id);
        
        if (!$device) {
            return $this->failNotFound('Device tidak ditemukan');
        }

        return $this->respond([
            'status' => 'success',
            'message' => 'Data berhasil diambil',
            'data' => $device
        ], 200);
    }

    /**
     * POST /api/devices
     * Buat device baru
     */
    public function create()
    {
        $data = [
            'device_type_id' => $this->request->getJSON(true)['device_type_id'] ?? null,
            'name' => $this->request->getJSON(true)['name'] ?? null,
            'specification' => $this->request->getJSON(true)['specification'] ?? null,
            'status' => $this->request->getJSON(true)['status'] ?? 'active',
        ];

        if (!$this->model->insert($data)) {
            return $this->failValidationErrors($this->model->errors());
        }

        $device = $this->model->getWithType($this->model->getInsertID());

        return $this->respondCreated([
            'status' => 'success',
            'message' => 'Device berhasil ditambahkan',
            'data' => $device
        ]);
    }

    /**
     * PUT /api/devices/{id}
     * Update device
     */
    public function update($id = null)
    {
        $device = $this->model->find($id);
        
        if (!$device) {
            return $this->failNotFound('Device tidak ditemukan');
        }

        $data = [
            'device_type_id' => $this->request->getJSON(true)['device_type_id'] ?? $device['device_type_id'],
            'name' => $this->request->getJSON(true)['name'] ?? $device['name'],
            'specification' => $this->request->getJSON(true)['specification'] ?? $device['specification'],
            'status' => $this->request->getJSON(true)['status'] ?? $device['status'],
        ];

        if (!$this->model->update($id, $data)) {
            return $this->failValidationErrors($this->model->errors());
        }

        $updatedDevice = $this->model->getWithType($id);

        return $this->respond([
            'status' => 'success',
            'message' => 'Device berhasil diupdate',
            'data' => $updatedDevice
        ], 200);
    }

    /**
     * DELETE /api/devices/{id}
     * Hapus device
     */
    public function delete($id = null)
    {
        $device = $this->model->find($id);
        
        if (!$device) {
            return $this->failNotFound('Device tidak ditemukan');
        }

        $this->model->delete($id);

        return $this->respondDeleted([
            'status' => 'success',
            'message' => 'Device berhasil dihapus'
        ]);
    }
}
```

### 1.3 Setup Routes untuk API

**File:** `app/Config/Routes.php`

Tambahkan di dalam function `setRoutes()`:

```php
// API Routes
$routes->group('api', ['namespace' => 'App\Controllers\Api'], function($routes) {
    // Device Types API
    $routes->get('device-types', 'DeviceTypeApiController::index');
    $routes->get('device-types/(:num)', 'DeviceTypeApiController::show/$1');
    
    // Devices API
    $routes->get('devices', 'DeviceApiController::index');
    $routes->get('devices/(:num)', 'DeviceApiController::show/$1');
    $routes->post('devices', 'DeviceApiController::create');
    $routes->put('devices/(:num)', 'DeviceApiController::update/$1');
    $routes->delete('devices/(:num)', 'DeviceApiController::delete/$1');
});
```

### 1.4 Test API dengan Browser

Buka di browser untuk test GET:
- http://localhost/device-management/public/api/device-types
- http://localhost/device-management/public/api/devices

---

## 2. Menggunakan API Lokal di View

### 2.1 Update Form Device untuk Menggunakan API Device Types

**File:** `app/Views/devices/create.php`

Update form create untuk load device types dari API:

```php
<?= $this->extend('layout') ?>

<?= $this->section('content') ?>
<div class="row">
    <div class="col-md-8">
        <h1 class="mb-4"><?= $title ?></h1>

        <form action="<?= base_url('devices/create') ?>" method="post" id="deviceForm">
            <?= csrf_field() ?>
            
            <div class="mb-3">
                <label for="device_type_id" class="form-label">Device Type <span class="text-danger">*</span></label>
                <select class="form-select" id="device_type_id" name="device_type_id" required>
                    <option value="">Loading...</option>
                </select>
                <div id="device-type-error" class="text-danger d-none"></div>
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

<?= $this->section('scripts') ?>
<script>
let isLoadingDeviceTypes = false;
let lastFetchTime = 0;
const FETCH_DEBOUNCE = 300; // Debounce 300ms

// Load device types dari API saat halaman load
document.addEventListener('DOMContentLoaded', function() {
    const select = document.getElementById('device_type_id');
    
    // Load saat halaman pertama kali dimuat
    loadDeviceTypes();
    
    // Load setiap kali user akan membuka dropdown
    // Gunakan focus karena lebih reliable untuk dropdown
    select.addEventListener('focus', function() {
        const now = Date.now();
        // Debounce untuk menghindari multiple calls
        if (now - lastFetchTime > FETCH_DEBOUNCE) {
            loadDeviceTypes();
            lastFetchTime = now;
        }
    });
    
    // Juga load saat click (untuk memastikan)
    select.addEventListener('click', function() {
        const now = Date.now();
        if (now - lastFetchTime > FETCH_DEBOUNCE) {
            loadDeviceTypes();
            lastFetchTime = now;
        }
    });
});

async function loadDeviceTypes() {
    // Prevent multiple simultaneous calls
    if (isLoadingDeviceTypes) {
        return;
    }
    
    isLoadingDeviceTypes = true;
    const select = document.getElementById('device_type_id');
    const errorDiv = document.getElementById('device-type-error');
    
    // Simpan nilai yang sedang dipilih sebelum reload
    const currentValue = select.value;
    
    // Tampilkan loading indicator tanpa disable select (biarkan dropdown bisa dibuka)
    const wasEmpty = select.options.length === 0 || (select.options.length === 1 && select.options[0].value === '');
    
    try {
        const response = await fetch('<?= base_url('api/device-types') ?>');
        const result = await response.json();
        
        console.log('API Response:', result); // Debug log
        
        if (result.status === 'success' && result.data) {
            // Clear dan rebuild options
            select.innerHTML = '<option value="">Pilih Device Type</option>';
            
            // Pastikan data adalah array
            const deviceTypes = Array.isArray(result.data) ? result.data : [];
            
            if (deviceTypes.length === 0) {
                select.innerHTML = '<option value="">Tidak ada data</option>';
            } else {
                deviceTypes.forEach(deviceType => {
                    const option = document.createElement('option');
                    option.value = deviceType.id;
                    option.textContent = deviceType.name;
                    
                    // Set selected berdasarkan old value atau nilai yang sedang dipilih
                    const oldValue = <?= old('device_type_id') ? old('device_type_id') : 'null' ?>;
                    const valueToSelect = oldValue !== null ? oldValue : currentValue;
                    
                    if (deviceType.id == valueToSelect) {
                        option.selected = true;
                    }
                    
                    select.appendChild(option);
                });
            }
            
            errorDiv.classList.add('d-none');
            console.log('Options loaded:', select.options.length); // Debug log
        } else {
            throw new Error(result.message || 'Gagal mengambil data');
        }
    } catch (error) {
        console.error('Error:', error);
        select.innerHTML = '<option value="">Error loading device types</option>';
        errorDiv.textContent = 'Gagal memuat device types: ' + error.message;
        errorDiv.classList.remove('d-none');
    } finally {
        isLoadingDeviceTypes = false;
    }
}
</script>
<?= $this->endSection() ?>
<?= $this->endSection() ?>
```

### 2.2 Update Form Edit Device

**File:** `app/Views/devices/edit.php`

Update form edit dengan cara yang sama:

```php
<?= $this->extend('layout') ?>

<?= $this->section('content') ?>
<div class="row">
    <div class="col-md-8">
        <h1 class="mb-4"><?= $title ?></h1>

        <form action="<?= base_url('devices/' . $device['id'] . '/update') ?>" method="post" id="deviceForm">
            <?= csrf_field() ?>
            
            <div class="mb-3">
                <label for="device_type_id" class="form-label">Device Type <span class="text-danger">*</span></label>
                <select class="form-select" id="device_type_id" name="device_type_id" required>
                    <option value="">Loading...</option>
                </select>
                <div id="device-type-error" class="text-danger d-none"></div>
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

<?= $this->section('scripts') ?>
<script>
// Load device types dari API saat halaman load
document.addEventListener('DOMContentLoaded', function() {
    loadDeviceTypes();
});

async function loadDeviceTypes() {
    const select = document.getElementById('device_type_id');
    const errorDiv = document.getElementById('device-type-error');
    const currentDeviceTypeId = <?= $device['device_type_id'] ?>;
    
    try {
        const response = await fetch('<?= base_url('api/device-types') ?>');
        const result = await response.json();
        
        if (result.status === 'success') {
            select.innerHTML = '<option value="">Pilih Device Type</option>';
            
            result.data.forEach(deviceType => {
                const option = document.createElement('option');
                option.value = deviceType.id;
                option.textContent = deviceType.name;
                
                // Set selected berdasarkan device yang sedang di-edit
                if (deviceType.id == currentDeviceTypeId) {
                    option.selected = true;
                }
                
                select.appendChild(option);
            });
            
            errorDiv.classList.add('d-none');
        } else {
            throw new Error('Gagal mengambil data');
        }
    } catch (error) {
        console.error('Error:', error);
        select.innerHTML = '<option value="">Error loading device types</option>';
        errorDiv.textContent = 'Gagal memuat device types. Silakan refresh halaman.';
        errorDiv.classList.remove('d-none');
    }
}
</script>
<?= $this->endSection() ?>
<?= $this->endSection() ?>
```

**Keuntungan menggunakan API:**
- Device types selalu up-to-date tanpa perlu refresh halaman
- Bisa digunakan untuk autocomplete atau search
- Lebih dinamis dan interaktif

---

## 3. Mengkonsumsi API Publik: Netbox Device List

### 3.1 Buat Controller untuk Netbox API

**File:** `app/Controllers/NetboxController.php`

```php
<?php

namespace App\Controllers;

use App\Controllers\BaseController;

class NetboxController extends BaseController
{
    /**
     * Menampilkan list devices dari Netbox
     * GET /netbox/devices
     */
    public function index()
    {
        // API Key akan di-set di .env atau config
        $netboxUrl = getenv('NETBOX_URL') ?: 'http://localhost:8000';
        $apiKey = getenv('NETBOX_API_KEY') ?: '';
        
        if (empty($apiKey)) {
            return redirect()->back()
                ->with('error', 'Netbox API Key belum dikonfigurasi. Silakan set NETBOX_API_KEY di .env');
        }
        
        $client = \Config\Services::curlrequest();
        
        try {
            $response = $client->get($netboxUrl . '/api/dcim/devices/', [
                'headers' => [
                    'Authorization' => 'Token ' . $apiKey,
                    'Accept' => 'application/json',
                ],
                'http_errors' => false,
            ]);
            
            $statusCode = $response->getStatusCode();
            
            if ($statusCode !== 200) {
                throw new \Exception('Netbox API error: ' . $statusCode);
            }
            
            $result = json_decode($response->getBody(), true);
            $devices = $result['results'] ?? [];
            
            $data = [
                'title' => 'Netbox Devices',
                'devices' => $devices,
                'netboxUrl' => $netboxUrl,
            ];
            
            return view('netbox/devices', $data);
        } catch (\Exception $e) {
            return redirect()->back()
                ->with('error', 'Gagal mengambil data dari Netbox: ' . $e->getMessage());
        }
    }
}
```

### 3.2 Setup Environment Variable

**File:** `env` (atau `.env`)

Tambahkan konfigurasi Netbox:

```env
# Netbox Configuration
NETBOX_URL=http://localhost:8000
NETBOX_API_KEY=your_api_key_here
```

**Catatan:** Ganti `your_api_key_here` dengan API key Netbox Anda.

### 3.3 Buat View untuk Netbox Devices

**File:** `app/Views/netbox/devices.php`

Buat folder `netbox` di `app/Views/` jika belum ada:

```php
<?= $this->extend('layout') ?>

<?= $this->section('content') ?>
<div class="row">
    <div class="col-md-12">
        <h1 class="mb-4"><?= $title ?></h1>
        <p class="text-muted">Data diambil dari Netbox API: <?= esc($netboxUrl) ?></p>

        <?php if (empty($devices)): ?>
            <div class="alert alert-info">
                Tidak ada device ditemukan di Netbox.
            </div>
        <?php else: ?>
            <div class="table-responsive">
                <table class="table table-striped table-hover">
                    <thead>
                        <tr>
                            <th>ID</th>
                            <th>Name</th>
                            <th>Device Type</th>
                            <th>Status</th>
                            <th>Site</th>
                            <th>Rack</th>
                            <th>Position</th>
                        </tr>
                    </thead>
                    <tbody>
                        <?php foreach ($devices as $device): ?>
                            <tr>
                                <td><?= $device['id'] ?? '-' ?></td>
                                <td>
                                    <strong><?= esc($device['name'] ?? $device['display'] ?? '-') ?></strong>
                                </td>
                                <td>
                                    <?php if (isset($device['device_type'])): ?>
                                        <?= esc($device['device_type']['model'] ?? '-') ?>
                                    <?php else: ?>
                                        -
                                    <?php endif; ?>
                                </td>
                                <td>
                                    <?php
                                    $status = $device['status']['value'] ?? 'unknown';
                                    $badgeClass = [
                                        'active' => 'bg-success',
                                        'planned' => 'bg-info',
                                        'staged' => 'bg-warning',
                                        'failed' => 'bg-danger',
                                        'inventory' => 'bg-secondary',
                                        'decommissioning' => 'bg-dark',
                                    ];
                                    $badge = $badgeClass[$status] ?? 'bg-secondary';
                                    ?>
                                    <span class="badge <?= $badge ?>"><?= ucfirst($status) ?></span>
                                </td>
                                <td>
                                    <?php if (isset($device['site'])): ?>
                                        <?= esc($device['site']['name'] ?? '-') ?>
                                    <?php else: ?>
                                        -
                                    <?php endif; ?>
                                </td>
                                <td>
                                    <?php if (isset($device['rack'])): ?>
                                        <?= esc($device['rack']['name'] ?? '-') ?>
                                    <?php else: ?>
                                        -
                                    <?php endif; ?>
                                </td>
                                <td>
                                    <?php if (isset($device['position'])): ?>
                                        <?= $device['position'] ?>
                                    <?php else: ?>
                                        -
                                    <?php endif; ?>
                                </td>
                            </tr>
                        <?php endforeach; ?>
                    </tbody>
                </table>
            </div>
        <?php endif; ?>
    </div>
</div>
<?= $this->endSection() ?>
```

### 3.4 Tambahkan Routes untuk Netbox

**File:** `app/Config/Routes.php`

Tambahkan di dalam function `setRoutes()`:

```php
// Netbox Routes
$routes->group('netbox', function($routes) {
    $routes->get('devices', 'NetboxController::index');
});
```

### 3.5 Tambahkan Link di Navbar (Opsional)

**File:** `app/Views/layout.php`

Tambahkan link Netbox di navbar:

```php
<li class="nav-item">
    <a class="nav-link" href="<?= base_url('netbox/devices') ?>">Netbox Devices</a>
</li>
```

### 3.6 Cara Mendapatkan Netbox API Key

1. Login ke Netbox
2. Buka **User Menu** → **API Tokens**
3. Klik **Add a token**
4. Copy token yang di-generate
5. Paste ke file `.env` sebagai `NETBOX_API_KEY`

---

## 4. Testing API

### 4.1 Test API Lokal dengan Browser

- **Device Types:** http://localhost/device-management/public/api/device-types
- **Devices:** http://localhost/device-management/public/api/devices

### 4.2 Test API Lokal dengan cURL

#### GET Device Types
```bash
curl -X GET http://localhost/device-management/public/api/device-types
```

#### GET Devices
```bash
curl -X GET http://localhost/device-management/public/api/devices
```

#### POST Device Baru
```bash
curl -X POST http://localhost/device-management/public/api/devices \
  -H "Content-Type: application/json" \
  -d '{
    "device_type_id": 1,
    "name": "Server Production 1",
    "specification": "Intel Xeon, 32GB RAM, 1TB SSD",
    "status": "active"
  }'
```

### 4.3 Test Netbox API

1. Pastikan Netbox sudah running dan accessible
2. Set `NETBOX_API_KEY` di `.env`
3. Buka: http://localhost/device-management/public/netbox/devices

---

## 5. API Response Format

### Format Standar Response (Success)

```json
{
  "status": "success",
  "message": "Data berhasil diambil",
  "data": [...]
}
```

### Format Error Response

```json
{
  "status": "error",
  "message": "Pesan error",
  "errors": {...}
}
```

---

## 6. Best Practices

1. **Gunakan ResponseTrait** untuk format response konsisten
2. **Validasi Input** sebelum insert/update
3. **Error Handling** yang baik dengan try-catch
4. **HTTP Status Code** yang tepat (200, 201, 400, 404, 500)
5. **Environment Variables** untuk konfigurasi sensitif (API keys)
6. **CORS** jika API akan diakses dari domain berbeda (opsional)
7. **Rate Limiting** untuk mencegah abuse (opsional)
8. **Authentication** jika API perlu di-secure (opsional)

---

## Selesai!

Setelah API selesai, lanjut ke: **praktek_ai.md**
