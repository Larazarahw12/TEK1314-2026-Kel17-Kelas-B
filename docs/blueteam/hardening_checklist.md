# Checklist Hardening Target Node 

---

##  Ringkasan Target Node

* **Nama Host / VM:** `SIMONAT` (`labvm`)
* **Sistem Operasi:** Ubuntu 22.04 LTS
* **IP Address:** `192.168.17.5/24` (Static)
* **Layanan Aktif (Whitelisted):**
  * `2222/tcp` — SSH Management
  * `80/tcp` — Web Dashboard SIMONAT (Apache)
  * `1883/tcp` — MQTT Broker (IoT Communication)

---

## Matriks Ringkasan Kontrol Hardening

| ID | Kategori | Deskripsi Kontrol | Status | Bukti Sebelum / Sesudah |
| :--- | :--- | :--- | :---: | :--- |
| **A-1**  | Access & Auth | Nonaktifkan Direct Root Login via SSH | **SELESAI** | `docs/blueteam/evidence/katA_root_ssh_before.png`<br>`docs/blueteam/evidence/katA_root_ssh_after.png` |
| **A-2** | Access & Auth | Migrasi Port SSH Default (`22` $\rightarrow$ `2222`) | **SELESAI** | `docs/blueteam/evidence/katA_ssh_port_config.png` |
| **A-3** | Access & Auth | Implementasi & Pengaktifan Fail2ban (Brute-Force Shield) | **SELESAI** | `docs/blueteam/evidence/katA_fail2ban_status.png`<br>`docs/blueteam/evidence/katA_failed_login_block.png` |
| **B-1** | Service & Attack Surface | Aktivasi UFW Firewall (*Default Deny Incoming*) | **SELESAI** | `docs/blueteam/evidence/katB_nmap_before.png`<br>`docs/blueteam/evidence/katB_nmap_after.png` |
| **B-2** | Service & Attack Surface | OS Patching & Update Kernel Ubuntu | **SELESAI** | `docs/blueteam/evidence/katB_os_patching.png` |
| **C-1** | Data & Application | Menyembunyikan Identitas & Versi Web Server (Banner Hiding) | **BELUM** | `docs/blueteam/evidence/katC_apache_banner_before.png`<br>`docs/blueteam/evidence/katC_apache_banner_after.png`<br>`docs/blueteam/evidence/katC_directory_browsing.png` |
| **C-2** | Data & Application | Penguatan Layanan & Hak Akses Aplikasi Web (Service Hardening) | **BELUM** | `docs/blueteam/evidence/katC_disabled_modules.png`<br>`docs/blueteam/evidence/katC_web_permissions.png`<br>`docs/blueteam/evidence/katC_default_files_removed.png` |

---

## Rincian Implementasi & Bukti Eksperimen

### Kategori A: Access & Authentication

#### A-01: Nonaktifkan Direct Root Login via SSH
* **Tujuan:** Mencegah peretas melakukan *brute-force* langsung terhadap akun penguasa tertinggi (`root`).
* **Konfigurasi (`/etc/ssh/sshd_config`):**
  ```bash
  PermitRootLogin no
  ```
* **Bukti:**
  * Config BEFORE/AFTER: `docs/blueteam/evidence/katA_root_ssh_before.png` & `katA_root_ssh_after.png`
  * Hasil Percobaan Gagal Login Root: `docs/blueteam/evidence/katA_root_ssh_denied.png`

#### A-02: Ubah Port Default SSH
* **Tujuan:** Mengurangi *noise* pemindaian otomatis (*botnet scanning*) pada port standar `22`.
* **Konfigurasi (`/etc/ssh/sshd_config`):**
  ```bash
  Port 2222
  ```
* **Bukti:**
  * Tangkapan layar status port aktif: `docs/blueteam/evidence/katA_ssh_port_config.png`

#### A-03: Proteksi Fail2ban Terhadap Brute-Force
* **Tujuan:** Mengisolasi dan memblokir IP penyerang secara otomatis jika terjadi kegagalan autentikasi berulang.
* **Konfigurasi (`/etc/fail2ban/jail.local`):**
  ```ini
  [sshd]
  enabled = true
  port = 2222
  maxretry = 3
  bantime = 3600
  ```
* **Bukti:**
  * Status layanan Fail2ban: `docs/blueteam/evidence/katA_fail2ban_status.png`
  * Log pemblokiran IP penyerang setelah 3x gagal login: `docs/blueteam/evidence/katA_failed_login_block.png`

---

### Kategori B: Service & Attack Surface

#### B-01: Pembatasan Port via Firewall (UFW)
* **Tujuan:** Menutup seluruh pintu akses yang tidak diperlukan (*Default Deny*) dan hanya membuka port minimal yang valid.
* **Perintah Eksekusi:**
  ```bash
  sudo ufw default deny incoming
  sudo ufw allow 2222/tcp
  sudo ufw allow 80/tcp
  sudo ufw allow 1883/tcp
  sudo ufw enable
  ```
