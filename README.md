# FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833
Repository ini berisi Dokumentasi Instalasi dan Konfigurasi berbagai layanan Server, seperti NTP, DNS, SSH, WEB, Mail Server dll. Beberapa Service yang dijelaskan dalam Repository ini masih dalam proses pengembangan, meaning masih ada beberap service yang progressnya masih 70% jadi, kedepannya akan dikembangkan lagi.

**Update**
Beberapa Service yang telah saya install disini antara lain ada Web Server dengan Platform Wordpress, WebMail, SSL(Self-Certificate), Database Server. Progress pada saat ini adalah 95%

## DAFTAR ISI
1. [Instalasi dan Konfigurasi SSH Server](#1-Instalasi-dan-Konfigurasi-SSH-Server)
2. [Instalasi dan Konfigurasi DNS Server](#2-Instalasi-dan-Konfigurasi-DNS-Server)
3. [Instalasi dan Konfigurasi NTP Server](#3-Instalasi-dan-Konfiguras-NTP-Server)
4. [Instalasi dan Konfigurasi WEB Server](#4-Instalasi-dan-Konfigurasi-WEB-Server)
5. [Instalasi dan Konfigurasi Mail Server](#5-Instalasi-dan-Konfigurasi-Mail-Server)
6. [Instalasi dan Konfigurasi Database Server](#6-Instalasi-dan-Konfigurasi-Database-Server)
7. [Instalasi dan Konfigurasi WebMail](#7-Instalasi-dan-Konfigurasi-WebMail)

## 1. Instalasi dan Konfigurasi SSH Server
#### 1.1 Konfigurasi SSH
**Langkah 1: Buka dan konfigurasi file conf menggunakan text editor(nano/vi). Dikarenakan OpenSSH sudah terinstall Default pada CentOS 7, kita hanya akan langsung mengkonfigurasinya.**
```
nano /etc/ssh/sshd_config
```
**Langkah 2: Edit konfigurasi seperti dibawah ini:**
```
Pada line 38: hilangkan tanda # dan ubah "PermitRootLogin yes" ke "no"
```
**Langkah 3: Restart SSH**
```
systemctl restart sshd
```
#### 1.2 Test Konfigurasi
**Langkah 1: Test Koneksi**
```
ssh root@ip_address -p 22
```
**Disini saya menggunakan CMD dari Windows 10, karena Windows 10 sendiri sudah mendukung koneksi SSH jadi tidak perlu menggunakan software pihak ketiga lainnya. Dan "ip_address" disini adalah IP dari CentOS 7 yang digunakan.**
## 2. Instalasi dan Konfigurasi DNS Server
#### 2.1 Instalasi DNS Server
**Langkah 1: Instalasi BIND**
```
yum -y install bind bind-utils
```
#### 2.2 Konfigurasi DNS Server

**Langkah 1: Konfigurasi file named.conf seperti dibawah ini**

![Capture5](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/69acf65a-8d88-4816-be3e-0532cd405e51)

**Disini saya menambahkan IP forwarder dengan menggunakan IP Gateway dan DNS Public 8.8.8.8 fungsinya ketika Client menggunakan IP DNS Lokal, maka Client masih dapat mengakses ke Internet. Silahkan sesuaikan dengan IP DNS masing-masing**

![Capture7](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/0d1c0a37-4362-49a5-a9d9-f1f26512524e)

**Selanjutnya, saya akan menambahkan dua file Zone yaitu Forward Zone File dan Reverse Zone. Untuk nama domain disini saya menggunakan nama saya "georelbonai.com". Silahkan sesuaikan dengan nama domain masing-masing.**

![Capture8](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/43ce9bae-441c-469b-867a-23c603079152)

**Langkah 2: Buat file baru untuk forward zone**
```
nano /var/named/db.georel.com
```
**Bebas untuk menamainya, disana saya menggunakan nama**

**Lalu tambahkan script seperti dibawah ini**

![Capture9](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/98acc4c5-3d23-4566-899b-576434ea9331)

**Langkah 3: Buat file reverse zone**

**Selanjutnya, buat file reverse zone dengan nama db.ip_reverse pada direktori /var/named**
```
nano /var/named/db.56.168.192
```

**Tambahkan script seperti dibawah ini:**

![Capture10](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/ab57fe36-d9da-4458-94dd-8ff6ae0f590e)

**Langkah 4: Simpan Konfigurasi dan Jalankan Service DNS**

**Masukkan Perintah ini:**
```
systemctl start named
```
```
systemctl enable named
```
**Cek juga status Service DNS Server, pastikan service berjalan dengan baik**

![Capture12](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/19332ecd-3b0c-41ae-b7a8-44782034e7e9)

#### 2.3 Tambahkan Firewall
**Langkah 1: Tambahkan Firewall pada Service DNS**
```
firewall-cmd --add-service=dns --permanent
```
```
firewall-cmd --reload
```
#### 2.4 Cek Settingan resolv.conf
**Langkah 1: Cek juga settingan resolv.conf, pastikan nameserver sudah diisi dengan IP Address Server**
```
nano /etc/resolv.conf
```
![Capture15](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/fbf57214-3e22-4750-94b7-47eb2fc24c17)

#### 2.5 Pengetesan
**Langkah 1: Lakukan pengetesan**

**Untuk pengetesan silahkan jalankan perintah nslookup nama domain, jika berhasil maka nama domain akan di translasikan ke alamat IP Address Server**

![Capture16](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/f2c42046-d328-48c7-b313-0ebe0cf26eaa)

**Lalu test ping ke nama Domain dari Laptop Host**

![Capture25](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/e5e6df0e-b737-41fc-aae6-3d952839dfa0)

## 3. Instalasi dan Konfigurasi NTP Server
#### 3.1 Instalasi dan Konfigurasi Chrony
**Langkah 1: Install Chrony**
```
yum -y install chrony
```
**Langkah 2: Backup file utama chrony**
```
cp /etc/chrony.conf /etc/chrony.conf.backup
```

**Langkah 3: Buka file chrony.conf lalu edit**

**Buka file chrony.conf lalu edit seperti dibawah ini:**

![Capture5](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/e5c5300e-715f-4453-9462-98d6ce6f19f4)
**Disana saya menggunakan server id dari Indonesia agar aturan Waktu akan mengikuti Waktu Indonesia**

**Langkah 4: Simpan konfigurasi lalu jalankan service chronyd**
```
systemctl start chronyd
```
```
systemctl enable chronyd
```
**Cek juga status service Chrony**

![Capture7](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/b7fe5a3c-fad1-4d33-8da4-39a269c315ae)
**Langkah 5: Tambahkan Firewall pada Chrony**
```
firewall-cmd --add-service=ntp --permanent
```
```
firewall-cmd --reload
```
#### 3.2 Pengetesan
**Langkah 1: Jalankan perintah chronyc sources**

![Capture9](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/0c9d9a74-6416-4ff7-85a2-b319a3dfdc2b)

#### 3.3 Pengetesan pada Client
**Langkah 1: Install NTPd**
```
yum -y install ntpdate
```
**Langkah 2: Tambahkan Firewall**
```
firewall-cmd --add-service=ntp --permanent
```
```
firewall-cmd --reload
```
**Langkah 3: Sinkronisasi NTP Server**

**Untuk mensinkronisasi ke NTP Server, silahkan jalankan perintah berikut.**

![Capture4](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/b23c1130-11b9-45a0-a132-994f7f2d3e8a)

**Langkah 4: Aktifkan Service NTPdate**
```
systemctl enable ntpdate
```
#### 3.4 Pengetesan pada Windows Client
**Langkah 1: Pengetesan pada Windows Client**

**Untuk pengetesan pada Windwos Client, silahkan buka Setting Time Zone pada Windows lalu masukkan IP Address Server dan Klik "Update". Jika berhasil, maka PC Client akan "Syncronized" dengan Server.**

![Capture6](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/fc887727-501d-4141-a3ae-5e10b5fb40e7)

![Capture7](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/67715a73-bf9a-41d9-9512-d4304ba6a6d0)

## 4. Instalasi dan Konfigurasi WEB Server
#### 4.1 Instalasi dan Konfigurasi HTTPd
**Langkah 1: Install HTTPd**
```
yum -y install httpd
```
**Langkah 2: Konfigurasi HTTPD Web Server**

**Untuk file konfigurasi utama httpd dengan nama httpd.conf yang berada pada direktori /etc/httpd/conf/ Silahkan buka file tersebut dengan menggunakan text editor kesukaan anda.**
```
nano /etc/httpd/conf/httpd.conf
```
**Lalu edit file tersebut seperti dibawah ini:**

![Capture4](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/21a008bf-cf5c-499e-b7f1-b501b7119603)

![Capture5](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/17816ed9-9c56-4642-95a9-8e7508bfa543)

**Langkah 3: Simpan Konfigurasi dan aktifkan service HTTPd**
```
systemctl start httpd
```
```
systemctl enable httpd
```

**Langkah 4: Tambahkan Firewall**
```
firewall-cmd --add-service=http --permanent
```
```
firewall-cmd --reload
```

#### 4.2 Pengetesan
**Langkah 1: Test pada PC Client**

![Capture24](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/80a54ef5-3b40-4735-be2b-82adc75e558c)

**Dikarenakan saya telah membuat DNS maka, saya dapat memanggil WEB Server dengan Domain saya**

#### 4.3 Membuat Website Sederhana
**Selanjutnya kita akan coba membuat sebuah website sederahan, untuk penempatan file website yang kita buat ada pada direktori /var/www/html/ disinilah tempat untuk meletakan file website yang kita buat. Sebagai contoh, kita akan membuat satu folder dengan nama website1 lalu di dalam folder tersebut kita buat satu buah file dengan nama index.html lalu kita akan masukkan script HTML sederhana. Silahkan ikuti langkah-langkahnya seperti dibawah ini:**

**Langkah 1: Menghapus file Welcome Page**
```
rm -f /etc/httpd/conf.d/welcome.conf
```
**Langkah 2: Membuat file Welcome page baru**
```
cd /var/www/html/
```
```
mkdir website1
```
```
cd website1/
```
```
nano index.html
```
**Masukkan Script HTML sederhana dibawah ini:**
```
<!doctype html>
<html>
<head>
    <title>22.83.0833</title>

    <meta charset="utf-8" />
    <meta http-equiv="Content-type" content="text/html; charset=utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <style type="text/css">
    body {
        background-color: #f0f0f2;
        margin: 0;
        padding: 0;
        font-family: -apple-system, system-ui, BlinkMacSystemFont, "Segoe UI", "Open Sans", "Helvetica Neue", Helvetica, Arial, sans-serif;
        
    }
    div {
        width: 600px;
        margin: 5em auto;
        padding: 2em;
        background-color: #fdfdff;
        border-radius: 0.5em;
        box-shadow: 2px 3px 7px 2px rgba(0,0,0,0.02);
    }
    a:link, a:visited {
        color: #38488f;
        text-decoration: none;
    }
    @media (max-width: 700px) {
        div {
            margin: 0 auto;
            width: auto;
        }
    }
    </style>    
</head>

<body>
<div>
    <h1 style="text-align: center;">www.georelbonai.com</h1>
    <p>Hello World! ini adalah halaman selamat datang standar yang digunakan untuk menguji pengoperasian 
    Server Apache2 yang benar setelah instalasi pada sistem anda. Jika Anda melihat halaman ini, berarti 
    Server Apache Anda sudah berjalan dengan baik.</p>

</div>
</body>
```

**Langkah 3: Simpan Konfigurasi dan Jalankan Service HTTPd**
```
systemctl restart httpd
```

**Langkah 4: Pengetesan**
**Untuk pengetesan silahkan akses menggunakan browser dari PC Client lalu masukan pada url http://ip_address_server/nama_folder_web jika berhasil maka akan mengarah ke website yang kita buat.**

![Capture14](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/3c9179da-e271-47fa-8b33-f0a476f1e632)

#### 4.4 Membuat VirtualHost
**Langkah 1: Membuat VirtualHost**

**Selanjutnya kita akan membuat Virtualhost, Jika sebelumnya kita mengakses website kita dengan menggunakan IP Address, maka dengan menggunakan Virtualhost kita dapat mengakses website kita dengan menggunakan nama domain. Ok silahkan masuk pada direktori /etc/httpd/conf.d/ lalu buat satu buah file dengan extention .conf**

```
cd /etc/httpd/conf.d/
```
```
nano contoh.conf
```
**Masukkan Script seperti dibawah ini:**

![Capture16](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/5f740221-033f-4b86-9421-7babf0f85234)

**Langkah 2: Restart Service HTTPd**
```
systemctl restart httpd
```
**Untuk pengetesan silahkan akses menggunakan browser dari PC Client lalu masukan pada url http://namadomain.com jika berhasil maka nama domain akan mengarah ke website yang kita buat.**

![Capture18](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/ed482a12-6bb8-4330-affb-c3fb8ee255d7)

## 5. Instalasi dan Konfigurasi Mail Server
#### 5.1 Instalasi dan Konfigurasi Postfix
**Langkah 1: Install Postfix**
```
yum -y install postfix
```

**Langkah 2: Konfigurasi Main.cf**
```
nano /etc/postfix/main.cf
```
**konfigurasi file main.cf seperti dibawah ini:**
![Capture4](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/e33da381-66c4-4401-a7d1-d6f4adeac885)
![Capture5](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/c02806db-c777-44df-b458-70dbdded212e)
![Capture6](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/14c3f324-f2eb-45ec-b231-e4a66883ffd5)
![Capture7](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/5453de69-f3ec-4589-b431-05635418636a)
![Capture8](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/ec59f0b1-04f9-4925-9ff3-eb3449d0710b)
![Capture9](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/396e2be0-c5a7-4c2d-bfe6-6151e210cd3a)
![Capture10](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/53f15cf7-133e-413c-adce-3ee7e5853ac9)

**Langkah 3: Restart dan Jalankan Service Postfix**
```
systemctl restart postfix
```
```
systemctl enable postfix
```
#### 5.2 Tambahkan Firewall pada Postfix
**Langkah 1: Tambahkan Firewall pada Postfix**
```
firewall-cmd --add-service=smtp --permanent
```
```
firewall-cmd --reload
```

#### 5.3 Instalasi dan Konfigurasi Dovecot
**Langkah 1: Install Dovecot**
```
yum -y install dovecot
```
**Langkah 2: Konfigurasi file dovecot.conf**
```
nano /etc/dovecot/dovecot.conf
```
**Ubahlah .conf seperti dibawah ini:**

![Capture16](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/f212e3d8-31c4-4721-8459-50334a133b1b)

**Langkah 3: Konfigurasi file 10-auth.conf**
```
nano /etc/dovecot/conf.d/10-auth.conf
```
**Ubahlah .conf seperti dibawha ini:**

![Capture18](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/a90f49c8-cc91-48b9-a1f6-83698ea8627e)

![Capture19](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/f840b454-c1cb-43a7-8c99-e4c8835a590a)

**Langkah 4: Konfigurasi file 10-mail.conf**
```
nano /etc/dovecot/conf.d/10-mail.conf
```
**Ubahlah .conf seperti dibawah ini:**

![Capture21](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/e2340f3f-2dfe-4560-ba05-a892aa432a61)

**Langkah 5: Konfigurasi file 10-master.conf**
```
nano /etc/dovecot/conf.d/10-master.conf
```
**Ubahlah .conf seperti dibawah in:**

![Capture23](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/aa6a4ce4-77d1-4f5d-9564-3d4a1422c5cb)

**Langkah 6: Konfigurasi file 10-ssl.conf**
```
nano /etc/dovecot/conf.d/10-ssl.conf
```
**Ubahlah .conf seperti dibawah ini**

![Capture25](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/8f7c448f-9681-4e7d-9ddb-a3db6f9a44fd)

**Langkah 7: Aktifkan dan Jalankan service Dovecot**
```
systemctl start dovecot
```
```
systemctl enable dovecot
```
#### 5.4 Tambahkan Firewall pada Dovecot
**Langkah 1: Tambahkan Firewall pada Dovecot**
```
firewall-cmd --add-port={110/tcp,143/tcp} --permanent
```
```
firewall-cmd --reload
```
#### 5.5 Pengetesan
**Langkah 1: Buat user baru**
```
useradd nama_user_baru
```
```
passwd password_baru
```
**Langkah 2: Install Mailx**
```
yum -y install mailx
```
**Langkah 3: Setting Environment Variabel untuk menggunakan Maildirectory**
```
echo 'export MAIL=$HOME/Maildir' >> /etc/profile
```
**Langkah 4: Masukkan Perintah disini untuk mengirim Pesan**
```
telnet localhost smtp #Enter
mail from: <test> #Enter
rcpt to: <jorel> #Enter
data #Enter
Hello World #Enter
. #Enter
quit #Enter
```
**Contoh**

![Capture37](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/f18ebf70-1c41-45cc-809a-b6a0a4adf447)

## 6. Instalasi dan Konfigurasi Database Server
#### 6.1 Instalasi dan Konfigurasi MariaDB
**Langkah 1: Instalasi MariaDB**
```
yum -y install mariadb-server
```
**Langkah 2: Konfigurasi MariaDB**
```
nano /etc/my.conf
```
**Ubah beberapa baris kode**
```
[mysqld]
character-set-server=utf8
```
**Langkah 3: Jalankan service MariaDB dan tambahkah Firewall**
```
systemctl start mariadb
systemctl enable mariadb
```
![Capture5](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/05923cb9-6fa2-4ea7-9e67-92a23da36c17)

**Langkah 4: Settingan keamanan untuk MariaDB**
```
mysql_secure_installation
```
**Ikut beberapa baris kode perintah seperti dibawah ini:**

![Capture7](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/03809c78-9cf2-4305-98ea-b1a16b5219ba)

#### 6.2 Instalasi dan Konfigurasi PhpMyAdmin
**Langkah 1: Instalasi PhpMyAdmin**
```
yum --enablerepo=epel -y install phpMyAdmin php-mysql php-mcrypt
```
**Langkah 2: Konfigurasi PhpMyAdmin**
```
nano /etc/httpd/conf.d/phpMyAdmin.conf
```
![Capture](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/676aa369-0fc7-4719-ad63-2252d10d5160)

**Langkah 3: Mulai kembali Service Apache**
```
systemctl restart httpd
```
#### 6.3 Pengetesan
**Langkah 1: Akses PhpMyAdmin**
```
http://hostname_or_ip/phpmyadmin/
```
![Capture2](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/d79344c6-cbf8-417f-b674-8674252cae72)

## 7. Instalasi dan Konfigurasi WebMail
#### 7.1 Instalasi dan Konfigurasi SquirrelMail

**Langkah 1: Instalasi SquirrelMail**
```
yum --enablerepo=epel -y install squirrelmail php-mysql
```
```
curl -O https://www.squirrelmail.org/plugins/compatibility-2.0.16-1.0.tar.gz
```
```
curl -O https://www.squirrelmail.org/plugins/empty_trash-2.0-1.2.2.tar.gz
```
```
curl -O https://www.squirrelmail.org/plugins/secure_login-1.4-1.2.8.tar.gz
```
```
tar zxvf compatibility-2.0.16-1.0.tar.gz -C /usr/share/squirrelmail/plugins 
```
```
tar zxvf empty_trash-2.0-1.2.2.tar.gz -C /usr/share/squirrelmail/plugins
```
```
tar zxvf secure_login-1.4-1.2.8.tar.gz -C /usr/share/squirrelmail/plugins
```
```
rm -f ./*.tar.gz
```

**Langkah 2: Jalankan Scriptnya**

**Ikut beberapa perintah baris kode seperti dibawah ini**
```
/usr/share/squirrelmail/config/conf.pl
```
**Pilih 1**

![Capture11](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/5fbcd9c8-5ef2-436d-92a6-1391c4ec59bc)

**Pilih 5, setelah itu atur Logout Page**

![Capture12](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/51d07313-9d45-4a0c-9ebd-a3b7fc1fba08)

**Pilih r, untuk kembali ke menu utama**

![Capture13](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/88acfe6c-ae7e-49d0-95d6-9535302920e3)

**Pilih 2 untuk masuk ke Settingan Server**

![Capture14](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/6a30f01f-6064-4795-8aca-e3439e442526)

**Pilih 1, dan masukkan domain di baris [Localhost]**

![Capture15](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/d77250e6-5df4-426c-9fd8-826c68dd8de1)

**Pilih 3, dan Masukkan 2(SMTP)**

![Capture16](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/f4293532-aed4-40cc-81cd-49ba332af049)

**Pilih A, untuk update IMAP Settings**

![Capture17](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/a85dd031-2b38-4a57-bb35-8edf6cc0f9f0)

**Pilih 4, untuk mengisi IMAP Server pada "[localhost]:"**

![Capture18](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/7b974bba-6faa-404e-99b5-cef7df0de24a)

**Pilih 8, dan Masukkan "dovecot" pada baris "[uw]"**

![Capture19](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/d8ab4a5f-df0b-48ca-8d01-3c8219172e31)

![Capture20](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/ea7c49ab-2210-453d-a4a1-95211a5c3d61)

**Pilih 9, dan masukkan "detect" pada "[/]"**

![Capture21](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/ad1771b5-899e-43a2-91ba-6914a72c032a)

![Capture22](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/eb9a8ee4-efe7-440f-a701-d4a31b050890)

**Pilih B, untuk mengupdate settingan SMTP**

![Capture23](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/dc32ccb3-5c8a-4cea-a2cb-69f3d531f45c)

**Pilih 4, untuk mengisi SMTP Server di "[localhost]:"**

![Capture24](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/251c1797-b7d0-41dd-a41d-a1281679b844)

**Pilih 7, dan Tekan y untuk terima, setelah itu masukkan "login" dan masukkan "N"**

![Capture25](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/cf9de928-2692-431c-90d5-bcc6261f2895)

![Capture26](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/626bf8d1-4cd7-4c77-b039-68a1caefc5ad)

**Pilih r, untuk kembali ke Menu Utama**

![Capture27](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/5aec5fd1-a89b-49c8-97e0-9f176c469664)

**Pilih 4, untuk masuk ke General Options**

![Capture28](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/da7b1ac5-50b7-4487-a4aa-9e5a8b64245c)

**Pilih 7, dan tekan "y"**

![Capture29](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/07dd5ddb-f679-4dad-8be3-8d15a6e4e17c)

**Pilih r, untuk kembali Menu Utama**

![Capture30](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/8bdb2ec1-ab95-4d1f-84fc-16598c3441c6)

**Pilih 8, untuk masuk ke menu Plugins**

![Capture31](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/c78aadef-7752-4c2a-b9c9-b5b0a26adb70)

**Pilih 7, untuk menambahkan "Compatibility"**

![Capture32](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/e44fdb89-24ef-4ed0-9d2e-cc8674e4a97f)

**Pilih 8, untuk menambahkan "Empty Trash"**

![Capture33](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/05b1892d-65b2-4673-8464-cb37212f2049)

**Pilih 15, untuk menambahkan "secure_login"**

![Capture34](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/ccfbb73c-862e-4bfa-b66b-23003d68ec94)

**Pilih q, untuk keluar dan Save**

![Capture35](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/f9eaaf0c-1116-484b-af81-49a9829b8711)

**Langkah 3: Salin config.sample.php ke config.php**
```
cp /usr/share/squirrelmail/plugins/secure_login/config.sample.php /usr/share/squirrelmail/plugins/secure_login/config.php
```

**Langkah 4: Edit file config.php**
```
nano /usr/share/squirrelmail/plugins/secure_login/config.php
```
![Capture38](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/dfaae79e-5c56-4e51-b4ba-520b5855127d)

**Langkah 5: Mulai ulang service Apache**
```
systemctl restart httpd
```

**Langkah 6: Matikan Selinux**
```
setsebool -P httpd_can_network_connect on
```
#### 7.2 Pengetesan WebMail
**Langkah 1: Akses ke WebMail kamu via https://server's name or IP/webmail/**
```
https://georelbonai.com/webmail/
```
![Capture41](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/13596905-4b91-4ef9-8978-30345148f567)
![Capture42](https://github.com/Quetzalcoatlos23/FINAL-PROJECT-OS-SERVER-SYSTEM-ADMIN---22.83.0833/assets/114808262/d96880eb-68c7-4df3-b207-bac58da03500)
