# XAU/USD M5 Breakout Scanner (RBS / SBR)

A single-file browser tool that watches **Gold (XAU/USD)** on the **5-minute (M5)** timeframe
and fires a **desktop notification + sound** each time a breakout-retest signal completes.

## Signals

- **RBS — Resistance Becomes Support** (bullish / BUY): price breaks *above* a resistance level,
  pulls back to retest it, holds, and closes back up.
- **SBR — Support Becomes Resistance** (bearish / SELL): price breaks *below* a support level,
  rallies back to retest it, rejects, and closes back down.

Levels are recent swing pivots. A signal needs three things in order: **break → retest → hold/reject**.

## Data sources

Pick one in the **Data source** dropdown:

| Source | Setup | Data |
|---|---|---|
| **OANDA XAU/USD** (default) | Free practice account + API token | Real broker gold, M5 candles, direct from browser (CORS OK). Closed weekends. |
| **Binance PAXG** | None | Gold-backed token (≈ spot gold), 24/7, no key/signup. |
| **Twelve Data** | Free API key | Real XAU/USD, 800 req/day. |
| **Simulation** | None | Fake random-walk gold — for testing alerts. |

### OANDA setup (real broker gold)
1. Create a free **practice** (demo) account at [oanda.com](https://www.oanda.com/).
2. In the account portal open **Manage API Access** and **generate a personal access token**.
3. In the app: Data source → **Live — OANDA XAU/USD**, paste the token, leave env on **Practice**, click **Save**.
4. Click **Start scanning**. Uses `GET /v3/instruments/XAU_USD/candles?granularity=M5&price=M`.
   - `401`? Token wrong, or you made a Live token but left the app on Practice (or vice-versa).

## Run it

Open `index.html` in your browser (or serve the folder: `python3 -m http.server 8123`
then visit `http://localhost:8123`). Notifications work best over `http://localhost` or `https://`.

1. Click **Enable notifications** and allow the browser prompt.
2. **Simulation mode** runs immediately with no key — click **Inject demo pattern** to watch a real signal fire.
3. For **live gold**, pick **Live — Twelve Data**, paste a free API key
   ([twelvedata.com](https://twelvedata.com/pricing)), Save, then **Start scanning**.

## Hit-rate tracker

Each fired signal is forward-tested from its entry (the confirmation candle's close):
within **Look** bars, did price hit **Target ×ATR** (win) or **Stop ×ATR** (loss) first?
The card shows a running **hit-rate** with W / L / open counts and a bar, and every signal
gets a ✅ win / ❌ loss / ⏳ open badge. Tune Target / Stop / Look live to see how the edge changes.
*Approximation only* — same-bar ties count as the stop (conservative), no spread/slippage modeled.

## M5 candle countdown

The header shows time until the current 5-minute candle closes (goes amber in the last 10s).
Optional **tick last 5s** beeps the final seconds. When a candle closes, live sources refetch
immediately so a fresh signal shows without waiting for the next poll.

## Roadmap / next update

- [ ] **Telegram alerts** — push signals to your phone via a Telegram bot (bot token + chat ID)
  so you're notified even with the tab closed. *Queued for next session.*

## Notes

- Free Twelve Data tier = 800 requests/day, so live mode polls every **120s** (plenty for M5 bars).
- Tune **Break buffer** / **Retest tol** (both in ×ATR) higher for fewer, cleaner signals.
- This is a **signal/education tool, not financial advice** and it places no orders.
- Want a different data source (MetaTrader bridge, another broker API, forex pairs, or crypto)?
  Swap the `fetchLive()` function — it just needs to return an array of `{t,o,h,l,c}` candles.
