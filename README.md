# Stock Market Candle Trend

A chart in the stock market usually uses candles to present the values at a specific time.
If the chart presents an asset in a daily time frame, for example, then every day will be represented per one candle. Usually, it uses colors to show if the candle is positive or negative. A positive candle is a candle that has a `Close` value bigger than the `Open` value. A negative candle is when the `Close` value is smaller than the `Open` value.

Below an example of a chart where the green candle are positive and the red are negative.

![chart_win.jpg](attachment:chart_win.jpg)

Evaluating the image above is possible to notice that after a positive candle sometimes it have one, two, or more positive candles, and sometimes we have a negative candle. The same happens to a negative candle.

In this study, we want to see what the frequency of has a different candle after the current one. If the last candle was positive what is the probability that the next one is positive and what is the probability of the next one to be negative. We want to verify what is the probability to have the same candle, positive or negative, in a row. For example, have two, three, or more candles positive or negative in a row.

To verify those events this study will evaluate the Brazilian mini index in the daily time frame.

## Load libraries


```python
# Load libraries

import pandas as pd
import os
```

## Load data for the Brazilian mini index from 10/20/2014 to 10/30/2020


```python
# Load data
os.chdir('/Users/morais.rm/Projects/Personal/DataScience/portifolio')

win = pd.read_csv('WIN$_Daily_201410200000_202010300000.csv', sep = '\t')

win.columns = ['Date', 'Open', 'High', 'Low', 'Close', 'Tick_vol', 'Vol', 'Spread']
win.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Date</th>
      <th>Open</th>
      <th>High</th>
      <th>Low</th>
      <th>Close</th>
      <th>Tick_vol</th>
      <th>Vol</th>
      <th>Spread</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2014.10.20</td>
      <td>76665</td>
      <td>77282</td>
      <td>75472</td>
      <td>76172</td>
      <td>141387</td>
      <td>342239</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2014.10.21</td>
      <td>73039</td>
      <td>73946</td>
      <td>72116</td>
      <td>72941</td>
      <td>163626</td>
      <td>394149</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2014.10.22</td>
      <td>72628</td>
      <td>73998</td>
      <td>72213</td>
      <td>72448</td>
      <td>142727</td>
      <td>345869</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2014.10.23</td>
      <td>71873</td>
      <td>72122</td>
      <td>69177</td>
      <td>70022</td>
      <td>174140</td>
      <td>422085</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2014.10.24</td>
      <td>70547</td>
      <td>73688</td>
      <td>70078</td>
      <td>71398</td>
      <td>186986</td>
      <td>477306</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>



## Cleaning data


```python
# Removing columns unnecessary for the study
win = win[['Date', 'Open', 'Close']]
```


```python
# Looking for NaN
win.isnull().sum()
```




    Date     0
    Open     0
    Close    0
    dtype: int64



The data doesn't have any null or NaN data.


```python
# Data statistics
win.describe()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Open</th>
      <th>Close</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>1471.000000</td>
      <td>1471.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>81380.554045</td>
      <td>81450.523453</td>
    </tr>
    <tr>
      <th>std</th>
      <td>15690.233130</td>
      <td>15540.871502</td>
    </tr>
    <tr>
      <th>min</th>
      <td>1001.000000</td>
      <td>48032.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>70210.000000</td>
      <td>70162.500000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>78096.000000</td>
      <td>78144.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>94100.000000</td>
      <td>94328.500000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>119895.000000</td>
      <td>119785.000000</td>
    </tr>
  </tbody>
</table>
</div>



The data above has a strange value to the `Open` column. The minimun value is `1001.00` which is too different comparing other values.<br/>
Let's investigate it.


