RTSP Backup Manager
====================

Deskripsi
---------
RTSP Backup Manager adalah sistem yang digunakan untuk mengambil stream RTSP dari NVR/DVR secara otomatis dan menyimpannya dalam struktur folder terorganisir. Sistem ini dilengkapi dengan fitur:
1. Penyesuaian zona waktu dinamis (Wita/Wib).
2. Struktur folder berdasarkan tanggal dan nomor channel.
3. Log terstruktur untuk memantau status backup.
4. Pembersihan otomatis folder backup tertua jika kapasitas HDD penuh.

Struktur Direktori
------------------
### Lokasi Root Proyek
```
/home/abdullah/rtsp-backup
├── data/                     # Folder untuk menyimpan file backup (mount point container)
├── logs/                     # Folder untuk menyimpan file log
│   ├── backup.log            # Log aktivitas backup
│   ├── hdd_monitor.log       # Log monitor HDD
├── scripts/                  # Folder berisi script utama
│   ├── backup_manager.py     # Script untuk backup RTSP
│   ├── entrypoint.sh         # Script untuk menjalankan container
├── docker-compose.yml        # File untuk menjalankan aplikasi menggunakan Docker Compose
├── Dockerfile                # File konfigurasi Docker
├── .env                      # File konfigurasi lingkungan
```

Konfigurasi
-----------
### File `.env`
Berikut adalah contoh konfigurasi `.env`:
```
# Pilih zona waktu sesuai lokasi:
# Untuk Jakarta:
TIMEZONE=Asia/Jakarta
# Untuk Makassar:
# TIMEZONE=Asia/Makassar

# Detail Login untuk RTSP:
RTSP_USERNAME=babahdigital          # Username untuk autentikasi RTSP
RTSP_PASSWORD=Admin123@             # Password untuk autentikasi RTSP

# IP RTSP NVR atau DVR:
RTSP_IP=172.16.10.252               # IP dari perangkat RTSP Anda

# Jumlah channel RTSP yang ingin di-backup:
CHANNELS=8                          # Contoh: 8 channel

# Folder output RTSP (container Docker):
RTSP_OUTPUT_DIR=/data               # Lokasi penyimpanan hasil backup di dalam container

# Folder TrueNAS (host):
BACKUP_DIR=/mnt/Backup/             # Lokasi backup di sistem host (TrueNAS)
```

Fitur
-----
1. **Struktur Folder Backup**
   - **Format Folder**: `/mnt/Backup/<Tanggal>/Channel <Nomor Channel>`
   - **Format File**: `<Jam-Menit-Detik>.mp4`
   - Contoh:
     ```
     /mnt/Backup/21-12-2024/Channel 1/08-15-30.mp4
     /mnt/Backup/21-12-2024/Channel 2/08-15-30.mp4
     ```

2. **Log Backup**
   - Format log mencakup waktu lokal (Wita/Wib), level log, dan pesan.
   - Contoh log:
     ```
     21-12-2024 08:15:00 Wita - INFO - Backup berhasil: /mnt/Backup/21-12-2024/Channel 1/08-15-30.mp4
     21-12-2024 08:16:00 Wita - ERROR - Backup gagal untuk channel 2. Exit status 1
     ```

3. **Pembersihan Otomatis**
   - Folder backup tertua akan dihapus jika penggunaan HDD melebihi 90%.

Langkah Instalasi
-----------------
### 1. Kloning Proyek
```
git clone https://github.com/babahdigital/rtsp-backup-manager.git
cd rtsp-backup-manager
```

### 2. Buat File `.env`
- Salin template dan sesuaikan:
```
cp .env.example .env
nano .env
```

### 3. Bangun dan Jalankan Container
- Dengan Docker Compose:
```
docker-compose up --build -d
```

Langkah Simulasi
----------------
1. Pastikan `.env` sudah dikonfigurasi.
2. Jalankan simulasi backup untuk channel tertentu:
   ```
   python3 scripts/backup_manager.py --channel 1
   ```
3. Periksa log di:
   ```
   /home/abdullah/rtsp-backup/logs/backup.log
   ```

Troubleshooting
---------------
### 1. Backup Gagal
- Periksa log di `logs/backup.log` untuk melihat pesan error.
- Pastikan URL RTSP, username, dan password sudah benar.

### 2. HDD Penuh
- Sistem akan otomatis menghapus folder tertua.
- Periksa log di `logs/hdd_monitor.log` untuk riwayat pembersihan.

### 3. Tidak Ada File di Folder Backup
- Pastikan konfigurasi `.env` sudah benar.
- Jalankan perintah berikut untuk memeriksa:
   ```
   docker logs <container_id>
   ```

Pengembangan dan Kontribusi
---------------------------
1. Untuk menambahkan fitur baru, pastikan mengikuti struktur proyek yang sudah ada.
2. Dokumentasi setiap perubahan wajib diperbarui di file `README.md`.

Jika ada pertanyaan lebih lanjut, silakan hubungi tim pengembang di GitHub atau melalui email resmi.
