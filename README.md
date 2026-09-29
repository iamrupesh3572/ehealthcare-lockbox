# E-Healthcare Lockbox (EHR Blockchain)

A patient-controlled electronic health record (EHR) system built on Ethereum. Patients own their data, store it encrypted on IPFS, and grant doctors **time-bound access** that expires automatically.

## Features

- Patient-controlled health records stored on-chain (references) and IPFS (encrypted files)
- Time-bound consent: access expires automatically after the period the patient sets
- MetaMask wallet login
- Angular frontend using ethers.js
- Local development on an Anvil blockchain, deployed with Hardhat Ignition

## Tech Stack

| Layer | Technology |
| --- | --- |
| Smart contracts | Solidity, Hardhat, Hardhat Ignition |
| Local blockchain | Anvil (Foundry) |
| File storage | IPFS (Kubo), kubo-rpc-client |
| Frontend | Angular, ethers.js |
| Wallet | MetaMask |

## Project Structure

```
.
├── contracts/
│   └── EHR.sol              # Main smart contract
├── ignition/
│   └── modules/
│       └── EHR.ts           # Hardhat Ignition deployment module
├── src/                     # Angular frontend
├── deployer.sh              # Compile + deploy + seed script
├── start-project.sh         # Starts IPFS, Anvil and the Angular app
├── hardhat.config.ts
├── .env.example             # Copy to .env and fill in your values
└── README.md
```

## Prerequisites

Install these before you start:

- [Node.js](https://nodejs.org/) v18 or later (with npm)
- [Foundry](https://book.getfoundry.sh/getting-started/installation) (provides `anvil`)
- [IPFS Kubo](https://docs.ipfs.tech/install/command-line/) (provides the `ipfs` command)
- [MetaMask](https://metamask.io/) browser extension
- Angular CLI (optional, `npx ng` works without installing it globally)

Check that everything is installed:

```bash
node -v
anvil --version
ipfs --version
```

## Setup

### 1. Clone the repository

```bash
git clone https://github.com/<your-username>/<repo-name>.git
cd <repo-name>
```

### 2. Install dependencies

```bash
npm install
```

### 3. Create your `.env` file

The real `.env` is not included in this repo for security reasons. Create it from the example:

```bash
cp .env.example .env
```

Open `.env` and fill in the values. See `.env.example` for what each variable does.

### 4. One-time IPFS setup

Initialise IPFS and allow the browser app to talk to your local node:

```bash
ipfs init
ipfs config --json API.HTTPHeaders.Access-Control-Allow-Origin '["http://localhost:4200"]'
ipfs config --json API.HTTPHeaders.Access-Control-Allow-Methods '["PUT", "POST", "GET"]'
```

## Running the Project

You need **four terminals**, all opened in the project folder. Run them in this order.

**Terminal 1: start IPFS**

```bash
ipfs daemon
```

**Terminal 2: start the local blockchain**

```bash
anvil
```

Leave it running. It prints 10 test accounts with private keys. These are public test keys and only work on your local chain.

**Terminal 3: compile and deploy the contract**

```bash
npx hardhat compile
npx hardhat ignition deploy ignition/modules/EHR.ts --network localhost
```

Note the deployed contract address printed at the end. If your frontend needs it, put it in the config file or `.env` value mentioned in `.env.example`.

Shortcut: `./deployer.sh` runs compile, deploy and seed in one step.

**Terminal 4: start the frontend**

```bash
npx ng serve
```

Open **http://localhost:4200** in your browser.

> Shortcut: `./start-project.sh` starts IPFS, Anvil and the Angular app together. On Linux/macOS run `chmod +x start-project.sh deployer.sh` first. On Windows, use Git Bash or WSL.

## Connect MetaMask to Anvil

1. Open MetaMask, then **Add network** and **Add a network manually**.
2. Enter:
   - Network name: `Anvil Local`
   - RPC URL: `http://127.0.0.1:8545`
   - Chain ID: `31337`
   - Currency symbol: `ETH`
3. Import one of the test accounts printed by Anvil: **Account menu, Import account, paste a private key**.
4. Use only these test accounts here. Never import a real wallet's key for local testing.

## How to Use

1. Connect MetaMask on the home page.
2. As a **patient**, upload a health record. It is encrypted and stored on IPFS, and its reference is saved on-chain.
3. Grant a **doctor** access for a chosen time period.
4. Switch to the doctor account in MetaMask to view the record while consent is active.
5. After the consent period ends, access expires automatically.

## Troubleshooting

| Problem | Fix |
| --- | --- |
| IPFS upload fails or shows a CORS error | Repeat the IPFS CORS commands in step 4, then restart `ipfs daemon` |
| MetaMask shows a nonce or wrong-state error after restarting Anvil | MetaMask, then Settings, Advanced, **Clear activity tab data** |
| Contract calls fail after restarting Anvil | Anvil resets on restart. Redeploy the contract and update the address |
| `anvil` or `ipfs` command not found | Install the prerequisites and reopen your terminal |
| Port 4200, 8545 or 5001 already in use | Stop the other process using that port |

## Security Notes

- `.env` and private keys are never committed. Only `.env.example` is in the repo.
- The Anvil accounts are public test keys. Do not use them on a real network.
- This is a learning/demo project and has not been audited. Do not use it with real patient data.

## Author

**Rupesh Kumar**
GitHub: [@<your-username>](https://github.com/iamrupesh3572)

## License

MIT (add a `LICENSE` file if you want to use this)

