# Package Delivery

{% hint style="info" %}
Category: Joy (Game)

Difficulty: easy
{% endhint %}

## Deskripsi

> A game about a mundane task...
>
> Some say if you deliver an exact number of packages, a magic text will appear??
>
> Diberikan source code apk: [PackageDelivery.zip](https://ctf.slashrootctf.id/files/f57dd9357ae8166409d2443ebd59a4a0/PackageDelivery.zip?token=eyJ1c2VyX2lkIjoxNiwidGVhbV9pZCI6MzIsImZpbGVfaWQiOjEyfQ.Zvgzcg.sBBnzp0VLjyjDhwBT-NuRE4Ab58)

## Langkah Penyelesaian

* Challenge ini sangat mudah dari yang saya bayangkan, hanya modal `strings` langsung solve.
* Awalnya saya coba mainkan gamenya dulu siapa tau ada anomali kan wkwk.
* Ternyata di harus sampe score 9999 baru ada gift spesial (mungkin aja flag).
* Ngapain harus se-effort itu kan, lalu saya coba quick analysis file `.pck` untuk nemuin something interest.
* Alhamdulillah, ketika saya coba cari **"slashroot"** langsung ketemu flagnya. Agak curiga sih di awal kenapa semudah itu, tapi gas ajalah wkwk.

<figure><img src="../.gitbook/assets/image (6) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Flag

<kbd>slashroot8{N0T90Nn4l1e\_tH4T\_w45\_E45y\_hUh?}</kbd>
