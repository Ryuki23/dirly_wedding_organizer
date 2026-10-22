# Perbaikan: Paket & Portofolio Kosong di Web

## Apa yang diubah
Ditambahkan "fallback otomatis" pada 3 provider:
- lib/providers/package_provider.dart
- lib/providers/portfolio_provider.dart
- lib/providers/customization_provider.dart
- lib/services/database_service.dart (buka akses daftar opsi bawaan)

Cara kerjanya: kalau database tidak memberi data (apa pun sebabnya —
gagal, kosong, atau storage browser tidak tersedia), aplikasi otomatis
memakai data bawaan dari kode (DummyDataService). Jadi:

- Di WEB    : Paket, Portofolio, dan Customize Paket DIJAMIN tampil
- Di ANDROID: query database berhasil, fallback TIDAK aktif —
              katalog tetap yang dikelola Admin (tidak ada yang berubah)

## Cara pakai (paling mudah)
Timpa seluruh folder `lib` project Anda dengan folder `lib` dari paket ini.

Lalu jalankan:
    flutter clean
    flutter pub get
    flutter run -d chrome

Setelah Chrome terbuka, tekan Ctrl+Shift+R (hard refresh).

## Catatan
Fitur yang tetap TIDAK bisa jalan di web (karena package-nya mobile-only):
- Upload foto dari galeri (image_picker) di menu Admin
- Pembayaran WebView (webview_flutter)
Halaman lain — Beranda, Paket, Detail, Customize, Ringkasan, Booking,
Portofolio, Promo, Kontak, FAQ — bisa didemokan penuh di web.