```python
win[win.Open == 1001]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Date</th>
      <th>Open</th>
      <th>Close</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1207</th>
      <td>2019.10.08</td>
      <td>1001</td>
      <td>100596</td>
    </tr>
  </tbody>
</table>
</div>




```python
win.iloc[1200:1215,]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Date</th>
      <th>Open</th>
      <th>Close</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1200</th>
      <td>2019.09.27</td>
      <td>106369</td>
      <td>106325</td>
    </tr>
    <tr>
      <th>1201</th>
      <td>2019.09.30</td>
      <td>106575</td>
      <td>105919</td>
    </tr>
    <tr>
      <th>1202</th>
      <td>2019.10.01</td>
      <td>106001</td>
      <td>104862</td>
    </tr>
    <tr>
      <th>1203</th>
      <td>2019.10.02</td>
      <td>104081</td>
      <td>101832</td>
    </tr>
    <tr>
      <th>1204</th>
      <td>2019.10.03</td>
      <td>102237</td>
      <td>102341</td>
    </tr>
    <tr>
      <th>1205</th>
      <td>2019.10.04</td>
      <td>101965</td>
      <td>103240</td>
    </tr>
    <tr>
      <th>1206</th>
      <td>2019.10.07</td>
      <td>102897</td>
      <td>101392</td>
    </tr>
    <tr>
      <th>1207</th>
      <td>2019.10.08</td>
      <td>1001</td>
      <td>100596</td>
    </tr>
    <tr>
      <th>1208</th>
      <td>2019.10.09</td>
      <td>101587</td>
      <td>102152</td>
    </tr>
    <tr>
      <th>1209</th>
      <td>2019.10.10</td>
      <td>101889</td>
      <td>103079</td>
    </tr>
    <tr>
      <th>1210</th>
      <td>2019.10.11</td>
      <td>104106</td>
      <td>104310</td>
    </tr>
    <tr>
      <th>1211</th>
      <td>2019.10.14</td>
      <td>104136</td>
      <td>105251</td>
    </tr>
    <tr>
      <th>1212</th>
      <td>2019.10.16</td>
      <td>105010</td>
      <td>106085</td>
    </tr>
    <tr>
      <th>1213</th>
      <td>2019.10.17</td>
      <td>106330</td>
      <td>105940</td>
    </tr>
    <tr>
      <th>1214</th>
      <td>2019.10.18</td>
      <td>105895</td>
      <td>105300</td>
    </tr>
  </tbody>
</table>
</div>



Examining this day is possible to notice that the `Open` value doesn't make any sense.<br/>
To solve this problem we have 3 options:
<ul>
    <li>Remove the line with the problem</li>
    <li>Change the `Open` value to the same to as `Close` value to make this line not positive either negative</li>
    <li>Investigate what was the real values to this day</li>
</ul>


```python
win.loc[1207,'Open'] = 100096
win.loc[1207,]
```




    Date     2019.10.08
    Open         100096
    Close        100596
    Name: 1207, dtype: object



Searching on the internet is possible to find the correct value and then we update that in our dataset.

## Categorizing candles as positive, negative, or neutral


```python
win['Direction'] = win.apply((lambda row:
                              'Positive' if row['Open'] < row['Close'] else 'Negative' if row['Open'] > row['Close'] else 'Neutral'
                             ), axis = 1)

win.head(10)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Date</th>
      <th>Open</th>
      <th>Close</th>
      <th>Direction</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2014.10.20</td>
      <td>76665</td>
      <td>76172</td>
      <td>Negative</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2014.10.21</td>
      <td>73039</td>
      <td>72941</td>
      <td>Negative</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2014.10.22</td>
      <td>72628</td>
      <td>72448</td>
      <td>Negative</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2014.10.23</td>
      <td>71873</td>
      <td>70022</td>
      <td>Negative</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2014.10.24</td>
      <td>70547</td>
      <td>71398</td>
      <td>Positive</td>
    </tr>
    <tr>
      <th>5</th>
      <td>2014.10.27</td>
      <td>66160</td>
      <td>70153</td>
      <td>Positive</td>
    </tr>
    <tr>
      <th>6</th>
      <td>2014.10.28</td>
      <td>70535</td>
      <td>71950</td>
      <td>Positive</td>
    </tr>
    <tr>
      <th>7</th>
      <td>2014.10.29</td>
      <td>71259</td>
      <td>71129</td>
      <td>Negative</td>
    </tr>
    <tr>
      <th>8</th>
      <td>2014.10.30</td>
      <td>71423</td>
      <td>72386</td>
      <td>Positive</td>
    </tr>
    <tr>
      <th>9</th>
      <td>2014.10.31</td>
      <td>73614</td>
      <td>75829</td>
      <td>Positive</td>
    </tr>
  </tbody>
</table>
</div>



Now is possible to know when a candle was positive, negative, or neutral (`Open` and `Close` values are equal).


```python
win['Direction'].value_counts()
```




    Positive    737
    Negative    732
    Neutral       2
    Name: Direction, dtype: int64



The dataset has in total 1471 candles where only 2 are neutral. The dataset has almost 50/50 for positive and negative candles.<br/>
It is expected in a big sample because the stock market is a <a href='https://en.wikipedia.org/wiki/Random_walk_hypothesis' target='_blank'>random walk</a>.

## Counting the candles trend


```python
win['Count'] = 0
for i in range(1, len(win)):
    direction = 0
    
    if win.loc[i, 'Direction'] == win.loc[i - 1, 'Direction']:
        if win.loc[i, 'Direction'] == 'Positive':
            direction = 1
        elif win.loc[i, 'Direction'] == 'Negative':
            direction = -1
    
    if (direction > 0) & (win.loc[i - 1, 'Count'] > 0):
        direction += win.loc[i - 1, 'Count']
    elif (direction < 0) & (win.loc[i - 1, 'Count'] < 0):
        direction += win.loc[i - 1, 'Count']
    
    win.loc[i, 'Count'] = direction

