<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Aplikasi Kasir Sederhana</title>
    <style>
        * { box-sizing: border-box; margin: 0; padding: 0; font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; }
        body { background-color: #f4f7f6; display: flex; justify-content: center; padding: 20px; }
        .container { display: flex; width: 100%; max-width: 1000px; gap: 20px; }
        .card { background: white; padding: 20px; border-radius: 8px; box-shadow: 0 4px 6px rgba(0,0,0,0.1); }
        .products-section { flex: 1; }
        .cart-section { width: 350px; display: flex; flex-direction: column; justify-content: space-between; }
        h2 { margin-bottom: 15px; color: #333; font-size: 1.2rem; border-bottom: 2px solid #eee; padding-bottom: 5px; }
        .product-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(130px, 1fr)); gap: 10px; }
        .product-item { background: #fafafa; border: 1px solid #ddd; border-radius: 6px; padding: 10px; text-align: center; cursor: pointer; transition: 0.2s; }
        .product-item:hover { background: #e2e8f0; border-color: #cbd5e1; }
        .product-item h4 { font-size: 0.95rem; color: #1e293b; margin-bottom: 5px; }
        .product-item p { font-size: 0.85rem; color: #64748b; }
        .cart-list { list-style: none; max-height: 200px; overflow-y: auto; margin-bottom: 15px; }
        .cart-item { display: flex; justify-content: space-between; align-items: center; padding: 8px 0; border-bottom: 1px solid #f1f5f9; font-size: 0.9rem; }
        .cart-item button { background: #ef4444; color: white; border: none; padding: 2px 6px; border-radius: 4px; cursor: pointer; font-size: 0.75rem; }
        .summary { border-top: 2px solid #eee; padding-top: 10px; font-size: 0.95rem; }
        .summary div { display: flex; justify-content: space-between; margin-bottom: 5px; }
        .summary .total { font-weight: bold; font-size: 1.1rem; color: #0f172a; }
        .payment-box { margin-top: 10px; }
        .payment-box input { width: 100%; padding: 8px; margin-top: 5px; margin-bottom: 10px; border: 1px solid #cbd5e1; border-radius: 4px; }
        .btn-checkout { width: 100%; background: #22c55e; color: white; border: none; padding: 10px; border-radius: 4px; font-weight: bold; cursor: pointer; transition: 0.2s; }
        .btn-checkout:hover { background: #16a34a; }
    </style>
</head>
<body>

    <div class="container">
        <!-- Bagian Daftar Produk -->
        <div class="card products-section">
            <h2>Daftar Produk</h2>
            <div class="product-grid" id="productGrid">
                <!-- Produk akan dimuat lewat JavaScript -->
            </div>
        </div>

        <!-- Bagian Keranjang & Pembayaran -->
        <div class="card cart-section">
            <div>
                <h2>Keranjang Belanja</h2>
                <ul class="cart-list" id="cartList">
                    <li style="color: #94a3b8; text-align: center; padding: 20px 0;">Keranjang masih kosong</li>
                </ul>
            </div>
            
            <div class="summary">
                <div><span>Total:</span> <span id="totalText">Rp 0</span></div>
                <div class="payment-box">
                    <label for="cashInput">Uang Dibayar:</label>
                    <input type="number" id="cashInput" placeholder="Masukkan nominal uang..." oninput="hitungKembalian()">
                    <div><span>Kembalian:</span> <span id="changeText" style="color: #2563eb; font-weight: bold;">Rp 0</span></div>
                </div>
                <button class="btn-checkout" onclick="prosesBayar()" style="margin-top: 15px;">Proses Pembayaran</button>
            </div>
        </div>
    </div>

    <script>
        // Data Produk Toko
        const products = [
            { id: 1, name: "Kopi Hitam", price: 15000 },
            { id: 2, name: "Es Teh Manis", price: 5000 },
            { id: 3, name: "Roti Bakar", price: 12000 },
            { id: 4, name: "Mie Goreng", price: 10000 },
            { id: 5, name: "Kentang Goreng", price: 13000 },
            { id: 6, name: "Air Mineral", price: 4000 }
        ];

        let cart = [];

        // Tampilkan produk ke layar
        function renderProducts() {
            const grid = document.getElementById('productGrid');
            grid.innerHTML = products.map(p => `
                <div class="product-item" onclick="addToCart(${p.id})">
                    <h4>${p.name}</h4>
                    <p>Rp ${p.price.toLocaleString('id-ID')}</p>
                </div>
            `).join('');
        }

        // Tambah produk ke keranjang
        function addToCart(id) {
            const product = products.find(p => p.id === id);
            const existing = cart.find(item => item.id === id);

            if (existing) {
                existing.qty += 1;
            } else {
                cart.push({ ...product, qty: 1 });
            }
            renderCart();
        }

        // Hapus produk dari keranjang
        function removeFromCart(id) {
            cart = cart.filter(item => item.id !== id);
            renderCart();
        }

        // Render keranjang belanja
        function renderCart() {
            const cartList = document.getElementById('cartList');
            if (cart.length === 0) {
                cartList.innerHTML = `<li style="color: #94a3b8; text-align: center; padding: 20px 0;">Keranjang masih kosong</li>`;
            } else {
                cartList.innerHTML = cart.map(item => `
                    <li class="cart-item">
                        <span>${item.name} (x${item.qty})</span>
                        <span>Rp ${(item.price * item.qty).toLocaleString('id-ID')}</span>
                        <button onclick="removeFromCart(${item.id})">Hapus</button>
                    </li>
                `).join('');
            }
            hitungTotal();
        }

        // Hitung total belanjaan
        function hitungTotal() {
            const total = cart.reduce((sum, item) => sum + (item.price * item.qty), 0);
            document.getElementById('totalText').innerText = `Rp ${total.toLocaleString('id-ID')}`;
            hitungKembalian();
        }

        // Hitung kembalian otomatis
        function hitungKembalian() {
            const total = cart.reduce((sum, item) => sum + (item.price * item.qty), 0);
            const cash = parseFloat(document.getElementById('cashInput').value) || 0;
            const change = cash - total;

            const changeText = document.getElementById('changeText');
            if (change >= 0) {
                changeText.innerText = `Rp ${change.toLocaleString('id-ID')}`;
                changeText.style.color = '#2563eb';
            } else {
                changeText.innerText = `Uang Kurang`;
                changeText.style.color = '#ef4444';
            }
        }

        // Proses pembayaran selesai
        function prosesBayar() {
            const total = cart.reduce((sum, item) => sum + (item.price * item.qty), 0);
            const cash = parseFloat(document.getElementById('cashInput').value) || 0;

            if (cart.length === 0) {
                alert("Keranjang masih kosong!");
                return;
            }
            if (cash < total) {
                alert("Uang pembayaran kurang!");
                return;
            }

            alert(`Transaksi Berhasil!\nKembalian: Rp ${(cash - total).toLocaleString('id-ID')}`);
            
            // Reset keranjang & input
            cart = [];
            document.getElementById('cashInput').value = '';
            renderCart();
        }

        // Inisialisasi awal saat halaman dibuka
        renderProducts();
    </script>
</body>
</html>
