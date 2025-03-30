# Indoor WebApp

{% hint style="info" %}
Category: Web

Difficulty: very easy
{% endhint %}

## Description

<figure><img src="../../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>

## Solve Walkthrough

* Given a web app that we can see personal information, but notice that every person is have unique id: `?user_id` value parameters.

<figure><img src="../../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>

* I try to change it to person **2** or `?user_id=2` and I got the flag.

<figure><img src="../../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>

* We can perform brute force attack to check if the spesific `user_id` is exist or not by using **Burp Suite** or simply **cURL** (combined with for/while loop).

<figure><img src="../../.gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>

* Luckily, we just have 3 available users.

## Flag

<kbd>OSCTF{1nd00r\_M4dE\_n0\_5enS3}</kbd>
