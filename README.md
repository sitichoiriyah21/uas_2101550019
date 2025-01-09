# uas_2101550019
UAS Web Service



#### Nama: Siti Choiriyah
#### NIM: 21.01.55.0019
#### Kuliner 
#### Tabel: menu makanan khas Purwodadi
- id (PK)
- name
- category
- price
- portion


## LANGKAH-LANGKAH


1. Buka XAMPP lalu klik start pada Apache dan mySQL

Salin dan tempel kode berikut dalam `menus_api.php`:

```php
<?php
header("Content-Type: application/json; charset=UTF-8");
header("Access-Control-Allow-Origin: *");
header("Access-Control-Allow-Methods: GET, POST, PUT, DELETE");
header("Access-Control-Allow-Headers: Content-Type, Access-Control-Allow-Headers, Authorization, X-Requested-With");

$method = $_SERVER['REQUEST_METHOD'];
$request = [];

if (isset($_SERVER['PATH_INFO'])) {
    $request = explode('/', trim($_SERVER['PATH_INFO'],'/'));
}

function getConnection() {
    $host = 'localhost';
    $db   = 'culinary';
    $user = 'root';
    $pass = ''; // Ganti dengan password MySQL Anda jika ada
    $charset = 'utf8mb4';

    $dsn = "mysql:host=$host;dbname=$db;charset=$charset";
    $options = [
        PDO::ATTR_ERRMODE            => PDO::ERRMODE_EXCEPTION,
        PDO::ATTR_DEFAULT_FETCH_MODE => PDO::FETCH_ASSOC,
        PDO::ATTR_EMULATE_PREPARES   => false,
    ];
    try {
        return new PDO($dsn, $user, $pass, $options);
    } catch (\PDOException $e) {
        throw new \PDOException($e->getMessage(), (int)$e->getCode());
    }
}

function response($status, $data = NULL) {
    header("HTTP/1.1 " . $status);
    if ($data) {
        echo json_encode($data);
    }
    exit();
}

$db = getConnection();

switch ($method) {
    case 'GET':
        if (!empty($request) && isset($request[0])) {
            $id = $request[0];
            $stmt = $db->prepare("SELECT * FROM menus WHERE id = ?");
            $stmt->execute([$id]);
            $menu = $stmt->fetch();
            if ($menu) {
                response(200, $menu);
            } else {
                response(404, ["message" => "menu not found"]);
            }
        } else {
            $stmt = $db->query("SELECT * FROM menus");
            $menus = $stmt->fetchAll();
            response(200, $menus);
        }
        break;
    
    case 'POST':
        $data = json_decode(file_get_contents("php://input"));
        if (!isset($data->name) || !isset($data->category) || !isset($data->price) || !isset($data->porsi)) {
            response(400, ["message" => "Missing required fields"]);
        }
        $sql = "INSERT INTO menus (name, category, price, porsi) VALUES (?, ?, ?, ?)";
        $stmt = $db->prepare($sql);
        if ($stmt->execute([$data->name, $data->category, $data->price, $data->porsi])) {
            response(201, ["message" => "Menu created", "id" => $db->lastInsertId()]);
        } else {
            response(500, ["message" => "Failed to create menu"]);
        }
        break;
    
    case 'PUT':
        if (empty($request) || !isset($request[0])) {
            response(400, ["message" => "menu ID is required"]);
        }
        $id = $request[0];
        $data = json_decode(file_get_contents("php://input"));
        if (!isset($data->name) || !isset($data->category) || !isset($data->price) || !isset($data->porsi)) {
            response(400, ["message" => "Missing required fields"]);
        }
        $sql = "UPDATE menus SET name = ?, category = ?, price = ?, porsi = ? WHERE id = ?";
        $stmt = $db->prepare($sql);
        if ($stmt->execute([$data->name, $data->category, $data->price, $data->porsi, $id])) {
            response(200, ["message" => "menu updated"]);
        } else {
            response(500, ["message" => "Failed to update menu"]);
        }
        break;
    
    case 'DELETE':
        if (empty($request) || !isset($request[0])) {
            response(400, ["message" => "Menu ID is required"]);
        }
        $id = $request[0];
        $sql = "DELETE FROM menus WHERE id = ?";
        $stmt = $db->prepare($sql);
        if ($stmt->execute([$id])) {
            response(200, ["message" => "menu deleted"]);
        } else {
            response(500, ["message" => "Failed to delete menu"]);
        }
        break;
    
    default:
        response(405, ["message" => "Method not allowed"]);
        break;
}
?>
```


