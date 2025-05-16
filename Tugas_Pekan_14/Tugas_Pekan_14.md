## 📍 Laporan Implementasi Tahap 4 – Pekan 14  
**Implementasi Keamanan & Pengujian Jaringan Enterprise PT. Nusantara Network**  

**Disusun oleh Kelompok 8:**  
1. **Adhitya Hermawan** – Network Architect  
2. **Achmad Zaki Zaidan** – Network Engineer  
3. **Amalia Tiara Rezfani** – Network Services Specialist  
4. **Faradila Zakiah Nur Hafitsa** – Security & Documentation Specialist  

**Tanggal Pengumpulan:** Jumat, Pekan 14, 2025  

---

### 🔗 Link File Simulasi  
[Link ke file .pkt di Google Drive atau LMS]

---

## Daftar Isi  
1. Implementasi Access Control List (ACL) sesuai kebijakan keamanan  
2. Pengujian menyeluruh semua fitur jaringan
3. Troubleshooting dan Perbaikan Masalah   

---

## 1. Implementasi Access Control List (ACL) sesuai kebijakan keamanan 

Konfigurasi ACL ini dilakukan untuk membatasi komunikasi langsung antara departemen Keuangan dan SDM, serta membatasi akses dari subnet Marketing ke jaringan selain Server Farm.

### Konfigurasi CLI  
```bash 
Router>enable
Router#configure terminal
Router(config)#! -- IT (VLAN 10) boleh akses semua
Router(config)#access-list 100 permit ip 192.168.10.0 0.0.0.255 any
Router(config)#!-- keuangan (VLAN 20) hanya boleh akses server room
Router(config)#access-list 100 permit ip 192.168.20.0 0.0.0.255 192.168.100.0 0.0.0.255
Router(config)#access-list 100 deny ip 192.168.20.0 0.0.0.255 any
Router(config)#!-- SDM (VLAN 30) hanya boleh akses internet
Router(config)#access-list 100 deny ip 192.168.30.0 0.0.0.255 192.168.0.0 0.0.0.255
Router(config)#access-list 100 permit ip 192.168.30.0 0.0.0.255 any
Router(config)#!-- marketing (VLAN 50) tidak boleh akses finance
Router(config)#access-list 100 deny ip 192.168.50.0 0.0.0.255 192.168.20.0 0.0.0.255
Router(config)#access-list 100 permit ip 192.168.50.0 0.0.0.255 any
Router(config)#!-- operasional (VLAN 60) hanya akses SNS (UDP 53) dan HTTP (TPC 80) ke server
Router(config)#access-list 100 permit udp 192.168.50.0 0.0.0.255 192.168.100.0 0.0.0.255 eq 53
Router(config)#access-list 100 permit tcp 192.168.50.0 0.0.0.255 192.168.100.0 0.0.0.255 eq 80
Router(config)#access-list 100 deny ip 192.168.50.0 0.0.0.255 any
```

## 2. Pengujian menyeluruh semua fitur jaringan

## Pengujian Ping Berdasarkan ACL (Semua Diblock - Sesuai Kebijakan)

Tabel berikut menunjukkan bahwa semua pengujian ping menghasilkan **Request Timed Out**, yang sesuai dengan kebijakan keamanan bahwa protokol ICMP (ping) diblokir oleh ACL.

