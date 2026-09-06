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

// TABEL PRODUK
mysqli_query($conn, "CREATE TABLE IF NOT EXISTS products(
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(200),
    price INT,
    stock INT,
    category VARCHAR(100),
    expired_date DATE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
)");

// TABEL CUSTOMER
mysqli_query($conn, "CREATE TABLE IF NOT EXISTS customers(
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100),
    phone VARCHAR(20),
    address TEXT,
    email VARCHAR(100)
)");

// TABEL TRANSAKSI
mysqli_query($conn, "CREATE TABLE IF NOT EXISTS transactions(
    id INT AUTO_INCREMENT PRIMARY KEY,
    invoice VARCHAR(100),
    customer_name VARCHAR(100),
    total INT,
    pay INT,
    change_money INT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
)");

// TABEL DETAIL TRANSAKSI
mysqli_query($conn, "CREATE TABLE IF NOT EXISTS transaction_details(
    id INT AUTO_INCREMENT PRIMARY KEY,
    invoice VARCHAR(100),
    product_name VARCHAR(100),
    qty INT,
    price INT,
    subtotal INT
)");

// LOGIN
$defaultUser = "admin";
$defaultPass = "12345";

if (isset($_POST['login'])) {
    $username = $_POST['username'];
    $password = $_POST['password'];

    if ($username == $defaultUser && $password == $defaultPass) {
        $_SESSION['login'] = true;
        header("Location: index.php");
        exit;
    } else {
        $error = "Username atau Password salah";
    }
}

if (isset($_GET['logout'])) {
    session_destroy();
    header("Location: index.php");
    exit;
}

// CRUD PRODUK
if (isset($_POST['add_product'])) {
    $name = $_POST['name'];
    $price = $_POST['price'];
    $stock = $_POST['stock'];
    $category = $_POST['category'];
    $expired = $_POST['expired_date'];

    mysqli_query($conn, "INSERT INTO products(name,price,stock,category,expired_date) VALUES('$name','$price','$stock','$category','$expired')");
    header("Location: index.php?page=products");
    exit;
}

if (isset($_GET['delete_product'])) {
    $id = $_GET['delete_product'];
    mysqli_query($conn, "DELETE FROM products WHERE id='$id'");
    header("Location: index.php?page=products");
    exit;
}

// CRUD CUSTOMER
if (isset($_POST['add_customer'])) {
    $name = $_POST['name'];
    $phone = $_POST['phone'];
    $address = $_POST['address'];
    $email = $_POST['email'];

    mysqli_query($conn, "INSERT INTO customers(name,phone,address,email) VALUES('$name','$phone','$address','$email')");
    header("Location: index.php?page=customers");
    exit;
}

if (isset($_GET['delete_customer'])) {
    $id = $_GET['delete_customer'];
    mysqli_query($conn, "DELETE FROM customers WHERE id='$id'");
    header("Location: index.php?page=customers");
    exit;
}

// KERANJANG
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
    header("Location: index.php?page=cashier");
    exit;
}

if (isset($_GET['remove_cart'])) {
    $index = $_GET['remove_cart'];
    unset($_SESSION['cart'][$index]);
    $_SESSION['cart'] = array_values($_SESSION['cart']);
    header("Location: index.php?page=cashier");
    exit;
}

// CHECKOUT
if (isset($_POST['checkout'])) {
    $customer = $_POST['customer_name'];
    $pay = $_POST['pay'];
    $invoice = "INV" . time();
    $total = 0;

    foreach ($_SESSION['cart'] as $item) {
        $total += $item['price'] * $item['qty'];
    }

    if ($pay < $total) {
        echo "<script>alert('Uang bayar kurang!'); window.location='index.php?page=cashier';</script>";
        exit;
    }

    $change = $pay - $total;

    mysqli_query($conn, "INSERT INTO transactions(invoice,customer_name,total,pay,change_money) VALUES('$invoice','$customer','$total','$pay','$change')");

    foreach ($_SESSION['cart'] as $item) {
        $subtotal = $item['price'] * $item['qty'];
        mysqli_query($conn, "INSERT INTO transaction_details(invoice,product_name,qty,price,subtotal) VALUES('$invoice', '{$item['name']}', '{$item['qty']}', '{$item['price']}', '$subtotal')");
        mysqli_query($conn, "UPDATE products SET stock = stock - {$item['qty']} WHERE id='{$item['id']}'");
    }

    $_SESSION['last_invoice'] = $invoice;
    $_SESSION['cart'] = [];
    header("Location: index.php?page=receipt");
    exit;
}
?>

