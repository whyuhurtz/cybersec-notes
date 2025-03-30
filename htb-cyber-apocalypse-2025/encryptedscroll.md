# EncryptedScroll

{% hint style="info" %}
Category: Reverse

Difficulty: very easy
{% endhint %}

## Description

> Elowen Moonsong, an Elven mage of great wisdom, has discovered an ancient scroll rumored to contain the location of The Dragon’s Heart. However, the scroll is enchanted with an old magical cipher, preventing Elowen from reading it.

## Required Knowledge

* C programming
* Reverse binary file

## Solve Walkthrough

### 1. Basic File Checks

First, I do basic file check using `file` command.

{% code overflow="wrap" %}
```bash
challenge: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=5e966c94fbbe92e2607134ac2c0c78ee9d555b30, for GNU/Linux 4.4.0, not stripped
```
{% endcode %}

From the output above, we can see that it is a ELF 64-bit dynamically linked binary with PIE enabled. To get more binary protection, use the `checksec` command.

```bash
[*] '/home/hurtz4eva/Nextcloud/CTF/international/htb-cyber-apocalypse/2025/rev/01_EncryptedScroll/rev_encryptedscroll/challenge'
    Arch:       amd64-64-little
    RELRO:      Partial RELRO
    Stack:      Canary found
    NX:         NX enabled
    PIE:        PIE enabled
    Stripped:   No
```

Mostly, all the protections is enabled and only **Partial RELRO**.

### 2. Analyze the Binary

Here's the decompiled code of some interesting functions.

{% tabs %}
{% tab title="main" %}
{% code lineNumbers="true" %}
```c
undefined8 main(void)

{
  long in_FS_OFFSET;
  undefined1 buffer [56];
  long canary;
  
  canary = *(long *)(in_FS_OFFSET + 0x28);
  anti_debug();
  display_scroll();
  printf(&DAT_00102220);
  __isoc99_scanf(%49s,buffer);
  decrypt_message(buffer);
  if (canary != *(long *)(in_FS_OFFSET + 0x28)) {
                    /* WARNING: Subroutine does not return */
    __stack_chk_fail();
  }
  return 0;
}
```
{% endcode %}
{% endtab %}

{% tab title="decrypt_message" %}
{% code lineNumbers="true" %}
```c
void decrypt_message(char *param_1)

{
  int is_same;
  long in_FS_OFFSET;
  int i;
  char buffer [40];
  long canary;
  
  canary = *(long *)(in_FS_OFFSET + 0x28);
  builtin_strncpy(buffer,"IUC|t2nqm4`gm5h`5s2uin4u2d~",0x1c);
  for (i = 0; buffer[i] != '\0'; i = i + 1) {
    buffer[i] = buffer[i] + -1;
  }
  is_same = strcmp(param_1,buffer);
  if (is_same == 0) {
    puts("The Dragon\'s Heart is hidden beneath the Eternal Flame in Eldoria.");
  }
  else {
    puts("The scroll remains unreadable... Try again.");
  }
  if (canary != *(long *)(in_FS_OFFSET + 0x28)) {
                    /* WARNING: Subroutine does not return */
    __stack_chk_fail();
  }
  return;
}
```
{% endcode %}
{% endtab %}
{% endtabs %}

From the decompiled of `decrypt_message`  we got interesting information, that is the string `IUC|t2nqm4gm5h5s2uin4u2d~` . The logic behind `decrypt_message` is very simple, it just **decrease each character of string "`IUC|t2nqm4gm5h5s2uin4u2d~`" by 1 and the result will be put in buffer**. Then, our input will be compared with the buffer.

### 3. Decrypt the Secret Message

To get the flag is simply do the logic `buffer[i] = buffer[i] + -1;` . Take a look at the image below.

<figure><img src="../.gitbook/assets/image (56).png" alt=""><figcaption></figcaption></figure>

As you can see on the image above, the pattern of flag is appears. So, we just simply do for loop to decrypt the message. Here's my solver script.

{% code title="solver.py" lineNumbers="true" %}
```python
#!/usr/bin/env python3

def solve(encrypted_message):
    res = ""
    len_encrypted_message = len(encrypted_message)

    for i in range(len_encrypted_message):
        res += chr(ord(encrypted_message[i]) - 1)

    return res

if __name__ == "__main__":
    encrypted_message = "IUC|t2nqm4`gm5h`5s2uin4u2d~"
    decrypted_message = solve(encrypted_message)
    print(decrypted_message)
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (58).png" alt=""><figcaption></figcaption></figure>

## Flag

<kbd>HTB{s1mpl3\_fl4g\_4r1thm3t1c}</kbd>
