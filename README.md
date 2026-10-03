# 🕌 Sistem Kas Mushola Pasar - Real-time & Transparan (PWA)

Aplikasi web **Progressive Web App (PWA)** modern, ringan, dan **responsif penuh (Mobile, Tablet, dan Desktop)** untuk pencatatan dan transparansi keuangan kas **Mushola Pasar**. Didesain khusus untuk kebutuhan pedagang, pembeli, dan pengurus DKM mushola pasar.

Aplikasi ini dapat di-install langsung ke layar utama smartphone (Android/iOS) atau desktop seperti aplikasi native dengan icon kubah masjid bernuansa islami modern.

🔗 **Repository GitHub:** [https://github.com/angga2103/Kas-mushola.git](https://github.com/angga2103/Kas-mushola.git)

---

## 🌟 Fitur Unggulan Terbaru

### 1. 📱 Progressive Web App (PWA) & Offline Ready
- **Bisa Di-install (Add to Home Screen):** Mendukung instalasi langsung pada Android (Chrome) dan iOS (Safari) tanpa perlu download dari PlayStore.
- **Icon Kubah Masjid "Kas Mushola":** Ikon vektor resolusi tinggi (`icons/icon.svg`) dan PNG (`192x192`, `512x512`, `apple-touch-icon`) dengan gambar kubah masjid hijau emerald, aksen bulan sabit emas (*hilal*), dan emblem bertuliskan "KAS MUSHOLA".
- **Service Worker (`sw.js`):** Caching otomatis untuk app shell agar aplikasi tetap terbuka seketika bahkan saat koneksi pasar terganggu/offline.

### 2. 📸 Upload Foto Pengurus & Auto-Kompresi Canvas (~25 KB)
- **Kompresi Otomatis di Sisi Klien:** Foto asli dari kamera HP (biasanya 3-8 MB) langsung dikompres oleh browser menjadi gambar persegi berdimensi 320x320 px (~20-30 KB) sebelum disimpan.
- **Web Tetap Super Cepat:** Tidak membebani memori, hemat kuota internet, dan tidak memerlukan biaya penyimpanan cloud storage tambahan.
- **Tampilan Menarik di Tab Profil:** Foto pengurus ditampilkan dalam bentuk kartu avatar modern dengan bingkai cincin emerald, bayangan lembut, dan badge jabatan yang rapi.
- **Kelola Foto di Pengaturan:** Pengurus dapat menambah foto saat mendaftarkan pengurus baru, mengganti foto yang ada, atau menghapus foto kapan saja.

### 3. 🖥️ Desain Responsif Multi-Perangkat
- **Smartphone (Mobile):** Dilengkapi *Bottom Navigation Bar* ergonomis yang nyaman dioperasikan dengan satu jempol.
- **Tablet & Desktop:** Layout melebar otomatis (`max-w-6xl`) dengan *Top Navigation Bar* di header dan dashboard multi-kolom (sisi kiri ringkasan kas & riwayat transaksi, sisi kanan grafik donat & widget QRIS infaq cepat).

### 4. 🏪 Kategori Khusus Mushola Pasar & Istilah Bisaroh
- Disesuaikan khusus untuk aktivitas mushola di pasar (tanpa shalat Jumat & tanpa shalat Tarawih).
- **Pemasukan:** Kotak Amal Harian Mushola, Infaq Pedagang & Kios Pasar, Infaq Shalat Berjamaah, Donatur & Pengunjung Pasar, Kotak Wudhu & Sarana, dll.
- **Pengeluaran:** Menggunakan istilah **Bisaroh** (Imam & Petugas Mushola), Listrik & Token Air Pasar, Kebersihan & Sanitasi Mushola, Operasional, dsb.

### 5. 🏷️ Kategori Dinamis & Kustom
- Pengurus DKM dapat menambah atau menghapus kategori pengeluaran dan pemasukan secara fleksibel melalui tab **Pengaturan** > **Kategori Transaksi Dinamis**.
- Kategori baru otomatis muncul pada formulir pencatatan kas baru dan pilihan cepat (*quick chips*).

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
2. Pilih lokasi server terdekat (misal: `asia-southeast2` untuk Jakarta).

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