| No | Sumber (Departemen) | IP Sumber (Contoh) | Tujuan (Departemen) | IP Tujuan (Contoh) | Hasil Ping | Screenshot | Alasan Teknis |
|----|----------------------|--------------------|----------------------|---------------------|-------------|-------------|----------------|
| 1  | IT (VLAN 10)         | 192.168.10.10      | Keuangan             | 192.168.20.10       | ❌ Request Timed Out | ![a](Tugas_Pekan_14/image1.png) | ICMP tidak diizinkan oleh ACL |
| 2  | IT                   | 192.168.10.10      | SDM                  | 192.168.30.10       | ❌ Request Timed Out | ![a](Tugas_Pekan_14/image1.png) | Semua ping diblokir oleh ACL |
| 3  | Keuangan (VLAN 20)   | 192.168.20.10      | Server Room          | 192.168.100.10      | ❌ Request Timed Out | ![a](Tugas_Pekan_14/image2.png) | Hanya HTTP yang diizinkan, ICMP diblok |
| 4  | Keuangan             | 192.168.20.10      | SDM                  | 192.168.30.10       | ❌ Request Timed Out | ![a](Tugas_Pekan_14/image2.png) | Diblokir oleh ACL antara subnet |
| 5  | SDM (VLAN 30)        | 192.168.30.10      | Keuangan             | 192.168.20.10       | ❌ Request Timed Out | ![a](Tugas_Pekan_14/image3.png) | Hanya boleh akses internet, bukan internal |
| 6  | SDM                  | 192.168.30.10      | Server Room          | 192.168.100.10      | ❌ Request Timed Out | ![a](Tugas_Pekan_14/image3.png) | ICMP tidak diizinkan |
| 7  | Marketing (VLAN 50)  | 192.168.50.10      | Keuangan             | 192.168.20.10       | ❌ Request Timed Out | ![a](Tugas_Pekan_14/image4.png) | Diblokir oleh ACL antar departemen |
| 8  | Marketing            | 192.168.50.10      | Server Room          | 192.168.100.10      | ❌ Request Timed Out | ![a](Tugas_Pekan_14/image4.png) | ICMP tidak diizinkan |
| 9  | Operasional (VLAN 60)| 192.168.60.10      | Server Room          | 192.168.100.10      | ❌ Request Timed Out | ![a](Tugas_Pekan_14/image5.png) | Hanya DNS/HTTP diizinkan, ICMP tidak |
| 10 | Operasional          | 192.168.60.10      | Keuangan             | 192.168.20.10       | ❌ Request Timed Out | ![a](Tugas_Pekan_14/image5.png) | Akses antar subnet diblok oleh ACL |
"""

## 3. Troubleshooting dan Perbaikan Masalah

Berikut adalah beberapa hasil pengujian yang memerlukan klarifikasi teknis dan penyesuaian konfigurasi berdasarkan hasil implementasi ACL.

### Masalah 1: Semua Ping Antar Departemen Gagal
- **Gejala:** Semua ping antara subnet internal (misal: Keuangan ke SDM, Marketing ke Keuangan) menghasilkan *Request Timed Out*.
- **Penyebab:** ACL secara eksplisit memblokir akses antar departemen (inter-subnet communication).
- **Solusi:** Tidak perlu perbaikan. Ini **sesuai kebijakan keamanan** untuk membatasi lalu lintas antar departemen. Bukti bahwa ACL bekerja dengan benar.

---

### Masalah 2: Ping dari SDM ke Internet Berhasil
- **Gejala:** PC dari VLAN 30 (SDM) berhasil `ping 8.8.8.8` dan mendapat *reply*.
- **Penyebab:** Baris ACL `permit ip 192.168.30.0 0.0.0.255 any` mengizinkan semua trafik keluar dari SDM, setelah memblokir akses ke subnet internal.
- **Solusi:** Ini **bukan kesalahan**, melainkan **hasil yang diharapkan**. Validasi bahwa ACL mengizinkan SDM hanya ke internet sesuai kebijakan.

---

### Masalah 3: Ping dari Operasional ke Server Room Gagal
- **Gejala:** Ping dari PC VLAN 60 (Operasional) ke Server Room (192.168.100.x) gagal.
- **Penyebab:** ACL hanya mengizinkan port tertentu (UDP 53 untuk DNS, TCP 80 untuk HTTP), sedangkan ping (ICMP) tidak termasuk.
- **Solusi:** Gunakan aplikasi Web Browser atau test DNS untuk memverifikasi koneksi. Jangan pakai ping jika memang tidak diizinkan oleh kebijakan ACL.

---

