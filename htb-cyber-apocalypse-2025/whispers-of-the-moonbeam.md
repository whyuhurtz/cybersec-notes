# Whispers of the Moonbeam

{% hint style="info" %}
Category: Web

Difficulty: Very Easy
{% endhint %}

## Description

> In the heart of Valeria's bustling capital, the Moonbeam Tavern stands as a lively hub of whispers, wagers, and illicit dealings. Beneath the laughter of drunken patrons and the clinking of tankards, it is said that the tavern harbors more than just ale and merriment—it is a covert meeting ground for spies, thieves, and those loyal to Malakar's cause. The Fellowship has learned that within the hidden backrooms of the Moonbeam Tavern, a crucial piece of information is being traded—the location of the Shadow Veil Cartographer, an informant who possesses a long-lost map detailing Malakar’s stronghold defenses. If the fellowship is to stand any chance of breaching the Obsidian Citadel, they must obtain this map before it falls into enemy hands.

## Required Knowledge

* Command Injection

## Solve Walkthrough

When open the web url, type `help` to see list what commands that can we use. There's a lot of commands that can we use, but the `gossip` command is have behavior like `ls` command. We can directly see the `flag.txt` file.

<figure><img src="../.gitbook/assets/image (53).png" alt=""><figcaption></figcaption></figure>

Okay, now let's find out how to read that `flag.txt` file. Simply, we can use **semicolon as delimiter of second command**. So, the first command: `gossip` is to bypass the command check and `; cat flag.txt` is to read the flag.&#x20;

Here's my POC to read the flag.

```bash
gossip; cat flag.txt
```

<figure><img src="../.gitbook/assets/image (55).png" alt=""><figcaption></figcaption></figure>

## Flag

<kbd>HTB{Sh4d0w\_3x3cut10n\_1n\_Th3\_M00nb34m\_T4v3rn\_df37873135314ddc601fbc674ec2339f}</kbd>
