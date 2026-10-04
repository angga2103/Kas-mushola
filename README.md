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

---

## 💰 Fitur Finansial Unggulan: Saldo Bulan Lalu & Kategori Dinamis

### 1. Skema Saldo Kas Berjalan (Saldo Bulan Lalu / Saldo Awal)
- Jika mushola sudah memiliki kas fisik atau rekening yang berjalan sebelum aplikasi digunakan:
  - Buka tab **Pengaturan** ➔ isi **"Saldo Kas Awal / Berjalan (Sebelum Sistem)"** ➔ klik **Simpan Informasi Mushola**.
- Rumus perhitungan saldo otomatis:
  $$\text{Saldo Kas Terkini} = \text{Saldo Bulan Lalu} + \text{Pemasukan Bulan Ini} - \text{Pengeluaran Bulan Ini}$$
- Tersedia kartu rumus transparan di Beranda, tabel pembukuan bulanan di Laporan, rincian cetak PDF, serta otomatis terformat di broadcast WhatsApp!

### 2. Kategori Pemasukan & Pengeluaran Dinamis
- Kategori Pemasukan (Kotak Amal, Infaq Pedagang, dll) dan Pengeluaran (Bisaroh, Listrik, Kebersihan, dll) kini sepenuhnya dinamis.
- Pengurus dapat menambah kategori baru dari menu **Pengaturan**, atau menambahkan langsung secara instan (*+ Tambah Kategori Baru*) saat mengisi form transaksi tanpa harus berpindah halaman.

---

## 📢 Fitur Universal: Agenda, Pengumuman & Rencana Pembangunan

### 1. Kolom Cantik Dinamis di Beranda
- **Kondisional Cerdas**: Hanya tampil ketika ada agenda berstatus **Aktif**. Jika kosong atau dinonaktifkan, halaman Beranda tetap bersih dan rapi tanpa kolom kosong.
- **Tampilan Khusus per Kategori**:
  - 📢 **Pengumuman Resmi** (Aksen Biru/Sky): Untuk info sholat tarawih, qurban, jadwal imsakiyah, pengumuman DKM.
  - 📅 **Undangan Acara & Kajian** (Aksen Emas/Amber): Untuk kajian rutin pedagang, peringatan hari besar, rapat DKM.
  - 🏗️ **Rencana Pembangunan & Renovasi** (Aksen Hijau/Emerald): Untuk perbaikan wudhu, kanopi, renovasi kubah, dilengkapi info target dana.
  - 🤝 **Kegiatan Sosial**: Santunan anak yatim pasar, bantuan pedagang.
- **Interaksi Instan**:
  - Tombol **"Bagikan ke WA"**: Membuat pesan WhatsApp formal dan islami dengan 1 klik untuk di-blast ke grup jamaah pasar.
  - Tombol **"Infaq Digital QRIS"**: Membuka popup QRIS langsung agar jamaah bisa berinfaq saat membaca rencana pembangunan atau acara.

### 2. Pengelolaan di Menu Pengaturan
- Masuk ke tab **Pengaturan** ➔ **Agenda, Acara & Pembangunan**.
- Terdapat tombol **Template Cepat 1-Klik** (*📢 Pengumuman*, *📅 Undangan Acara*, *🏗️ Rencana Pembangunan*) agar pengurus tidak perlu mengetik dari awal.
- Pengurus dapat mengedit, menghapus, atau mengaktifkan/menonaktifkan agenda (*toggle status*) kapan saja dengan sinkronisasi real-time cloud Firestore.

---

## 📖 Fitur Inspirasi Islami: Mutiara Hadits Shahih & Ayat Al-Qur'an (Rotasi Dinamis)

### 1. Kolom Indah di Beranda
- **Kutipan Otentik Berganti Otomatis**: Menampilkan ayat Al-Qur'an dan hadits shahih secara dinamis berdasarkan interval waktu rotasi (default per 3 jam, atau opsi 1 jam, 6 jam, 12 jam, 24 jam, maupun acak setiap reload).
- **Tipografi Indah**: Menggunakan kaligrafi Arab dengan font *Amiri*, teks terjemahan bahasa Indonesia yang jelas, badge tema (*Fadilah Sedekah & Infaq* / *Sholat Berjamaah & Tepat Waktu*), serta sumber perawi/surat yang shahih dan terverifikasi.
- **Interaktif**:
  - Tombol **"Acak Lainnya" (Shuffle)**: Menampilkan kutipan acak baru seketika.
  - Tombol **"Bagikan Hadits/Ayat ke WhatsApp"**: Memformat teks Arab, terjemahan, sumber riwayat, dan link mushola untuk syiar digital ke grup pedagang & jamaah.

### 2. Database Bawaan & Pengelolaan di Pengaturan
- **60 Database Awal Terverifikasi**:
  - 30 Kutipan otentik mengenai **Fadilah Sedekah & Infaq**.
  - 30 Kutipan otentik mengenai **Fadilah Sholat Berjamaah & Tepat Waktu**.
- **Pengaturan Lengkap Pengurus**:
  - Atur **Interval Rotasi Waktu** sesuai kebutuhan.
  - Tambah hadits/ayat baru kapan saja dengan form input lengkap (Tema, Jenis, Teks Arab, Terjemahan, Sumber Riwayat).
  - Filter pencarian dan penyaringan tema kutipan.
  - Edit & hapus kutipan.
  - Tombol **"Reset ke 60 Kutipan Bawaan"** jika ingin mengembalikan database original.

