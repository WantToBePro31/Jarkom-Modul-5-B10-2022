# Jarkom-Modul-5-B10-2022

## Kelompok B10
| Name                      | NRP        | 
| ------------------------- | ---------- |
| Frederick Wijayadi Susilo | 5025201111 |
| Zunia Aswaroh             | 5025201058 |

IP Prefix Kelompok: `10.8`

# Soal dan Jawaban

## Topologi
![image](https://user-images.githubusercontent.com/67154280/206476268-47cad854-1275-4950-8a2a-5d2c394a62f9.png)

- Eden adalah DNS Server
- WISE adalah DHCP Server
- Garden dan SSS adalah Web Server
- Jumlah Host pada Forger adalah 62 host
- Jumlah Host pada Desmond adalah 700 host
- Jumlah Host pada Blackbell adalah 255 host
- Jumlah Host pada Briar adalah 200 host

## VLSM
Dalam mengerjakan soal ini, kami menggunakan teknik VLSM dengan menggunakan GNS3.

### Pembagian Subnet
Hal pertama yang dilakukan adalah menentukan subnet-subnet pada topologi. Pembagian subnet berdasarkan setiap node yang terhubung melalui switch ataupun tidak, dalam satu daerah.

![image](https://user-images.githubusercontent.com/67154280/206476449-9f2a5195-4de4-4ce2-a1e9-134bd952ed90.png)

### Penentuan Jumlah IP
Setelah itu, setiap subnet akan dihitung jumlah IP yang dibutuhkan dan netmask yang dihasilkan berdasarkan node-node yang terhubung.

![image](https://user-images.githubusercontent.com/67154280/206476666-4bff11c2-2e3f-4ef3-a58c-1bbd7e4d4b23.png)

### VLSM Tree
Setelah mendapatkan jumlah IP setiap subnet, akan dibuat Tree berdasarkan topologi. Pada subnet induk memiliki NID 10.8.0.0 dengan netmask /21 sehingga pembagian IP dilakukan berdasarkan NID dan Netmask yang di dapat hingga mencapai subnet terbawah.

![image](https://user-images.githubusercontent.com/67154280/206476863-304cd643-96fd-4194-a8fa-72dd81c3c8e8.png)

Lalu, kita mendapatkan IP untuk masing-masing subnet.

### Pembagian IP
Kemudian kita bagi dengan tetap menggunakan ip perfix `10.8`. Hasil pembagian seperti berikut :

![image](https://user-images.githubusercontent.com/67154280/206476959-ffebe04b-e6ba-423a-b7eb-1c63d49908af.png)

### Eth Configuration
Pada masing-masing node akan dilakukan network configuration, kita melakukan assignment dengan IP yang telah ditetapkan sesuai dengan subnet

- Strix

  Karena menggunkana DHCP, pada Strix dibuat fix address dengan memasukkan `hwaddress` agar IP tidak berubah-ubah.
  
  ```shell
  auto lo
  iface lo inet loopback

  auto eth0
  iface eth0 inet dhcp
  hwaddress ether b6:23:6b:b5:5d:33

  auto eth1
  iface eth1 inet static
  address 10.8.7.149
  netmask 255.255.255.252

  auto eth2
  iface eth2 inet static
  address 10.8.7.145
  netmask 255.255.255.252
  ```

- Ostania
  ```shell
  auto lo
  iface lo inet loopback

  auto eth0
  iface eth0 inet static
  address 10.8.7.150
  netmask 255.255.255.252
  gateway 10.8.7.149

  auto eth1
  iface eth1 inet static
  address 10.8.4.1
  netmask 255.255.254.0

  auto eth2
  iface eth2 inet static
  address 10.8.7.137
  netmask 255.255.255.248

  auto eth3
  iface eth3 inet static
  address 10.8.6.1
  netmask 255.255.255.0
  ```
  
- Westalis
  ```shell
  auto lo
  iface lo inet loopback

  auto eth0
  iface eth0 inet static
  address 10.8.7.146
  netmask 255.255.255.252
  gateway 10.8.7.145

  auto eth1
  iface eth1 inet static
  address 10.8.0.1
  netmask 255.255.252.0

  auto eth2
  iface eth2 inet static
  address 10.8.7.1
  netmask 255.255.255.128

  auto eth3
  iface eth3 inet static
  address 10.8.7.129
  netmask 255.255.255.248
  ```
  
- Garden
  ```shell
  auto eth0
  iface eth0 inet static
  address 10.8.7.138
  netmask 255.255.255.248
  gateway 10.8.7.137
  ```
  
- SSS
  ```shell
  auto eth0
  iface eth0 inet static
  address 10.8.7.139
  netmask 255.255.255.248
  gateway 10.8.7.137
  ```
  
- Eden
  ```shell
  auto eth0
  iface eth0 inet static
  address 10.8.7.130
  netmask 255.255.255.248
  gateway 10.8.7.129
  ```
  
- WISE
  ```shell
  auto eth0
  iface eth0 inet static
  address 10.8.7.131
  netmask 255.255.255.248
  gateway 10.8.7.129
  ```
  
- Blackbell, Briar, Desmond, dan Forger sebagai Client sehingga akan dibuat eth configurationnya sebagai berikut. 
  ```shell
  auto lo
  iface lo inet loopback

  auto eth0
  iface eth0 inet dhcp
  ```
  
### Routing
Setelah itu akan dilakukan routing pada GNS3 agar semua router, server, dan client akan saling terhubung.

- Strix
  ```shell
  route add -net 10.8.7.128 netmask 255.255.255.248 gw 10.8.7.146
  route add -net 10.8.7.0 netmask 255.255.255.128 gw 10.8.7.146
  route add -net 10.8.0.0 netmask 255.255.252.0 gw 10.8.7.146

  route add -net 10.8.4.0 netmask 255.255.254.0 gw 10.8.7.150
  route add -net 10.8.6.0 netmask 255.255.255.0 gw 10.8.7.150
  route add -net 10.8.7.136 netmask 255.255.255.248 gw 10.8.7.150
  ```
  
## Config DNS Server
Node yang berperan sebagai DNS Server yaitu Eden akan menambahkan konfigurasi pada file `/etc/bind/named.conf.options` sebagai berikut.

```shell
options {
        directory "/var/cache/bind";

        // If there is a firewall between you and nameservers you want
        // to talk to, you may need to fix the firewall to allow multiple
        // ports to talk.  See http://www.kb.cert.org/vuls/id/800113

        // If your ISP provided one or more IP addresses for stable
        // nameservers, you probably want to use them as forwarders.
        // Uncomment the following block, and insert the addresses replacing
        // the all-0's placeholder.

        forwarders {
                192.168.122.1;
        };

        //========================================================================
        // If BIND logs error messages about the root key being expired,
        // you will need to update your keys.  See https://www.isc.org/bind-keys
        //========================================================================
        //dnssec-validation auto;
        allow-query{any;};

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
};
```
  
## Config DHCP Server
Node yang berperan sebagai DHCP Server yaitu WISE akan menambahkan konfigurasi pada file-file sebagai berikut.

- `.bashrc`
  ```shell
  echo nameserver 192.168.122.1 > /etc/resolv.conf
  apt-get update
  apt-get install isc-dhcp-server -y
  ```
  
- `/etc/default/isc-dhcp-server`
  ```shell
  # Defaults for isc-dhcp-server initscript
  # sourced by /etc/init.d/isc-dhcp-server
  # installed at /etc/default/isc-dhcp-server by the maintainer scripts

  #
  # This is a POSIX shell fragment
  #

  # Path to dhcpd's config file (default: /etc/dhcp/dhcpd.conf).
  #DHCPD_CONF=/etc/dhcp/dhcpd.conf

  # Path to dhcpd's PID file (default: /var/run/dhcpd.pid).
  #DHCPD_PID=/var/run/dhcpd.pid

  # Additional options to start dhcpd with.
  #       Don't use options -cf or -pf here; use DHCPD_CONF/ DHCPD_PID instead
  #OPTIONS=""

  # On what interfaces should the DHCP server (dhcpd) serve DHCP requests?
  #       Separate multiple interfaces with spaces, e.g. "eth0 eth1".
  INTERFACES="eth0"
  ```
  
- `/etc/dhcp/dhcpd.conf`
  ```shell
  #Forger
  subnet 10.8.7.0 netmask 255.255.255.128 {
      range 10.8.7.2 10.8.7.126;
      option routers 10.8.7.1;
      option broadcast-address 10.8.7.127;
      option domain-name-servers 10.8.7.130; 
      default-lease-time 600;
      max-lease-time 7200;
  }

  #Desmond
  subnet 10.8.0.0 netmask 255.255.252.0 {
      range 10.8.0.2 10.8.3.254;
      option routers 10.8.0.1;
      option broadcast-address 10.8.3.255;
      option domain-name-servers 10.8.7.130;
      default-lease-time 600;
      max-lease-time 7200;
  }

  #Blackbell
  subnet 10.8.4.0 netmask 255.255.254.0 {
      range 10.8.4.2 10.8.5.254;
      option routers 10.8.4.1;
      option broadcast-address 10.8.5.255;
      option domain-name-servers 10.8.7.130;
      default-lease-time 600;
      max-lease-time 7200;
  }

  #Briar
  subnet 10.8.6.0 netmask 255.255.255.0 {
      range 10.8.6.2 10.8.6.254;
      option routers 10.8.6.1;
      option broadcast-address 10.8.6.255;
      option domain-name-servers 10.8.7.130;
      default-lease-time 600;
      max-lease-time 7200;
  }

  subnet 10.8.7.128 netmask 255.255.255.248 {
      option routers 10.8.7.129;
  }
  ```
  
## Config DHCP Relay
Node yang berperan sebagai DHCP Relay yaitu Ostania dan Westalis akan menambahkan konfigurasi pada file-file sebagai berikut.

- `.bashrc`
  ```shell
  echo nameserver 192.168.122.1 > /etc/resolv.conf
  apt-get update
  echo "" | apt-get install isc-dhcp-relay -y
  ```

- `/etc/default/isc-dhcp-relay`
  ```shell
  # What servers should the DHCP relay forward requests to?
  SERVERS="10.8.7.131"

  # On what interfaces should the DHCP relay (dhrelay) serve DHCP requests?
  INTERFACES="eth0 eth1 eth2 eth3"

  # Additional options that are passed to the DHCP relay daemon?
  OPTIONS=""
  ```
  
## Config Web Server
Node yang berperan sebagai Web Server yaitu Garden dan SSS akan menambahkan konfigurasi pada file-file sebagai berikut.

- `.bashrc`
  ```shell
  echo nameserver 192.168.122.1 > /etc/resolv.conf
  apt-get update
  apt-get install apache2 -y
  ```
 
- `/var/www/html/index.html`
  ```shell
  # Garden
  Garden nih
  
  # SSS
  SSS nih
  ```
  
- `/etc/apache2/ports.conf`
  ```shell
  # If you just change the port or add more ports here, you will likely also
  # have to change the VirtualHost statement in
  # /etc/apache2/sites-enabled/000-default.conf

  Listen 80
  Listen 443

  <IfModule ssl_module>
          Listen 443
  </IfModule>

  <IfModule mod_gnutls.c>
          Listen 443
  </IfModule>

  # vim: syntax=apache ts=4 sw=4 sts=4 sr noet
  ```
  
## Penyelesaian
### 1
> Agar topologi yang kalian buat dapat mengakses keluar, kalian diminta untuk mengkonfigurasi Strix menggunakan iptables, tetapi Loid tidak ingin menggunakan MASQUERADE

Pertama kita dapatkan terlebih dahulu IP dari Strix yang berhubungan dengan NAT dengan menggunakan command `ip a`.

![image](https://user-images.githubusercontent.com/67154280/206515877-957cedba-7027-4911-95ad-ad320cdbe9f6.png)

Karena diminta untuk tidak menggunakan MASQUERADE, maka digunakan SNAT. Source akan diubah dari yang awalnya 0.0 ke Strix dengan --to-source 192.168.122.213.

```shell
iptables -t nat -A POSTROUTING -s 10.8.0.0/21 -o eth0 -j SNAT --to-source 192.168.122.213
```

Hasil testing

![image](https://user-images.githubusercontent.com/67154280/206523651-e40838de-2bda-43f2-ac44-464336ed465f.png)

### 2
> Kalian diminta untuk melakukan drop semua TCP dan UDP dari luar Topologi kalian pada server yang merupakan DHCP Server demi menjaga keamanan

### 3
> Loid meminta kalian untuk membatasi DHCP dan DNS Server hanya boleh menerima maksimal 2 koneksi ICMP secara bersamaan menggunakan iptables, selebihnya didrop

Karena koneksinya ICMP maka akan digunakan flag `-p` dengan nilai `icmp`. Selain, itu akan digunakan limit maksimal 2 untuk akses koneksi secara bersamaan sehingga dapat menggunakan `--connlimit-above 3` dan menambahkan target `DROP` agar koneksi lainnya selain 2 koneksi tersebut ditolak.

```shell
iptables -A INPUT -p icmp -m connlimit --connlimit-above 2 --connlimit-mask 0 -j DROP
```

Hasil testing

- DHCP Server

![image](https://user-images.githubusercontent.com/67154280/206534505-07d81b6d-2734-4aed-99c0-e30334fd9867.png)

- DNS Server

![image](https://user-images.githubusercontent.com/67154280/206534246-7dbed78d-897f-4ad1-bf81-fa31304c3c5c.png)

### 4
> Akses menuju Web Server hanya diperbolehkan disaat jam kerja yaitu Senin sampai Jumat pada pukul 07.00 - 16.00

### 5
> Karena kita memiliki 2 Web Server, Loid ingin Ostania diatur sehingga setiap request dari client yang mengakses Garden dengan port 80 akan didistribusikan secara bergantian pada SSS dan Garden secara berurutan dan request dari client yang mengakses SSS dengan port 443 akan didistribusikan secara bergantian pada Garden dan SSS secara berurutan

### 6
> Karena Loid ingin tau paket apa saja yang di-drop, maka di setiap node server dan router ditambahkan logging paket yang di-drop dengan standard syslog level
