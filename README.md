<div align=center>

# Dokumentasi Jarkom Praktikum 2

| Nama | Muhammad Ersya Vinorian |
| :--- | :---------------------- |
| NRP  | 5025211045              |

</div>

<hr/>

## Soal 0

> Kali ini, kalian diminta untuk melakukan register domain berupa riegel.canyon.yyy.com untuk worker Laravel dan granz.channel.yyy.com untuk worker PHP mengarah pada worker yang memiliki IP [prefix IP].x.1.

Di sini saya mengasumsikan bahwa yang diarahkan ke IP Worker dengan `[Prefix IP].x.1` adalah subdomain `riegel` dan juga `granz`. Untuk domain `canyon.a01.com` dan `channel.a01.com` akan saya arahkan ke Load Balancer.

Berikut adalah konfigurasi dari `Heiter:/etc/bind/praktikum/canyon.a01.com`.

```bind
$TTL    604800
@       IN      SOA     canyon.a01.com. root.canyon.a01.com. (
                     2023111301         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      canyon.a01.com.
@       IN      A       192.169.2.2     ; IP Load Balancer Eisen
riegel  IN      A       192.169.4.1     ; IP Laravel Worker Fern
```

Berikut adalah konfigurasi dari `Heiter:/etc/bind/praktikum/channel.a01.com`.

```bind
$TTL    604800
@       IN      SOA     channel.a01.com. root.channel.a01.com. (
                     2023111302         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      channel.a01.com.
@       IN      A       192.169.2.2     ; IP Load Balancer Eisen
granz   IN      A       192.169.3.1     ; IP PHP Worker Lugner
```

Berikut adalah konfigurasi dari `/etc/bind/named.conf.local`.

```nginx
zone "canyon.a01.com" {
        type master;
        file "/etc/bind/praktikum/canyon.a01.com";
};

zone "channel.a01.com" {
        type master;
        file "/etc/bind/praktikum/channel.a01.com";
};
```

Berikut adalah konfigurasi dari `/etc/bind/named.conf.options`.

```nginx
options {
        directory "/var/cache/bind";

        forwarders {
                192.168.122.1;
        };

        allow-query { any; };
        auth-nxdomain no;

        listen-on-v6 { any; };
};
```

Agar client yang terhubung melalui DHCP ke DNS Server dapat terhubung ke internet, maka akan ditambahkan konfigurasi forwarders ke nameserver dari Router.

Melakukan tes di node Himmel yang menggunakan nameserver dari DNS Server.

