# JAB — trading desk

A trade journal, backtest bench, rulebook and learning portal that runs entirely in your browser. Sibling to B.O.B. Same architecture: multi-profile, AES-GCM encrypted on device, synced through a private GitHub Gist, installable as a PWA.

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

## First run

JAB asks for a GitHub token before you can create or load an account. That is deliberate — it means restoring an existing journal on a new phone never requires making a throwaway account just to reach the settings page. Once the token is connected, JAB scans your gists and offers any journals it finds, so you usually never touch a gist ID by hand.

The token is stored on the device rather than inside your encrypted journal, because it has to be readable *before* you unlock. That means your passphrase does not protect it. Use a token with the `gist` scope and nothing else, and revoke it from GitHub if you lose the device. There is a "set up without syncing" link if you want to run offline only.

## Set up sync

Both devices need the same passphrase — that is what the encryption key comes from, and it is never stored or sent anywhere.

**First device**
1. Create a profile and pick a passphrase you will not lose. There is no reset.
2. GitHub → Settings → Developer settings → Personal access tokens → Tokens (classic) → Generate new token, tick **gist** only.
3. JAB → Settings → GitHub sync → paste the token, leave Gist ID blank, press **Push now**. A private gist is created and its ID fills in.

**Second device**
1. Open the same URL and paste the token when asked.
2. JAB lists the journals it finds in your gists — pick one and enter the passphrase.

After that: pull down from the top of any page to sync (or press the sync button in the header on desktop), and it pulls automatically when you unlock. It is manual on purpose — a background writer would eventually overwrite the wrong side.

> Fine-grained tokens also work, but they need read and write access to Gists. Classic tokens with the `gist` scope are simpler.

## What is where

| Section | What it does |
| --- | --- |
| **Desk** | Equity trace in R, live/backtest toggle, daily loss guardrail, week summary |
| **Trades** | Full journal with filters, rule checklist per trade, CSV export |
| **Live charts** | TradingView charts in-app, pinned symbols, symbols pulled from your journal |
| **AI lab** | Describe a strategy in plain English → testable config → real backtest on your own price data |
| **Backtest bench** | One test = one strategy over one period. Fast entry, verdict against your sample-size bar |
| **Playbook** | Win rate and expectancy by strategy family, live vs backtest per strategy, plus your trading plan |
| **Rulebook** | Rules that appear as a checklist on every trade, and a table of what breaking each one costs |
| **Reviews** | Daily/weekly/monthly write-ups, prefilled with the period's numbers |
| **Academy** | YouTube modules that play in-app, mark complete, with a takeaway box per video |
| **Glossary** | ~95 terms plus 18 strategy families, each with how to test it and when it fails |
| **Tools** | Position sizer, trade planner with break-even win rate, drawdown recovery table |
| **Platforms** | Your broker, charts and calendar, one tap away |
| **Settings** | Fifteen sections — see below |

## The AI lab, honestly

An AI cannot backtest. It has no price data and no memory of what markets did. So the lab splits the job:

1. **You describe the strategy out loud.** Instrument, timeframe, entry, stop, target, filters.
2. **The model writes a precise config** — indicators, conditions, stop and target logic, session filters — plus a plain-English spec, a list of the decisions it had to make on your behalf, an honest critique of where it will fail, and a Pine Script version.
3. **A deterministic engine in your browser runs that config** over OHLCV data you supply. No AI involved in the numbers.
4. **The trades land in your bench** as a normal test run, so they sit alongside everything else you have logged.

The engine reads signals on a closed bar and fills at the next bar's open, so there is no look-ahead. When a stop and a target both fall inside one bar it assumes the stop filled first. It models spread and commission if you give it figures, and it warns you when you have not.

Getting price data: TradingView → right-click the chart → Export chart data. Most brokers export the same shape. Needs open, high, low, close and a time column.

