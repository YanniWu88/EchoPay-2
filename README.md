# EchoPay: Voice-Activated Payment System (Proof of Concept)

EchoPay is an innovative payment platform concept enabling users to conduct secure, cross-chain transactions through voice commands. This repository contains the initial proof-of-concept implementation focusing on the frontend interface, wallet connection, and voice input simulation.

Demo: https://www.youtube.com/watch?v=lrFvO2qnxU0

## Overview (Vision)

By integrating advanced AI voice recognition with Polkadot’s blockchain infrastructure, EchoPay aims to eliminate traditional payment friction while prioritizing accessibility, security, and interoperability. The ultimate goal is to allow users to execute transactions via natural language (e.g., “Pay 50 DOT to Alice”).

## How it work
![How it Work Now](docs/Screenshot%202025-04-19%20225750.png)

The smart contract itself cannot directly initiate a native DOT transfer from the user's account. The actual DOT transfer will be initiated by the user through their wallet (SubWallet, Talisman), triggered by your frontend application after the voice command is processed.
The smart contract's primary role will be to record the details of these payments after they happen (or are initiated).

## Features (Current Implementation)

*   **React Frontend:** User interface built with React and Vite (TypeScript).
*   **Wallet Connection:** Connects to Polkadot JS compatible browser extensions (e.g., SubWallet, Talisman) using `@polkadot/extension-dapp`.
*   **Account Selection:** Lists accounts from the connected extension and allows selection.
*   **Balance Display:** Shows the selected account's balance by querying the Westend testnet using `@polkadot/api`.
*   **Voice Input:** Uses the browser's Web Speech API to capture voice commands.
*   **Manual Processing Trigger:** Requires clicking a button to process the recognized voice command (currently sends to a mock backend).
*   **Mock Backend:** A simple Node.js/Express server that simulates receiving and acknowledging commands.
*   **Contact List:** Displays a static, hardcoded list of contacts in a sidebar. testing reason hard coded for now
*   **Disconnect:** Allows disconnecting the wallet within the application's state.


## Why Record the Transaction Afterwards?
![Why Record the Transaction Afterwards?](docs/Screenshot%202025-04-20%20011229.png)

##  Why Not Just Use the Native Transfer?
Even though the transaction is processed on Polkadot via the user’s wallet, recording it in a smart contract provides an immutable, auditable record that can be referenced for receipts, compliance, transparency, and additional business logic. This dual-step approach is a common pattern for decentralized applications where user-initiated actions need to be tracked or verified independently of the native asset transfer.

We could also log additional information, such as location recording, geotagging, and a link to IPFS (InterPlanetary File System).

![Extended Data Section](Screenshot%202025-04-20%20060607.png)


## ROADMAP
Version 1 Aug 2024

* EchoPay is a voice-activated Ethereum transaction application that interacts with the Moonbase Alpha network (a test network for the Moonbeam blockchain). It allows users to send transactions and check balances using voice commands.

Version 2 19 April 2025
*  Colour Scheme Updates:
*  GUI interface separated from CLI
*  Link to SubWallet / Activation
*  Animation added for loading
*  Improved layout
*  Enhanced AI for voice input
*  Dedicated status message area created
*  Responsive layout with column stacking
*  Now reads balance from wallet
*  Signing out of the wallet from Echo Pay no longer signs you out of SubWallet
*  Allows selection of multiple wallets
*  Migrated off Moonbase network

## Tech Stack

*   **Frontend:** React, Vite, TypeScript, CSS
*   **Blockchain Interaction (Client-side):** `@polkadot/api`, `@polkadot/extension-dapp`, `@polkadot/util`
*   **Voice Input:** Web Speech API (Browser-native)
*   **Mock Backend:** Node.js, Express, CORS

## Voice Payment system on Polkadot
![Voice Payment system on Polkadot
](docs/Screenshot%202025-04-19%20174748.png)

Frontend Application (dApp)
* Voice Capture: Uses the Web Speech API to record voice commands.
* Transcription: Sends audio to a service (e.g., AWS Transcribe) for text conversion.
* Instruction Parsing: Extracts payment details (e.g., "Pay 5 DOT to Alice").
* Transaction Construction: Builds a payload with recipient/amount using @polkadot/api.
```
const transfer = api.tx.balances.transferKeepAlive(recipientAddress, amount);
```
* User Signing: Connects to SubWallet for transaction review and signing.
Polkadot Smart Contract (ink!)
* Payment Execution: Receives parsed instructions and either:
* Transfers DOT (if contract holds funds).
* Interacts with a PSP22 token contract to move approved tokens from the user to the recipient.
* Logging: Records transaction details on-chain for auditability

## Smart Contract Plan (ink!)
![Smart Contract Plan (ink!)
](docs/Screenshot%202025-04-19%20175055.png)


Why the Smart Contract Can't Move DOT Directly
* Your Wallet = Your Control
* Only you can send DOT from your account using your private key (like a password). The smart contract doesn't have access to this key.
* When you say "Send 5 DOT," your voice app prepares the transaction, but SubWallet (your crypto wallet) asks you to approve it first.

