# Go-Ping

{% hint style="info" %}
Category: Web

Difficulty: easy
{% endhint %}

## Deskripsi

> Let's try ping on my web
>
> Semakin kamu merasa nyaman, sistem semakin tidak aman
>
> http://ctf.slashrootctf.id:30012/

## Langkah Penyelesaian

* Diberikan challenge black box testing lagi, setelah quick analysis, jelas itu vuln **Command Injection**.
* Tapi setelah saya coba masukan payload: `127.0.0.1;ls` tidak muncul list file apa-apa.
* Tidak berhenti di situ, saya coba tambahkan opsi `-lah` ternyata terpotong **`-lah` not found**. Saya juga udah coba ganti spasi dengan `${IFS}` tetap not found.
* Saya coba `ls /`, tapi muncul **permission denied**.
* Saya coba cara lain yaitu menggunakan commaand `find` seperti berikut:

<pre class="language-bash!"><code class="lang-bash!"><strong>find${IFS}/${IFS}-name${IFS}'flag*'${IFS}-type${IFS}f
</strong></code></pre>

<figure><img src="../.gitbook/assets/image (2) (1) (1).png" alt=""><figcaption></figcaption></figure>

* Nama file flagnya adalah `/flag_Zr8ovfVgFXqQdlbI.txt`.
* Dari sini, bisa kita ambil kesimpulan jika terdapat filter pada inputannya, yaitu **spasi akan dihilangkan (bisa kita akali dengan `${IFS}`)** dan **terdapat beberapa command yang prohibited untuk dieksekusi, seperti `ls`, `cat`, dan `more`**.
* Dan berikut adalah payload untuk bisa membaca flagnya, bisa menggunakan `tac`, `nl`, dan `less`, dll.

<figure><img src="../.gitbook/assets/image (4) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Flag

<kbd>slashroot8{Rc3\_W1Th\_c0mM4Nd\_1Nj3cT1On\_1S\_V3Ry\_v3rY\_N1Ce}</kbd>