win.head(10)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Date</th>
      <th>Open</th>
      <th>Close</th>
      <th>Direction</th>
      <th>Count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2014.10.20</td>
      <td>76665</td>
      <td>76172</td>
      <td>Negative</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2014.10.21</td>
      <td>73039</td>
      <td>72941</td>
      <td>Negative</td>
      <td>-1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2014.10.22</td>
      <td>72628</td>
      <td>72448</td>
      <td>Negative</td>
      <td>-2</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2014.10.23</td>
      <td>71873</td>
      <td>70022</td>
      <td>Negative</td>
      <td>-3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2014.10.24</td>
      <td>70547</td>
      <td>71398</td>
      <td>Positive</td>
      <td>0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>2014.10.27</td>
      <td>66160</td>
      <td>70153</td>
      <td>Positive</td>
      <td>1</td>
    </tr>
    <tr>
      <th>6</th>
      <td>2014.10.28</td>
      <td>70535</td>
      <td>71950</td>
      <td>Positive</td>
      <td>2</td>
    </tr>
    <tr>
      <th>7</th>
      <td>2014.10.29</td>
      <td>71259</td>
      <td>71129</td>
      <td>Negative</td>
      <td>0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>2014.10.30</td>
      <td>71423</td>
      <td>72386</td>
      <td>Positive</td>
      <td>0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>2014.10.31</td>
      <td>73614</td>
      <td>75829</td>
      <td>Positive</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>



## Evaluate the candles trend


```python
round(win['Count'].value_counts(normalize = True) * 100, 2)
```




     0    51.53
     1    13.60
    -1    12.17
    -2     6.05
     2     5.85
    -3     3.13
     3     2.45
     4     1.43
    -4     1.43
    -5     0.68
     5     0.54
    -6     0.34
     6     0.27
    -7     0.27
     7     0.20
     8     0.07
    Name: Count, dtype: float64




```python
(win['Count'].value_counts(normalize = True) * 100).sort_index().plot.bar(figsize = [16,8]);
```


![png](README_files/README_27_0.png)


It is easy to notice the big difference in the counting of candles. The candles don't repeat the direction in more than 50% of the time.
This result corroborated with the idea that the stock market is a random walk.<br/><br/>
It is possible to notice that the chance to repeat the direction once is around 15.77%, and the chance to repeat twice is 11.90%. The percentages only decrease when the number of repetitions increases.<br/><br/>
Let's try to create a trading system using the knowledge that we just gained.

## Trading system

In this trading system should buy after a negative day and sell after a positive day. Because the Brazilian mini index has a bunch of gaps the operation should start with the `Open` price and finish with the `Close` price of the next day.<br/><br/>
First step is to mark every day as buy or sell.


