# Buku Kas Kelas — Versi Android (siap Play Store)

Folder ini adalah proyek **Capacitor** yang membungkus aplikasi web (di folder `www/`) menjadi **aplikasi Android asli (.aab/.apk)** yang bisa kamu publikasikan ke Google Play Store dan dipasang banyak orang seperti aplikasi pada umumnya.

Setelah dibungkus dengan Capacitor, `localStorage` yang dipakai aplikasi **otomatis berperilaku seperti penyimpanan lokal aplikasi native**: tersimpan privat di dalam data aplikasi milik HP tiap pengguna, tetap ada walau HP di-restart, dan hanya hilang jika pengguna meng-uninstall aplikasi atau menekan "Hapus Data/Clear Storage" di pengaturan aplikasi Android — persis seperti aplikasi Play Store pada umumnya (tidak lagi terikat ke "data browser" seperti versi web biasa).

---

## 0. Yang Perlu Disiapkan di Komputer

Semua proses build APK/AAB **wajib dilakukan di komputer** (tidak bisa lewat HP), karena butuh Android SDK. Install dulu:

1. **Node.js** (versi 18 ke atas) — https://nodejs.org
2. **Android Studio** (sudah termasuk Android SDK) — https://developer.android.com/studio
3. **Java JDK 17** — biasanya sudah ikut terpasang bersama Android Studio.
4. **VS Code** — untuk mengedit kode (opsional, boleh pakai editor lain).

Akun yang perlu disiapkan:
- **Akun Google Play Console** (biaya pendaftaran developer **sekali bayar $25 / ±Rp400rb**) — https://play.google.com/console/signup

---

## 1. Install Dependensi Proyek

Buka folder `kas-kelas-capacitor` ini di terminal VS Code, lalu jalankan:

```bash
npm install
```

## 2. Tambahkan Platform Android

```bash
npx cap add android
```

Perintah ini akan membuat folder `android/` berisi proyek Android native lengkap (dibuat otomatis, jangan diedit manual kecuali paham Android).

## 3. Sinkronkan File Web ke Proyek Android

Setiap kali kamu mengubah isi folder `www/` (misalnya menambah fitur baru di `index.html`), jalankan ulang:

```bash
npx cap sync android
```

## 4. Buka & Jalankan di Android Studio

```bash
npx cap open android
```

Android Studio akan terbuka otomatis. Tunggu proses **Gradle Sync** selesai (bisa beberapa menit di percobaan pertama). Setelah selesai:
- Sambungkan HP Android (aktifkan USB Debugging) **atau** buat emulator lewat **Device Manager**.
- Klik tombol ▶️ **Run** untuk memasang & mencoba aplikasi.

---

## 5. Menyiapkan Ikon Aplikasi

Ikon dasar sudah tersedia di `www/icons/icon-512.png`. Agar ikon tampil rapi di semua ukuran layar Android (adaptive icon):

1. Di Android Studio: klik kanan folder `app/res` → **New → Image Asset**.
2. Pilih **Launcher Icons (Adaptive and Legacy)**.
3. Upload `www/icons/icon-512.png` sebagai foreground image.
4. Ikuti wizard sampai selesai (klik **Next** → **Finish**).

## 6. Mengganti Nama Paket / Nama Aplikasi (opsional)

- **Nama aplikasi** yang tampil di HP: edit `appName` di `capacitor.config.json`, lalu jalankan `npx cap sync android`.
- **App ID / package name** (identitas unik di Play Store, tidak bisa diubah setelah publish pertama kali): edit `appId` di `capacitor.config.json` **sebelum** menjalankan `npx cap add android`. Contoh: `com.namasekolahmu.kaskelas`.

---

## 7. Membuat Signed App Bundle (.aab) untuk Diunggah ke Play Store

Google Play mewajibkan file **.aab (Android App Bundle)**, ditandatangani dengan keystore.

1. Di Android Studio, buka menu **Build → Generate Signed Bundle / APK**.
2. Pilih **Android App Bundle** → **Next**.
3. Klik **Create new...** untuk membuat keystore baru (hanya sekali, untuk seterusnya):
   - Isi lokasi file keystore (simpan baik-baik, misalnya `kaskelas-release-key.jks`).
   - Isi password, alias, dan validitas (misalnya 25 tahun).
   - **PENTING:** simpan file keystore + password ini di tempat aman (Google Drive pribadi, dsb). Jika hilang, kamu **tidak akan bisa lagi mengupdate aplikasi** yang sudah terlanjur publish, dan harus publish sebagai aplikasi baru dari nol.
4. Pilih **release** build variant → **Finish**.
5. Android Studio akan menghasilkan file `app-release.aab` di folder `android/app/release/`.

