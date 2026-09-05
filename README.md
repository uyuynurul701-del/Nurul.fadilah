<?php
session_start();
$host = "localhost";
$user = "root";
$pass = "";
$db   = "kasir_app";

$conn = mysqli_connect($host, $user, $pass);

if (!$conn) {
die("Koneksi gagal");
}

mysqli_query($conn, "CREATE DATABASE IF NOT EXISTS $db");
mysqli_select_db($conn, $db);

// ================================
// TABEL PRODUK
// ================================
mysqli_query($conn, "CREATE TABLE IF NOT EXISTS products(
id INT AUTO_INCREMENT PRIMARY KEY,
name VARCHAR(200),
price INT,
stock INT,
category VARCHAR(100),
expired_date DATE,
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
)");

// ================================
// TABEL PELANGGAN
// ================================
mysqli_query($conn, "CREATE TABLE IF NOT EXISTS customers(
id INT AUTO_INCREMENT PRIMARY KEY,
nama VARCHAR(100),
telepon VARCHAR(20),
alamat TEXT,
email VARCHAR(100)
)");

// ================================
// TABEL TRANSAKSI
// ================================
mysqli_query($conn, "CREATE TABLE IF NOT EXISTS transactions(
id INT AUTO_INCREMENT PRIMARY KEY,
invoice VARCHAR(100),
customer_name VARCHAR(100),
total INT,
pay INT,
change_money INT,
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
)");

// ================================
// TABEL DETAIL TRANSAKSI
// ================================
mysqli_query($conn, "CREATE TABLE IF NOT EXISTS transaction_details(
id INT AUTO_INCREMENT PRIMARY KEY,
invoice VARCHAR(100),
nama_produk VARCHAR(100),
qty INT,
harga INT,
subtotal INT
)");

// ================================
// LOGIN
// ================================
$defaultUser = "admin";
$defaultPass = "12345";

if (isset($_POST['login'])) {

    $username = $_POST['username'];
    $password = $_POST['password'];

    if ($username == $defaultUser && $password == $defaultPass) {
        $_SESSION['login'] = true;
        header("Location:index.php");
    } else {
        $error = "Username atau Password salah";
    }
}

if (isset($_GET['logout'])) {
    session_destroy();
    header("Location:index.php");
}

