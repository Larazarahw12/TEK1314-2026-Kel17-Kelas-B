# Proyek PBL Keamanan Siber - Desain Arsitektur Jaringan

Repositori ini berisi rancangan arsitektur jaringan dan skema IP untuk Proyek PBL Keamanan Siber (Tahap Desain).

## 1. Topologi Jaringan
Topologi menggunakan arsitektur *flat network* tunggal dalam segmen subnet **`192.168.17.0/24`**. Semua node terhubung ke satu *switch* pusat untuk memudahkan komunikasi dan *monitoring*.

## 2. Skema IP (IP Plan)
Detail alokasi IP dapat dilihat secara lengkap pada file `ip_plan.md`.

## 3. Skenario & Cara Kerja Arsitektur

Arsitektur ini mensimulasikan lingkungan penyerangan dan pertahanan dengan 3 komponen utama:

*   **Attacker-Node (Kali Linux - `192.168.17.100`)**
    Berfungsi sebagai mesin penyerang (Red Team). Mesin ini akan digunakan untuk melakukan *scanning* port, mencari celah keamanan, dan mengeksekusi *exploit* ke arah server target.
    
*   **Target-Node (Metasploitable - `192.168.17.5`)**
    Berfungsi sebagai server korban. Menggunakan Metasploitable karena OS ini memang didesain rentan dan memiliki banyak port/service terbuka untuk sarana edukasi. Ini akan menjadi sasaran eksploitasi dari *Attacker*.

*   **Monitoring-SecurityOnion (`192.168.17.200`)**
    Berfungsi sebagai sistem pemantau (Blue Team). Terhubung ke jaringan yang sama untuk merekam dan menganalisis *traffic* jaringan antara *Attacker* dan *Target*. Sistem ini bertugas mendeteksi log serangan atau aktivitas anomali yang terjadi.