```python
win['Operation'] = win['Direction'].apply(lambda direction: ('Buy' if direction == 'Negative'
                                                              else 'Sell' if direction == 'Positive'
                                                              else 'Nothing'
                                                            )
                                         )
win['Operation'] = win['Operation'].shift()
win['Operation'].fillna('Nothing', inplace = True)

win.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Date</th>
      <th>Open</th>
      <th>Close</th>
      <th>Direction</th>
      <th>Count</th>
      <th>Operation</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2014.10.20</td>
      <td>76665</td>
      <td>76172</td>
      <td>Negative</td>
      <td>0</td>
      <td>Nothing</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2014.10.21</td>
      <td>73039</td>
      <td>72941</td>
      <td>Negative</td>
      <td>-1</td>
      <td>Buy</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2014.10.22</td>
      <td>72628</td>
      <td>72448</td>
      <td>Negative</td>
      <td>-2</td>
      <td>Buy</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2014.10.23</td>
      <td>71873</td>
      <td>70022</td>
      <td>Negative</td>
      <td>-3</td>
      <td>Buy</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2014.10.24</td>
      <td>70547</td>
      <td>71398</td>
      <td>Positive</td>
      <td>0</td>
      <td>Buy</td>
    </tr>
  </tbody>
</table>
</div>



Second step is to create the target which will be the `Close` price minus the `Open` price.


```python
win['Target'] = win['Close'] - win['Open']

win.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Date</th>
      <th>Open</th>
      <th>Close</th>
      <th>Direction</th>
      <th>Count</th>
      <th>Operation</th>
      <th>Target</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2014.10.20</td>
      <td>76665</td>
      <td>76172</td>
      <td>Negative</td>
      <td>0</td>
      <td>Nothing</td>
      <td>-493</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2014.10.21</td>
      <td>73039</td>
      <td>72941</td>
      <td>Negative</td>
      <td>-1</td>
      <td>Buy</td>
      <td>-98</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2014.10.22</td>
      <td>72628</td>
      <td>72448</td>
      <td>Negative</td>
      <td>-2</td>
      <td>Buy</td>
      <td>-180</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2014.10.23</td>
      <td>71873</td>
      <td>70022</td>
      <td>Negative</td>
      <td>-3</td>
      <td>Buy</td>
      <td>-1851</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2014.10.24</td>
      <td>70547</td>
      <td>71398</td>
      <td>Positive</td>
      <td>0</td>
      <td>Buy</td>
      <td>851</td>
    </tr>
  </tbody>
</table>
</div>



Now is possible to see the result.


```python
win[win.Operation == 'Buy']['Target'].sum()
```




    40233




```python
win[win.Operation == 'Sell']['Target'].sum() * -1
```




    36421



The result looks good! It is necessary to notice that for `Sell` operations is necessary to multiply the result for -1 because when the operation is a `Sell` the profit only happens if the price decrease and to sum it is necessary to do this multiplication.<br/><br/>
Another important point is that the result is the number of points and not currency value.


```python
df = pd.DataFrame(pd.concat([win[win.Operation == 'Buy']['Target'], (win[win.Operation == 'Sell']['Target'] * -1)]))
df.reset_index(drop = True, inplace = True)
df.cumsum().plot(legend = False , figsize = [16, 8]);
```


![png](README_files/README_38_0.png)


The result visually looks good, but there are some problems. One problem is the drawdown after position 400, it is quite steep.<br/><br/>
Is the result really good?<br/>
In the end, the profit in more than 6 years was `76,654` points which are in Brazilian currency `R$ 15,330`. This result is operating one contract.

Let's evaluate now the initial result where we have the frequency percentage of candles.


```python
win['Count_Abs'] = abs(win['Count'])
round(win['Count_Abs'].value_counts(normalize = True) * 100, 2)
```




    0    51.53
    1    25.76
    2    11.90
    3     5.57
    4     2.86
    5     1.22
    6     0.61
    7     0.48
    8     0.07
    Name: Count_Abs, dtype: float64



