````markdown
# Secure Anonymous Auction System

This project implements a **privacy-preserving auction system** using advanced cryptographic techniques such as:

- Pedersen Commitments
- Linkable Ring Signatures (LSAG)
- Oblivious Transfer (Naor-Pinkas Tree OT)
- Zero-Knowledge Proofs
- Shamir Secret Sharing

The system ensures **bid privacy, bidder anonymity, and verifiable winner selection** without relying on a single trusted authority.

---

## 🚀 Demo Setup

### Run the Application

```bash
python backend/app.py
````

Open in browser:

```
http://127.0.0.1:5000
```

### Multi-User Testing

* Use **normal browser window** → Auctioneer
* Use **incognito/private window** → Bidder

---

## ✅ Pre-Demo Requirements

Ensure the following before running the demo:

* At least **2 auctioneer accounts** (required for Shamir threshold)
* At least **1–2 bidder accounts**
* At least **1 auction created**

### Important Design Note

All auctioneers are **globally available** to every auction.
There is **no separate "join auctioneer" step**.

---

## 🔄 Full Demo Flow

### 1. Auction Creation

* Auctioneer logs in
* Creates auction from **Auctions page**
* Auction starts in `REGISTRATION` state

---

### 2. Bidder Registration

* Bidder clicks **Participate (Generate Key Pair)**

Backend:

* Adds bidder to auction
* Generates auction-specific key pair
* Stores:

  * Public key → backend
  * Private key → browser session only

---

### 3. Start Auction

Auctioneer clicks **Start Auction**

Checks:

* Threshold auctioneers available
* At least one bidder joined
* Start time reached
* Status = `REGISTRATION`

➡ Status → `BIDDING_OPEN`

---

### 4. Submit Commitment

Bidder enters:

* Bid value `b`
* Randomness `r`

System:

* Creates Pedersen Commitment

  ```
  C = bG + rH
  ```
* Generates ring signature
* Stores:

  * Commitment
  * Signature
  * Key image

➡ Raw bid remains hidden

---

### 5. Close Bidding

Auctioneer clicks **Close Bidding**

Checks:

* Auction is open
* At least one bid exists

➡ Status → `BIDDING_CLOSED`

---

### 6. Prepare OT Stage

Auctioneer clicks **Prepare OT Stage**

System:

* Generates OT keys per bid value
* Builds sender state
* Encrypts OT data

➡ Status → `OT_READY`

---

### 7. Retrieve OT Key

Bidder clicks:

* **Retrieve OT Shared Key**

System:

* Allows bidder to retrieve key **only for committed bid**

---

### 8. Reveal Phase

Bidder submits:

* Bid `b`
* Randomness `r`
* OT key

System generates:

* Commitment opening proof
* OT key possession proof

---

### 9. Verification (Important Step)

Auctioneer clicks **Verify Revealed Bids**

Each bid is checked:

| Check              | Description         |
| ------------------ | ------------------- |
| `shared_key_valid` | OT key correctness  |
| `commitment_valid` | Commitment matches  |
| `zk_valid`         | ZK proof verified   |
| `key_proof_valid`  | OT possession proof |
| `final`            | All checks passed   |

➡ Visible in **Auctioneer Panel (Judge Evidence Screen)**

---

### 10. Declare Winner

Auctioneer clicks **Declare Winner**

System:

* Selects:

  * Highest bid → Forward auction
  * Lowest bid → Reverse auction
* Splits winner secret using Shamir Secret Sharing
* Verifies correctness

➡ Status → `COMPLETED`

Winner displayed on Auctioneer Panel

---

## 🔐 Security Explanation (For Judges)

```
The system ensures fairness and privacy using cryptographic guarantees:

1. Bids are hidden using Pedersen commitments.
2. Bidders remain anonymous via ring signatures.
3. During reveal, four checks ensure correctness:
   - OT key validity
   - Commitment consistency
   - Zero-knowledge proof
   - Key ownership proof
4. Only valid bids are considered.
5. Winner is computed securely.
6. Shamir Secret Sharing prevents single-point trust.
```

---

## 🧪 Manual Test Cases

### ✔ Happy Path

* Forward auction → highest bid wins
* Reverse auction → lowest bid wins

### ❌ Failure Cases

* Start without bidders → blocked
* Start before time → blocked
* Close without bids → blocked
* Bid without participation → blocked
* Duplicate bid → blocked
* Out-of-range bid → blocked
* Wrong OT retrieval → blocked
* Reveal mismatch → blocked
* Declare winner before verification → blocked

---

## 🎯 Strong Demo Sequence

```
Create Auction
→ Bidder Participate
→ Start Auction
→ Submit Commitment
→ Close Bidding
→ Prepare OT
→ Retrieve OT Key
→ Reveal
→ Verify Bids
→ Declare Winner
→ Show Proof Flags + Winner
```

---

## ⚠ UI Note

* Full proof evidence is visible **only in Auctioneer Panel**
* Recommended to present **Auctioneer screen to judges**

---

## 📁 Project Structure (Simplified)

```
backend/
 ├── app.py
 ├── commitment.py
 ├── ring_signature.py
 ├── oblivious_transfer.py
 ├── zk_proof.py
 ├── shamir.py

templates/
 ├── auctions.html
 ├── auctioneer_panel.html
 ├── bidder_panel.html
```

---

## 🧠 Key Technologies

* Flask (Backend)
* Cryptographic Protocols
* Browser Session Key Management
* Secure Multi-Party Logic

---

## 📌 Conclusion

This system demonstrates a **fully verifiable, anonymous, and trust-minimized auction mechanism** suitable for secure digital bidding environments.

```
```
