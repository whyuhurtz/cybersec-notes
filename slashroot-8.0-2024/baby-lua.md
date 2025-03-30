# baby-lua

{% hint style="info" %}
Category: Rev

Difficulty: easy
{% endhint %}

## Deskripsi

> lua but baby
>
> Diberikan sebuah ELF file: [main](https://ctf.slashrootctf.id/files/4770ab6c4f75fa4142e05a0fae60fed8/main?token=eyJ1c2VyX2lkIjoxNiwidGVhbV9pZCI6MzIsImZpbGVfaWQiOjIwfQ.Zvg07w.10ejWUi6oVFoMxYAajY2YXtVxxk)

## Langkah Penyelesaian

* Disclaimer dulu, saya gak jago Reverse ya ges, iseng-iseng aja ini.
* Setelah saya coba decompile menggunakan Ghidra, ada something interesting di fungsi `init()`, di mana mengeksekusi command seperti berikut.

{% code title="chall.c" lineNumbers="true" %}
```c
int init(EVP_PKEY_CTX *ctx)

{
  int iVar1;
  char local_408 [1024];
  
  sprintf(local_408,
          "echo d2dldCAtcSAtTyAvdG1wL3V3dSBodHRwczovL2FpbWFyLmlkL2ZsYWcubHVhYw== | base64 -d | bash"
         );
  iVar1 = system(local_408);
  return iVar1;
}
```
{% endcode %}

* Ketika saya coba execute command tersebut di local machine, ada file `uwu` di direktori `/tmp`. Saya coba cek file apakah itu, ternyata merupakan file bytecode dalam bahasa Lua.
* Langsung saja saya decompile bytecode itu dan ini hasilnya.

<pre class="language-lua" data-title="bytecode.lua" data-line-numbers><code class="lang-lua"><strong>    -- filename: 
</strong>    -- version: lua52
    -- line: [0, 0] id: 0
    check_flag = function(r0_1)
    -- line: [1, 45] id: 1
    local r1_1 = {
        0,
        1,
        1,
        1,
        0,
       ... omitted ...,
        0,
        1,
        1,
        1,
        1,
        1,
        0,
        1
    }
    local r2_1 = ""
    for r6_1 = 1, #r0_1, 1 do
        local r8_1 = string.byte(r0_1:sub(r6_1, r6_1))
        for r12_1 = 7, 0, -1 do
        if 2 ^ r12_1 &#x3C;= r8_1 then
            r2_1 = r2_1 .. "1"
            r8_1 = r8_1 - 2 ^ r12_1
        else
            r2_1 = r2_1 .. "0"
        end
        end
    end
    if #r2_1 ~= #r1_1 then
        return false
    end
    local r3_1 = 0
    for r7_1 = 1, #r1_1, 1 do
        if tonumber(r2_1:sub(r7_1, r7_1)) == r1_1[r7_1] then
        r3_1 = r3_1 + 1
        end
    end
    local r4_1 = r2_1 == table.concat(r1_1)
    end
</code></pre>

* Kode Lua di atas tampaknya melakukan pengecekan flag dengan mengubah input menjadi representasi biner dan membandingkannya dengan array biner yang telah ditentukan.
* Langsung saja saya buat solver scriptnya menggunakan Python3.

{% code title="solver.py" lineNumbers="true" %}
```python
# Function to decode flag from binary array
def decode_flag(binary_array):
    # Convert binary array to string
    binary_string = ''.join(map(str, binary_array))
    
    # Split binary string into 8-bit chunks
    chunks = [binary_string[i:i+8] for i in range(0, len(binary_string), 8)]
    
    # Convert each chunk to character
    flag = ''.join([chr(int(chunk, 2)) for chunk in chunks])
    
    return flag

# Binary array from the Lua code
binary_array = [
    0,1,1,1,0,0,1,1,0,1,1,0,1,1,0,0,0,1,1,0,0,0,0,1,0,1,1,1,0,0,1,1,
    0,1,1,0,1,0,0,0,0,1,1,1,0,0,1,0,0,1,1,0,1,1,1,1,0,1,1,0,1,1,1,1,
    0,1,1,1,0,1,0,0,0,0,1,1,1,0,0,0,0,1,1,1,1,0,1,1,0,1,1,0,0,1,0,1,
    0,1,1,1,1,0,1,0,0,1,0,1,1,1,1,1,0,0,1,1,0,0,0,0,0,1,1,0,1,1,1,0,
    0,0,1,1,0,0,1,1,0,1,0,1,1,1,1,1,0,1,1,0,0,1,1,0,0,0,1,1,0,0,0,0,
    0,1,1,1,0,0,1,0,0,1,0,1,1,1,1,1,0,1,1,1,1,0,0,1,0,0,1,1,0,0,0,0,
    0,1,1,1,0,1,0,1,0,1,0,1,1,1,1,1,0,1,1,1,0,0,1,1,0,1,1,0,0,1,0,1,
    0,1,1,0,0,1,0,1,0,1,0,1,1,1,1,1,0,1,1,1,1,0,0,1,0,0,1,1,0,0,0,0,
    0,1,1,1,0,1,0,1,0,1,0,1,1,1,1,1,0,0,1,1,0,0,0,1,0,1,1,0,1,1,1,0,
    0,1,0,1,1,1,1,1,0,1,1,0,0,1,1,0,0,0,1,1,0,0,0,1,0,1,1,0,1,1,1,0,
    0,0,1,1,0,1,0,0,0,1,1,0,1,1,0,0,0,1,1,1,1,1,0,1
]

flag = decode_flag(binary_array)
print("Flag:", flag)
```
{% endcode %}

## Flag

<kbd>slashroot8{ez\_0n3\_f0r\_y0u\_see\_y0u\_1n\_f1n4l}</kbd>
