# Attack Plan - Fase 2 

## Skenario Serangan

| No | Kategori | Teknik | Tools | Target | Tujuan |
|---|---|---|---|---|---|
| 1 | Reconnaissance | Port/service scanning | nmap | Alamat IP target | Mengintai seluruh port yang terbuka, serta melihat versi service dan OS |
| 2 | Exploitation/Akses | Brute-force/credential stuffing pada login SSH | Hydra + wordlist rockyou.txt | Port SSH target | Menebak kredensial secara berulang sampai salah satu kombinasi berhasil |
| 3 | Web/API/Protokol IoT | Publish data palsu ke topic MQTT tanpa autentikasi | mosquitto_pub | Port MQTT target | Menyusupkan data palsu ke broker |
