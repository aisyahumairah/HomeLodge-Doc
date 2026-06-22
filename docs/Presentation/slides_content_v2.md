# HomeLodge — PSM I Presentation Slides (v2)
**Jabatan Kejuruteraan Perisian | Projek Sarjana Muda I**

> **Format:** 10 slides · 10 min presentation + 5 min Q&A · 1–1½ min per slide
> **Guidelines:** Big & bold text · Reduce text, increase infographics · No animations · Use tables & point-forms

---

## SLIDE 1 — PENDAHULUAN PEMBENTANGAN (Title)

**HOMESTAY BOOKING MANAGEMENT SYSTEM (HomeLodge)**

- **Nama Pelajar:** Aisyah Umairah binti Azmir
- **Nama Penyelia:** Dr. Zalmiyah binti Zakaria
- **Program:** Sarjana Muda Kejuruteraan Perisian
- **Sesi:** 2025 / 2026

---

## SLIDE 2 — PENGENALAN PROJEK (Project Introduction)

**Latarbelakang Masalah:**

Pengendali homestay yang menguruskan beberapa unit bergantung kepada kaedah manual yang menyebabkan pelbagai masalah operasi.

| Masalah | Kesan |
|---|---|
| Tiada rekod ketersediaan dikongsi | Tempahan berganda — dua tetamu menempah tarikh sama |
| Kutipan bayaran manual | Tiada resit, tiada proses bayaran balik yang konsisten |
| Penyerahan kunci fizikal | Pengendali perlu hadir setiap kali daftar masuk |
| Komunikasi tersebar di chat peribadi | Tiada kaitan antara perbualan dengan rekod tempahan |
| Peringatan manual | Tempahan belum dibayar terlepas pandang |

**Satu unit boleh diurus. Tiga unit atau lebih berjalan serentak — di sinilah masalah bermula.**

---

## SLIDE 3 — MATLAMAT PROJEK (Project Aim)

**Matlamat:**

Membangunkan HomeLodge, sebuah sistem pengurusan tempahan homestay berasaskan web yang mendigitalkan proses tempahan, pembayaran, akses hartanah, dan komunikasi — untuk pengendali yang menguruskan pelbagai unit homestay.

**HomeLodge bukan marketplace.**

Ia adalah alat pengurusan peribadi yang dimiliki, dikonfigurasi, dan dikawal sepenuhnya oleh pengendali.

| Ciri Pembeza | Penerangan |
|---|---|
| Tiada komisen | Sifar yuran per-tempahan |
| Kawalan penuh | Pengendali memiliki sistem dan semua data |
| Penyenaraian peribadi | Tiada pendedahan marketplace wajib |

---

## SLIDE 4 — OBJEKTIF PROJEK (Project Objectives)

**Objektif 1 — Kaji & Analisis:**
Mengumpul dan mendokumentasikan semua keperluan fungsian dan bukan fungsian untuk HomeLodge.

**Objektif 2 — Reka Bentuk:**
Menghasilkan seni bina sistem, skema pangkalan data, dan reka bentuk UI untuk semua modul dan kedua-dua peranan pengguna.

**Objektif 3 — Pembangunan:**
Membangunkan aplikasi web HomeLodge dengan 11 modul fungsian.

**Objektif 4 — Pengujian:**
Mengesahkan ketepatan, keselamatan, dan kebolehgunaan berdasarkan keperluan yang ditetapkan.

**11 Modul:**
Authentication · Homestay Management · Booking · Payment · Notification · Chat · User Management · Role & Permission · System Settings · Audit Logs · QR Code Door Access

---

## SLIDE 5 — KAJIAN LATARBELAKANG MASALAH (Literature Review / Background Study)

**Perbandingan Sistem Sedia Ada dengan HomeLodge:**

| Ciri / Feature | Airbnb | Booking.com | Agoda | HomeLodge |
|---|---|---|---|---|
| Kalendar ketersediaan dalam talian | ✓ | ✓ | ✓ | ✓ |
| Pemprosesan pembayaran dalam talian | ✓ | ✓ | ✓ | ✓ |
| Pemesejan tetamu-ke-hos | ✓ | ✓ | ✓ | ✓ |
| Akses pintu QR code | ✗ | ✗ | ✗ | ✓ |
| Pengurusan lanjutan tempahan | ✗ | ✗ | ✗ | ✓ |
| Kawalan akses berasaskan peranan (RBAC) | ✗ | ✗ | ✗ | ✓ |
| Operasi peribadi, bukan marketplace | ✗ | ✗ | ✗ | ✓ |
| Log audit kekal (immutable) | ✗ | ✗ | ✗ | ✓ |
| Tiada komisen per-tempahan | ✗ | ✗ | ✗ | ✓ |

**Kesimpulan:** Ketiga-tiga platform menyelesaikan asas tetapi tiada yang memberi pengendali sistem peribadi yang boleh dikawal.

**Penyelesaian yang dicadangkan:**
HomeLodge menyediakan akses pintu QR code, pengurusan lanjutan tempahan, RBAC, log audit, dan operasi tanpa komisen — ciri-ciri yang tiada pada platform sedia ada.

---

## SLIDE 6 — METODOLOGI PEMBANGUNAN (Development Methodology)

**Pendekatan Hibrid: Waterfall + Agile**

```
Fasa 1: Analisis Keperluan       → Waterfall
         ↓
Fasa 2: Reka Bentuk Sistem       → Waterfall
         ↓
Fasa 3: Pembangunan (11 modul)   → Agile (Scrum + Kanban Board)
         ↓
Fasa 4: Pengujian                → Agile (Scrum + Kanban Board)
         ↓
Fasa 5: Pelancaran               → Waterfall (senarai semak berurutan)
```