* **Bukti Audit Nmap (BEFORE vs AFTER):**
  * **BEFORE:** Scanning menunjukkan port terbuka tanpa batasan. (`docs/blueteam/evidence/katB_nmap_before.png`)
  * **AFTER:** Output Nmap menunjukkan port tertutup sepenuhnya kecuali `2222`, `80`, dan `1883`. (`docs/blueteam/evidence/katB_nmap_after.png`)

#### B-02: System Update & Security Patching
* **Tujuan:** Menutup celah kerentanan *Kernel* dan *Package* tingkat OS (CVE mitigation).
* **Perintah Eksekusi:**
  ```bash
  sudo apt update && sudo apt dist-upgrade -y
  ```
* **Bukti:**
  * Output pembaruan OS & Kernel Ubuntu versi aman terbaru: `docs/blueteam/evidence/katB_os_patching.png`

---

### Kategori C: Data & Application Security

#### C-01: Menyembunyikan Identitas & Versi Web Server (Banner Hiding)
* **Tujuan:** Meminimalkan kebocoran informasi teknis server (*information disclosure*) agar penyerang tidak dapat mengidentifikasi versi spesifik Apache dan OS Ubuntu.
* **Langkah Konfigurasi:**
  1. **Header Response Apache:** Ubah parameter pada `/etc/apache2/conf-available/security.conf`:
     ```apache
     ServerTokens Prod
     ServerSignature Off
     ```
  2. **Nonaktifkan Directory Browsing:** Pastikan direktori web tidak dapat diintip dari browser jika file indeks tidak ada dengan menghapus atau mematikan opsi `Indexes` (`Options -Indexes`) pada konfigurasi situs Apache (`/etc/apache2/sites-available/000-default.conf`).
* **Bukti SS (Tambahkan oleh rekan tim):**
  * Respon `curl -I http://localhost` BEFORE (`Apache/2.4.52 (Ubuntu)`): `docs/blueteam/evidence/katC_apache_banner_before.png`
  * Respon `curl -I http://localhost` AFTER (`Server: Apache`): `docs/blueteam/evidence/katC_apache_banner_after.png`
  * Bukti penolakan Directory Browsing (HTTP 403 Forbidden): `docs/blueteam/evidence/katC_directory_browsing.png`

#### C-02: Penguatan Layanan & Hak Akses Aplikasi Web (Service Hardening)
* **Tujuan:** Mengurangi *attack surface* aplikasi web dan mengunci file sensitif agar hanya bisa diakses oleh proses web server resmi.
* **Langkah Konfigurasi:**
  1. **Matikan Modul Tidak Terpakai:** Menonaktifkan modul bawaan Apache yang berisiko membocorkan status server (seperti `mod_status` atau `mod_info`):
     ```bash
     sudo a2dismod status info
     ```
  2. **Pengerasan Hak Akses Berkas Web:** Mengatur kepemilikan dan izin akses direktori `/var/www/html/` atau folder SIMONAT:
     ```bash
     sudo chown -R www-data:www-data /var/www/html/
     sudo find /var/www/html/ -type d -exec chmod 755 {} \;
     sudo find /var/www/html/ -type f -exec chmod 644 {} \;
     # Berkas konfigurasi sensitif (koneksi DB/secrets):
     sudo chmod 600 /var/www/html/config.php
     ```
  3. **Hapus File Bawaan/Contoh:** Menghapus halaman bawaan atau file pengujian yang tidak diperlukan di lingkungan produksi:
     ```bash
     sudo rm /var/www/html/index.html /var/www/html/info.php
     ```
* **Bukti SS (Tambahkan oleh rekan tim):**
  * Bukti modul nonaktif (`a2dismod`): `docs/blueteam/evidence/katC_disabled_modules.png`
  * Bukti hak akses direktori & file (`ls -la /var/www/html/`): `docs/blueteam/evidence/katC_web_permissions.png`
  * Bukti penghapusan file contoh/bawaan: `docs/blueteam/evidence/katC_default_files_removed.png`

#### C-03: Penguatan Manajemen Log & Auditing
* **Tujuan:** Memastikan seluruh aktivitas lalu lintas web tercatat dengan rapi, sistem log terpelihara dari kepenuhan disk, serta siap diintegrasikan untuk analisis SIEM/Security Onion.
* **Langkah Konfigurasi:**
  1. **Verifikasi Pencatatan Log Web:** Memastikan setiap transaksi HTTP/HTTPS terekam pada `/var/log/apache2/access.log` (akses masuk) dan `/var/log/apache2/error.log` (pesan kesalahan).
  2. **Rotasi Log (Logrotate):** Memeriksa dan memastikan konfigurasi `logrotate` aktif untuk log Apache (`/etc/logrotate.d/apache2`) dan log autentikasi (`/var/log/auth.log`) agar penyimpanan VM tidak habis.
  3. **Uji Analisis Log Real-Time:** Melakukan simulasi pengujian akses web sederhana untuk membuktikan bahwa alamat IP pengakses, *user-agent*, dan *status code* HTTP tercatat secara *real-time*:
     ```bash
     tail -f /var/log/apache2/access.log
     ```