Smart Contracts Follow Rules
* Smart contracts on Polkadot are like vending machines: they only do what they're programmed to do after you initiate an action.
* They can’t “reach into” your wallet – you must start the process.
  
What the Smart Contract Does Do
* Records the Transaction
* After you approve the DOT transfer via SubWallet, the smart contract logs:


```
 "User [Your Address] sent 5 DOT to [Recipient] at [Time]."
```
This creates a permanent, tamper-proof record on the blockchain.

Checks for Errors
It can verify if the transfer followed rules (e.g., "Was the recipient address valid?").

Example Flow:
* 1. You say: “Send 5 DOT to Alice.”
* 2. Voice app converts this to text and prepares a transaction.
* 3. SubWallet pops up: “Approve sending 5 DOT?” ✅
* 4. You click Approve – DOT moves from your wallet to Alice’s.
* 5. Smart contract adds: ✅ “Payment confirmed!” to the blockchain.
* This keeps you in control while using the smart contract as a secure receipt tracker 🔐.

## Summary of Workflow:
![Summary of Workflow:](docs/Screenshot%202025-04-19%20175150.png)

## Project Structure

```
.
├── backend/         # Mock Node.js backend server
│   ├── node_modules/
│   ├── package.json
│   ├── package-lock.json
│   └── server.js
├── docs/            # Original project description
│   └── README.md
├── frontend/        # React frontend application
│   ├── node_modules/
│   ├── public/
│   ├── src/
│   │   ├── assets/
│   │   ├── App.css
│   │   ├── App.tsx
│   │   ├── ContactList.tsx
│   │   ├── index.css
│   │   ├── main.tsx
│   │   └── vite-env.d.ts
│   ├── .gitignore
│   ├── eslint.config.js
│   ├── index.html
│   ├── package.json
│   ├── package-lock.json
│   ├── README.md
│   ├── tsconfig.app.json
│   ├── tsconfig.json
│   ├── tsconfig.node.json
│   └── vite.config.ts
├── .gitignore       # (Optional: Add a root gitignore if needed)
└── README.md        # This file
```

## Setup and Running

**Prerequisites:**

*   Node.js (v18 or later recommended)
*   npm (usually comes with Node.js)
*   A Polkadot JS compatible browser extension (e.g., SubWallet, Talisman) installed in your browser.
*   An account within the extension, preferably funded on the **Westend testnet** (for balance display).

**Steps:**

1.  **Clone the repository:**
    ```bash
    git clone <repository-url>
    cd <repository-directory>
    ```
2.  **Install Backend Dependencies:**
    ```bash
    cd backend
    npm install
    ```
3.  **Install Frontend Dependencies:**
    ```bash
    cd ../frontend
    npm install
    ```
4.  **Run Backend Server:**
    *   Open a terminal in the `backend` directory.
    *   Run: `node server.js`
    *   *(Expected output: `Mock backend server listening at http://localhost:3001`)*
5.  **Run Frontend Development Server:**
    *   Open a *separate* terminal in the `frontend` directory.
    *   Run: `npm run dev`
    *   Open the URL provided (usually `http://localhost:5173`) in your browser.

**Using the App:**

* 1.  Click "Connect Wallet" and approve the connection request in your browser extension.
* 2.  Select an account from the dropdown (balance from Westend should appear).
* 3.  Click "Start Listening", grant microphone permission if requested, and speak a command (e.g., "Pay 1 Dot to Bob").
* 4.  Click "Process Command (Mock)" to send the recognized text to the testnet backend.
* 5.  Observe the status updates.
* 6.  Use the "Disconnect Wallet" button to clear the app's connection state.

## Current Status & Future Work

This is a proof-of-concept demonstrating the basic UI flow and integration points.

**Next Steps / Potential Improvements:**

*   **Actual Transaction Signing:** Integrate `@polkadot/extension-dapp` signer to sign real transactions based on voice commands.
*   **Backend Logic:** Replace the mock backend with actual logic to parse commands and construct transactions.
*   **AI/ML Integration:** Integrate a real voice processing pipeline (like the envisioned Gemma model) to parse natural language commands into structured transaction data.
*   **Parachain/Smart Contract:** Connect to the actual EchoPay parachain runtime or smart contracts on Westend/Polkadot.
*   **Cross-Chain Functionality:** Implement XCM calls or bridge interactions based on parsed commands.
*   **Dynamic Contact List:** Fetch contacts from on-chain storage, user settings, or other sources.
*   **Error Handling:** More robust error handling and user feedback.
*   **UI/UX Refinements:** Improve the overall user experience.
*   

## Know issues
We cannot control your wallet extension directly or force it to log out or revoke permissions. This is a security feature to protect your wallet.
It is not a secure payment method to rely solely on voice; hence, the wallet will prompt for a password to enhance security — unless we use voice recognition, two-step verification, or multi-factor authentication (MFA).
The primary role of smart contracts will be to record the details of these payments after they occur (or are initiated).



