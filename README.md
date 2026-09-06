<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Aplikasi Kasir Modern - Nurul Fadilah</title>
    <!-- Bootstrap 5 CSS -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
    <!-- FontAwesome Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        :root {
            --primary-color: #4f46e5;
            --primary-hover: #4338ca;
            --bg-color: #f8fafc;
        }
        body {
            background-color: var(--bg-color);
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }
        .navbar {
            background: linear-gradient(135deg, var(--primary-color), var(--primary-hover)) !important;
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
        }
        .card {
            border: none;
            border-radius: 12px;
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.05), 0 2px 4px -1px rgba(0, 0, 0, 0.03);
            transition: transform 0.2s;
        }
        .card:hover {
            transform: translateY(-2px);
        }
        .btn-primary {
            background-color: var(--primary-color);
            border-color: var(--primary-color);
        }
        .btn-primary:hover {
            background-color: var(--primary-hover);
            border-color: var(--primary-hover);
        }
        .product-card {
            cursor: pointer;
            border-left: 4px solid var(--primary-color);
        }
        @media print {
            body * { visibility: hidden; }
            #printArea, #printArea * { visibility: visible; }
            #printArea { position: absolute; left: 0; top: 0; width: 100%; }
        }
    </style>
</head>
<body>

    <!-- Navbar -->
    <nav class="navbar navbar-expand-lg navbar-dark py-3">
        <div class="container">
            <a class="navbar-brand fw-bold" href="#"><i class="fa-solid fa-cash-register me-2"></i> Kasir Pro</a>
            <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNav">
                <span class="navbar-toggler-icon"></span>
            </button>
            <div class="collapse navbar-collapse" id="navbarNav">
                <ul class="navbar-nav ms-auto gap-2">
                    <li class="nav-item"><a class="nav-link active px-3 rounded" href="#" onclick="switchPage('produk')"><i class="fa-solid fa-box-open me-1"></i> Produk</a></li>
                    <li class="nav-item"><a class="nav-link px-3 rounded" href="#" onclick="switchPage('transaksi')"><i class="fa-solid fa-cart-shopping me-1"></i> Transaksi POS</a></li>
                    <li class="nav-item"><a class="nav-link px-3 rounded" href="#" onclick="switchPage('riwayat')"><i class="fa-solid fa-clock-rotate-left me-1"></i> Riwayat & Cetak</a></li>
                </ul>
            </div>
        </div>
    </nav>

    <!-- Konten Utama -->
    <div class="container my-5">

        <!-- HALAMAN PRODUK -->
        <div id="page-produk" class="page-content">
            <div class="d-flex align-items-center mb-4">
                <div class="bg-indigo text-primary p-3 rounded-circle me-3 bg-white shadow-sm">
                    <i class="fa-solid fa-boxes-stacked fa-2x"></i>
                </div>
                <div>
                    <h2 class="fw-bold mb-0">Manajemen Produk</h2>
                    <p class="text-muted mb-0">Tambah, ubah, dan pantau ketersediaan stok barang.</p>
                </div>
            </div>
            
            <div class="row g-4">
                <div class="col-md-4">
                    <div class="card p-4">
                        <h5 class="fw-bold mb-3"><i class="fa-solid fa-circle-plus text-primary me-2"></i> Tambah Produk</h5>
                        <form id="formProduk">
                            <div class="mb-3">
                                <label class="form-label text-muted small fw-bold">Nama Produk</label>
                                <div class="input-group">
                                    <span class="input-group-text bg-light"><i class="fa-solid fa-tag"></i></span>
                                    <input type="text" id="namaProduk" class="form-control" placeholder="Cth: Kopi Susu" required>
                                </div>
                            </div>
                            <div class="mb-3">
                                <label class="form-label text-muted small fw-bold">Harga (Rp)</label>
                                <div class="input-group">
                                    <span class="input-group-text bg-light"><i class="fa-solid fa-rupiah-sign"></i></span>
                                    <input type="number" id="hargaProduk" class="form-control" placeholder="Cth: 15000" required>
                                </div>
                            </div>
                            <div class="mb-3">
                                <label class="form-label text-muted small fw-bold">Stok Awal</label>
                                <div class="input-group">
                                    <span class="input-group-text bg-light"><i class="fa-solid fa-cubes"></i></span>
                                    <input type="number" id="stokProduk" class="form-control" placeholder="Cth: 20" required>
                                </div>
                            </div>
                            <button type="submit" class="btn btn-primary w-100 py-2 fw-bold"><i class="fa-solid fa-save me-2"></i> Simpan Produk</button>
                        </form>
                    </div>
                </div>
                <div class="col-md-8">
                    <div class="card p-4">
                        <h5 class="fw-bold mb-3"><i class="fa-solid fa-list text-primary me-2"></i> Daftar Katalog Produk</h5>
                        <div class="table-responsive">
                            <table class="table table-hover align-middle mt-2">
                                <thead class="table-light">
                                    <tr>
                                        <th>#</th>
                                        <th>Nama Produk</th>
                                        <th>Harga</th>
                                        <th>Stok</th>
                                        <th class="text-center">Aksi</th>
                                    </tr>
                                </thead>
                                <tbody id="tabelProduk"></tbody>
                            </table>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <!-- HALAMAN TRANSAKSI (POS) -->
        <div id="page-transaksi" class="page-content d-none">
            <div class="d-flex align-items-center mb-4">
                <div class="bg-white text-success p-3 rounded-circle me-3 shadow-sm">
                    <i class="fa-solid fa-cash-register fa-2x"></i>
                </div>
                <div>
                    <h2 class="fw-bold mb-0">Kasir / Point of Sales</h2>
                    <p class="text-muted mb-0">Pilih produk untuk dimasukkan ke keranjang belanja.</p>
                </div>
            </div>

            <div class="row g-4">
                <div class="col-md-7">
                    <div class="card p-4">
                        <h5 class="fw-bold mb-3"><i class="fa-solid fa-store text-success me-2"></i> Katalog Tersedia</h5>
                        <div class="row g-3" id="listProdukTransaksi"></div>
                    </div>
                </div>
                <div class="col-md-5">
                    <div class="card p-4 bg-white border">
                        <h5 class="fw-bold mb-3"><i class="fa-solid fa-shopping-cart text-primary me-2"></i> Keranjang Belanja</h5>
                        <ul class="list-group my-3 overflow-auto" id="cartList" style="max-height: 220px;">
                            <li class="list-group-item text-muted text-center py-4 border-0 bg-light rounded">
                                <i class="fa-solid fa-basket-shopping fa-2x mb-2 d-none d-block"></i>
                                Keranjang masih kosong
                            </li>
                        </ul>
                        <hr>
                        <div class="d-flex justify-content-between mb-3">
                            <span class="fw-bold fs-5">Total Tagihan:</span>
                            <span id="cartTotal" class="fw-bold fs-4 text-success">Rp 0</span>
                        </div>
                        <div class="mb-3">
                            <label class="form-label text-muted small fw-bold">Nominal Pembayaran (Rp)</label>
                            <div class="input-group">
                                <span class="input-group-text bg-light"><i class="fa-solid fa-wallet"></i></span>
                                <input type="number" id="bayarUang" class="form-control" placeholder="Masukkan uang..." oninput="hitungKembalian()">
                            </div>
                        </div>
                        <div class="mb-4 bg-light p-3 rounded">
                            <div class="d-flex justify-content-between">
                                <span class="fw-bold text-muted">Kembalian:</span>
                                <span id="kembalianUang" class="fw-bold text-primary fs-5">Rp 0</span>
                            </div>
                        </div>
                        <button class="btn btn-success w-100 py-2 fw-bold text-uppercase" onclick="prosesTransaksi()"><i class="fa-solid fa-circle-check me-2"></i> Selesaikan Transaksi</button>
                    </div>
                </div>
            </div>
        </div>

        <!-- HALAMAN RIWAYAT & CETAK -->
        <div id="page-riwayat" class="page-content d-none">
            <div class="d-flex align-items-center mb-4">
                <div class="bg-white text-info p-3 rounded-circle me-3 shadow-sm">
                    <i class="fa-solid fa-receipt fa-2x"></i>
                </div>
                <div>
                    <h2 class="fw-bold mb-0">Riwayat & Cetak Struk</h2>
                    <p class="text-muted mb-0">Daftar seluruh transaksi yang berhasil diproses.</p>
                </div>
            </div>

            <div class="card p-4">
                <div class="table-responsive">
                    <table class="table table-hover align-middle">
                        <thead class="table-light">
                            <tr>
                                <th>ID Transaksi</th>
                                <th>Waktu</th>
                                <th>Total Belanja</th>
                                <th class="text-center">Aksi Struk</th>
                            </tr>
                        </thead>
                        <tbody id="tabelRiwayat"></tbody>
                    </table>
                </div>
            </div>
        </div>

    </div>

    <!-- Modal Cetak Struk -->
    <div class="modal fade" id="modalStruk" tabindex="-1">
        <div class="modal-dialog modal-dialog-centered">
            <div class="modal-content border-0 shadow-lg">
                <div class="modal-header bg-dark text-white">
                    <h5 class="modal-title"><i class="fa-solid fa-print me-2"></i> Struk Pembayaran</h5>
                    <button type="button" class="btn-close btn-close-white" data-bs-dismiss="modal"></button>
                </div>
                <div class="modal-body p-4" id="printArea">
                    <div class="text-center mb-3">
                        <h4 class="fw-bold mb-0"><i class="fa-solid fa-shop text-primary"></i> TOKO NURUL</h4>
                        <p class="text-muted small mb-1">Jl. Inovasi Teknologi No. 45, Indonesia</p>
                        <hr class="border-dashed">
                    </div>
                    <div class="small mb-3" id="strukInfo"></div>
                    <table class="table table-sm table-borderless small">
                        <thead>
                            <tr class="border-bottom">
                                <th>Item</th>
                                <th class="text-center">Qty</th>
                                <th class="text-end">Subtotal</th>
                            </tr>
                        </thead>
                        <tbody id="strukItems"></tbody>
                    </table>
                    <hr class="border-dashed">
                    <div class="d-flex justify-content-between small">
                        <span>Total Belanja:</span>
                        <strong id="strukTotal"></strong>
                    </div>
                    <div class="d-flex justify-content-between small">
                        <span>Tunai Dibayar:</span>
                        <span id="strukBayar"></span>
                    </div>
                    <div class="d-flex justify-content-between small mb-3">
                        <span>Kembalian:</span>
                        <span id="strukKembalian"></span>
                    </div>
                    <hr class="border-dashed">
                    <div class="text-center mt-3">
                        <p class="mb-1 fw-bold text-success small">Terima Kasih Atas Kunjungan Anda!</p>
                        <small class="text-muted" style="font-size: 11px;">Aplikasi Kasir by <strong>Nurul Fadilah</strong></small>
                    </div>
                </div>
                <div class="modal-footer bg-light">
                    <button type="button" class="btn btn-secondary btn-sm" data-bs-dismiss="modal"><i class="fa-solid fa-xmark me-1"></i> Tutup</button>
                    <button type="button" class="btn btn-primary btn-sm px-4" onclick="window.print()"><i class="fa-solid fa-print me-1"></i> Cetak Sekarang</button>
                </div>
            </div>
        </div>
    </div>

    <!-- Footer -->
    <footer class="text-center py-4 bg-white border-top mt-5">
        <p class="text-muted small mb-0"><i class="fa-solid fa-code text-primary me-1"></i> Aplikasi Kasir Web Modern &copy; 2026 | Dibuat dengan penuh dedikasi oleh <strong class="text-dark">Nurul Fadilah</strong></p>
    </footer>

    <!-- Bootstrap JS Bundle -->
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>

    <!-- JavaScript Aplikasi -->
    <script>
        let produkList = [
            { id: 1, nama: "Kopi Hitam Arabica", harga: 12000, stok: 20 },
            { id: 2, nama: "Roti Bakar Keju", harga: 15000, stok: 15 },
            { id: 3, nama: "Es Teh Manis", harga: 5000, stok: 30 }
        ];
        let keranjang = [];
        let riwayatTransaksi = [];

        function switchPage(pageId) {
            document.querySelectorAll('.page-content').forEach(el => el.classList.add('d-none'));
            document.getElementById('page-' + pageId).classList.remove('d-none');
            
            document.querySelectorAll('.nav-link').forEach(el => el.classList.remove('active', 'bg-white', 'text-dark'));
            event.currentTarget.classList.add('active');

            if (pageId === 'produk') renderProduk();
            if (pageId === 'transaksi') renderProdukTransaksi();
            if (pageId === 'riwayat') renderRiwayat();
        }

        document.getElementById('formProduk').addEventListener('submit', function(e) {
            e.preventDefault();
            const nama = document.getElementById('namaProduk').value;
            const harga = parseInt(document.getElementById('hargaProduk').value);
            const stok = parseInt(document.getElementById('stokProduk').value);

            produkList.push({ id: Date.now(), nama, harga, stok });
            this.reset();
            renderProduk();
        });

        function renderProduk() {
            let html = '';
            produkList.forEach((p, index) => {
                html += `<tr>
                    <td>${index + 1}</td>
                    <td class="fw-bold">${p.nama}</td>
                    <td class="text-success fw-semibold">Rp ${p.harga.toLocaleString()}</td>
                    <td><span class="badge bg-secondary">${p.stok} unit</span></td>
                    <td class="text-center">
                        <button class="btn btn-outline-danger btn-sm" onclick="hapusProduk(${p.id})"><i class="fa-solid fa-trash-can"></i> Hapus</button>
                    </td>
                </tr>`;
            });
            document.getElementById('tabelProduk').innerHTML = html;
        }

        function hapusProduk(id) {
            produkList = produkList.filter(p => p.id !== id);
            renderProduk();
        }

        function renderProdukTransaksi() {
            let html = '';
            produkList.forEach(p => {
                html += `<div class="col-md-6">
                    <div class="card p-3 product-card shadow-sm h-100" onclick="tambahKeKeranjang(${p.id})">
                        <div class="d-flex justify-content-between align-items-start">
                            <h6 class="fw-bold mb-1">${p.nama}</h6>
                            <span class="badge bg-light text-dark border"><i class="fa-solid fa-box text-muted me-1"></i> ${p.stok}</span>
                        </div>
                        <p class="text-success fw-bold mb-2">Rp ${p.harga.toLocaleString()}</p>
                        <button class="btn btn-sm btn-primary w-100 mt-auto"><i class="fa-solid fa-cart-plus me-1"></i> Tambah</button>
                    </div>
                </div>`;
            });
            document.getElementById('listProdukTransaksi').innerHTML = html;
        }

        function tambahKeKeranjang(id) {
            let produk = produkList.find(p => p.id === id);
            if (produk.stok <= 0) {
                alert("Stok produk ini sudah habis!");
                return;
            }
            let item = keranjang.find(i => i.id === id);
            if (item) {
                if(item.qty < produk.stok) {
                    item.qty++;
                } else {
                    alert("Stok maksimum tercapai!");
                }
            } else {
                keranjang.push({ id: produk.id, nama: produk.nama, harga: produk.harga, qty: 1 });
            }
            renderKeranjang();
        }

        function renderKeranjang() {
            let html = '';
            let total = 0;
            if (keranjang.length === 0) {
                html = `<li class="list-group-item text-muted text-center py-4 border-0 bg-light rounded"><i class="fa-solid fa-basket-shopping fa-2x mb-2 d-block"></i>Keranjang kosong</li>`;
            } else {
                keranjang.forEach((item, index) => {
                    let subtotal = item.harga * item.qty;
                    total += subtotal;
                    html += `<li class="list-group-item d-flex justify-content-between align-items-center border-0 bg-light mb-2 rounded">
                        <div>
                            <h6 class="mb-0 fw-bold">${item.nama}</h6>
                            <small class="text-muted">Rp ${item.harga.toLocaleString()} × ${item.qty}</small>
                        </div>
                        <div class="text-end">
                            <span class="fw-bold text-success d-block">Rp ${subtotal.toLocaleString()}</span>
                            <button class="btn btn-link text-danger btn-sm p-0 text-decoration-none" onclick="hapusItemKeranjang(${index})"><i class="fa-solid fa-trash"></i> Hapus</button>
                        </div>
                    </li>`;
                });
            }
            document.getElementById('cartList').innerHTML = html;
            document.getElementById('cartTotal').innerText = `Rp ${total.toLocaleString()}`;
            hitungKembalian();
        }

        function hapusItemKeranjang(index) {
            keranjang.splice(index, 1);
            renderKeranjang();
        }

        function hitungKembalian() {
            let total = keranjang.reduce((sum, item) => sum + (item.harga * item.qty), 0);
            let bayar = parseInt(document.getElementById('bayarUang').value) || 0;
            let kembalian = bayar - total;
            document.getElementById('kembalianUang').innerText = kembalian >= 0 ? `Rp ${kembalian.toLocaleString()}` : `Uang Kurang`;
        }

        function prosesTransaksi() {
            let total = keranjang.reduce((sum, item) => sum + (item.harga * item.qty), 0);
            let bayar = parseInt(document.getElementById('bayarUang').value) || 0;
            let kembalian = bayar - total;

            if (keranjang.length === 0) {
                alert("Keranjang belanja masih kosong!");
                return;
            }
            if (bayar < total) {
                alert("Nominal pembayaran uang kurang!");
                return;
            }

            keranjang.forEach(item => {
                let prod = produkList.find(p => p.id === item.id);
                if(prod) prod.stok -= item.qty;
            });

            let transaksiBaru = {
                id: 'TRX-' + Math.floor(100000 + Math.random() * 900000),
                waktu: new Date().toLocaleString(),
                items: [...keranjang],
                total: total,
                bayar: bayar,
                kembalian: kembalian
            };

            riwayatTransaksi.push(transaksiBaru);
            keranjang = [];
            document.getElementById('bayarUang').value = '';
            renderKeranjang();
            alert("Transaksi berhasil diproses!");
            switchPage('riwayat');
        }

        function renderRiwayat() {
            let html = '';
            if (riwayatTransaksi.length === 0) {
                html = `<tr><td colspan="4" class="text-center text-muted py-4">Belum ada riwayat transaksi tercatat.</td></tr>`;
            } else {
                riwayatTransaksi.forEach(trx => {
                    html += `<tr>
                        <td class="fw-bold text-primary">${trx.id}</td>
                        <td class="text-muted small"><i class="fa-regular fa-clock me-1"></i> ${trx.waktu}</td>
                        <td class="fw-bold text-success">Rp ${trx.total.toLocaleString()}</td>
                        <td class="text-center">
                            <button class="btn btn-info btn-sm text-white px-3" onclick="bukaStruk('${trx.id}')"><i class="fa-solid fa-receipt me-1"></i> Cetak Struk</button>
                        </td>
                    </tr>`;
                });
            }
            document.getElementById('tabelRiwayat').innerHTML = html;
        }

        function bukaStruk(id) {
            let trx = riwayatTransaksi.find(t => t.id === id);
            if (!trx) return;

            document.getElementById('strukInfo').innerHTML = `<strong>ID:</strong> ${trx.id} <br><strong>Waktu:</strong> ${trx.waktu}`;
            let htmlItems = '';
            trx.items.forEach(item => {
                htmlItems += `<tr>
                    <td>${item.nama}</td>
                    <td class="text-center">${item.qty}</td>
                    <td class="text-end">Rp ${(item.harga * item.qty).toLocaleString()}</td>
                </tr>`;
            });
            document.getElementById('strukItems').innerHTML = htmlItems;
            document.getElementById('strukTotal').innerText = `Rp ${trx.total.toLocaleString()}`;
            document.getElementById('strukBayar').innerText = `Rp ${trx.bayar.toLocaleString()}`;
            document.getElementById('strukKembalian').innerText = `Rp ${trx.kembalian.toLocaleString()}`;

            let modalStruk = new bootstrap.Modal(document.getElementById('modalStruk'));
            modalStruk.show();
        }
    </script>
</body>
</html>
