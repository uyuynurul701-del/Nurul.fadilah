<!DOCTYPE html>
<html lang="id">
<head>
    <title>Aplikasi Kasir </title>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; }
        body { background: #0f172a; color: #f8fafc; }

        .sidebar {
            width: 260px; background: #1e293b; height: 100vh; position: fixed; padding: 20px;
            border-right: 1px solid #334155;
        }
        .sidebar h2 { margin-bottom: 30px; text-align: center; color: #3b82f6; }
        .sidebar a {
            display: block; color: #94a3b8; text-decoration: none; padding: 12px 16px;
            margin-top: 8px; border-radius: 8px; font-weight: 500; transition: 0.2s; cursor: pointer;
        }
        .sidebar a:hover, .sidebar a.active { background: #334155; color: #fff; }

        .main { margin-left: 260px; padding: 30px; }

        .card-grid {
            display: grid; grid-template-columns: repeat(auto-fit, minmax(220px, 1fr)); gap: 20px; margin-top: 20px;
        }
        .card { background: #1e293b; padding: 24px; border-radius: 16px; border: 1px solid #334155; }
        .card h3 { color: #94a3b8; font-size: 14px; }
        .card h2 { font-size: 28px; margin-top: 8px; color: #f8fafc; }

        input, select, textarea {
            width: 100%; padding: 12px 16px; border: 1px solid #334155; border-radius: 8px;
            margin-top: 12px; background: #0f172a; color: #fff; outline: none;
        }
        button {
            padding: 12px 20px; border: none; border-radius: 8px; background: #3b82f6;
            color: white; font-weight: 600; cursor: pointer; margin-top: 12px; width: 100%;
        }
        button:hover { background: #2563eb; }

        table { width: 100%; border-collapse: collapse; margin-top: 20px; background: #1e293b; border-radius: 12px; overflow: hidden; }
        table th { background: #334155; padding: 14px; text-align: center; font-size: 14px; color: #cbd5e1; }
        table td { padding: 14px; text-align: center; border-top: 1px solid #334155; font-size: 14px; color: #e2e8f0; }

        .topbar { background: #1e293b; padding: 20px 24px; border-radius: 16px; margin-bottom: 20px; border: 1px solid #334155; }
        .topbar h1 { font-size: 22px; }

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

        .page-content { display: none; }
        .page-content.active { display: block; }

        @media(max-width: 768px) {
            .sidebar { width: 100%; height: auto; position: relative; }
            .main { margin-left: 0; padding: 15px; }
            .flex { flex-direction: column; }
            .w-50 { width: 100%; }
        }
    </style>
</head>
<body>

<div class="sidebar">
    <h2>KASIR APP</h2>
    <a onclick="switchPage('dashboard')" id="nav-dashboard" class="active">Dashboard</a>
    <a onclick="switchPage('products')" id="nav-products">Produk</a>
    <a onclick="switchPage('customers')" id="nav-customers">Customer</a>
    <a onclick="switchPage('cashier')" id="nav-cashier">Kasir</a>
    <a onclick="switchPage('transactions')" id="nav-transactions">Transaksi</a>
</div>

<div class="main">

    <!-- DASHBOARD -->
    <div id="page-dashboard" class="page-content active">
        <div class="topbar"><h1>Dashboard</h1></div>
        <div class="card-grid">
            <div class="card"><h3>Total Produk</h3><h2 id="stat-product">0</h2></div>
            <div class="card"><h3>Total Customer</h3><h2 id="stat-customer">0</h2></div>
            <div class="card"><h3>Total Transaksi</h3><h2 id="stat-transaction">0</h2></div>
            <div class="card"><h3>Total Pendapatan</h3><h2 id="stat-income">Rp 0</h2></div>
        </div>
    </div>

    <!-- PRODUK -->
    <div id="page-products" class="page-content">
        <div class="topbar"><h1>Manajemen Produk</h1></div>
        <div class="flex">
            <div class="w-50">
                <form onsubmit="addProduct(event)">
                    <input type="text" id="p-name" placeholder="Nama Produk" required>
                    <input type="number" id="p-price" placeholder="Harga" required>
                    <input type="number" id="p-stock" placeholder="Stok" required>
                    <button type="submit">Tambah Produk</button>
                </form>
            </div>
            <div class="w-50">
                <table>
                    <thead><tr><th>Nama</th><th>Harga</th><th>Stok</th><th>Aksi</th></tr></thead>
                    <tbody id="table-product-body"></tbody>
                </table>
            </div>
        </div>
    </div>

    <!-- CUSTOMER -->
    <div id="page-customers" class="page-content">
        <div class="topbar"><h1>Manajemen Customer</h1></div>
        <div class="flex">
            <div class="w-50">
                <form onsubmit="addCustomer(event)">
                    <input type="text" id="c-name" placeholder="Nama Customer" required>
                    <input type="text" id="c-phone" placeholder="Nomor HP" required>
                    <button type="submit">Tambah Customer</button>
                </form>
            </div>
            <div class="w-50">
                <table>
                    <thead><tr><th>Nama</th><th>HP</th><th>Aksi</th></tr></thead>
                    <tbody id="table-customer-body"></tbody>
                </table>
            </div>
        </div>
    </div>

    <!-- KASIR -->
    <div id="page-cashier" class="page-content">
        <div class="topbar"><h1>Kasir / Penjualan</h1></div>
        <h3>Pilih Produk</h3>
        <div class="grid-product" id="cashier-product-list"></div>

        <h3 style="margin-top:30px;">Keranjang Belanja</h3>
        <table>
            <thead><tr><th>No</th><th>Produk</th><th>Harga</th><th>Qty</th><th>Subtotal</th><th>Aksi</th></tr></thead>
            <tbody id="cart-body"></tbody>
        </table>

        <div id="checkout-form-container" style="margin-top:20px; background:#1e293b; padding:20px; border-radius:12px; border:1px solid #334155; display:none;">
            <input type="text" id="co-customer" placeholder="Nama Customer" required>
            <input type="number" id="co-pay" placeholder="Jumlah Uang Bayar" required>
            <button onclick="checkout()" style="background:#16a34a;">Proses Checkout</button>
        </div>
    </div>

    <!-- TRANSAKSI -->
    <div id="page-transactions" class="page-content">
        <div class="topbar"><h1>Riwayat Transaksi</h1></div>
        <table>
            <thead><tr><th>No</th><th>Invoice</th><th>Customer</th><th>Total</th><th>Tanggal</th></tr></thead>
            <tbody id="table-transaction-body"></tbody>
        </table>
    </div>

</div>

<script>
    // LocalStorage Database Initialization
    let products = JSON.parse(localStorage.getItem('kasir_products')) || [];
    let customers = JSON.parse(localStorage.getItem('kasir_customers')) || [];
    let transactions = JSON.parse(localStorage.getItem('kasir_transactions')) || [];
    let cart = [];

    function saveData() {
        localStorage.setItem('kasir_products', JSON.stringify(products));
        localStorage.setItem('kasir_customers', JSON.stringify(customers));
        localStorage.setItem('kasir_transactions', JSON.stringify(transactions));
        updateDashboard();
    }

    function switchPage(pageId) {
        document.querySelectorAll('.page-content').forEach(el => el.classList.remove('active'));
        document.querySelectorAll('.sidebar a').forEach(el => el.classList.remove('active'));
        
        document.getElementById('page-' + pageId).classList.add('active');
        document.getElementById('nav-' + pageId).classList.add('active');

        if(pageId === 'products') renderProducts();
        if(pageId === 'customers') renderCustomers();
        if(pageId === 'cashier') renderCashier();
        if(pageId === 'transactions') renderTransactions();
    }

    function formatRupiah(angka) {
        return 'Rp ' + angka.toLocaleString('id-ID');
    }

    // Dashboard Stats
    function updateDashboard() {
        document.getElementById('stat-product').innerText = products.length;
        document.getElementById('stat-customer').innerText = customers.length;
        document.getElementById('stat-transaction').innerText = transactions.length;
        let totalIncome = transactions.reduce((acc, t) => acc + t.total, 0);
        document.getElementById('stat-income').innerText = formatRupiah(totalIncome);
    }

    // Produk Functions
    function addProduct(e) {
        e.preventDefault();
        let name = document.getElementById('p-name').value;
        let price = parseInt(document.getElementById('p-price').value);
        let stock = parseInt(document.getElementById('p-stock').value);

        products.push({ id: Date.now(), name, price, stock });
        saveData();
        document.getElementById('p-name').value = '';
        document.getElementById('p-price').value = '';
        document.getElementById('p-stock').value = '';
        renderProducts();
    }

    function deleteProduct(id) {
        products = products.filter(p => p.id !== id);
        saveData();
        renderProducts();
    }

    function renderProducts() {
        let html = '';
        products.forEach(p => {
            html += `<tr>
                <td>${p.name}</td>
                <td>${formatRupiah(p.price)}</td>
                <td>${p.stock}</td>
                <td><button class="btn-danger" onclick="deleteProduct(${p.id})" style="margin:0; padding:6px 12px;">Hapus</button></td>
            </tr>`;
        });
        document.getElementById('table-product-body').html = html; // fix assignment below
        document.getElementById('table-product-body').innerHTML = html;
    }

    // Customer Functions
    function addCustomer(e) {
        e.preventDefault();
        let name = document.getElementById('c-name').value;
        let phone = document.getElementById('c-phone').value;

        customers.push({ id: Date.now(), name, phone });
        saveData();
        document.getElementById('c-name').value = '';
        document.getElementById('c-phone').value = '';
        renderCustomers();
    }

    function deleteCustomer(id) {
        customers = customers.filter(c => c.id !== id);
        saveData();
        renderCustomers();
    }

    function renderCustomers() {
        let html = '';
        customers.forEach(c => {
            html += `<tr>
                <td>${c.name}</td>
                <td>${c.phone}</td>
                <td><button class="btn-danger" onclick="deleteCustomer(${c.id})" style="margin:0; padding:6px 12px;">Hapus</button></td>
            </tr>`;
        });
        document.getElementById('table-customer-body').innerHTML = html;
    }

    // Cashier Functions
    function renderCashier() {
        let htmlProd = '';
        products.filter(p => p.stock > 0).forEach(p => {
            htmlProd += `<div class="product-card">
                <h3>${p.name}</h3>
                <p>Harga: ${formatRupiah(p.price)}</p>
                <p>Stok: ${p.stock}</p>
                <button onclick="addToCart(${p.id})">Tambah</button>
            </div>`;
        });
        document.getElementById('cashier-product-list').innerHTML = htmlProd;

        let htmlCart = '';
        let total = 0;
        cart.forEach((item, index) => {
            let subtotal = item.price * item.qty;
            total += subtotal;
            htmlCart += `<tr>
                <td>${index + 1}</td>
                <td>${item.name}</td>
                <td>${formatRupiah(item.price)}</td>
                <td>${item.qty}</td>
                <td>${formatRupiah(subtotal)}</td>
                <td><button class="btn-danger" onclick="removeFromCart(${index})" style="margin:0; padding:6px 12px;">Hapus</button></td>
            </tr>`;
        });
        htmlCart += `<tr><td colspan="4"><b>Total Pembayaran</b></td><td colspan="2"><b>${formatRupiah(total)}</b></td></tr>`;
        document.getElementById('cart-body').innerHTML = htmlCart;

        document.getElementById('checkout-form-container').style.display = cart.length > 0 ? 'block' : 'none';
    }

    function addToCart(id) {
        let prod = products.find(p => p.id === id);
        if(!prod || prod.stock <= 0) return;

        let exist = cart.find(item => item.id === id);
        if(exist) {
            if(exist.qty < prod.stock) exist.qty++;
            else alert('Stok tidak cukup!');
        } else {
            cart.push({ id: prod.id, name: prod.name, price: prod.price, qty: 1 });
        }
        renderCashier();
    }

    function removeFromCart(index) {
        cart.splice(index, 1);
        renderCashier();
    }

    function checkout() {
        let customerName = document.getElementById('co-customer').value;
        let pay = parseInt(document.getElementById('co-pay').value);

        if(!customerName || !pay) {
            alert('Nama customer dan jumlah bayar harus diisi!');
            return;
        }

        let total = cart.reduce((acc, item) => acc + (item.price * item.qty), 0);
        if(pay < total) {
            alert('Uang bayar kurang!');
            return;
        }

        // Kurangi stok produk asli
        cart.forEach(item => {
            let p = products.find(prod => prod.id === item.id);
            if(p) p.stock -= item.qty;
        });

        let invoice = 'INV' + Date.now();
        transactions.push({
            invoice,
            customer_name: customerName,
            total,
            created_at: new Date().toLocaleString()
        });

        cart = [];
        saveData();
        alert('Transaksi Berhasil!');
        switchPage('transactions');
    }

    function renderTransactions() {
        let html = '';
        transactions.slice().reverse().forEach((t, index) => {
            html += `<tr>
                <td>${index + 1}</td>
                <td>${t.invoice}</td>
                <td>${t.customer_name}</td>
                <td>${formatRupiah(t.total)}</td>
                <td>${t.created_at}</td>
            </tr>`;
        });
        document.getElementById('table-transaction-body').innerHTML = html;
    }

    // Init first load
    updateDashboard();
</script>
</body>
</html>
