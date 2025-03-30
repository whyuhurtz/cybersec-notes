# anti-inspect

{% hint style="info" %}
Category: Web

Difficulty: very easy
{% endhint %}

## Description

> can you find the answer? **WARNING: do not open the link your computer will not enjoy it much**. URL: [_http://litctf.org:31779/_](http://litctf.org:31779/)
>
> Hint: If your flag does not work, think about how to style the output of console.log

## Solve Walkthrough

* As you can see at the description, if we open the link,your browser will crashed.
* So, I try another way to open the page, yap.. by using **cURL**.
* Simply, cURL is web browser, but in your terminal. We can perform HTTP request to the target server of course with command-line :).
* Here's the output while i'm trying to request with cURL.

```bash
curl http://litctf.org:31779/
```

<figure><img src="../../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>

* After I see the response, makes sense that we are told not to open the link through a web browser, it does **infinite loop** (look at the _while true_ statement).
* Alright, just ignore it. In the response show me the flag, but, if you read the challenge description carefully, you will notice that the flag is gone wrong !
* So, to fix that, I try to save the response into a HTML file and open it in my web browser to see the correct formatted flag.
* And yeah, we got the flag (just copy the output from your console).

<figure><img src="../../.gitbook/assets/image (3) (1).png" alt=""><figcaption></figcaption></figure>

## Flag

<kbd>LITCTF{your\_fOund\_teh\_fI@g\_94932}</kbd>
