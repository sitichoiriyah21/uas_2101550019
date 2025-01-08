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

### 1. Persiapan Lingkungan
1. Buka XAMPP lalu klik start pada Apache dan mySQL
2. Buat folder baru bernama `klien_menu` di dalam direktori `htdocs` XAMPP
3. Buat file bernama `login.php`, `dashboard.php`, `config.php`, `logout.php`, `index.css` dalam folder `klien_menu`
4. Berikut adalah kode masing-masing file
- login.php
```php
<?php
session_start();
require_once 'config.php';

if(isset($_POST['login'])) {
    $username = $_POST['username'];
    $password = $_POST['password'];
    
    // Gunakan fungsi PASSWORD() untuk mencocokkan dengan hasil di database
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
            background-color: #e6e6fa; /* Warna latar belakang ungu muda */
            font-family: Arial, sans-serif; /* Font yang lebih modern */
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh; /* Mengisi tinggi viewport */
            margin: 0;
            color: #333; /* Warna teks default */
        }
        .login-container {
            background-color: rgba(255, 255, 255, 0.9); /* Warna latar belakang untuk formulir dengan transparansi */
            padding: 20px;
            border-radius: 8px; /* Sudut yang membulat */
            box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2); /* Bayangan untuk efek kedalaman */
            width: 300px; /* Lebar tetap untuk formulir */
        }
        h1 {
            text-align: center; /* Pusatkan judul */
            color: rgb(180, 142, 166); /* Warna teks judul ungu */
            margin-bottom: 20px; /* Jarak bawah judul */
        }
        p {
            margin: 10px 0; /* Jarak antar elemen */
        }
        input[type="text"],
        input[type="password"] {
            width: 100%; /* Lebar penuh */
            padding: 10px; /* Padding untuk ruang dalam */
            border: 1px solid #ccc; /* Border abu-abu */
            border-radius: 5px; /* Sudut yang membulat */
            box-sizing: border-box; /* Menghitung padding dan border dalam lebar */
            font-size: 14px; /* Ukuran font */
        }
        input[type="submit"],
        input[type="reset"] {
            background-color: rgb(180, 142, 166); /* Warna latar belakang tombol ungu */
            color: white; /* Warna teks tombol */
            border: none; /* Tanpa border */
            padding: 10px; /* Padding untuk tombol */
            border-radius: 5px; /* Sudut yang membulat */
            cursor: pointer; /* Kursor pointer saat hover */
            width: 48%; /* Lebar tombol */
            transition: background-color 0.3s; /* Transisi untuk efek hover */
            margin-top: 10px; /* Jarak atas untuk tombol */
        }
        input[type="submit"]:hover,
        input[type="reset"]:hover {
            background-color: rgb(150, 120, 140); /* Warna saat hover */
        }
        .button-container {
            display: flex; /* Menggunakan flexbox untuk tombol */
            justify-content: space-between; /* Jarak antar tombol */
        }
        .error {
            color: red; /* Warna merah untuk pesan error */
            text-align: center; /* Pusatkan pesan error */
        }
    </style>
</head>
<body>
<div class="login-container">
        <h1>Login Form</h1> <!-- Judul dipindahkan ke sini -->
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
            <input type="password" name="password" required placeholder=" Masukkan Password"> <!-- Simbol untuk menunjukkan input password -->
        </p>
        <div class="button-container">
            <input type="submit" name="login" value="Login">
            <input type="reset" value="Reset"> <!-- Tombol reset ditambahkan -->
        </div>
    </form>
</div>
</body>
</html>
```

- dashboard.php
```php
<?php
session_start();

// Cek apakah user sudah login
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

- config.php
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
- logout.php
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

- index.css
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

6. 
7. *
