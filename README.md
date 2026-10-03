# 🕌 Sistem Kas Mushola Pasar - Real-time & Transparan

Aplikasi web Single Page Application (SPA) modern, ringan, dan **responsif penuh (Mobile, Tablet, dan Desktop)** untuk pencatatan dan transparansi keuangan kas **Mushola Pasar**. Didesain khusus untuk kebutuhan pedagang, pembeli, dan pengurus DKM mushola pasar.

Dibuat dalam **satu file tunggal (`index.html`)** tanpa proses *build/compile*, siap di-deploy langsung ke **Cloudflare Pages** atau **GitHub Pages**.

🔗 **Repository GitHub:** [https://github.com/angga2103/Kas-mushola.git](https://github.com/angga2103/Kas-mushola.git)

---

## 🌟 Fitur Utama & Keunggulan

### 1. 🖥️ Desain Responsif Multi-Perangkat
- **Tampilan Smartphone (Mobile):** Dilengkapi *Bottom Navigation Bar* ergonomis yang nyaman dioperasikan dengan satu jempol.
- **Tampilan Tablet & Desktop:** Layout melebar otomatis dengan *Top Navigation Bar* di header dan dashboard multi-kolom (sisi kiri ringkasan kas & riwayat transaksi, sisi kanan grafik donat & widget QRIS infaq cepat).

### 2. 🏪 Kategori Khusus Mushola Pasar & Istilah Bisaroh
- Disesuaikan khusus untuk aktivitas mushola di pasar (tanpa shalat Jumat & tanpa shalat Tarawih).
- **Pemasukan:** Kotak Amal Harian Mushola, Infaq Pedagang & Kios Pasar, Infaq Shalat Berjamaah, Donatur & Pengunjung Pasar, Kotak Wudhu & Sarana, dll.
- **Pengeluaran:** Menggunakan istilah **Bisaroh** (Imam & Petugas Mushola), Listrik & Token Air Pasar, Kebersihan & Sanitasi Mushola, Operasional, dsb.

### 3. 🏷️ Kategori Dinamis & Kustom (Bisa Ditambah/Dihapus)
- Pengurus DKM dapat menambah atau menghapus kategori pengeluaran dan pemasukan secara fleksibel melalui tab **Pengaturan** > **Kategori Transaksi Dinamis**.
- Kategori baru yang ditambahkan otomatis muncul pada formulir pencatatan kas baru dan pilihan cepat (quick chips).

### 4. 👥 Mode Publik (Akses Jamaah & Pedagang Pasar)
- **Dashboard Real-time:** Menampilkan saldo kas terkini, total pemasukan bulan ini, dan total pengeluaran bulan ini.
- **Grafik Interaktif (Chart.js):** Visualisasi diagram *doughnut* perbandingan pemasukan vs pengeluaran bulan berjalan.
- **Infaq Digital (QRIS):** Pop-up gambar QRIS resmi mushola pasar dan tombol cepat salin rekening bank.
- **Riwayat Lengkap & Filter:** Filter jenis (Masuk/Keluar), pencarian keterangan/kategori, dan filter per bulan.
- **Cetak Laporan / PDF:** Dilengkapi format ramah cetak (`@media print`) untuk mencetak fisik atau simpan PDF.
- **Profil & Struktur Pengurus:** Menampilkan identitas mushola pasar, rekening, dan susunan kepengurusan.

### 5. 🔐 Mode Pengurus / Admin (PIN Protected: `123456`)
- **Autentikasi PIN:** Klik tombol gembok di header dan masukkan PIN (Default awal: `123456`).
- **Catat Transaksi Cepat:** Tombol melayang (+) atau tombol atas untuk mencatat kas masuk/keluar dengan auto-format rupiah.
- **Broadcast WhatsApp Otomatis:** Setelah menyimpan transaksi baru, otomatis muncul pop-up konfirmasi untuk membagikan laporan rapi ke grup WhatsApp jamaah & pedagang pasar.
- **Hapus Transaksi:** Tombol hapus muncul di setiap baris transaksi saat mode pengurus aktif.
- **Pengaturan Lengkap:** Kelola identitas mushola, kelola susunan pengurus, kelola kategori kustom, ganti PIN, dan hubungkan Firebase.

---

## 🚀 Panduan Setup Firebase Firestore

Aplikasi ini menggunakan **Google Firebase Firestore** untuk sinkronisasi data secara *real-time* dan **Firebase Anonymous Authentication** untuk akses publik yang aman.

### Langkah 1: Buat Project Firebase
1. Buka [Firebase Console](https://console.firebase.google.com/).
2. Buat project baru (misal: `kas-mushola-pasar`).

### Langkah 2: Aktifkan Anonymous Authentication
1. Pilih menu **Build** > **Authentication** > tab **Sign-in method**.
2. Aktifkan **Anonymous**, lalu klik **Save**.

### Langkah 3: Aktifkan Cloud Firestore Database
1. Pilih menu **Build** > **Firestore Database** > **Create database**.
2. Pilih lokasi server (misal: `asia-southeast2` untuk Jakarta).

### Langkah 4: Atur Security Rules Firestore
Masuk ke tab **Rules** pada Firestore Database, ganti dengan aturan berikut:

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /transactions/{transactionId} {
      allow read, write: if request.auth != null;
    }
    match /profile/{docId} {
      allow read, write: if request.auth != null;
    }
  }
}
```
Klik **Publish**.

### Langkah 5: Hubungkan ke Aplikasi
1. Buka **Project settings** (ikon gerigi) > bagian *Your apps* > klik Web `</>`.
2. Salin data `firebaseConfig`.
3. Buka aplikasi `index.html` di browser:
   - Masuk ke **Mode Pengurus** (PIN: `123456`).
   - Buka tab **Pengaturan** > **Koneksi Firebase Cloud**.
   - Masukkan API Key, Project ID, dll., lalu klik **Simpan & Hubungkan**.

> **Catatan:** Jika belum menghubungkan Firebase, aplikasi akan otomatis berjalan dalam **Mode Demo Lokal** menggunakan `localStorage`, sehingga dapat langsung diuji coba seketika tanpa setup awal.

---

## 🌐 Cara Deploy ke Cloudflare Pages

1. Masuk ke [Cloudflare Dashboard](https://dash.cloudflare.com/) > **Workers & Pages**.
2. Klik **Create Application** > tab **Pages** > **Connect to Git**.
3. Pilih repository `https://github.com/angga2103/Kas-mushola.git`.
4. Pengaturan build:
   - **Framework preset:** `None`
   - **Build command:** *(Kosongkan)*
   - **Build output directory:** *(Kosongkan)*
5. Klik **Save and Deploy**. Website kas mushola pasar Anda langsung aktif secara global dengan domain gratis dan HTTPS.
