## ✨ Solana Sniper Bot (Rust) — Yellowstone gRPC + Helius Sender

<p align="center">
  <img src="SNIPER.png" height="130" alt="Solana Sniper Bot">
</p>

<div align="center">

[![CI](https://img.shields.io/github/actions/workflow/status/bogardt/solana-sniper-bot/ci.yml?label=build)](https://github.com/bogardt/solana-sniper-bot/actions)
![Rust async](https://img.shields.io/badge/Rust-async-93450B?logo=rust)
![Solana 1.18](https://img.shields.io/badge/Solana-1.18-purple)
![Made&nbsp;with&nbsp;❤](https://img.shields.io/badge/Made_with-❤-ff69b4)

</div>

> **Solana Sniper Bot** is a lightning‑fast copy‑trading / MEV engine that
> mirrors Pump.fun launches and whale transfers in **under 150 ms**.  
> Mempool via Yellowstone gRPC ➜ atomic Jito bundles ➜ dynamic TP / SL.

---

A high-performance sniper bot for Solana that:
- **Streams blockchain data in real-time** via **Yellowstone gRPC**, using Solana's Geyser plugin for ultra-low latency data. :contentReference[oaicite:1]{index=1}
- **Submits transactions** using **Helius Sender**, which dual-routes to validators and Jito to maximize inclusion speed. :contentReference[oaicite:2]{index=2}
- Persists metrics/signals to **MongoDB** for analytics and observability.

## 💬 Table of Contents

1. [Architecture](#architecture)  
2. [Prerequisites](#prerequisites)  
3. [Installation & Running Instructions](#installation--running-instructions)  
4. [Streaming Data: Yellowstone gRPC](#streaming-data-yellowstone-grpc)  
5. [Fast Transaction Submission: Helius Sender](#fast-transaction-submission-helius-sender)  
6. [Observability & Database](#observability--database)  
7. [Security & Best Practices](#security--best-practices)  
8. [Troubleshooting](#troubleshooting)  
9. [References](#references)

---

## ❤ Architecture

```

\[ Yellowstone gRPC Stream ]
↓
\[ Rust Bot Logic ]
↓ (signed tx)
\[ Helius Sender API ]
↓
Solana Network

```
         ↓
    [ MongoDB Logging ]
```

```

- **Yellowstone gRPC** provides real-time streaming of slots, account updates, transactions, blocks—all with minimal delay. :contentReference[oaicite:3]{index=3}  
- **Helius Sender** submits your signed transactions to both Solana validators and the Jito network concurrently, improving the likelihood of fast inclusion. It's free and high-throughput. :contentReference[oaicite:4]{index=4}

---

## Prerequisites

- Rust (stable toolchain)  
- MongoDB instance accessible via connection string  
- **Helius API Key** (for RPC access + Sender usage)  
- **Yellowstone gRPC endpoint & token** (provided by your RPC provider, e.g., QuickNode, Helius, Shyft)

---

## 🏗 Installation & Running Instructions

1. Clone the repository and add your `.env` file.  
2. Install Rust and ensure `cargo` is in your PATH.  
3. (Optional) Install and run MongoDB locally or via Docker:  
```bash
# on macOS
brew services start mongodb-community
````

4. Build the bot:

```bash
cargo build --release
```
5. Run the bot:

```bash
cargo run "token_creator_wallet_address"
```

You can override `RUST_LOG` in `.env` or command line.

---

## 🚀 Streaming Data: Yellowstone gRPC

* Provides ultra-low latency real-time streaming of Solana data: accounts, transactions, slots, blocks. ([Helius][1], [QuickNode][2], [QuickNode][3], [Chainstack][4], [Helius][5])
* Supports advanced filtering by program ID, accounts, transaction types. ([Helius][5])
* Use LaserStream (recommended for reliability/backfill) or Dedicated Nodes based on load. ([Helius][6])
* Implement **auto-reconnect**, **ping keepalive**, and backoff for stability.

---

## ⚡ Fast Transaction Submission: Helius Sender

* **Helius Sender** optimizes latency by simultaneously sending your transaction to validators and Jito. ([Helius][1])
* No API credits consumed; includes global HTTP endpoints. ([Helius][1])
* Requires a tip (≥ 0.001 SOL) to function efficiently. ([Helius][1])
* Build your transaction using `ComputeBudgetProgram.setComputeUnitLimit/setComputeUnitPrice` to tune priority fee.

---

## 📦 Observability & Database

* Persist signals, actions, and errors to MongoDB for post-mortem and analytics.
* Optional: add indexes (e.g., `created_at`, `mint`, `program_id`) and TTLs.
* Log key metrics: send rate, latency, success/failure, priority fee values.

---

## 🛡 Security & Best Practices

* **Never commit** your `.env` or private keys.
* Use separate wallets for operations vs trading.
* Include rate-limiting logic if hitting Helius quotas.
* Regularly **rotate tokens/keys** and **backup** your database.

---

## 🌊 Troubleshooting

* **Streaming lag in JS clients**: Node.js may lag under high throughput; Rust or Go handle streaming data more reliably. ([Helius][1], [Helius][7], [Solana Stack Exchange][8])
* **Choosing compute units**: Simulate to estimate limits, add \~10% headroom, and set price via Helius priority fee estimate API.
* **Yellowstone access**: Get endpoint/token from your provider’s dashboard (QuickNode, Helius, Shyft, etc.). ([QuickNode][2])

---

## 📄 References

* **Yellowstone gRPC Streaming**: ultra-low latency streaming with filtering. ([Helius][5])
* **Account / Transaction / Slot Monitoring** using Yellowstone. ([Helius][9])
* **Helius Sender API**: dual-routing to validators & Jito, no credits required, priority tip. ([Helius][1])
* **Data Streaming Options (LaserStream / WebSockets)**. ([Helius][7])


[1]: https://www.helius.dev/docs/sending-transactions/sender?utm_source=chatgpt.com "Helius Sender: Ultra-Low Latency Solana Transaction ..."
[2]: https://www.quicknode.com/docs/solana/yellowstone-grpc/overview?utm_source=chatgpt.com "Yellowstone gRPC - Solana Geyser Streaming"
[3]: https://www.quicknode.com/guides/solana-development/tooling/geyser/yellowstone?utm_source=chatgpt.com "Monitor Solana Programs with Yellowstone Geyser gRPC ..."
[4]: https://docs.chainstack.com/docs/yellowstone-grpc-geyser-plugin?utm_source=chatgpt.com "Yellowstone gRPC Geyser plugin"
[5]: https://www.helius.dev/docs/grpc?utm_source=chatgpt.com "Solana Yellowstone gRPC: Real-Time Data Streaming"
[6]: https://www.helius.dev/docs/grpc/quickstart?utm_source=chatgpt.com "Solana Yellowstone gRPC Quickstart: Real-Time Data ..."
[7]: https://www.helius.dev/docs/data-streaming?utm_source=chatgpt.com "Solana Data Streaming - Helius Docs"
[8]: https://solana.stackexchange.com/questions/22909/solana-yellowstone-geyser-grpc-with-javascript?utm_source=chatgpt.com "solana yellowstone geyser grpc with javascript"
[9]: https://www.helius.dev/docs/grpc/transaction-monitoring?utm_source=chatgpt.com "Transaction Monitoring with Yellowstone gRPC"

---

## 💬 Community & Commercial Edition

* Telegram → **@bogardt**
* Discord → `kama_92`

---

**Want the full ultra‑low‑latency engine?**
DM for single‑user licences (private repo + EULA).

---

**License**: MIT (demo edition). Commercial licence available for the full build.
