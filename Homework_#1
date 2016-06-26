# QSTK Imports
import QSTK.qstkutil.qsdateutil as du
import QSTK.qstkutil.tsutil as tsu
import QSTK.qstkutil.DataAccess as da

# Third Party Imports
import datetime as dt
import matplotlib.pyplot as plt
import pandas as pd
import numpy as np

Number_Trading_Days = 252

def simulate(start_date, end_date, symbols, allocations):

  # List of symbols
  ls_symbols = symbols
  
  # Start and End date of the simualte function
  dt_start = start_date
  dt_end = end_date

  # We need closing prices so the timestamp should be hours=16. (time = 4PM)
  dt_timeofday = dt.timedelta(hours=16)

  # Get a list of trading days between the start and the end.
  ldt_timestamps = du.getNYSEdays(dt_start, dt_end, dt_timeofday)

  # Creating an object of the dataaccess class with Yahoo as the source.
  c_dataobj = da.DataAccess('Yahoo')

  # Keys to be read from the data, it is good to read everything in one go.
  ls_keys = ['open', 'high', 'low', 'close', 'volume', 'actual_close']

  # Reading the data, now d_data is a dictionary with the keys above.
  # Timestamps and symbols are the ones that were specified before.
  ldf_data = c_dataobj.get_data(ldt_timestamps, ls_symbols, ls_keys)
  d_data = dict(zip(ls_keys, ldf_data))

  # Copying close price into separate dataframe to find rets
  df_rets = d_data['close'].values.copy()

  # Normalizing the prices to start at 1 and see relative returns
  df_normal = df_rets / df_rets[0, :] 

  #reshape the list of allocations
  alloc = np.array(allocations).reshape(4,1) 

  #Calculate the portfolio value
  portVal = np.dot(df_normal, alloc)

  #Calculate the daily value
  dailyVal = portVal.copy()

  # returnize0 works on ndarray and not dataframes.
  tsu.returnize0(dailyVal)

  #Calculate daily returns
  daily_ret = np.mean(dailyVal)
  vol = np.std(dailyVal)

  #sharpe ratio = K * mean(daily return) / std(daily return)
  #K = sqrt(Number of trading days)
  sharpe_ratio = np.sqrt(Number_Trading_Days) * (daily_ret / vol)

  cum_ret = portVal[portVal.shape[0] -1][0]

  return daily_ret,vol,sharpe_ratio,cum_ret


def print_simulate(start_date, end_date, symbols, allocations):
  daily_ret, vol, sharpe_ratio, cum_ret = simulate(start_date, end_date, symbols, allocations)

  print "Start Date: ", start_date
  print "End Date: ", end_date
  print "Symbols: ", symbols
  print "Optimal Allocations: ", allocations
  print "Average Daily Return: ", daily_ret
  print "Volatility: ", vol
  print "Shape Ratio: ", sharpe_ratio
  print "Cumulative Return: ", cum_ret


def optimal_allocation_4_stocks(start_date, end_date, symbols):

  max_sharpe = -1
  max_allocation = [0.0, 0.0, 0.0, 0.0]
  for i in range(0,11):
    for j in range(0,11-i):
      for k in range(0,11-i-j):
        for l in range(0,11-i-j-k):
          if (i+j+k+l) == 10:
            alloc = [float(i)/10, float(j)/10, float(k)/10, float(l)/10]
            daily_ret, vol, sharpe_ratio, cum_ret = simulate(start_date, end_date, symbols, alloc)
            if sharpe_ratio > max_sharpe:
              max_sharpe = sharpe_ratio
              max_allocation = alloc
  return max_allocation
              

  
  
start_date = dt.datetime(2010,1,1)
end_date = dt.datetime(2010,12,31)
symbols = ['C', 'GS', 'IBM', 'HNZ']

max_allocation = optimal_allocation_4_stocks(start_date, end_date, symbols)

print_simulate(start_date, end_date, symbols, max_allocation)
