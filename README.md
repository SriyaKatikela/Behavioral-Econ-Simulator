# Behavioral-Econ-Simulator
### Repository URL: https://github.com/SriyaKatikela/Behavioral-Econ-Simulator
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

This simulator is a simplified, educational market model where **agents are people with different psychological traits**. Each agent makes buying/selling decisions based on emotions and biases like loss aversion, herd behavior, and overconfidence. The goal is to show how those individual decisions combine to create group-level outcomes like bubbles, crashes, or surprising stability.

---

## 🧪 Step-by-Step: How the Simulation Works (High-level)

1. **Create the world** — a virtual market and a set of agents (commonly 50–100).
2. **Give each agent a personality** — initial money, risk tolerance, and bias levels.
3. **Each simulated day**, agents look at each available stock and decide to **buy**, **sell**, or **hold**.
4. The market adjusts prices based on the total buying/selling pressure, with a bit of randomness to mimic real markets.
5. **Optional events** (news, rumors) can shock the market and change how agents feel.
6. After N days, the simulator produces results that show how behavior influenced the market and who won or lost.

---

## 🔎 How the Agent Works

Each agent represents an individual investor. Important things to know (without reading code):

- **Name & Portfolio**: Every agent has a name and a `Portfolio` that tracks their cash and the stocks they own.
- **Market view**: Agents know about the same list of stocks available in the market.
- **Planned actions**: For each stock, the agent decides whether to **BUY**, **SELL**, or **HOLD** and stores that plan temporarily.
- **Confidence score**: For each stock the agent calculates a number between 0 and 1 representing how confident they are that the stock is worth buying or holding. This score is based on:
  - Recent percent price change for the stock.
  - The agent’s personal traits: **risk tolerance**, **loss aversion**, **herd bias**, **overconfidence**, and **emotional noise**.
  - A simple market sentiment signal (a placeholder for crowd behavior).
  - Small random emotional noise so agents are not perfectly predictable.

**Decision rules (simple summary):**
- Confidence > 0.6 → plan **BUY**
- Confidence < 0.4 → plan **SELL**
- Otherwise → plan **HOLD**

**How much to buy or sell** is scaled by the agent’s confidence, risk tolerance, and the size of their holdings or cash.

**Behavioral intent:**
- **Loss aversion** makes agents react strongly to losses.
- **Overconfidence** makes them buy more aggressively on gains.
- **Herd bias** nudges their confidence toward general market sentiment.
- **Emotional noise** makes actions sometimes surprising.

---

## 💼 The Portfolio

The **Portfolio** is the part of the simulation that keeps track of what an agent actually owns and how much cash they have. Think of it like a wallet + list of investments for each person.

### What a Portfolio holds
- **Cash balance** — how much money the agent can spend right now.
- **Holdings** — a simple list (or map) of which stocks the agent owns and how many shares of each (for example, AAPL: 5 shares).

### What a Portfolio can do
- **Buy stocks**:
  - The portfolio checks whether the agent has enough cash to buy the requested number of shares.
  - If there is enough cash, it subtracts the cost and adds those shares to the holdings.
  - If not enough cash, it refuses the purchase and prints a message like "Insufficient funds."
- **Sell stocks**:
  - The portfolio checks whether the agent actually owns the number of shares they want to sell.
  - If they do, it adds the sale proceeds to cash and decreases or removes that holding.
  - If they don’t own enough, it refuses the sale and prints a message like "You don't own that number of shares."
- **Get total value**:
  - The portfolio can report the **current total value**: cash + value of all owned stocks (based on the latest prices).
  - Values are rounded to two decimal places for clarity (so dollar amounts look normal).
- **Get portfolio-only value**:
  - It can also report how much value is invested in stocks (total value minus cash).
- **Show a summary**:
  - The portfolio can print a simple summary listing each owned stock, number of different stocks, value of shares, total account value, and cash balance.

### Why Portfolio matters for the simulation
- It **enforces real-world limits**: agents can’t buy what they can’t afford and can’t sell what they don’t own. This keeps the simulation realistic.
- It records **wealth changes** over time, which lets you measure outcomes like who made money, who lost money, and how inequality develops.
- Because the portfolio calculates total value using the **current stock prices**, it links individual decisions to market outcomes — an essential loop for studying behavioral effects.

---

## 🔗 How Portfolio and Agent Interact
1. The **Agent** decides to buy or sell a particular stock.
2. The **Agent** asks their **Portfolio** to execute that buy or sell.
3. The **Portfolio** checks cash or holdings and either completes the trade or refuses it.
4. If the trade completes, the Portfolio updates the agent’s cash and holdings.
5. Later, portfolio values are used to see who profited and how behavior affected wealth.

---

## **Daily Simulation Loop**
For **N simulated days**, the following steps repeat:

#### a. **Update Stock Prices**
- For each stock:
  - Apply a random price change based on volatility.
  - Store this new price in history.

#### b. **Agents Analyze the Market**
- Each agent reviews each stock.
- They calculate a **confidence score** using:
  - Recent price change.
  - Loss aversion and overconfidence adjustments.
  - Risk tolerance.
  - Market sentiment (currently randomized).
  - Random emotional noise.

