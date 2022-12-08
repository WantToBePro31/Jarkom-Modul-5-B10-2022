# Jarkom-Modul-5-B10-2022

## Kelompok B10
| Name                      | NRP        | 
| ------------------------- | ---------- |
| Frederick Wijayadi Susilo | 5025201111 |
| Zunia Aswaroh             | 5025201058 |

IP Prefix Kelompok: `10.8`

# Soal dan Jawaban

## Topologi
- Eden adalah DNS Server
- WISE adalah DHCP Server
- Garden dan SSS adalah Web Server
- Jumlah Host pada Forger adalah 62 host
- Jumlah Host pada Desmond adalah 700 host
- Jumlah Host pada Blackbell adalah 255 host
- Jumlah Host pada Briar adalah 200 host

## VLSM
Dalam mengerjakan soal ini, kami menggunakan teknik VLSM dengan menggunakan gns3.

### Pembagian Subnet
Hal pertama yang dilakukan adalah menentukan subnet-subnet pada topologi. Pembagian subnet berdasarkan setiap node yang terhubung melalui switch ataupun tidak, dalam satu daerah.


### Penentuan Jumlah IP
Setelah itu, setiap subnet akan dihitung jumlah IP yang dibutuhkan dan netmask yang dihasilkan berdasarkan node-node yang terhubung.


### VLSM Tree
Setelah mendapatkan jumlah IP setiap subnet, akan dibuat Tree berdasarkan topologi. Pada subnet induk memiliki NID 10.8.0.0 dengan netmask /21 sehingga pembagian IP dilakukan berdasarkan NID dan Netmask yang di dapat hingga mencapai subnet terbawah.



Lalu, kita mendapatkan IP untuk masing-masing subnet.

### Pembagian IP
Kemudian kita bagi dengan tetap menggunakan ip perfix `10.8`. Hasil pembagian seperti berikut :

### Eth Configuration