![image](https://github.com/mvinorian/jarkom-praktikum-3/assets/54766683/ecb474b1-148b-4cb6-ae38-9caa6f17c020)

## Soal 1

> Lakukan konfigurasi sesuai dengan peta yang sudah diberikan.

![image](https://github.com/mvinorian/jarkom-praktikum-3/assets/54766683/1cf535bb-c8bf-44dd-8444-edc4051f9613)

### Network Interface Aura

```bind
auto eth0
iface eth0 inet dhcp
	up iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.169.0.0/16

auto eth1
iface eth1 inet static
	address 192.169.1.10
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 192.169.2.10
	netmask 255.255.255.0

auto eth3
iface eth3 inet static
	address 192.169.3.10
	netmask 255.255.255.0

auto eth4
iface eth4 inet static
	address 192.169.4.10
	netmask 255.255.255.0
```

### Network Interface Himmel

```bind
auto eth0
iface eth0 inet static
	address 192.169.1.1
	netmask 255.255.255.0
	gateway 192.169.1.10
	up echo nameserver 192.169.1.2 > /etc/resolv.conf
```

### Network Interface Heiter

```bind
auto eth0
iface eth0 inet static
	address 192.169.1.2
	netmask 255.255.255.0
	gateway 192.169.1.10
	up echo nameserver 192.168.122.1 > /etc/resolv.conf
```

### Network Interface Denken

```bind
auto eth0
iface eth0 inet static
	address 192.169.2.1
	netmask 255.255.255.0
	gateway 192.169.2.10
	up echo nameserver 192.169.1.2 > /etc/resolv.conf
```

### Network Interface Eisen

```bind
auto eth0
iface eth0 inet static
	address 192.169.2.2
	netmask 255.255.255.0
	gateway 192.169.2.10
	up echo nameserver 192.169.1.2 > /etc/resolv.conf
```

### Network Interface Lugner

```bind
auto eth0
iface eth0 inet dhcp
hwaddress ether 10:10:10:10:10:31
```

### Network Interface Linie

```bind
auto eth0
iface eth0 inet dhcp
hwaddress ether 10:10:10:10:10:32
```

### Network Interface Lawine

```bind
auto eth0
iface eth0 inet dhcp
hwaddress ether 10:10:10:10:10:33
```

### Network Interface Richter

```bind
auto eth0
iface eth0 inet dhcp
hwaddress ether 10:10:10:10:10:34
```

### Network Interface Revolte

```bind
auto eth0
iface eth0 inet dhcp
hwaddress ether 10:10:10:10:10:35
```

### Network Interface Fern

```bind
auto eth0
iface eth0 inet dhcp
hwaddress ether 10:10:10:10:10:41
```

### Network Interface Flamme

```bind
auto eth0
iface eth0 inet dhcp
hwaddress ether 10:10:10:10:10:42
```

### Network Interface Frieren

```bind
auto eth0
iface eth0 inet dhcp
hwaddress ether 10:10:10:10:10:43
```

### Network Interface Stark

```bind
auto eth0
iface eth0 inet dhcp
hwaddress ether 10:10:10:10:10:44
```

### Network Interface Sein

```bind
auto eth0
iface eth0 inet dhcp
hwaddress ether 10:10:10:10:10:45
```

## Soal 2, 3, 4, 5

> Client yang melalui Switch3 mendapatkan range IP dari [prefix IP].3.16 - [prefix IP].3.32 dan [prefix IP].3.64 - [prefix IP].3.80

> Client yang melalui Switch4 mendapatkan range IP dari [prefix IP].4.12 - [prefix IP].4.20 dan [prefix IP].4.160 - [prefix IP].4.168

> Client mendapatkan DNS dari Heiter dan dapat terhubung dengan internet melalui DNS tersebut

> Lama waktu DHCP server meminjamkan alamat IP kepada Client yang melalui Switch3 selama 3 menit sedangkan pada client yang melalui Switch4 selama 12 menit. Dengan waktu maksimal dialokasikan untuk peminjaman alamat IP selama 96 menit

Dapat dilakukan konfigurasi DHCP Server dan DHCP Relay sebagai berikut.

Berikut adalah konfigurasi dari `Himmel:/etc/default/isc-dhcp-server`.

```sh
INTERFACESv4="eth0"
INTERFACESv6=""
```

Agar target client yang dikenakan DHCP hanya IPv4 saja, maka dapat ditambahkan `eth0` pada `INTERFACESv4`. Karena menggunakan DHCP Relay, maka interface yang ditambahkan adalah interface penghubung dari DHCP Server ke DHCP Relay.

Berikut adalah konfigurasi dari `Himmel:/etc/dhcp/dhcpd.conf`.

```nginx
subnet 192.169.1.0 netmask 255.255.255.0 {}

subnet 192.169.2.0 netmask 255.255.255.0 {}

subnet 192.169.3.0 netmask 255.255.255.0 {
        range 192.169.3.16 192.169.3.32;
        range 192.169.3.64 192.169.3.80;

        option routers 192.169.3.10;
        option broadcast-address 192.169.3.255;
        option domain-name-servers 192.169.1.2; # IP DNS Server Heiter

        default-lease-time 180;
        max-lease-time 5760;
}

subnet 192.169.4.0 netmask 255.255.255.0 {
        range 192.169.4.12  192.169.4.20;
        range 192.169.4.160 192.169.4.168;

        option routers 192.169.4.10;
        option broadcast-address 192.169.4.255;
        option domain-name-servers 192.169.1.2; # IP DNS Server Heiter

        default-lease-time 720;
        max-lease-time 5760;
}

# Fixed Address PHP Worker

host Lugner {
        hardware ethernet 10:10:10:10:10:31;
        fixed-address 192.169.3.1;
}

host Linie {
        hardware ethernet 10:10:10:10:10:32;
        fixed-address 192.169.3.2;
}

host Lawine {
        hardware ethernet 10:10:10:10:10:33;
        fixed-address 192.169.3.3;
}

# Fixed Address Laravel Worker

host Fern {
        hardware ethernet 10:10:10:10:10:41;
        fixed-address 192.169.4.1;
}

host Flamme {
        hardware ethernet 10:10:10:10:10:42;
        fixed-address 192.169.4.2;
}

host Frieren {
        hardware ethernet 10:10:10:10:10:43;
        fixed-address 192.169.4.3;
}

# Fixed Address Client for Testing

# host Richter {
#         hardware ethernet 10:10:10:10:10:34;
#         fixed-address 192.169.3.69;
# }
#
# host Revolte {
#         hardware ethernet 10:10:10:10:10:35;
#         fixed-address 192.169.3.70;
# }
#
# host Stark {
#         hardware ethernet 10:10:10:10:10:44;
#         fixed-address 192.169.4.167;
# }
#
# host Sein {
#         hardware ethernet 10:10:10:10:10:45;
#         fixed-address 192.169.4.168;
# }
```

Konfigurasi di atas dilakukan sesuai dengan perintah soal untuk IP range dari DHCP dan juga lease time serta max lease time. Untuk broadcast address akan disesuaikan dengan konvensi yaitu IP subnet dengan IP terakhir 255. Kemudian untuk DNS akan diarahkan ke IP DNS Server.

Karena worker node akan menggunakan fixed address, maka perlu dilakukan konfigurasi tersendiri juga. Kemudian konfigurasi fixed address untuk client ditambahkan sebagai testing pada salah satu soal di bawah.

Berikut adalah konfigurasi dari `Aura:/etc/sysctl.conf`.

```sh
net.ipv4.ip_forward=1
```

Agar dapat melakukan IP Forwarding pada IPv4 akan diubah konfigurasi dari `sysctl` seperti di atas.

Berikut adalah konfigurasi dari `Aura:/etc/default/isc-dhcp-relay`.

```sh
SERVERS="192.169.1.1"
INTERFACES="eth1 eth2 eth3 eth4"
```

Konfigurasi di atas ditambahkan untuk mengetahui di mana DHCP Server dan pada interfaces mana saja yang akan dikenakan DHCP.

Akan dilakukan tes pada client node Richter dan Stark sebagai berikut.

![image](https://github.com/mvinorian/jarkom-praktikum-3/assets/54766683/cefe6175-81ef-4c82-ab95-ab42b2e31a64)

![image](https://github.com/mvinorian/jarkom-praktikum-3/assets/54766683/5085e6e9-06b8-444d-b898-187839c95f98)

Client akan diberikan IP yang kosong pertama kali dengan nameserver dari DNS Server secara otomatis.

## Soal 6

> Pada masing-masing worker PHP, lakukan konfigurasi virtual host untuk website [berikut](https://drive.google.com/file/d/1ViSkRq7SmwZgdK64eRbr5Fm1EGCTPrU1/view?usp=sharing) dengan menggunakan php 7.3.

Dapat dikonfigurasi menggunakan web server nginx.

Berikut adalah konfigurasi dari `(PHP Worker):/etc/nginx/sites-available/default`.

```nginx
server {
        listen 80;

        root /var/www/granz.channel.a01.com;

        index index.html index.htm index.nginx-debian.html index.php;

        server_name granz.channel.a01.com;

        location / {
                try_files $uri $uri/ /index.php?$query_string;
        }

        location ~ \.php$ {
                include snippets/fastcgi-php.conf;
                fastcgi_pass unix:/run/php/php7.3-fpm.sock;
        }

        error_log /var/log/nginx/error.log;
        access_log /var/log/nginx/access.log;
}
```

Untuk project `granz.channel.a01.com` menggunakan resources yang telah disediakan di soal.

## Soal 8

> Kepala suku dari Bredt Region memberikan resource server sebagai berikut:
> Lawine, 4GB, 2vCPU, dan 80 GB SSD.
> Linie, 2GB, 2vCPU, dan 50 GB SSD.
> Lugner 1GB, 1vCPU, dan 25 GB SSD.
> aturlah agar Eisen dapat bekerja dengan maksimal, lalu lakukan testing dengan 1000 request dan 100 request/second.

Dapat menggunakan web server nginx untuk melakukan konfigurasi load balancer.

Berikut adalah konfigurasi dari `Eisen:/etc/nginx/sites-available/channel.a01.com`.

```nginx
# Weighted Round Robin
upstream worker_weighted_round_robin {
        server 192.169.3.1 weight=4; # IP Lugner
        server 192.169.3.2 weight=2; # IP Linie
        server 192.169.3.3 weight=1; # IP Lawine
}

server {
        listen 80;

        server_name channel.a01.com;

        location / {
                proxy_pass http://worker_weighted_round_robin;
        }

        location ~ /\.ht {
                deny all;
        }

        error_log /var/log/nginx/channel.a01.com-error.log;
        access_log /var/log/nginx/channel.a01.com-access.log;
}
```

Digunakan algoritma Weighted Round Robin agar sesuai dengan resources yang dijelaskan pada soal.

Kemudian untuk tes akan dilaksanakan pada client node dengan hasil sebagai berikut.

```sh
ab -n 1000 -c 100 http://channel.a01.com/
```

```
Server Software:        nginx/1.14.2
Server Hostname:        channel.a01.com
Server Port:            80

Document Path:          /
Document Length:        195 bytes

Concurrency Level:      100
Time taken for tests:   0.422 seconds
Complete requests:      1000
Failed requests:        0
Non-2xx responses:      1000
Total transferred:      398000 bytes
HTML transferred:       195000 bytes
Requests per second:    2367.58 [#/sec] (mean)
Time per request:       42.237 [ms] (mean)
Time per request:       0.422 [ms] (mean, across all concurrent requests)
Transfer rate:          920.21 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0   12   4.8     12      25
Processing:     2   17  25.3     14     246
Waiting:        1   17  24.4     14     246
Total:          2   29  26.1     26     263

Percentage of the requests served within a certain time (ms)
  50%     26
  66%     30
  75%     32
  80%     33
  90%     36
  95%     40
  98%     42
  99%    243
 100%    263 (longest request)
```

## Soal 8 dan 9

> Karena diminta untuk menuliskan grimoire, buatlah analisis hasil testing dengan 200 request dan 10 request/second masing-masing algoritma Load Balancer dengan ketentuan sebagai berikut:

> a. Nama Algoritma Load Balancer

> b. Report hasil testing pada Apache Benchmark

> c. Grafik request per second untuk masing masing algoritma.

> d. Analisis

> Dengan menggunakan algoritma Round Robin, lakukan testing dengan menggunakan 3 worker, 2 worker, dan 1 worker sebanyak 100 request dengan 10 request/second, kemudian tambahkan grafiknya pada grimoire.

Konfigurasi dari `Eisen:/etc/nginx/sites-available/channel.a01.com` dapat diubah menjadi berikut.

```nginx
# Round Robin
upstream worker_round_robin {
        server 192.169.3.1; # IP Lugner
        server 192.169.3.2; # IP Linie
        server 192.169.3.3; # IP Lawine
}

# Weighted Round Robin
upstream worker_weighted_round_robin {
        server 192.169.3.1 weight=4; # IP Lugner
        server 192.169.3.2 weight=2; # IP Linie
        server 192.169.3.3 weight=1; # IP Lawine
}

# Least Connection
upstream worker_least_conn {
        least_conn;
        server 192.169.3.1; # IP Lugner
        server 192.169.3.2; # IP Linie
        server 192.169.3.3; # IP Lawine
}

# IP Hash
upstream worker_ip_hash {
        ip_hash;
        server 192.169.3.1; # IP Lugner
        server 192.169.3.2; # IP Linie
        server 192.169.3.3; # IP Lawine
}

# Generic Hash
upstream worker_generic_hash {
        hash $request_uri consistent;
        server 192.169.3.1; # IP Lugner
        server 192.169.3.2; # IP Linie
        server 192.169.3.3; # IP Lawine
}

server {
        listen 80;

        server_name channel.a01.com;

        location / {
                proxy_pass http://worker_round_robin;
        }

        location ~ /\.ht {
                deny all;
        }

        error_log /var/log/nginx/channel.a01.com-error.log;
        access_log /var/log/nginx/channel.a01.com-access.log;
}
```

Testing dapat dilakukan dengan mengubah `proxy_pass` pada `location /` agar menggunakan algoritma yang berbeda. Hasil testing dapat diakses [di sini](assets/A01_Grimoire.pdf).

## Soal 10, 11, 12

> Selanjutnya coba tambahkan konfigurasi autentikasi di LB dengan dengan kombinasi username: “netics” dan password: “ajkyyy”, dengan yyy merupakan kode kelompok. Terakhir simpan file “htpasswd” nya di /etc/nginx/rahasisakita/

> Lalu buat untuk setiap request yang mengandung /its akan di proxy passing menuju halaman https://www.its.ac.id.

> Selanjutnya LB ini hanya boleh diakses oleh client dengan IP [Prefix IP].3.69, [Prefix IP].3.70, [Prefix IP].4.167, dan [Prefix IP].4.168.

Untuk melakukan semua pengecekan tersebut, konfigurasi pada `Eisen:/etc/nginx/sites-available/channel.a01.com` dapat diubah menjadi sebagai berikut.

```nginx
# Round Robin
upstream worker_round_robin {
        server 192.169.3.1; # IP Lugner
        server 192.169.3.2; # IP Linie
        server 192.169.3.3; # IP Lawine
}

# Weighted Round Robin
upstream worker_weighted_round_robin {
        server 192.169.3.1 weight=4; # IP Lugner
        server 192.169.3.2 weight=2; # IP Linie
        server 192.169.3.3 weight=1; # IP Lawine
}

# Least Connection
upstream worker_least_conn {
        least_conn;
        server 192.169.3.1; # IP Lugner
        server 192.169.3.2; # IP Linie
        server 192.169.3.3; # IP Lawine
}

# IP Hash
upstream worker_ip_hash {
        ip_hash;
        server 192.169.3.1; # IP Lugner
        server 192.169.3.2; # IP Linie
        server 192.169.3.3; # IP Lawine
}

# Generic Hash
upstream worker_generic_hash {
        hash $request_uri consistent;
        server 192.169.3.1; # IP Lugner
        server 192.169.3.2; # IP Linie
        server 192.169.3.3; # IP Lawine
}

server {
        listen 80;

        server_name channel.a01.com;

        location / {
                proxy_pass http://worker_round_robin;

                auth_basic "Praktikum Jarkom";
                auth_basic_user_file /etc/nginx/rahasisakita/.htpasswd;

                allow 192.169.3.69;
                allow 192.169.3.70;
                allow 192.169.4.167;
                allow 192.169.4.168;
                deny all;
        }

        location ~ /\.ht {
                deny all;
        }

        location ~ .*its*. {
                proxy_pass https://www.its.ac.id;
        }

        error_log /var/log/nginx/channel.a01.com-error.log;
        access_log /var/log/nginx/channel.a01.com-access.log;
}
```

Untuk menggunakan autentikasi dapat menambahkan dulu kredensialnya di `/etc/nginx/rahasisakita/.htpasswd` dengan menggunakan perintah berikut.

```sh
htpasswd -cb /etc/nginx/rahasisakita/.htpasswd netics ajka01
```

## Soal 13

> Semua data yang diperlukan, diatur pada Denken dan harus dapat diakses oleh Frieren, Flamme, dan Fern.

Berikut adalah konfigurasi dari `Denken:/etc/mysql/my.cnf`.

```sh
[client-server]

!includedir /etc/mysql/conf.d/
!includedir /etc/mysql/mariadb.conf.d/

[mysqld]

skip-networking=0
skip-bind-address
```

Konfigurasi di atas digunakan agar Database Server dapat diakses dari host lain.

Kemudian dapat dilakukan pembuatan user dan juga database yang ingin digunakan dengan menggunakan SQL berikut.

```sql
-- ~/init.sql

CREATE USER IF NOT EXISTS 'kelompoka01'@'%' IDENTIFIED BY 'passworda01';
CREATE DATABASE IF NOT EXISTS dbkelompoka01;
GRANT ALL PRIVILEGES ON dbkelompoka01.* TO 'kelompoka01'@'%';
FLUSH PRIVILEGES;
```

SQL tersebut dapat diotomasi pemanggilannya dengan menggunakan perintah berikut.

```sh
mysql < ~/init.sql
```

## Soal 14

> Frieren, Flamme, dan Fern memiliki Riegel Channel sesuai dengan [quest guide](https://github.com/martuafernando/laravel-praktikum-jarkom) berikut. Jangan lupa melakukan instalasi PHP8.0 dan Composer!

Dapat dikonfigurasi menggunakan web server nginx.

Berikut adalah konfigurasi dari `(Laravel Worker):/etc/nginx/sites-available/default`.

```nginx
server {
        listen 80;

        root /var/www/riegel.canyon.a01.com/public;

        index index.html index.htm index.nginx-debian.html index.php;

        server_name _;

        location / {
                try_files $uri $uri/ /index.php?$query_string;
        }

        location ~ \.php$ {
                include snippets/fastcgi-php.conf;
                fastcgi_pass unix:/run/php/php8.0-fpm-trial-1.sock;
        }

        location ~ /\.ht {
                deny all;
        }

        error_log /var/log/nginx/error.log;
        access_log /var/log/nginx/access.log;
}
```

Untuk project `riegel.canyon.a01.com` dapat di akses pada resource di soal. Selain itu perlu dikonfigurasi file `.env` menjadi seperti ini.

```sh
APP_NAME=Laravel
APP_ENV=local
APP_KEY=base64:3+qUQkP8RHTWkFFAUl8L8o7otiTxUaTWTgHvvTVdb5g=
APP_DEBUG=true
APP_URL=http://localhost

LOG_CHANNEL=stack
LOG_DEPRECATIONS_CHANNEL=null
LOG_LEVEL=debug

DB_CONNECTION=mysql
DB_HOST=192.169.2.1
DB_PORT=3306
DB_DATABASE=dbkelompoka01
DB_USERNAME=kelompoka01
DB_PASSWORD=passworda01

BROADCAST_DRIVER=log
CACHE_DRIVER=file
FILESYSTEM_DISK=local
QUEUE_CONNECTION=sync
SESSION_DRIVER=file
SESSION_LIFETIME=120

MEMCACHED_HOST=127.0.0.1

REDIS_HOST=127.0.0.1
REDIS_PASSWORD=null
REDIS_PORT=6379

MAIL_MAILER=smtp
MAIL_HOST=mailpit
MAIL_PORT=1025
MAIL_USERNAME=null
MAIL_PASSWORD=null
MAIL_ENCRYPTION=null
MAIL_FROM_ADDRESS="hello@example.com"
MAIL_FROM_NAME="${APP_NAME}"

AWS_ACCESS_KEY_ID=
AWS_SECRET_ACCESS_KEY=
AWS_DEFAULT_REGION=us-east-1
AWS_BUCKET=
AWS_USE_PATH_STYLE_ENDPOINT=false

PUSHER_APP_ID=
PUSHER_APP_KEY=
PUSHER_APP_SECRET=
PUSHER_HOST=
PUSHER_PORT=443
PUSHER_SCHEME=https
PUSHER_APP_CLUSTER=mt1

VITE_PUSHER_APP_KEY="${PUSHER_APP_KEY}"
VITE_PUSHER_HOST="${PUSHER_HOST}"
VITE_PUSHER_PORT="${PUSHER_PORT}"
VITE_PUSHER_SCHEME="${PUSHER_SCHEME}"
VITE_PUSHER_APP_CLUSTER="${PUSHER_APP_CLUSTER}"

JWT_SECRET=1Yz1qEr5FdHpk4CdcAC4LfXsbTOm2YEiNEptGiNFatcdDbCfURNWCTwbES0kH18f
```

File `.env` tersebut harus sama pada semua worker agar aplikasi laravel dapat tersinkron pada semua workernya. `JWT_SECRET` dan `APP_KEY` didapatkan dari `php artisan jwt:secret` dan juga `php artisan key:generate` pada direktori aplikasi `/var/www/riegel.canyon.a01.com`. Migrasi dan juga seeding database hanya perlu dilakukan pada salah satu worker saja.

## Soal 15, 16, 17

> Riegel Channel memiliki beberapa endpoint yang harus ditesting sebanyak 100 request dengan 10 request/second. Tambahkan response dan hasil testing pada grimoire.

> POST /auth/register (15)

> POST /auth/login (16)

> GET /me (17)

Untuk perintah dan hasil testing dapat diakses [di sini](assets/A01_Grimoire.pdf).

## Soal 18

> Untuk memastikan ketiganya bekerja sama secara adil untuk mengatur Riegel Channel maka implementasikan Proxy Bind pada Eisen untuk mengaitkan IP dari Frieren, Flamme, dan Fern.

Berikut adalah konfigurasi dari `Eisen:/etc/nginx/sites-available/canyon.a01.com`.

```nginx
upstream worker {
        server 192.169.4.1; # IP Fern
        server 192.169.4.2; # IP Flamme
        server 192.169.4.3; # IP Frieren
}

server {
        listen 80;

        server_name canyon.a01.com;

        location / {
                proxy_bind 192.169.2.2; # IP Load Balancer Eisen
                proxy_pass http://worker;
        }

        location ~ /\.ht {
                deny all;
        }

        error_log /var/log/nginx/canyon.a01.com-error.log;
        access_log /var/log/nginx/canyon.a01.com-access.log;
}
```

Karena pada soal diperintahkan untuk menggunakan `proxy_bind` maka akan ditambahkan `proxy_bind` untuk interface `eth0` dari Eisen, yaitu IP `192.169.2.2`.

## Soal 19

> Untuk meningkatkan performa dari Worker, coba implementasikan PHP-FPM pada Frieren, Flamme, dan Fern. Untuk testing kinerja naikkan

> - pm.max_children
> - pm.start_servers
> - pm.min_spare_servers
> - pm.max_spare_servers

> sebanyak tiga percobaan dan lakukan testing sebanyak 100 request dengan 10 request/second kemudian berikan hasil analisisnya pada Grimoire.

Akan dibuat tiga konfigurasi php-fpm untuk tiga kali percobaan dengan detail masing masing berikut.

Berikut adalah konfigurasi dari `(Laravel Worker):/etc/php/8.0/fpm/pool.d/trial-1.conf`.

```sh
[trial_1]

user = www-data
group = www-data

listen = /var/run/php/php8.0-fpm-trial-1.sock
listen.owner = www-data
listen.group = www-data

php_admin_value[disable_functions] = exec,passthru,shell_exec,system
php_admin_flag[allow_url_fopen] = off

pm = dynamic
pm.max_children = 5
pm.start_servers = 2
pm.min_spare_servers = 1
pm.max_spare_servers = 3
```

Berikut adalah konfigurasi dari `(Laravel Worker):/etc/php/8.0/fpm/pool.d/trial-2.conf`.

```sh
[trial_2]

user = www-data
group = www-data

listen = /var/run/php/php8.0-fpm-trial-2.sock
listen.owner = www-data
listen.group = www-data

php_admin_value[disable_functions] = exec,passthru,shell_exec,system
php_admin_flag[allow_url_fopen] = off

pm = dynamic
pm.max_children = 10
pm.start_servers = 4
pm.min_spare_servers = 2
pm.max_spare_servers = 6
```

Berikut adalah konfigurasi dari `(Laravel Worker):/etc/php/8.0/fpm/pool.d/trial-3.conf`.

```sh
[trial_3]

user = www-data
group = www-data

listen = /var/run/php/php8.0-fpm-trial-3.sock
listen.owner = www-data
listen.group = www-data

php_admin_value[disable_functions] = exec,passthru,shell_exec,system
php_admin_flag[allow_url_fopen] = off

pm = dynamic
pm.max_children = 20
pm.start_servers = 8
pm.min_spare_servers = 4
pm.max_spare_servers = 12
```

Untuk perintah dan hasil testing dapat diakses [di sini](assets/A01_Grimoire.pdf).

## Soal 20

> Nampaknya hanya menggunakan PHP-FPM tidak cukup untuk meningkatkan performa dari worker maka implementasikan Least-Conn pada Eisen. Untuk testing kinerja dari worker tersebut dilakukan sebanyak 100 request dengan 10 request/second.

Dapat diubah konfigurasi dari `Eisen:/etc/nginx/sites-available/canyon.a01.com` menjadi seperti berikut.

```nginx
upstream worker {
        least_conn;
        server 192.169.4.1; # IP Fern
        server 192.169.4.2; # IP Flamme
        server 192.169.4.3; # IP Frieren
}

server {
        listen 80;

        server_name canyon.a01.com;

        location / {
                proxy_bind 192.169.2.2; # IP Load Balancer Eisen
                proxy_pass http://worker;
        }

        location ~ /\.ht {
                deny all;
        }

        error_log /var/log/nginx/canyon.a01.com-error.log;
        access_log /var/log/nginx/canyon.a01.com-access.log;
}
```

Untuk perintah dan hasil testing dapat diakses [di sini](assets/A01_Grimoire.pdf).