#### c. **Agents Decide What to Do**
- **Confidence > 0.6** → Buy.
- **Confidence < 0.4** → Sell.
- Otherwise → Hold.

#### d. **Agents Act**
- **Buy**:
  - Determine how many shares they can afford and are willing to risk.
  - Deduct cash, add shares.
- **Sell**:
  - Sell some or all shares depending on confidence and loss aversion.
  - Add cash from sale.
- **Hold**:
  - Do nothing for that stock today.

#### e. **Record Results**
- Update portfolios with new values.
- Calculate total portfolio value = cash + stock value.
- Optionally print:
  - Market prices.
  - Agents’ daily gains/losses.
  - Trading decisions.

---

### **End of Simulation**
- Print final results:
  - Rankings of agents by total portfolio value.
  - Market trends over the period.
  - Observations on how different biases affected outcomes.

---

## Example Stock Data:
### Format: Stock Ticker, Price, Volatility
- AAPL,197.82,0.012
- TSLA,244.14,0.035
- AMZN,132.54,0.018
- NFLX,449.78,0.026
- JPM,155.91,0.011
- GOOGL,131.67,0.015
- MSFT,417.89,0.013
- NVDA,121.22,0.034
- META,316.11,0.020
- BA,176.41,0.022

---

## ⚙️ Practical Notes & Suggestions (non-technical)
- Right now, buy/sell requests are all-or-nothing based on affordability and holdings. To increase realism, you could later:
  - Add transaction fees or taxes.
  - Add partial fills (only some of an order gets executed).
  - Add a minimum trade size or rounding rules to mimic real brokerages.
- The portfolio rounds monetary values to two decimal places so results look like real dollar amounts.
- Replace `getFakeMarketSentiment` with a real market-wide indicator (average of recent returns, volume-weighted sentiment, or a social-network influence model).
- Use actual stock price histories (uncomment/implement `priceHistory`) to compute moving averages and momentum signals.
- Add **transaction costs**, **slippage**, and **partial fills** for more realism.
- Consider making `emotionalNoiseLevel` evolve over time (stress accumulates after losses).
- Let `overConfidenceBias` or `lossAversionFactor` slowly **adapt** based on past performance (learning/desensitization).

---

## How to Run
1. Prepare two files:
   - **stocks.txt**: Stock ticker, price, volatility.
   - **agents.txt**: List of names (one per line).
2. Set simulation parameters (number of agents, number of days, starting cash).
3. Run the program.
4. Observe market and portfolio changes over time.



## Output
At the end, you’ll see:
- Each agent’s final portfolio value.
- Stocks that gained or lost the most.
- How behavioral biases influenced the market.

### Example Output:

'=== Market Status: Day 1 of 3 ===

STOCK     | PRICE     | Δ%     | VOL%
----------------------------------------
AAPL      | $197.08   | 0.0  | 0.01


TSLA      | $237.59   | -3.0  | 0.04


AMZN      | $133.88   | +1.0  | 0.02


NFLX      | $453.38   | +1.0  | 0.03


JPM      | $154.76   | -1.0  | 0.01


GOOGL      | $131.12   | 0.0  | 0.02


MSFT      | $422.81   | +1.0  | 0.01


NVDA      | $121.09   | 0.0  | 0.03


META      | $313.7   | -1.0  | 0.02


BA      | $176.19   | 0.0  | 0.02


Top Gains: AMZN (+0.01%), NFLX (+0.01%), MSFT (+0.01%)
Top Losses: TSLA (-0.03%), META (-0.01%), JPM (-0.01%)

AGENT DECISIONS AND SUMMARIES:

Bob:
   - Cash: $12585.52  | Portfolio: $20720.29
   - NVDA: BUY (Confidence: 0.87 | Positive trend detected)
   - BA: HOLD (Confidence: 0.60 | Market uncertrain, holding position)
   - AMZN: SELL (Confidence: -0.03 | Negative sentiment / Loss aversion triggered)
   - NFLX: BUY (Confidence: 0.70 | Positive trend detected)
   - META: HOLD (Confidence: 0.56 | Market uncertrain, holding position)
   - TSLA: BUY (Confidence: 1.06 | Positive trend detected)
   - MSFT: BUY (Confidence: 0.71 | Positive trend detected)
   - GOOGL: SELL (Confidence: 0.25 | Negative sentiment / Loss aversion triggered)
   - AAPL: SELL (Confidence: 0.09 | Negative sentiment / Loss aversion triggered)
   - JPM: SELL (Confidence: 0.10 | Negative sentiment / Loss aversion triggered)

---

## 📚 Educational Use Cases

This simulator is perfect for:

- **Students** learning behavioral economics
- **Teachers** explaining how markets work in real life
- **Coders** experimenting with agent-based models
- **Researchers** exploring economic psychology
- **Writers/creators** crafting realistic economic dynamics

---

## 📄 License
MIT License — open-source and available for educational or non-commercial use.

---
README generated with the assistance of AI (ChatGPT) to enhance clarity and structure.
