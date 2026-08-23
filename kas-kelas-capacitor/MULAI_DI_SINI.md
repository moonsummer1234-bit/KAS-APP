# MULAI DI SINI — Cara Mendapatkan File .aab Siap Upload ke Play Store

Semua kode dan konfigurasi sudah selesai dibuatkan, termasuk **keystore penandatanganan** (di folder `keystore-JANGAN-DIBAGIKAN/`) dan **pipeline build otomatis** (GitHub Actions). Kamu tinggal ikuti 3 langkah ini — tidak perlu install Android Studio sama sekali.

---

## Langkah 1 — Upload Proyek Ini ke GitHub

1. Buat akun GitHub (gratis) di https://github.com jika belum punya.
2. Buat repository baru (boleh **Private**, supaya orang lain tidak bisa lihat kode kamu): klik **New repository** → beri nama misalnya `kas-kelas-app` → **Create repository**.
3. Upload semua isi folder ini ke repository tersebut. Cara termudah tanpa perlu tahu Git:
   - Di halaman repository GitHub, klik **Add file → Upload files**.
   - Drag & drop **semua isi folder ini** (kecuali folder `keystore-JANGAN-DIBAGIKAN/` — **JANGAN diupload ke GitHub**, lihat peringatan di Langkah 2).
   - Klik **Commit changes**.

> ⚠️ **PENTING:** Folder `keystore-JANGAN-DIBAGIKAN/` berisi kunci rahasia penandatanganan aplikasi. **Jangan pernah upload folder ini ke GitHub atau tempat publik manapun.** Simpan folder ini di penyimpanan pribadi yang aman (Google Drive pribadi, dsb) sebagai cadangan — jika hilang, kamu tidak bisa lagi merilis update aplikasi di masa depan.

## Langkah 2 — Masukkan Kunci Rahasia ke GitHub Secrets

Ini langkah supaya proses build di GitHub bisa menandatangani aplikasi memakai keystore kamu, **tanpa** keystore-nya ikut ter-upload secara publik.

1. Buka repository kamu di GitHub → **Settings** → **Secrets and variables** → **Actions**.
2. Klik **New repository secret**, buat 4 secret berikut satu per satu:

| Nama Secret | Isi (ambil dari file di `keystore-JANGAN-DIBAGIKAN/`) |
|---|---|
| `KEYSTORE_BASE64` | Seluruh isi file `kaskelas-release.jks.base64.txt` (copy-paste semua teksnya) |
| `KEYSTORE_PASSWORD` | Lihat baris `KEYSTORE_PASSWORD=` di file `kredensial.txt` |
| `KEY_ALIAS` | Lihat baris `KEY_ALIAS=` di file `kredensial.txt` (isinya: `kaskelas`) |
| `KEY_PASSWORD` | Lihat baris `KEY_PASSWORD=` di file `kredensial.txt` |

## Langkah 3 — Jalankan Build & Unduh File .aab

1. Buka tab **Actions** di repository GitHub kamu.
2. Kamu akan lihat workflow **"Build Signed Android App Bundle"** berjalan otomatis (karena baru saja upload file). Kalau belum jalan, klik workflow itu → **Run workflow**.
3. Tunggu 3–5 menit sampai tanda centang hijau ✅ muncul.
4. Klik hasil run yang sukses tersebut → scroll ke bagian **Artifacts** di bawah → unduh **`app-release-aab`** (ini file yang diupload ke Play Store) dan/atau **`app-release-apk`** (untuk dicoba install manual dulu di HP kamu sebelum publish).
5. Extract file zip yang diunduh, di dalamnya ada `app-release.aab`.

**File `app-release.aab` inilah yang kamu upload ke Google Play Console.**

---

## Langkah Terakhir — Publish ke Play Console (bagian yang harus kamu lakukan sendiri)

Ini satu-satunya bagian yang **tidak bisa diwakilkan ke siapapun** — termasuk saya — karena Google mewajibkan verifikasi identitas & pembayaran langsung dari pemilik akun:

1. Daftar di https://play.google.com/console/signup (biaya sekali $25).
2. Buat aplikasi baru, isi Store Listing (judul, deskripsi, screenshot, ikon — ikon sudah tersedia di `www/icons/icon-512.png`).
3. Isi **Privacy Policy URL** — pakai isi file `privacy-policy.html` yang sudah disediakan (upload/host dulu, misalnya lewat GitHub Pages, lalu masukkan link-nya).
4. Di menu **Production** (atau mulai dari **Internal testing** dulu) → **Create new release** → upload `app-release.aab` dari Langkah 3 di atas.
5. Lengkapi Content rating, Data safety (jawab: tidak mengumpulkan data), Ads (jawab: tidak ada iklan).
6. **Submit for review.** Google biasanya butuh beberapa jam–beberapa hari untuk menyetujui rilis pertama.

Setelah disetujui, aplikasi otomatis tayang di Play Store dan siapa saja bisa mengunduh & install.

---

## Update Aplikasi di Masa Depan

Setiap kali ingin merilis update:
1. Edit `www/index.html` (atau file lain) seperti biasa.
2. Buka `android/app/build.gradle`, naikkan angka `versionCode` (misal 1 → 2).
3. Upload perubahan ke GitHub (commit & push) → workflow otomatis build ulang `.aab` baru dengan keystore yang sama.
4. Unduh hasilnya dari tab Actions → upload ke Play Console sebagai release baru.

Lihat `README.md` untuk detail teknis lebih lengkap (termasuk cara build manual lewat Android Studio jika suatu saat kamu ingin melakukannya secara lokal).
