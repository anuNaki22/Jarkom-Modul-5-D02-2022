# Jarkom-Modul-5-D02-2022

## Anggota Kelompok D02
| NRP | Nama | Kontribusi |
| :---:        |     :---:           | :---: |
| 5025201082   | Farrel Emerson      |      |
| 5025201087   | Daniel Hermawan     |      |
| 5025201003   | Rahmat Faris Akbar  |     | 

# Soal dan Jawaban

## (A) Topologi
![image](https://user-images.githubusercontent.com/99629909/206746808-40e129e8-671f-4b54-92e2-2f44d2e78e3f.png)

## Keterangan Node:

- Eden adalah DNS Server
- WISE adalah DHCP Server
- Garden dan SSS adalah Web Server
- Jumlah Host pada Forger adalah 62 host
- Jumlah Host pada Desmond adalah 700 host
- Jumlah Host pada Blackbell adalah 255 host
- Jumlah Host pada Briar adalah 200 host

## (B) Pembagian Subnet - VLSM

![Subnet VLSM](https://cdn.discordapp.com/attachments/856609726225973278/1049249641172062239/Subnet_VLSM.png)

## Penghitungan Jumlah Subnet

![image](https://user-images.githubusercontent.com/99629909/206746981-db41638e-5086-4767-ae4b-79c0da433cfb.png)

## VLSM Tree

![image](https://user-images.githubusercontent.com/99629909/206747135-60912d67-77f3-4d04-90c9-fbeb291b85ec.png)

## Pembagian IP

![image](https://user-images.githubusercontent.com/99629909/206747237-60363daa-6065-4e08-9498-ebbc4989e320.png)

## Network Configuration

### Strix

```
auto eth0
iface eth0 inet dhcp
hwaddress ether 3a:20:6d:d7:5b:79
auto eth1
iface eth1 inet static
        address 10.16.0.1
        netmask 255.255.255.252
auto eth2
iface eth2 inet static
        address 10.16.0.5
        netmask 255.255.255.252
```
**Strix** menggunakan `hwaddress` agar bisa mendapatkan fixed address yang akan digunakan untuk konfigurasi iptables nantinya. Fixed address dari **Strix** adalah `192.168.122.227`

### Westalis

```
auto eth0
iface eth0 inet static
        address 10.16.0.2
        netmask 255.255.255.252
        gateway 10.16.0.1
auto eth1
iface eth1 inet static
        address 10.16.0.17
        netmask 255.255.255.248
auto eth2
iface eth2 inet static
        address 10.16.0.129
        netmask 255.255.255.128
auto eth3
iface eth3 inet static
        address 10.16.4.1
        netmask 255.255.252.0
```

### Ostania

```
auto eth0
iface eth0 inet static
        address 10.16.0.6
        netmask 255.255.255.252
        gateway 10.16.0.5
auto eth1
iface eth1 inet static
        address 10.16.1.1
        netmask 255.255.255.0
auto eth2
iface eth2 inet static
        address 10.16.0.25
        netmask 255.255.255.248
auto eth3
iface eth3 inet static
        address 10.16.2.1
        netmask 255.255.254.0
```

### Eden

```
auto eth0
iface eth0 inet static
        address 10.16.0.18
        netmask 255.255.255.248
        gateway 10.16.0.17
```

### WISE

```
auto eth0
iface eth0 inet static
        address 10.16.0.19
        netmask 255.255.255.248
        gateway 10.16.0.17
```

### SSS

```
auto eth0
iface eth0 inet static
        address 10.16.0.26
        netmask 255.255.255.248
        gateway 10.16.0.25
```

### Garden

```
auto eth0
iface eth0 inet static
        address 10.16.0.27
        netmask 255.255.255.248
        gateway 10.16.0.25
```

## (C) Routing

Pada Strix

```
route add -net 10.16.0.16 netmask 255.255.255.248 gw 10.16.0.2
route add -net 10.16.0.128 netmask 255.255.255.128 gw 10.16.0.2
route add -net 10.16.4.0 netmask 255.255.252.0 gw 10.16.0.2
route add -net 10.16.1.0 netmask 255.255.255.0 gw 10.16.0.6
route add -net 10.16.0.24 netmask 255.255.255.248 gw 10.16.0.6
route add -net 10.16.2.0 netmask 255.255.254.0 gw 10.16.0.6
```

Pada Westalis

```
route add -net 0.0.0.0 netmask 0.0.0.0 gw 10.16.0.1
```

Pada Ostania

```
route add -net 0.0.0.0 netmask 0.0.0.0 gw 10.16.0.5
```

## IP Tables Strix
Pada router Strix jalankan perintah berikut ini (belum menjawab soal 1):
```
  iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.189.0.0/21
```

## Setting resolv.conf
Pada semua node selain Strix (termasuk router-router lain), jalankan perintah berikut ini:
```
  echo nameserver 192.168.122.1 > /etc/resolv.conf
```

## (D) DHCP

Pada **Forger**, **Desmond**, **Blackbell**, dan **Briar** dilakukan network configuration seperti berikut

```
auto eth0
iface eth0 inet dhcp
```

### DHCP Server

1. Pada **WISE** sebagai DHCP Server, dilakukan instalasi DHCP Server

```
echo nameserver 192.168.122.1 > /etc/resolv.conf
apt-get update
apt-get install isc-dhcp-server -y
```

2. Konfigurasi file `/etc/default/isc-dhcp-server` pada **WISE** sebagai DHCP Server

```
INTERFACES="eth0"
```

3. Konfigurasi file `/etc/dhcp/dhcpd.conf` pada **WISE** sebagai DHCP Server

```
# Forger (A2)
subnet 10.16.0.128 netmask 255.255.255.128 {
        range 10.16.0.130 10.16.0.254;
        option routers 10.16.0.129;
        option broadcast-address 10.16.0.255;
        option domain-name-servers 10.16.0.18;
        default-lease-time 600;
        max-lease-time 7200;
}
# Desmond (A3)
subnet 10.16.4.0 netmask 255.255.252.0 {
        range 10.16.4.2 10.16.7.254;
        option routers 10.16.4.1;
        option broadcast-address 10.16.7.255;
        option domain-name-servers 10.16.0.18;
        default-lease-time 600;
        max-lease-time 7200;
}
# Briar (A6)
subnet 10.16.1.0 netmask 255.255.255.0 {
        range 10.16.1.2 10.16.1.254;
        option routers 10.16.1.1;
        option broadcast-address 10.16.1.255;
        option domain-name-servers 10.16.0.18;
        default-lease-time 600;
        max-lease-time 7200;
}
# Blackbell (A7)
subnet 10.16.2.0 netmask 255.255.254.0 {
        range 10.16.2.2 10.16.3.254;
        option routers 10.16.2.1;
        option broadcast-address 10.16.3.255;
        option domain-name-servers 10.16.0.18;
        default-lease-time 600;
        max-lease-time 7200;
}
# WISE menuju Westalis (A1)
subnet 10.16.0.16 netmask 255.255.255.248 {
        option routers 192.200.0.17;
}
```

4. Menjalankan DHCP Server

```
service isc-dhcp-server start
```

### DHCP Relay

Dengan topologi yang ada, **Westalis** dan **Ostania** akan bekerja sebagai DHCP Relay

1. Pada **Westalis** dan **Ostania** sebagai DHCP Relay, dilakukan instalasi DHCP Relay

```
echo nameserver 192.168.122.1 > /etc/resolv.conf
apt-get update
apt-get install isc-dhcp-relay -y
```

2. Konfigurasi file `/etc/default/isc-dhcp-relay` pada **Westalis** dan **Ostania** sebagai DHCP Relay

```
SERVERS="10.16.0.19"
INTERFACES="eth0 eth1 eth2 eth3"
OPTIONS=""
```

### DNS Forwarder

Pada **Eden** sebagai DNS Server, dilakukan instalasi Bind9

```
echo nameserver 192.168.122.1 > /etc/resolv.conf
apt-get update
apt-get install bind9 -y
service bind9 start
```

Kemudian konfigurasi DNS Forwarder pada file `/etc/bind/named.conf.options`

```
options {
        directory \"/var/cache/bind\";
         forwarders {
                192.168.122.1;
         };
        allow-query{any;};
        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
};
```

Setelah itu restart bind9 dengan `service bind9 restart`

### Testing

#### Forger


#### Desmond


#### Briar


#### Blackbell


## (1) iptables pada **Strix** tanpa `MASQUERADE`

`MASQUERADE` dapat diganti dengan `SNAT` dengan tambahan `--to-source` dengan IP dari Strix sendiri yaitu `192.168.122.227`, syntaxnya adalah

```
iptables -t nat -A POSTROUTING -s 10.16.0.0/21 -o eth0 -j SNAT --to-source 192.168.122.227
```

### Testing

#### Pada **Strix**


#### Pada **Westalis**


#### Pada **WISE**


#### Pada **Briar**



## (2) Drop semua TCP dan UDP pada DHCP Server

Pada **Strix** dilakukan konfigurasi firewall sebagai berikut

```
iptables -A FORWARD -p tcp -d 10.16.0.19 -i eth0 -j DROP # Drop semua TCP
iptables -A FORWARD -p udp -d 10.16.0.19 -i eth0 -j DROP # Drop semua UDP
```

iptables di atas akan melalukan drop pada semua TCP dan UDP dengan tujuan **WISE** yang memiliki IP address `10.16.0.19`

### Testing

#### Ping google.com pada WISE setelah iptables


#### Netcat **WISE** dengan **Strix** menggunakan port 18



## (3) Membatasi DHCP dan DNS Server hanya boleh menerima maksimal 2 koneksi ICMP secara bersamaan

Limit koneksi ICMP dengan iptables pada **WISE** sebagai DHCP Server dan **Eden** sebagai DNS Server

```
iptables -A INPUT -p icmp -m connlimit --connlimit-above 2 --connlimit-mask 0 -j DROP
```

### Testing ping Eden (10.16.0.18) sebagai DNS Server dengan 3 client





## (4) Akses menuju Web Server hanya diperbolehkan disaat jam kerja yaitu Senin sampai Jumat pada pukul 07.00 - 16.00

Pada **Garden** dan **SSS** sebagai Web Server, dibuat firewall sebagai berikut

```
iptables -A INPUT -m time --timestart 07:00 --timestop 16:00 --weekdays Mon,Tue,Wed,Thu,Fri -j ACCEPT
iptables -A INPUT -j REJECT
```

### Testing

Ping **Garden** (10.16.0.27) pada jam kerja


Ping **Garden** (10.16.0.27) pada hari libur


## (5) Setiap request dari client yang mengakses Garden dengan port 80 akan didistribusikan secara bergantian pada SSS dan Garden secara berurutan dan request dari client yang mengakses SSS dengan port 443 akan didistribusikan secara bergantian pada Garden dan SSS secara berurutan

Pada **Ostania** dilakukan konfigurasi iptables sebagai berikut

```
iptables -t nat -A PREROUTING -p tcp -d 10.16.0.27 --dport 80 -m statistic --mode nth --every 2 --packet 0 -j DNAT --to-destination 10.16.0.26:80
iptables -t nat -A PREROUTING -p tcp -d 10.16.0.26 --dport 443 -m statistic --mode nth --every 2 --packet 0 -j DNAT --to-destination 10.16.0.27:443
```

## (6) Logging paket yang di-drop dengan standard syslog level

```
iptables -N LOGGING
iptables -A INPUT -j LOGGING
iptables -A LOGGING -j LOG --log-prefix "IPTables-Dropped: " --log-level 4
iptables -A LOGGING -j DROP
```