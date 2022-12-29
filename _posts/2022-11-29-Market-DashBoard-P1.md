---
title: Market Dashboard Part 1
date: 2022-11-29 9:00:00 -500
categories: [python,yfinance]
tags: [yfiannce,markets,dashboard,python]
---

This article is going to step through the initial steps of creating a market dashboard. We will be using the yfiance package to pull market data from Yahoo Finance.

In Part 1, we will cover:
- Determing the market indices
- Handling Dates and Date Ranges
- Pulling the market data from yFiannce
- Putting it together in a Trailing Returns Table

Firstly, let's setup the python environment by importing/installing the required packages:

 ```python
import yfinance as yf #used to extract market data
import numpy as np #used to calculate log returns
from datetime import datetime, timedelta #used for setting the dates 
import pandas as pd #used for dataframe manipulation
from calendar import monthrange #used within the DateList class
```

## The Market Indices

For this example I'm using some Australian market indices available on Yahoo Finance.

- AXJO: S&P/ASX 200 Index
- AXSO: S&P/ASX Small Ordinaries
- AXMJ: S&P/ASX 200 Materials
- AXEJ: S&P/ASX 200 Energy
- AXJR: S&P/ASX 200 Resources
- AXXJ: S&P/ASX 200 Financials ex Property
- AXPJ: S&P/ASX 200 A-REIT
- AXSJ: S&P/ASX 200 Consumer Staples
- AXDJ: S&P/ASX 200 Consumer Discretionary
- AXUJ: S&P/ASX 200 Utilities
- AXNJ: S&P/ASX 200 Industrials
- AXIJ: S&P/ASX 200 Information Technology
- AXTJ: S&P/ASX 200 Communication


```python 
data={'Ticker':['^AXJO','^AXSO','^AXMJ','^AXEJ','^AXJR','^AXXJ','^AXPJ','^AXSJ','^AXDJ','^AXUJ','^AXNJ','^AXIJ','^AXTJ'],
'Short-Name':['ASX 200','ASX Small Caps','ASX Materials','ASX Energy','ASX Resources','ASX Financials','ASX REITs','ASX Cons Staples','ASX Cons Disc','ASX Utilities','ASX Ind','ASX IT','ASX Comms'],
'Long-Name':['S&P/ASX 200 Index','S&P/ASX Small Ordinaries','S&P/ASX 200 Materials','S&P/ASX 200 Energy','S&P/ASX 200 Resources','S&P/ASX 200 Financials ex Property','S&P/ASX 200 A-REIT','S&P/ASX 200 Consumer Staples','S&P/ASX 200 Consumer Discretionary','S&P/ASX 200 Utilities','S&P/ASX 200 Industrials','S&P/ASX 200 Information Technology','S&P/ASX 200 Communication']
}

tickdf = pd.DataFrame(data)
tickdf.set_index('Ticker', inplace = True)
```

## Handling Dates
 I've created a class called DateList to take an list of periods and an end date. This will provide all of the information we require from the dates:

For each trailing period, the class will provide
- Number of days relative to the end date 
- Start dates relative to the end date
- The oldest date within the range of dates
- An annualised period to be used when turning cumulative returns to annualised returns, for periods greater than one year


```python
class DateList:
  def __init__(self, days: list, enddate: str):
    # Convert the input date string to a datetime object and store it in an instance variable
    self.dt = datetime.strptime(enddate, "%Y-%m-%d")
    self.days = days
  
  #Determines number of days from each item in the list to enddate
  def num_days(self) -> list:
    # Initialize an empty list to store the result
    result = []
    # Loop through the days in the list
    for x in self.days:
      if isinstance(x, str):
        # x.lower() converts everything to lowercase, so the strings can be upper or lower
        if x.lower() == "mtd":
          # If the value is "mtd", get the last day of the previous month
           self.dcount = timedelta(days=self.dt.day)
        elif "QTD" in x:
          quarter_end_dates = [datetime(self.dt.year -1, 12, 31),
                              datetime(self.dt.year, 3, 31),
                              datetime(self.dt.year, 6, 30),
                              datetime(self.dt.year, 9, 30)]
          quarter = (self.dt.month - 1) // 3
          self.dcount = self.dt - quarter_end_dates[quarter]
        elif "cytd" in x.lower():
          #if value is Calender Year to Date (CYTD), then set day to 31 and month to 12 and subtract one year
          self.dcount = self.dt - self.dt.replace(day=31, month=12, year= self.dt.year -1)
        elif " year" in x.lower():
          # If the value is in the form "x years", extract the number of years and convert it to an integer
          num_years = int(x.split(" ")[0])
          # Subtract the number of years from the datetime object
          self.dcount = self.dt - self.dt.replace(year=self.dt.year - num_years)
      else:
        # If the value is not "mtd" or "x years", subtract the number of days from the datetime object
        self.dcount = timedelta(days=x)
        # Append the result as a formatted string to the list
      result.append(int(self.dcount.days))
    # Return the list of dates
    return result
  
  # Subtracts result of num_days() from end date to determine the start date for each item in the list
  def start_dates(self) -> list:
    result = []
    num_days = self.num_days()
    for y in num_days:
      self.sdate = self.dt - timedelta(days=y)
      result.append(self.sdate.strftime("%Y-%m-%d"))
    return result 

# Used to calculate annualised returns based off series of returns. 
  def annualised_period(self) -> list:
    result = []
    for y in self.num_days():
      if y < 365:
        self.ann_period = 1
        result.append(self.ann_period)
      else:
        self.ann_period = 1 / (y / 365)
        result.append(self.ann_period)
    return result 
  
  def oldest_date(self): 
    result = min(self.start_dates(), key=lambda x: datetime.strptime(x, '%Y-%m-%d').date())
    return result
```

## Pulling the market data from yFinance
For this example we'll be looking at the below periods:

- Month to Date (MTD)
- Quarter to Date (QTD)
- Calendar Year to Date (CYTD)
- Trailing 1 Month (T1M)
- Trailing 3 Month (T3M)
- Trailing 1 Year (T1Yr)
- Trailing 5 Year (T5Yr)

We'll pass these through to our DateList class along with yesterday's date:

```python
enddate = (datetime.today() - timedelta(days=1)).strftime("%Y-%m-%d")
timelist = DateList(['mtd','QTD','CYTD',30,90,"1 years","5 Years"], enddate)
```

Then we'll use the oldest date from our list of dates and the tickers for our market indices to pull the data from yFinance (Yahoo Finance) 

```python
data = yf.download(tickdf.index.to_list(), start=timelist.oldest_date(), end=enddate, group_by="column")

dailyreturns = data['Adj Close'].ffill().pct_change()
```

## Creating a Trailing Returns Table
Now we have the starting date for each time period, relative to the current date, we will subset the dataframe and calculate the return for each index and period.

```python
dfreturnstable = pd.DataFrame(columns = timelist.start_dates(), index=dailyreturns.columns)
n = 0
for p in timelist.start_dates():
  dfreturnstable[p] = (dailyreturns[(dailyreturns.index > p) & (dailyreturns.index <= today)].add(1).prod() ** timelist.annualised_period()[n]) - 1 
  n = n + 1
```