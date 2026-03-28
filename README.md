# Crypto Volatility and Sentiment-Enhanced Trading Framework

## Overview

This project develops a **GARCH-MIDAS volatility forecasting framework enhanced with macroeconomic and sentiment information**, and integrates the volatility forecasts into a systematic multi-asset crypto trading strategy.

The primary objective is to examine:

- Whether incorporating macro and sentiment variables improves volatility prediction  
- Whether improved volatility forecasts translate into better risk-adjusted trading performance  

The framework consists of four major components:

1. Data construction  
2. Sentiment modeling  
3. GARCH-MIDAS estimation  
4. Strategy backtesting with event-study validation  

---

## 1. Data Collection and Preprocessing

### Market Data

Daily market data are downloaded using `yfinance`, including:

- BTC  
- ETH  
- XRP  
- USDT  
- BNB  
- DOGE  
- S&P 500  
- CMC200 crypto index  

Daily log returns are computed for all assets.

Open prices are stored separately to allow realistic execution modeling in backtesting.

### Macroeconomic Data

Monthly macro variables are retrieved from FRED:

- M2  
- Federal Funds Rate  
- CPI  

All macro variables are shifted to avoid look-ahead bias.

---

## 2. Sentiment Construction

Two types of sentiment are constructed.

### Monetary Policy Sentiment

- Derived from FOMC statements  
- Classified using FinBERT  
- Tone score = P(Positive) − P(Negative)  
- Standardized using an expanding z-score  
- Aggregated to monthly frequency  

### Crypto-Specific Sentiment

- Fine-tuned FinBERT model on crypto news  
- Sentiment scores weighted by article importance  
- Aggregated daily  
- Resampled to monthly averages  

The final sentiment dataset includes:

- Crypto sentiment factor  
- Monetary policy sentiment factor  

These are aligned with macro variables for MIDAS modeling.

---

## 3. GARCH-MIDAS Volatility Model

The volatility model follows a multiplicative structure:

\[
\sigma_t^2 = \tau_t \times g_t
\]

Where:

- \( \tau_t \) = Long-run component (MIDAS)  
- \( g_t \) = Short-run GARCH(1,1) component  

### Long-Run Component (MIDAS)

- Exponential Almon lag weights  
- Six-month lag window  
- Incorporates macro and sentiment variables  
- Parameters estimated via:
  - Simulated annealing  
  - L-BFGS-B refinement  

Three specifications are considered:

1. Baseline model (M2 only)  
2. Sentiment-only model  
3. Combined macro + sentiment model  

Feature weights are estimated using:

- Ridge  
- Lasso  
- Random Forest  
- XGBoost  

### Short-Run Component (GARCH)

- Standard GARCH(1,1) dynamics  
- Captures volatility clustering  
- Returns normalized for numerical stability  

### Model Evaluation

- AIC  
- QLIKE loss  
- In-sample vs out-of-sample comparison  
- Forecast error distribution analysis  

---

## 4. Event Study Analysis

A classical event-study framework analyzes crypto market reactions to monetary policy sentiment shocks.

### Event Classification

Events are categorized into:

- Negative  
- Neutral  
- Positive  

Based on empirical quantiles of sentiment scores.

For each event:

- A symmetric window of [-3, +3] days is extracted.

### Metrics

- Cumulative Abnormal Returns (CAR)  
- Cumulative Realized Volatility (CRV)  

A two-sample t-test compares abnormal returns following negative versus positive shocks to detect asymmetric market reactions.

This determines whether sentiment shocks primarily affect:

- Prices  
- Volatility  
- Or both  

---

## 5. Trading Strategy

The trading strategy integrates:

- Time-series momentum  
- Volatility scaling  
- Event-based adjustments  
- Realistic cost modeling  

### Execution Modeling

Returns account for:

- Commissions  
- Slippage  
- Perpetual funding costs  

Returns are computed using open-to-open prices to reflect executable trades.

### Signal Generation

- 20-day Donchian breakout rule  
- Long or short positions per asset  
- Trend persistence at asset level  

### Volatility Scaling

- Predicted volatility converted into rolling z-score  
- Logistic transformation applied  
- Exposure reduced during volatility spikes  
- Exposure increased during calmer regimes  

### Event-Based Adjustment

For three days following monetary policy events:

- Exposure temporarily reduced  
- Intensity varies depending on shock direction  

### Portfolio Construction

- Equal-weight across all assets  
- Rebalanced every n days  
- Benchmarked against CMC200 index  

---

## 6. Performance Evaluation

Strategy performance is evaluated using:

- Net Asset Value (NAV)  
- Maximum drawdown  
- Sharpe ratio  
- Hit ratio  
- Payoff ratio  
- Position difference analysis  

Both:

- Unadjusted strategy  
- Volatility-adjusted strategy  

are compared to assess whether volatility-aware exposure improves risk-adjusted performance.

---

## Core Contribution

This framework integrates:

- Macro-finance modeling  
- NLP-based sentiment analysis  
- Machine learning feature selection  
- Event-study methodology  
- Realistic trading simulation  

into a unified empirical system.

It investigates:

- Whether sentiment improves long-run volatility forecasting  
- Whether improved volatility forecasts enhance portfolio stability and drawdown control  
- Whether monetary policy sentiment shocks affect crypto prices, volatility, or both  
