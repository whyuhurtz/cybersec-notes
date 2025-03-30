# Heads or Tails

{% hint style="info" %}
Category: Web

Difficulty: very easy
{% endhint %}

## Description

<figure><img src="../../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>

## Solve Walkthrough

* From the title we know that the challenge is the web app vulnerable to **SQL injection attack**.
* Just performing basic SQL injection payload to bypass login: `1' OR 1=1--`.
* Use the payload in username and password and it will make the username and password val0id. 4. In the default page, it will show the **fake flag**. You must be careful okay.

<figure><img src="../../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

* But, you can access the admin page directly by changing the endpoint to `/admin` (if you know).

<figure><img src="../../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

* If you don't know what all endpoints or subdirectories inside the web page, you can do **directory scanning** using tool like **dirsearch**, **feroxbuster**, **dirb**, or **gobuster**. I recommend to use **dirsearch** cause they have default wordlist.

## Flag

<kbd>OSCTF{D1r3ct0RY\_BrU7t1nG\_4nD\_SQL}</kbd>
