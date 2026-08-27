# Bench — trading desk

A trade journal, backtest bench, rulebook and learning portal that runs entirely in your browser. Same architecture as B.O.B.: multi-profile, AES-GCM encrypted on device, synced through a private GitHub Gist, installable as a PWA.

```
index.html            the whole app (HTML + CSS + JS in one file)
manifest.webmanifest  makes it installable
sw.js                 offline cache for the app shell
icons/                app icons
```

## Deploy to GitHub Pages

1. Create a repo — public is fine, none of your journal data lives in it.
2. Drop these files in the root and push.
3. Settings → Pages → Source: `Deploy from a branch`, branch `main`, folder `/ (root)`.
4. Wait a minute, then open `https://<you>.github.io/<repo>/`.

HTTPS matters. Without it the service worker, install prompt and device unlock all stay switched off.

**Install it:** iPhone — Safari, Share, Add to Home Screen. Android — Chrome, menu, Install app. Desktop — install icon in the address bar. Once installed it opens full screen with no browser chrome.

## Set up sync

Both devices need the same passphrase — that is what the encryption key comes from, and it is never stored or sent anywhere.

**First device**
1. Create a profile and pick a passphrase you will not lose. There is no reset.
2. GitHub → Settings → Developer settings → Personal access tokens → Tokens (classic) → Generate new token, tick **gist** only.
3. Bench → Settings → GitHub sync → paste the token, leave Gist ID blank, press **Push now**. A private gist is created and its ID fills in.

**Second device**
1. Open the same URL, choose **Restore from GitHub** on the lock screen.
2. Enter the token, the Gist ID, and the same passphrase.

After that, press Push when you finish a session and it pulls automatically when you unlock. It is manual on purpose — a background writer would eventually overwrite the wrong side.

> Fine-grained tokens also work, but they need read and write access to Gists. Classic tokens with the `gist` scope are simpler.

## What is where

| Section | What it does |
| --- | --- |
| **Desk** | Equity trace in R, live/backtest toggle, daily loss guardrail, week summary |
| **Trades** | Full journal with filters, rule checklist per trade, CSV export |
| **Backtest bench** | One test = one strategy over one period. Fast entry, verdict against your sample-size bar |
| **Playbook** | Written strategies with live vs backtest performance side by side, plus your trading plan |
| **Rulebook** | Rules that appear as a checklist on every trade, and a table of what breaking each one costs |
| **Reviews** | Daily/weekly/monthly write-ups, prefilled with the period's numbers |
| **Academy** | YouTube modules that play in-app, mark complete, with a takeaway box per video |
| **Glossary** | ~95 terms plus 18 strategy families, each with how to test it and when it fails |
| **Tools** | Position sizer, trade planner with break-even win rate, drawdown recovery table |
| **Platforms** | Your broker, charts and calendar, one tap away |

## Things worth knowing

- **Everything is measured in R**, not cash. One R is the money you risked on that trade. It makes a £50 account and a £50,000 account directly comparable, and it stops a good month of position sizing from disguising a bad month of decisions.
- **Backtest trades and live trades share one table**, tagged by mode. That is deliberate — Playbook shows both for each strategy, and the gap between them is usually the most useful number in the app.
- **The rule checklist is only worth having if you answer it honestly.** Ticking everything by reflex turns the adherence figure into decoration.
- **`Export JSON` is not encrypted.** It is a plain readable backup. Keep it somewhere private.
- **Lose the passphrase and the data is gone.** That is the trade-off for nobody else being able to read it — not GitHub, not anyone holding the token, not me.

## Changing things

- App name: search `const APP = {` near the top of the script in `index.html`.
- Colours and fonts: the `:root` block at the top of the stylesheet. Every colour in the app comes from those tokens, including the light theme underneath it.
- Starter rules, platforms and glossary terms: the `SEED CONTENT` section.
- After changing anything, bump `const V = 'bench-v1'` in `sw.js` or returning visitors will keep the cached old version.