// ================================
// CRUD PRODUK
// ================================
if (isset($_POST['add_product'])) {

    $name = $_POST['name'];
    $price = $_POST['price'];
    $stock = $_POST['stock'];
    $category = $_POST['category'];
    $expired = $_POST['expired_date'];

    mysqli_query($conn, "INSERT INTO products(name,price,stock,category,expired_date)
    VALUES('$name','$price','$stock','$category','$expired')");

    header("Location:index.php?page=products");
}

if (isset($_POST['update_product'])) {

    $id = $_POST['id'];
    $name = $_POST['name'];
    $price = $_POST['price'];
    $stock = $_POST['stock'];
    $category = $_POST['category'];
    $expired = $_POST['expired_date'];

    mysqli_query($conn, "UPDATE products SET
        name='$name',
        price='$price',
        stock='$stock',
        category='$category',
        expired_date='$expired'
        WHERE id='$id'
    ");

    header("Location:index.php?page=products");
}

if (isset($_GET['delete_product'])) {

    $id = $_GET['delete_product'];

    mysqli_query($conn, "DELETE FROM products WHERE id='$id'");

    header("Location:index.php?page=products");
}

// ================================
// CRUD CUSTOMER
// ================================
if (isset($_POST['add_customer'])) {

    $name = $_POST['name'];
    $phone = $_POST['phone'];
    $address = $_POST['address'];
    $email = $_POST['email'];

    mysqli_query($conn, "INSERT INTO customers(name,phone,address,email)
    VALUES('$name','$phone','$address','$email')");

    header("Location:index.php?page=customers");
}

if (isset($_POST['update_customer'])) {

    $id = $_POST['id'];
    $name = $_POST['name'];
    $phone = $_POST['phone'];
    $address = $_POST['address'];
    $email = $_POST['email'];

    mysqli_query($conn, "UPDATE customers SET
        name='$name',
        phone='$phone',
        address='$address',
        email='$email'
        WHERE id='$id'
    ");

    header("Location:index.php?page=customers");
}

if (isset($_GET['delete_customer'])) {

    $id = $_GET['delete_customer'];

    mysqli_query($conn, "DELETE FROM customers WHERE id='$id'");

    header("Location:index.php?page=customers");
}

// ================================
// KERANJANG
// ================================
if (!isset($_SESSION['cart'])) {
    $_SESSION['cart'] = [];
}

if (isset($_GET['add_cart'])) {

    $id = $_GET['add_cart'];

    $data = mysqli_fetch_assoc(mysqli_query($conn, "SELECT * FROM products WHERE id='$id'"));

    $found = false;

    foreach ($_SESSION['cart'] as $key => $item) {

        if ($item['id'] == $id) {
            $_SESSION['cart'][$key]['qty'] += 1;
            $found = true;
        }
    }

    if (!$found) {
        $_SESSION['cart'][] = [
            'id' => $data['id'],
            'name' => $data['name'],
            'price' => $data['price'],
            'qty' => 1
        ];
    }

    header("Location:index.php?page=cashier");
}

if (isset($_GET['remove_cart'])) {

    $index = $_GET['remove_cart'];

    unset($_SESSION['cart'][$index]);

    $_SESSION['cart'] = array_values($_SESSION['cart']);

    header("Location:index.php?page=cashier");
}

// ================================
// CHECKOUT
// ================================
if (isset($_POST['checkout'])) {

    $customer = $_POST['customer_name'];
    $pay = $_POST['pay'];

    $invoice = "INV" . time();

    $total = 0;

    foreach ($_SESSION['cart'] as $item) {
        $total += $item['price'] * $item['qty'];
    }

    $change = $pay - $total;

    mysqli_query($conn, "INSERT INTO transactions(invoice,customer_name,total,pay,change_money)
VALUES('$invoice','$customer','$total','$pay','$change')");

    foreach ($_SESSION['cart'] as $item) {

        $subtotal = $item['price'] * $item['qty'];

        mysqli_query($conn, "INSERT INTO transaction_details(invoice,product_name,qty,price,subtotal)
        VALUES(
            '$invoice',
            '{$item['name']}',
            '{$item['qty']}',
            '{$item['price']}',
            '$subtotal'
        )");

        mysqli_query($conn, "UPDATE products SET stock = stock - {$item['qty']} WHERE id='{$item['id']}'");
    }

    $_SESSION['last_invoice'] = $invoice;
    $_SESSION['cart'] = [];

    header("Location:index.php?page=receipt");
}

?>

<!DOCTYPE html>
<html>
<head>
    <title>Aplikasi Kasir Modern</title>

    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">

    <style>

    *{
        margin:0;
        padding:0;
        box-sizing:border-box;
        font-family:Arial;
    }

    body{
        background:#0f172a;
        color:white;
    }

    .login-box{
        width:400px;
        background:#1e293b;
        margin:80px auto;
        padding:40px;
        border-radius:20px;
        box-shadow:0 0 20px rgba(0,0,0,0.4);
    }

    .login-box h1{
        text-align:center;
        margin-bottom:30px;
    }

    input,select,textarea{
    lebar:100%;
        padding:14px;
        border:none;
        border-radius:10px;
        margin-top:10px;
        background:#334155;
        color:white;
    }

    button{
        padding:14px 20px;
        border:none;
        border-radius:10px;
        background:#3b82f6;
        color:white;
        cursor:pointer;
        margin-top:10px;
    }

    button:hover{
        opacity:0.9;
    }

    .sidebar{
        width:250px;
        background:#1e293b;
        height:100vh;
        position:fixed;
        padding:20px;
    }

    .sidebar h2{
        margin-bottom:30px;
        text-align:center;
    }

    .sidebar a{
        display:block;
        color:white;
        text-decoration:none;
        padding:14px;
        margin-top:10px;
        border-radius:10px;
        background:#334155;
    }

    .sidebar a:hover{
        background:#3b82f6;
    }

    .main{
        margin-left:270px;
        padding:20px;
    }

    .card-grid{
        display:grid;
        grid-template-columns:repeat(auto-fit,minmax(250px,1fr));
        gap:20px;
        margin-top:20px;
    }

    .card{
        background:#1e293b;
        padding:25px;
        border-radius:20px;
    }

    .card h2{
        font-size:35px;
        margin-top:10px;
    }

    table{
    lebar:100%;
        border-collapse:collapse;
        margin-top:20px;
    }

    table th{
        background:#1e293b;
        padding:15px;
    }

    table td{
        padding:15px;
        background:#334155;
        text-align:center;
    }

    .topbar{
    lebar:100%;
        background:#1e293b;
        padding:20px;
        border-radius:20px;
        margin-bottom:20px;
    }

    .success{
        background:#16a34a;
        padding:10px;
        border-radius:10px;
        margin-bottom:10px;
    }

    .danger{
        background:#dc2626;
        padding:10px;
        border-radius:10px;
        margin-bottom:10px;
    }

    .warning{
        background:#f59e0b;
        padding:10px;
        border-radius:10px;
        margin-bottom:10px;
    }

    .receipt{
        background:white;
        color:black;
        width:400px;
        margin:auto;
        padding:20px;
        border-radius:10px;
    }

    .receipt h2{
        text-align:center;
        margin-bottom:20px;
    }

    .receipt table td{
        background:white;
        color:black;
    }

    .flex{
        display:flex;
        gap:20px;
    }

    .w-50{
        width:50%;
    }

    .search-box{
        margin-top:20px;
    }

    .product-card{
        background:#1e293b;
        padding:20px;
        border-radius:20px;
        margin-top:20px;
    }

    .product-card h3{
        margin-bottom:10px;
    }

    .grid-product{
        display:grid;
        grid-template-columns:repeat(auto-fit,minmax(250px,1fr));
        gap:20px;
    }

    @media(max-width:768px){

        .sidebar{
    width:100%;
            height:auto;
            position:relative;
        }

        .main{
            margin-left:0;
        }

        .flex{
            flex-direction:column;
        }

        .w-50{
            width:100%;
        }
    }

    </style>
</head>
<body>

<?php if(!isset($_SESSION['login'])): ?>

<divclass="login-box">

    <h1>LOGIN KASIR</h1>

    <?php if(isset($error)): ?>
    <div class="danger"><?php echo $error; ?></div>
    <?php endif; ?>

    <form method="POST">

    <input type="text" name="username" placeholder="Nama Pengguna" required>

    <input type="password" name="password" placeholder="Kata Sandi" required>

    <button name="login">MASUK</button>

    </form>

</div>

<?php else: ?>

<divclass="sidebar">

    <h2>APP KASIR</h2>

    <a href="index.php">Dasbor</a>
    <a href="index.php?page=products">Produk</a>
    <a href="index.php?page=customers">Pelanggan</a>
    <a href="index.php?page=cashier">Kasir</a>
    <a href="index.php?page=transactions">Transaksi</a>
    <a href="index.php?logout=true">Keluar</a>

</div>

<divclass="main">

<?php
$page = isset($_GET['page']) ? $_GET['page'] : 'dashboard';
?>

<?php if($page == 'dashboard'): ?>

<divclass="topbar">
    <h1>Dasbor</h1>
</div>

<?php
$totalProduct = mysqli_num_rows(mysqli_query($conn, "SELECT * FROM products"));
$totalCustomer = mysqli_num_rows(mysqli_query($conn, "SELECT * FROM customers"));
$totalTransaction = mysqli_num_rows(mysqli_query($conn, "SELECT * FROM transactions"));

$getIncome = mysqli_query($conn, "SELECT SUM(total) as income FROM transactions");
$income = mysqli_fetch_assoc($getIncome);
?>

<divclass="card-grid">

    <div class="card">
    <h3>Total Produk</h3>
    <h2><?php echo $totalProduct; ?></h2>
    </div>

    <div class="card">
    <h3>Total Pelanggan</h3>
    <h2><?php echo $totalCustomer; ?></h2>
    </div>

<div class="card">
<h3>Total Transaksi</h3>
<h2><?php echo $totalTransaction; ?></h2>
</div>

<div class="card">
<h3>Total Pendapatan</h3>
<h2>Rp <?php echo number_format($income['income']); ?></h2>
</div>

</div>

<?php endif; ?>

<?php if($page == 'products'): ?>

<divclass="topbar">
    <h1>Manajemen Produk</h1>
</div>

<divclass="flex">

<divclass="w-50">

<formmethod="POST">

<input type="text" name="name" placeholder="Nama Produk" required>

<input type="number" name="price" placeholder="Harga" required>

    <input type="number" name="stock" placeholder="Stok" required>

<input type="text" name="category" placeholder="Kategori" required>

<input type="date" name="expired_date" required>

<button name="add_product">Tambah Produk</button>

</form>

</div>

<divclass="w-50">

<table>

<tr>
    <th>ID</th>
<th>Nama</th>
<th>Harga</th>
    <th>Stock</th>
<th>Aksi</th>
</tr>

<?php
$products = mysqli_query($conn, "SELECT * FROM products ORDER BY id DESC");
while($p = mysqli_fetch_assoc($products)):
?>

<tr>
    <td><?php echo $p['id']; ?></td>
    <td><?php echo $p['name']; ?></td>
    <td>Rp <?php echo number_format($p['price']); ?></td>
    <td><?php echo $p['stock']; ?></td>
<td>
        <a href="index.php?delete_product=<?php echo $p['id']; ?>">
            <button>Hapus</button>
</a>
    </td>
</tr>

<?php endwhile; ?>

</table>

</div>

</div>

<?php endif; ?>

<?php if($page == 'customers'): ?>

<div class="topbar">
    <h1>Customer</h1>
</div>

<div class="flex">

<div class="w-50">

<form method="POST">

    <input type="text" name="name" placeholder="Nama Customer" required>

    <input type="text" name="phone" placeholder="Nomor HP" required>

    <textarea name="address" placeholder="Alamat"></textarea>

    <input type="email" name="email" placeholder="Email">

    <button name="add_customer">Tambah Customer</button>

</form>

</div>

<div class="w-50">

<table>

<tr>
    <th>ID</th>
<th>Nama</th>
    <th>HP</th>
<th>Aksi</th>
</tr>

<?php
$customers = mysqli_query($conn, "SELECT * FROM customers ORDER BY id DESC");
while($c = mysqli_fetch_assoc($customers)):
?>

<tr>
    <td><?php echo $c['id']; ?></td>
    <td><?php echo $c['name']; ?></td>
    <td><?php echo $c['phone']; ?></td>
<td>
        <a href="index.php?delete_customer=<?php echo $c['id']; ?>">
            <button>Hapus</button>
</a>
    </td>
</tr>

<?php endwhile; ?>

</table>

</div>

</div>

<?php endif; ?>

<?php if($page == 'cashier'): ?>

<div class="topbar">
    <h1>Kasir</h1>
</div>

<div class="grid-product">

<?php
$products = mysqli_query($conn, "SELECT * FROM products WHERE stock > 0 ORDER BY id DESC");
while($p = mysqli_fetch_assoc($products)):
?>

<div class="product-card">

    <h3><?php echo $p['name']; ?></h3>

    <p>Harga : Rp <?php echo number_format($p['price']); ?></p>

    <p>Stock : <?php echo $p['stock']; ?></p>

    <a href="index.php?page=cashier&add_cart=<?php echo $p['id']; ?>">
        <button>Tambah</button>
    </a>

</div>

<?php endwhile; ?>

</div>

<h2 style="margin-top:40px;">Keranjang</h2>

<table>

<tr>
<th>Nomor</th>
<th>Nama</th>
<th>Harga</th>
<th>Jumlah</th>
<th>Subtotal</th>
<th>Aksi</th>
</tr>

<?php
$no = 1;
$total = 0;
foreach($_SESSION['cart'] as $index => $cart):

$subtotal = $cart['price'] * $cart['qty'];
$total += $subtotal;
?>

<tr>
<td><?php echo $no++; ?></td>
<td><?php echo $cart['name']; ?></td>
<td>Rp <?php echo number_format($cart['price']); ?></td>
<td><?php echo $cart['qty']; ?></td>
<td>Rp <?php echo number_format($subtotal); ?></td>
<td>
        <a href="index.php?page=cashier&remove_cart=<?php echo $index; ?>">
            <button>Hapus</button>
</a>
    </td>
</tr>

<?php endforeach; ?>

<tr>
    <td colspan="4"><b>Total</b></td>
    <td colspan="2"><b>Rp <?php echo number_format($total); ?></b></td>
</tr>

</table>

<form method="POST" style="margin-top:20px;">

    <input type="text" name="customer_name" placeholder="Nama Customer" required>

    <input type="number" name="pay" placeholder="Jumlah Bayar" required>

    <button name="checkout">Checkout</button>

</form>

<?php endif; ?>

<?php if($page == 'transactions'): ?>

<div class="topbar">
    <h1>Riwayat Transaksi</h1>
</div>

<table>

<tr>
<th>Nomor</th>
    <th>Invoice</th>
    <th>Customer</th>
    <th>Total</th>
    <th>Tanggal</th>
</tr>

<?php
$no = 1;
$transactions = mysqli_query($conn, "SELECT * FROM transactions ORDER BY id DESC");
while($t = mysqli_fetch_assoc($transactions)):
?>

<tr>
<td><?php echo $no++; ?></td>
    <td><?php echo $t['invoice']; ?></td>
    <td><?php echo $t['customer_name']; ?></td>
    <td>Rp <?php echo number_format($t['total']); ?></td>
    <td><?php echo $t['created_at']; ?></td>
</tr>

<?php endwhile; ?>

</table>

<?php endif; ?>

<?php if($page == 'receipt'): ?>

<?php
$invoice = $_SESSION['last_invoice'];

$data = mysqli_fetch_assoc(mysqli_query($conn, "SELECT * FROM transactions WHERE invoice='$invoice'"));

$details = mysqli_query($conn, "SELECT * FROM transaction_details WHERE invoice='$invoice'");
?>

<div class="receipt">

<h2>STRUK BELANJA</h2>

<p>Invoice : <?php echo $data['invoice']; ?></p>
<p>Customer : <?php echo $data['customer_name']; ?></p>
<p>Tanggal : <?php echo $data['created_at']; ?></p>

<hr><br>

<table>

<tr>
    <th>Produk</th>
<th>Jumlah</th>
    <th>Total</th>
</tr>

<?php while($d = mysqli_fetch_assoc($details)): ?>

<tr>
    <td><?php echo $d['product_name']; ?></td>
    <td><?php echo $d['qty']; ?></td>
<td>Rp <?php echo number_format($d['subtotal']); ?></td>
</tr>

<?php endwhile; ?>

</table>

<br>
<hr>

<h3>Total : Rp <?php echo number_format($data['total']); ?></h3>
<h3>Bayar : Rp <?php echo number_format($data['pay']); ?></h3>
<h3>Kembalian : Rp <?php echo number_format($data['change_money']); ?></h3>

<br>

<button onclick="window.print()">Cetak</button>

<a href="index.php?page=transactions">
<button>Kembali</button>
</a>

</div>

<?php endif; ?>

</div>

<?php endif; ?>

<script>script>

// ================================
// JAM DIGITAL
// ================================
function updateClock(){

const now=newDate();

let hour = now.getHours();
let minute = now.getMinutes();
let second = now.getSeconds();

hour = hour < 10 ? '0'+hour : hour;
menit = menit < 10 ? '0'+menit : menit;
detik = detik < 10 ? '0'+detik : detik;

const jam = document.getElementById('clock');

if(jam){
jam.innerHTML = jam + ':' + menit + ':' + detik;
    }
}

setInterval(updateClock,1000);

// ================================
// TABEL PENCARIAN
// ================================
function cariTabel(){

let input = document.getElementById('searchInput');

if(!input) return;

let filter = input.value.toUpperCase();

let table = document.getElementById('tableData');

let tr = table.getElementsByTagName('tr');

for(let i=0;i<tr.length;i++){

let td = tr[i].getElementsByTagName('td')[1];

if(td){

let txtValue = td.textContent || td.innerText;

if(txtValue.toUpperCase().indexOf(filter) > -1){
tr[i].style.display='';
}lain{
tr[i].style.display='none';
            }
        }
    }
}

// ================================
// NOTIFIKASI
// ================================
fungsi tampilkanNotif(teks){

peringatan(teks);
}

// ================================
// MODE GELAP
// ================================
biarkan gelap = benar;

fungsi alihkanGelap(){

jika(gelap){
latarBelakang.tubuh.gaya.latarBelakang='#ffffff';
warna.tubuh.gaya.warna='#000000';
gelap = salah;
}lain{
document.body.style.background='#0f172a';
document.body.style.color='#ffffff';
gelap = true;
    }
}

// ================================
// VALIDASI FORM
// ================================
function validasiAngka(input){

if(input.value<0){
input.value=0;
    }
}

// ================================
// FOKUS OTOMATIS
// ================================
window.onload=function(){

let firstInput = document.querySelector('input');

if(firstInput){
firstInput.focus();
    }
}

</script>

</body>
</html>
