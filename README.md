# Behavioral-Econ-Simulator

## 🌍 Overview

The Behavioral Economics Simulator models the decisions of agents (consumers/investors) influenced by psychological biases in a dynamic, simplified market environment. It challenges traditional rational-choice theory by integrating behavioral economic principles into the logic of decision-making.

The simulator is a learning tool designed to show how irrational choices can cause unexpected and realistic effects like bubbles, panics, and stagnation.

---

## 🧠 Why Behavioral Economics?

Traditional economics assumes humans are **rational** and make perfect decisions that maximize utility with perfect logic and information. However, in reality, human decisions are bounded by emotion, cognitive shortcuts (heuristics), and social influence.

Behavioral economics studies those deviations. This simulator demonstrates key ideas:

- Bounded Rationality = limited cognitive processing leads to imperfect choices.
- Heuristics & Biases = people rely on mental shortcuts like anchoring or loss aversion.
- Loss Aversion = people prefer avoiding losses over acquiring equivalent gains. Investors sell winning stocks early and hold onto losers too long.
- Herd Behavior = Individuals mimic the actions of a larger group. Stock market bubbles and crashes often result from this.
- Overconfidence Bias = People overestimate their knowledge or ability to predict. Leads to excessive trading and poor risk assessment.
- Status Quo Bias = Tendency to prefer things to stay the same. People avoid changing portfolios or trying new financial products.

## 🌍 Conceptual Overview (Non-Code Explanation)

This simulator is a simplified, educational model of a market where **the agents aren't perfectly rational**. Each one makes decisions based on real behavioral biases like loss aversion, herd mentality, and overconfidence.

---

## 🧪 Step-by-Step: How the Simulation Works

### 1. The World Is Created
- A **virtual market** is initialized.
- Between **50 and 100 agents** are generated (configurable).
- Each agent acts like an individual economic participant (consumer/investor).
- Agents can **buy, sell, or hold investments**.

### 2. Each Agent Is Unique
Every agent has:
- Starting **wealth** (cash) inside a `Portfolio`.
- A local copy/reference of the **market** (list of `Stock` objects).
- A set of **behavioral traits** (risk tolerance, loss aversion, herd bias, overconfidence, emotional noise).
- Per-stock **confidence scores** and **planned actions** (BUY / SELL / HOLD).

### 3. The Market Runs Day-by-Day
- On each simulated day, every agent processes each stock and decides whether to buy, sell, or hold based on:
  - Recent price performance
  - Their behavioral traits and noise
  - A simple “market sentiment” input (placeholder in current code)
- The aggregate of agent actions moves the market price (market update logic lives in `Market`).

### 5. Track & Analyze
- The simulator logs agent actions, market price changes, and wealth distribution for post-run analysis.

---

## 🧩 In-Depth: The `Agent` Component

Below is a **non-code explanation** of exactly how the `Agent` class in the code works, how it stores data, and how it decides actions. This is suitable for anyone reading the repository who wants to understand agent behavior without reading Java line-by-line.

### Core data each Agent holds
- **`name`**: a string identifier for the agent.
- **`portfolio`**: the agent’s `Portfolio` object (cash balance + holdings).
- **`market`**: reference to the `ArrayList<Stock>` that represents the market the agent trades in.
- **`plannedActions`**: a map (`Map<Stock, String>`) storing the agent’s planned action for each stock for the current decision cycle. Values are `"BUY"`, `"SELL"`, or `"HOLD"`.
- **`confidence`**: a map (`Map<Stock, Double>`) storing the agent’s computed “confidence” value for each stock (0.0 to 1.0). This is the numeric foundation for decisions.
  - Note: In your code you also have commented-out `priceHistory` support — this suggests future history-based logic is planned.
- **Behavioral trait fields** (initialized randomly in constructor):
  - `riskTolerance` — range 0.0 (very conservative) to 1.0 (very bold). It affects whether an agent will act on small trends and scales buy quantities.
  - `lossAversionFactor` — initialized roughly in the range **1.2 to 2.0**. When percent change is negative, this factor increases the effective weight of losses in confidence calculations and influences sell quantities.
  - `herdBehaviorBias` — roughly **0 to 0.5**. Scales the impact of market sentiment on confidence.
  - `overConfidenceBias` — roughly **0 to 0.5**. Increases confidence on positive moves and scales buy quantities.
  - `emotionalNoiseLevel` — roughly **0.5 to 0.6** (note: code sets `0.5 + 0.1 * Math.random()` which yields 0.5–0.6). This controls the magnitude of random emotional noise added to confidence and influences sell quantities.

### Decision flow (high level)
1. **For each stock**, the agent calls `makeDecision(stock)`:
   - Compute `confidence` for that stock using `calculateBehavioralConfidence(stock)`.
   - Based on the resulting confidence number:
     - If confidence > 0.6 → plan `"BUY"`.
     - If confidence < 0.4 → plan `"SELL"`.
     - Otherwise → plan `"HOLD"`.
