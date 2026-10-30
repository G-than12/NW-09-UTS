# Implementasi Jaringan Komputer Tiga Gedung Sekolah Menggunakan RIP v2

Dokumentasi proyek Ujian Tengah Semester (UTS) mata kuliah **Jaringan Komputer II**. Proyek ini memodelkan jaringan sekolah yang terdiri atas tiga gedung—Gedung A (ruang kelas), Gedung B (laboratorium), dan Gedung C (ruang server)—menggunakan Cisco Packet Tracer.

---

## Informasi Proyek

| Keterangan | Informasi |
|---|---|
| Institusi | UIN K.H. Abdurrahman Wahid Pekalongan |
| Fakultas | Fakultas Sains dan Teknologi |
| Program Studi | Informatika |
| Semester | 5 |
| Mata Kuliah | Jaringan Komputer II |
| Dosen Pengampu | Akrim Teguh Suseno, M.T.I. |
| Nama Mahasiswa | Gathan Hilabi |
| NIM | **059** |
| Kelas | **A** |
| Tanggal UTS | 30 Oktober 2026 |
| Simulator | Cisco Packet Tracer |
| Protokol Routing | RIPv2 (RIP versi 2) |

## 1. Ringkasan

Proyek ini merancang dan mengimplementasikan jaringan komputer sekolah yang menghubungkan tiga gedung melalui tiga router. Setiap gedung memiliki satu router sebagai penghubung jaringan lokal (LAN) dengan jaringan antargedung. Ketiga router dihubungkan secara langsung membentuk topologi segitiga sehingga tersedia lebih dari satu jalur antargedung.

Jaringan lokal masing-masing gedung menggunakan subnet IPv4 yang berbeda. Pertukaran informasi rute ant router dilakukan menggunakan **Routing Information Protocol versi 2 (RIPv2)**. Implementasi meliputi penempatan perangkat, pengkabelan, konfigurasi alamat IP, konfigurasi routing dinamis, serta pengujian konektivitas menuju server sekolah.

## 2. Tujuan

1. Merancang topologi jaringan yang menghubungkan tiga gedung sekolah.
2. Menentukan alamat IPv4, subnet mask, dan default gateway untuk router, PC, dan server.
3. Mengonfigurasi interface router dan routing dinamis RIPv2.
4. Memverifikasi informasi routing pada router.
5. Menguji konektivitas dari ruang kelas dan laboratorium menuju server sekolah menggunakan `ping`.

## 3. Topologi dan Arsitektur Jaringan

### 3.1 Gambaran topologi

Topologi terdiri atas tiga jaringan LAN dan tiga koneksi point-to-point ant router.

<img width="885" height="691" alt="image" src="https://github.com/user-attachments/assets/7633748b-3f85-44d6-a20a-189c98bf2ec0" />


**Keterangan hubungan ant router:**

- Router0 (Gedung A) terhubung langsung ke Router1 dan Router2.
- Router1 (Gedung B) terhubung langsung ke Router0 dan Router2.
- Router2 (Gedung C) terhubung langsung ke Router0 dan Router1.

### 3.2 Inventaris perangkat

| Perangkat | Model/tipe di Packet Tracer | Jumlah | Fungsi |
|---|---|---:|---|
| Router | Cisco 2911 | 3 | Menghubungkan LAN setiap gedung dan melakukan routing antargedung |
| Switch | Cisco 2960-24TT | 7 | Menghubungkan perangkat dalam LAN |
| PC | PC-PT | 9 | Perangkat pengguna di kelas, laboratorium, dan ruang server |
| Server | Server-PT | 1 | Menyimpan materi pembelajaran dan data sekolah |
| **Total** |  | **20** |  |

**Distribusi perangkat:**

- **Gedung A:** Router0, Switch0 (penghubung), Switch1 (Ruang Kelas 1), Switch2 (Ruang Kelas 2), PC0–PC3.
- **Gedung B:** Router1, Switch3 (penghubung), Switch4 (Lab Fisika), Switch5 (Lab Kimia), PC4–PC7.
- **Gedung C:** Router2, Switch6, Server0, PC8 (Admin).