**Two limits worth stating.** The engine covers rule-based strategies built from moving averages, RSI, ATR, standard deviation, N-bar highs and lows, session and weekday filters. It cannot test anything discretionary — if you cannot write the rule down precisely, it cannot be tested here or anywhere else. And a config the model wrote is a first draft, not an oracle. Read the assumptions list every time; that is where it quietly decided something you did not.

The API key is yours, stored encrypted with the rest of your journal, sent only to Anthropic, billed to your own account. Everything else in the lab works without one.

## Settings

Fifteen sections. Every control changes real behaviour rather than just storing a preference.

- **General** — theme (dark / light / system), six accent colours, comfortable or compact density, content zoom, animations off, typeface (including Atkinson Hyperlegible), high contrast, larger tap targets, date format, week start, hide-money mode, landing page, confirm-before-delete.
- **Trading** — account, currency, risk %, and every default the new-trade form is pre-filled with.
- **Desk** — pick which six figures sit in the readout from eighteen available, show or hide each card, how many recent trades to list, and the default breakdown angle.
- **Metrics** — break-even band, R decimal places, whether fees come out of R, drawdown in R or percent, profit-factor display cap.
- **Risk & limits** — daily and weekly loss stops, max trades per day, consecutive-loss limit, minimum R:R, sample-size bar, and whether hitting a limit is ignored, warned, or blocked behind a confirmation.
- **Journal** — validation rules, plus which of seventeen columns the trades table shows, default sort and rows per page.
- **Lists** — your own instruments, sessions, timeframes, tags and mistake taxonomy.
- **Charts** — timeframe, candle style, timezone, toolbar, theme-follow, pinned symbols.
- **Engine** — fill at next bar open or signal bar close; when one bar hits both stop and target, assume stop first, target first, or discard the trade; trade cap.
- **AI lab** — key, model, token limit, temperature, default spread and commission, house rules appended to every request.
- **Shortcuts** — enable, and rebind each of seven single-key actions.
- **Reminders** — daily review nudge after a set time, weekly review day.
- **Sync** — token, gist ID, auto-push and auto-pull.
- **Security** — quick unlock, auto-lock when idle, lock on switching away, rename profile, change passphrase.
- **Data** — export and import, CSV separator and date format, restore starter sets, reset settings, delete profile.

**The Engine tab matters more than it looks.** Run any promising strategy on both stop-first and target-first. If the verdict flips between them, you do not have an edge — you have a coin toss on what happened inside a bar you cannot see.

## Things worth knowing

- **Everything is measured in R**, not cash. One R is the money you risked on that trade. It makes a £50 account and a £50,000 account directly comparable, and it stops a good month of position sizing from disguising a bad month of decisions.
- **Backtest trades and live trades share one table**, tagged by mode. That is deliberate — Playbook shows both for each strategy, and the gap between them is usually the most useful number in the app.
- **Icons are embedded in the file**, not loaded from the `icons/` folder, so a missing folder can never cost you the app logo. The folder is still in the zip if you want the raw PNGs.
- **Live charts need a connection.** Everything else works offline once installed.
- **The rule checklist is only worth having if you answer it honestly.** Ticking everything by reflex turns the adherence figure into decoration.
- **`Export JSON` is not encrypted.** It is a plain readable backup. Keep it somewhere private.
- **Upgrading from Bench?** Data saved under the old name is carried across automatically the first time JAB opens. Your gist keeps working too — the old filename is still recognised.
- **Lose the passphrase and the data is gone.** That is the trade-off for nobody else being able to read it — not GitHub, not anyone holding the token, not me.

## Changing things

- App name: search `const APP = {` near the top of the script in `index.html`.
- Colours and fonts: the `:root` block at the top of the stylesheet. Every colour in the app comes from those tokens, including the light theme underneath it.
- Starter rules, platforms and glossary terms: the `SEED CONTENT` section.
- After changing anything, bump `const V = 'jab-v1'` in `sw.js` or returning visitors will keep the cached old version.
