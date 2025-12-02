# Panduan Instalasi EPrints 3.4.5 di Ubuntu/Debian

## 1. Update Sistem

Update package list dan upgrade sistem:
```bash
sudo apt update
sudo apt upgrade -y
```

## 2. Instalasi Apache Web Server

Install Apache2:
```bash
sudo apt install apache2 -y
```

Verifikasi Apache berjalan dengan baik:
```bash
sudo systemctl status apache2
```

## 3. Instalasi Dependensi yang Diperlukan

### 3.1 Instalasi Build Tools dan Library
```bash
sudo apt install wget curl build-essential libxml2-dev libgd-dev libapache2-mod-perl2 git -y
```

### 3.2 Instalasi Perl dan Modul Inti
```bash
sudo apt install perl libperl-dev cpanminus -y
```

### 3.3 Instalasi Dependensi Modul Perl
```bash
sudo apt install libxml-libxml-perl libxml-sax-perl libxml-parser-perl \
libterm-readkey-perl libunicode-string-perl libmime-lite-perl \
libmime-types-perl libdigest-sha-perl libdbi-perl libdbd-mysql-perl \
libtext-unidecode-perl libjson-perl libdata-dumper-simple-perl \
libsearch-xapian-perl libio-string-perl liblingua-stem-perl -y
```

### 3.4 Instalasi Modul Perl Tambahan via CPAN
```bash
sudo cpanm CGI
sudo cpanm XML::LibXSLT
sudo cpanm Archive::Zip
sudo cpanm MIME::Base64
```

> ** Troubleshooting:** Jika `XML::LibXSLT` gagal dikonfigurasi (error versi 2.003000), install dari repository apt:
> ```bash
> sudo apt install libxml-libxslt-perl -y
> ```

## 4. Download dan Instalasi EPrints

### 4.1 Masuk ke Direktori /opt
```bash
cd /opt
```

### 4.2 Download EPrints 3.4.5
```bash
sudo wget https://github.com/eprints/eprints3.4/archive/refs/tags/v3.4.5.tar.gz
```

### 4.3 Ekstrak File
```bash
sudo tar -xzf v3.4.5.tar.gz
```

### 4.4 Rename Direktori
```bash
sudo mv eprints3.4-3.4.5 eprints3
```

### 4.5 Verifikasi Instalasi
```bash
ls -l | grep eprints3
```

### 4.6 Masuk ke Direktori EPrints
```bash
cd eprints3
```

## 5. Buat User EPrints dan Set Permission

### 5.1 Buat User Sistem EPrints
```bash
sudo useradd -r -m -d /opt/eprints3 eprints
```

### 5.2 Set Ownership Direktori
```bash
sudo chown -R eprints:eprints /opt/eprints3
```

### 5.3 Verifikasi Perubahan Ownership
```bash
ls -l | grep eprints3
```

Pastikan owner berubah dari `root root` menjadi `eprints eprints`

### 5.4 Verifikasi Binary epadmin Ada
```bash
ls -la /opt/eprints3/bin/
```

Cari file `epadmin` dalam list

## 6. Setup Password Root MariaDB/MySQL

### 6.1 Masuk ke MySQL
```bash
mysql
```

### 6.2 Jalankan Perintah SQL Berikut
```sql
ALTER USER 'root'@'localhost' IDENTIFIED BY 'PasswordBaru123!';
FLUSH PRIVILEGES;
GRANT ALL PRIVILEGES ON *.* TO 'root'@'localhost' WITH GRANT OPTION;
FLUSH PRIVILEGES;
EXIT;
```

### 6.3 Test Password
```bash
mysql -u root -p
```

Masukkan password yang baru dibuat, lalu exit.

## 7. Buat Repository EPrints

### 7.1 Ganti ke User EPrints
```bash
sudo su - eprints
```

### 7.2 Masuk ke Direktori EPrints
```bash
cd /opt/eprints3
```

### 7.3 Jalankan Wizard Pembuatan Repository
```bash
./bin/epadmin create pub
```

### 7.4 Jawaban Konfigurasi Wizard

Ikuti prompt wizard dengan nilai-nilai berikut:

| Pertanyaan | Jawaban |
|------------|---------|
| Archive ID? | `Eprints1` |
| Configure vital settings? | `yes` |
| Hostname? | `eprints.local` |
| Webserver Port? | `80` |
| Alias? | *[Tekan Enter]* |
| Path? | `/` |
| HTTPS Hostname? | *[Tekan Enter]* |
| Administrator Email? | `wicaksonob208@gmail.com` *(Gunakan Email Anda)* |
| Archive Name? | `Test Repository` |
| Organisation Name? | `UIN Jakarta` |
| Configure database? | `yes` |
| Database Name? | `Eprints1` |
| MySQL Host? | `localhost` |
| MySQL Port? | `#` *(Untuk No Setting)* |
| MySQL Socket? | `#` *(Untuk No Setting)* |
| Database User? | `Eprints1` |
| Database Password? | *[Masukkan Password]* |
| Database Engine? | `InnoDB` |
| Write these database settings? | `yes` |
| Create database 'Eprints1'? | `yes` |
| Database Superuser Username? | `root` |
| Database Superuser Password? | *[Password root MySQL]* |
| Create database tables? | `yes` |
| Create an initial user? | `yes` |
| Enter a username? | `bagas` |
| Select a user type? | `admin` |
| Enter Password? | *[Buat Password Admin]* |
| Email? | `wicaksonob208@gmail.com` *(Gunakan Email Anda)* |
| Build static web pages? | `yes` |
| Import LOC subjects and sample divisions? | `yes` |
| Update Apache config files? | `yes` |