### 3.3 Jenis kabel

| Hubungan perangkat | Jenis kabel |
|---|---|
| Router–Router | Copper Cross-Over |
| Switch–Switch | Copper Cross-Over |
| Router–Switch | Copper Straight-Through |
| PC–Switch | Copper Straight-Through |
| Server–Switch | Copper Straight-Through |

## 4. Rancangan Pengalamatan IP

Skema pengalamatan menggunakan subnet `/24` untuk LAN setiap gedung dan `/30` untuk setiap koneksi point-to-point ant router.

### 4.1 Jaringan LAN

| Gedung | Network ID | Subnet Mask | Default Gateway |
|---|---|---|---|
| Gedung A | `192.168.10.0/24` | `255.255.255.0` | `192.168.10.1` |
| Gedung B | `192.168.20.0/24` | `255.255.255.0` | `192.168.20.1` |
| Gedung C | `192.168.30.0/24` | `255.255.255.0` | `192.168.30.1` |

### 4.2 Koneksi ant router

| Koneksi | Interface sisi pertama | IP sisi pertama | Interface sisi kedua | IP sisi kedua | Subnet |
|---|---|---|---|---|---|
| Router0–Router1 | Router0 G0/1 | `10.10.12.1/30` | Router1 G0/1 | `10.10.12.2/30` | `10.10.12.0/30` |
| Router0–Router2 | Router0 G0/2 | `10.10.13.1/30` | Router2 G0/1 | `10.10.13.2/30` | `10.10.13.0/30` |
| Router1–Router2 | Router1 G0/2 | `10.10.23.1/30` | Router2 G0/2 | `10.10.23.2/30` | `10.10.23.0/30` |

Subnet mask untuk seluruh koneksi `/30` adalah `255.255.255.252`.

### 4.3 Alamat interface router

| Router | Interface | Alamat IPv4 | Terhubung ke |
|---|---|---|---|
| Router0 | G0/0 | `192.168.10.1/24` | Switch0 (LAN Gedung A) |
| Router0 | G0/1 | `10.10.12.1/30` | Router1 G0/1 |
| Router0 | G0/2 | `10.10.13.1/30` | Router2 G0/1 |
| Router1 | G0/0 | `192.168.20.1/24` | Switch3 (LAN Gedung B) |
| Router1 | G0/1 | `10.10.12.2/30` | Router0 G0/1 |
| Router1 | G0/2 | `10.10.23.1/30` | Router2 G0/2 |
| Router2 | G0/0 | `192.168.30.1/24` | Switch6 (LAN Gedung C) |
| Router2 | G0/1 | `10.10.13.2/30` | Router0 G0/2 |
| Router2 | G0/2 | `10.10.23.2/30` | Router1 G0/2 |

### 4.4 Alamat IP perangkat akhir

Semua perangkat akhir menggunakan subnet mask `255.255.255.0`. DNS Server tidak dikonfigurasi dan dibiarkan `0.0.0.0` pada simulasi.

| Gedung | Perangkat | Ruangan/Fungsi | IP Address | Default Gateway |
|---|---|---|---|---|
| A | PC0 | Ruang Kelas 1 | `192.168.10.11` | `192.168.10.1` |
| A | PC1 | Ruang Kelas 1 | `192.168.10.12` | `192.168.10.1` |
| A | PC2 | Ruang Kelas 2 | `192.168.10.13` | `192.168.10.1` |
| A | PC3 | Ruang Kelas 2 | `192.168.10.14` | `192.168.10.1` |
| B | PC4 | Lab Fisika | `192.168.20.11` | `192.168.20.1` |
| B | PC5 | Lab Fisika | `192.168.20.12` | `192.168.20.1` |
| B | PC6 | Lab Kimia | `192.168.20.13` | `192.168.20.1` |
| B | PC7 | Lab Kimia | `192.168.20.14` | `192.168.20.1` |
| C | Server0 | Server materi dan data sekolah | `192.168.30.11` | `192.168.30.1` |
| C | PC8 | PC Admin | `192.168.30.12` | `192.168.30.1` |

