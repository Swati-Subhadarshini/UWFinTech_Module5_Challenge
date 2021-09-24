# UWFinTech_Module5_Challenge
## Financial Analysis Tools using Monte Carlo Simulation

The project entails building a tool to help credit union members evaluate their financial health. Specifically, the credit union board wants the members to be able to do two things. First, they should be able to assess their monthly budgets. Second, they should be able to forecast a reasonably effective retirement plan based on their current holdings of cryptocurrencies, stocks, and bonds.

Part 1: A financial planner for emergencies. The members will be able to use this tool to visualize their current savings. The members can then determine if they have enough reserves for an emergency fund.

Part 2: A financial planner for retirement. This tool will forecast the performance of their retirement portfolio in 30 years. To do this, the tool will make an Alpaca API call via the Alpaca SDK to get historical price data for use in Monte Carlo simulations.

---

## Technologies Used

Leveraging Jupyter Notebook
Gitbash CLI is used to pull and push the code from local repository to remote repository
Code written with the help of Jupyter Notebook.
Using Alpaca API and SDK for the cryptocurrencies data and stock & bond datas.
ALPACA API KEY AND ALPACA SECRET KEY are used for data retrieval from APIs

---

## Libraries and Dependencies

import os
import requests
import json
import pandas as pd
from dotenv import load_dotenv
import alpaca_trade_api as tradeapi
from MCForecastTools import MCSimulation

%matplotlib inline

---
## Analysis 

### Part 1: Create a Financial Planner for Emergencies

Each credit union member has a savings portfolio that consists of a cryptocurrency wallet, stocks, and bonds.

### Evaluate the Cryptocurrency Wallet by Using the Requests Library

You’ll collect the current prices for the Bitcoin and Ethereum cryptocurrencies by using the Python Requests library. For the prototype, you’ll assume that the member holds the 1.2 Bitcoins (BTC) and 5.3 Ethereum coins (ETH).

Use the Requests library to get the current price (in US dollars) of Bitcoin (BTC) and Ethereum (ETH) by using the API endpoints.

btc_url = "https://api.alternative.me/v2/ticker/Bitcoin/?convert=USD"
eth_url = "https://api.alternative.me/v2/ticker/Ethereum/?convert=USD"

Navigate the JSON response object to access the current price of each coin, and store each in a variable.

`btc_response = requests.get(btc_url).json()`

### Evaluate the Stock and Bond Holdings by Using the Alpaca SDK

In this section, you’ll determine the current value of a member’s stock and bond holdings. You’ll make an API call to Alpaca via the Alpaca SDK to get the current closing prices of the SPDR S&P 500 ETF Trust (ticker: SPY) and of the iShares Core US Aggregate Bond ETF (ticker: AGG). For the prototype, assume that the member holds 110 shares of SPY, which represents the stock portion of their portfolio, and 200 shares of AGG, which represents the bond portion.

In the Starter_Code folder, create an environment file (.env) to store the values of your Alpaca API key and Alpaca secret key.

Set the variables for the Alpaca API and secret keys. Using the Alpaca SDK, create the Alpaca tradeapi.REST object. In this object, include the parameters for the Alpaca API key, the secret key, and the version number.

`alpaca = tradeapi.REST(alpaca_api_key, alpaca_secret_key, api_version="v2")`

Set the following parameters for the Alpaca API call:

tickers: Use the tickers for the member’s stock and bond holdings.
`tickers = ["SPY", "AGG"]`

timeframe: Use a time frame of one day.
`timeframe = "1D"`

start_date and end_date: Use the same date for these parameters, and format them with the date of the previous weekday (or 2020-08-07). This is because you want the one closing price for the most-recent trading day.

```start_date = pd.Timestamp("2020-08-07", tz="America/New_York").isoformat()
end_date = pd.Timestamp("2020-08-07", tz="America/New_York").isoformat()
```

Get the current closing prices for SPY and AGG by using the Alpaca get_barset function. Format the response as a Pandas DataFrame by including the df property at the end of the get_barset function.
`closing_prices = alpaca.get_barset(tickers, timeframe, start=start_date, end=end_date).df`

Calculate the value, in US dollars, of the current amount of shares in each of the stock and bond portions of the portfolio, and print the results.

### Evaluate the Emergency Fund
In this section, you’ll use the valuations for the cryptocurrency wallet and for the stock and bond portions of the portfolio to determine if the credit union member has enough savings to build an emergency fund into their financial plan.

`savings_data = [total_crypto_wallet, total_stocks_bonds]`

Use the savings_df DataFrame to plot a pie chart that visualizes the composition of the member’s portfolio. The y-axis of the pie chart uses amount. Be sure to add a title.

