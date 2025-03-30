---
icon: globe-pointer
---

# just simple upload

{% hint style="info" %}
Tantangan web KMIPN VI yang sangat mudah diselesaikan. Seperti pada namanya, upload file dan dapatkan flagnya jika paylaod-nya sudah sesuai.
{% endhint %}

## 1. Deskripsi Tantangan

> temen ku baru belajar bikin form upload, sepertinya tidak beres
>
> [_http://157.173.204.136:8089/_](http://157.173.204.136:8089/)

## 2. Langkah Penyelesaian

* Sebuah web simple berbasis PHP yang memiliki fitur untuk upload file.
* Craft sebuah png file yang disisipkan dengan script malicious php berikut ini.

```bash
echo "<?php echo file_get_contents('flag.txt'); ?>" > get_flag.png
```

* Upload file tersebut dan nantinya akan tampil flag-nya!

<figure><img src="../../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

## 3. Flag

<kbd>KMIPNVIPNJ{disable\_function\_on\_upload\_file\_exploit}</kbd>

***

## References

* [https://github.com/azfarwisnu/KMIPN-FINAL-CYBER-SECURITY-2024/tree/main/web](https://github.com/azfarwisnu/KMIPN-FINAL-CYBER-SECURITY-2024/tree/main/web)

***

**Tags**: #ctf, #web-exploitation