## 5. Konfigurasi Jaringan

### 5.1 Konfigurasi interface router

Setiap interface yang digunakan diberi alamat IP sesuai tabel pada Bagian 4 dan diaktifkan menggunakan perintah `no shutdown`.

Contoh konfigurasi interface LAN pada Router0:

```text
enable
configure terminal
interface gigabitEthernet 0/0
ip address 192.168.10.1 255.255.255.0
no shutdown
```

Contoh konfigurasi interface point-to-point Router0 menuju Router1:

```text
interface gigabitEthernet 0/1
ip address 10.10.12.1 255.255.255.252
no shutdown
```

Contoh konfigurasi interface point-to-point Router0 menuju Router2:

```text
interface gigabitEthernet 0/2
ip address 10.10.13.1 255.255.255.252
no shutdown
```

Interface router lainnya dikonfigurasi mengikuti tabel alamat interface pada Bagian 4.3.

### 5.2 Konfigurasi routing RIPv2

Ketiga router menggunakan RIPv2, dengan automatic summarization dinonaktifkan. Perintah `network 10.0.0.0` mengikutsertakan interface yang menggunakan alamat jaringan kelas A `10.0.0.0` pada proses RIP, termasuk ketiga subnet point-to-point `/30`.

**Router0 — Gedung A**

```text
enable
configure terminal
router rip
version 2
no auto-summary
network 10.0.0.0
network 192.168.10.0
end
```

**Router1 — Gedung B**

```text
enable
configure terminal
router rip
version 2
no auto-summary
network 10.0.0.0
network 192.168.20.0
end
```

**Router2 — Gedung C**

```text
enable
configure terminal
router rip
version 2
no auto-summary
network 10.0.0.0
network 192.168.30.0
end
```

### 5.3 Perintah verifikasi

Perintah berikut digunakan untuk memeriksa status interface, konfigurasi protokol routing, dan tabel routing:

```text
show ip interface brief
show ip protocols
show ip route
```

Pada pemeriksaan Router0, protokol yang terbaca adalah RIP dengan pengiriman dan penerimaan versi 2. Jaringan `10.0.0.0` dan `192.168.10.0` tercantum pada bagian *Routing for Networks*. Tabel routing juga menampilkan jaringan LAN Gedung B (`192.168.20.0/24`) dan Gedung C (`192.168.30.0/24`) sebagai rute yang dipelajari melalui RIP.

Tabel routing Router0 juga memperlihatkan dua jalur menuju subnet koneksi Router1–Router2 (`10.10.23.0/30`), masing-masing melalui Router1 dan Router2. Informasi ini menunjukkan adanya jalur routing alternatif pada topologi segitiga. Pengujian pemutusan link secara khusus tidak dicatat dalam hasil pengujian proyek ini.

## 6. Pengujian Konektivitas

### 6.1 Metode pengujian

Pengujian dilakukan dari Command Prompt pada PC perwakilan setiap ruang kelas dan laboratorium. Setiap PC mengirimkan empat paket ICMP ke alamat IPv4 Server0, yaitu `192.168.30.11`, menggunakan perintah:

```text
ping 192.168.30.11
```

Koneksi dinyatakan berhasil apabila PC menerima balasan (*Reply*) dari alamat server. Hasil statistik yang diharapkan pada pengujian berulang adalah empat paket diterima dan tidak ada paket yang hilang.

### 6.2 Hasil pengujian

