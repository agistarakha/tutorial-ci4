# Praktek: Membuat dan Mengkonsumsi API

## 1. Membuat API di CodeIgniter 4

### 1.1 Setup API Controller

CodeIgniter 4 sudah punya ResourceController untuk membuat RESTful API dengan mudah.

### 1.2 Buat API Controller untuk Devices

**File:** `app/Controllers/Api/DeviceApiController.php`

```php
<?php

namespace App\Controllers\Api;

use App\Controllers\BaseController;
use App\Models\DeviceModel;
use App\Models\DeviceTypeModel;
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
     * Ambil semua devices
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

**Cara Buat:**
```bash
# Buat folder Api jika belum ada
mkdir app/Controllers/Api

# Buat file DeviceApiController.php
```

### 1.3 Buat API Controller untuk Device Types

**File:** `app/Controllers/Api/DeviceTypeApiController.php`

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

    /**
     * POST /api/device-types
     */
    public function create()
    {
        $data = [
            'name' => $this->request->getJSON(true)['name'] ?? null,
            'description' => $this->request->getJSON(true)['description'] ?? null,
        ];

        if (!$this->model->insert($data)) {
            return $this->failValidationErrors($this->model->errors());
        }

        $deviceType = $this->model->find($this->model->getInsertID());

        return $this->respondCreated([
            'status' => 'success',
            'message' => 'Device type berhasil ditambahkan',
            'data' => $deviceType
        ]);
    }

    /**
     * PUT /api/device-types/{id}
     */
    public function update($id = null)
    {
        $deviceType = $this->model->find($id);
        
        if (!$deviceType) {
            return $this->failNotFound('Device type tidak ditemukan');
        }

        $data = [
            'name' => $this->request->getJSON(true)['name'] ?? $deviceType['name'],
            'description' => $this->request->getJSON(true)['description'] ?? $deviceType['description'],
        ];

        if (!$this->model->update($id, $data)) {
            return $this->failValidationErrors($this->model->errors());
        }

        $updatedDeviceType = $this->model->find($id);

        return $this->respond([
            'status' => 'success',
            'message' => 'Device type berhasil diupdate',
            'data' => $updatedDeviceType
        ], 200);
    }

    /**
     * DELETE /api/device-types/{id}
     */
    public function delete($id = null)
    {
        $deviceType = $this->model->find($id);
        
        if (!$deviceType) {
            return $this->failNotFound('Device type tidak ditemukan');
        }

        $this->model->delete($id);

        return $this->respondDeleted([
            'status' => 'success',
            'message' => 'Device type berhasil dihapus'
        ]);
    }
}
```

### 1.4 Setup Routes untuk API

**File:** `app/Config/Routes.php`

Tambahkan di dalam function `setRoutes()`:

```php
// API Routes
$routes->group('api', ['namespace' => 'App\Controllers\Api'], function($routes) {
    // Device Types API
    $routes->get('device-types', 'DeviceTypeApiController::index');
    $routes->get('device-types/(:num)', 'DeviceTypeApiController::show/$1');
    $routes->post('device-types', 'DeviceTypeApiController::create');
    $routes->put('device-types/(:num)', 'DeviceTypeApiController::update/$1');
    $routes->delete('device-types/(:num)', 'DeviceTypeApiController::delete/$1');
    
    // Devices API
    $routes->get('devices', 'DeviceApiController::index');
    $routes->get('devices/(:num)', 'DeviceApiController::show/$1');
    $routes->post('devices', 'DeviceApiController::create');
    $routes->put('devices/(:num)', 'DeviceApiController::update/$1');
    $routes->delete('devices/(:num)', 'DeviceApiController::delete/$1');
});
```

### 1.5 Test API dengan cURL

#### GET - Ambil Semua Devices
```bash
curl -X GET http://localhost/device-management/public/api/devices
```

#### GET - Ambil Device Berdasarkan ID
```bash
curl -X GET http://localhost/device-management/public/api/devices/1
```

#### POST - Buat Device Baru
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

#### PUT - Update Device
```bash
curl -X PUT http://localhost/device-management/public/api/devices/1 \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Server Production 1 Updated",
    "status": "maintenance"
  }'
```

#### DELETE - Hapus Device
```bash
curl -X DELETE http://localhost/device-management/public/api/devices/1
```

### 1.6 Test API dengan Postman

1. **Install Postman** dari: https://www.postman.com/downloads/

2. **Setup Request:**
   - Method: GET, POST, PUT, DELETE
   - URL: http://localhost/device-management/public/api/devices
   - Headers: `Content-Type: application/json`
   - Body (untuk POST/PUT): Raw → JSON

