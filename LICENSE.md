---

### `src/engine.py`

```python
"""
Core Deduction Logic for the Elastic Credit Engine.
Implements the ₹300 survival floor, progressive surge acceleration, and balance updates.
"""

from dataclasses import dataclass
from typing import Dict, Union

@dataclass
class EngineConfig:
    survival_floor: float = 300.0      # Income threshold below which deduction is 0
    base_tier_cap: float = 800.0       # Upper bound for the 10% base deduction tier
    base_rate: float = 0.10            # 10% deduction rate between 301 and 800
    surge_rate: float = 0.12           # 12% marginal rate above 800
    max_daily_cap: float = 180.0       # Hard ceiling on single-day deduction


class ElasticCreditEngine:
    def __init__(self, principal: float, processing_fee: float, config: EngineConfig = EngineConfig()):
        self.total_repayable = principal + processing_fee
        self.remaining_balance = self.total_repayable
        self.config = config
        self.cumulative_sweeps = 0.0

    def calculate_deduction(self, daily_income: float) -> float:
        """Calculates daily sweep amount based on survival floor and tiered rates."""
        if daily_income <= self.config.survival_floor:
            return 0.0

        # Tier 1: Income between ₹300.01 and ₹800.00
        tier1_income = min(daily_income, self.config.base_tier_cap) - self.config.survival_floor
        deduction = tier1_income * self.config.base_rate

        # Tier 2: Income above ₹800.00
        if daily_income > self.config.base_tier_cap:
            tier2_income = daily_income - self.config.base_tier_cap
            deduction += tier2_income * self.config.surge_rate

        # Apply hard daily cap and balance cap
        deduction = min(deduction, self.config.max_daily_cap)
        deduction = min(deduction, self.remaining_balance)

        return round(deduction, 2)

    def process_daily_inflow(self, daily_income: float) -> Dict[str, Union[float, bool]]:
        """Processes a daily income entry and updates the active loan balance."""
        deduction = self.calculate_deduction(daily_income)
        self.remaining_balance -= deduction
        self.cumulative_sweeps += deduction

        return {
            "daily_income": daily_income,
            "deduction": deduction,
            "net_worker_payout": daily_income - deduction,
            "remaining_balance": max(0.0, round(self.remaining_balance, 2)),
            "is_cleared": self.remaining_balance <= 0
        }
