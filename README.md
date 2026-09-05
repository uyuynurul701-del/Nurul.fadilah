<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Aplikasi Kasir - Nurul Fadilah</title>
    <!-- Google Fonts & FontAwesome -->
    <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;500;600;700&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Poppins', sans-serif;
        }

        body {
            background-color: #f4f7f6;
            color: #333;
            display: flex;
            flex-direction: column;
            min-height: 100vh;
        }

        header {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            padding: 20px 30px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            box-shadow: 0 4px 6px rgba(0,0,0,0.1);
        }

        header h1 {
            font-size: 24px;
            font-weight: 600;
        }

        header p {
            font-size: 14px;
            opacity: 0.9;
        }

        .container {
            display: flex;
            flex: 1;
            padding: 20px;
            gap: 20px;
            max-width: 1400px;
            margin: 0 auto;
            width: 100%;
        }

        /* Bagian Produk */
        .products-section {
            flex: 2;
            background: white;
            border-radius: 12px;
            padding: 20px;
            box-shadow: 0 4px 12px rgba(0,0,0,0.05);
            display: flex;
            flex-direction: column;
        }

        .section-title {
            font-size: 18px;
            font-weight: 600;
            margin-bottom: 15px;
            color: #444;
            display: flex;
            align-items: center;
            gap: 10px;
        }

        .product-grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(160px, 1fr));
            gap: 15px;
            overflow-y: auto;
            max-height: 500px;
            padding-right: 5px;
        }

        .product-card {
            background: #fff;
            border: 2px solid #eee;
            border-radius: 10px;
            padding: 15px;
            text-align: center;
            cursor: pointer;
            transition: all 0.3s ease;
            display: flex;
            flex-direction: column;
            justify-content: space-between;
        }

        .product-card:hover {
            border-color: #667eea;
            transform: translateY(-3px);
            box-shadow: 0 6px 15px rgba(102, 126, 234, 0.15);
        }

        .product-card i {
            font-size: 35px;
            color: #667eea;
            margin-bottom: 10px;
        }

        .product-card h4 {
            font-size: 14px;
            font-weight: 500;
            margin-bottom: 8px;
        }

        .product-card .price {
            font-size: 13px;
            font-weight: 600;
            color: #27ae60;
        }

        /* Bagian Keranjang / Kasir */
        .cart-section {
            flex: 1;
            background: white;
            border-radius: 12px;
            padding: 20px;
            box-shadow: 0 4px 12px rgba(0,0,0,0.05);
            display: flex;
            flex-direction: column;
            justify-content: space-between;
        }

        .cart-items {
            flex: 1;
            overflow-y: auto;
            max-height: 250px;
            margin-bottom: 15px;
            border-bottom: 1px solid #eee;
            padding-right: 5px;
        }

        .cart-item {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 10px;
            font-size: 13px;
            background: #f9f9f9;
            padding: 8px 10px;
            border-radius: 8px;
        }

        .cart-item-info {
            display: flex;
            flex-direction: column;
        }

        .cart-item-info span:last-child {
            color: #777;
            font-size: 11px;
        }

        .cart-item-actions {
            display: flex;
            align-items: center;
            gap: 8px;
        }

        .cart-item-actions button {
            background: #eee;
            border: none;
            width: 22px;
            height: 22px;
            border-radius: 4px;
            cursor: pointer;
            font-weight: bold;
        }

        .cart-item-actions button:hover {
            background: #ddd;
        }

        .cart-summary {
            font-size: 14px;
        }

        .summary-row {
            display: flex;
            justify-content: space-between;
            margin-bottom: 8px;
        }

        .summary-row.total {
            font-size: 18px;
            font-weight: 700;
            color: #2c3e50;
            border-top: 2px dashed #eee;
            padding-top: 8px;
            margin-top: 8px;
        }

        .payment-box {
            margin-top: 10px;
        }

        .payment-box label {
            font-size: 12px;
            font-weight: 500;
            color: #555;
        }

        .payment-box input {
            width: 100%;
            padding: 10px;
            border: 1px solid #ddd;
            border-radius: 8px;
            margin-top: 5px;
            font-size: 14px;
        }

        .btn-checkout {
            background: #27ae60;
            color: white;
            border: none;
            width: 100%;
            padding: 12px;
            border-radius: 8px;
            font-size: 15px;
            font-weight: 600;
            cursor: pointer;
            margin-top: 15px;
            transition: background 0.3s;
        }

        .btn-checkout:hover {
            background: #219653;
        }

        footer {
            text-align: center;
            padding: 15px;
            background: #fff;
            color: #777;
            font-size: 13px;
            border-top: 1px solid #eee;
        }

        /* Responsif untuk HP */
        @media (max-width: 900px) {
            .container {
                flex-direction: column;
            }
        }
    </style>
