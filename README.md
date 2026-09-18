# trading-fleet

A small multi-bot algorithmic trading platform running in paper mode at Interactive Brokers. Started September 2025 as a first coding project, now a fleet of six bots sharing one account.

> Built with AI coding assistants under my direction. What gets built, kept, and killed is my call.

## What runs

- Six strategies under test at the same time: intraday opening-range and VWAP variants on US large-caps, and noise-area strategies on micro S&P and Nasdaq futures.
- Each bot is its own process with its own broker connection, config, and state directory.
- A shared bar recorder, an account-level watchdog, and a nightly position reconciler sit alongside the trading bots.
- Everything boots, trades, and shuts down on a schedule without me at the keyboard.

## How it is built

- Python, ib_async, IB Gateway controlled by IBC on Windows.
- Bracket orders with one-cancels-all exits, reconnect handling, orphan-position recovery, daily loss caps.
- Push alerts when the fleet dies, when a bot disconnects, or when positions do not reconcile.
- Test suite in the low thousands, run before every change ships.
- A separate research framework with hash-locked strategy specs and pre-declared kill criteria. Most candidates it has tested were killed.

## What I am not claiming

- No performance numbers are published. The fleet changes continuously, so any number would be stale within weeks.
- Paper mode only. No real money has been traded.
- Source is private. Ask if you want to see specific parts.

## Contact

- Email: lukawanne@gmail.com
- LinkedIn: [linkedin.com/in/luka-wanne-300203237](https://www.linkedin.com/in/luka-wanne-300203237)
