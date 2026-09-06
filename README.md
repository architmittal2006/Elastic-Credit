# Elastic-Credit
# Elastic Credit Engine for Irregular Earners

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Python 3.10+](https://img.shields.io/badge/python-3.10+-blue.svg)](https://www.python.org/downloads/)
[![RBI DLG Compliant](https://img.shields.io/badge/RBI-DLG%20Compliant-green.svg)](#regulatory--bureau-architecture)

An open-source financial engine that replaces rigid monthly EMIs with dynamic, percentage-of-earnings repayments. Designed for gig workers, daily-wage earners, and irregular income profiles in India.

---

## Key Features

* **Daily Survival Floor (₹300 Threshold):** 0% repayment deduction on daily earnings under ₹300 to protect operational cash (fuel, meals).
* **Surge Acceleration Rule:** 10% base sweep rate on earnings between ₹301–₹800; 12% marginal rate on earnings above ₹800 (capped at ₹180/day) to accelerate debt clearance during peak weeks.
* **AA Inflow Filtering:** Parses Account Aggregator (AA) data using corporate VPA regex whitelist to isolate gig payouts (`zomato.payout@icici`, `swiggy@axis`) from P2P transfers.
* **CIBIL Type 05 Overdraft Compliance:** Dynamic Minimum Amount Due (MAD) calculation prevents Days Past Due (DPD) penalties during extended zero-income periods.
* **Direct-to-Garage Emergency Override:** Unlocks a ₹1,000 emergency repair line at 30% loan clearance, payable exclusively to verified mechanic UPI QRs.

---

## Architectural Flow