</head>
<body>

    <!-- Header -->
    <header>
        <div>
            <h1><i class="fa-solid fa-cash-register"></i> Aplikasi Kasir</h1>
            <p>Sistem Point of Sale Modern</p>
        </div>
        <div>
            <p><i class="fa-solid fa-user-shield">️</i> Dibuat oleh: <strong>Nurul Fadilah</strong></p>
        </div>
    </header>

    <!-- Konten Utama -->
    <div class="container">
        <!-- Daftar Produk -->
        <div class="products-section">
            <div class="section-title"><i class="fa-solid fa-box-open"></i> Pilih Produk</div>
            <div class="product-grid" id="productGrid">
                <!-- Produk akan dimuat lewat JavaScript -->
            </div>
        </div>

        <!-- Keranjang & Pembayaran -->
        <div class="cart-section">
            <div>
                <div class="section-title"><i class="fa-solid fa-cart-shopping"></i> Keranjang Belanja</div>
                <div class="cart-items" id="cartItems">
                    <p style="text-align: center; color: #aaa; margin-top: 20px; font-size: 13px;">Keranjang masih kosong</p>
                </div>
            </div>

            <div class="cart-summary">
                <div class="summary-row total">
                    <span>Total:</span>
                    <span id="grandTotal">Rp 0</span>
                </div>
                
                <div class="payment-box">
                    <label for="cashAmount">Uang Tunai (Rp):</label>
                    <input type="number" id="cashAmount" placeholder="Masukkan jumlah uang..." oninput="calculateChange()">
                </div>

                <div class="summary-row" style="margin-top: 10px;">
                    <span style="font-size: 13px; color: #555;">Kembalian:</span>
                    <span id="changeAmount" style="font-weight: 600; color: #2980b9;">Rp 0</span>
                </div>

                <button class="btn-checkout" onclick="processPayment()"><i class="fa-solid fa-check"></i> Bayar & Cetak Struk</button>
            </div>
        </div>
    </div>

    <!-- Footer -->
    <footer>
        <p>&copy; 2026 Aplikasi Kasir Profesional | Dibuat dengan ❤️ oleh <strong>Nurul Fadilah</strong></p>
    </footer>

    <!-- JavaScript -->
    <script>
        // Data Produk
        const products = [
            { id: 1, name: "Kopi Espresso", price: 18000, icon: "fa-mug-hot" },
            { id: 2, name: "Caffe Latte", price: 22000, icon: "fa-coffee" },
            { id: 3, name: "Juice Alpukat", price: 15000, icon: "fa-glass-water" },
            { id: 4, name: "Es Teh Manis", price: 5000, icon: "fa-cup-straw" },
            { id: 5, name: "Roti Bakar", price: 12000, icon: "fa-bread-slice" },
            { id: 6, name: "Nasi Goreng", price: 25000, icon: "fa-bowl-food" },
            { id: 7, name: "Mie Goreng", price: 20000, icon: "fa-plate-wheat" },
            { id: 8, name: "Kentang Goreng", price: 14000, icon: "fa-utensils" }
        ];

        let cart = [];

        // Tampilkan Produk ke Layar
        function displayProducts() {
            const grid = document.getElementById('productGrid');
            grid.innerHTML = "";
            products.forEach(product => {
                grid.innerHTML += `
                    <div class="product-card" onclick="addToCart(${product.id})">
                        <i class="fa-solid ${product.icon}"></i>
                        <div>
                            <h4>${product.name}</h4>
                            <div class="price">Rp ${product.price.toLocaleString('id-ID')}</div>
                        </div>
                    </div>
                `;
            });
        }

        // Tambah ke Keranjang
        function addToCart(productId) {
            const product = products.find(p => p.id === productId);
            const cartItem = cart.find(item => item.id === productId);

            if (cartItem) {
                cartItem.qty += 1;
            } else {
                cart.push({ ...product, qty: 1 });
            }
            updateCart();
        }

        // Ubah Jumlah Qty
        function changeQty(productId, amount) {
            const cartItem = cart.find(item => item.id === productId);
            if (cartItem) {
                cartItem.qty += amount;
                if (cartItem.qty <= 0) {
                    cart = cart.filter(item => item.id !== productId);
                }
            }
            updateCart();
        }

        // Update Tampilan Keranjang
        function updateCart() {
            const container = document.getElementById('cartItems');
            const grandTotalEl = document.getElementById('grandTotal');
            
            if (cart.length === 0) {
                container.innerHTML = `<p style="text-align: center; color: #aaa; margin-top: 20px; font-size: 13px;">Keranjang masih kosong</p>`;
                grandTotalEl.innerText = "Rp 0";
                document.getElementById('changeAmount').innerText = "Rp 0";
                return;
            }

            container.innerHTML = "";
            let total = 0;

            cart.forEach(item => {
                let subtotal = item.price * item.qty;
                total += subtotal;
                container.innerHTML += `
                    <div class="cart-item">
                        <div class="cart-item-info">
                            <span><strong>${item.name}</strong></span>
                            <span>Rp ${item.price.toLocaleString('id-ID')} x ${item.qty}</span>
                        </div>
                        <div class="cart-item-actions">
                            <button onclick="changeQty(${item.id}, -1)">-</button>
                            <span>${item.qty}</span>
                            <button onclick="changeQty(${item.id}, 1)">+</button>
                        </div>
                    </div>
                `;
            });

            grandTotalEl.innerText = `Rp ${total.toLocaleString('id-ID')}`;
            calculateChange();
        }

        // Hitung Kembalian
        function calculateChange() {
            const total = cart.reduce((sum, item) => sum + (item.price * item.qty), 0);
            const cash = parseFloat(document.getElementById('cashAmount').value) || 0;
            const change = cash - total;

            const changeEl = document.getElementById('changeAmount');
            if (change >= 0) {
                changeEl.innerText = `Rp ${change.toLocaleString('id-ID')}`;
                changeEl.style.color = "#2980b9";
            } else {
                changeEl.innerText = "Uang Kurang";
                changeEl.style.color = "#e74c3c";
            }
        }

        // Proses Pembayaran & Struk
        function processPayment() {
            if (cart.length === 0) {
                alert("Keranjang masih kosong!");
                return;
            }

            const total = cart.reduce((sum, item) => sum + (item.price * item.qty), 0);
            const cash = parseFloat(document.getElementById('cashAmount').value) || 0;

            if (cash < total) {
                alert("Uang tunai kurang dari total belanja!");
                return;
            }

            const change = cash - total;
            
            let struk = `=== STRUK PEMBAYARAN ===\n`;
            struk += `APLIKASI KASIR\n`;
            struk += `Kasir: Nurul Fadilah\n`;
            struk += `------------------------\n`;
            cart.forEach(item => {
                struk += `${item.name} x${item.qty} = Rp ${(item.price * item.qty).toLocaleString('id-ID')}\n`;
            });
            struk += `------------------------\n`;
            struk += `Total: Rp ${total.toLocaleString('id-ID')}\n`;
            struk += `Tunai: Rp ${cash.toLocaleString('id-ID')}\n`;
            struk += `Kembali: Rp ${change.toLocaleString('id-ID')}\n`;
            struk += `========================\n`;
            struk += `Terima Kasih Telah Berbelanja!`;

            alert(struk);

            // Reset Kasir
            cart = [];
            document.getElementById('cashAmount').value = "";
            updateCart();
        }

        // Inisialisasi awal
        displayProducts();
    </script>
</body>
</html>
