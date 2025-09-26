🔍 Solana Scanner

A high-performance Solana blockchain scanner written in Rust.
It continuously monitors Solana slots, processes blocks, and applies customizable filters to detect specific on-chain activity (e.g., Raydium V4 liquidity events).
Optionally, events can be published to RabbitMQ for downstream processing.

✨ Features

⏱ Real-time scanning of Solana mainnet (via RPC + WebSocket).

⚡ Concurrent block processing with configurable queue capacity.

🧩 Pluggable transaction filters (currently supports Raydium V4 Liquidity).

📡 RabbitMQ integration (optional) for event publishing.

🛠 Configurable via .env file (easy to tweak without code changes).

📜 Structured logging with tracing.

✅ Error-handling & graceful shutdown via Ctrl+C.

📂 Project Structure
src/
 ├── main.rs          # Entry point
 ├── config.rs        # Loads environment configuration
 ├── error.rs         # Error definitions
 ├── filter.rs        # Trait for building custom filters
 ├── filters/         # Built-in filters (e.g. Raydium V4 Liquidity)
 ├── scanner.rs       # Core scanner logic
 └── rabbitmq.rs      # RabbitMQ integration (feature-gated)

⚙️ Configuration

Set up your .env file:

# Solana RPC Configuration
SOLANA_RPC_URL=https://api.mainnet-beta.solana.com
SOLANA_WS_URL=wss://api.mainnet-beta.solana.com

# Scanner Configuration
SCAN_INTERVAL_MS=1000
MAX_CONCURRENT_BLOCKS=10
QUEUE_CAPACITY=1000

# RabbitMQ Configuration
RABBITMQ_ENABLED=false
RABBITMQ_URL=amqp://guest:guest@localhost:5672
RABBITMQ_PREFIX=solana_scanner

# Filter Configuration
ENABLED_FILTERS=raydium_v4_liquidity

Key Parameters
Variable	Description	Default
SOLANA_RPC_URL	Solana RPC endpoint	mainnet-beta RPC
SOLANA_WS_URL	Solana WebSocket endpoint	mainnet-beta WS
SCAN_INTERVAL_MS	Interval (ms) between slot checks	1000
MAX_CONCURRENT_BLOCKS	Maximum blocks processed in parallel	10
QUEUE_CAPACITY	Capacity of internal slot queue	1000
RABBITMQ_ENABLED	Enable RabbitMQ integration	false
RABBITMQ_URL	RabbitMQ connection string	amqp://guest:guest@localhost
RABBITMQ_PREFIX	Queue prefix name	solana_scanner
ENABLED_FILTERS	Comma-separated list of enabled filters	raydium_v4_liquidity
🚀 Getting Started
1. Clone the repo
git clone https://github.com/yourusername/solana-scanner.git
cd solana-scanner

2. Install dependencies

Ensure you have Rust (1.70+) and Cargo installed.

cargo build

3. Configure environment

Copy .env.example → .env and update values as needed.

4. Run the scanner
cargo run

🧩 Filters

Filters are modular components implementing the TransactionFilter trait.

Available Filters

Raydium V4 Liquidity Filter
Detects liquidity pool creation & updates in Raydium's V4 contracts.

Example: Enabling a filter
ENABLED_FILTERS=raydium_v4_liquidity

📡 RabbitMQ Integration

If RABBITMQ_ENABLED=true, matched transactions are published to queues.

Queue names are prefixed with RABBITMQ_PREFIX.

Each filter has its own queue (e.g., solana_scanner_raydium_v4_liquidity).

Messages are serialized JSON payloads of processed transactions.

🛠 Development
Run tests
cargo test

With feature flags

With RabbitMQ:

cargo run --features rabbitmq


Without RabbitMQ:

cargo run --no-default-features

📊 Architecture

Slot Monitor
Polls the Solana RPC for the latest slot and pushes new slots into a queue.

Block Processor
Concurrently fetches blocks from the queue, applies filters, and handles matches.

Filters
Each filter checks transactions and processes matches (e.g., extract event data).

RabbitMQ (optional)
Matched events are published to message queues for external consumption.