## 8.Konfigurasi Apache
### 8.1 Konfigurasi Nano Apache
```bash
sudo nano /etc/apache2/apache2.conf
```
Tambahkan syntax berikut pada baris terakhir:
```bash
Include /opt/eprints3/cfg/apache.conf
```
> [!NOTE]
> Pastikan tidak ada ```#``` di depan syntax ```Include /opt/eprints3/cfg/apache.conf```

Kemudian dibawahnya lagi tambahkan:
```bash
ServerName localhost
```
kemudian tekan Ctrl + O, untuk save perubahan tekan Enter, dan untuk keluar dari nano tekan Ctrl + X

### 8.2 Generate Konfigurasi Apache:
```bash
sudo su - eprints
cd /opt/eprints3
./bin/generate_apacheconf Eprints1
```
Jika muncul syntax seperti ini generate konfigurasi berarti berhasil
```bash
/opt/eprints3/cfg/apache/Eprints1.conf
```

> ** Troubleshooting:** Jika muncul ```No files were changed.``` dan tidak ada Eprints1.conf, maka ketik syntax berikut untuk memaksa EPrints menulis ulang apache.conf:
> ```bash
> ./bin/generate_apacheconf --replace Eprints1
> ```

### 8.3 Aktifkan Apache
pertama pastikan sudah ```exit``` lalu salin file ke folder sites-available dengan mengetik:
```bash
sudo cp /opt/eprints3/cfg/apache/Eprints1.conf /etc/apache2/sites-available/eprints.conf
```
Terakhir aktifkan site dan restart apache dengan syntax:
```bash
sudo a2ensite eprints.conf
sudo systemctl restart apache2
```
kemudian jika ingin mengecek status apache gunakan:
```bash
sudo systemctl status apache2
```

## 9. Testing EPrints

Setelah konfigurasi apache selesai, Coba akses repositori anda dengan membuka browser dan navigate ke:
```bash
http://eprints.local
```
Testing berhasil jika sudah tampil seperti berikut:
<img width="1366" height="728" alt="EPrints Done" src="https://github.com/user-attachments/assets/aca09afd-f487-4b60-8faa-9b8e7967e08b" />

> [!NOTE]
> Klik Ctrl + Shift + R jika ingin membersihkan cache browser dan melakukan reload page
> 
> Nama ```eprints.local``` disesuaikan dengan nama yang anda buat saat mengisi "**Hostname**" Konfigurasi Wiard

## Troubleshooting 1: Error Database
```bash
Error connecting to database: Access denied for user 'www-data'@'localhost'
```
Cara mengatasinya:
### 1. Cek User Database yang Seharusnya Dipakai
```bash
sudo mysql -u root -p
```
```sql
CREATE USER IF NOT EXISTS 'Eprints1'@'localhost' IDENTIFIED BY 'eprints123';
GRANT ALL PRIVILEGES ON Eprints1.* TO 'Eprints1'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```
### 2.Edit File Konfigurasi ```database.pl```
```bash
sudo nano /opt/eprints3/archives/Eprints1/cfg/cfg.d/database.pl
```
Sesuaikan isinya dengan yang sudah anda input pada konfigurasi wiard, contoh:
```bash
$c->{dbname} = 'Eprints1';
$c->{dbhost} = 'localhost';
$c->{dbport} = undef;
$c->{dbsock} = undef;
$c->{dbuser} = 'Eprints1';
$c->{dbpass} = 'eprints123';
$c->{dbengine} = 'InnoDB';
```
### 3. Perbaiki Permission File
```bash
sudo chown eprints:eprints /opt/eprints3/archives/Eprints1/cfg/cfg.d/database.pl
sudo chmod 640 /opt/eprints3/archives/Eprints1/cfg/cfg.d/database.pl
```
### 4.Generate Ulang File Apache untuk EPrints
```bash
sudo su - eprints
cd /opt/eprints3
./bin/generate_apacheconf --replace Eprints1
exit
```
### 5. Copy Konfigurasi Apache ke System
```bash
sudo cp /opt/eprints3/cfg/apache/Eprints1.conf /etc/apache2/sites-available/eprints.conf
```
Lalu enable:
```bash
sudo a2ensite eprints.conf
sudo systemctl reload apache2
```
### 6. Restart Apache
```bash
sudo systemctl restart apache2
```
### 7. Test Akses Browser
```bash
http://eprints.local
```

## Troubleshooting 2: Link Mengalami Error
```bash
Can't write to /opt/eprints3/archives/Eprints1/html/en//javascript/auto.js: Permission denied
```
Cara mengatasinya yaitu:
### 1.Pastikan semua folder dan file di archive Eprints1 dimiliki oleh user eprints dan group www-data, supaya Apache bisa menulis
```bash
sudo chown -R eprints:www-data /opt/eprints3/archives/Eprints1
```
### 2. Berikan permission folder writable untuk owner dan group
```bash
sudo find /opt/eprints3/archives/Eprints1 -type d -exec chmod 775 {} \;
```
### 3. Berikan permission file writable untuk owner dan group
```bash
sudo find /opt/eprints3/archives/Eprints1 -type f -exec chmod 664 {} \;
```
### 4. Restart kembali apache:
```bash
sudo systemctl restart apache2
```
### 5. Coba Buka Kembali EPrints
```bash
http://eprints.local
```
### 6. Bersihkan cache browser dan reload
```bash
Ctrl + Shift + R
```

## Troubleshooting Umum

| Masalah | Solusi |
|---------|--------|
| Apache tidak bisa start | `sudo systemctl restart apache2` |
| Permission denied | Pastikan ownership direktori sudah benar (`eprints:eprints`) |
| Database connection failed | Cek kredensial MySQL dan pastikan service berjalan |
| Module Perl tidak ditemukan | Install ulang dengan `sudo cpanm NamaModule` |
