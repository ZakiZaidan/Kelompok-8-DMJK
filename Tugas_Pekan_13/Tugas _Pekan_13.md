**Dokumen Desain Jaringan: Perancangan & Implementasi Jaringan Enterprise PT. Nusantara Network**

**Deliverable Pekan 13 Mata Kuliah Desain & Manajemen Jaringan Komputer**

**Disusun oleh Kelompok 8:**

1.  **Adhitya Hermawan** - Network Architect
2.  **Achmad Zaki Zaidan** - Network Engineer
3.  **Amalia Tiara Rezfani** - Network Services Specialist
4.  **Faradila Zakiah Nur Hafitsa** - Security & Documentation Specialist

**Tanggal Pengumpulan:** Jumat, [Pekan 13], [2025]

###  Link File Simulasi

 File simulasi Cisco Packet Tracer: [https://github.com/ZakiZaidan/Kelompok-8-DMJK/blob/7b1baecf877b26595a41a329da4ca754eee61d98/Tugas_Pekan_13/Topologi_UAS_Minggu13.pkt]

---

**Daftar Isi**
 1. Konfigurasi DHCP & DNS Server
 2. Hasil Penujian DHCP & DNS
 3. Konfigurasi dan Pengujian NAT
 4. Kesimpulan

### Pekan 13: Implementasi Layanan Jaringan

#### Tugas Kelompok

1. Konfigurasi DHCP Server untuk setiap departemen.
2. Implementasi DNS Server untuk resolusi nama internal.
3. Konfigurasi NAT untuk akses internet.


###  Link File Simulasi
 File simulasi Cisco Packet Tracer: \[Link ke file .pkt di Google Drive atau LMS]

### Konfigurasi CLI

### DHCP & DNS Configuration

Menggunakan Router 1 melalui CLI, pada konfigurasi ini dilakukan pengaturan layanan Dynamic Host Configuration Protocol (DHCP) dan Domain Name System (DNS) untuk mendukung komunikasi jaringan secara otomatis dan efisien.

```bash
Router>enable
Router#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
Router(config)#ip dhcp excluded-address 192.168.10.1 192.168.10.10
Router(config)#ip dhcp pool IT_POOL
Router(dhcp-config)#network 192.168.10.0 255.255.255.0
Router(dhcp-config)#default-router 192.168.10.1
Router(dhcp-config)#dns-server 8.8.8.8
Router(dhcp-config)#exit
Router(config)#ip dhcp excluded-address 192.168.20.1 192.168.20.10
Router(config)#ip dhcp pool KEUANGAN_POOL
Router(dhcp-config)#network 192.168.20.0 255.255.255.0
Router(dhcp-config)#default-router 192.168.20.1
Router(dhcp-config)#dns-server 8.8.8.8
Router(dhcp-config)#exit
Router(config)#ip dhcp excluded-address 192.168.30.1 192.168.30.10
Router(config)#ip dhcp pool SDM_POOL
Router(dhcp-config)#network 192.168.30.0 255.255.255.0
Router(dhcp-config)#default-router 192.168.30.1
Router(dhcp-config)#dns-server 8.8.8.8
Router(dhcp-config)#exit
Router(config)#ip dhcp excluded-address 192.168.40.1 192.168.40.10
Router(config)#ip dhcp pool SERVER_POOL
Router(dhcp-config)#network 192.168.40.0 255.255.255.0
Router(dhcp-config)#default-router 192.168.40.1
Router(dhcp-config)#dns-server 8.8.8.8
Router(dhcp-config)#exit
Router(config)#ip dhcp excluded-address 192.168.50.1 192.168.50.10
Router(config)#ip dhcp pool MARKETING_POOL
Router(dhcp-config)#network 192.168.50.0 255.255.255.0
Router(dhcp-config)#default-router 192.168.50.1
Router(dhcp-config)#dns-server 8.8.8.8
Router(dhcp-config)#exit
Router(config)#ip dhcp excluded-address 192.168.60.1 192.168.60.10
Router(config)#ip dhcp pool OPERASIONAL_POOL
Router(dhcp-config)#network 192.168.60.0 255.255.255.0
Router(dhcp-config)#default-router 192.168.60.1
Router(dhcp-config)#dns-server 8.8.8.8
Router(dhcp-config)#exit
Router(config)#exit
...
```
DHCP memastikan setiap perangkat mendapatkan alamat IP secara otomatis, lengkap dengan gateway dan subnet mask yang sesuai. Sementara itu, DNS digunakan untuk memudahkan komunikasi dengan perangkat lain melalui nama domain, menggunakan server DNS publik (8.8.8.8) untuk resolve alamat. Dengan konfigurasi ini, jaringan dapat berjalan lebih efisien tanpa perlu pengaturan IP manual pada setiap perangkat.

![alt text]("Image/image1.png")

**Gambar 1. Departemen IT & Departemen Keuangan**
![alt text]("Image/image2.png")
  
  Pada gambar tersebut kedua PC disetting menggunakan mode DHCP. dan terdapat status "DHCP request successful", artinya permintaan IP otomatis berhasil.
  - PC0 mendapat IP: 192.168.10.12, gateway: 192.168.10.1

  - PC50 mendapat IP: 192.168.20.12, gateway: 192.168.20.1

Kedua PC berhasil mendapatkan alamat IP dari server DHCP masing-masing subnet secara otomatis, yang menunjukkan bahwa konfigurasi DHCP sudah berjalan dengan baik.
Ini untuk memastikan setiap departemen (misalnya, IT dan Keuangan) berada di jaringan yang terisolasi sesuai subnet-nya, dan ini dapat dijadikan dasar pengaturan keamanan dan manajemen akses yang lebih lanjut.



**Gambar 2. Departemen SDM & Server Farm**

![alt text]("Image/image3.png")

Gambar menunjukkan bahwa perangkat PC75 dan Server0 berhasil memperoleh alamat IP secara otomatis dari DHCP server. Keduanya menampilkan status "DHCP request successful" yang menandakan bahwa permintaan DHCP berhasil dan perangkat telah mendapatkan:
   - Alamat IP sesuai pool DHCP masing-masing subnet,

   - Subnet mask yang sesuai (255.255.255.0),

   - Default Gateway yang tepat,

   - DNS Server yang telah diset ke 8.8.8.8.

Alokasi IP berhasil secara otomatis, baik untuk PC maupun server.
Ini dapat dimanfaatkan untuk pengujian komunikasi lintas subnet antar perangkat dan server, serta sebagai dasar konfigurasi routing atau access control di tahap selanjutnya.



### Gambar 3. Departemen Marketing & Departemen Operasional
![alt text]("Image/image4.png")

Pada gambar 4 ini juga memperlihatkan pengujian alokasi IP dinamis (DHCP) pada client, yang ditunjukkan oleh dua perangkat _PC108 mendapatkan IP 192.168.60.12_ dan _PC125 juga mendapatkan IP 192.168.60.12_



### Hasil Pengujian Konektivitas NAT

![alt text]("Image/image5.png")

**1. Konfigurasi pada Router1**

Pada screenshot sebelah kiri, terlihat perintah yang dijalankan _show ip nat translations_ yang dijalankan pada Router1. Output dari hasil tersebut adalah : 

* Inside Global: Alamat IP publik yang digunakan untuk menerjemahkan alamat lokal saat perangkat dalam jaringan internal mengirim paket ke jaringan eksternal. pada gambar tersebut
Inside Global (192.168.59.2) adalah alamat IP publik yang digunakan untuk mewakili perangkat ini saat berkomunikasi ke luar jaringan.

* Inside Local: Alamat IP privat dari perangkat dalam jaringan internal sebelum diterjemahkan oleh NAT. Pada gambar tersebut Inside Local (192.168.40.11) adalah alamat IP privat yang digunakan oleh perangkat dalam jaringan internal sebelum NAT.

* Outside Local / Outside Global: Alamat tujuan di luar jaringan internal.Pada gambar tersebut  Outside Global (192.168.77.2) adalah alamat tujuan dari perangkat eksternal yang diakses oleh perangkat internal.

```bash
Pro Inside global      Inside local      Outside local     Outside global
icmp 192.168.59.2      192.168.40.11     192.168.77.2      192.168.77.2
icmp 192.168.59.2      192.168.40.11     192.168.77.2      192.168.77.2
```
Fungsi utama dari tabel ini adalah untuk memastikan bahwa NAT berfungsi dengan benar, menerjemahkan alamat lokal ke global sehingga perangkat internal dapat mengakses jaringan eksternal.

**2. Pengujian Konektivitas pada PC40.**

Pada screenshot sebelah kanan, merupakan engujian konektivitas yang dilakukan dari PC40 antara lain : 

* Ping ke 192.168.59.2 ,Menunjukkan koneksi berhasil dengan 0% packet loss, menandakan jalur NAT berfungsi dengan baik.

* Ping ke 192.168.40.11, Mengonfirmasi bahwa perangkat d* apat saling berkomunikasi dalam jaringan internal.

* Ping ke serverlocal – Percobaan untuk mengakses server melalui nama domain, menandakan adanya pengujian DNS.


Dari pengujian ini dapat disimpulkan bahwa NAT pada Router1 berfungsi dengan baik, menerjemahkan alamat IP lokal ke global. Perangkat dalam jaringan internal berhasil mengakses perangkat eksternal melalui NAT, dan DNS untuk nama domain "serverlocal" juga terdeteksi meskipun perlu verifikasi lebih lanjut untuk memastikan resolusi nama berfungsi secara penuh. Konfigurasi ini menunjukkan bahwa jaringan yang dibangun memiliki konektivitas yang baik antar perangkat, baik secara internal maupun eksternal.

### Kesimpulan
Konfigurasi DHCP berhasil memberikan alamat IP otomatis ke setiap departemen sesuai subnet-nya. DNS internal juga berfungsi untuk resolusi nama, dan pengujian NAT menunjukkan koneksi ke jaringan luar berjalan lancar. Dengan konfigurasi ini, jaringan sudah siap digunakan untuk operasional perusahaan secara efisien dan terpusat.



- **Laporan Implementasi Tahap 3**, berisi:
- Link file simulasi yang diperbarui.


