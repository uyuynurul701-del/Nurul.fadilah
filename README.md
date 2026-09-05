<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>KasirKu Pro - POS System</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.jsdelivr.net/npm/@tailwindcss/browser@4"></script>
    <!-- FontAwesome -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
</head>
<body class="bg-slate-900 font-sans text-slate-100 h-screen flex flex-col overflow-hidden">

    <!-- Top Header -->
    <header class="bg-slate-800 border-b border-slate-700 px-6 py-3.5 flex justify-between items-center shadow-md">
        <div class="flex items-center space-x-3">
            <div class="bg-gradient-to-tr from-indigo-500 to-violet-500 text-white p-2.5 rounded-xl shadow-lg">
                <i class="fa-solid fa-cash-register text-lg"></i>
            </div>
            <div>
                <h1 class="text-base font-bold tracking-wide text-white">KasirKu Pro</h1>
                <p class="text-xs text-slate-400">Point of Sales Dashboard</p>
            </div>
        </div>
        <div class="flex items-center space-x-4">
            <div class="text-xs bg-slate-700/60 border border-slate-600 px-3 py-1.5 rounded-lg text-slate-300">
                <i class="fa-regular fa-calendar-days mr-2 text-indigo-400"></i><span id="currentDate"></span>
            </div>
        </div>
    </header>

    <!-- Main Workspace -->
    <main class="flex-1 flex overflow-hidden p-4 gap-4">
        
        <!-- Kiri: Katalog Produk & Kategori -->
        <section class="flex-1 flex flex-col bg-slate-800 rounded-2xl border border-slate-700 overflow-hidden shadow-xl">
            <!-- Kategori Filter -->
            <div class="p-4 border-b border-slate-700 flex items-center space-x-2 overflow-x-auto">
                <button onclick="filterCategory('Semua')" id="btn-Semua" class="cat-btn px-4 py-2 text-xs font-semibold rounded-xl bg-indigo-600 text-white transition shadow-sm">Semua Menu</button>
                <button onclick="filterCategory('Minuman')" id="btn-Minuman" class="cat-btn px-4 py-2 text-xs font-semibold rounded-xl bg-slate-700 hover:bg-slate-600 text-slate-300 transition">Minuman</button>
                <button onclick="filterCategory('Makanan')" id="btn-Makanan" class="cat-btn px-4 py-2 text-xs font-semibold rounded-xl bg-slate-700 hover:bg-slate-600 text-slate-300 transition">Makanan</button>
                <button onclick="filterCategory('Cemilan')" id="btn-Cemilan" class="cat-btn px-4 py-2 text-xs font-semibold rounded-xl bg-slate-700 hover:bg-slate-600 text-slate-300 transition">Cemilan</button>
            </div>

            <!-- Grid Produk -->
            <div class="flex-1 p-4 overflow-y-auto grid grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-3.5" id="productGrid">
                <!-- Data produk dimuat via JS -->
            </div>
        </section>

        <!-- Kanan: Keranjang & Checkout -->
        <section class="w-[380px] flex flex-col bg-slate-800 rounded-2xl border border-slate-700 overflow-hidden shadow-xl">
            <div class="p-4 border-b border-slate-700 flex justify-between items-center bg-slate-800/50">
                <h2 class="font-bold text-sm text-white flex items-center"><i class="fa-solid fa-basket-shopping mr-2 text-indigo-400"></i> Keranjang Pesanan</h2>
                <button onclick="clearCart()" class="text-xs text-rose-400 hover:text-rose-300 font-medium transition flex items-center"><i class="fa-solid fa-trash-can mr-1"></i> Kosongkan</button>
            </div>

            <!-- List Item -->
            <div class="flex-1 p-4 overflow-y-auto divide-y divide-slate-700/60" id="cartList">
                <div class="h-full flex flex-col items-center justify-center text-slate-500 space-y-2">
                    <i class="fa-solid fa-cart-shopping text-4xl text-slate-600"></i>
                    <p class="text-xs">Belum ada item dipilih</p>
                </div>
            </div>

            <!-- Summary & Payment -->
            <div class="p-4 bg-slate-900/60 border-t border-slate-700 space-y-3">
                <div class="space-y-1.5 text-xs">
                    <div class="flex justify-between text-slate-400">
                        <span>Total Tagihan</span>
                        <span id="totalText" class="text-base font-bold text-indigo-400">Rp 0</span>
                    </div>
                </div>

                <div class="space-y-1">
                    <label class="text-[11px] font-semibold text-slate-400">Nominal Tunai (Rp)</label>
                    <div class="relative">
                        <span class="absolute inset-y-0 left-0 pl-3 flex items-center text-xs text-slate-500 font-bold">Rp</span>
                        <input type="number" id="cashInput" placeholder="0" oninput="hitungKembalian()" 
                               class="w-full pl-9 pr-3 py-2 bg-slate-800 border border-slate-700 rounded-xl text-sm text-white focus:outline-none focus:ring-2 focus:ring-indigo-500 transition">
                    </div>
                </div>

                <div class="flex justify-between items-center text-xs bg-indigo-950/40 p-2.5 rounded-xl border border-indigo-900/50">
                    <span class="font-medium text-indigo-300">Kembalian:</span>
                    <span id="changeText" class="font-bold text-indigo-400 text-sm">Rp 0</span>
                </div>

                <button onclick="prosesBayar()" 
                        class="w-full bg-gradient-to-r from-indigo-600 to-violet-600 hover:from-indigo-500 hover:to-violet-500 text-white font-semibold py-2.5 px-4 rounded-xl shadow-lg shadow-indigo-600/20 transition duration-200 flex items-center justify-center space-x-2 text-sm">
                    <i class="fa-solid fa-circle-check"></i>
                    <span>Bayar Sekarang</span>
                </button>
            </div>
        </section>

    </main>

    <!-- Modal Struk Pembayaran Sukses -->
    <div id="receiptModal" class="fixed inset-0 bg-slate-950/80 backdrop-blur-xs hidden items-center justify-center p-4 z-50">
        <div class="bg-slate-800 border border-slate-700 rounded-2xl w-full max-w-sm p-6 shadow-2xl space-y-4">
            <div class="text-center space-y-1">
                <div class="w-12 h-12 bg-emerald-500/10 border border-emerald-500/20 text-emerald-400 rounded-full flex items-center justify-center mx-auto mb-2 text-xl">
                    <i class="fa-solid fa-check"></i>
                </div>
                <h3 class="font-bold text-base text-white">Pembayaran Berhasil!</h3>
                <p class="text-xs text-slate-400" id="receiptDate">Waktu Transaksi</p>
            </div>
            
            <div class="border-t border-b border-slate-700 py-3 space-y-2 text-xs" id="receiptItems">
                <!-- Struk item list -->
            </div>

            <div class="space-y-1 text-xs">
                <div class="flex justify-between text-slate-400"><span>Total Belanja:</span> <span id="rTotal" class="text-white font-semibold">Rp 0</span></div>
                <div class="flex justify-between text-slate-400"><span>Tunai Dibayar:</span> <span id="rCash" class="text-white font-semibold">Rp 0</span></div>
                <div class="flex justify-between text-slate-400"><span>Kembalian:</span> <span id="rChange" class="text-indigo-400 font-bold">Rp 0</span></div>
            </div>

            <button onclick="closeModal()" class="w-full bg-slate-700 hover:bg-slate-600 text-white font-semibold py-2.5 rounded-xl text-xs transition">
                Tutup & Transaksi Baru
            </button>
        </div>
    </div>

    <!-- Script Logika -->
    <script>
        // Tanggal
        const options = { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric' };
        document.getElementById('currentDate').innerText = new Date().toLocaleDateString('id-ID', options);

        // Data Produk
        const products = [
            { id: 1, name: "Kopi Susu Gula Aren", price: 18000, category: "Minuman", icon: "fa-mug-hot" },
            { id: 2, name: "Matcha Latte Premium", price: 24000, category: "Minuman", icon: "fa-cup-togo" },
            { id: 3, name: "Es Teh Manis Segar", price: 5000, category: "Minuman", icon: "fa-glass-water" },
            { id: 4, name: "Nasi Goreng Spesial", price: 25000, category: "Makanan", icon: "fa-bowl-rice" },
            { id: 5, name: "Mie Goreng Telur Kornet", price: 16000, category: "Makanan", icon: "fa-plate-wheat" },
            { id: 6, name: "Roti Bakar Keju Coklat", price: 14000, category: "Cemilan", icon: "fa-bread-slice" },
            { id: 7, name: "French Fries Crispy", price: 13000, category: "Cemilan", icon: "fa-fries" },
            { id: 8, name: "Air Mineral 600ml", price: 4000, category: "Minuman", icon: "fa-bottle-water" }
        ];

        let cart = [];
        let activeCategory = 'Semua';

        // Filter Kategori
        function filterCategory(category) {
            activeCategory = category;
            document.querySelectorAll('.cat-btn').forEach(btn => {
                btn.classList.remove('bg-indigo-600', 'text-white', 'shadow-sm');
                btn.classList.add('bg-slate-700', 'text-slate-300');
            });
            const activeBtn = document.getElementById(`btn-${category}`);
            activeBtn.classList.remove('bg-slate-700', 'text-slate-300');
            activeBtn.classList.add('bg-indigo-600', 'text-white', 'shadow-sm');
            renderProducts();
        }

        // Render Produk
        function renderProducts() {
            const grid = document.getElementById('productGrid');
            const filtered = activeCategory === 'Semua' ? products : products.filter(p => p.category === activeCategory);
            
            grid.innerHTML = filtered.map(p => `
                <div onclick="addToCart(${p.id})" class="bg-slate-900/40 border border-slate-700/70 rounded-xl p-3 flex flex-col justify-between cursor-pointer hover:border-indigo-500 hover:bg-slate-900/80 transition group">
                    <div class="bg-slate-800/80 text-indigo-400 rounded-lg h-16 flex items-center justify-center group-hover:scale-105 transition duration-200">
                        <i class="fa-solid ${p.icon} text-xl"></i>
                    </div>
                    <div class="mt-2.5">
                        <span class="text-[9px] uppercase font-bold tracking-widest text-slate-500">${p.category}</span>
                        <h3 class="font-medium text-slate-200 text-xs leading-snug line-clamp-1">${p.name}</h3>
                        <p class="text-indigo-400 font-bold text-xs mt-1">Rp ${p.price.toLocaleString('id-ID')}</p>
                    </div>
                </div>
            `).join('');
        }

        // Tambah Item
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

        // Ubah Kuantitas
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

        function clearCart() {
            cart = [];
            renderCart();
        }

        // Render Keranjang
        function renderCart() {
            const cartList = document.getElementById('cartList');
            if (cart.length === 0) {
                cartList.innerHTML = `
                    <div class="h-full flex flex-col items-center justify-center text-slate-500 space-y-2">
                        <i class="fa-solid fa-cart-shopping text-4xl text-slate-600"></i>
                        <p class="text-xs">Belum ada item dipilih</p>
                    </div>
                `;
            } else {
                cartList.innerHTML = cart.map(item => `
                    <div class="py-2.5 flex items-center justify-between">
                        <div class="flex-1 pr-2">
                            <h4 class="text-xs font-medium text-slate-200 line-clamp-1">${item.name}</h4>
                            <p class="text-[11px] text-indigo-400 font-semibold">Rp ${(item.price * item.qty).toLocaleString('id-ID')}</p>
                        </div>
                        <div class="flex items-center space-x-1.5">
                            <button onclick="changeQty(${item.id}, -1)" class="w-5 h-5 bg-slate-700 hover:bg-slate-600 text-slate-300 rounded flex items-center justify-center text-[10px] transition"><i class="fa-solid fa-minus"></i></button>
                            <span class="text-xs font-bold w-5 text-center text-white">${item.qty}</span>
                            <button onclick="changeQty(${item.id}, 1)" class="w-5 h-5 bg-slate-700 hover:bg-slate-600 text-slate-300 rounded flex items-center justify-center text-[10px] transition"><i class="fa-solid fa-plus"></i></button>
                        </div>
                    </div>
                `).join('');
            }
            hitungTotal();
        }

        function hitungTotal() {
            const total = cart.reduce((sum, item) => sum + (item.price * item.qty), 0);
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
                changeText.className = "font-bold text-indigo-400 text-sm";
            } else {
                changeText.innerText = `Uang Kurang`;
                changeText.className = "font-bold text-rose-400 text-sm";
            }
        }

        // Proses Bayar & Tampilkan Struk Modal
        function prosesBayar() {
            const total = cart.reduce((sum, item) => sum + (item.price * item.qty), 0);
            const cash = parseFloat(document.getElementById('cashInput').value) || 0;

            if (cart.length === 0) {
                alert("Keranjang masih kosong!");
                return;
            }
            if (cash < total) {
                alert("Nominal uang tunai kurang!");
                return;
            }

            // Isi data modal struk
            document.getElementById('receiptDate').innerText = new Date().toLocaleString('id-ID');
            document.getElementById('rTotal').innerText = `Rp ${total.toLocaleString('id-ID')}`;
            document.getElementById('rCash').innerText = `Rp ${cash.toLocaleString('id-ID')}`;
            document.getElementById('rChange').innerText = `Rp ${(cash - total).toLocaleString('id-ID')}`;

            const rItems = document.getElementById('receiptItems');
            rItems.innerHTML = cart.map(i => `
                <div class="flex justify-between text-slate-300">
                    <span>${i.name} (${i.qty}x)</span>
                    <span>Rp ${(i.price * i.qty).toLocaleString('id-ID')}</span>
                </div>
            `).join('');

            // Tampilkan modal
            document.getElementById('receiptModal').classList.remove('hidden');
            document.getElementById('receiptModal').classList.add('flex');
        }

        function closeModal() {
            document.getElementById('receiptModal').classList.remove('flex');
            document.getElementById('receiptModal').classList.add('hidden');
            
            // Reset kasir
            cart = [];
            document.getElementById('cashInput').value = '';
            renderCart();
        }

        // Inisialisasi
        renderProducts();
    </script>
</body>
</html>
