# UAS PBF - 230102040 
pada uas PBF hari ini membuat seperti sistem peminjaman buku di perpustakaan 
dengan database 
```
CREATE DATABASE db_perpus_[NIM_ANDA];
USE db_perpus_[NIM_ANDA];

CREATE TABLE buku (
  id INT AUTO_INCREMENT PRIMARY KEY,
  judul VARCHAR(100),
  pengarang VARCHAR(100),
  penerbit VARCHAR(100),
  tahun_terbit INT
);

CREATE TABLE peminjaman (
  id INT AUTO_INCREMENT PRIMARY KEY,
  nama_peminjam VARCHAR(100),
  judul_buku VARCHAR(100),
  tanggal_pinjam DATE,
  tanggal_kembali DATE
);
```
setelah kita punya database nya kita lanjutkan ke bagian backend 

## BACKEND ( CI )
Ci atau CodeIgniter adalah framework PHP yang ringan dan cepat, digunakan untuk membangun aplikasi web. 
CI menggunakan pola MVC (Model-View-Controller), yang membantu memisahkan logika program dari tampilan (UI) dan pengolahan data.

Komponen Utama MVC:
- Model → Mengelola data (berhubungan dengan database).
- View → Menampilkan data ke pengguna (HTML, CSS).
- Controller → Menghubungkan model dan view; logika utama aplikasi.

- Langkah awal saat menjadi Backend 
1. Buat Proyek Ci baru 
```
composer create-project codeigniter4/appstarter backend_perpustakaan
```
2. edit env agar terarahkan ke database kita 
```
database.default.hostname = localhost
database.default.database = db_perpus_230102040
database.default.username = root
database.default.password =
```
3. Membuat Model Buku dan Peminjaman 
- BukuModel.php 
```
<?php namespace App\Models;
use CodeIgniter\Model;

class BukuModel extends Model {
    protected $table = 'buku';
    protected $primaryKey = 'id';
    protected $allowedFields = ['judul', 'pengarang', 'penerbit', 'tahun_terbit'];
    protected $useTimestamps = false;
}
```

- PeminjamanModel.php
```
<?php namespace App\Models;
use CodeIgniter\Model;

class PeminjamanModel extends Model {
    protected $table = 'peminjaman';
    protected $primaryKey = 'id';
    protected $allowedFields = ['nama_peminjam', 'judul_buku', 'tanggal_pinjam', 'tanggal_kembali'];
    protected $useTimestamps = false;
}
```

4. Membuat Controllers
- BukuController.php 
```
<?php 

namespace App\Controllers;
use CodeIgniter\RESTful\ResourceController;


class Buku extends ResourceController {
    protected $modelName = 'App\Models\BukuModel';
    protected $format    = 'json';

    public function index()
    {
        return $this->respond($this->model->findAll());
    }

    public function show($id = null)
    {
        $data = $this->model->find($id);
        if (!$data) {
            return $this->failNotFound("Data $id tidak ditemukan");
        }

        return $this->respond($data);
    }

    public function create()
    {
        $json = $this->request->getJSON();

        $data = [
            // 'id' => $json->id,
            'id' => $json->id,
            'judul' => $json->judul,
            'pengarang' => $json->pengarang,
            'penerbit' => $json->penerbit,
            'tahun_terbit' => $json->tahun_terbit,
        ];

        $this->model->insert($data);

        return $this->respondCreated(['message' => 'Data berhasil ditambahkan']);
    }

    public function update($id = null)
    {
        $json = $this->request->getJSON();

        if (!$json) {
            return $this->fail('Request body harus berupa JSON yang valid', 400);
        }

        $existing = $this->model->find($id);
        if (!$existing) {
            return $this->failNotFound("Data $id tidak ditemukan");
        }

        $data = [
            'id' => $json->id,
            'judul' => $json->judul,
            'pengarang' => $json->pengarang,
            'penerbit' => $json->penerbit,
            'tahun_terbit' => $json->tahun_terbit,
        ];

        $this->model->update($id, $data);

        return $this->respond(['message' => 'Data berhasil diperbarui']);
    }

    public function delete($id = null)
    {
        try {
            $data = $this->model->find($id);

            if (!$data) {
                return $this->failNotFound("Data $id tidak ditemukan");
            }

            if ($this->model->delete($id)) {
                return $this->respondDeleted(['message' => "Data $id berhasil dihapus"]);
            } else {
                return $this->failServerError("Gagal menghapus data $id");
            }

        } catch (\Exception $e) {
            return $this->failServerError("Terjadi kesalahan: " . $e->getMessage());
}
}
}
```