Here it ignores if the number of candles are positive or negative and uses the absolute value. With this information is possible to see what is the probability to have 1, 2, 3, ... candles doesn't matter the direction.<br/><br/>
The previous trade system buys or sells ignoring this probability. Looking to the result above is possible to see, for example, that after a positive candle has `51.53%` to the next to be a negative one, but after two positive candle this probability increases to 74.24% and this probability just increase when it has more positive candles in sequence.<br/><br/>
Because the probability to have an opposite direction to the current when the `Count` is higher we can try to improve the trading system. If the `Count` is 0 then it operates 1 contract, if it is 1 then operates 2 contacts, if it is 2 then operates 3 contracts, and so on. It has to use always the d-1 (previous `Count`) to evaluate the number of contracts.


```python
win['Contracts'] = abs(win['Count'].shift()) + 1
win.head(10)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Date</th>
      <th>Open</th>
      <th>Close</th>
      <th>Direction</th>
      <th>Count</th>
      <th>Operation</th>
      <th>Target</th>
      <th>Count_Abs</th>
      <th>Contracts</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2014.10.20</td>
      <td>76665</td>
      <td>76172</td>
      <td>Negative</td>
      <td>0</td>
      <td>Nothing</td>
      <td>-493</td>
      <td>0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2014.10.21</td>
      <td>73039</td>
      <td>72941</td>
      <td>Negative</td>
      <td>-1</td>
      <td>Buy</td>
      <td>-98</td>
      <td>1</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2014.10.22</td>
      <td>72628</td>
      <td>72448</td>
      <td>Negative</td>
      <td>-2</td>
      <td>Buy</td>
      <td>-180</td>
      <td>2</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2014.10.23</td>
      <td>71873</td>
      <td>70022</td>
      <td>Negative</td>
      <td>-3</td>
      <td>Buy</td>
      <td>-1851</td>
      <td>3</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2014.10.24</td>
      <td>70547</td>
      <td>71398</td>
      <td>Positive</td>
      <td>0</td>
      <td>Buy</td>
      <td>851</td>
      <td>0</td>
      <td>4.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>2014.10.27</td>
      <td>66160</td>
      <td>70153</td>
      <td>Positive</td>
      <td>1</td>
      <td>Sell</td>
      <td>3993</td>
      <td>1</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>2014.10.28</td>
      <td>70535</td>
      <td>71950</td>
      <td>Positive</td>
      <td>2</td>
      <td>Sell</td>
      <td>1415</td>
      <td>2</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>2014.10.29</td>
      <td>71259</td>
      <td>71129</td>
      <td>Negative</td>
      <td>0</td>
      <td>Sell</td>
      <td>-130</td>
      <td>0</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>2014.10.30</td>
      <td>71423</td>
      <td>72386</td>
      <td>Positive</td>
      <td>0</td>
      <td>Buy</td>
      <td>963</td>
      <td>0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>2014.10.31</td>
      <td>73614</td>
      <td>75829</td>
      <td>Positive</td>
      <td>1</td>
      <td>Sell</td>
      <td>2215</td>
      <td>1</td>
      <td>1.0</td>
    </tr>
  </tbody>
</table>
</div>



Let's see the result using contracts.


```python
buy = win[win.Operation == 'Buy'].copy()
buy['Result'] = buy['Target'] * buy['Contracts']
buy['Result'].sum()
```




    67369.0




```python
sell = win[win.Operation == 'Sell'].copy()
sell['Result'] = sell['Target'] * -1 * sell['Contracts']
sell['Result'].sum()
```




    164815.0




```python
df = pd.DataFrame(pd.concat([buy['Result'], sell['Result']]))
df.reset_index(drop = True, inplace = True)
df.cumsum().plot(legend = False , figsize = [16, 12]);
```


![png](README_files/README_47_0.png)


The result looks even better, but we have more problems with the drawdown.<br/><br/>
The final result was `232,184` points which are in Brazilian currency `R$ 46,436`. The result is around 3 times better than the first one.<br/><br/>
For sure it is a too simple strategy that can't be applied and need more study mainly in the drawdowns, but the idea of candle's frequency probability is really good and can be used to create a professional and profitable trade strategy.
