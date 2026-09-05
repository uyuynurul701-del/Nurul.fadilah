<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>KasirKu Enterprise - Ultimate POS</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.jsdelivr.net/npm/@tailwindcss/browser@4"></script>
    <!-- FontAwesome -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        @media print {
            body * { visibility: hidden; }
            #receiptPrintArea, #receiptPrintArea * { visibility: visible; }
            #receiptPrintArea { position: absolute; left: 0; top: 0; width: 100%; color: black !important; background: white !important; }
        }
    </style>
</head>
<body class="bg-slate-950 font-sans text-slate-100 h-screen flex flex-col overflow-hidden">

    <!-- Top Header -->
    <header class="bg-slate-900 border-b border-slate-800 px-6 py-3.5 flex justify-between items-center shadow-lg">
        <div class="flex items-center space-x-3">
            <div class="bg-gradient-to-tr from-blue-600 to-indigo-600 text-white p-2.5 rounded-xl shadow-lg shadow-indigo-500/20">
                <i class="fa-solid fa-store text-lg"></i>
            </div>
            <div>
                <h1 class="text-base font-bold tracking-wide text-white">KasirKu Enterprise</h1>
                <p class="text-xs text-slate-400">Advanced Point of Sales System</p>
            </div>
        </div>
        <div class="flex items-center space-x-3">
            <div class="text-xs bg-slate-800 border border-slate-700 px-3.5 py-1.5 rounded-xl text-slate-300 flex items-center space-x-2">
                <i class="fa-regular fa-calendar-days text-indigo-400"></i>
                <span id="currentDate"></span>
            </div>
        </div>
    </header>

    <!-- Main Workspace -->
    <main class="flex-1 flex overflow-hidden p-4 gap-4">
        
        <!-- Kiri: Katalog & Pencarian -->
        <section class="flex-1 flex flex-col bg-slate-900 rounded-2xl border border-slate-800 overflow-hidden shadow-2xl">
            <!-- Search & Filter Bar -->
            <div class="p-4 border-b border-slate-800 flex flex-col md:flex-row gap-3 justify-between items-center bg-slate-900/60">
                <div class="relative w-full md:w-72">
                    <span class="absolute inset-y-0 left-0 pl-3.5 flex items-center text-slate-400 text-xs">
                        <i class="fa-solid fa-magnifying-glass"></i>
                    </span>
                    <input type="text" id="searchInput" oninput="filterProducts()" placeholder="Cari nama menu..." 
                           class="w-full pl-9 pr-4 py-2 bg-slate-800 border border-slate-700 rounded-xl text-xs text-white focus:outline-none focus:ring-2 focus:ring-indigo-500 transition">
                </div>
                <div class="flex items-center space-x-2 overflow-x-auto w-full md:w-auto">
                    <button onclick="filterCategory('Semua')" id="btn-Semua" class="cat-btn px-3.5 py-1.5 text-xs font-semibold rounded-xl bg-indigo-600 text-white transition shadow-sm">Semua</button>
                    <button onclick="filterCategory('Minuman')" id="btn-Minuman" class="cat-btn px-3.5 py-1.5 text-xs font-semibold rounded-xl bg-slate-800 hover:bg-slate-700 text-slate-300 transition">Minuman</button>
                    <button onclick="filterCategory('Makanan')" id="btn-Makanan" class="cat-btn px-3.5 py-1.5 text-xs font-semibold rounded-xl bg-slate-800 hover:bg-slate-700 text-slate-300 transition">Makanan</button>
                    <button onclick="filterCategory('Cemilan')" id="btn-Cemilan" class="cat-btn px-3.5 py-1.5 text-xs font-semibold rounded-xl bg-slate-800 hover:bg-slate-700 text-slate-300 transition">Cemilan</button>
                </div>
            </div>

            <!-- Grid Produk -->
            <div class="flex-1 p-4 overflow-y-auto grid grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-3.5" id="productGrid">
                <!-- Data produk dimuat via JS -->
            </div>
        </section>

        <!-- Kanan: Keranjang & Checkout -->
        <section class="w-[400px] flex flex-col bg-slate-900 rounded-2xl border border-slate-800 overflow-hidden shadow-2xl">
            <div class="p-4 border-b border-slate-800 flex justify-between items-center bg-slate-900/60">
                <h2 class="font-bold text-sm text-white flex items-center"><i class="fa-solid fa-receipt mr-2 text-indigo-400"></i> Keranjang Transaksi</h2>
                <button onclick="clearCart()" class="text-xs text-rose-400 hover:text-rose-300 font-medium transition flex items-center"><i class="fa-solid fa-rotate-right mr-1"></i> Reset</button>
            </div>

            <!-- List Item Keranjang -->
            <div class="flex-1 p-4 overflow-y-auto divide-y divide-slate-800/60" id="cartList">
                <div class="h-full flex flex-col items-center justify-center text-slate-500 space-y-2">
                    <i class="fa-solid fa-basket-shopping text-4xl text-slate-700"></i>
                    <p class="text-xs">Keranjang masih kosong</p>
                </div>
            </div>

            <!-- Payment Summary Area -->
            <div class="p-4 bg-slate-950/80 border-t border-slate-800 space-y-3">
                <!-- Metode Pembayaran Switcher -->
                <div class="grid grid-cols-2 gap-2 bg-slate-900 p-1 rounded-xl border border-slate-800">
                    <button onclick="setPaymentMethod('cash')" id="methodCash" class="py-1.5 text-xs font-bold rounded-lg bg-indigo-600 text-white transition">Tunai</button>
                    <button onclick="setPaymentMethod('qris')" id="methodQris" class="py-1.5 text-xs font-bold rounded-lg text-slate-400 hover:text-white transition">QRIS</button>
                </div>

                <div class="space-y-1 text-xs">
                    <div class="flex justify-between text-slate-400">
                        <span>Subtotal</span>
                        <span id="subtotalText" class="text-white font-medium">Rp 0</span>
                    </div>
                    <div class="flex justify-between text-slate-400">
                        <span>Diskon (Rp)</span>
                        <input type="number" id="discountInput" value="0" oninput="hitungTotal()" class="w-28 px-2 py-0.5 bg-slate-800 border border-slate-700 rounded text-right text-xs text-white focus:outline-none">
                    </div>
                    <div class="flex justify-between text-slate-200 border-t border-slate-800 pt-2 text-sm font-bold">
                        <span>Total Akhir</span>
                        <span id="totalText" class="text-indigo-400">Rp 0</span>
                    </div>
                </div>

                <!-- Bagian Input Tunai -->
                <div id="cashSection" class="space-y-2 pt-1">
                    <div class="flex justify-between items-center">
                        <label class="text-[11px] font-semibold text-slate-400">Bayar Tunai</label>
                        <div class="space-x-1">
                            <button onclick="addCash(10000)" class="text-[10px] bg-slate-800 hover:bg-slate-700 px-1.5 py-0.5 rounded text-slate-300">+10rb</button>
                            <button onclick="addCash(50000)" class="text-[10px] bg-slate-800 hover:bg-slate-700 px-1.5 py-0.5 rounded text-slate-300">+50rb</button>
                            <button onclick="addCash(100000)" class="text-[10px] bg-slate-800 hover:bg-slate-700 px-1.5 py-0.5 rounded text-slate-300">+100rb</button>
                        </div>
                    </div>
                    <input type="number" id="cashInput" placeholder="Masukkan nominal..." oninput="hitungKembalian()" 
                           class="w-full px-3 py-2 bg-slate-900 border border-slate-800 rounded-xl text-xs text-white focus:outline-none focus:ring-2 focus:ring-indigo-500">
                    <div class="flex justify-between items-center text-xs bg-indigo-950/40 p-2 rounded-xl border border-indigo-950">
                        <span class="font-medium text-indigo-300">Kembalian:</span>
                        <span id="changeText" class="font-bold text-indigo-400">Rp 0</span>
                    </div>
                </div>

                <!-- Bagian QRIS (Hidden by default) -->
                <div id="qrisSection" class="hidden text-center py-2 space-y-2 bg-slate-900 border border-slate-800 rounded-xl">
                    <div class="w-24 h-24 bg-white p-1 rounded mx-auto flex items-center justify-center">
                        <i class="fa-solid fa-qrcode text-6xl text-slate-950"></i>
                    </div>
                    <p class="text-[10px] text-slate-400">Scan QRIS menggunakan GoPay, OVO, BCA, dll</p>
                </div>

                <button onclick="prosesBayar()" 
                        class="w-full bg-gradient-to-r from-indigo-600 to-violet-600 hover:from-indigo-500 hover:to-violet-500 text-white font-semibold py-2.5 px-4 rounded-xl shadow-lg shadow-indigo-600/20 transition duration-200 flex items-center justify-center space-x-2 text-xs">
                    <i class="fa-solid fa-circle-check"></i>
                    <span>Selesaikan Transaksi</span>
                </button>
            </div>
        </section>

    </main>

    <!-- Modal Struk & Nota Sukses -->
    <div id="receiptModal" class="fixed inset-0 bg-slate-950/80 backdrop-blur-xs hidden items-center justify-center p-4 z-50">
        <div class="bg-slate-900 border border-slate-800 rounded-2xl w-full max-w-sm p-6 shadow-2xl space-y-4">
            
            <!-- Area yang akan dicetak -->
            <div id="receiptPrintArea" class="bg-slate-900 text-slate-200 space-y-3 p-2">
                <div class="text-center space-y-1 border-b border-dashed border-slate-700 pb-3">
                    <h3 class="font-bold text-sm text-white">KASIRKU ENTERPRISE</h3>
                    <p class="text-[11px] text-slate-400">Jl. Teknologi No. 88, Nusantara</p>
                    <p class="text-[10px] text-slate-500" id="receiptDate">Waktu</p>
                </div>
                
                <div class="space-y-1.5 text-xs max-h-40 overflow-y-auto divide-y divide-slate-800" id="receiptItems">
                    <!-- Item struk -->
                </div>

                <div class="border-t border-dashed border-slate-700 pt-2 space-y-1 text-xs">
                    <div class="flex justify-between text-slate-400"><span>Subtotal:</span> <span id="rSub" class="text-white">Rp 0</span></div>
                    <div class="flex justify-between text-slate-400"><span>Diskon:</span> <span id="rDisc" class="text-white">Rp 0</span></div>
                    <div class="flex justify-between text-slate-200 font-bold pt-1 border-t border-slate-800"><span>Total:</span> <span id="rTotal" class="text-indigo-400">Rp 0</span></div>
                    <div class="flex justify-between text-slate-400 pt-1"><span>Metode:</span> <span id="rMethod" class="text-white font-medium uppercase">TUNAI</span></div>
                    <div class="flex justify-between text-slate-400" id="rCashRow"><span>Bayar:</span> <span id="rCash" class="text-white">Rp 0</span></div>
                    <div class="flex justify-between text-slate-400" id="rChangeRow"><span>Kembali:</span> <span id="rChange" class="text-indigo-400 font-bold">Rp 0</span></div>
                </div>
                <div class="text-center text-[10px] text-slate-500 pt-2 border-t border-dashed border-slate-700">
                    <p>Terima Kasih Atas Kunjungan Anda!</p>
                </div>
            </div>

            <!-- Tombol Aksi Modal -->
            <div class="flex gap-2 pt-2">
                <button onclick="window.print()" class="flex-1 bg-slate-800 hover:bg-slate-700 text-white font-semibold py-2 rounded-xl text-xs transition flex items-center justify-center space-x-1.5">
                    <i class="fa-solid fa-print"></i><span>Cetak Struk</span>
                </button>
                <button onclick="closeModal()" class="flex-1 bg-indigo-600 hover:bg-indigo-500 text-white font-semibold py-2 rounded-xl text-xs transition">
                    Transaksi Baru
                </button>
            </div>
        </div>
    </div>

    <!-- Script Logika -->
    <script>
        // Tanggal Header
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
            { id: 8, name: "Air Mineral 600ml", price: 4000, category: "Minuman", icon: "fa-bottle-water" },
            { id: 9, name: "Dimsum Mentai (4pcs)", price: 20000, category: "Cemilan", icon: "fa-dumpling" },
            { id: 10, name: "Burger Sapi Keju", price: 30000, category: "Makanan", icon: "fa-burger" }
        ];

        let cart = [];
        let activeCategory = 'Semua';
        let paymentMethod = 'cash';

        // Filter Kategori
        function filterCategory(category) {
            activeCategory = category;
            document.querySelectorAll('.cat-btn').forEach(btn => {
                btn.classList.remove('bg-indigo-600', 'text-white', 'shadow-sm');
                btn.classList.add('bg-slate-800', 'text-slate-300');
            });
            const activeBtn = document.getElementById(`btn-${category}`);
            activeBtn.classList.remove('bg-slate-800', 'text-slate-300');
            activeBtn.classList.add('bg-indigo-600', 'text-white', 'shadow-sm');
            renderProducts();
        }

        // Filter Pencarian
        function filterProducts() {
            renderProducts();
        }

        // Render Katalog Produk
        function renderProducts() {
            const grid = document.getElementById('productGrid');
            const keyword = document.getElementById('searchInput').value.toLowerCase();
            
            let filtered = products.filter(p => {
                const matchCategory = activeCategory === 'Semua' || p.category === activeCategory;
                const matchKeyword = p.name.toLowerCase().includes(keyword);
                return matchCategory && matchKeyword;
            });
            
            if (filtered.length === 0) {
                grid.innerHTML = `<div class="col-span-full text-center py-10 text-slate-500 text-xs">Menu tidak ditemukan</div>`;
                return;
            }

            grid.innerHTML = filtered.map(p => `
                <div onclick="addToCart(${p.id})" class="bg-slate-900/40 border border-slate-800 rounded-xl p-3 flex flex-col justify-between cursor-pointer hover:border-indigo-500 hover:bg-slate-900 transition group">
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

        // Pengaturan Metode Pembayaran
        function setPaymentMethod(method) {
            paymentMethod = method;
            const btnCash = document.getElementById('methodCash');
            const btnQris = document.getElementById('methodQris');
            const cashSec = document.getElementById('cashSection');
            const qrisSec = document.getElementById('qrisSection');

            if (method === 'cash') {
                btnCash.className = "py-1.5 text-xs font-bold rounded-lg bg-indigo-600 text-white transition";
                btnQris.className = "py-1.5 text-xs font-bold rounded-lg text-slate-400 hover:text-white transition";
                cashSec.classList.remove('hidden');
                qrisSec.classList.add('hidden');
            } else {
                btnQris.className = "py-1.5 text-xs font-bold rounded-lg bg-indigo-600 text-white transition";
                btnCash.className = "py-1.5 text-xs font-bold rounded-lg text-slate-400 hover:text-white transition";
                qrisSec.classList.remove('hidden');
                cashSec.classList.add('hidden');
            }
        }

        // Tambah Item ke Keranjang
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
            document.getElementById('discountInput').value = 0;
            document.getElementById('cashInput').value = '';
            renderCart();
        }

        function addCash(amount) {
            const cashInput = document.getElementById('cashInput');
            const current = parseFloat(cashInput.value) || 0;
            cashInput.value = current + amount;
            hitungKembalian();
        }

        // Render Keranjang
        function renderCart() {
            const cartList = document.getElementById('cartList');
            if (cart.length === 0) {
                cartList.innerHTML = `
                    <div class="h-full flex flex-col items-center justify-center text-slate-500 space-y-2">
                        <i class="fa-solid fa-basket-shopping text-4xl text-slate-700"></i>
                        <p class="text-xs">Keranjang masih kosong</p>
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
                            <button onclick="changeQty(${item.id}, -1)" class="w-5 h-5 bg-slate-800 hover:bg-slate-700 text-slate-300 rounded flex items-center justify-center text-[10px] transition"><i class="fa-solid fa-minus"></i></button>
                            <span class="text-xs font-bold w-5 text-center text-white">${item.qty}</span>
                            <button onclick="changeQty(${item.id}, 1)" class="w-5 h-5 bg-slate-800 hover:bg-slate-700 text-slate-300 rounded flex items-center justify-center text-[10px] transition"><i class="fa-solid fa-plus"></i></button>
                        </div>
                    </div>
                `).join('');
            }
            hitungTotal();
        }

        // Perhitungan Total & Diskon
        function hitungTotal() {
            const subtotal = cart.reduce((sum, item) => sum + (item.price * item.qty), 0);
            const discount = parseFloat(document.getElementById('discountInput').value) || 0;
            let total = subtotal - discount;
            if (total < 0) total = 0;

            document.getElementById('subtotalText').innerText = `Rp ${subtotal.toLocaleString('id-ID')}`;
            document.getElementById('totalText').innerText = `Rp ${total.toLocaleString('id-ID')}`;
            hitungKembalian();
        }

        function hitungKembalian() {
            const subtotal = cart.reduce((sum, item) => sum + (item.price * item.qty), 0);
            const discount = parseFloat(document.getElementById('discountInput').value) || 0;
            let total = subtotal - discount;
            if (total < 0) total = 0;

            const cash = parseFloat(document.getElementById('cashInput').value) || 0;
            const change = cash - total;

            const changeText = document.getElementById('changeText');
            if (change >= 0) {
                changeText.innerText = `Rp ${change.toLocaleString('id-ID')}`;
                changeText.className = "font-bold text-indigo-400 text-xs";
            } else {
                changeText.innerText = `Uang Kurang`;
                changeText.className = "font-bold text-rose-400 text-xs";
            }
        }

        // Proses Selesai Pembayaran
        function prosesBayar() {
            const subtotal = cart.reduce((sum, item) => sum + (item.price * item.qty), 0);
            const discount = parseFloat(document.getElementById('discountInput').value) || 0;
            let total = subtotal - discount;
            if (total < 0) total = 0;

            const cash = parseFloat(document.getElementById('cashInput').value) || 0;

            if (cart.length === 0) {
                alert("Keranjang masih kosong!");
                return;
            }

            if (paymentMethod === 'cash' && cash < total) {
                alert("Nominal uang tunai kurang dari total tagihan!");
                return;
            }

            // Tulis Struk Modal
            document.getElementById('receiptDate').innerText = new Date().toLocaleString('id-ID');
            document.getElementById('rSub').innerText = `Rp ${subtotal.toLocaleString('id-ID')}`;
            document.getElementById('rDisc').innerText = `Rp ${discount.toLocaleString('id-ID')}`;
            document.getElementById('rTotal').innerText = `Rp ${total.toLocaleString('id-ID')}`;
            document.getElementById('rMethod').innerText = paymentMethod.toUpperCase();

            const cashRow = document.getElementById('rCashRow');
            const changeRow = document.getElementById('rChangeRow');

            if (paymentMethod === 'cash') {
                cashRow.style.display = 'flex';
                changeRow.style.display = 'flex';
                document.getElementById('rCash').innerText = `Rp ${cash.toLocaleString('id-ID')}`;
                document.getElementById('rChange').innerText = `Rp ${(cash - total).toLocaleString('id-ID')}`;
            } else {
                cashRow.style.display = 'none';
                changeRow.style.display = 'none';
            }

            document.getElementById('receiptItems').innerHTML = cart.map(i => `
                <div class="flex justify-between">
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
            
            // Reset keranjang
            clearCart();
        }

        // Inisialisasi awal
        renderProducts();
    </script>
</body>
</html>
