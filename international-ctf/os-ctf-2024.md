---
description: Beginner level online CTF.
icon: swords
cover: >-
  https://images.unsplash.com/photo-1550751827-4bd374c3f58b?crop=entropy&cs=srgb&fm=jpg&ixid=M3wxOTcwMjR8MHwxfHNlYXJjaHwyfHxjeWJlcnxlbnwwfHx8fDE3NDE4MjA4MjF8MA&ixlib=rb-4.0.3&q=85
coverY: 0
---

# OS CTF 2024

{% hint style="info" %}
MISC, OSINT, WEB

Links: [https://ctf.os.ftp.sh/](https://ctf.os.ftp.sh/)
{% endhint %}

## Cyber Quiz (Misc - Very Easy)

### 1. Description

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

### 2. Solve Walkthrough

* Just answer all the questions after making a connection.
* ChatGPT helped a lot. :joy:

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

### 3. Flag

<kbd>OSCTF{L33t\_Kn0wl3Dg3}</kbd>

## The statue is being repaired (OSINT - Easy)

### 1. Description

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

The repaired statue image:

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

### 2. Solve Walkthrough

* Save the picture chall and try to search with Google Lens. You will find 2 posts in Facebook (in Vietnam language).

<figure><img src="../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

* The image looks same, and then I explore the tag in the post `#FPTAround`.
* Seems like it refers to the **FPT University in Vietnam**.

<figure><img src="../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

* I do more exploration about **fptaround** and luckily I got the community page in Facebook: [_https://www.facebook.com/fptaround_](https://www.facebook.com/fptaround).

<figure><img src="../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

* I try to translate what is the Intro description

<figure><img src="../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

* Nice, we got the city name: **Ho Chi Minh**.
* Next, we've to found what is the statue name. I just search FPT university statue in google, and found the useful wiki link: [_https://commons.wikimedia.org/wiki/File:Self-made\_Man\_statue\_at\_FPT\_University\_Ha\_Noi.jpg_](https://commons.wikimedia.org/wiki/File:Self-made_Man_statue_at_FPT_University_Ha_Noi.jpg).
* Alhamdulillah, I got the name of statue: **Self-made man**.

<figure><img src="../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

* Finally, we can crafting the correct flag format: **selfmademan\_hochiminh**.

### 3. Flag

<kbd>OSCTF{selfmademan\_hochiminh}</kbd>

## Introspection (Web - Very Easy)

### 1. Description

<figure><img src="../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

### 2. Solve Walkthrough

* Visit the web page, and open the inspect element in web browser,

<figure><img src="../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

* Notice that the web page have JS file embedded. Open the `script.js` file and yeah.. we found the flag.

<figure><img src="../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

* Check the flag for make sure. Sooo... ez dude.

<figure><img src="../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

### 3. Flag

<kbd>OSCTF{Cr4zY\_In5P3c71On}</kbd>

### 1. Description

<figure><img src="../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>

### 2. Solve Walkthrough

* From the title we know that the challenge is the web app vulnerable to **SQL injection attack**.
* Just performing basic SQL injection payload to bypass login: `1' OR 1=1--`.
* Use the payload in username and password and it will make the username and password val0id. 4. In the default page, it will show the **fake flag**. You must be careful okay.

<figure><img src="../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

* But, you can access the admin page directly by changing the endpoint to `/admin` (if you know).

<figure><img src="../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

* If you don't know what all endpoints or subdirectories inside the web page, you can do **directory scanning** using tool like **dirsearch**, **feroxbuster**, **dirb**, or **gobuster**. I recommend to use **dirsearch** cause they have default wordlist.

### 3. Flag

<kbd>OSCTF{D1r3ct0RY\_BrU7t1nG\_4nD\_SQL}</kbd>

## Indoor WebApp (Web - Easy)

### 1. Description

<figure><img src="../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>

### 2. Solve Walkthrough

* Given a web app that we can see personal information, but notice that every person is have unique id: `?user_id` value parameters.

<figure><img src="../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>

* I try to change it to person **2** or `?user_id=2` and I got the flag.

<figure><img src="../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>

* We can perform brute force attack to check if the spesific `user_id` is exist or not by using **Burp Suite** or simply **cURL** (combined with for/while loop).

<figure><img src="../.gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>

* Luckily, we just have 3 available users.

### 3. Flag

<kbd>OSCTF{1nd00r\_M4dE\_n0\_5enS3}</kbd>

## Action Notes (Web - Easy)

### 1. Description

<figure><img src="../.gitbook/assets/image (21).png" alt=""><figcaption></figcaption></figure>

### 2. Solve Walkthrough

* I just try to login as **admin** and luckily the password that I guess is **admin123** so I can the flag in the admin page.

<figure><img src="../.gitbook/assets/image (22).png" alt=""><figcaption></figcaption></figure>

* Try to guess common user passwords, such as:

| Username | Password     |
| -------- | ------------ |
| admin    | admin        |
| admin    | password     |
| admin    | **admin123** |
| admin    | admin1234    |

### 3. Flag

<kbd>OSCTF{Av0id\_S1mpl3\_P4ssw0rDs}</kbd>

***

**Tags**: #ctf, #misc, #osint, #web
