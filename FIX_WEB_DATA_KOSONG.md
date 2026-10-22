# Perbaikan: Data Paket Kosong di Web ("Paket tidak ditemukan")

## Penyebab
Di web, `getDatabasesPath()` (fungsi khusus Android/iOS) MELEMPAR ERROR
karena browser tidak punya filesystem. Akibatnya `openDatabase()` gagal →
semua query database gagal → daftar paket kosong → muncul pesan
"Paket tidak ditemukan".

Ini beda dari perbaikan sebelumnya (yang sudah benar: factory web +
worker files). Titik gagal terakhir ada di cara membangun PATH database.

## Yang diperbaiki (1 file saja)
`lib/services/database_service.dart` — method `_initDatabase()`:
- Di WEB  : pakai nama database langsung (tanpa getDatabasesPath)
- Di ANDROID: tetap seperti biasa (tidak ada yang berubah)

## Cara pakai
1. Timpa file lama dengan `lib/services/database_service.dart` dari paket ini.
2. Jalankan ulang:
   flutter clean
   flutter run -d chrome

Tidak perlu setup ulang worker / pubspec — semua itu sudah benar dari
langkah sebelumnya.

## Cara memastikan berhasil
Buka Console (F12) di Chrome, cari baris:
  [DEBUG] SQLite database path: dirly_wedding.db (web: true)
Kalau muncul "web: true" dan tidak ada error merah setelahnya, database
web sudah jalan dan daftar paket akan tampil.
