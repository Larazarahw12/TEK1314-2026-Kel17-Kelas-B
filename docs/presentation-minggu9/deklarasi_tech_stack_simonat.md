# Deklarasi Tech Stack — Proyek SIMONAT (Sistem Monitoring Pakan Ikan Otomatis)
*Dibuat untuk memenuhi tugas praktikum keamanan siber (Blue Team).*

Berikut adalah tabel deklarasi *tech stack* resmi yang disesuaikan dengan implementasi nyata pada proyek IoT SIMONAT yang telah dibangun di dalam lingkungan VirtualBox (Ubuntu VM):

| Komponen | Teknologi pada Proyek IoT Asli | Direplikasi sebagai (Target Node) |
| :--- | :--- | :--- |
| **Sistem Operasi backend** | Linux (Ubuntu Server / Debian) | Ubuntu 22.04 LTS (NetAcad CSE Lab VM di VirtualBox) |
| **Database/penyimpanan data** | MySQL / MariaDB | MySQL Server dengan user khusus `admin_simonat` & database `db_iot_monitoring` |
| **Protokol komunikasi sensor** | MQTT (Message Queuing Telemetry Transport) | Mosquitto MQTT Broker (`localhost:1883`) dengan jembatan Python (*bridge.py*) |
| **Dashboard/monitoring** | Web Dashboard Custom (PHP + Bootstrap) / Grafana | Web Dashboard PHP lokal (`http://localhost/index.php`) dengan *auto-refresh* data sensor |

---

## Catatan Arsitektur Sistem (Blue Team Notes)
1. **Penyimpanan Data:** Seluruh parameter sensor kolam ikan (suhu air, pH, TDS, dan status pakan) dikumpulkan secara *real-time* dan disimpan ke dalam tabel `simonat_logs` pada database MySQL.
2. **Jalur Komunikasi:** Perangkat IoT mengirimkan data melalui protokol MQTT, yang kemudian diteruskan oleh skrip penghubung (*bridge*) ke backend PHP (`simpan_data.php`) via metode HTTP POST.
3. **Pemantauan:** Tim pertahanan (*Blue Team*) memantau anomali dan aktivitas log melalui halaman dashboard web lokal yang terhubung langsung ke basis data.