Course: [Importing and Managing Financial Data in Python](https://app.datacamp.com/learn/courses/importing-and-managing-financial-data-in-python)

This study note focuses on basic financial terms, KPIs, metrics when analyzing stocks.

### Table of Content
- [](#)




### Importing stock listing data from Excel
![img](images/m01_01.png)
a file with info on companies listed on the AmEx Stock Exchange. This file contains a __company's name and stock ticker__, which is the symbol needed to get price and other information about a company from an exchange. It also contains its __sector, industry and IPO year__, that is the year when it started trading on a stock exchange. It also contains __the most recent share price, and the market capitalization__, which the combined value of all its shares, and the __date of the latest update__. 

![img](images/m01_02.png)
you will be using an Excel workbook with 3 worksheets containing listing information for 3 exchanges: the AmEx Exchange and the NASDAQ that you are already familiar with, and also the NYSE. Each sheet contains the same information as the AMEX CSV file you have seen before

- NYSE (New York Stock Exchange): (New York City, Wall Street) The largest and oldest stock exchange in the U.S. Focus on Big, established companies, often "blue-chip" companies like Coca-Cola, IBM, and Walmart. 	Floor + electronic
- NASDAQ (National Association of Securities Dealers Automated Quotations):	The second-largest U.S. stock exchange, and it was the first electronic stock market. Focus on	tech-heavy and growth companies like Apple, Microsoft, Amazon, and Google.	Fully electronic. 
- AmEx (NYSE American): American Stock Exchange is Historically the third-largest U.S. stock exchange, known for trading smaller, emerging companies and alternative investments (like ETFs and options). It was bought by NYSE Euronext in 2008 and is now part of the NYSE American exchange. 	Focus on small-cap stocks, ETFs, options.	Electronic. AmEx played a big role in developing ETFs (Exchange-Traded Funds).
All locate in New York City.
