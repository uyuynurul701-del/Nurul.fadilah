<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Aplikasi Kasir - Nurul Fadilah</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
    <style>
        @media print {
            body * {
                visibility: hidden;
            }
            #printArea, #printArea * {
                visibility: visible;
            }
            #printArea {
                position: absolute;
                left: 0;
                top: 0;
                width: 100%;
            }
        }
    </style>
</head>
<body class="bg-light">

    <nav class="navbar navbar-expand-lg navbar-dark bg-dark">
        <div class="container">
            <a class="navbar-brand" href="#">Kasir App</a>
            <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNav">
                <span class="navbar-toggler-icon"></span>
            </button>
            <div class="collapse navbar-collapse" id="navbarNav">
                <ul class="navbar-nav ms-auto">
                    <li class="nav-item"><a class="nav-link active" href="#" onclick="switchPage('produk')">Produk</a></li>
                    <li class="nav-item"><a class="nav-link" href="#" onclick="switchPage('transaksi')">Transaksi</a></li>
                    <li class="nav-item"><a class="nav-link" href="#" onclick="switchPage('riwayat')">Riwayat & Cetak</a></li>
                </ul>
            </div>
        </div>
    </nav>

    <div class="container my-4">

        <div id="page-produk" class="page-content">
            <h2 class="mb-4">Manajemen Produk</h2>
            <div class="row">
                <div class="col-md-4">
                    <div class="card p-3 shadow-sm mb-3">
                        <h4>Tambah Produk</h4>
                        <form id="formProduk">
                            <div class="mb-3">
                                <label class="form-label">Nama Produk</label>
                                <input type="text" id="namaProduk" class="form-control" required>
                            </div>
                            <div class="mb-3">
                                <label class="form-label">Harga (Rp)</label>
                                <input type="number" id="hargaProduk" class="form-control" required>
                            </div>
                            <div class="mb-3">
                                <label class="form-label">Stok</label>
                                <input type="number" id="stokProduk" class="form-control" required>
                            </div>
                            <button type="submit" class="btn btn-primary w-100">Simpan Produk</button>
                        </form>
                    </div>
                </div>
                <div class="col-md-8">
                    <div class="card p-3 shadow-sm">
                        <h4>Daftar Produk</h4>
                        <table class="table table-striped mt-2">
                            <thead>
                                <tr>
                                    <th>No</th>
                                    <th>Nama Produk</th>
                                    <th>Harga</th>
                                    <th>Stok</th>
                                    <th>Aksi</th>
                                </tr>
                            </thead>
                            <tbody id="tabelProduk">
                                </tbody>
                        </table>
                    </div>
                </div>
            </div>
        </div>

        <div id="page-transaksi" class="page-content d-none">
            <h2 class="mb-4">Halaman Transaksi (Kasir)</h2>
            <div class="row">
                <div class="col-md-7">
                    <div class="card p-3 shadow-sm">
                        <h4>Pilih Produk</h4>
                        <div class="row" id="listProdukTransaksi">
                            </div>
                    </div>
                </div>
                <div class="col-md-5">
                    <div class="card p-3 shadow-sm">
                        <h4>Keranjang Belanja</h4>
                        <ul class="list-group my-3" id="cartList">
                            <li class="list-group-item text-muted text-center">Keranjang kosong</li>
                        </ul>
                        <h5>Total: <span id="cartTotal" class="text-success">Rp 0</span></h5>
                        <div class="mb-3 mt-2">
                            <label class="form-label">Bayar (Rp)</label>
                            <input type="number" id="bayarUang" class="form-control" oninput="hitungKembalian()">
                        </div>
                        <div class="mb-3">
                            <label class="form-label">Kembalian:</label>
                            <h5 id="kembalianUang" class="text-primary">Rp 0</h5>
                        </div>
                        <button class="btn btn-success w-100" onclick="prosesTransaksi()">Selesaikan Transaksi</button>
                    </div>
                </div>
            </div>
        </div>

        <div id="page-riwayat" class="page-content d-none">
            <h2 class="mb-4">Riwayat & Cetak Transaksi</h2>
            <div class="card p-3 shadow-sm">
                <table class="table table-bordered">
                    <thead>
                        <tr>
                            <th>ID Transaksi</th>
                            <th>Waktu</th>
                            <th>Total Belanja</th>
                            <th>Aksi</th>
                        </tr>
                    </thead>
                    <tbody id="tabelRiwayat">
                        </tbody>
                </table>
            </div>
        </div>

    </div>

    <div class="modal fade" id="modalStruk" tabindex="-1">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <h5 class="modal-title">Struk Pembayaran</h5>
                    <button type="button" class="btn-close" data-bs-dismiss="modal"></button>
                </div>
                <div class="modal-body" id="printArea">
                    <div class="text-center">
                        <h4>TOKO KASIR BERKAH</h4>
                        <p class="mb-1">Jl. Contoh No. 123, Indonesia</p>
                        <hr>
                    </div>
                    <p id="strukInfo" class="mb-1"></p>
                    <table class="table table-sm mt-2">
                        <thead>
                            <tr>
                                <th>Item</th>
                                <th>Qty</th>
                                <th>Subtotal</th>
                            </tr>
                        </thead>
                        <tbody id="strukItems"></tbody>
                    </table>
                    <hr>
                    <div class="d-flex justify-content-between">
                        <strong>Total:</strong>
                        <span id="strukTotal"></span>
                    </div>
                    <div class="d-flex justify-content-between">
                        <strong>Bayar:</strong>
                        <span id="strukBayar"></span>
                    </div>
                    <div class="d-flex justify-content-between">
                        <strong>Kembalian:</strong>
                        <span id="strukKembalian"></span>
                    </div>
                    <div class="text-center mt-4">
                        <p class="mb-0">Terima Kasih Telah Berbelanja!</p>
                        <small class="text-muted">Kasir App by Nurul Fadilah</small>
                    </div>
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Tutup</button>
                    <button type="button" class="btn btn-primary" onclick="window.print()">Cetak Struk</button>
                </div>
            </div>
        </div>
    </div>

    <footer class="text-center py-4 mt-5 bg-white border-top">
        <p class="mb-0 text-muted">Aplikasi Kasir Web &copy; 2026 | Dibuat oleh <strong>Nurul Fadilah</strong></p>
    </footer>

    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>

    <script>
        let produkList = [
            { id: 1, nama: "Kopi Hitam", harga: 5000, stok: 20 },
            { id: 2, nama: "Roti Bakar", harga: 10000, stok: 15 }
        ];
        let keranjang = [];
        let riwayatTransaksi = [];

        function switchPage(pageId) {
            document.querySelectorAll('.page-content').forEach(el => el.classList.add('d-none'));
            document.getElementById('page-' + pageId).classList.remove('d-none');
            
            document.querySelectorAll('.nav-link').forEach(el => el.classList.remove('active'));
            event.target.classList.add('active');

            if (pageId === 'produk') renderProduk();
            if (pageId === 'transaksi') renderProdukTransaksi();
            if (pageId === 'riwayat') renderRiwayat();
        }

        // --- MANAJEMEN PRODUK ---
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
                    <td>${p.nama}</td>
                    <td>Rp ${p.harga.toLocaleString()}</td>
                    <td>${p.stok}</td>
                    <td><button class="btn btn-danger btn-sm" onclick="hapusProduk(${p.id})">Hapus</button></td>
                </tr>`;
            });
            document.getElementById('tabelProduk').innerHTML = html;
        }

        function hapusProduk(id) {
            produkList = produkList.filter(p => p.id !== id);
            renderProduk();
        }

        // --- TRANSAKSI / POS ---
        function renderProdukTransaksi() {
            let html = '';
            produkList.forEach(p => {
                html += `<div class="col-md-6 mb-3">
                    <div class="card p-2 border shadow-sm">
                        <h5>${p.nama}</h5>
                        <p class="mb-1 text-muted">Harga: Rp ${p.harga.toLocaleString()}</p>
                        <p class="mb-2 text-muted">Stok: ${p.stok}</p>
                        <button class="btn btn-outline-primary btn-sm" onclick="tambahKeKeranjang(${p.id})">Tambah</button>
                    </div>
                </div>`;
            });
            document.getElementById('listProdukTransaksi').innerHTML = html;
        }

        function tambahKeKeranjang(id) {
            let produk = produkList.find(p => p.id === id);
            if (produk.stok <= 0) {
                alert("Stok habis!");
                return;
            }
            let item = keranjang.find(i => i.id === id);
            if (item) {
                if(item.qty < produk.stok) {
                    item.qty++;
                } else {
                    alert("Stok tidak mencukupi!");
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
                html = `<li class="list-group-item text-muted text-center">Keranjang kosong</li>`;
            } else {
                keranjang.forEach((item, index) => {
                    let subtotal = item.harga * item.qty;
                    total += subtotal;
                    html += `<li class="list-group-item d-flex justify-content-between align-items-center">
                        <div>
                            <h6 class="mb-0">${item.nama}</h6>
                            <small class="text-muted">Rp ${item.harga.toLocaleString()} x ${item.qty}</small>
                        </div>
                        <span>Rp ${subtotal.toLocaleString()}</span>
                        <button class="btn btn-sm btn-danger ms-2" onclick="hapusItemKeranjang(${index})">&times;</button>
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
                alert("Keranjang masih kosong!");
                return;
            }
            if (bayar < total) {
                alert("Jumlah uang pembayaran kurang!");
                return;
            }

            // Kurangi stok produk asli
            keranjang.forEach(item => {
                let prod = produkList.find(p => p.id === item.id);
                if(prod) prod.stok -= item.qty;
            });

            let transaksiBaru = {
                id: 'TRX-' + Date.now(),
                waktu: new Date().toLocaleString(),
                items: [...keranjang],
                total: total,
                bayar: bayar,
                kembalian: kembalian
            };

            riwayatTransaksi.push(transaksiBaru);

            // Reset keranjang
            keranjang = [];
            document.getElementById('bayarUang').value = '';
            renderKeranjang();
            alert("Transaksi Berhasil Disimpan!");
            switchPage('riwayat');
        }

        // --- RIWAYAT & CETAK ---
        function renderRiwayat() {
            let html = '';
            if (riwayatTransaksi.length === 0) {
                html = `<tr><td colspan="4" class="text-center text-muted">Belum ada riwayat transaksi.</td></tr>`;
            } else {
                riwayatTransaksi.forEach(trx => {
                    html += `<tr>
                        <td>${trx.id}</td>
                        <td>${trx.waktu}</td>
                        <td>Rp ${trx.total.toLocaleString()}</td>
                        <td><button class="btn btn-info btn-sm text-white" onclick="bukaStruk('${trx.id}')">Cetak Struk</button></td>
                    </tr>`;
                });
            }
            document.getElementById('tabelRiwayat').innerHTML = html;
        }

    function bukaStruk(id) {
            let trx = riwayatTransaksi.find(t => t.id === id);
            if (!trx) return;

            document.getElementById('strukInfo').innerHTML = `ID: ${trx.id} <br> Waktu: ${trx.waktu}`;
            let htmlItems = '';
            trx.items.forEach(item => {
                htmlItems += `<tr>
                    <td>${item.nama}</td>
                    <td>${item.qty}</td>
                    <td>Rp ${(item.harga * item.qty).toLocaleString()}</td>
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