3. **Contoh Body untuk POST:**
```json
{
  "device_type_id": 1,
  "name": "Server Production 1",
  "specification": "Intel Xeon, 32GB RAM, 1TB SSD",
  "status": "active"
}
```

---

## 2. Mengkonsumsi API Lokal

### 2.1 Konsumsi API dari View (JavaScript Fetch)

**File:** `app/Views/devices/index.php`

Tambahkan script di bagian bawah sebelum `</body>`:

```html
<script>
// Ambil semua devices dari API
async function loadDevicesFromApi() {
    try {
        const response = await fetch('<?= base_url('api/devices') ?>');
        const result = await response.json();
        
        if (result.status === 'success') {
            console.log('Devices:', result.data);
            // Update UI dengan data dari API
            updateDevicesTable(result.data);
        }
    } catch (error) {
        console.error('Error:', error);
    }
}

// Update tabel dengan data dari API
function updateDevicesTable(devices) {
    const tbody = document.querySelector('table tbody');
    tbody.innerHTML = '';
    
    if (devices.length === 0) {
        tbody.innerHTML = '<tr><td colspan="6" class="text-center">Tidak ada data</td></tr>';
        return;
    }
    
    devices.forEach(device => {
        const row = document.createElement('tr');
        row.innerHTML = `
            <td>${device.id}</td>
            <td>${device.device_type_name}</td>
            <td>${device.name}</td>
            <td>${device.specification || '-'}</td>
            <td><span class="badge bg-success">${device.status}</span></td>
            <td>
                <a href="<?= base_url('devices/') ?>${device.id}" class="btn btn-sm btn-info">Detail</a>
                <a href="<?= base_url('devices/') ?>${device.id}/edit" class="btn btn-sm btn-warning">Edit</a>
                <a href="<?= base_url('devices/') ?>${device.id}/delete" class="btn btn-sm btn-danger">Hapus</a>
            </td>
        `;
        tbody.appendChild(row);
    });
}

// Panggil saat halaman load
// loadDevicesFromApi();
</script>
```

### 2.2 Konsumsi API dari Controller (cURL)

**File:** `app/Controllers/DeviceController.php`

Tambahkan method untuk konsumsi API:

```php
/**
 * Contoh konsumsi API lokal dari controller
 */
public function apiExample()
{
    // Konsumsi API lokal
    $client = \Config\Services::curlrequest();
    
    try {
        $response = $client->get(base_url('api/devices'));
        $result = json_decode($response->getBody(), true);
        
        $data = [
            'title' => 'Devices dari API',
            'devices' => $result['data'] ?? [],
        ];
        
        return view('devices/api_example', $data);
    } catch (\Exception $e) {
        return redirect()->back()
            ->with('error', 'Gagal mengambil data dari API: ' . $e->getMessage());
    }
}
```

---

## 3. Mengkonsumsi API Publik

### 3.1 Contoh: REST Countries API

**API Endpoint:** https://restcountries.com/v3.1/all

### 3.2 Buat Controller untuk Konsumsi API Publik

**File:** `app/Controllers/Api/PublicApiController.php`

```php
<?php

namespace App\Controllers\Api;

use App\Controllers\BaseController;
use CodeIgniter\API\ResponseTrait;

class PublicApiController extends BaseController
{
    use ResponseTrait;

    /**
     * Konsumsi REST Countries API
     * GET /api/public/countries
     */
    public function getCountries()
    {
        $client = \Config\Services::curlrequest();
        
        try {
            $response = $client->get('https://restcountries.com/v3.1/all');
            $countries = json_decode($response->getBody(), true);
            
            // Ambil hanya data yang diperlukan
            $data = array_map(function($country) {
                return [
                    'name' => $country['name']['common'] ?? '',
                    'capital' => $country['capital'][0] ?? '',
                    'region' => $country['region'] ?? '',
                    'population' => $country['population'] ?? 0,
                    'flag' => $country['flags']['png'] ?? '',
                ];
            }, $countries);
            
            return $this->respond([
                'status' => 'success',
                'message' => 'Data berhasil diambil dari REST Countries API',
                'data' => $data
            ], 200);
        } catch (\Exception $e) {
            return $this->fail('Gagal mengambil data: ' . $e->getMessage());
        }
    }

    /**
     * Konsumsi API dan tampilkan di view
     * GET /api/public/countries-view
     */
    public function getCountriesView()
    {
        $client = \Config\Services::curlrequest();
        
        try {
            $response = $client->get('https://restcountries.com/v3.1/all?fields=name,capital,region,population,flags');
            $countries = json_decode($response->getBody(), true);
            
            $data = [
                'title' => 'Daftar Negara (dari API Publik)',
                'countries' => array_slice($countries, 0, 20), // Ambil 20 pertama saja
            ];
            
            return view('api/countries', $data);
        } catch (\Exception $e) {
            return redirect()->back()
                ->with('error', 'Gagal mengambil data: ' . $e->getMessage());
        }
    }
}
```

