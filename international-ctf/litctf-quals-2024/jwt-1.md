# jwt-1

{% hint style="info" %}
Category: Web

Difficulty: very easy
{% endhint %}

## Description

> I just made a website. Since cookies seem to be a thing of the old days, I updated my authentication! With these modern web technologies, I will never have to deal with sessions again. Come try it out at http://litctf.org:31781/.

## Solve Walkthrough

* As you can know from the challenge title, it shoule be correlation with JWT token, so prepare for https://jwt.io website.
* The website have 3 features/endpoints, that is:
  * Sign up -> **/signup/**
  * Log in -> **/login/**
  * Get the flag -> **/flag**
* Of course we don't know admin password, but after you login (or signup if you don't have an account before), you can see new generated JWT token in the **Storage > Cookies** of your developer tools.
* Copy that JWT token to the jwt.io website, and you will see the payload data contain `name` and `admin`.
* I change the `admin` value to **true**, and then I replace the original JWT token cookies to crafted JWT token payload.

<figure><img src="../../.gitbook/assets/image (4) (1).png" alt=""><figcaption></figcaption></figure>

* When I try to visit the **/flag** endpoint, it show me the flag.

<figure><img src="../../.gitbook/assets/image (5) (1).png" alt=""><figcaption></figcaption></figure>

## Flag

<kbd>LITCTF{o0ps\_forg0r\_To\_v3rify\_1re4DV9}</kbd>