- PeminjamanController.php
```
<?php 

namespace App\Controllers;
use CodeIgniter\RESTful\ResourceController;


class Peminjaman extends ResourceController {
    protected $modelName = 'App\Models\PeminjamanModel';
    protected $format    = 'json';

    public function index()
    {
        return $this->respond($this->model->findAll());
    }

    public function show($id = null)
    {
        $data = $this->model->find($id);
        if (!$data) {
            return $this->failNotFound("Data $id tidak ditemukan");
        }
        return $this->respond($data);
    }

    public function create()
    {
        $json = $this->request->getJSON();

        $data = [
            // 'id' => $json->id,
            'id' => $json->id,
            'nama_peminjaman' => $json->nama_peminjaman,
            'judul_buku' => $json->judul_buku,
            'tanggal_peminjaman' => $json->tanggal_peminjaman,
            'tanggal_kembali' => $json->tanggal_kembali,

        ];

        $this->model->insert($data);

        return $this->respondCreated(['message' => 'Data berhasil ditambahkan']);
    }

    public function update($id = null)
    {
        $json = $this->request->getJSON();

        if (!$json) {
            return $this->fail('Request body harus berupa JSON yang valid', 400);
        }

        $existing = $this->model->find($id);
        if (!$existing) {
            return $this->failNotFound("Data $id tidak ditemukan");
        }

        $data = [
            'id' => $json->id,
            'nama_peminjaman' => $json->nama_peminjaman,
            'judul_buku' => $json->judul_buku,
            'tanggal_peminjaman' => $json->tanggal_peminjaman,
            'tanggal_kembali' => $json->tanggal_kembali,
        ];

        $this->model->update($id, $data);

        return $this->respond(['message' => 'Data berhasil diperbarui']);
    }

    public function delete($id = null)
    {
        try {
            $data = $this->model->find($id);

            if (!$data) {
                return $this->failNotFound("Data $id tidak ditemukan");
            }

            if ($this->model->delete($id)) {
                return $this->respondDeleted(['message' => "Data $id berhasil dihapus"]);
            } else {
                return $this->failServerError("Gagal menghapus data $id");
            }

        } catch (\Exception $e) {
            return $this->failServerError("Terjadi kesalahan: " . $e->getMessage());
}
}
}
```

5. Routing 
```
$routes->resource('buku');
$routes->resource('peminjaman');
```
6. Pada Database.php tambahkan Routes 
```
public array $default = [
        'DSN'          => '',
        'hostname'     => 'localhost',
        'username'     => 'root',
        'password'     => '',
        'database'     => 'db_perpus_230102040',
```
7. Lalu Test Postman 
```
http://localhost:8080/buku
http://localhost:8080/peminjaman
``` 

## FRONTEND ( LARAVEL ) 
Laravel membantu developer membuat website atau aplikasi web lebih cepat dan aman, karena sudah menyediakan banyak fitur bawaan, seperti:
- Routing (mengatur URL dan halaman yang dituju)
- Blade (template untuk tampilan web)
- Validasi data
- dan banyak lagi.

# Tahapan Install Laravel 
1. Persiapan Awal
Pastikan perangkat sudah terinstal:
- PHP 
- Composer
- MySQL/MariaDB
- Git

2. Membuat Project Laravel 
- Masuk ke Cmd lalu ketik
```
cd..
cd..
cd laragon
cd www
composer create-project laravel/laravel frontend-uas-230102040 ( sesuaikan dengan nim ) 
```
- Masuk Folder yang sudah di buat 
```
cd frontend-uas-230102040
```





































