# SealedRune

{% hint style="info" %}
Category: Reverse

Difficulty: Very Easy
{% endhint %}

## Description

> Elowen has reached the Ruins of Eldrath, where she finds a sealed rune stone glowing with ancient power. The rune is inscribed with a secret incantation that must be spoken to unlock the next step in her journey to find The Dragon’s Heart.

## Required Knowledge

* C programming
* Reverse binary file

## Solve Walkthrough

### 1. Basic File Checks

First, I do basic file check using `file` command.

{% code overflow="wrap" %}
```bash
./challenge: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=47f180529af15b5a7d4601583b2944010ae6e092, for GNU/Linux 4.4.0, not stripped
```
{% endcode %}

From the output above, this is a 64-bit dynamically linked ELF binary. Let's see for others binary protections.

```bash
[*] '/home/hurtz4eva/Nextcloud/CTF/international/htb-cyber-apocalypse/2025/rev/02_SealedRune/rev_sealedrune/challenge'
    Arch:       amd64-64-little
    RELRO:      Partial RELRO
    Stack:      Canary found
    NX:         NX enabled
    PIE:        PIE enabled
    Stripped:   No
```

Same as rev challenge before "_EncryptedScroll_", the binary mostly have all protections, except **Partial RELRO**.

### 2. Analyze the Binary

Let's see the decompiled code inside the binary. I'm using Ghidra for this case.

{% tabs %}
{% tab title="main" %}
{% code overflow="wrap" lineNumbers="true" %}
```c
undefined8 main(void)

{
  long in_FS_OFFSET;
  undefined1 input [56];
  long canary;
  
  canary = *(long *)(in_FS_OFFSET + 0x28);
  anti_debug();
  display_rune();
  puts(&DAT_00102750);
  printf("Enter the incantation to reveal its secret: ");
  __isoc99_scanf(%49s,input);
  check_input(input);
  if (canary != *(long *)(in_FS_OFFSET + 0x28)) {
                    /* WARNING: Subroutine does not return */
    __stack_chk_fail();
  }
  return 0;
}
```
{% endcode %}
{% endtab %}

{% tab title="check_input" %}
{% code overflow="wrap" lineNumbers="true" %}
```c
void check_input(char *param_1)

{
  int iVar1;
  char *secret_msg;
  long lVar2;
  
  secret_msg = (char *)decode_secret();
  iVar1 = strcmp(param_1,secret_msg);
  if (iVar1 == 0) {
    puts(&DAT_00102050);
    lVar2 = decode_flag();
    printf("\x1b[1;33m%s\x1b[0m\n",lVar2 + 1);
  }
  else {
    puts("\x1b[1;31mThe rune rejects your words... Try again.\x1b[0m");
  }
  free(secret_msg);
  return;
}
```
{% endcode %}
{% endtab %}

{% tab title="decode_secret" %}
{% code overflow="wrap" lineNumbers="true" %}
```c
undefined8 decode_secret(void)

{
  undefined8 uVar1;
  
  decoded_secret = base64_decode(incantation);
  reverse_str(decoded_secret);
  return uVar1;
}
```
{% endcode %}
{% endtab %}

{% tab title="base64_decode" %}
{% code overflow="wrap" lineNumbers="true" %}
```c
void * base64_decode(char *param_1)

{
  int iVar1;
  size_t __nmemb;
  void *pvVar2;
  char *pcVar3;
  long in_FS_OFFSET;
  int local_80;
  int local_7c;
  int local_78;
  int i;
  char buffer [72];
  long canary;
  
  canary = *(long *)(in_FS_OFFSET + 0x28);
  __nmemb = strlen(param_1);
  pvVar2 = calloc(__nmemb,1);
  builtin_strncpy(buffer,"ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/",0x41);
  local_80 = 0;
  local_7c = 0;
  local_78 = 0;
  for (i = 0; param_1[i] != '\0'; i = i + 1) {
    pcVar3 = strchr(buffer,(int)param_1[i]);
    local_7c = ((int)pcVar3 - (int)buffer) + local_7c * 0x40;
    iVar1 = local_78 + 6;
    if (7 < iVar1) {
      *(char *)((long)pvVar2 + (long)local_80) = (char)(local_7c >> ((char)iVar1 - 8U & 0x1f));
      local_80 = local_80 + 1;
      iVar1 = local_78 + -2;
    }
    local_78 = iVar1;
  }
  if (canary != *(long *)(in_FS_OFFSET + 0x28)) {
                    /* WARNING: Subroutine does not return */
    __stack_chk_fail();
  }
  return pvVar2;
}
```
{% endcode %}
{% endtab %}

