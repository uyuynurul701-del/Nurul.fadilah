<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Aplikasi Kasir Modern</title>
    <script src="https://cdn.jsdelivr.net/npm/@tailwindcss/browser@4"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
</head>
<body class="bg-slate-100 font-sans text-slate-800 antialiased h-screen flex flex-col overflow-hidden">

    <header class="bg-white border-b border-slate-200 px-6 py-4 flex justify-between items-center shadow-xs">
        <div class="flex items-center space-x-3">
            <div class="bg-indigo-600 text-white p-2 rounded-lg shadow-sm">
                <i class="fa-solid fa-cash-register text-xl"></i>
            </div>
            <div>
                <h1 class="text-lg font-bold text-slate-900 leading-tight">KasirKu POS</h1>
                <p class="text-xs text-slate-500">Sistem Penjualan Modern</p>
            </div>
        </div>
        <div class="text-sm font-medium text-slate-600 bg-slate-100 px-3 py-1.5 rounded-full">
            <i class="fa-regular fa-calendar-days mr-2"></i><span id="currentDate"></span>
        </div>
    </header>

    <main class="flex-1 flex overflow-hidden p-4 gap-4">
        
        <section class="flex-1 flex flex-col bg-white rounded-2xl shadow-xs border border-slate-200 overflow-hidden">
            <div class="p-4 border-b border-slate-100 flex justify-between items-center bg-white">
                <h2 class="font-bold text-slate-700">Daftar Menu / Produk</h2>
                <span class="text-xs bg-indigo-50 text-indigo-600 font-semibold px-2.5 py-1 rounded-md">Pilih Produk</span>
            </div>
            
            <div class="flex-1 p-4 overflow-y-auto grid grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-4" id="productGrid">
                </div>
        </section>

        <section class="w-96 flex flex-col bg-white rounded-2xl shadow-xs border border-slate-200 overflow-hidden">
            <div class="p-4 border-b border-slate-100 bg-white flex justify-between items-center">
                <h2 class="font-bold text-slate-700">Keranjang Belanja</h2>
                <button onclick="clearCart()" class="text-xs text-rose-500 hover:text-rose-700 font-medium transition">Kosongkan</button>
            </div>

            <div class="flex-1 p-4 overflow-y-auto divide-y divide-slate-100" id="cartList">
                <div class="h-full flex flex-col items-center justify-center text-slate-400 space-y-2">
                    <i class="fa-solid fa-cart-shopping text-3xl"></i>
                    <p class="text-sm">Keranjang masih kosong</p>
                </div>
            </div>

            <div class="p-4 bg-slate-50 border-t border-slate-200 space-y-3">
                <div class="space-y-1.5 text-sm">
                    <div class="flex justify-between text-slate-500">
                        <span>Subtotal</span>
                        <span id="subtotalText">Rp 0</span>
                    </div>
                    <div class="flex justify-between text-lg font-bold text-slate-900 border-t border-slate-200 pt-2">
                        <span>Total Tagihan</span>
                        <span id="totalText" class="text-indigo-600">Rp 0</span>
                    </div>
                </div>

                <div class="space-y-1">
                    <label class="text-xs font-semibold text-slate-600">Uang Tunai (Rp)</label>
                    <input type="number" id="cashInput" placeholder="Masukkan nominal..." oninput="hitungKembalian()" 
                           class="w-full px-3 py-2 bg-white border border-slate-300 rounded-lg text-sm focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 transition">
                </div>

                <div class="flex justify-between items-center text-sm bg-indigo-50/50 p-2.5 rounded-lg border border-indigo-100">
                    <span class="font-medium text-slate-600">Kembalian:</span>
                    <span id="changeText" class="font-bold text-indigo-700">Rp 0</span>
                </div>

                <button onclick="prosesBayar()" 
                        class="w-full bg-indigo-600 hover:bg-indigo-700 text-white font-semibold py-2.5 px-4 rounded-xl shadow-sm hover:shadow transition duration-200 flex items-center justify-center space-x-2">
                    <i class="fa-solid fa-circle-check"></i>
                    <span>Selesaikan Pembayaran</span>
                </button>
            </div>
        </section>

    </main>

    <script>
        // Tanggal Hari Ini
        const options = { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric' };
        document.getElementById('currentDate').innerText = new Date().toLocaleDateString('id-ID', options);

        // Data Produk
        const products = [
            { id: 1, name: "Kopi Susu Gula Aren", price: 18000, category: "Minuman", icon: "fa-mug-hot" },
            { id: 2, name: "Matcha Latte", price: 22000, category: "Minuman", icon: "fa-cup-togo" },
            { id: 3, name: "Es Teh Manis", price: 5000, category: "Minuman", icon: "fa-glass-water" },
            { id: 4, name: "Nasi Goreng Spesial", price: 25000, category: "Makanan", icon: "fa-bowl-rice" },
            { id: 5, name: "Mie Goreng Telur", price: 15000, category: "Makanan", icon: "fa-plate-wheat" },
            { id: 6, name: "Roti Bakar Coklat", price: 12000, category: "Cemilan", icon: "fa-bread-slice" },
            { id: 7, name: "Kentang Goreng", price: 13000, category: "Cemilan", icon: "fa-fries" },
            { id: 8, name: "Air Mineral", price: 4000, category: "Minuman", icon: "fa-bottle-water" }
        ];

        let cart = [];

        // Render Produk ke Grid
        function renderProducts() {
            const grid = document.getElementById('productGrid');
            grid.innerHTML = products.map(p => `
                <div onclick="addToCart(${p.id})" class="bg-white border border-slate-200/80 rounded-xl p-3.5 flex flex-col justify-between cursor-pointer hover:border-indigo-500 hover:shadow-md transition group">
                    <div class="bg-slate-50 text-indigo-600 rounded-lg h-20 flex items-center justify-center group-hover:bg-indigo-50 transition">
                        <i class="fa-solid ${p.icon} text-2xl"></i>
                    </div>
                    <div class="mt-3">
                        <span class="text-[10px] uppercase font-bold tracking-wider text-slate-400">${p.category}</span>
                        <h3 class="font-semibold text-slate-800 text-sm leading-snug line-clamp-1">${p.name}</h3>
                        <p class="text-indigo-600 font-bold text-sm mt-1">Rp ${p.price.toLocaleString('id-ID')}</p>
                    </div>
                </div>
            `).join('');
        }

        // Tambah ke Keranjang
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

        // Ubah Kuantitas (+ / -)
        function changeQty(id, delta) {
            const item = cart.find(i => i.id === id);
            if (item) {
                item.qty += delta;
                if (item.qty <= 0) {
                    cart = cart.filter(i => i.id !== id);
                }
            }
            renderCart();
        }

        // Kosongkan Keranjang
        function clearCart() {
            cart = [];
            renderCart();
        }

        // Render Keranjang
        function renderCart() {
            const cartList = document.getElementById('cartList');
            if (cart.length === 0) {
                cartList.innerHTML = `
                    <div class="h-full flex flex-col items-center justify-center text-slate-400 space-y-2">
                        <i class="fa-solid fa-cart-shopping text-3xl"></i>
                        <p class="text-sm">Keranjang masih kosong</p>
                    </div>
                `;
            } else {
                cartList.innerHTML = cart.map(item => `
                    <div class="py-3 flex items-center justify-between">
                        <div class="flex-1 pr-2">
                            <h4 class="text-xs font-semibold text-slate-800 line-clamp-1">${item.name}</h4>
                            <p class="text-xs text-indigo-600 font-medium">Rp ${item.price.toLocaleString('id-ID')}</p>
                        </div>
                        <div class="flex items-center space-x-2">
                            <button onclick="changeQty(${item.id}, -1)" class="w-6 h-6 bg-slate-100 hover:bg-slate-200 text-slate-600 rounded flex items-center justify-center text-xs transition"><i class="fa-solid fa-minus"></i></button>
                            <span class="text-xs font-bold w-4 text-center">${item.qty}</span>
                            <button onclick="changeQty(${item.id}, 1)" class="w-6 h-6 bg-slate-100 hover:bg-slate-200 text-slate-600 rounded flex items-center justify-center text-xs transition"><i class="fa-solid fa-plus"></i></button>
                        </div>
                    </div>
                `).join('');
            }
            hitungTotal();
        }

        // Hitung Total dan Kembalian
        function hitungTotal() {
            const total = cart.reduce((sum, item) => sum + (item.price * item.qty), 0);
            document.getElementById('subtotalText').innerText = `Rp ${total.toLocaleString('id-ID')}`;
            document.getElementById('totalText').innerText = `Rp ${total.toLocaleString('id-ID')}`;
            hitungKembalian();
        }

        function hitungKembalian() {
            const total = cart.reduce((sum, item) => sum + (item.price * item.qty), 0);
            const cash = parseFloat(document.getElementById('cashInput').value) || 0;
            const change = cash - total;

            const changeText = document.getElementById('changeText');
            if (change >= 0) {
                changeText.innerText = `Rp ${change.toLocaleString('id-ID')}`;
                changeText.classList.remove('text-rose-600');
                changeText.classList.add('text-indigo-700');
            } else {
                changeText.innerText = `Uang Kurang`;
                changeText.classList.remove('text-indigo-700');
                changeText.classList.add('text-rose-600');
            }
        }

        // Proses Selesai Pembayaran
        function prosesBayar() {
            const total = cart.reduce((sum, item) => sum + (item.price * item.qty), 0);
            const cash = parseFloat(document.getElementById('cashInput').value) || 0;

            if (cart.length === 0) {
                alert("Keranjang masih kosong!");
                return;
            }
            if (cash < total) {
                alert("Nominal uang tunai kurang dari total tagihan!");
                return;
            }

            const kembalian = cash - total;
            alert(`Transaksi Sukses!\n\nTotal: Rp ${total.toLocaleString('id-ID')}\nBayar: Rp ${cash.toLocaleString('id-ID')}\nKembalian: Rp ${kembalian.toLocaleString('id-ID')}`);

            // Reset setelah sukses
            cart = [];
            document.getElementById('cashInput').value = '';
            renderCart();
        }

        // Inisialisasi awal
        renderProducts();
    </script>
</body>
</html>