---

## 8. Mempublikasikan ke Google Play Store

1. Daftar/masuk ke **Google Play Console**: https://play.google.com/console
2. Klik **Create app**, isi:
   - Nama aplikasi (contoh: *Buku Kas Kelas*)
   - Bahasa default: Indonesia
   - Jenis: App, Gratis (Free)
3. Lengkapi **App content** (wajib sebelum bisa publish):
   - **Privacy Policy URL** — wajib walaupun aplikasi tidak mengirim data ke server manapun. Lihat template di bagian 9 di bawah.
   - **Content rating** — isi kuesioner (aplikasi ini akan dapat rating rendah/semua umur karena tidak ada konten sensitif).
   - **Target audience & content**
   - **Data safety** — jawab jujur: aplikasi ini **tidak mengumpulkan atau membagikan data pengguna** (semua data tersimpan lokal di HP).
   - **Ads** — pilih "No ads" (aplikasi ini tidak menampilkan iklan).
4. Buka menu **Store listing**, isi:
   - Deskripsi singkat & lengkap aplikasi.
   - **Screenshot** (ambil dari aplikasi yang sedang berjalan di HP/emulator, minimal 2).
   - **App icon** (512×512, gunakan `www/icons/icon-512.png`).
   - **Feature graphic** (1024×500 — buat gambar banner sederhana, bisa pakai Canva).
5. Buka menu **Production** (atau mulai dari **Testing → Internal testing** dulu untuk uji coba terbatas sebelum rilis publik) → **Create new release**.
6. Upload file `app-release.aab` dari langkah 7.
7. Isi **Release notes** (misalnya: "Rilis pertama").
8. Klik **Review release**, lalu **Start rollout to Production**.
9. Google akan melakukan **review** (biasanya beberapa jam sampai beberapa hari). Setelah disetujui, aplikasi otomatis muncul di Play Store dan bisa dipasang siapa saja.

---

## 9. Template Privacy Policy (Kebijakan Privasi)

Play Store mewajibkan URL kebijakan privasi. Kamu bisa salin teks ini ke halaman web sederhana (misalnya file `privacy.html` yang di-hosting gratis lewat GitHub Pages/Netlify), lalu masukkan link-nya ke Play Console:

```
Kebijakan Privasi — Buku Kas Kelas

Aplikasi "Buku Kas Kelas" tidak mengumpulkan, menyimpan, atau membagikan
data pribadi pengguna ke server atau pihak ketiga manapun. Seluruh data
yang dimasukkan pengguna (nama siswa, catatan kas, pembayaran, dan
pengeluaran) disimpan sepenuhnya secara lokal di perangkat pengguna dan
tidak pernah dikirim melalui internet.

Data akan terhapus jika pengguna menghapus data aplikasi atau
meng-uninstall aplikasi ini.

Jika ada pertanyaan terkait kebijakan privasi ini, hubungi: [email kamu]
```

---

## 10. Update Aplikasi di Masa Depan

Setiap kali merilis update baru:
1. Edit fitur di `www/index.html` seperti biasa.
2. Buka `android/app/build.gradle`, naikkan `versionCode` (misal 1 → 2) dan `versionName` (misal "1.0" → "1.1").
3. Jalankan `npx cap sync android`.
4. Generate signed bundle baru (langkah 7) **memakai keystore yang sama** seperti pertama kali.
5. Upload `.aab` baru ke Play Console → buat release baru.

---

## Struktur Folder

```
kas-kelas-capacitor/
├── package.json
├── capacitor.config.json     -> nama app, appId, konfigurasi Android
├── www/                       -> isi aplikasi web (HTML/CSS/JS) yang dibungkus
│   ├── index.html
│   ├── manifest.json
│   ├── sw.js
│   └── icons/
└── android/                   -> dibuat otomatis setelah `npx cap add android`
```

## Ringkasan Tentang Penyimpanan Data

- Di dalam aplikasi native (setelah dibungkus Capacitor), `localStorage` disimpan di ruang data privat aplikasi (`/data/data/com.kaskelas.app/...` di sistem Android) — **persis seperti penyimpanan lokal aplikasi Play Store pada umumnya**, bukan lagi "data browser".
- Data antar-pengguna **tidak saling terhubung**: tiap orang yang install aplikasi ini akan punya data kas kelasnya sendiri-sendiri di HP masing-masing (tidak otomatis sinkron ke satu database bersama).
- Fitur **Cadangkan & Pulihkan Data (JSON)** di menu Pengaturan aplikasi tetap disarankan dipakai secara berkala, untuk berjaga-jaga jika pengguna mengganti HP atau meng-uninstall aplikasi.
