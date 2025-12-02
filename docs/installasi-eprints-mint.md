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

## 6. Konfigurasi MariaDB/MySQL

### 6.1 Setup Password Root MySQL

Masuk ke MySQL:
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
| Administrator Email? | `wicaksonob208@gmail.com` *(gunakan email Anda)* |
| Archive Name? | `Test Repository` |
| Organisation Name? | `UIN Jakarta` |
| Configure database? | `yes` |
| Database Name? | `Eprints1` |
| MySQL Host? | `localhost` |
| MySQL Port? | `#` *(untuk no setting)* |
| MySQL Socket? | `#` *(untuk no setting)* |
| Database User? | `Eprints1` |
| Database Password? | *[Masukkan password]* |
| Database Engine? | `InnoDB` |
| Write these database settings? | `yes` |
| Create database 'Eprints1'? | `yes` |
| Database Superuser Username? | `root` |
| Database Superuser Password? | *[Password root MySQL]* |
| Create database tables? | `yes` |
| Create an initial user? | `yes` |
| Enter a username? | `bagas` |
| Select a user type? | `admin` |
| Enter Password? | *[Buat password admin]* |
| Email? | `wicaksonob208@gmail.com` *(gunakan email Anda)* |
| Build static web pages? | `yes` |
| Import LOC subjects and sample divisions? | `yes` |
| Update Apache config files? | `yes` |

## 8. Setelah Instalasi

Setelah wizard selesai, EPrints sudah terinstal dan terkonfigurasi. Akses repository Anda dengan membuka browser dan navigate ke:
```
http://eprints.local
```

## Troubleshooting Umum

| Masalah | Solusi |
|---------|--------|
| Apache tidak bisa start | `sudo systemctl restart apache2` |
| Permission denied | Pastikan ownership direktori sudah benar (`eprints:eprints`) |
| Database connection failed | Cek kredensial MySQL dan pastikan service berjalan |
| Module Perl tidak ditemukan | Install ulang dengan `sudo cpanm NamaModule` |
