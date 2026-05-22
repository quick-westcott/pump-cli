# pumpfun-launch-cli

A minimal CLI launcher for [pump.fun](https://pump.fun) coins on the [TrenchyTools API](https://trenchy.tools/docs): one file, no RPC node, no Jito client, no IPFS keys. Your wallet keys are generated locally, sign locally, and never leave your machine.

## Setup

```bash
npm install
cp .env.example .env   # then put your TRENCHY_API_KEY in it (GET https://api.trenchy.tools/api/create-api-key)
```

## Launch a coin

```bash
node launch.mjs --name "My Coin" --symbol MYC --image coin.png --dev-buy 0.05
```

Options:

| flag | meaning | default |
|---|---|---|
| `--name` | token name (required) | |
| `--symbol` | ticker (required) | |
| `--image` | image file (required) | |
| `--dev-buy` | dev buy in SOL | 0 |
| `--description` | description | empty |
| `--twitter` / `--telegram` / `--website` | socials | none |

The dev and mint keypairs are generated fresh, the create is built by the API, signed here, and sent back. Keys land in `dev-key.json` / `mint-key.json` next to the script; keep them.

## How it works

1. `POST /api/ipfs` pins the image + metadata (a `run record` of the launch's wallets is sealed into the metadata, X25519 + ChaCha20-Poly1305, `run-seal.mjs`)
2. `POST /api/sol/trade-local` builds the pump.fun create (+ dev buy) as an unsigned transaction
3. the CLI signs with the dev + mint keypairs and `POST /api/sol/send` sends it
