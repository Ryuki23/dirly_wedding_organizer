# Perubahan: Fitur Pembayaran Langsung (Midtrans Snap) + WhatsApp

Ringkasan diff terhadap `lib/` yang Anda upload. Flow WhatsApp yang lama
**tidak diubah perilakunya sama sekali** — hanya ditambah satu flow baru
yang berjalan berdampingan.

## File baru
| File | Fungsi |
|---|---|
| `lib/models/payment_model.dart` | Enum `PaymentMethod`, `PaymentStatus`, class `SnapTransaction` |
| `lib/services/midtrans_service.dart` | Panggil backend (create transaction + cek status) |
| `lib/screens/payment/payment_webview_screen.dart` | WebView Snap + deteksi redirect selesai |
| `lib/widgets/payment_method_selector.dart` | Kartu pilihan "WhatsApp" vs "Bayar Langsung" |
| `midtrans_backend_reference/` (di luar `lib/`) | Backend Node.js minimal — **wajib di-deploy** |
| `PUBSPEC_ADDITIONS.yaml` | Dependency yang perlu ditambahkan |

## File yang diedit
| File | Perubahan |
|---|---|
| `lib/models/booking_model.dart` | + field `paymentMethod`, `paymentStatus`, `paymentAmount`, `snapRedirectUrl`; + `copyWith()` |
| `lib/core/constants/app_constants.dart` | + konstanta Midtrans (`midtransBackendBaseUrl`, `midtransClientKey`, `midtransIsProduction`, `defaultDownPaymentRate`); `dbVersion` 1 → 2 |
| `lib/services/database_service.dart` | + kolom `payment_*` di tabel `bookings`; + `onUpgrade` migration; + `updateBookingPaymentStatus()` |
| `lib/providers/booking_provider.dart` | + state `paymentMethod`; `submit()` kini bercabang WA/Midtrans; + `confirmPaymentStatus()` |
| `lib/core/routes/app_routes.dart` | + route `paymentWebview` |
| `lib/core/routes/route_generator.dart` | + case untuk `paymentWebview` |
| `lib/core/constants/app_strings.dart` | + string UI untuk metode pembayaran & status |
| `lib/screens/booking/booking_screen.dart` | + `PaymentMethodSelector` + ringkasan DP; tombol submit dinamis; `_submit()` bercabang |
| `lib/screens/booking_success/booking_success_screen.dart` | Judul/ikon/notice dinamis sesuai status; + baris nominal & status pembayaran; + tombol "Coba Bayar Lagi" |

## File yang TIDAK berubah
Semua screen lain (home, package_list, package_detail, portfolio, promo,
faq, contact, splash, shared/main_nav_shell), semua provider lain, semua
model lain, semua widget lain, dan `main.dart` — **tidak disentuh sama
sekali**.

## Langkah pemasangan
1. Timpa folder `lib/` project Anda dengan `lib/` dari zip ini (atau merge
   file per file jika Anda sempat mengubah sesuatu sejak upload terakhir).
2. Tambahkan 2 dependency dari `PUBSPEC_ADDITIONS.yaml` ke `pubspec.yaml`
   Anda, lalu `flutter pub get`.
3. Deploy `midtrans_backend_reference/` (lihat README di dalamnya) —
   **wajib**, tanpa ini tombol "Bayar Langsung" akan selalu gagal.
4. Isi `AppConstants.midtransBackendBaseUrl` dan `midtransClientKey` di
   `lib/core/constants/app_constants.dart` dengan nilai asli setelah
   backend live.
5. Uninstall app lama dari device test / hapus data app sebelum run ulang,
   supaya migrasi `onUpgrade` sempat teruji dari database lama (atau cukup
   jalankan seperti biasa — `onUpgrade` otomatis jalan untuk yang sudah
   pernah install versi lama).

## Yang belum di-cover (di luar scope perubahan ini)
- Riwayat "pesanan saya" (list semua booking dari `getBookings()`) — data
  sudah tersimpan lengkap di SQLite, tinggal dibuatkan 1 screen baru kapan
  pun dibutuhkan.
- Refund/pembatalan dari sisi app — Midtrans mendukung ini via API, tapi
  belum ada UI/endpoint untuk itu di reference ini.
