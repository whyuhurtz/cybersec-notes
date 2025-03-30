# Poison Master

{% hint style="info" %}
Category: Boot2root

Difficulty: easy
{% endhint %}

## 1. Detail Tantangan

| Name       | Description           |
| ---------- | --------------------- |
| Level      | Easy                  |
| Points     | 30 (user) & 70 (root) |
| Machine IP | 10.1.2.234            |
| Machine OS | Linux                 |

## 2. Langkah Penyelesaian

### 2.1. Port and Service Enumeration

* Hasil scan port menggunakan `rustscan`.

```bash
rustscan -a 10.1.2.234 -r 1-65535 --scripts default | tee -a output_scan_all_ports.txt
```

<figure><img src="../.gitbook/assets/image (26).png" alt=""><figcaption></figcaption></figure>

### 2.2. RPC Enumeration

* Terdapat celah null session yang berarti bisa dimanfaatkan untuk login sebagai `anonymous`.

```bash
rpcclient -U "" -N 10.1.2.234 -p 139
```

* Hasil enumerasi list user: `enumdomusers` tidak tampil informasi apa-apa.

<figure><img src="../.gitbook/assets/image (27).png" alt=""><figcaption></figcaption></figure>

* Hasil enumerasi list group juga tidak menampilkan informasi apa pun: `enumdomgroups`.

<figure><img src="../.gitbook/assets/image (28).png" alt=""><figcaption></figcaption></figure>

* Hasil enumerasi list domain: `enumdomains`.

<figure><img src="../.gitbook/assets/image (29).png" alt=""><figcaption></figcaption></figure>

* Hasil enumerasi shared folder: `netshareenumall`.

<figure><img src="../.gitbook/assets/image (30).png" alt=""><figcaption></figcaption></figure>

* Dapat dilihat pada gambar di bawah ini, list SID yang tersedia hanya berjumlah 6 saja.

<figure><img src="../.gitbook/assets/image (31).png" alt=""><figcaption></figcaption></figure>

* Tetapi, ketika saya coba enumerasi secara otomatis menggunakan: `enum4linux` muncul SID baru yaitu: **S-1-22-1** yang di dalamnya terdapat 2 user baru:
  * **ubmaj**, dan
  * **jambu**

```bash
enum4linux 10.1.2.234
```

<figure><img src="../.gitbook/assets/image (32).png" alt=""><figcaption></figcaption></figure>

* Tampaknya, dua user tersebut berupaya disembunyikan oleh si pemilik server.

### 2.3. SMB Enumeration

* Tidak ada folder apa pun di net share `IPC$`.

<figure><img src="../.gitbook/assets/image (33).png" alt=""><figcaption></figcaption></figure>

* Not interesting for now.

### 2.4. HTTP Port 7765 Enumeration

* Setelah saya coba visit port **7765** di web browser, muncul tampilan Apache2 Default Web Page, tapi setelah saya coba cari-cari CVE-nya atau informasi terkait, hasilnya nihil. Jadi, saya pikir ini hanya _**rabbithole**_ aja.

<figure><img src="../.gitbook/assets/image (34).png" alt=""><figcaption></figcaption></figure>

### 2.5. Bruteforce SSH with Known Users

* Karena stuck dan minim informasi, saya coba bruteforce service SSH menggunakan list user yang disembunyikan tadi, yaitu: `ubmaj` dan `jambu` dengan passwordnya menggunakan wordlist: `rockyou.txt`.
* Berikut command hydra untuk bruteforce-nya.

```bash
hydra -L users.txt -P /usr/share/wordlists/rockyou.txt 10.1.2.234 ssh -t 4
```

* Found SSH password for user `ubmaj` that is: `princess`.

<figure><img src="../.gitbook/assets/image (35).png" alt=""><figcaption></figcaption></figure>

### 2.6. Remote SSH to Target Server

* Langsung saja lakukan remote ke server target menggunakan kredensial SSH yang telah didapatkan:

| Name     | Description |
| -------- | ----------- |
| Username | ubmaj       |
| Password | princess    |

<figure><img src="../.gitbook/assets/image (36).png" alt=""><figcaption></figcaption></figure>

## 3. User Flag

<kbd>9f752edba37a6dbe680219e472ad2b85</kbd>
