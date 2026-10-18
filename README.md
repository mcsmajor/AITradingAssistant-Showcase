<p align="center">
  <img src="assets/banner.svg" alt="AITrading — Gold Market Desk" width="100%">
</p>

<p align="center">
  <a href="https://gold.jerngub.com"><img alt="Live desk" src="https://img.shields.io/badge/live-gold.jerngub.com-E7C36F?style=for-the-badge&labelColor=0F1114"></a>
  <img alt="macOS" src="https://img.shields.io/badge/macOS-SwiftUI-EEF0F3?style=for-the-badge&logo=apple&logoColor=white&labelColor=0F1114">
  <img alt="Python" src="https://img.shields.io/badge/engine-Python-34D399?style=for-the-badge&logo=python&logoColor=white&labelColor=0F1114">
  <img alt="Cloudflare" src="https://img.shields.io/badge/web-Cloudflare%20Workers-F59E54?style=for-the-badge&logo=cloudflare&logoColor=white&labelColor=0F1114">
</p>

<p align="center">
  <b>A personal trading desk for spot gold (XAUUSD).</b><br>
  It watches the market around the clock, explains what it sees in plain language,<br>
  and sends an alert with a chart when a setup is complete. <b>The human always presses the button.</b>
</p>

<p align="center"><sub>ระบบเฝ้าตลาดทองคำส่วนตัว · แจ้งเตือนพร้อมกราฟ · คนตัดสินใจและกดออร์เดอร์เองทุกครั้ง</sub></p>

---

## ✨ Highlights

| Feature | What it does |
|---|---|
| ⚡ **Real-time** | Prices stream from MetaTrader 5 to the Mac app in ~0.25 s and to the web desk within a few seconds. |
| 🧭 **Multi-timeframe engine** | A rule-based signal engine reads closed candles across timeframes and raises a staged alert only when every condition is met. |
| 🖼️ **Alerts with a picture** | Each Telegram alert carries a freshly drawn chart, so the setup can be judged in one glance. |
| 🛡️ **Safe by design** | Read-only market access. No order-execution API, no automatic trading, no stale-data alerts. |
| 🧾 **Honest record keeping** | Every signal is frozen at the moment it is issued and stored append-only, never edited afterwards. |
| 🌗 **Light · System · Black** | The macOS app and the web desk share one design system and three themes. |

## 🖥️ macOS app

<p align="center">
  <img src="assets/app-overview-dark.png" alt="macOS app — overview, black theme" width="100%">
</p>

<table>
  <tr>
    <td width="50%"><img src="assets/app-overview-light.png" alt="macOS app — light theme"></td>
    <td width="50%"><img src="assets/app-status-dark.png" alt="macOS app — service status"></td>
  </tr>
  <tr>
    <td align="center"><sub>Light theme</sub></td>
    <td align="center"><sub>Service status and data health</sub></td>
  </tr>
</table>

A native SwiftUI app that also runs the background service: it supervises the data collector,
keeps the Mac awake during market hours, restarts a hung terminal, and raises an owner-only alert
when anything looks wrong.

## 🌐 Web desk

<p align="center">
  <img src="assets/web-desk-dark.png" alt="Web desk — gold.jerngub.com" width="100%">
</p>

A read-only, responsive desk at **[gold.jerngub.com](https://gold.jerngub.com)**: live price, daily range,
market sessions, the next high-impact event, a candle chart (24 / 48 / 96 bars) and an optional entry sound.
Only an explicit allowlist of fields ever leaves the Mac, and every snapshot is validated before it is shown.

## 🏗️ Architecture

```mermaid
flowchart LR
    MT5["MetaTrader 5<br/>read-only reader"] -->|quotes & closed bars| COL["Collector<br/>(Python)"]
    COL --> DB[("Local SQLite<br/>append-only evidence")]
    DB --> ENG["Signal engine<br/>multi-timeframe rules"]
    NEWS["News & calendar<br/>feeds"] --> ENG
    ENG --> OUT["Outbox<br/>dedup · expiry · fresh-price gate"]
    OUT -->|text + chart| TG["Telegram"]
    DB --> APP["macOS app<br/>SwiftUI"]
    DB -->|allowlisted snapshot| WEB["Web desk<br/>Cloudflare Workers + D1"]
    SUP["Native supervisor<br/>watchdog · keep-awake"] -.-> COL
    SUP -.-> MT5
```

**Principles**

- **Human in the loop** — the system proposes, the trader decides. There is deliberately no path from a signal to an order.
- **No signal on bad data** — stale quotes, data gaps, wide spreads or a forming candle block new alerts.
- **Evidence first** — each alert stores its inputs at issue time; outcomes are tracked separately from real trades, and unknown stays unknown.
- **Secrets stay local** — tokens and ids live only in ignored local files, never in Git.

## 🧰 Tech stack

| Layer | Technology |
|---|---|
| Market data | MetaTrader 5 (MQL5 reader) on macOS |
| Engine & services | Python 3, SQLite, launchd |
| Desktop | Swift, SwiftUI, AppKit (native chart renderer) |
| Web | React, TypeScript, Cloudflare Workers, D1 |
| Messaging | Telegram Bot API |
| Quality | ~940 automated Python tests, native self-tests, web tests |

## 🔒 About the source

This repository is a **showcase**. The full source code, the signal rules and all trading data
are kept in a **private** repository.

---

<p align="center"><sub>
⚠️ For personal research and monitoring only. Nothing here is financial advice, a recommendation to trade,
or a promise of profit. Trading gold carries a high risk of loss.<br>
© 2026 mcsmajor · All rights reserved.
</sub></p>
