Modul 2 : DNS dan Web Server

Nama anggota :
- 05111840000093 Muhammad Afif Fadhlurrahman 
- 05111740000091 Affan Ahsanul Habib

**1. Membuat alamat http://semeruyyy.pw**
- Pada UML MALANG jalankan perintah : </br>
`nano /etc/bind/named.conf.local`
- Isi konfigurasi seperti berikut (UML MALANG) :
```
zone "semerud01.pw" {
	type master;
  notify yes;
  also-notify { 10.151.79.19 };
	file "/etc/bind/jarkom/semerud01.pw";
};
```
![gambar MALANG.named_conf_local.PNG](img/MALANG.named_conf_local.PNG)
- Isi konfigurasi pada file `/etc/bind/jarkom/semerud01.pw` seperti berikut (UML MALANG) : </br>
![gambar MALANG_semerud01_pw.PNG](img/MALANG_semerud01_pw.PNG)
- Setelah selesai setting maka kita restart bind9 dengan perintah (UML MALANG) </br>
`service bind9 restart`
- Kemudian atur nameserver pada client (contoh pada UML GRESIK) </br>
![gambar GRESIK_resolve_conf](img/GRESIK_resolv_conf.PNG)
- Testing (UML GRESIK) </br>
![gambar hasil ping di gresik](img/ping_gresik_no1.PNG)

**2. alamat http://semeruyyy.pw memiliki alias http://www.semeruyyy.pw**
- Isi konfigurasi pada file `/etc/bind/jarkom/semerud01.pw` seperti berikut (UML MALANG) : </br> 
*yang perlu diperhatikan adalah record CNAME agar kita bisa membuat alias </br>
![gambar MALANG_semerud01_pw.PNG ](img/MALANG_semerud01_pw.PNG)
- Kemudian kita restart bind9 dengan perintah (UML MALANG) </br>
`service bind9 restart`
- Cek dengan melakukan host -t CNAME www.semeruyyy.pw. Hasilnya harus mengarah ke host dengan IP MALANG. </br>
![gambar hasil ping di gresik](img/ping_gresik_no2.PNG)

**3. subdomain http://penanjakan.semeruyyy.pw yang diatur DNS-nya pada MALANG dan mengarah ke IP Server PROBOLINGGO**
- Lihat konfigurasi seperti pada gambar (file /etc/bind/jarkom/semerud01.pw) di UML MALANG, apakah sudah ada record A penanjakan pada filenya, karena hal tersebut yang menjadikan kita bisa membuat subdomain `penanjakan.semerud01.pw` </br>
![gambar MALANG_semerud01_pw.PNG ](img/MALANG_semerud01_pw.PNG)
- Lakukan perintah `service bind9 restart` pada UML MALANG
- Kemudian lakukan perintah `host -t A penanjakan.semerud01.pw` pada UML GRESIK </br>
![gambar hasil ping di gresik](img/ping_gresik_no3.PNG)

**4. Membuat reverse domain untuk domain utama.**
- Buka file menggunakan perintah `nano /etc/bind/named.conf.local` pada UML MALANG
- Perhatikan konfigurasi berikut apakah sudah ada di dalam file named.conf.local
```
zone "79.151.10.in-addr.arpa" {
    type master;
    file "/etc/bind/jarkom/79.151.10.in-addr.arpa";
};
```
- Jalankan perintah `cp /etc/bind/db.local /etc/bind/jarkom/79.151.10.in-addr.arpa` pada UML MALANG
- Perhatikan dan edit sesuai pada gambar </br>
![gambar MALANG_addr_arpa](img/MALANG_addr_arpa.PNG)
- Lakukan perintah `service bind9 restart` pada UML MALANG </br>
- Jalankan perintah `host -t PTR 10.151.79.20` </br>
![gambar hasil ping di gresik](img/ping_gresik_no4.PNG)