<!DOCTYPE html>
<html lang="id">
<head>
    <title>Aplikasi Kasir Modern</title>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; }
        body { background: #0f172a; color: #f8fafc; }

        .login-box {
            width: 100%; max-width: 400px; background: #1e293b; margin: 100px auto; padding: 40px;
            border-radius: 16px; box-shadow: 0 10px 25px rgba(0,0,0,0.3);
        }
        .login-box h1 { text-align: center; margin-bottom: 25px; font-size: 24px; }

        input, select, textarea {
            width: 100%; padding: 12px 16px; border: 1px solid #334155; border-radius: 8px;
            margin-top: 12px; background: #0f172a; color: #fff; outline: none; transition: 0.2s;
        }
        input:focus, select:focus, textarea:focus { border-color: #3b82f6; }

        button {
            padding: 12px 20px; border: none; border-radius: 8px; background: #3b82f6;
            color: white; font-weight: 600; cursor: pointer; margin-top: 12px; transition: 0.2s; width: 100%;
        }
        button:hover { background: #2563eb; }

        .sidebar {
            width: 260px; background: #1e293b; height: 100vh; position: fixed; padding: 20px;
            border-right: 1px solid #334155;
        }
        .sidebar h2 { margin-bottom: 30px; text-align: center; color: #3b82f6; }
        .sidebar a {
            display: block; color: #94a3b8; text-decoration: none; padding: 12px 16px;
            margin-top: 8px; border-radius: 8px; font-weight: 500; transition: 0.2s;
        }
        .sidebar a:hover { background: #334155; color: #fff; }

        .main { margin-left: 260px; padding: 30px; }

        .card-grid {
            display: grid; grid-template-columns: repeat(auto-fit, minmax(220px, 1fr)); gap: 20px; margin-top: 20px;
        }
        .card { background: #1e293b; padding: 24px; border-radius: 16px; border: 1px solid #334155; }
        .card h3 { color: #94a3b8; font-size: 14px; }
        .card h2 { font-size: 28px; margin-top: 8px; color: #f8fafc; }

        table { width: 100%; border-collapse: collapse; margin-top: 20px; background: #1e293b; border-radius: 12px; overflow: hidden; }
        table th { background: #334155; padding: 14px; text-align: center; font-size: 14px; color: #cbd5e1; }
        table td { padding: 14px; text-align: center; border-top: 1px solid #334155; font-size: 14px; color: #e2e8f0; }

        .topbar { background: #1e293b; padding: 20px 24px; border-radius: 16px; margin-bottom: 20px; border: 1px solid #334155; }
        .topbar h1 { font-size: 22px; }

        .danger { background: rgba(220, 38, 38, 0.2); color: #fca5a5; padding: 12px; border-radius: 8px; margin-bottom: 15px; border: 1px solid #dc2626; text-align: center; }

        .receipt {
            background: white; color: black; width: 100%; max-width: 400px; margin: 0 auto;
            padding: 30px; border-radius: 12px; box-shadow: 0 4px 15px rgba(0,0,0,0.2);
        }
        .receipt h2 { text-align: center; margin-bottom: 15px; }
        .receipt table { background: white; }
        .receipt table th, .receipt table td { background: white; color: black; border-color: #ddd; }

        .flex { display: flex; gap: 20px; }
        .w-50 { width: 50%; }

        .grid-product {
            display: grid; grid-template-columns: repeat(auto-fit, minmax(200px, 1fr)); gap: 15px; margin-top: 15px;
        }
        .product-card { background: #1e293b; padding: 16px; border-radius: 12px; border: 1px solid #334155; }
        .product-card h3 { font-size: 16px; margin-bottom: 6px; }
        .product-card p { font-size: 13px; color: #94a3b8; margin-bottom: 4px; }
        .product-card button { width: 100%; margin-top: 8px; padding: 8px; }

        .btn-danger { background: #dc2626; }
        .btn-danger:hover { background: #b91c1c; }

        @media(max-width: 768px) {
            .sidebar { width: 100%; height: auto; position: relative; }
            .main { margin-left: 0; padding: 15px; }
            .flex { flex-direction: column; }
            .w-50 { width: 100%; }
        }
    </style>
</head>
<body>

<?php if(!isset($_SESSION['login'])): ?>
<div class="login-box">
    <h1>LOGIN KASIR</h1>
    <?php if(isset($error)): ?>
        <div class="danger"><?php echo $error; ?></div>
    <?php endif; ?>
    <form method="POST">
        <input type="text" name="username" placeholder="Username" required autocomplete="off">
        <input type="password" name="password" placeholder="Password" required>
        <button type="submit" name="login">LOGIN</button>
    </form>
</div>
<?php else: ?>

<div class="sidebar">
    <h2>KASIR APP</h2>
    <a href="index.php">Dashboard</a>
    <a href="index.php?page=products">Produk</a>
    <a href="index.php?page=customers">Customer</a>
    <a href="index.php?page=cashier">Kasir</a>
    <a href="index.php?page=transactions">Transaksi</a>
    <a href="index.php?logout=true" style="color: #fca5a5;">Logout</a>
</div>

<div class="main">
<?php
$page = isset($_GET['page']) ? $_GET['page'] : 'dashboard';
?>

<?php if($page == 'dashboard'): ?>
<div class="topbar"><h1>Dashboard</h1></div>
<?php
$totalProduct = mysqli_num_rows(mysqli_query($conn, "SELECT * FROM products"));
$totalCustomer = mysqli_num_rows(mysqli_query($conn, "SELECT * FROM customers"));
$totalTransaction = mysqli_num_rows(mysqli_query($conn, "SELECT * FROM transactions"));
$income = mysqli_fetch_assoc(mysqli_query($conn, "SELECT SUM(total) as income FROM transactions"))['income'] ?? 0;
?>
<div class="card-grid">
    <div class="card"><h3>Total Produk</h3><h2><?php echo $totalProduct; ?></h2></div>
    <div class="card"><h3>Total Customer</h3><h2><?php echo $totalCustomer; ?></h2></div>
    <div class="card"><h3>Total Transaksi</h3><h2><?php echo $totalTransaction; ?></h2></div>
    <div class="card"><h3>Total Pendapatan</h3><h2>Rp <?php echo number_format($income); ?></h2></div>
</div>
<?php endif; ?>

<?php if($page == 'products'): ?>
<div class="topbar"><h1>Manajemen Produk</h1></div>
<div class="flex">
    <div class="w-50">
        <form method="POST">
            <input type="text" name="name" placeholder="Nama Produk" required>
            <input type="number" name="price" placeholder="Harga" required>
            <input type="number" name="stock" placeholder="Stok" required>
            <input type="text" name="category" placeholder="Kategori" required>
            <input type="date" name="expired_date" required>
            <button type="submit" name="add_product">Tambah Produk</button>
        </form>
    </div>
    <div class="w-50">
        <table>
            <tr>
                <th>Nama</th>
                <th>Harga</th>
                <th>Stok</th>
                <th>Aksi</th>
            </tr>
            <?php
            $products = mysqli_query($conn, "SELECT * FROM products ORDER BY id DESC");
            while($p = mysqli_fetch_assoc($products)):
            ?>
            <tr>
                <td><?php echo $p['name']; ?></td>
                <td>Rp <?php echo number_format($p['price']); ?></td>
                <td><?php echo $p['stock']; ?></td>
                <td><a href="index.php?page=products&delete_product=<?php echo $p['id']; ?>"><button class="btn-danger" style="margin:0; padding:6px 12px;">Hapus</button></a></td>
            </tr>
            <?php endwhile; ?>
        </table>
    </div>
</div>
<?php endif; ?>

<?php if($page == 'customers'): ?>
<div class="topbar"><h1>Manajemen Customer</h1></div>
<div class="flex">
    <div class="w-50">
        <form method="POST">
            <input type="text" name="name" placeholder="Nama Customer" required>
            <input type="text" name="phone" placeholder="Nomor HP" required>
            <textarea name="address" placeholder="Alamat"></textarea>
            <input type="email" name="email" placeholder="Email">
            <button type="submit" name="add_customer">Tambah Customer</button>
        </form>
    </div>
    <div class="w-50">
        <table>
            <tr>
                <th>Nama</th>
                <th>HP</th>
                <th>Aksi</th>
            </tr>
            <?php
            $customers = mysqli_query($conn, "SELECT * FROM customers ORDER BY id DESC");
            while($c = mysqli_fetch_assoc($customers)):
            ?>
            <tr>
                <td><?php echo $c['name']; ?></td>
                <td><?php echo $c['phone']; ?></td>
                <td><a href="index.php?page=customers&delete_customer=<?php echo $c['id']; ?>"><button class="btn-danger" style="margin:0; padding:6px 12px;">Hapus</button></a></td>
            </tr>
            <?php endwhile; ?>
        </table>
    </div>
</div>
<?php endif; ?>

<?php if($page == 'cashier'): ?>
<div class="topbar"><h1>Kasir / Penjualan</h1></div>
<h3>Pilih Produk</h3>
<div class="grid-product">
    <?php
    $products = mysqli_query($conn, "SELECT * FROM products WHERE stock > 0 ORDER BY id DESC");
    while($p = mysqli_fetch_assoc($products)):
    ?>
    <div class="product-card">
        <h3><?php echo $p['name']; ?></h3>
        <p>Harga: Rp <?php echo number_format($p['price']); ?></p>
        <p>Stok: <?php echo $p['stock']; ?></p>
        <a href="index.php?page=cashier&add_cart=<?php echo $p['id']; ?>"><button>Tambah</button></a>
    </div>
    <?php endwhile; ?>
</div>

<h3 style="margin-top:30px;">Keranjang Belanja</h3>
<table>
    <tr>
        <th>No</th>
        <th>Nama Produk</th>
        <th>Harga</th>
        <th>Qty</th>
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
        <td><a href="index.php?page=cashier&remove_cart=<?php echo $index; ?>"><button class="btn-danger" style="margin:0; padding:6px 12px;">Hapus</button></a></td>
    </tr>
    <?php endforeach; ?>
    <tr>
        <td colspan="4"><b>Total Pembayaran</b></td>
        <td colspan="2"><b>Rp <?php echo number_format($total); ?></b></td>
    </tr>
</table>

<?php if(!empty($_SESSION['cart'])): ?>
<form method="POST" style="margin-top:20px; background:#1e293b; padding:20px; border-radius:12px; border:1px solid #334155;">
    <input type="text" name="customer_name" placeholder="Nama Customer" required>
    <input type="number" name="pay" placeholder="Jumlah Uang Bayar" required>
    <button type="submit" name="checkout" style="background:#16a34a;">Proses Checkout</button>
</form>
<?php endif; ?>
<?php endif; ?>

<?php if($page == 'transactions'): ?>
<div class="topbar"><h1>Riwayat Transaksi</h1></div>
<table>
    <tr>
        <th>No</th>
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
$invoice = $_SESSION['last_invoice'] ?? '';
$data = mysqli_fetch_assoc(mysqli_query($conn, "SELECT * FROM transactions WHERE invoice='$invoice'"));
$details = mysqli_query($conn, "SELECT * FROM transaction_details WHERE invoice='$invoice'");
?>
<div class="receipt">
    <h2>STRUK BELANJA</h2>
    <p><b>Invoice:</b> <?php echo $data['invoice'] ?? '-'; ?></p>
    <p><b>Customer:</b> <?php echo $data['customer_name'] ?? '-'; ?></p>
    <p><b>Tanggal:</b> <?php echo $data['created_at'] ?? '-'; ?></p>
    <hr style="margin: 15px 0; border: none; border-top: 1px dashed #000;">
    <table>
        <tr>
            <th style="background:#eee; color:#000;">Produk</th>
            <th style="background:#eee; color:#000;">Qty</th>
            <th style="background:#eee; color:#000;">Subtotal</th>
        </tr>
        <?php while($d = mysqli_fetch_assoc($details)): ?>
        <tr>
            <td><?php echo $d['product_name']; ?></td>
            <td><?php echo $d['qty']; ?></td>
            <td>Rp <?php echo number_format($d['subtotal']); ?></td>
        </tr>
        <?php endwhile; ?>
    </table>
    <hr style="margin: 15px 0; border: none; border-top: 1px dashed #000;">
    <p><b>Total:</b> Rp <?php echo number_format($data['total'] ?? 0); ?></p>
    <p><b>Bayar:</b> Rp <?php echo number_format($data['pay'] ?? 0); ?></p>
    <p><b>Kembalian:</b> Rp <?php echo number_format($data['change_money'] ?? 0); ?></p>
    <br>
    <button onclick="window.print()" style="background:#3b82f6;">Cetak Struk</button>
    <a href="index.php?page=transactions"><button style="background:#64748b;">Kembali</button></a>
</div>
<?php endif; ?>

</div>
<?php endif; ?>

</body>
</html>
