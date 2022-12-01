---
title: Market Dashboard Part 1
date: 2022-11-29 9:00:00 -500
categories: [python,yfinance]
tags: [yfiannce,markets,dashboard,python]
---

This article is going to step through the initial steps to creating a market dashboard. We will be using the yfiance package to pull market data from Yahoo Finance.

In Part 1, we will cover:
- Collecting and Structuring Data
- Determing Start and End Dates
- Creating a Trailing Returns Table

Firstly, let's setup the python environment by importing/installing the required packages:

 ```python
import yfinance as yf #used to extract market data
import numpy as np #used to calculate log returns
import datetime as dt #used for setting the dates 
from calendar import monthrange #used for setting the dates
import pandas as pd #used for dataframe manipulation
import matplotlib.pyplot as plt #used to create charts
```

## Collecting and Structuring the Data

For this example I'm using some Australian market indices available on Yahoo Finance:

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
tickerlist = tickdf.index.values.tolist()

#lets first set our date range. We want to this to provide the most up to date data which would be as of yesterday's close. We'll also calulate the start date as five years prior. 
asofdate = (dt.date.today() - dt.timedelta(days=1))
ed = asofdate.strftime('%Y-%m-%d')
st = (dt.date.today() - dt.timedelta(days=365*5)).strftime('%Y-%m-%d')

#this function will pull the data from yahoo finance
data = yf.download(tickerlist, start=st, end=ed, group_by="column")

#This will replace any missing days with the prior day's value and then calculate the simple daily return. 
dailyreturns = data['Adj Close'].ffill().pct_change()
```

## Determing Start and End Dates
 
 For this example we'll be looking at the below periods and determing what the start date is relative to the current date for each period:

- Month to Date (MTD)
- Quarter to Date (QTD)
- Calendar Year to Date (CYTD)
- Trailing 1 Month (T1M)
- Trailing 3 Month (T3M)
- Trailing 1 Year (T1Yr)
- Trailing 5 Year (T5Yr)

```python
#The below will use the asofdate to determine the beginning date for each time period
MTD = asofdate - dt.timedelta(days=asofdate.day)
QTD = dt.date(asofdate.year, (asofdate.month // 3) * 3, monthrange(asofdate.year,(asofdate.month // 3) * 3)[1])
CYTD = dt.date(asofdate.year-1,12,31)
T1M = asofdate - dt.timedelta(days=30)
T3M = asofdate - dt.timedelta(days=90)
T1Yr = dt.date(asofdate.year-1,asofdate.month,asofdate.day)
T5Yr = dt.date(asofdate.year-5,asofdate.month,asofdate.day)

#This combines them into a dataframe which can be used to iterate though and also to provide specific column names
timeList = pd.DataFrame({
'periods':[MTD,QTD,CYTD,T1M,T3M,T1Yr,T5Yr],
'Short-Name':['MTD',"QTD",'CYTD','1M','3M','1Yr','5Yr']
})
numdays = []
for t in timeList['periods']:
    numdays.append((asofdate - t).days)
timeList['NumberOfDays'] = numdays
timeList['SubDays'] = [numdays[0],numdays[1],numdays[2],numdays[3],numdays[4],365,365]
```
## Creating a Trailing Returns Table

Now we have the starting date for each time period, relative to the current date, we will subset the dataframe and calculate the return for each index and period.

```python
dfreturnstable = pd.DataFrame(columns = timeList['Short-Name'], index=logreturns.columns)

p = 0
while(p < len(timeList['NumberOfDays'])):
        dfreturnstable[timeList.iloc[p,1]] = (dailyreturns[(dailyreturns.index > str(timeList.loc[p,'periods'])) & (dailyreturns.index <= str(asofdate))]).add(1).prod() ** (timeList.loc[p,'SubDays'] / timeList.loc[p,'NumberOfDays']) - 1
        p = p + 1

dfreturnstable = dfreturnstable.merge(tickdf['Long-Name'],how='inner',right_index=True, left_index=True)
dfreturnstable = dfreturnstable.reindex(tickdf.index)
dfreturnstable = dfreturnstable.set_index('Long-Name')
dfreturnstable = dfreturnstable.multiply(100).round(2)
```