**Mengapa Hibrid?**

| Aspek | Waterfall | Agile |
|---|---|---|
| Digunakan untuk | Dokumentasi formal PSM, analisis & reka bentuk | Pembangunan & pengujian 11 modul |
| Kelebihan | Keperluan diformalkan sebelum pengkodan | Penghantaran berperingkat, kerja boleh dijejak |
| Kanban States | — | To Do → In Progress → In Review → Done |

---

## SLIDE 7 — SPESIFIKASI KEPERLUAN / INPUT / OUTPUT (System Requirements)

**Spesifikasi Perkakasan (Hardware):**

| Komponen | Spesifikasi |
|---|---|
| Laptop / Desktop | Intel Core i5, 8 GB RAM, 256 GB SSD |
| Internet | Broadband, minimum 10 Mbps |
| Pelayan (Server) | 2 vCPU, 4 GB RAM, 20 GB SSD |
| Peranti Smart Lock | Serasi QR code (pemasangan fizikal di luar skop) |

**Spesifikasi Perisian (Software / Technology Stack):**

| Lapisan | Teknologi |
|---|---|
| Backend | Laravel 11 · PHP 8.2 |
| Frontend | Blade · Alpine.js · Bootstrap 5 |
| Pangkalan Data | MySQL 8 · Eloquent ORM |
| Masa Nyata (Real-time) | Laravel Reverb (WebSocket) · Laravel Echo |
| Pengesahan / RBAC | laravel/breeze · laravel/socialite · spatie/laravel-permission |
| QR Code | simplesoftwareio/simple-qrcode (lokal, tiada API luaran) |
| Pembayaran | Billplz / Toyyibpay / Stripe (webhook-driven) |
| Log Audit | spatie/laravel-activitylog |
| PDF | barryvdh/laravel-dompdf |

---

## SLIDE 8 — HASIL PERLAKSANAAN PSM I DAN PERANCANGAN PSM II

### Hasil PSM I (Analysis & Design)

**Model Use-Case:**
- 4 Aktor: Guest, Admin, Payment Gateway, Google Calendar API
- 12 Modul · 47 Use Cases didokumentasikan
- Semua use cases dijejak kepada keperluan URS dan PRD

**Seni Bina Sistem (MVC + Service Layer):**
- Client: Blade + Alpine.js + Bootstrap 5 + Laravel Echo
- Server: Nginx → PHP-FPM → Laravel 11 + Reverb WebSocket
- Data: MySQL 8 (primer) · Redis 7 (cache / queues / sessions)
- Luaran: Payment Gateway · Google OAuth + Calendar · SMTP

**Reka Bentuk Pangkalan Data:**
- 17 entiti dengan kekangan foreign key dan soft deletes
- Jadual utama: bookings, booking_extensions, payments, qr_codes, bills
- Jadual settings menggunakan model key-value — tiada migrasi skema untuk konfigurasi baru
- Pengendalian webhook idempotent melalui indeks gateway_reference unik

**Reka Bentuk Antara Muka:**
- Guest: top navbar, aliran tempahan linear (layari → tempah → bayar → QR code)
- Admin: sidebar kiri tetap, 10 kumpulan navigasi
- Responsif merentasi mobile, tablet, dan desktop

### Perancangan PSM II

| Fasa | Aktiviti |
|---|---|
| Fasa 1 | Persediaan persekitaran: Laragon, MySQL, Redis, Composer + npm |
| Fasa 2 | Pembangunan Backend: 11 modul dalam sprint Agile mengikut urutan kebergantungan |
| Fasa 3 | Pembangunan Frontend: Wireframe dilaksanakan sebagai Blade + Alpine.js |
| Fasa 4 | Pengujian: Unit test, integration test, UAT |
| Fasa 5 | Pelancaran: Nginx + PHP-FPM + MySQL 8 + Redis + SSL (Let's Encrypt) |

---

## SLIDE 9 — PENUTUP (Conclusion)

**Rumusan Masalah:**
- Tempahan berganda daripada pengesanan ketersediaan manual
- Pembayaran tidak berstruktur tanpa proses bayaran balik yang konsisten
- Penyerahan kunci fizikal yang memerlukan kehadiran pengendali
- Komunikasi tersebar di chat peribadi tanpa kaitan kepada tempahan

**Hasil yang dijangka dan kesimpulan:**

| Fasa | Hasil |
|---|---|
| PSM I (Selesai) | Spesifikasi keperluan lengkap (URS + PRD) |
| | Reka bentuk sistem penuh: seni bina, ER diagram (17 entiti), wireframe UI |
| | 47 use cases merentasi 12 modul didokumentasikan |
| | Metodologi hibrid Waterfall + Agile dipilih dan dijustifikasikan |
| | Stack teknologi dinilai dan dipilih |
| PSM II (Akan datang) | Pembangunan, pengujian, dan pelancaran HomeLodge |

**Harapan:**
HomeLodge akan menggantikan kaedah manual dengan platform digital yang lengkap untuk pengurusan tempahan homestay.

---

## SLIDE 10 — TERIMA KASIH / SESI SOAL-JAWAB

**HomeLodge**
Sistem Pengurusan Tempahan Homestay Berasaskan Web

Terima kasih atas perhatian anda.

**Sesi Soal-Jawab**

---
*Aisyah Umairah binti Azmir | Dr. Zalmiyah binti Zakaria | Jabatan Kejuruteraan Perisian | PSM I — 2025/2026*
