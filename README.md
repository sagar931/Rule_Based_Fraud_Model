# 🛡️ Credit Card Fraud Detection: Rule-Based Engine

> A transparent, interpretable fraud detection system using expert-driven business rules to score transaction risk in real-time.

[![Python](https://img.shields.io/badge/Python-3.8+-blue.svg)](https://www.python.org/downloads/)
[![Pandas](https://img.shields.io/badge/Pandas-Latest-green.svg)](https://pandas.pydata.org/)
[![License](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

---

## 📋 Table of Contents
- [Overview](#-overview)
- [Why Rule-Based?](#-why-rule-based)
- [Architecture](#-architecture)
- [Rule Categories](#-rule-categories)
- [Dataset Features](#-dataset-features)
- [Installation](#-installation)
- [Usage](#-usage)
- [Risk Scoring Logic](#-risk-scoring-logic)
- [Performance Metrics](#-performance-metrics)
- [Future Enhancements](#-future-enhancements)
- [Contributing](#-contributing)

---

## 🎯 Overview

This project implements a **rule-based fraud detection engine** designed for credit card transactions. Unlike black-box ML models, this system uses transparent, explainable rules that can be audited, debugged, and easily updated by business analysts.

### Key Highlights
- ✅ **100% Explainable** - Every decision can be traced back to specific rules
- ✅ **Real-time Ready** - Low latency scoring suitable for production
- ✅ **No Training Required** - Deploy immediately without historical data
- ✅ **Domain Expert Friendly** - Rules mirror actual fraud analyst logic
- ✅ **Regulatory Compliant** - Full audit trail for compliance requirements

---

## 🤔 Why Rule-Based?

While machine learning models are powerful, rule-based systems offer unique advantages:

<img width="558" height="254" alt="Screenshot 2025-10-01 at 9 21 41 PM" src="https://github.com/user-attachments/assets/4926c86c-729f-4a41-87ac-d8d26ca3ac93" />


**Best Use Case**: This system works excellently as:
- A first-line defense in fraud prevention
- A fallback when ML models fail
- A baseline for ML model comparison
- A complementary scoring system alongside ML

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    Transaction Input                        │
│  (Customer, Amount, Merchant, Device, Location, Time, etc.) │
└────────────────────────┬────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────────┐
│                    Rule Engine Core                         │
├─────────────────────────────────────────────────────────────┤
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐       │
│  │   Amount     │  │  Behavioral  │  │  Historical  │       │
│  │    Rules     │  │    Pattern   │  │     Risk     │       │
│  │  (15-25 pts) │  │    Rules     │  │    Rules     │       │
│  └──────────────┘  │  (20-35 pts) │  │  (30-50 pts) │       │
│                    └──────────────┘  └──────────────┘       │
│  ┌──────────────┐  ┌──────────────┐                         │
│  │  Merchant &  │  │  Technical   │                         │
│  │   Location   │  │   Anomaly    │                         │
│  │    Rules     │  │    Rules     │                         │
│  │  (10-30 pts) │  │  (15-25 pts) │                         │
│  └──────────────┘  └──────────────┘                         │
└────────────────────────┬────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────────┐
│              Risk Score Aggregation (0-1000)                │
│  • Low Risk: 0-300  • Medium: 301-600  • High: 601-1000     │
└────────────────────────┬────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────────┐
│                   Decision & Action                         │
│  ✓ Approve  |  ⚠ Manual Review  |  ✗ Decline                │
└─────────────────────────────────────────────────────────────┘
```

---

## 📊 Rule Categories

### 1️⃣ Amount-Based Rules (15-25 points)
Rules that flag unusual transaction amounts:

| Rule | Description | Points | Rationale |
|------|-------------|--------|-----------|
| **High Amount Ratio** | Amount > 95th percentile of customer history | 25 | Sudden large purchases are suspicious |
| **Transaction-to-Average** | Amount > 5x average spend | 15-25 | Deviations from normal behavior |
| **Round Numbers** | Amounts ending in 000 or 00 | 10-15 | Fraudsters often use round numbers |
| **Below Threshold** | Just under reporting limits (₹49,500) | 15 | Structuring to avoid detection |

### 2️⃣ Behavioral Pattern Rules (20-35 points)
Rules that detect anomalous user behavior:

| Rule | Description | Points | Rationale |
|------|-------------|--------|-----------|
| **New Device** | First transaction on device | 15 | Account takeover indicator |
| **Device Change** | Device switch mid-session | 25 | Strong takeover signal |
| **Unusual Hour** | Transaction outside 6AM-10PM | 20 | Compromised card usage pattern |
| **High Velocity** | >3 txns in 1 hour | 30 | Automated fraud attempts |
| **Geographic Distance** | >500km from home | 25 | Card testing or theft |

### 3️⃣ Historical Risk Rules (30-50 points)
Rules based on customer and device history:

| Rule | Description | Points | Rationale |
|------|-------------|--------|-----------|
| **Prior Fraud** | Customer has fraud history | 40 | Repeat offender risk |
| **New Account** | Account age < 30 days | 20 | Higher risk period |
| **Blacklisted Device** | Device on blacklist | 50 | Known fraud source |
| **Recent Unblock** | Card unblocked <30 days ago | 35 | Elevated monitoring period |

### 4️⃣ Merchant & Location Rules (10-30 points)
Rules targeting risky merchant categories and locations:

| Rule | Description | Points | Rationale |
|------|-------------|--------|-----------|
| **Foreign Country** | Transaction outside India | 25 | Cross-border fraud common |
| **High-Risk Category** | Cash advance, gambling, etc. | 20 | Fraud-prone categories |
| **New Merchant** | First time with merchant | 15 | Testing stolen cards |
| **Cash Advance** | ATM or cash transaction | 30 | High-value fraud target |

### 5️⃣ Technical Anomaly Rules (15-25 points)
Rules detecting technical red flags:

| Rule | Description | Points | Rationale |
|------|-------------|--------|-----------|
| **No OTP** | High-value txn without 2FA | 20 | Security bypass attempt |
| **Manual Entry** | Card-not-present fraud | 15 | Higher fraud rate |
| **High Velocity** | Rapid successive transactions | 25 | Automated fraud bot |

---

## 📊 Dataset Features

The model analyzes **47 features** across multiple dimensions:

### Customer Demographics
- Age, Gender, Marital Status
- Employment Status, Income Bracket
- Account Tenure (months), Credit Limit

### Transaction Details
- Amount, Currency, Time, Day of Week
- Merchant Name, Category Code, Country
- Transaction Channel (POS, E-com, ATM)
- Entry Mode (Chip, Swipe, Manual)

### Behavioral Signals
- Transactions in last 1hr / 24hr
- Amount spent in last 24hr
- Time gap between transactions
- Transaction-to-average ratio
- Distance from home location

### Device & Security
- Device ID, IP Address, Device Type
- Device Change Flag
- OTP Usage Status
- Device Blacklist Status

### Geolocation
- Latitude, Longitude
- Transaction City
- Distance from Home (km)

### Historical Context
- Prior Fraud Count
- Rule Hit Flags & Codes
- Manual Review History

---

## 🚀 Installation

### Prerequisites
```bash
Python 3.8+
pandas
numpy
```

### Setup
```bash
# Clone the repository
git clone https://github.com/sagar931/Rule_Based_Fraud_Model.git
cd Rule_Based_Fraud_Model

# Install dependencies
pip install -r requirements.txt

# Run the notebook
jupyter notebook Rule_Model.ipynb
```

---

## 💻 Usage

### Basic Usage
```python
import pandas as pd
import numpy as np

# Load your transaction data
df = pd.read_csv("test_data.csv")

# Apply rule-based scoring
# (Refer to Rule_Model.ipynb for complete implementation)

# View risk scores
print(df[['transaction_id', 'Total_Risk_Score', 'fraud_flag']].head())
```

### Quick Start Example
```python
# Example: Score a single transaction
transaction = {
    'transaction_amount': 75000,
    'avg_monthly_spend': 15000,
    'txn_amount_to_avg_ratio': 5.0,
    'transaction_hour': 2,
    'distance_from_home_km': 600,
    'otp_used': 'No',
    'prior_fraud_count': 1
}

# Apply rules
risk_score = calculate_risk_score(transaction)
print(f"Risk Score: {risk_score}/1000")
```

---

## 🎲 Risk Scoring Logic

### Score Calculation
```python
Total_Risk_Score = sum([
    risk_high_amount_ratio,      # 0-25 points
    risk_txn_to_avg_ratio,       # 0-25 points
    risk_round_number,           # 0-15 points
    risk_below_threshold,        # 0-15 points
    risk_new_device_first_txn,   # 0-15 points
    risk_device_change_session,  # 0-25 points
    risk_unusual_hour,           # 0-20 points
    risk_multi_txns_1h,          # 0-30 points
    risk_far_from_home,          # 0-25 points
    risk_velocity_rule           # 0-25 points
    # ... additional rules
])
```

### Risk Tiers
| Score Range | Risk Level | Action |
|-------------|-----------|--------|
| 0-300 | 🟢 Low Risk | Auto-approve |
| 301-600 | 🟡 Medium Risk | Enhanced monitoring |
| 601-800 | 🟠 High Risk | Manual review |
| 801-1000 | 🔴 Critical Risk | Block/decline |

### Decision Threshold Tuning
Adjust thresholds based on your risk appetite:
```python
# Conservative (Minimize fraud)
DECLINE_THRESHOLD = 600

# Balanced (Equal false positives/negatives)
DECLINE_THRESHOLD = 750

# Aggressive (Minimize friction)
DECLINE_THRESHOLD = 850
```

---

## 📈 Performance Metrics

Based on the test dataset (99,888 transactions):

### Distribution Statistics
```
Mean Risk Score: 85.47
Std Deviation: 22.67
Min Score: 15
Max Score: 160
```

### Sample Results
- **Fraud Detection Rate**: Rule effectiveness varies by category
- **False Positive Management**: Tune thresholds based on business needs
- **Explainability**: Every score is fully traceable

### Key Insights
- Rules triggered most frequently: New device (15 pts), Unusual hour (20 pts)
- High-risk transactions: Multi-rule triggers indicate compound fraud signals
- Geographic rules: Distance >500km shows strong fraud correlation

---

## 🔮 Future Enhancements

### Planned Improvements
- [ ] **Dynamic Rule Weights**: Adjust weights based on fraud trends
- [ ] **Time-Decay Rules**: Recent patterns weighted higher
- [ ] **Merchant Risk Scoring**: Build merchant-specific risk profiles
- [ ] **Ensemble Approach**: Combine with ML model scores
- [ ] **Real-time Adaptation**: Update rules based on fraud feedback
- [ ] **Network Analysis**: Detect fraud rings across customers
- [ ] **Velocity Patterns**: More sophisticated sequence detection

### Integration Roadmap
- REST API for real-time scoring
- Dashboard for rule performance monitoring
- A/B testing framework for rule optimization
- Feedback loop for continuous improvement

---

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

### Areas for Contribution
- New rule ideas based on fraud patterns
- Performance optimization
- Documentation improvements
- Test coverage expansion
- Real-world case studies

### Guidelines
1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingRule`)
3. Commit your changes (`git commit -m 'Add some AmazingRule'`)
4. Push to the branch (`git push origin feature/AmazingRule`)
5. Open a Pull Request

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## 🙏 Acknowledgments

- Inspired by real-world fraud detection systems used by financial institutions
- Dataset structure based on industry-standard transaction formats
- Rule logic derived from fraud analyst best practices

---

## 📧 Contact

For questions or collaboration opportunities:
- GitHub: [@sagarmandal931](https://github.com/sagar931)
- LinkedIn: [Sagar Mandal](https://www.linkedin.com/in/sagar-mandal-526698196/)
- Email: sagarmandal931@gmail.com

---

<div align="center">

**⭐ If you find this project useful, please consider giving it a star! ⭐**

Made with ❤️ for the fraud prevention community

</div>