**5. Membuat DNS Server Slave pada MOJOKERTO**
- Edit file /etc/bind/named.conf.local dan sesuaikan dengan syntax berikut pada UML MALANG
```
zone "semerud01.pw" {
	type master;
  	notify yes;
 	also-notify { 10.151.79.19 };
  	allow-transfer { 10.151.79.19 };
	file "/etc/bind/jarkom/semerud01.pw";
};
```
- Lakukan perintah `service bind9 restart` pada UML MALANG
- Kemudian buka file /etc/bind/named.conf.local pada MOJOKERTO dan tambahkan syntax berikut:
```
zone "semerud01.pw" {
    type slave;
    masters { 10.151.79.18 }; 
    file "/var/lib/bind/semerud01.pw";
};
```
- Lakukan perintah `service bind9 restart` pada UML MOJOKERTO
- Pada server MALANG matikan bind9 dengan perintah `service bind9 stop`
- Pada client GRESIK pastikan pengaturan nameserver mengarah ke IP MALANG dan IP MOJOKERTO </br>
![gambar GRESIK_resolv_conf.PNG](img/GRESIK_resolv_conf.PNG)
- Lakukan ping ke semerud01.pw pada client GRESIK. </br>
![gambar hasil ping di gresik](img/ping_gresik_no5.PNG)

**6. Membuat subdomain dengan alamat http://gunung.semeruyyy.pw yang didelegasikan pada server MOJOKERTO dan mengarah ke IP Server PROBOLINGGO.**
- Edit file dengan menggunakan perintah `nano /etc/bind/jarkom/semerud01.pw`, perhatikan record NS pastikan hal tersebut sudah sesuai (UML MALANG) </br>
![gambar MALANG_semerud01_pw.PNG ](img/MALANG_semerud01_pw.PNG)
- Edit file dengan menggunakan perintah `nano /etc/bind/named.conf.options`
- Kemudian comment `dnssec-validation auto;` dan tambahkan baris berikut pada `/etc/bind/named.conf.options` </br>
`allow-query{any;};` </br>
![gambar MALANG_named.conf.options ](img/no6_conf_options.PNG)
- Kemudian edit file /etc/bind/named.conf.local menjadi seperti gambar di bawah :
```
zone "semerud01.pw" {
	type master;
  	notify yes;
 	also-notify { 10.151.79.19 };
  	allow-transfer { 10.151.79.19 };
	file "/etc/bind/jarkom/semerud01.pw";
};
```
![gambar MALANG.named_conf_local.PNG](img/MALANG.named_conf_local.PNG)
- Kemudian lakukan perintah `service bind9 restart` pada UML MALANG
- Lakukan perintah `nano /etc/bind/named.conf.options` pada UML MOJOKERTO
- Kemudian comment `dnssec-validation auto;` dan tambahkan baris berikut pada `/etc/bind/named.conf.options` </br>
`allow-query{any;};` </br>
![gambar uml mojokerto file /etc/bind/named.conf.options](img/no6_MOJOKERTO_conf_options.PNG)
- Lalu edit file `/etc/bind/named.conf.local` menjadi seperti gambar di bawah: </br>
![gambar MOJOKERTO_namde_conf_local.PNG](img/MOJOKERTO_namde_conf_local.PNG)
- lakukan perintah `cp /etc/bind/db.local /etc/bind/delegasi/gunung.semerud01.pw`, setelah sebelumnya telah membuat directory delegasi di dalam directory /etc/bind
- Kemudian edit file gunung.semerud01.pw menjadi seperti dibawah ini </br>
![gambar MOJOKERTO_delegasi_gunung_semeru_pw.PNG](img/MOJOKERTO_delegasi_gunung_semeru_pw.PNG)
- Kemudian restart bind9 dengan perintah `service bind9 restart` pada UML MOJOKERTO
- Testing ping gunung.semerud01.pw dari UML GRESIK </br>
![gambar hasil ping di gresik](img/ping_gresik_no6.PNG)

**7. Membuat subdomain dengan nama http://naik.gunung.semeruyyy.pw, domain ini diarahkan ke IP Server PROBOLINGGO.**
- Edit file dengan perintah `nano /etc/bind/delegasi/gunung.semerud01.pw` pada UML MOJOKERTO
![gambar /etc/bind/delegasi/gunung.semerud01.pw](img/no7.delegasi.gunung.semerud01.pw.PNG)
- Lalu lakukan perintah `service bind9 restart` pada UML MOJOKERTO
- Testing ping naik.gunung.semerud01.pw dari UML GRESIK </br>
![gambar hasil ping di gresik](img/ping_gresik_no7.PNG)