2. After planning actions for all stocks, the agent calls `decideAndAct(...)`:
   - Loops through the agent’s `market` list (the actual stocks available).
   - For each stock:
     - Reads the planned action and the `confidence` value.
     - **If BUY**:
       - Computes the maximum number of shares they could afford (`affordable = cash / currentPrice`).
       - Sets buy quantity as: `affordable * confidence * riskTolerance * (1 + overConfidenceBias)`, cast to integer.
       - If confidence is extremely high (> 0.85) but quantity evaluated to zero and they can afford at least one, the agent buys **1 share** as a floor.
       - Calls `portfolio.buyStock(stock, qty)` when qty > 0.
     - **If SELL**:
       - Reads how many shares the agent currently owns of that stock.
       - Sets sell quantity as: `own * (1 - confidence) * lossAversionFactor * (1 + emotionalNoiseLevel)`, cast to integer.
       - If confidence is very low (< 0.2), they sell **all** holdings of that stock.
       - Quantities are capped to what the agent actually owns.
       - Calls `portfolio.sellStock(stock, qty)` when qty > 0.
     - **If HOLD**: do nothing.

### How `confidence` is computed (behavioral logic)
The `calculateBehavioralConfidence(Stock s)` method transforms recent performance into a 0–1 confidence value using several behavioral steps:

1. **Start from percent price change**:
   - `percentChange = s.getPercentChange()` — assumes `Stock` exposes percent movement over some interval.
   - `baseConfidence` initial formula: `percentChange * 10 + 0.5`, then clamped to `[0,1]`. This maps small percent changes to a base 0–1 estimate.

2. **Loss aversion & overconfidence adjustments**:
   - If the stock moved **down** (`percentChange < 0`): multiply `baseConfidence` by `lossAversionFactor`. Because `lossAversionFactor` > 1, **negative moves lower confidence more strongly** (or if baseConfidence is small, multiplying may push it toward 0).
   - If the stock moved **up**: multiply `baseConfidence` by `(1 + overConfidenceBias)` — so positive moves get an extra boost when the agent is overconfident.

3. **Risk tolerance cutoff for small changes**:
   - If the absolute price change is smaller than a threshold `(0.01 + (1 - riskTolerance) * 0.05)`, the agent treats the signal as too weak.
   - In that case the method returns `0.5 + randomNoise()` — i.e., neutral confidence biased slightly by emotional noise. This models conservative agents ignoring small trends.

4. **Herd influence**:
   - `getFakeMarketSentiment(s)` returns a placeholder random sentiment in `[-1, 1]`.
   - `baseConfidence` is incremented by `herdSentiment * herdBehaviorBias`. This models agents partially following the crowd.

5. **Emotional noise**:
   - `randomNoise()` returns a value in `[-emotionalNoiseLevel, +emotionalNoiseLevel]`.
   - This noise is added to `baseConfidence` to represent day-to-day emotional fluctuation.

6. **Clamp** final confidence into `[0,1]` and store in the `confidence` map.

### Key thresholds & numbers (what to expect)
- **Buy threshold**: confidence > 0.6
- **Sell threshold**: confidence < 0.4
- **Neutral**: confidence between 0.4 and 0.6 → HOLD
- **Forced buy floor**: conf > 0.85 → will buy at least 1 share if affordable
- **Forced sell**: conf < 0.2 → sells entire holding of that stock

### Behavioral interpretation
- **Loss aversion** is modeled by multiplying confidence on negative returns: agents react disproportionately to losses (they will lower confidence and thus sell more aggressively).
- **Overconfidence** boosts the agent’s willingness to buy when price moves upward and scales the amount bought.
- **Herd behavior** nudges confidence toward whatever the market "sentiment" shows; in your current code that sentiment is randomized (placeholder), but this is where market-level signals could be plugged in.
- **Risk tolerance** controls whether the agent pays attention to small trends and scales buy quantities.
- **Emotional noise** makes agents occasionally irrational — sometimes making them buy/sell more or less than their rational baseline.

---

## 🛠 How `Agent` affects the simulator
- **Agents analyze stocks** and produce buy/sell pressure that the `Market` class uses to update prices.
- **Portfolios** track cash and holdings and enforce real constraints (an agent can only buy what they can afford and only sell what they own).
- Because traits are initialized randomly, the population contains **heterogeneous actors** — a primary mechanism for producing emergent, non-rational market outcomes (bubbles, panic selling, etc).

---

## 📈 Suggested next steps (code improvements & realism)
- Replace `getFakeMarketSentiment` with a real market-wide indicator (average of recent returns, volume-weighted sentiment, or a social-network influence model).
- Use actual stock price histories (uncomment/implement `priceHistory`) to compute moving averages and momentum signals.
- Add **transaction costs**, **slippage**, and **partial fills** for more realism.
- Consider making `emotionalNoiseLevel` evolve over time (stress accumulates after losses).
- Let `overConfidenceBias` or `lossAversionFactor` slowly **adapt** based on past performance (learning/desensitization).
