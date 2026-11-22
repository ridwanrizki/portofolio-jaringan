# Dokumentasi Infrastruktur Jaringan Lokal & Sistem Pengawasan

Dokumentasi ini memaparkan rancangan arsitektur jaringan, pembagian perangkat, serta studi kasus *troubleshooting* pada infrastruktur jaringan lokal berbasis **MikroTik RouterOS**.

## 1. Diagram Topologi Jaringan

![Topologi Jaringan](topologi-jaringan.png)

## 2. Spesifikasi & Peran Perangkat

| Perangkat | Jumlah | Tipe Koneksi | Fungsi  |
| :--- | :---: | :--- | :--- |
| **ONT ISP** | 1 Unit | Fiber Optic / LAN | Terminasi jalur internet dari penyedia layanan internet. |
| **MikroTik Router (PC x86)** | 1 Unit | Kabel LAN | *Gateway* utama, DHCP Server, NAT, Firewall, dan manajemen *bandwidth* (QoS). |
| **Unmanaged Switch** | 2 Unit | Kabel LAN | Distribusi koneksi LAN. |
| **Access Point (AP-1 – AP-8)** | 8 Unit | Kabel LAN | Menyediakan akses koneksi WIFI |
| **DVR CCTV (16-Channel)** | 1 Unit | Kabel LAN | Sistem perekaman video 24/7 dan pemantauan kamera keamanan lokal/jarak jauh. |
| **Smart TV (TV-1 – TV-4)** | 4 Unit | Kabel LAN | Perangkat pengguna |
| **Desktop PC** | 1 Unit | Kabel LAN | Komputer kerja utama dan pusat administrasi jaringan (WinBox). |
| **Wireless Clients** | Multi-user | Wi-Fi (DHCP) | Perangkat *smartphone* dan laptop yang terhubung melalui *access point* `AP-1` hingga `AP-8`. |

## 3. Arsitektur & Manajemen Trafik

- **MikroTik Sebagai Pusat Kendali** — Seluruh manajemen IP (DHCP), *Static Lease* (DVR & AP), NAT, dan aturan keamanan dipusatkan pada MikroTik karena kedua *switch* bersifat *unmanaged*.
- **Pemisahan Jalur Distribusi** — `Switch-1` menangani perangkat tetap (`Smart TV 1–4`, `PC`, `DVR`, `AP-1`), sedangkan `Switch-2` dikhususkan menyuplai jalur *uplink* kabel untuk `AP-2` hingga `AP-8`.
- **Manajemen Bandwidth (QoS)** — Penerapan distribusi trafik secara adil di MikroTik agar setiap pengguna dan perangkat mendapatkan koneksi internet yang stabil serta merata tanpa saling membebani.

## 4. Studi Kasus *Troubleshooting*
<details>
<summary><b>1. Mengatasi Konflik IP Lokal pada Access Point Saat DHCP Lease Habis</b></summary>

- **Gejala:** Setiap kali *DHCP lease time* dari MikroTik habis, salah satu Access Point mengalami error dan justru membagikan IP lokalnya sendiri ke *client*, sehingga perangkat yang terhubung kehilangan akses internet.
- **Analisis Penyebab:** Setelah memeriksa log pada Access Point tersebut, ditemukan catatan aktivitas **BOOTP** saat proses pembaruan IP berlangsung. Respons BOOTP yang aktif pada DHCP Server MikroTik memicu kegagalan *renewal* di sisi AP, sehingga AP beralih mengaktifkan DHCP bawaannya sendiri.
- **Solusi:** Menonaktifkan fitur **BOOTP** (*BOOTP Support: none*) pada pengaturan DHCP Server di MikroTik.
- **Hasil:** Proses pembaruan *DHCP lease* berjalan lancar tanpa memicu DHCP internal Access Point, dan distribusi IP dari MikroTik ke seluruh *client* kembali stabil.

</details>
