# Internal Tools — Kerja Praktik Telkom

Aplikasi internal untuk mengelola registry aset dan data operasional, dibuat waktu Kerja Praktik di Telkom Indonesia. Dibangun pakai Lowcoder (low-code) dengan database PostgreSQL, dan sudah dibungkus Docker biar gampang dijalankan di komputer mana saja.

Fokus utamanya bukan cuma CRUD biasa, tapi kontrol akses: data sensitif (kredensial, kunci akses) hanya bisa dilihat oleh peran yang berhak.

## Fitur

- Registry produk/aset dengan operasi CRUD lengkap
- Kontrol akses berbasis peran: Admin, PO, dan Viewer
- Data rahasia otomatis disembunyikan dari peran yang tidak berhak
- Data dummy terisi otomatis saat pertama dijalankan (auto-seeding)

## Teknologi

Lowcoder CE, PostgreSQL 15, Docker Compose.

## Prasyarat

Sebelum menjalankan, pastikan:

1. **Docker Desktop** sudah terinstall.
2. **Alokasi RAM Docker minimal 4GB.** Lowcoder butuh resource Java dan database yang lumayan besar. Kalau RAM kurang, aplikasi bisa gagal dengan error 502 Bad Gateway.

   Khusus Windows (WSL2), buat file `.wslconfig` di folder user (`C:\Users\NamaUser\.wslconfig`) dengan isi:
```ini
   [wsl2]
   memory=4GB
```

## Cara Menjalankan

### 1. Nyalakan server

Buka terminal di dalam folder project ini, lalu jalankan:

```bash
docker-compose up -d
```

Tunggu sekitar 3 sampai 5 menit setelah container nyala. Service backend (Java) butuh waktu buat booting sepenuhnya.

### 2. Buat akun admin

Buka browser ke `http://localhost:3000`.

Ini instalasi bersih, jadi belum ada user. Klik **Sign Up** untuk daftar akun baru.

Pakai email di bawah ini supaya otomatis terdeteksi sebagai Super Admin (sesuai logic RBAC):

- Email: `yosiaparadesinaga@gmail.com`
- Password: bebas

### 3. Import aplikasinya

Tampilan aplikasi (UI) tersimpan dalam bentuk file JSON, jadi perlu di-load dulu:

1. Setelah login, masuk ke Dashboard.
2. Klik **Create App**, lalu pilih **Import**.
3. Pilih file `app-backup/Lowcoder_App_Export.json`.

Selesai. Aplikasi siap dipakai dan data dummy sudah terisi.

## Informasi Database

PostgreSQL sudah dikonfigurasi otomatis (auto-seeding) saat Docker jalan.

| Keterangan | Nilai |
|---|---|
| Host | `db` (dari dalam Docker) atau `localhost` (dari luar) |
| Port | `5432` |
| Username | `lowcoder` |
| Password | `password123` |
| Database | `lowcoder_db` |

Catatan: password di atas hanya untuk lingkungan lokal/development.

## Troubleshooting

**Error: "Bind for 0.0.0.0:3000 failed: port is already allocated"**
Port 3000 sedang dipakai aplikasi lain atau container lama yang belum mati.
1. Cek container yang jalan: `docker ps`
2. Matikan yang memblokir: `docker rm -f <ID_CONTAINER>`
3. Jalankan ulang: `docker-compose up -d`

**Error: 502 Bad Gateway atau layar putih terus**
Backend (Java) belum selesai loading, atau RAM kurang.
1. Tunggu 2 sampai 3 menit, lalu refresh browser.
2. Kalau masih error, naikkan RAM Docker jadi 4GB (lihat bagian Prasyarat).
3. Pastikan lewat log: `docker logs lowcoder_app`. Tunggu sampai muncul `Started LowcoderApplication`.

**Error: "User not found" saat login**
Ini fresh install, database user masih kosong. Jangan langsung login. Daftar dulu lewat **Sign Up** atau buka `http://localhost:3000/auth/register`.

**Tombol Edit/Hapus tidak muncul**
Kamu login pakai email yang tidak terdaftar di script RBAC. Login pakai `yosiaparadesinaga@gmail.com`, atau tambahkan email kamu ke list `allowedEmails` di dalam Query RBAC pada aplikasi Lowcoder.

## Catatan

Selain versi Lowcoder ini, saya juga membangun ulang aplikasinya dari nol pakai HTML/CSS/JavaScript untuk mengeksplorasi desain dan model RBAC yang lebih dalam (public/internal/restricted, audit log, dark mode). Versi itu terpisah dari repo ini.

## Dibuat oleh

Yosia Parade Banua Sinaga — Kerja Praktik 2025