2. Buat folder baru bernama `klien_menu` di dalam direktori `htdocs` XAMPP
3. Buat file bernama `login.php`, `dashboard.php`, `config.php`, `logout.php`, `index.php`, dan `index.css` dalam folder `klien_menu`
4. Berikut adalah kode masing-masing file


### login.php
```php
<?php
session_start();
require_once 'config.php';

if(isset($_POST['login'])) {
    $username = $_POST['username'];
    $password = $_POST['password'];
    
    
    $query = "SELECT * FROM users WHERE username='$username' AND password=PASSWORD('$password')";
    $result = mysqli_query($conn, $query);
    
    if(mysqli_num_rows($result) == 1) {
        $row = mysqli_fetch_assoc($result);
        $_SESSION['user_id'] = $row['id'];
        $_SESSION['username'] = $row['username'];
        $_SESSION['name'] = $row['name'];
            
        header("Location: dashboard.php");
        exit();
    } else {
        $error = "Username atau Password salah!";
    }
}
?>

<!DOCTYPE html>
<html>
<head>
    <title>Login</title>
    <style>
        body {
            background-color: #e6e6fa; 
            font-family: Arial, sans-serif; 
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh; 
            margin: 0;
            color: #333; 
        }
        .login-container {
            background-color: rgba(255, 255, 255, 0.9); 
            padding: 20px;
            border-radius: 8px; 
            box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2); 
            width: 300px; 
        }
        h1 {
            text-align: center; 
            color: rgb(180, 142, 166); 
            margin-bottom: 20px; 
        }
        p {
            margin: 10px 0; 
        }
        input[type="text"],
        input[type="password"] {
            width: 100%; 
            padding: 10px; 
            border: 1px solid #ccc; 
            border-radius: 5px; 
            box-sizing: border-box; 
            font-size: 14px; 
        }
        input[type="submit"],
        input[type="reset"] {
            background-color: rgb(180, 142, 166); 
            color: white; 
            border: none; 
            padding: 10px; 
            border-radius: 5px; 
            cursor: pointer;
            width: 48%; 
            transition: background-color 0.3s; 
            margin-top: 10px; 
        }
        input[type="submit"]:hover,
        input[type="reset"]:hover {
            background-color: rgb(150, 120, 140); 
        }
        .button-container {
            display: flex; 
            justify-content: space-between; 
        }
        .error {
            color: red; 
            text-align: center; 
        }
    </style>
</head>
<body>
<div class="login-container">
        <h1>Login Form</h1> 
        <?php if(isset($error)) { ?>
            <p class="error"><?php echo $error; ?></p>
        <?php } ?>
    
    <form action="" method="POST">
        <p>
            <label for="username">Username:</label>
            <input type="text" name="username" required placeholder=" Masukkan Username">
        </p>
        <p>
            <label for="password">Password:</label>
            <input type="password" name="password" required placeholder=" Masukkan Password"> 
        </p>
        <div class="button-container">
            <input type="submit" name="login" value="Login">
            <input type="reset" value="Reset"> 
        </div>
    </form>
</div>
</body>
</html>
```
![login](https://github.com/user-attachments/assets/0aad608d-2981-444c-a47e-65b94455384b)



### dashboard.php
```php
<?php
session_start();


if(!isset($_SESSION['user_id'])) {
    header("Location: login.php");
    exit();
}
?>

<!doctype html>
<html lang="en">
  <head>
  <!-- Required meta tags -->
  <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

    <!-- Bootstrap CSS -->
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.0/css/bootstrap.min.css" integrity="sha384-9aIt2nRpC12Uk9gS9baDl411NQApFmC26EwAOH8WgZl5MYYxFfc+NcPb1dKGj7Sk" crossorigin="anonymous">
    <link rel="stylesheet" type="text/css" href="css/bootstrap.min.css">
    <link rel="stylesheet" type="text/css" href="index.css">
    <link rel="stylesheet" type="text/css" href="fontawesome/css/all.min.css">

    <title>Arek-arek Lesehan</title>
    <link rel="shortcut icon" type="image" href="img/y.png">
  </head>
<body>

        <!-- Jumbotron -->
      <div class="jumbotron jumbotron-fluid text-center">
        <div class="container">
          <h1 class="display-4"><span class="font-weight-bold"> Arek-arek</span></h1>
          <hr>
          <p class="lead font-weight-bold">Kuliner Ndeso</p>
        </div>
      </div>
  <!-- Akhir Jumbotron -->

  <!-- Navbar -->
      <nav class="navbar navbar-expand-lg bg-dark">
        <div class="container">
        <a class="navbar-brand text-white" href="admin.php" <img src="img/y.png" width="50px"><strong></strong> Arek-arek</a>
        <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarNav" aria-controls="navbarNav" aria-expanded="false" aria-label="Toggle navigation">
          <span class="navbar-toggler-icon"></span>
        </button>

        <div class="collapse navbar-collapse" id="navbarNav">
          <ul class="navbar-nav ml-auto">
            <li class="nav-item">
              <a class="nav-link mr-4" href="index.php">BERANDA</a>
            </li>
            <li class="nav-item">
              <a class="nav-link mr-4" href="dashboard.php">DAFTAR MENU</a>
            </li>
            <li class="nav-item">
              <a class="nav-link mr-4" href="pesanan.php">PESANAN</a>
            </li>
            <li class="nav-item">
              <a class="nav-link mr-4" href="logout.php">LOGOUT</a>
            </li>
          </ul>
        </div>
       </div> 
      </nav>
  <!-- Akhir Navbar -->
  <div class="all-content" class="container py-4">
    <h1 class="container py-4", class="text-white" >Daftar Menu</h1>
    
    <div class="row mb-3" class="container py-4">
        <div class="col" >
            <input type="text" id="searchInput" class="form-control" placeholder="Cari berdasarkan ID">
        </div>
        <div class="col-auto">
            <button onclick="searchBook()" class="btn btn-primary">Cari</button>
        </div>
        <div class="col-auto">
            <button type="button" class="btn btn-success" data-bs-toggle="modal" data-bs-target="#bookModal">
                Tambah Menu
            </button>
        </div>
    </div>
    <table class="table table-bordered table-striped" id="table" >
        <thead class="bg-info text-white" class="container py-4">
            <tr class="container py-4">
                <th>ID</th>
                <th>Name</th>
                <th>Category</th>
                <th>Price</th>
                <th>Porsi</th>
                <th>Aksi</th>
            </tr>
        </thead>
        <tbody class="text-white" id="bookList">
        </tbody>
    </table>

    <!-- Modal for Add/Edit Book -->
    <div class="modal fade" id="bookModal" tabindex="-1">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <h5 class="modal-title" id="modalTitle">Tambah Menu</h5>
                    <button type="button" class="btn-close" data-bs-dismiss="modal"></button>
                </div>
                <div class="modal-body">
                    <form id="bookForm">
                        <input type="hidden" id="bookId">
                        <div class="mb-3">
                            <label for="name" class="form-label">Name</label>
                            <input type="text" class="form-control" id="name" required>
                        </div>
                        <div class="mb-3">
                            <label for="category" class="form-label">Category</label>
                            <input type="text" class="form-control" id="category" required>
                        </div>
                        <div class="mb-3">
                            <label for="price" class="form-label">Price</label>
                            <input type="number" class="form-control" id="price" required>
                        </div>
                        <div class="mb-3">
                            <label for="porsi" class="form-label">Porsi</label>
                            <input type="number" class="form-control" id="porsi" required>
                        </div>
                    </form>
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Batal</button>
                    <button type="button" class="btn btn-primary" onclick="saveBook()">Simpan</button>
                </div>
            </div>
        </div>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/js/bootstrap.bundle.min.js"></script>
    <script>
        const API_URL = 'http://localhost/rest_menus/menus_api.php';
        let bookModal;

        document.addEventListener('DOMContentLoaded', function() {
            bookModal = new bootstrap.Modal(document.getElementById('bookModal'));
            loadBooks();
        });

        function loadBooks() {
            fetch(API_URL)
                .then(response => response.json())
                .then(books => {
                    const bookList = document.getElementById('bookList');
                    bookList.innerHTML = '';
                    books.forEach(book => {
                        bookList.innerHTML += `
                            <tr>
                                <td>${book.id}</td>
                                <td>${book.name}</td>
                                <td>${book.category}</td>
                                <td>${book.price}</td>
                                <td>${book.porsi}</td>
                                <td class="btn-group-action">
                                    <button class="btn btn-sm btn-warning me-1" onclick="editBook(${book.id})">Edit</button>
                                    <button class="btn btn-sm btn-danger" onclick="deleteBook(${book.id})">Hapus</button>
                                </td>
                            </tr>
                        `;
                    });
                })
                .catch(error => alert('Error loading menus: ' + error));
        }

        function searchBook() {
            const id = document.getElementById('searchInput').value;
            if (!id) {
                loadBooks();
                return;
            }
            
            fetch(`${API_URL}/${id}`)
                .then(response => response.json())
                .then(book => {
                    const bookList = document.getElementById('bookList');
                    if (book.message) {
                        alert('Menu not found');
                        return;
                    }
                    bookList.innerHTML = `
                        <tr>
                            <td>${book.id}</td>
                                <td>${book.name}</td>
                                <td>${book.category}</td>
                                <td>${book.price}</td>
                                <td>${book.porsi}</td>
                            <td class="btn-group-action">
                                <button class="btn btn-sm btn-warning me-1" onclick="editBook(${book.id})">Edit</button>
                                <button class="btn btn-sm btn-danger" onclick="deleteBook(${book.id})">Hapus</button>
                            </td>
                        </tr>
                    `;
                })
                .catch(error => alert('Error searching menu: ' + error));
        }

        function editBook(id) {
            fetch(`${API_URL}/${id}`)
                .then(response => response.json())
                .then(book => {
                    document.getElementById('bookId').value = book.id;
                    document.getElementById('name').value = book.name;
                    document.getElementById('category').value = book.category;
                    document.getElementById('price').value = book.price;
                    document.getElementById('porsi').value = book.porsi;
                    document.getElementById('modalTitle').textContent = 'Edit Menu';
                    bookModal.show();
                })
                .catch(error => alert('Error loading menu details: ' + error));
        }

        function deleteBook(id) {
            if (confirm('Are you sure you want to delete this menu?')) {
                fetch(`${API_URL}/${id}`, {
                    method: 'DELETE'
                })
                .then(response => response.json())
                .then(data => {
                    alert('Menu deleted successfully');
                    loadBooks();
                })
                .catch(error => alert('Error deleting menu: ' + error));
            }
        }
        

        function saveBook() {
            const bookId = document.getElementById('bookId').value;
            const bookData = {
                name: document.getElementById('name').value,
                category: document.getElementById('category').value,
                price: document.getElementById('price').value,
                porsi: document.getElementById('porsi').value
            };

            const method = bookId ? 'PUT' : 'POST';
            const url = bookId ? `${API_URL}/${bookId}` : API_URL;

            fetch(url, {
                method: method,
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify(bookData)
            })
            .then(response => response.json())
            .then(data => {
                alert(bookId ? 'menu updated successfully' : 'Menu added successfully');
                bookModal.hide();
                loadBooks();
                resetForm();
            })
            .catch(error => alert('Error saving menu: ' + error));
        }
        

        function resetForm() {
            document.getElementById('bookId').value = '';
            document.getElementById('bookForm').reset();
            document.getElementById('modalTitle').textContent = 'Tambah Menu';
        }

        // Reset form when modal is closed
        document.getElementById('bookModal').addEventListener('hidden.bs.modal', resetForm);
    </script>

    <!-- Awal Footer -->
    <hr class="footer">
      <div class="container">
        <div class="row footer-body">
          <div class="col-md-6">
          <div class="copyright">
            <strong>Copyright</strong> <i class="far fa-copyright"></i> 2024 -  Designed by me</p>
          </div>
          </div>

          <div class="col-md-6 d-flex justify-content-end">
          <div class="icon-contact">
          <label class="font-weight-bold">Follow Us </label>
          <a href="#"><img src="img/fb.png" class="mr-3 ml-4" data-toggle="tooltip" title="Facebook"></a>
          <a href="#"><img src="img/ig.png" class="mr-3" data-toggle="tooltip" title="Instagram"></a>
          <a href="#"><img src="img/twitter.png" class="" data-toggle="tooltip" title="Twitter"></a>
          </div>
          </div>
        </div>
      </div>
  <!-- Akhir Footer -->


    
  <a href="logout.php">Logout</a>    
</body>
</html>
```
![dashboard](https://github.com/user-attachments/assets/cf62e59f-34a8-40d6-a300-a590fe84e158)
![tabel](https://github.com/user-attachments/assets/83445986-7670-473c-bfb8-2095ff20a491)


### config.php
```php
<?php
$host = "localhost";
$dbuser = "root";
$dbpass = "";
$dbname = "culinary";

$conn = mysqli_connect($host, $dbuser, $dbpass, $dbname);
if (!$conn) {
    die("Connection failed: " . mysqli_connect_error());
}
?>
```

### logout.php
```php
<?php
session_start();

// Hapus semua data session
session_destroy();

// Redirect ke halaman login
header("Location: login.php");
exit();
?>
```

### index.css
```php
html {
	scroll-behavior: smooth;
}

body {
	font-family: sans-serif;
	font-size: 16px;
}
.all-content{
    background: rgb(180, 142, 166);
}
/* navbar */
#navbar{
    background-color: #020228;
    padding-left: 30px;
    font-weight: bold;
}

/*Jumbotron*/
.jumbotron {
	background-image: url(img/b3.jpg);
	background-size: cover;
	height: 440px;
	width: 100%;
	color: rgb(180, 142, 166);
}

.jumbotron .display-4 {
	margin-top: 30px;
}

.jumbotron p {
	font-size: 23px;
}

.jumbotron hr {
	border-color: rgb(180, 142, 166);
	width: 400px;
	border-width: 3px;
}
/*Akhir Jumbotron*/

/*Navbar*/
.navbar {
	margin-top: -32px;
}

.navbar .navbar-nav .nav-link {
	color: rgb(180, 142, 166);
}

.navbar .navbar-nav .nav-link:hover {
	color: whitesmoke;
}

.navbar .navbar-brand  {
	font-size: 30px;
}
/*Akhir Navbar*/

/*Footer*/
.footer {
	margin-top: 150px;
	border-width: 3px;
}

.footer-body {
	margin-top: 60px;
	margin-bottom: 50px;
}
/*Akhir Footer*/

/*Home*/
.judul {
	margin-bottom: 120px;
}

.card-img:hover {
	box-shadow: 2px 2px 10px rgba(0,0,0,0.4);	
}

/*Akhir Home*/
```

### index.php
```php
<?php
session_start();


if(!isset($_SESSION['user_id'])) {
    header("Location: login.php");
    exit();
}
?>

<!doctype html>
<html lang="en">
  <head>
  <!-- Required meta tags -->
  <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

    <!-- Bootstrap CSS -->
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.0/css/bootstrap.min.css" integrity="sha384-9aIt2nRpC12Uk9gS9baDl411NQApFmC26EwAOH8WgZl5MYYxFfc+NcPb1dKGj7Sk" crossorigin="anonymous">
    <link rel="stylesheet" type="text/css" href="css/bootstrap.min.css">
    <link rel="stylesheet" type="text/css" href="index.css">
    <link rel="stylesheet" type="text/css" href="fontawesome/css/all.min.css">

    <title>Arek-arek Lesehan</title>
    <link rel="shortcut icon" type="image" href="img/y.png">
  </head>
<body>

        <!-- Jumbotron -->
      <div class="jumbotron jumbotron-fluid text-center">
        <div class="container">
          <h1 class="display-4"><span class="font-weight-bold"> Arek-arek</span></h1>
          <hr>
          <p class="lead font-weight-bold">Kuliner Ndeso</p>
        </div>
      </div>
  <!-- Akhir Jumbotron -->

  <!-- Navbar -->
      <nav class="navbar navbar-expand-lg bg-dark">
        <div class="container">
        <a class="navbar-brand text-white" href="admin.php" <img src="img/y.png" width="50px"><strong></strong> Arek-arek</a>
        <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarNav" aria-controls="navbarNav" aria-expanded="false" aria-label="Toggle navigation">
          <span class="navbar-toggler-icon"></span>
        </button>

        <div class="collapse navbar-collapse" id="navbarNav">
          <ul class="navbar-nav ml-auto">
            <li class="nav-item">
              <a class="nav-link mr-4" href="index.php">BERANDA</a>
            </li>
            <li class="nav-item">
              <a class="nav-link mr-4" href="dashboard.php">DAFTAR MENU</a>
            </li>
            <li class="nav-item">
              <a class="nav-link mr-4" href="pesanan.php">PESANAN</a>
            </li>
            <li class="nav-item">
              <a class="nav-link mr-4" href="logout.php">LOGOUT</a>
            </li>
          </ul>
        </div>
       </div> 
      </nav>
  <!-- Akhir Navbar -->
  <!-- Menu -->    
  <div class="container">
        <div class="judul text-center mt-5">
          <h3 class="font-weight-bold"> Arek-arek</h3>
          <h5>Grobogan
          <br>Buka Jam <strong>08:00-20:00</strong></h5>
        </div>

        <div class="row mb-5 mt-5 ">
          <div class="col-md-6 d-flex justify-content-end">
            <div class="card bg-dark text-white border-light">
              <img src="img/b1.png" class="card-img" alt="Lihat Daftar Menu">
              <div class="card-img-overlay mt-5 text-center">
               <a href="dashboard.php" class="btn btn-danger">Lihat Menu</a>
              </div>
            </div>
          </div>

          <div class="col-md-6 d-flex justify-content-start">
            <div class="card bg-dark text-white border-light">
              <img src="img/b1.png" class="card-img" alt="Lihat Pesanan">
              <div class="card-img-overlay mt-5 text-center">
               <a href="pesanan.php" class="btn btn-danger">Lihat Pesanan</a>
              </div>
            </div>
          </div>
        </div>
      </div>
  <!-- Akhir Menu -->

    <!-- Awal Footer -->
    <hr class="footer">
      <div class="container">
        <div class="row footer-body">
          <div class="col-md-6">
          <div class="copyright">
            <strong>Copyright</strong> <i class="far fa-copyright"></i> 2024 -  Designed by me</p>
          </div>
          </div>

          <div class="col-md-6 d-flex justify-content-end">
          <div class="icon-contact">
          <label class="font-weight-bold">Follow Us </label>
          <a href="#"><img src="img/fb.png" class="mr-3 ml-4" data-toggle="tooltip" title="Facebook"></a>
          <a href="#"><img src="img/ig.png" class="mr-3" data-toggle="tooltip" title="Instagram"></a>
          <a href="#"><img src="img/twitter.png" class="" data-toggle="tooltip" title="Twitter"></a>
          </div>
          </div>
        </div>
      </div>
  <!-- Akhir Footer --> 
</body>
</html>
```
![index and beranda](https://github.com/user-attachments/assets/ddcceb0f-1175-49bf-95d8-70c3e08ad93a)
![footer](https://github.com/user-attachments/assets/0c5e0949-ff4f-405e-a104-a84261540614)


### Berikut adalah hasil fungsi setiap tombol
#### Tombol Cari
![cari](https://github.com/user-attachments/assets/ec6ee8bf-d38f-4620-b8f2-e0a933d57796)

#### Tombol Tambah
![tambah2](https://github.com/user-attachments/assets/85c7c7b6-1b62-4e73-b11c-5926b962bfff)
![hasil tambah](https://github.com/user-attachments/assets/50ddd391-96e5-4beb-b99d-4ddb16a65fca)

#### Tombol Edit/update
![edit update](https://github.com/user-attachments/assets/127c133e-7ffe-4536-8912-e0fe43509dba)

#### Tombol Hapus
![delete](https://github.com/user-attachments/assets/4384193f-128b-4d09-ba1d-2d8c0278d5a2)


