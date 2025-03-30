# Rocket

{% hint style="info" %}
Category: Boot2root

Difficulty: easy
{% endhint %}

## 1. Detail Tantangan

| Name       | Description           |
| ---------- | --------------------- |
| Level      | Easy                  |
| Points     | 30 (user) & 70 (root) |
| Machine IP | 10.1.2.233            |
| Machine OS | Windows 10            |

## 2. Langkah Penyelesaian

### 2.1. Port and Service Enumeration

* Saya menggunakan tool `rustscan` untuk mempercepat proses port scanning.

```bash
rustscan -a 10.1.2.233 -r 1-65535 --scripts default | tee -a output_scan_all_ports.txt
```

* Beberapa port yang open dan interesting antara lain:
  * **445** (SMB)
  * **135** (MSRPC)
  * **139** (RPC)
  * **3389** (RDP)
  * **8111** (HTTP)

### 2.2. RDP Enumeration

* Hasil scan service pada port 3389 (RDP) menggunakan `nmap`.

```bash
nmap -p 3389 -sVC -v -oN output_scan_port_3389.txt 10.1.2.233
```

* Tidak ada yang interesting untuk saat ini. Mungkin untuk **Domain Name** nanti berguna.

<figure><img src="../.gitbook/assets/image (20) (1).png" alt=""><figcaption></figcaption></figure>

### 2.3. RPC Enumeration

* Terdapat celah null session atau attacker dapat login sebagai anonymous.

```bash
rpcclient -U "" -N 10.1.2.233 -p 139
```

* Hasil enumerasi domain: `enumdomains`.

```bash
rpcclient $> enumdomains
name:[LETHOS] idx:[0x0]
name:[Builtin] idx:[0x1]
```

* Hasil enumerasi shared folder: `netshareenumall`.

```bash
rpcclient $> netshareenumall
netname: print$
        remark: Printer Drivers
        path:   C:\var\lib\samba\printers
        password:
netname: IPC$
        remark: IPC Service (lethos server (Samba, Ubuntu))
        path:   C:\tmp
        password:
```

* Tidak berhasil mendapatkan `enumdomusers`.

### 2.4. HTTP Port 8111 Enumeration

* Hasil scan service pada port **8111** menggunakan `nmap`.

```bash
nmap -p 8111 -sVC -v -oN output_scan_port_8111.txt 10.1.2.233
```

<figure><img src="../.gitbook/assets/image (21) (1).png" alt=""><figcaption></figcaption></figure>

* Setelah saya coba open port **8111** di web browser, terdapat info menarik yaitu tampil halaman login **JetBrains TeamCity versi 2023.11.3**.
* Tanpa pikir panjang, saya langsung mencari tau apakah versi tersebut ada CVE-nya atau tidak.
* Dari hasil pencarian, ditemukan bahwa **JetBrains TeamCity versi 2023.11.3** terdapat kerentanan _**Authentication Bypass to RCE**_, yang mana mengizinkan attacker bisa login tanpa menggunakan akun yang sah/terdaftar di sistem (lebih lengkapnya baca artikel ini: [https://www.vicarius.io/vsociety/posts/teamcity-auth-bypass-to-rce-cve-2024-27198-and-cve-2024-27199](https://www.vicarius.io/vsociety/posts/teamcity-auth-bypass-to-rce-cve-2024-27198-and-cve-2024-27199)).

<figure><img src="../.gitbook/assets/image (22) (1).png" alt=""><figcaption></figcaption></figure>

* Kemudian saya cari script POC yang mungkin orang lain pernah buat, dan ternyata saya menemukannya dari repo GitHub berikut: [https://github.com/W01fh4cker/CVE-2024-27198-RCE](https://github.com/W01fh4cker/CVE-2024-27198-RCE).
* Saya coba jalankan script tersebut menggunakan command:

```bash
pip3 install requests urllib3 faker
python3 CVE-2024-27198-RCE.py -t http://10.1.2.233:8111
```

<figure><img src="../.gitbook/assets/image (23) (1).png" alt=""><figcaption></figcaption></figure>

* Dan **Alhamdulillah**! saya berhasil mendapatkan powershellnya, lanjut ke pencarian kredensial untuk bisa melakukan RDP.
* Saya coba `ls` current directory dan menemukan sebuah file: `cred.txt` yang berisi username dan password.
* Tanpa pikir panjang saya langsung coba login RDP menggunakan tool bawaan Windows langsung.

<figure><img src="../.gitbook/assets/image (24) (1).png" alt=""><figcaption></figcaption></figure>

* Terdapat sebuah file `user.txt` di **Desktop**, dan setelah saya buka ternyata berisikan sebuah flag user. **GOTCHAA!!**.
* Berikut POC setelah berhasil remote desktop ke mesin target Windows 10:

<figure><img src="../.gitbook/assets/image (25).png" alt=""><figcaption></figcaption></figure>

## 3. User Flag

<kbd>24ee7336f91a82fcab6bd8b1fd981d2a</kbd>