{% tab title="reverse_str" %}
{% code overflow="wrap" lineNumbers="true" %}
```c
void reverse_str(char *param_1)

{
  char cVar1;
  int msg_length;
  size_t sVar2;
  int i;
  
  sVar2 = strlen(param_1);
  msg_length = (int)sVar2;
  for (i = 0; i < msg_length / 2; i = i + 1) {
    cVar1 = param_1[i];
    param_1[i] = param_1[(long)(msg_length - i) + -1];
    param_1[(long)(msg_length - i) + -1] = cVar1;
  }
  return;
}
```
{% endcode %}
{% endtab %}

{% tab title="decode_flag" %}
{% code overflow="wrap" lineNumbers="true" %}
```c
undefined8 decode_flag(void)

{
  undefined8 decoded_flag;
  
  decoded_flag = base64_decode(flag);
  reverse_str(decoded_flag);
  return decoded_flag;
}
```
{% endcode %}
{% endtab %}
{% endtabs %}

The logic behind the program is pretty simple:

* Our input will compare with the secret message. If our input is same with the secret message, then the flag will appears.
* The secret message can be found inside the `decode_secret`  function.  String **incantation** is stored in the `$RDI` register. The secret message is **encoded with base64** and **reversed**.

### 3. Decrypt the Secret Message

<figure><img src="../.gitbook/assets/image (64).png" alt=""><figcaption></figcaption></figure>

The image above is the encoded secret message in hex format (stored in `$RDI`  register). The combination of each hex characters is `65h 6Dh 46h 79h 5Ah 6Dh 5Ah 31h 62h 6Bh 64h 73h 5Ah 57h 46h 57h 00h`. If we try to decode all hex characters, the output will be `emFyZmZ1bkdsZWFW`. Okay, its seems like base64 encoded string, the output of decoded string is `zarffunGleaV`. We're not done yet, we've to reverse the decoded string, so the final secret message is `VaelGnuffraz`.

If we input the correct secret message, then we got the flag. Here's my solver script.

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-

from pwn import *
from base64 import b64decode

def solve(encoded_secret_msg) -> bytes:   
    # 1. Decode the hex encoded string.
    hex_decoded = bytes.fromhex(encoded_secret_msg.replace("h", "").replace(" ", "")).decode()
    
    # 2. Decode the base64 encoded string.
    base64_decoded = b64decode(hex_decoded)
    
    # 3. Reverse the decoded string.
    reversed_string = base64_decoded[::-1]
    
    return reversed_string

if __name__ == "__main__":
    encoded_secret_msg = "65h 6Dh 46h 79h 5Ah 6Dh 5Ah 31h 62h 6Bh 64h 73h 5Ah 57h 46h 57h 00h"
    decoded_secret_msg = solve(encoded_secret_msg)
    
    # Input the decoded secret message into the program.
    exe = ELF('./challenge', checksec=0)
    context.binary = exe
    # context.log_level = "DEBUG"
    
    # Start the process
    io = exe.process()
    io.sendline(decoded_secret_msg)
    
    # Print the flag
    search_flag = re.search(r'HTB{.*}', io.recvall(timeout=1).decode())
    flag = search_flag.group(0) if search_flag else None
    print(f"Flag --> {flag}") if flag else print("Flag not found")
    
    io.close()
```

<figure><img src="../.gitbook/assets/image (65).png" alt=""><figcaption></figcaption></figure>

## Flag

<kbd>HTB{run3\_m4g1c\_r3v34l3d}</kbd>
