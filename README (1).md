 # Aave V2 Wallet Credit Scoring System

This project assigns a **credit score (0–1000)** to wallets that interact with the Aave V2 DeFi protocol, based solely on their historical transaction behavior. The objective is to evaluate wallets for their reliability and risk based on actions like deposit, borrow, repay, redeem, and liquidation.

---

## 📌 Problem Statement

Given 100,000+ raw transaction logs from the Aave V2 protocol, develop a machine learning model or heuristic logic that assigns a credit score to each wallet. Higher scores indicate responsible and consistent usage patterns, while lower scores flag risky, inactive, or exploitative users.

Each transaction record includes:
- `user`: Wallet address
- `action`: One of `deposit`, `borrow`, `repay`, `redeemUnderlying`, `liquidationCall`
- `amount`: Amount involved
- `asset`: Token used
- `timestamp`: UNIX timestamp of the transaction

---

## 📦 Input

**Input File:**  
`user_transactions.json` — a JSON array of transaction records.

**Example:**
```json
[
  {
    "user": "0xabc123...",
    "action": "deposit",
    "amount": 1000,
    "asset": "USDC",
    "timestamp": 1638394012
  },
  ...
]
✅ Feature Engineering
From the raw transaction logs, we extract wallet-level behavioral features, including:

Action Counts: num_deposit, num_borrow, num_repay, num_liquidationcall, num_redeemunderlying

Ratios:

repay_ratio = repay / (borrow + ε)

redeem_ratio = redeemUnderlying / (deposit + ε)

liquidation_rate = liquidationCall / total_actions

Temporal Features:

active_days: Number of unique days the wallet was active

days_since_last_txn: Recency of last transaction

These features are used to score wallets on reliability and risk.

🧠 Scoring Logic
We normalize the features using MinMaxScaler, then assign weights based on their relevance:

Feature	Weight
Repay Ratio	35%
Redeem Ratio	15%
Activity Duration	15%
Recency	15%
Liquidation Rate	20% (penalized)

Final score is computed as:

python
Copy
Edit
score = (
    repay_score * 0.35 +
    redeem_score * 0.15 +
    activity_score * 0.15 +
    recency_score * 0.15 +
    (1 - liquidation_score) * 0.20
) * 1000
Scores are clipped to 0–1000 and rounded.

🚀 How to Run
Install dependencies:
bash
Copy
Edit
pip install pandas numpy matplotlib seaborn scikit-learn
Run the scoring script:
bash
Copy
Edit
python score_wallets.py --input user_transactions.json --output wallet_scores.csv
📁 Output
wallet_scores.csv:

csv
Copy
Edit
userWallet,score
0xabc123...,814
0xdef456...,123
...
 