# Login Begin

{% hint style="info" %}
Category: Web

Difficulty: easy
{% endhint %}

## Deskripsi

> Hallo Peserta Slashroot CTF #8, Pada Hari Ini Kita Akan Mengikuti Penyisihan CTF, Sebelum Memulai Kegiatan, Saya Sebagai Probset Ingin Menguji Kemampuan Temen" Dalam Bidang Web Hacking, Ayo Teman" Cobalah Exploit Web Sederhana Buatan Saya Dan Temukan Hak Akses Admin Dalam Sebuah Web Tersebut !, Siapa Tau Diantara Teman" Dapat Menemukan Flag Tersembunyi Di Dalam Web Tersebut.
>
> http://ctf.slashrootctf.id:30011

## Langkah Penyelesaian

* Challengenya berupa black box testing, sehingga tidak diberikan source code-nya.
* Di halaman register `/register.php` ada select **role**, tanpa pikir panjang, saya langsung intercept menggunakan burp suite ketika coba register.
* Waktu register, saya ganti rolenya menjadi **admin**.

<figure><img src="../.gitbook/assets/image (8) (1).png" alt=""><figcaption></figcaption></figure>

* Alhamdulillah, flag berhasil didapatkan.

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Flag

<kbd>slashroot8{W0w\_Y0u\_G00d\_B3gg1nn3r}</kbd>