### 3.3 Tambahkan Routes

**File:** `app/Config/Routes.php`

```php
// Public API Routes
$routes->group('api/public', ['namespace' => 'App\Controllers\Api'], function($routes) {
    $routes->get('countries', 'PublicApiController::getCountries');
    $routes->get('countries-view', 'PublicApiController::getCountriesView');
});
```

### 3.4 Buat View untuk Menampilkan Data dari API Publik

**File:** `app/Views/api/countries.php`

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
                <p class="text-muted">Data diambil dari REST Countries API</p>

                <div class="row">
                    <?php foreach ($countries as $country): ?>
                        <div class="col-md-4 mb-4">
                            <div class="card">
                                <img src="<?= esc($country['flags']['png']) ?>" class="card-img-top" alt="Flag" style="height: 150px; object-fit: cover;">
                                <div class="card-body">
                                    <h5 class="card-title"><?= esc($country['name']['common']) ?></h5>
                                    <p class="card-text">
                                        <strong>Ibu Kota:</strong> <?= esc($country['capital'][0] ?? '-') ?><br>
                                        <strong>Region:</strong> <?= esc($country['region']) ?><br>
                                        <strong>Populasi:</strong> <?= number_format($country['population']) ?>
                                    </p>
                                </div>
                            </div>
                        </div>
                    <?php endforeach; ?>
                </div>
            </div>
        </div>
    </div>
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```

### 3.5 Konsumsi API Publik dengan JavaScript Fetch

**File:** `app/Views/api/countries_js.php`

```php
<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Countries API - JavaScript</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
    <div class="container mt-5">
        <div class="row">
            <div class="col-md-12">
                <h1 class="mb-4">Daftar Negara (JavaScript Fetch)</h1>
                <button onclick="loadCountries()" class="btn btn-primary mb-3">Load Countries</button>
                
                <div id="loading" class="d-none">Loading...</div>
                <div id="countries-container" class="row"></div>
            </div>
        </div>
    </div>

    <script>
        async function loadCountries() {
            const container = document.getElementById('countries-container');
            const loading = document.getElementById('loading');
            
            container.innerHTML = '';
            loading.classList.remove('d-none');
            
            try {
                // Konsumsi API publik langsung dari browser
                const response = await fetch('https://restcountries.com/v3.1/all?fields=name,capital,region,population,flags');
                const countries = await response.json();
                
                // Ambil 20 pertama
                const limitedCountries = countries.slice(0, 20);
                
                limitedCountries.forEach(country => {
                    const card = document.createElement('div');
                    card.className = 'col-md-4 mb-4';
                    card.innerHTML = `
                        <div class="card">
                            <img src="${country.flags.png}" class="card-img-top" alt="Flag" style="height: 150px; object-fit: cover;">
                            <div class="card-body">
                                <h5 class="card-title">${country.name.common}</h5>
                                <p class="card-text">
                                    <strong>Ibu Kota:</strong> ${country.capital?.[0] || '-'}<br>
                                    <strong>Region:</strong> ${country.region}<br>
                                    <strong>Populasi:</strong> ${country.population.toLocaleString()}
                                </p>
                            </div>
                        </div>
                    `;
                    container.appendChild(card);
                });
            } catch (error) {
                console.error('Error:', error);
                container.innerHTML = '<div class="alert alert-danger">Gagal mengambil data</div>';
            } finally {
                loading.classList.add('d-none');
            }
        }
    </script>
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```

---

## 4. API Response Format

### Format Standar Response

**Success Response:**
```json
{
  "status": "success",
  "message": "Data berhasil diambil",
  "data": [...]
}
```

**Error Response:**
```json
{
  "status": "error",
  "message": "Pesan error",
  "errors": {...}
}
```

---

## 5. Testing API

### Test dengan Browser
- GET: Buka langsung di browser
  - http://localhost/device-management/public/api/devices
  - http://localhost/device-management/public/api/device-types

### Test dengan Postman
- Import collection atau buat request manual
- Test semua endpoint: GET, POST, PUT, DELETE

### Test dengan cURL
- Gunakan command di bagian 1.5

---

## 6. Best Practices API

1. **Gunakan ResponseTrait** untuk format response konsisten
2. **Validasi Input** sebelum insert/update
3. **Error Handling** yang baik
4. **HTTP Status Code** yang tepat (200, 201, 400, 404, 500)
5. **Documentation** API endpoint
6. **Rate Limiting** untuk mencegah abuse (opsional)
7. **Authentication** jika API perlu di-secure (opsional)

---

## Selesai!

Setelah API selesai, lanjut ke: **praktek_ai.md**

