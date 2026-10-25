# Attack Plan — Fase 2 (SOC Engagement)

## Skenario Serangan

### 1. Reconnaissance — Port/Service Scanning

|---|---|
| **Kategori** | Reconnaissance |
| **Teknik** | Port/service scanning |
| **Tools** | nmap |
| **Target** | Alamat IP target|
| **Tujuan** | Mengintai seluruh port yang terbuka terbuka, serta melihat versi layanan dan OS |

### 2. Web Application — SQL Injection

|---|---|
| **Kategori** | Web Application (Web/API/Protokol IoT) |
| **Teknik** | SQL Injection pada parameter input yang tidak divalidasi |
| **Tools** | Burp Suite/manual payload |
| **Target** | Alamat web dashboard |
| **Tujuan** | Menyusup lewat celah input yang lengah untuk membongkar isi database, dan mengambil atau menampilkan data yang seharusnya tidak boleh terlihat |

### 3. Authentication — SSH Brute Force

|---|---|
| **Kategori** | Authentication |
| **Teknik** | Brute-force / credential stuffing pada login SSH |
| **Tools** | Hydra (+ wordlist rockyou.txt) |
| **Target** | Port SSH target |
| **Tujuan** | Menebak kredensial secara berulang, sampai salah satu kombinasi berhasil menembus |

### 4. Web/API/Protokol IoT — MQTT Spoofing

|---|---|
| **Kategori** | Web/API/Protokol IoT |
| **Teknik** | Publish data palsu ke topic MQTT tanpa autentikasi |
| **Tools** | mosquitto_pub / MQTT Explorer |
| **Target** | Port MQTT targert |
| **Tujuan** | Mengirim data sensor palsu ke broker |