`savings_df = pd.DataFrame(savings_data, columns=['amount'], index=['crypto','stock/bond'])`

Using Python, determine if the current portfolio has enough to create an emergency fund as part of the member’s financial plan. Ideally, an emergency fund should equal to three times the member’s monthly income. 

Create a variable named emergency_fund_value, and set it equal to three times the value of the member’s monthly_income of $12000. (You set this earlier in Part 1).

Create a series of three if statements to determine if the member’s total portfolio is large enough to fund the emergency portfolio:

if total_portfolio > emergency_fund_value:
    print("Congratulations! You have enough funds in your savings.")
elif total_portfolio == emergency_fund_value:
    print("Congratulations! You are reaching important financial goal.")
elif total_portfolio < emergency_fund_value:
    print(f"You need additional money of amount: ${emergency_fund_value - total_portfolio}")

Part 2: Create a Financial Planner for Retirement
In this section, you’ll use the Alpaca API to get historical closing prices for a retirement portfolio. You’ll then run Monte Carlo simulations to forecast the portfolio performance 30 years from now. You’ll use the simulated data to answer questions in your Jupyter notebook about the portfolio.

Use the starter code in financial_planning_tools.ipynb to complete the steps in the following subsections.

Create the Monte Carlo Simulation
In this section, you’ll use the MCForecastTools library to create a Monte Carlo simulation for the member’s savings portfolio. To do this, complete the following steps:

Make an API call via the Alpaca SDK to get 3 years of historical closing prices for a traditional 60/40 portfolio split: 60% stocks (SPY) and 40% bonds (AGG).

Run a Monte Carlo simulation of 500 samples and 30 years for the 60/40 portfolio, and then plot the results. The following image shows the overlay line plot resulting from a simulation with these characteristics. However, because a random number generator is used to run each live Monte Carlo simulation, your image will differ slightly from this exact image:

`MC_30years = MCSimulation(
    portfolio_data = prices_df,
    weights = [.60,.40],
    num_simulation = 500,
    num_trading_days = 252 * 30
)`


What are the lower and upper bounds for the expected value of the portfolio with a 95% confidence interval?

`ci_lower_thirty_cumulative_return = round(MC_30years_summary_table[8] * total_stocks_bonds,2)
ci_upper_thirty_cumulative_return = round(MC_30years_summary_table[9] * total_stocks_bonds,2)
`
Forecast Cumulative Returns in 10 Years
The CTO of the credit union is impressed with your work on these planning tools but wonders if 30 years is a long time to wait until retirement. So, your next task is to adjust the retirement portfolio and run a new Monte Carlo simulation to find out if the changes will allow members to retire earlier.

For this new Monte Carlo simulation, do the following:

Forecast the cumulative returns for 10 years from now. Because of the shortened investment horizon (30 years to 10 years), the portfolio needs to invest more heavily in the riskier asset—that is, stock—to help accumulate wealth for retirement.

Adjust the weights of the retirement portfolio so that the composition for the Monte Carlo simulation consists of 20% bonds and 80% stocks.
Run the simulation over 500 samples, and use the same data that the API call to Alpaca generated.


`MC_10years = MCSimulation(
    portfolio_data = prices_df,
    weights = [.80,.20],
    num_simulation = 500,
    num_trading_days = 252 * 10
)`


Using the current value of only the stock and bond portion of the member's portfolio and the summary statistics that you generated from the new Monte Carlo simulation, what are the lower and upper bounds for the expected value of the portfolio (with the new weights) with a 95% confidence interval?

`ci_lower_ten_cumulative_return = round(MC_10years_summary_table[8] * total_stocks_bonds,2)
ci_upper_ten_cumulative_return = round(MC_10years_summary_table[9] * total_stocks_bonds,2)
`

Will weighting the portfolio more heavily toward stocks allow the credit union members to retire after only 10 years?

Amswer: Not exactly, even if weighting the portfolio more heavily to stocks doesn't give much profit. Considering with intial investment of $60689.50 the 10years return with more weightage around 80% of portfolio towards stocks gives a range of returns between $63985-$147495. However if the investment stays for 30 years the profit range is much better i.e, $142739.75 - $1167529.26 with comparatively lower stock exposure(60%). 

## Contributors

This project is designed by Swati Subhadarshini 
Emaid id: sereneswati@gmail.com
LinkedIn link: https://www.linkedin.com/in/swati-subhadarshini-89a8a538?lipi=urn%3Ali%3Apage%3Ad_flagship3_profile_view_base_contact_details%3BhUCLlUYCSc2jK4x4khPVUQ%3D%3D

---