<p align="center"><a href="https://laravel.com" target="_blank"><img src="https://raw.githubusercontent.com/laravel/art/master/logo-lockup/5%20SVG/2%20CMYK/1%20Full%20Color/laravel-logolockup-cmyk-red.svg" width="400" alt="Laravel Logo"></a></p>

<p align="center">
<a href="https://github.com/laravel/framework/actions"><img src="https://github.com/laravel/framework/workflows/tests/badge.svg" alt="Build Status"></a>
<a href="https://packagist.org/packages/laravel/framework"><img src="https://img.shields.io/packagist/dt/laravel/framework" alt="Total Downloads"></a>
<a href="https://packagist.org/packages/laravel/framework"><img src="https://img.shields.io/packagist/v/laravel/framework" alt="Latest Stable Version"></a>
<a href="https://packagist.org/packages/laravel/framework"><img src="https://img.shields.io/packagist/l/laravel/framework" alt="License"></a>
</p>

## About Laravel

Laravel is a web application framework with expressive, elegant syntax. We believe development must be an enjoyable and creative experience to be truly fulfilling. Laravel takes the pain out of development by easing common tasks used in many web projects, such as:

- [Simple, fast routing engine](https://laravel.com/docs/routing).
- [Powerful dependency injection container](https://laravel.com/docs/container).
- Multiple back-ends for [session](https://laravel.com/docs/session) and [cache](https://laravel.com/docs/cache) storage.
- Expressive, intuitive [database ORM](https://laravel.com/docs/eloquent).
- Database agnostic [schema migrations](https://laravel.com/docs/migrations).
- [Robust background job processing](https://laravel.com/docs/queues).
- [Real-time event broadcasting](https://laravel.com/docs/broadcasting).

Laravel is accessible, powerful, and provides tools required for large, robust applications.

## Learning Laravel

Laravel has the most extensive and thorough [documentation](https://laravel.com/docs) and video tutorial library of all modern web application frameworks, making it a breeze to get started with the framework.

You may also try the [Laravel Bootcamp](https://bootcamp.laravel.com), where you will be guided through building a modern Laravel application from scratch.

If you don't feel like reading, [Laracasts](https://laracasts.com) can help. Laracasts contains over 2000 video tutorials on a range of topics including Laravel, modern PHP, unit testing, and JavaScript. Boost your skills by digging into our comprehensive video library.

## Laravel Sponsors

We would like to extend our thanks to the following sponsors for funding Laravel development. If you are interested in becoming a sponsor, please visit the Laravel [Patreon page](https://patreon.com/taylorotwell).

### Premium Partners

- **[Vehikl](https://vehikl.com/)**
- **[Tighten Co.](https://tighten.co)**
- **[Kirschbaum Development Group](https://kirschbaumdevelopment.com)**
- **[64 Robots](https://64robots.com)**
- **[Cubet Techno Labs](https://cubettech.com)**
- **[Cyber-Duck](https://cyber-duck.co.uk)**
- **[Many](https://www.many.co.uk)**
- **[Webdock, Fast VPS Hosting](https://www.webdock.io/en)**
- **[DevSquad](https://devsquad.com)**
- **[Curotec](https://www.curotec.com/services/technologies/laravel/)**
- **[OP.GG](https://op.gg)**
- **[WebReinvent](https://webreinvent.com/?utm_source=laravel&utm_medium=github&utm_campaign=patreon-sponsors)**
- **[Lendio](https://lendio.com)**

## Contributing

Thank you for considering contributing to the Laravel framework! The contribution guide can be found in the [Laravel documentation](https://laravel.com/docs/contributions).

## Code of Conduct

In order to ensure that the Laravel community is welcoming to all, please review and abide by the [Code of Conduct](https://laravel.com/docs/contributions#code-of-conduct).

## Security Vulnerabilities

If you discover a security vulnerability within Laravel, please send an e-mail to Taylor Otwell via [taylor@laravel.com](mailto:taylor@laravel.com). All security vulnerabilities will be promptly addressed.

## License

The Laravel framework is open-sourced software licensed under the [MIT license](https://opensource.org/licenses/MIT).
