# SPX Index Option Backtester

![spx_backtest](https://github.com/ohdearquant/lion_consulting/assets/122793010/850e6a3d-1b4a-487c-a3f8-c9cd3617fcb7)

Set up a framework for backtesting here is a plot of a sample strategy. 

## Sample Strategy

### Data
Daily one-min frequency SPX option data, ~7 million rows of data per day

### Strategy
Rebalance at-the-money (ATM) straddle, at certain frequency. Meaning if at time of rebalancing, and the current position is not ATM, we then close it and reopen the current ATM straddle. Here used `[1, 3, 5, 10, 15, 30, 60]` mins frequencies for testing. 

## Python Plotting Function

```py
import plotly.graph_objects as go

# Define the x-axis time values
time_mins = [9*60 + 45 + i for i in range(360)]
time_labels = [f"{(time // 60) % 12 if (time // 60) % 12 != 0 else 12}:{time % 60:02} {'AM' if time < 12*60 else 'PM'}" for time in time_mins]

# Select a subset of time_labels to reduce density of x-ticks
time_ticks = [time_labels[i] for i in range(0, len(time_labels), 30)] # Adjust the step size to increase or decrease density

# Create figure
fig = go.Figure()

# Add traces
for idx, interval in enumerate(intervals):
	fig.add_trace(go.Scatter(x=time_labels, y=pnls[idx], 
				mode='lines', name=f"{interval} mins"))

# Update layout
fig.update_layout(
	title='SPX 2020-01-03 ATM Straddle PnL by rebalancing Frequency',
	xaxis=dict(
		title='Time (from 9:45 AM to 3:45 PM)',
		showline=True,
		showgrid=False,
		showticklabels=True,
		linecolor='rgb(204, 204, 204)',
		linewidth=2,
		ticks='outside',
		tickvals=time_ticks,
		ticktext=time_ticks,
		tickfont=dict(
		family='Arial',
		size=12,
		color='rgb(82, 82, 82)',
		),
	),
		
	yaxis=dict(
		title='PnL',
		showgrid=True,
		gridcolor='rgb(224, 224, 224)',
		zeroline=False,
		showline=True,
		showticklabels=True,
	),
		
	showlegend=True,	
	template='plotly',
	plot_bgcolor='rgba(240, 240, 240, 0.95)'
)
	
# Show plot
fig.show()
```

