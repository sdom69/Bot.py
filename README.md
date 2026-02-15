import yfinance as yf
import pandas as pd
import numpy as np

# Fetch data
symbol = 'AAPL'
data = yf.download(symbol, period='1y')
data['SMA_short'] = data['Close'].rolling(20).mean()
data['SMA_long'] = data['Close'].rolling(50).mean()

# Generate signals
data['Signal'] = np.where(data['SMA_short'] > data['SMA_long'], 1, 0)
data['Position'] = data['Signal'].diff()

# Backtest results (simplified)
data['Returns'] = data['Close'].pct_change()
data['Strategy'] = data['Returns'] * data['Signal'].shift(1)
cumulative = (1 + data['Strategy']).cumprod().iloc[-1] - 1
print(f"Cumulative return: {cumulative:.2%}")
print(data.tail())
