# WRDS-News-Backtest
This program uses the Wharton Research Data Services database to backtest the results of news events on stock prices using Python in Jupyter Notebook.

## Problem
The goal is to analyze the impact that certain types of news had on stock prices. For example, how did stocks perform after a secondary offering, contract, or FDA approval was announced? 

## Solution and Process
The input CSV has three columns: the stock ticker, the datetime of the news, and the news headline. Below is a sample of the input CSV:
<img width="829" alt="Screen Shot 2022-11-27 at 5 53 03 PM" src="https://user-images.githubusercontent.com/118930217/204170117-aa44e18f-4418-4250-8008-27526187942f.png">

The input CSV is sourced from a large database of news events using a python program or manually if desired. 

The process of the program is visualized in this diagram:
![Blank diagram (1)](https://user-images.githubusercontent.com/118930217/204179051-91affa62-c566-4ba6-88dd-6cfca43bd3e4.jpeg)

The output CSV has sixteen columns (some listed same line):
- Ticker
- Date
- Time of news
- Headline
- Market cap (at time of news)
- The following price changes (in %change from price before news): 1min, 5min, 15min, 1hr, 2hr, 4hr, EOD change
- Price right before news
- Bid/Ask (NBBO) at time of news
- Percent difference between bid and ask (relevant since small cap stocks can have wide spreads)

Below is a sample of the output CSV, using the input CSV above:
<img width="1135" alt="Screen Shot 2022-11-27 at 6 50 41 PM" src="https://user-images.githubusercontent.com/118930217/204174977-38e151eb-40a5-48ab-8b9d-4c7155e3fa96.png">
Note: In the event of incomplete data or a stock that simply has no trades at the time of news, fillers were used, such as "-1000" for price change, or "0" for bid/ask data.

## Results Analysis
The results give the user quick information on the following factors about the particular news event:
- Overall average move in shorter or longer time increments
- Ability to sort moves by market cap, showing the relation between market cap and price change
- Potential liquidity of the stock at the given time, based on the spread (assuming large spread = less liquidity)
- Permanence of the move (did stocks spike quickly but then immediately revert to earlier price?)

## Challenges
The primary challenges in creating this program involved maniuplating datetime, working with/accessing data from the WRDS database, and working with Jupyter Notebook. 

A few reasons the datetime was challenging:
- The data was timestamped with very specific times, so the program had to find the nearest prices before and after the timestamps of the input CSV to get "Price before news" and "Bid/Ask (NBBO) at time of news". For example, if the timestamp of the news event was 08:00:00, then the nearest price data points might be 07:59:59.995, or may not even exist on that day in the case of a very lightly traded stock.
- Concerning the price change, the program had to recognize the end time of the price data that day. For example, mostly the end of day change would be the price at 20:00:00 (8pm EST), but if the news came out five minutes earlier, say 19:55:00, then the 5min, 15min...all the way to 4hr and EOD price change would be the same. 
- Lastly, if the news came out on a weekend or other time outside of regular trading hours (4am EST to 8pm EST), then the program had to recognize the lack of price data and mark the price changes with "-1000"

A few reasons working with data was challenging:
- The data came from different databases within the WRDS relm. The price data came from a database called Nasdaq TAQ, while the market cap and NBBO data came from a database called CRSP. 
- The market cap was not simply a number in the CRSP database, it had to be calculated using price times shares outstanding. Also, tickers can get delisted/changed/etc, so the program had to be sure that it was accessing the price/shares outstanding for the correct company
- I had not used the WRDS API before, so gaining authentication and making SQL queries I had to learn as I made the program

Lastly, this was my first project in Jupyter Notebook so I learned the intricacies while making the program. 

## Other applications
Certain parts of this program can be used to granually visualize extremely fast news events. In one example, I used the price data part of this program to analyze the swiftness of buyout news events (either rumors or announcements). I modified the program to take a start/end time and ticker as input, and then print every trade of that ticker between those times into a CSV. The trades on the CSV could then be converted to a line graph, where the shape and speed of the spike could be easily visualized. 

The historical market cap program can also be used standalone. This data is not super easily accessible. Generally consumer-facing price stock companies don't keep data for historical market cap. So, for example, that section of the program could be used to figure out a question such as: "What was the market cap of AAPL on 10/10/2015?"

## Potential Advancements
There are three potential advancements:
- Firstly, an NLP program could be attached to the start of the program, so that the orginial input would just be the news events database. In this way the user could toggle easily between different types of news, without having to manually put together the input CSV. For example, the user could opt to analyze offering headlines, and the program could find offering examples from the news database, and then output the price change results.
- Secondly, the program could use the data from the output CSV to create visuals of the moves that the stocks go through, so that the user could easily analyze the results
- Thirdly, using the CRSP database was extremely slow to get the market cap of a program. For any given row in the input CSV, getting the market cap would take about 95% of the time (getting price data was the other 5%), so using another database or source to calculate maket cap would speed up the program significantly.
