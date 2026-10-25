# Attack Plan - Fase 2 

## Skenario Serangan

| No | Kategori | Teknik | Tools | Target | Tujuan |
|---|---|---|---|---|---|
| 1 | Reconnaissance | Port/service scanning | nmap | Alamat IP target | Mengintai seluruh port yang terbuka, serta melihat versi service dan OS |
| 2 | Web Application (Web/API/Protokol IoT) | SQL Injection pada parameter input yang tidak divalidasi | Burp Suite / manual payload | Alamat web dashboard | Menyusup melalui celah input untuk membongkar isi database serta mengambil atau menampilkan data yang seharusnya tidak boleh terlihat |
| 3 | Authentication | Brute-force / credential stuffing pada login SSH | Hydra (+ wordlist rockyou.txt) | Port SSH target | Menebak kredensial secara berulang sampai salah satu kombinasi berhasil |
| 4 | Web/API/Protokol IoT | Publish data palsu ke topic MQTT tanpa autentikasi | mosquitto_pub / MQTT Explorer | Port MQTT target | Menyamar jadi sensor dan menyusupkan data palsu ke broker, mengacaukan kepercayaan sistem terhadap data operasional yang sebenarnya |