| No. | Perangkat sumber | Ruangan | Tujuan | Paket terkirim | Paket diterima | Paket hilang | Hasil |
|---:|---|---|---|---:|---:|---:|---|
| 1 | PC0 (`192.168.10.11`) | Ruang Kelas 1 | Server0 (`192.168.30.11`) | 4 | 4 | 0 (0%) | Berhasil |
| 2 | PC2 (`192.168.10.13`) | Ruang Kelas 2 | Server0 (`192.168.30.11`) | 4 | 4 | 0 (0%) | Berhasil |
| 3 | PC4 (`192.168.20.11`) | Lab Fisika | Server0 (`192.168.30.11`) | 4 | 4 | 0 (0%) | Berhasil |
| 4 | PC6 (`192.168.20.13`) | Lab Kimia | Server0 (`192.168.30.11`) | 4 | 4 | 0 (0%) | Berhasil |

**Catatan pengujian:** Pada pengujian pertama dari PC0, satu paket awal mengalami *Request timed out* (3 paket diterima dari 4 paket, kehilangan 25%). Setelah perintah ping dijalankan kembali, hasilnya menjadi 4 paket diterima dari 4 paket (0% kehilangan). Kondisi awal tersebut dapat terjadi saat proses resolusi ARP berlangsung pada komunikasi pertama. Hasil pengujian ulang digunakan sebagai hasil konektivitas PC0.

Pengujian yang terdokumentasi mencakup satu PC per ruangan. Perangkat PC lain dan PC Admin tidak dicantumkan sebagai pengujian ping individual pada tabel ini.

## 7. Kesimpulan

Berdasarkan implementasi dan pengujian di Cisco Packet Tracer, jaringan sekolah yang terdiri atas Gedung A, Gedung B, dan Gedung C telah dibangun dengan tiga router Cisco 2911 yang saling terhubung membentuk topologi segitiga. Masing-masing gedung menggunakan jaringan LAN IPv4 yang berbeda, sedangkan koneksi ant router menggunakan subnet point-to-point `/30`.

Routing dinamis dikonfigurasi menggunakan RIPv2 dengan `no auto-summary`. Hasil verifikasi pada Router0 menunjukkan bahwa rute menuju jaringan Gedung B dan Gedung C telah dipelajari melalui RIP. Pengujian ICMP dari PC0, PC2, PC4, dan PC6 menuju Server0 menunjukkan keberhasilan 4 dari 4 paket dengan *packet loss* 0% pada pengujian akhir.

Dengan hasil tersebut, konektivitas antargedung menuju server telah berhasil dibuktikan untuk perangkat perwakilan pada masing-masing ruang kelas dan laboratorium. Pengujian failover dengan memutus salah satu koneksi ant router belum dilakukan, sehingga kemampuan pemulihan jalur belum dinyatakan sebagai hasil uji.

## 8. Struktur Berkas Pengumpulan

Simpan berkas proyek dan laporan dalam satu folder sebelum dikompres menjadi ZIP atau RAR. Contoh struktur:

```text
UTS_Nama_NIM/
├── README.md
├── uts.pkt
└── Laporan_UTS_Jaringan_Komputer_II.pdf
```

Sesuai petunjuk UTS, nama folder/arsip akhir perlu disesuaikan dengan format `UTS_Nama_NIM`. Nama file Packet Tracer pada komputer saat dokumentasi dibuat adalah `uts.pkt`; nama tersebut dapat dipertahankan di dalam folder pengumpulan atau disesuaikan agar konsisten dengan identitas mahasiswa.

## 9. Catatan Penggunaan

1. Buka file `.pkt` menggunakan Cisco Packet Tracer.
2. Pastikan seluruh perangkat dan koneksi tampil pada workspace.
3. Jika ingin menguji ulang, gunakan Command Prompt pada PC dan jalankan `ping 192.168.30.11`.
4. Untuk memeriksa routing, buka CLI router dan jalankan `show ip protocols` atau `show ip route`.
5. Simpan perubahan proyek menggunakan **File → Save** atau `Ctrl + S`.

---

**Dokumentasi ini disusun berdasarkan konfigurasi dan hasil pengujian yang telah ditampilkan pada Cisco Packet Tracer.**
