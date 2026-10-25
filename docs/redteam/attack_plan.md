# Attack Plan — Fase 2 (SOC Engagement)

## Skenario Serangan

### 1. Reconnaissance — Port/Service Scanning

| | |
|---|---|
| **Kategori** | Reconnaissance |
| **Teknik** | Port/service scanning |
| **Tools** | nmap |
| **Target** | Target Node (192.168.17.5) |
| **Tujuan** | Memetakan port terbuka sebagai dasar penentuan skenario eksploitasi berikutnya. |
| **Relevansi tech stack** | Scan awal ini yang menjadi dasar riset Minggu ke-4, menunjukkan permukaan serangan Target Node sebelum dan sesudah hardening |

### 2. Web Application — SQL Injection

| | |
|---|---|
| **Kategori** | Web Application (Web/API/Protokol IoT) |
| **Teknik** | SQL Injection pada parameter input yang tidak divalidasi |
| **Tools** | Burp Suite / manual payload (SQLMap opsional) |
| **Target** | Alamat IP Web Dashboard |
| **Tujuan** | Menguji apakah input pada endpoint dashboard dapat memanipulasi query database untuk mengambil atau menampilkan data di luar yang seharusnya |
| **Relevansi tech stack** | Dashboard IoT kelompok dibangun dengan PHP + MySQL |

### 3. Authentication — SSH Brute Force

| | |
|---|---|
| **Kategori** | Authentication |
| **Teknik** | Brute-force / credential stuffing pada login SSH |
| **Tools** | Hydra (+ wordlist rockyou.txt) |
| **Target** | Port SSH target |
| **Tujuan** | Menguji apakah kredensial akun server dapat ditebak melalui percobaan login berulang |
| **Relevansi tech stack** | Target Node diakses dan dikelola lewat SSH |

### 4. Web/API/Protokol IoT — MQTT Spoofing

| | |
|---|---|
| **Kategori** | Web/API/Protokol IoT |
| **Teknik** | Publish data palsu ke topic MQTT tanpa autentikasi |
| **Tools** | mosquitto_pub / MQTT Explorer |
| **Target** | Port MQTT target |
| **Tujuan** | Mengirim data sensor palsu ke broker untuk mensimulasikan aktor yang ingin mengacaukan data operasional sistem IoT |
| **Relevansi tech stack** | Komunikasi sensor-ke-server proyek memakai protokol MQTT |
