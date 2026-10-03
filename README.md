# 🕌 Panduan Lengkap Setup Firebase & Aplikasi Kas Mushola Pasar (PWA)

Aplikasi web **Progressive Web App (PWA)** real-time & transparan untuk pencatatan kas mushola pasar. Dilengkapi sistem **Firebase Authentication (Email & Password)** untuk pengurus DKM, serta akses publik aman untuk jamaah.

🔗 **Repository GitHub:** [https://github.com/angga2103/Kas-mushola.git](https://github.com/angga2103/Kas-mushola.git)

---

## 📖 DAFTAR ISI PANDUAN FIREBASE LENGKAP
1. [Langkah 1: Membuat Project Firebase Baru](#langkah-1-membuat-project-firebase-baru)
2. [Langkah 2: Mengaktifkan Firebase Authentication (Email & Password)](#langkah-2-mengaktifkan-firebase-authentication-email--password)
3. [Langkah 3: Mendaftarkan Akun Email Pengurus (Admin Pertama)](#langkah-3-mendaftarkan-akun-email-pengurus-admin-pertama)
4. [Langkah 4: Mengaktifkan Cloud Firestore Database (Real-time)](#langkah-4-mengaktifkan-cloud-firestore-database-real-time)
5. [Langkah 5: Memasang Aturan Keamanan (Security Rules) Firestore](#langkah-5-memasang-aturan-keamanan-security-rules-firestore)
6. [Langkah 6: Mengambil Kunci Konfigurasi Web (firebaseConfig)](#langkah-6-mengambil-kunci-konfigurasi-web-firebaseconfig)
7. [Langkah 7: Menghubungkan Firebase ke Aplikasi Web](#langkah-7-menghubungkan-firebase-ke-aplikasi-web)
8. [Uji Coba & Verifikasi Login Pengurus](#uji-coba--verifikasi-login-pengurus)

---

### Langkah 1: Membuat Project Firebase Baru
1. Buka peramban (browser) dan kunjungi: **[https://console.firebase.google.com/](https://console.firebase.google.com/)**.
2. Login menggunakan akun Google Anda (Gmail).
3. Klik tombol **"Add project"** (atau **"Tambah project"**).
4. Masukkan nama project Anda, misalnya: `kas-mushola-pasar`.
5. Klik **Continue**.
6. Pada bagian *Google Analytics*, Anda bisa **menonaktifkannya** (Turn OFF) agar setup lebih ringkas dan cepat, lalu klik **Create project**.
7. Tunggu sekitar 15-30 detik hingga muncul pesan *"Your new project is ready"*, lalu klik **Continue**.

---

### Langkah 2: Mengaktifkan Firebase Authentication (Email & Password)
Sistem ini menggunakan 2 metode autentikasi:
- **Email/Password:** Digunakan oleh Pengurus DKM untuk mencatat kas, menghapus, dan mengatur data.
- **Anonymous (Anonim):** Digunakan otomatis di latar belakang oleh Jamaah/Publik agar bisa melihat saldo dan laporan tanpa perlu login.

**Cara mengaktifkannya:**
1. Di menu bilah kiri (sidebar), klik **Build** > **Authentication**.
2. Klik tombol **Get started**.
3. Di tab **Sign-in method**, pilih penyedia **Email/Password**:
   - Aktifkan tombol toggle **Enable** pada baris pertama (*Email/Password*).
   - Biarkan opsi *Email link (passwordless)* nonaktif.
   - Klik **Save**.
4. Di halaman yang sama, klik tombol **Add new provider**:
   - Pilih **Anonymous** (paling bawah).
   - Aktifkan toggle **Enable**.
   - Klik **Save**.
5. Pastikan pada daftar *Sign-in providers* kini status **Email/Password** dan **Anonymous** sudah berstatus **Enabled**.

---

### Langkah 3: Mendaftarkan Akun Super Admin Utama
Aplikasi ini menetapkan email bawaan Super Admin: **`rayyan.kontak@gmail.com`**.

**Cara mendaftarkan Super Admin di Firebase Console:**
1. Di menu **Authentication**, klik tab **Users** (di sebelah tab *Sign-in method*).
2. Klik tombol **Add user**.
3. Masukkan:
   - **Email:** `rayyan.kontak@gmail.com`
   - **Password:** Minimal 6 karakter (misal: `123456` atau password kuat pilihan Anda).
4. Klik **Add user**.
5. Akun Super Admin Anda sudah aktif!

> [!NOTE]
> **Skema Pengangkatan Admin Pengurus Lainnya:**
> Anda **tidak perlu lagi** menambahkan user pengurus satu per satu lewat Firebase Console. Cukup login ke aplikasi web sebagai Super Admin, buka menu **Pengaturan** ➔ **Kelola Admin Pengurus**, pilih nama pengurus dari susunan DKM, lalu klik **Angkat Menjadi Admin** dan kirimkan akun via **WhatsApp** secara instan!


---

### Langkah 4: Mengaktifkan Cloud Firestore Database (Real-time)
Firestore berfungsi menyimpan data riwayat transaksi kas, profil mushola, susunan pengurus, dan kategori dinamis secara *real-time*.

1. Di menu bilah kiri, klik **Build** > **Firestore Database**.
2. Klik tombol **Create database**.
3. **Database location:** Pilih server terdekat dengan Indonesia, sangat disarankan memilih:
   - **`asia-southeast2` (Jakarta)** atau `asia-southeast1` (Singapura).
4. Klik **Next**.
5. Pada pilihan *Secure rules*, pilih **Start in test mode** untuk sementara, lalu klik **Enable** (atau **Create**).
6. Tunggu proses pembuatan database selesai hingga muncul halaman tabel Firestore.

---

### Langkah 5: Memasang Aturan Keamanan (Security Rules) Firestore
Langkah ini sangat **PENTING** demi keamanan kas mushola. Aturan ini memastikan:
- **Publik / Jamaah:** Hanya diizinkan **MEMBACA** (*read-only*), tidak bisa mengutak-atik uang kas.
- **Pengurus DKM (Email & Password):** Diizinkan **MEMBACA & MENULIS** (*read & write* / input & hapus).

**Cara memasang rules:**
1. Masuk ke halaman **Firestore Database**, lalu klik tab **Rules** di bagian atas.
2. Hapus seluruh isi kode yang ada, lalu salin dan tempel (copy-paste) kode aturan resmi berikut:

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    
    // Koleksi transaksi kas mushola
    match /transactions/{transactionId} {
      // Jamaah publik dapat melihat saldo & transparansi kas
      allow read: if true;
      // Hanya pengurus terotentikasi (Super Admin & Admin Pengurus) yang boleh input & hapus
      allow write: if request.auth != null;
    }
    
    // Koleksi profil & susunan pengurus mushola
    match /profile/{docId} {
      allow read: if true;
      allow write: if request.auth != null;
    }
  }
}
```

3. Klik tombol **Publish** (Terbitkan) di pojok kanan atas.

---

### Langkah 6: Mengambil Kunci Konfigurasi Web (firebaseConfig)
1. Di menu kiri paling atas, klik ikon **Gerigi (Settings)** di samping tulisan *Project Overview*, lalu pilih **Project settings**.
2. Gulir ke bawah ke bagian **Your apps**.
3. Klik ikon Web bertanda **`</>`**.
4. Beri nama aplikasi web Anda, misal: `Web Kas Mushola`.
5. Jangan centang *Firebase Hosting* (karena kita menggunakan Cloudflare Pages / GitHub Pages).
6. Klik **Register app**.
7. Akan muncul blok kode JavaScript berisi `const firebaseConfig = { ... }`.
8. Salin nilai-nilai yang ada di dalamnya:
   - `apiKey` (Contoh: `AIzaSyB...`)
   - `authDomain` (Contoh: `kas-mushola-pasar.firebaseapp.com`)
   - `projectId` (Contoh: `kas-mushola-pasar`)
   - `storageBucket` (Contoh: `kas-mushola-pasar.appspot.com`)
   - `appId` (Contoh: `1:123456789:web:abcdef...`)

---

### Langkah 7: Menghubungkan Firebase ke Aplikasi Web
Anda dapat memasukkan konfigurasi langsung dari antarmuka aplikasi tanpa perlu membuka kodingan:

1. Buka aplikasi `index.html` di browser Anda.
2. Klik tombol **"Pengurus"** di pojok kanan atas.
3. Masuk menggunakan mode demo atau klik banner kuning **"Setup Firebase"** di atas.
4. Buka tab **Pengaturan** > bagian **Koneksi Firebase Cloud** > klik **"Atur Kredensial Firebase"**.
5. Tempelkan nilai `apiKey`, `authDomain`, `projectId`, `storageBucket`, dan `appId` yang sudah Anda salin tadi.
6. Klik **"Simpan & Hubungkan"**.
7. Aplikasi akan memuat ulang secara otomatis. Banner hijau akan muncul bertuliskan:  
   *`"Terhubung ke Firebase Cloud Firestore & Auth."`*

---

### 🧪 Uji Coba & Verifikasi Login Pengurus
1. Klik tombol **"Login Pengurus"** di pojok kanan atas.
2. Masukkan email dan password pengurus yang Anda buat di **Langkah 3**.
3. Klik **"Masuk Pengurus"**.
4. Perhatikan:
   - Muncul notifikasi hijau: *"Login Pengurus berhasil!"*.
   - Badge header berubah menjadi **Admin: emailanda@domain.com**.
   - Tombol melayang **(+)** muncul di pojok kanan bawah.
   - Tombol **Hapus** muncul di riwayat transaksi.
   - Tab **Pengaturan** aktif.
5. Coba catat transaksi kas baru, dan cek di Firebase Console Firestore: data akan seketika tersimpan di awan secara real-time!

---

## 🌐 Deploy Otomatis ke Cloudflare Pages & Custom Domain `mushola.my.id`

### 1. Menghubungkan GitHub ke Cloudflare Pages:
1. Buka dashboard Cloudflare: **[https://dash.cloudflare.com/](https://dash.cloudflare.com/)**.
2. Masuk ke **Compute (Workers & Pages)** ➔ tab **Pages** ➔ klik **Create application** (Connect to Git).
3. Pilih repository GitHub: **`Kas-mushola`** (`angga2103/Kas-mushola`).
4. Pengaturan build:
   - **Framework preset:** `None`
   - **Build command:** *(Kosongkan)*
   - **Output directory:** *(Kosongkan / default `.`)*
5. Klik **Save and Deploy**. Website Anda langsung aktif di `https://kas-mushola.pages.dev`!

### 2. Memasang Domain `mushola.my.id`:
1. Di halaman project Cloudflare Pages Anda, buka tab **Custom domains**.
2. Klik tombol **Set up a custom domain**.
3. Masukkan domain Anda: **`mushola.my.id`**.
4. Ikuti instruksi verifikasi DNS/Nameserver Cloudflare.
5. Selesai! Domain Anda otomatis berstatus SSL/HTTPS aman.

### 3. Mendaftarkan Authorized Domain di Firebase (Wajib):
1. Buka **Firebase Console** ➔ Project `kas-mushola`.
2. Masuk ke menu **Build ➔ Authentication** ➔ tab **Settings** ➔ **Authorized domains**.
3. Klik **Add domain** dan tambahkan:
   - `mushola.my.id`
   - `kas-mushola.pages.dev`
4. Klik **Add**. Sekarang login pengurus di domain resmi Anda siap digunakan 100%!