---

## 📊 Panduan Lengkap: Skema Perhitungan Saldo & Pencatatan Kas Berjalan

### 1. Apakah "Saldo Kas Awal Sebelum Sistem" Harus Diisi Ulang Setiap Bulan?
> **Jawabannya: TIDAK PERLU! Saldo Kas Awal cukup diisi HANYA 1 KALI SEUMUR HIDUP saat pertama kali aplikasi mulai digunakan.**

- **Contoh Nyata:**
  Jika aplikasi mulai digunakan pada bulan **Oktober 2026**, dan sisa kas fisik di mushola saat itu adalah **Rp 5.000.000**, maka Super Admin cukup mengisi `Rp 5.000.000` di menu **Pengaturan** ➔ *Saldo Kas Awal Sebelum Sistem*.

- **Bagaimana di Bulan-Bulan Berikutnya (November, Desember, dst.)?**
  Sistem ini menerapkan rumus pembukuan akuntansi otomatis:
  $$\text{Saldo Bulan Lalu (Otomatis)} = \text{Saldo Awal Mula} + \text{Total Semua Pemasukan Bulan-Bulan Lalu} - \text{Total Semua Pengeluaran Bulan-Bulan Lalu}$$

  - Saat kalender berganti ke **1 November 2026**, sisa saldo akhir bulan Oktober **langsung otomatis menjadi "Saldo Bulan Lalu"** untuk bulan November!
  - Saat kalender berganti ke **1 Desember 2026**, akumulasi saldo akhir November otomatis menjadi "Saldo Bulan Lalu" untuk bulan Desember.
  - **Kesimpulan:** Pengurus **TIDAK PERLU** repot-repot menghitung manual atau mengubah angka saldo awal setiap awal bulan!

### 2. Cara Mudah Input Pencatatan Saldo Keluar & Masuk
1. Klik tombol **"+ Catat Kas"** (tombol hijau melayang di HP atau di header desktop).
2. Pilih jenis transaksi:
   - **Pemasukan:** Masukkan tanggal, nominal, pilih kategori (*Kotak Amal Harian*, *Infaq Jumat*, dll), dan tulis keterangan.
   - **Pengeluaran:** Masukkan tanggal, nominal, pilih kategori (*Bisaroh Marbot*, *Listrik & Air*, *Kebersihan*, dll), dan tulis keterangan.
3. Klik **Simpan Transaksi**. Saldo total, grafik, dan laporan bulanan otomatis terakumulasi secara instan.

---

## 💳 Status Publikasi QRIS & Rekening Bank (Dalam Proses Pengajuan)

- **Mode Dalam Proses Pengajuan:**
  Karena barcode QRIS dan rekening bank syariah resmi mushola memerlukan proses validasi perbankan, aplikasi menyediakan status publikasi yang ramah dan elegan:
  - Tampilan Beranda & Modal: Menampilkan kartu modern dengan badge emas `⏳ Dalam Proses Pengajuan Perbankan`, menginformasikan bahwa pendaftaran sedang diverifikasi bank dan mengarahkan jamaah berinfaq tunai lewat Kotak Amal Mushola.
- **Opsi Pengaturan Super Admin:**
  Super Admin dapat memilih status publikasi di menu Pengaturan:
  1. `⏳ Dalam Proses Pengajuan` *(Default)*
  2. `✅ Aktif & Terverifikasi` *(Menampilkan barcode QRIS resmi & tombol salin rekening)*
  3. `🚫 Sembunyikan dari Publik` *(Menonaktifkan kolom infaq digital dari beranda)*

---

## 👥 Hierarki Peran: Super Admin vs Admin Kas (Kelola Saldo)

1. **Super Admin (`rayyan.kontak@gmail.com`):**
   - Hak akses penuh: mencatat kas, edit/hapus transaksi, mengakses tab **Pengaturan**, mengatur identitas mushola, saldo awal, susunan pengurus, mengangkat/mencabut admin, kategori kas, agenda, dan hadits.
2. **Admin Kas Biasa:**
   - **Khusus Operasional Saldo:** Hanya bisa mencatat pemasukan/pengeluaran kas, mengedit transaksi, menghapus transaksi, dan mencetak laporan / PDF.
   - Tab **Pengaturan** disembunyikan dari menu navigasi untuk mencegah perubahan data institusi yang tidak disengaja.
3. **Pembuatan Admin di Luar Pengurus DKM:**
   - Super Admin dapat membuat akun admin untuk orang di luar susunan pengurus (misal: *Relawan Kasir Toko*, *Admin Donatur*, dll) dengan menginput Nama, Jabatan kustom, Email, dan Password langsung di menu Pengaturan.
4. **Form Login Bersih:**
   - Input email dan password kini selalu kosong dan bersih secara default saat modal login dibuka untuk kemudahan login berbagai akun.

---

## 👨‍💻 Developer & Pengembang Aplikasi

Aplikasi Kas Mushola Pasar ini dikembangkan dengan dedikasi untuk transparansi & kemudahan tata kelola kemakmuran mushola:

- **Pengembang:** Mas Angga
- **WhatsApp:** [081775700114](https://wa.me/6281775700114?text=Halo%20Mas%20Angga%20pengembang%20aplikasi%20Kas%20Mushola)
- **Situs Web Resmi:** [Ansor.studio](https://ansor.studio)

*Kontak dan kartu profil pengembang juga dapat diakses langsung oleh pengurus dan jamaah di bagian bawah tab **Profil** aplikasi.*
