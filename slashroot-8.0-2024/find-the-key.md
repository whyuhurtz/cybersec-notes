# Find The Key

{% hint style="info" %}
Category: Forensic

Difficulty: easy
{% endhint %}

## Deskripsi

> Temukan key = solve
>
> Diberikan sebuah file: [ssstttttt.unknown](https://ctf.slashrootctf.id/files/9e330d8aad63dd0b39c8a3690af28f95/ssstttttt.unknown?token=eyJ1c2VyX2lkIjoxNiwidGVhbV9pZCI6MzIsImZpbGVfaWQiOjd9.Zvg4Gg.zZyfjkmJlYBu4hk1FCIqjjDE8gc)

## Langkah Penyelesaian

* Saya coba liat file signature dari file tersebut, sepertinya terpotong. Bisa dilihat pada gambar di bawah, 3 digit hex yang di-highlight sepertinya indikasi file JPG.

<figure><img src="../.gitbook/assets/image (7) (1) (1).png" alt=""><figcaption></figcaption></figure>

* Setelah saya coba fix dengan file signature yang sesuai, tidak ada flag yang muncul.

<figure><img src="../.gitbook/assets/image (8) (1) (1).png" alt=""><figcaption></figcaption></figure>

* Analisis selanjutnya yaitu mengecek bagian human readable dari file tersebut dengan `strings`.

<figure><img src="../.gitbook/assets/image (9) (1).png" alt=""><figcaption></figcaption></figure>

* Nampak sebuah flag tapi formatnya salah, terbukti ketika dicoba submit, baik dengan kelebihan **r** atau pun tidak hasilnya tetap incorrect.
* Saya coba decode nilai hex di bawahnya, flagnya masih incorrect.

<figure><img src="../.gitbook/assets/image (10) (1).png" alt=""><figcaption></figcaption></figure>

* Kemudian, saya coba kode binary yang di bawahnya, berikut hasil konversi ke ascii (text).

<figure><img src="../.gitbook/assets/image (11) (1).png" alt=""><figcaption></figcaption></figure>

* Hmm, sebuah flag juga, tapi ketika coba submit masih incorrect.
* Saya coba decode nilai hex yang di atas flag awal, hasilnya adalah `admin123`.

<figure><img src="../.gitbook/assets/image (12) (1).png" alt=""><figcaption></figcaption></figure>

* Sejenak saya berpikir mungkin saja itu sebuah password, but for what ?? Apakah ini sebuah file archive yang diproteksi password ?
* Saya coba cari nilai hex yang relevan dengan file signature zip yaitu `50 4B 03 04`, ternyata ada dong dan itu **berulang sebanyak 4 kali**.

<figure><img src="../.gitbook/assets/image (13) (1).png" alt=""><figcaption></figcaption></figure>

* Ketika file signaturenya diganti dari jpg ke zip beneran bisa dong.

<figure><img src="../.gitbook/assets/image (14) (1).png" alt=""><figcaption></figcaption></figure>

* Saya coba baca isi file tersebut, rata-rata semuanya di-encode menggunakan base64 dan ternyata di dalamnya ada banyak sekali flag palsu. Sampai pada akhirnya, alhamdulillah saya berhasil ketemu flag aslinya sebelum nilai binary.

<figure><img src="../.gitbook/assets/image (15) (1).png" alt=""><figcaption></figcaption></figure>

* Bang jangan di prank mulu donk, udah salah berape kali gw :v.

## Flag

<kbd>slashroot8{Y4n9\_b1k1n\_s04l\_m4s1h\_p3mul4}</kbd>
