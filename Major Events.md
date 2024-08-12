### **Major events supported by TimeDataSeries**
TimeDataSeries supports several events that enable users to bind their callback functions to important alerts and updates.


### OnBarDataCompletedEvent

This event occurs when a bar data is completed in the TimeDataSeries. It allows users to handle the completion of each bar and perform necessary actions. Below is an example demonstrating how to specify and handle the **OnBarDataCompletedEvent**:
```
TimeDataSeries tds = LoadTimeDataSeries();

tds.OnBarDataCompletedEvent += TDSOnBarDataCompletedEvent;

private void TDSOnBarDataCompletedEvent(Guid id, IBarData barData, int currentBarIndex)
{
    // Handle bar data completion
    ……..
}
```

### **OnTickUpdateEvent**

This event triggers when there is a tick data update in the TimeDataSeries. It is useful for real-time monitoring of tick price updates and allows users to respond accordingly. The last bar in the updated TimeDataSeries continues to develop until the bar is completed. Here's an example illustrating how to specify and handle the **OnTickUpdateEvent**:

```
TimeDataSeries tds = LoadTimeDataSeries();
tds.OnTickUpdateEvent += TDSOnTickUpdateEvent;

private void TDSOnTickUpdateEvent(Guid id, IBarData barData, int currentBarIndex)
{
    // Handle tick data update
    ……..
}
```

### **OnFirstTickOfNewBar**

This event occurs when a new tick price update is received in the TimeDataSeries, indicating the beginning of a new bar. Similar to OnTickUpdateEvent, the last bar in the TimeDataSeries continues to develop until it is completed. Below is an example demonstrating how to specify and handle the **OnFirstTickOfNewBar** event:

```
TimeDataSeries tds = LoadTimeDataSeries();

tds.OnFirstTickOfNewBar += TDSOnFirstTickOfNewBar;

private void TDSOnFirstTickOfNewBar(Guid id, IBarData barData)
{
    // Handle first tick of a new bar
    ……
}    
```
Example of creating TimeDataSeries from csv file comprise of LTP data

The code below shows how TimeDataSeries structure can be created from csv file containing data point in below format of a particular instrument say BankNifty:

```
2023-12-08 09:15:00,30339.95,25
2023-12-08 09:15:00,30320.00,25
2023-12-08 09:15:00,30301.30,25
2023-12-08 09:15:00,30339.75,50
2023-12-08 09:15:00,30339.75,25
2023-12-08 09:15:00,30339.80,25
2023-12-08 09:15:00,30339.80,25
2023-12-08 09:15:00,30339.80,25
2023-12-08 09:15:00,30339.80,25
2023-12-08 09:15:00,30339.85,25
2023-12-08 09:15:00,30339.85,25
2023-12-08 09:15:00,30339.90,25
2023-12-08 09:15:00,30339.90,25
2023-12-08 09:15:00,30340.00,25
………..
………..
int compressionSecond = 60; // 1 Minute Bar

string fileName = @" BankNiftyTBTLTP.csv";
System.IO.StreamReader fileSR = new System.IO.StreamReader(fileName);

TimeDataSeries tdSeries = new TimeDataSeries(BarCompression.MinuteBar,
                compressionSecond,
                BarType.CandleStick,
                new TimeSpan(9, 15, 0),   // Session Start time
                new TimeSpan(15, 30, 0));   // Session End time);
tdSeries.OnBarDataCompletedEvent += OnBarDataCompletedEvent;

CultureInfo cultureInfo = CultureInfo.InvariantCulture;

string fileLine;
while ((fileLine = fileSR.ReadLine()) != null)
 {
    // 2020-12-08 09:15:00,30339.95,25

    string[] splittedString = fileLine.Split(',');
            
    DateTime ltt = DateTime.ParseExact(splittedString[0], "yyyy-MM-dd hh:mm:ss", cultureInfo);
    double ltp = Double.Parse(splittedString[1]);
    uint ltq = uint.Parse(splittedString[2]);

    tdSeries.AddTickData(ltt, ltp, ltq);
}


………………….
………………….

// This event will be called on completion of bar based on defined compression time
private void OnBarDataCompletedEvent(Guid id, IBarData barData, int currentBarIndex)
{
      System.Console.WriteLine("OnNewBar: {0}|{1}|{2}|{3}|{4}|{5}|{6}",
                            barData.DateTime,
                            barData.Open,
                            barData.High,
                            barData.Low,
                            barData.Close,
                            barData.Volume,
                            barData.OpenInterest);
}
```

The **AddBarData** function essentially serves as a mechanism for importing historical bar data into the TimeDataSeries object. When historical market data is stored in OHLCV bar format, often with a standard compression interval of 1 minute, developers can leverage this function to seamlessly integrate these bars into the TimeDataSeries object. Each bar encapsulates essential OHLCV (Open, High, Low, Close, Volume) data points, providing a comprehensive snapshot of price action within a specific time interval.

The TimeDataSeries is a primitive class which streamlines the process of integrating historical market data into the analysis workflow, enabling informed decision-making and strategy development based on reliable historical insights.

### Example to manage daily, weekly and monthly TimeDataSeries compression.

Daily bar has one BarData per day trading and it is always recommended to load the series from some reliable daily data source i.e a bhavcopy data file of NSE.  It is also recommended to create any Weekly, monthly compression only from daily bar source file 

#### // To get base timedata series for daily bar compression
TimeDataSeries tdSeries = TimeDataSeries.GetDailyBarCompression();

#### // To get base timedata series for weekly bar compression
TimeDataSeries tdSeries = TimeDataSeries.GetweeklyBarCompression();

#### // To get base timedata series for monthly bar compression
TimeDataSeries tdSeries = TimeDataSeries.GetMonthlyBarCompression();


File  HDFCBANK_EODDData.csv containing a daily bar data point
  
Date,Open,High,Low,Close,Volume
20041220,48.3,49.5,48.3,49.24,3818170
20041221,49.5,49.7,49.06,49.62,4792920
20041222,49.8,52,48.7,49.32,4457640
20041223,49.99,50,48.8,49.41,2829850
20041224,50,50.5,49.7,50.24,2825330
20041227,50.5,50.7,49.72,50.49,1588660
20041228,50.5,52,50.5,51.87,2533890
20041229,52.01,52.4,51.01,51.51,1523230

```

TimeDataSeries tdSeries = TimeDataSeries.GetDailyBarCompression();

string fileName = @" HDFCBANK_EODDData.csv";
System.IO.StreamReader fileSR = new System.IO.StreamReader(fileName);

// Files contain a daily data

//Date,Open,High,Low,Close,Volume
//20041220,48.3,49.5,48.3,49.24,3818170
//20041221,49.5,49.7,49.06,49.62,4792920
//20041222,49.8,52,48.7,49.32,4457640
//20041223,49.99,50,48.8,49.41,2829850

CultureInfo cultureInfo = CultureInfo.InvariantCulture;
string fileLine;
int counter = 0;
while ((fileLine = fileSR.ReadLine()) != null)
{
                if (counter == 0)
                {
                   //  Skip a first header line
                    counter++;
                    continue;
                }

                string[] splittedString = fileLine.Split(',');

                string dt = splittedString[0].Substring(0, 8);  // Only date part

                DateTime dtBar = DateTime.ParseExact(dt, "yyyyMMdd", cultureInfo);
                double o = Double.Parse(splittedString[1]);
                double h = Double.Parse(splittedString[2]);
                double l = Double.Parse(splittedString[3]);
                double c = Double.Parse(splittedString[4]);
                uint volume = uint.Parse(splittedString[5]);

                tdSeries.AddBarData(dtBar, o, h, l, c, volume);
}
```

### How to Convert TimeDataSeries to any other higher bar Compression?

Converting a TimeDataSeries to a different bar compression interval offers flexibility in analyzing market data at various time resolutions. TimeDataSeries enables users to specify their desired compression interval, ensuring data consistency and integrity when converting to the target compression.

For instance, you can convert a 15-minute compression TimeDataSeries from any Bar Data Series with a compression interval of 1 minute, 3 minutes, or 5 minutes, provided that the lower compression Bar Data is an absolute divisible of the target compression. This conversion process maintains data accuracy and preserves the structure of the original TimeDataSeries while adapting it to the new compression interval.

```
TimeDataSeries tds1Minute = Load1MinuteTimeSeriesDataFromCSV();

//INDIAVIX,2021/01/01,09:15,21.09,21.33,21.09,21.23,0,0
//INDIAVIX,2021/01/01,09:16,21.23,21.27,21.18,21.18,0,0
//INDIAVIX,2021/01/01,09:17,21.18,21.19,21.1,21.14,0,0
//INDIAVIX,2021/01/01,09:18,21.13,21.22,21.09,21.19,0,0
//INDIAVIX,2021/01/01,09:29,21.19,21.21,21.13,21.13,0,0


// Create 5 Minute TimeData Series object and fill all 1 Min Bar Data to it.
// 300 value represent 300 seconds which is equal to 5 Minute
TimeDataSeries timeDataSeries5Min = TimeDataSeries.GetMinutesBarCompression(300, BarType.CandleStick);
foreach (IBarData barData in tds1Minute)
{
    timeDataSeries5Min.AddBarData(barData.DateTimeDT, barData.Open, barData.High, barData.Low, barData.Close, barData.Volume);
}

// The timeDataSeries5Min time data series is now hold compressed data in 5 minute timeframe
//  with bar time stamps move as  09:15, 09:20, 09:25.....
```

This code snippet demonstrates how to convert a TimeDataSeries with 1-minute compression to a new TimeDataSeries with a 5-minute compression. It iterates through each bar in the 1-minute series and adds them to the 5-minute series, resulting in compressed data with bar timestamps at 5-minute intervals.

### How to load TimeDataSeries data points from csv file?
Using QX.FinLib.CSV component it is easy to load csv data file into TimeDataSeries object 

Add a reference of QX.FinLib.CSV.dll in your project from installation folder and include the relevant namespace reference in your code project.

The class CSVOHLCLoader is used to read the csv file representing a content of TimeDataSeries

Lets we have a csv file having following content and we use CSVOHLCLoader class to initialize TimeDataSeries using  some custom data formatter.

InstrumentName,TimeStamp,Open,High,Low,Close,Volume,OI
NIFTY01APR208750PE,27-03-2020 09:15,324.45,329.45,324.45,329.2,675,0
NIFTY01APR208750PE,27-03-2020 09:16,329.2,329.2,315,315,300,375
NIFTY01APR208750PE,27-03-2020 09:17,315,315,300,300,150,300
……
……

```
using QX.FinLib.CSV;

………

CSVOHLCLoader csvOHLCLoader = new CSVOHLCLoader(fileName,
                                                                BarDataFileFormat.Symbol_DateTime_O_H_L_C_V,
                                                                DateFormat.Custom,
                                                                DateSeparator.Dash,
                                                                "dd-MM-yyyy HH:mm",
                                                                true);
foreach (IBarData barData in csvOHLCLoader)
{
    timeDataSeries.AddBarData(barData.DateTimeDT, barData.Open, barData.High, barData.Low, barData.Close, barData.Volume);
}

// The last argument true indicate that a file contains a header
```

Following are some more example of other csv file format:


Date/Time,Open,High,Low,Close
01-01-2019 09:15,10925,10927.9,10910,10912
01-01-2019 09:16,10912.9,10916.8,10911.55,10912.7
01-01-2019 09:17,10911.35,10911.55,10910,10910.9
01-01-2019 09:18,10910.9,10910.9,10895.7,10896.3
01-01-2019 09:19,10896,10896,10872.15,10872.15
// Compressing 1 minute data points into a 5 minute
int compressionSecond = 300;
TimeDataSeries timeDataSeries = new TimeDataSeries(BarCompression.MinuteBar,
                                                               compressionSecond,
                                                               BarType.CandleStick,
                                                               new TimeSpan(9, 15, 0),     // Session Start time
                                                               new TimeSpan(15, 30, 0));   // Session End time);
CSVOHLCLoader csvOHLCLoader = new CSVOHLCLoader(fileName,
                                                               BarDataFileFormat.DateTime_O_H_L_C_V,
                                                               DateFormat.Custom,
                                                               DateSeparator.Dash,  
                                                               "dd-MM-yyyy HH:mm",
                                                              true);
// The last argument true indicates that csv file has a header text


Date,Open,High,Low,Close,Volume,rsi7,ema20,ema50,SIGNAL,,,,
08-06-2020 09:29,196.4,197.7,193.5,194.8,35271000,,,,,,,,
08-06-2020 09:44,194.85,195.8,194.45,195,8502000,,,,,,,,
|08-06-2020 09:59,194.95,195.55,193.55,193.65,10893000,,,,,,,,
08-06-2020 10:14,193.6,193.6,191.9,193.5,11385000,,,,,,,,
08-06-2020 10:29,193.55,194.15,192.85,193.8,4620000,,,,,,,,

CSVOHLCLoader csvOHLCLoader = new CSVOHLCLoader(fileName,                                                              
                                                               BarDataFileFormat.DateTime_O_H_L_C_V,
                                                                DateFormat.Day_Month_Year,                                                                                
                                                                DateSeparator.Dash,
                                                                "",        
                                                               true);

Example of a daily bar loading from csv file
//Date,Open,High,Low,Close,Adj Close,Volume
2018-01-02,42.540001,43.075001,42.314999,43.064999,41.31007,102223600
2018-01-03,43.1325,43.637501,42.990002,43.057499,41.302879,118071600
2018-01-05,43.360001,43.842499,43.262501,43.75,41.967163,94640000
2018-01-08,43.587502,43.9025,43.482498,43.587502,41.811283,82271200


TimeDataSeries timeDataSeries = TimeDataSeries.GetDailyBarCompression();
string fileName = @"D:\AAPL.csv";

CSVOHLCLoader csvOHLCLoader = new CSVOHLCLoader(fileName,                                                                         
                                                               BarDataFileFormat.DateTime_O_H_L_C_V,
                                                                DateFormat.Custom,
                                                                DateSeparator.Dash,
                                                                "yyyy-MM-dd",
                                                                true);
foreach (IBarData barData in csvOHLCLoader)
{
    timeDataseries.AddBarData(barData.DateTimeDT, 
                                                barData.Open, barData.High, barData.Low, barData.Close, barData.Volume);
}

Various examples above provides you an insight that it is easy to initialize and load the TimeaDataSeries of any compression from csv file

If the csv file contains datetime in multiple format, we can use a method  AddMoreCustomDateTimeFormatter to provide a relevant hint to correctly parse datetime column in the csv file.

Ticker,Date,Time,Open,High,Low,Close,Volume,Open Interest
ACC30JAN201360PE,01-01-2020,11:10:59,8.5,8.5,8.5,8.5,2000,7200
ACC30JAN201360PE,01-01-2020,11:11:59,8.5,8.6,8.5,8.6,400,6800

CSVOHLCLoader csvOHLCLoader = new CSVOHLCLoader(file,
                                                                        BarDataFileFormat.Symbol_Date_Time_O_H_L_C_V,
                                                                        DateFormat.Custom,
                                                                        DateSeparator.Slash,
                                                                       "dd-MM-yyyy HH:mm:ss",
                                                                        true);

csvOHLCLoader.AddMoreCustomDateTimeFormatter("dd/MM/yyyy HH:mm:ss");
csvOHLCLoader.AddMoreCustomDateTimeFormatter("dd/MM/yyyy HH:mm");
csvOHLCLoader.AddMoreCustomDateTimeFormatter("dd-MM-yyyy HH:mm");


foreach (IBarData barData in csvOHLCLoader)
{
    symbolName = csvOHLCLoader.GetValueAtColumnIndex(0);
    barDT = barData.DateTimeDT.AddSeconds(-barData.DateTimeDT.Second);
    ……
    ……
}

Following Data file contains some custom columns says Expiry with possible value I, II, III indicating current, next and far month expiry. To parse the file using CSVLoader, we need to provide a hint using a method PriceIndex indicating from which column index a price data starts

//SYMBOL,DATETIME,EXPIRY,OPEN,HIGH,LOW,CLOSE,VOLUME,OPEN_INTEREST
//BANKNIFTY,2022-08-01 09:15:59,I,37647.85,37721.95,37616.9,37698.6,107925,1972950
//BANKNIFTY,2022-08-01 09:16:59,I,37695.2,37702.0,37676.75,37686.1,44000,1972950
//BANKNIFTY,2022-08-01 09:17:59,I,37687.0,37687.0,37602.05,37611.05,34375,1973825
//BANKNIFTY,2022-08-01 09:18:59,I,37613.0,37615.0,37567.25,37600.1,21225,1973825

string fileName = @"D: \Future_Data\2022\8\BN_01082022.csv";
CSVOHLCLoader csvOHLCLoader = new CSVOHLCLoader(fileName,
          BarDataFileFormat.Symbol_DateTime_O_H_L_C_V_O,
          DateFormat.Custom,
          DateSeparator.Slash,
          "yyyy-MM-dd HH:mm:ss",
          true);

csvOHLCLoader.AddMoreCustomDateTimeFormatter("yyyy-MM-dd HH:mm:ss");
csvOHLCLoader.AddMoreCustomDateTimeFormatter("yyyy-MM-dd HH:mm");
csvOHLCLoader.AddMoreCustomDateTimeFormatter("yyyy/MM/dd HH:mm:ss");
csvOHLCLoader.PriceIndex = 3;
int rowCount = 0;
            
foreach (IBarData barData in csvOHLCLoader)|
{
    string col1 = csvOHLCLoader.GetValueAtColumnIndex(0);  
    string col2 = csvOHLCLoader.GetValueAtColumnIndex(1);
    string col3 = csvOHLCLoader.GetValueAtColumnIndex(2);
    string col4 = csvOHLCLoader.GetValueAtColumnIndex(3);
    string col5 = csvOHLCLoader.GetValueAtColumnIndex(4);
    rowCount++;
  }

#### Other important methods
| | |
| :--- | :--- |
| int FieldCount | Return the number of columns in csv file |
| int PriceIndex | User can set a starting index of price column usually an open price index as an hint to csv parser |
| string GetValueAtColumnIndex | Returns the column value |
| bool HasError  |	Return true if csv file parsing failed |
| string LastError	| Returns error text of failed reason |
| int IgnoreMaxExceptionCount |	If user set a value say 10, the csv parser ignore the maximum 10 exception during a data row parsing |
| Int IgnoreTopLines | If user set a value say 5, the csv parser will not parse the top 5 data lines and ignore it. |
| AddMoreCustomDateTimeFormatter("dd/MM/yyyy H:mm:ss") | additional datetime formatter hint to csv parser |

### How to find candle pattern in TimeDataSeries?

Detecting candlestick patterns in a TimeDataSeries involves analyzing the sequence of price action to identify specific chart patterns, such as Doji, Hammer, and many others. These patterns often signal potential changes in market sentiment or trends, providing valuable insights for traders and analysts.

To find candlestick patterns in a TimeDataSeries, developers can leverage built-in pattern recognition algorithms within QXFinLib. These algorithms are designed to detect various common candlestick patterns automatically from the historical price data stored in the TimeDataSeries instance. These algorithms analyze the open, high, low, and close prices of each candlestick in the TimeDataSeries to identify specific patterns based on predefined criteria. Some common pattern name are Doji, Hammer, Shooting Star, Engulfing Patter etc.

# Image

The candlestick based pattern are defined as enumerated value and represented by enum CandlePatternType . There are more than 50+ such pre build pattern defined to be used.

User can themselves write such pattern finding logic in their code:

```

TimeDataSeries tds = TimeDataSeries.GetDailyBarCompression();

……
……

//Maximum High Yesterday - (MHY)
//Yesterday is the maximum High in the last 5 days */
//MHY = HHV(H, 5) == Ref(H, -1);

if (barIndex >= 5 &&  tds.HHV('H', barIndex, 5) == tds [barIndex - 1].High)
{
    // MHY patern found at barindex
}



//Hammer = (((H-L)>3*(O-C)) AND ((C-L)/(0.001+H-L)>0.6) AND ((O-L)/(0.001+H-L)>0.6)); 
if( (((tds[barIndex].High - tds[barIndex].Low) > 3 * (tds[barIndex].Open - tds[barIndex].Close)) &&
                ((tds[barIndex].Close - tds[barIndex].Low) / (0.001 + tds[barIndex].High - tds[barIndex].Low) > 0.6) &&
                ((tds[barIndex].Open - tds[barIndex].Low) / (0.001 + tds[barIndex].High - tds[barIndex].Low) > 0.6))
{
    // Hammer patter found at bar index
}
```

### How to use a BarSince function to evaluate the number of bar passed since certain condition is true?

Barsince function calculates the number of bar passed since user defined expression is true at N occurrence from the latest bar.

```
[TestCase]
public void TestBarSince()
{
            int compressionSecond = 60;

            TimeDataSeries tdSeries = new TimeDataSeries(BarCompression.MinuteBar,
                 compressionSecond,
                 BarType.CandleStick,
                 new TimeSpan(9, 15, 0),   // Session Start time
                 new TimeSpan(15, 30, 0));  // Session End time);

            CultureInfo provider = CultureInfo.InvariantCulture;

            tdSeries.AddBarData(DateTime.ParseExact("2020-12-08 09:15", "yyyy-MM-dd hh:mm", provider), 64.51, 66.2, 63.4, 64.06, 6522690);
            tdSeries.AddBarData(DateTime.ParseExact("2020-12-08 09:16", "yyyy-MM-dd hh:mm", provider), 64.4, 65.4, 64.22, 65.18, 1537450);
            tdSeries.AddBarData(DateTime.ParseExact("2020-12-08 09:17", "yyyy-MM-dd hh:mm", provider), 65.55, 66.9, 65.5, 66.39, 2226590);
            tdSeries.AddBarData(DateTime.ParseExact("2020-12-08 09:18", "yyyy-MM-dd hh:mm", provider), 66.3, 67, 64.82, 65.81, 3300020);
            tdSeries.AddBarData(DateTime.ParseExact("2020-12-08 09:19", "yyyy-MM-dd hh:mm", provider), 65.8, 65.8, 63.7, 64.29, 3490020);
            tdSeries.AddBarData(DateTime.ParseExact("2020-12-08 09:20", "yyyy-MM-dd hh:mm", provider), 64.29, 65.5, 62.9, 64.52, 6839700);
            tdSeries.AddBarData(DateTime.ParseExact("2020-12-08 09:21", "yyyy-MM-dd hh:mm", provider), 64.5, 64.5, 62.71, 63.06, 2503180);
            tdSeries.AddBarData(DateTime.ParseExact("2020-12-08 09:22", "yyyy-MM-dd hh:mm", provider), 63.3, 63.8, 62.4, 62.78, 5076050);
            tdSeries.AddBarData(DateTime.ParseExact("2020-12-08 09:23", "yyyy-MM-dd hh:mm", provider), 63.5, 64, 63.14, 63.97, 2455090);
            tdSeries.AddBarData(DateTime.ParseExact("2020-12-08 09:24", "yyyy-MM-dd hh:mm", provider), 63.3, 67.5, 63.65, 64.14, 2777350);
            tdSeries.AddBarData(DateTime.ParseExact("2020-12-08 09:25", "yyyy-MM-dd hh:mm", provider), 64.49, 64.89, 61.22, 64.16, 804090);

            Assert.AreEqual(tdSeries.Count, 11);

           // Func<EvaluateFuncATIndex, ReturnMatchedBool>

            // 65.81 is close price at barIndex 3
            Func<int, bool> Evaluate = (barIndex) => tdSeries[barIndex].Close == 65.81;

            
            int barIndexMatched = tdSeries.BarSince(Evaluate, 1);    // 1 means break at first occurrence
            Assert.AreEqual(barIndexMatched, 3);

            // 63.3 open price second occurance is at 7th bar Index
            Evaluate = (barIndex) => tdSeries[barIndex].Open == 63.3;
            barIndexMatched = tdSeries.BarSince(Evaluate, 2);
            Assert.AreEqual(barIndexMatched, 7);

           
}
```
### How to use a ValueWhen function to extract a bar price at user defined matching condition?

The TimeDataSeries, **ValueWhen** function is a powerful is used to extract the value of a specified price component of a bar when a user-defined condition is met. The **ValueWhen** function is designed to search through the TimeDataSeries dataset, examining each bar until it encounters the first instance where the user-specified condition is met. Once this condition is satisfied, the function retrieves the value of the specified price component for that particular bar.

The condition is specified in terms of predicate function that must be satisfied for the value extraction.
```
[TestCase]
public void TestBarValueWhen()
{
            int compressionSecond = 60;

            TimeDataSeries tdSeries = new TimeDataSeries(BarCompression.MinuteBar,
                 compressionSecond,
                 BarType.CandleStick,
                 new TimeSpan(9, 15, 0),   // Session Start time
                 new TimeSpan(15, 30, 0));  // Session End time);

            CultureInfo provider = CultureInfo.InvariantCulture;

            tdSeries.AddBarData(DateTime.ParseExact("2020-12-08 09:15", "yyyy-MM-dd hh:mm", provider), 64.51, 66.2, 63.4, 64.06, 6522690);
            tdSeries.AddBarData(DateTime.ParseExact("2020-12-08 09:16", "yyyy-MM-dd hh:mm", provider), 64.4, 65.4, 64.22, 65.18, 1537450);
            tdSeries.AddBarData(DateTime.ParseExact("2020-12-08 09:17", "yyyy-MM-dd hh:mm", provider), 65.55, 66.9, 65.5, 66.39, 2226590);
            tdSeries.AddBarData(DateTime.ParseExact("2020-12-08 09:18", "yyyy-MM-dd hh:mm", provider), 66.3, 67, 64.82, 65.81, 3300020);
            tdSeries.AddBarData(DateTime.ParseExact("2020-12-08 09:19", "yyyy-MM-dd hh:mm", provider), 65.8, 65.8, 63.7, 64.29, 3490020);
            tdSeries.AddBarData(DateTime.ParseExact("2020-12-08 09:20", "yyyy-MM-dd hh:mm", provider), 64.29, 65.5, 62.9, 64.52, 6839700);
            tdSeries.AddBarData(DateTime.ParseExact("2020-12-08 09:21", "yyyy-MM-dd hh:mm", provider), 64.5, 64.5, 62.71, 63.06, 2503180);
            tdSeries.AddBarData(DateTime.ParseExact("2020-12-08 09:22", "yyyy-MM-dd hh:mm", provider), 63.3, 63.8, 62.4, 62.78, 5076050);
            tdSeries.AddBarData(DateTime.ParseExact("2020-12-08 09:23", "yyyy-MM-dd hh:mm", provider), 63.5, 64, 63.14, 63.97, 2455090);
            tdSeries.AddBarData(DateTime.ParseExact("2020-12-08 09:24", "yyyy-MM-dd hh:mm", provider), 63.3, 67.5, 63.65, 64.14, 2777350);
            tdSeries.AddBarData(DateTime.ParseExact("2020-12-08 09:25", "yyyy-MM-dd hh:mm", provider), 64.49, 64.89, 61.22, 64.16, 804090);

            Assert.AreEqual(tdSeries.Count, 11);

            // Func<EvaluateFuncATIndex, ReturnMatchedBool>

            // 65.81 is close price at barIndex 3
            Func<int, bool> Evaluate = (barIndex) => tdSeries.TimeNum(barIndex) == 091600;
            double closePrice = tdSeries.ValueWhen(Evaluate, 'C', 1);
            Assert.AreEqual(closePrice, 65.18);

} 
```

There are other important function i.e **LowestSince** and **HighestSince** used to evaluation a bar price when latest HHV or LLV occurrence condition meet.

**LowestSince Functionality:**
- The function iterates backward through the bars of the TimeDataSeries, starting from the most recent bar
- At each bar index, it evaluates the user-defined expression to determine if the condition is met.
- If the condition is met, the function increments the occurance counter.
- The function continues iterating until it reaches the specified occurrence (nthOccuranceTimes) of the user-
  defined expression.
- During iteration, it tracks the lowest value encountered for the specified price type (priceType).
- Once the specified occurrence is reached, the function returns the lowest value encountered since that occurrence.

The **HighestSince** function operates similarly to **LowestSince**, but instead of returning the lowest value, it returns the highest value of a specified price type since the occurrence of a user-defined expression.

### Lookup Method
Developers can use the Lookup function to retrieve specific bar data from the TimeDataSeries based on a datetime or timenum of interest. The function searches through the TimeDataSeries to find the bar that matches the specified datetime or time representation (timeNum)..

public IBarData Lookup(DateTime dateTime)

public IBarData Lookup(int timeNum)

```
int targetTime = 1315; // Example target time representation for 13:15 PM
IBarData targetBar = tdSeries.Lookup(targetTime);
```
```
DateTime targetDateTime = new DateTime(2024, 03, 30, 09, 35, 0); // Example target datetime
IBarData targetBar = tdSeries.Lookup(targetDateTime);
```

### Date and Time based function of TimeDataSeries
| | |
| :--- | :--- |
| TimeNum | Return at given bar index the Time value in format HHmmss
If time at bar index 10 is 11:15:00, the value it return is 111500 int timeNum = tdSeries.TimeNum(10); |
| DayOfWeekNum | Returns the array with day of week (0-6): 0 - Sunday 1 - Monday 5 - Friday 6- Saturday |
| DayOfMonth | Return the day part |

The utility class QX.FinLib.Common.Util.DateTimeUtil extended some more useful function on DateTime structure.

### General Utility method supported in TimeDataSeries

| |
| :--- |
| int TimeNum(int barIndex) - Returns the time of the bar at the specified index in the format HHmmss (hours, minutes, seconds). I,e. 091500 |
| int TimeNum(int hour, int minute, int second) - Converts the given time into the TimeNum number format |
| int DateNum(intbarIndex) - Returns the date of the bar at the specified index in the format yyyyMMdd (year, month, day) i.e. 20222801 |
| bool IsFirstBarOfDay() - Indicates whether the current bar index represents the first bar of the day |
| bool IsLastBarOfDay() - Indicates whether the current bar index represents the last bar of the day. |
| int DayOfWeekNum(int barIndex) - Returns the number representing the day of the week (Monday-1, ..., Sunday-0) for the specified bar index |
| int DayOfMonthNum(int barIndex) - TReturns the day of the month (1-31) for the specified bar index |
| bool IsGapUp() - Returns true if yesterday's high is less than today's open, indicating a gap up |
| bool IsGapDown() - Returns true if yesterday's low is greater than today's high, indicating a gap down. |
| bool IsInside() - Returns true if today's high is less than yesterday's high and today's low is greater than yesterday's low, indicating an inside day |
| IBarData GetDailyTimeFrameBar(int refBarIndex = 0, bool includeTodaysDate = true)
Returns daily bar data based on the reference index from the most recent bar index. |
| bool IsRising(int index) - Indicates whether the value at the given index is greater than the previous index value. |
| bool IsFalling(int barIndex) - Indicates whether the value at the given index is less than the previous index value. |
| Inidcates wheather the given index value is less than previous index value |
| double UpperShadow(int barIndex) - Returns the length of the upper shadow (wick) for the given bar index |
| public double LowerShadow(int barIndex) - Returns the length of the lower shadow (tail) for the given bar index. The long thin lines above and below the body represent the high/low range and are called “shadows” (also referred to as “wicks” and “tails”). |
| int BarsSince(Func<int, bool> evaluateFuncAtBarIndex, int nthOccuranceTimes = 1) - Returns the bar index tracking backward from the last bar until the nth occurrence of the specified evaluation function is satisfied. |
| public double HighestSince(Func<int, bool> evaluateFuncAtBarIndex, char priceType = 'C', int nthOccuranceTimes = 1)- Returns the highest value of the specified price type since the nth occurrence satisfies the user-defined expression. |
| double LowestSince(Func<int, bool> evaluateFuncAtBarIndex, char priceType = 'C', int nthOccuranceTimes = 1) - Returns the lowest value of the specified price type since the nth occurrence satisfies the user-defined expression. |
| double ValueWhen(Func<int, bool>evaluateFuncAtBarIndex, char priceType = 'C', int nthOccuranceTimes = 1) - 
Returns the price type at the bar when the user-defined condition is satisfied at the nth occurrence. |
| double LLV(int lengthPeriod), double LLV(char priceType, int lengthPeriod) - Returns the lowest low value over the preceding length periods |
| public double HHV(int lengthPeriod), double HHV(char priceType, int lengthPeriod) - Returns the highest high price over the preceding length periods. |
| double StdDev(int lengthPeriod) - Returns the standard deviation over the preceding length value. |
| double Sum(int lengthPeriod), double Sum(char priceType, int lengthPeriod)- Returns the sum of all preceding prices until the length period. |
| double Average(int lengthPeriod) - Returns the average of all preceding prices until the length period. |
| double WeightedAverage(int lengthPeriod) - Returns the weighted average of all preceding prices until the length period |
| double  TrueRange(int barIndex) - Measures the daily range plus any gap from the closing price of the preceding day |
| double TypicalPrice(int barIndex) - The Typical Price indicates an average of each day’s price.
Typical Price is calculated by adding the high, low, and closing prices together, and then dividing by three. |

### Indicator

This guide explores how to leverage QXFinLib, a powerful library, to create your own technical indicators for use in quantitative analysis and algorithmic trading.


A technical indicators is a mathematical calculation usually derived from a stock’s historical price and volume. They are used to understand stock’s price movements in hopes of predicting future swings. Calculating technical indicators takes time away from the modelling process and can therefore be a deterrent to building more complex statistical models. With QXFinLib library quants can leverage a powerful framework which offers you to build customized technical indicators and mechanical trading system model. The library contains prebuilt 100+ indicator developed using same simple way you may use to develop your indicator as per your need.

The library contains prebuilt 100+ indicator developed using same simple way you may use to develop your indicator as per your need.

In QXFinLib technical indicator is function of TimeDataSeries which you have studied in previous sections.

#### Building Your Own Indicator with QXFinLib
QXFinLib streamlines the creation of custom technical indicators by providing a powerful framework. Here's a step-by-step approach:

Create a class that inherits from the IndicatorBase class provided by QXFinLib. This class will serve as the foundation for your custom indicator.

Your Indicator must represented by your custom Indicator class that inherits from IndicatorBase class provided by QXFinLib. 

Allow dynamic adjustments by defining input parameters within your class. These parameters influence the indicator's behavior. You can designate internal primitive data type variable  as input variables by decorating them with the Parameter attribute.

For example, the Simple Moving Average (SMA) indicator requires a lookback period to calculate the average value. You can designate internal values as input variables by decorating them with the Parameter attribute.

```
[Parameter("LengthPeriod", Description = "The length of the SMA", DefaultValue = 12)]
private int _lengthPeriod = 12;
```
The Parameter attribute takes a unique name identifier, a description, and a default value. The name identifier must be unique name among all the input variables within same indicator class.

### Implement override method CalculateOnBar

The CalculateOnBar method is responsible for calculating the values for each output variable based on the current bar index.

Override the CalculateOnBar method, which is called for every historical bar in the data series. It is essential to store the indicator values, typically numeric, in a DoubleSeries for each index of the TimeDataSeries

The CalculateOnBar passes the current index starting from 0 index till end of bar index or as new bar completed. The function has access of TimeDataSeries and all its BarData till given current index. The function should not try to access any bar value beyond current index.

The result series can be exposed to outside as Indicator output value by decorating the variable of type DoubleSeries with Output Attribute

```
[Output("SMA", DefaultValue = double.NaN, LineColor = "DarkOrange")]
private DoubleSeries _smaPriceDoubleValueSeries = new DoubleSeries();
```

#### Other optional method in your Indicator class

```
void Initialize()

This method is called once even before any call to CalculateOnBar and developer can initialize any state of the Strategy
```
```
override void ResetIndicator()

This method is called by framework in case Strategy imput variables are changed and all Indicatorvalue need to be reevalaued. Here developes can reset the Output collection or any state of Indicator
```

By following these steps, you can create custom indicators tailored to your specific trading strategies and analysis needs, leveraging the flexibility of QXFinLib. This approach empowers you to move beyond pre-built indicators and develop unique custom indicator that cater to your individual requirements.

**Following code snippet shows an implementation of Simple Moving Average(SMA) Indicator:**

```

using System;

using QX.FinLib.Data;

namespace QX.Indicators
{
    [IndicatorScaleAttribute(true)]
    [IndicatorAttribute("{E5889445-BA22-43A0-ADA6-FD463771EA26}", "SMA", "Simple Moving Average", "QXT")]
    public class SMA : IndicatorBase
    {
        [Parameter("LengthPeriod", Description = "The length of the SMA", DefaultValue = 12)]
        private int _lengthPeriod = 12;

        [Output("SMA", DefaultValue = double.NaN, LineColor = "DarkOrange")]
        private DoubleSeries _smaPriceDoubleValueSeries = new DoubleSeries();

       
        public SMA() : base(null)
        {
        }

        public SMA(TimeDataSeries timeDataSeries, int lengthPeriod)
            : base(timeDataSeries)
        {
            if (lengthPeriod <= 0)
                throw new ArgumentException("Invalid SMA length");

            _lengthPeriod = lengthPeriod;
            _smaPriceDoubleValueSeries = new DoubleSeries();
        }

        public SMA(TimeDataSeries timeDataSeries)
            : base(timeDataSeries)
        {
            _smaPriceDoubleValueSeries = new DoubleSeries();
        }

        public override IndicatorCategoryType GetIndicatorCategoryType()
        {
            return IndicatorCategoryType.Trend;
        }

        protected override void Initialize()
        {
            
        }


        /// <summary>
        /// Calculates the Indicator Function value at the index.
        /// </summary>
        /// <param name="index">The index at which Indicator Function value need to be calculated.</param>
        /// <returns>
        /// The calculated Indicator function value at the index.
        /// </returns>
        /// 

        protected override double CalculateOnBar(int index)
        {
            if (index < _lengthPeriod)
                return _smaPriceDoubleValueSeries[index] = double.NaN;
            else
            {
                return _smaPriceDoubleValueSeries[index] = CloseSeries.Average(_lengthPeriod);
            }

        }

        protected override void ResetIndicator()
        {
            _smaPriceDoubleValueSeries = new DoubleSeries();
        }
    }
}
```

#### Following are other important points for your indicator class: 
- The **IndicatorAttribute** decorator must be defined at class level indicating unique Guid identifier, the short name, the description and the owner of the indicator. The unique Guid can be created through a GuidGen tool.

[IndicatorAttribute("{E5889445-BA22-43A0-ADA6-FD463771EA26}", "SMA", "Simple Moving Average", "QXT")]

- The variable decorated as **Parameter** is used to provide a control to end user to change the value as Input parameter. The framework reset the value with the user provided value of any input parameters which state is used to refer during a indicator value. In case of SMA, user wishes to make a lookup length period as an Input parameter.

  [Parameter("LengthPeriod", Description = "The length of the SMA", DefaultValue = 12)]
  private int _lengthPeriod = 12;


- Implement your constructor to initialize necessary variables. The default constructor must be implemented.

- Define an **output variable** of type **DoubleSeries**. The calculateOnBar must fill a value of calculated indicator value at given index.
The SMA indicator has only output like SMA, you need not to define an output variable as Calculate method return type is preserve as Output variable.

   [Output("SMA", DefaultValue = double.NaN, LineColor = "DarkOrange")]
   private DoubleSeries _smaPriceDoubleValueSeries = new DoubleSeries();

- You may specify Double.NaN value for indicator index that has no valid value and may be used in charting to show a clear value.

-  **Initialize** method is called once and can be used to initialize some variables or any embedded indicator object.

- **ResetIndicator** method is called each time when recalculation is required due to change in input parameter value.

### How to implement Indicator having more than one output?
Following is Bollinger band indicator class defines three output variable:

```
using System;

using QX.FinLib.Common;

namespace QX.Indicators
{

    [IndicatorScaleAttribute(true)]
    [IndicatorAttribute("{08BFD754-A2C5-432A-9265-8A29DA132637}", "BB", "Bollinger Band", "QXT"]
    public class BollingerBand : IndicatorBase
    {
        [Parameter("LengthPeriod", Description = "The length of the BB", DefaultValue = 20)]
        private int _lengthPeriod = 20;

        [Output("BB", DefaultValue = double.NaN, LineColor = "DarkOrange")]
        private DoubleSeries _bbsDoubleValueSeries = new DoubleSeries();

        [Output("UpperBand", DefaultValue = double.NaN, LineColor = "DeepSkyBlue", LineStyle = LineDashStyle.DashDot, IsAreaFill=true)]
        private DoubleSeries _bbsUpperBandDoubleValueSeries = new DoubleSeries();

        [Output("LowerBand", DefaultValue = double.NaN, LineColor = "Tomato", LineStyle = LineDashStyle.DashDot, IsAreaFill= true)]
        private DoubleSeries _bbsLowerBandDoubleValueSeries = new DoubleSeries();
      
        public BollingerBand() : base(null)
        {
        }

        public BollingerBand(TimeDataSeries timeDataSeries,  int lengthPeriod)
            : base(timeDataSeries)
        {
            if (lengthPeriod <= 0)
                throw new ArgumentException("Invalid BB length");

            _lengthPeriod = lengthPeriod;
        }

        public BollingerBand(TimeDataSeries timeDataSeries)
            : base(timeDataSeries)
        {

        }
        public override IndicatorCategoryType GetIndicatorCategoryType()
        {
            return IndicatorCategoryType.Trend;
        }

        /// <summary>
        /// Calculates the Indicator Function value at the index.
        /// </summary>
        /// <param name="index">The index at which Indicator Function value need to be calculated.</param>
        /// <returns>
        /// The calculated Indicator function value at the index.
        /// </returns>
        protected override double CalculateOnBar(int index)
        {

            //Middle Band = Lenght Period simple moving average (SMA)
            //Upper Band = Length Period SMA + (Length Period standard deviation of price x 2)
            //Lower Band = Length Period SMA – (Length Period day standard deviation of price x 2)


            if (index<_lengthPeriod)  
            {
                _bbsDoubleValueSeries[index] = double.NaN;
                _bbsUpperBandDoubleValueSeries[index] = double.NaN;
                _bbsLowerBandDoubleValueSeries[index] = double.NaN;

            }
            else if (index >= _lengthPeriod)
            {
                double stdDev = TimeDataSeries.StdDev('C', _lengthPeriod, index)*2.0;
                _bbsDoubleValueSeries[index]= TimeDataSeries.Average('C', _lengthPeriod, index);
                _bbsUpperBandDoubleValueSeries[index] = _bbsDoubleValueSeries[index] + (stdDev);
                _bbsLowerBandDoubleValueSeries[index] = _bbsDoubleValueSeries[index] - (stdDev);
            }

            return _bbsDoubleValueSeries[index];
        }

        protected override void ResetIndicator()
        {
            _bbsLowerBandDoubleValueSeries = new DoubleSeries();
            _bbsUpperBandDoubleValueSeries = new DoubleSeries();
            _bbsDoubleValueSeries = new DoubleSeries();
        }
    }
}
```

### Example of using predefined indicator definition in any new Indicator implementation?

Following indicator code is implementation of WilderSMA using SMA predefined indicator:
```
    [IndicatorAttribute("{EE7A557B-BE4B-4B51-9E06-CA41E9CE319C}", "WSMA", "Wilders Simple moving avearge", "QXT")]
    public class WilderSMA : IndicatorBase
    {
        [Parameter("LengthPeriod", Description = "The length of the WSMA", DefaultValue = 12)]
        private int _lengthPeriod = 12;

        [Output("WSMA", DefaultValue = double.NaN, LineColor = "DarkOrange")]
        private DoubleSeries _wsmaDoubleValueSeries = new DoubleSeries();

        private DoubleSeries _sumDoubleValueSeries = new DoubleSeries();


        private IndicatorBase _sma;

      
        /// <summary>
        /// Initializes a new instance of the <see cref="WilderSMA"/> class.
        /// </summary>
        /// <param name="timeDataSeries">The TimeDataSeries.</param>
        /// <param name="lengthPeriod">The length period.</param>
        public WilderSMA(TimeDataSeries timeDataSeries, int lengthPeriod)
            : base(timeDataSeries)
        {
            if (lengthPeriod <= 0)
            {
                throw new ArgumentException("Invalid EMA length");
            }

            _lengthPeriod = lengthPeriod;

        }

        public WilderSMA(TimeDataSeries timeDataSeries)
            : base(timeDataSeries)
        {
            _wsmaDoubleValueSeries = new DoubleSeries();
        }

        public WilderSMA() 
            : base(null)
        {

        }

        public override IndicatorCategoryType GetIndicatorCategoryType()
        {
            return IndicatorCategoryType.General;
        }

        protected override void Initialize()
        {
            _sma = GetIndicator("SMA");
            _wsmaDoubleValueSeries = new DoubleSeries();
        }


        /// <summary>
        /// Calculates the Indicator Function value at the index.
        /// </summary>
        /// <param name="index">The index at which Indicator Function value need to be calculated.</param>
        /// <returns>
        /// The calculated Indicator function value at the index.
        /// </returns>
        protected override double CalculateOnBar(int index)
        {
            double previousWSMAValue = _wsmaDoubleValueSeries[index - 1];
    
            if (double.IsNaN(previousWSMAValue))
            {
                _wsmaDoubleValueSeries[index] = _sma[index];
                _sumDoubleValueSeries[index] = _sma[index] * _lengthPeriod;
            }
            else
            {
                _sumDoubleValueSeries[index] = _sumDoubleValueSeries[index - 1] - previousWSMAValue + InputSeries[index];
                _wsmaDoubleValueSeries[index] = _sumDoubleValueSeries[index] / _lengthPeriod;

            }

            return _wsmaDoubleValueSeries[index];
        }

        protected override void ResetIndicator()
        {
            _sma = new SMA(this.TimeDataSeries, _lengthPeriod);
            _wsmaDoubleValueSeries = new DoubleSeries();

        }

    }
}
```

#### Registering Indicator and how to use it in your application?

Indicator class can be used independently in your application but framework provides some systematic usage of it and before this it has to be registered. Following code snippet is used to register an indicator and retrieve an instance of it from system. 

```
Type indicatorType = typeof(BollingerBand);
IndicatorRegistrationManager.Instance.RegisterIndicator(indicatorType);

// TimeDataSeries must have created and initialized with data points
IndicatorBase indicator = IndicatorRegistrationManager.Instance.GetIndicator(indicaorName, _timeDataSeries);
if (indicator == null)
    return;


// Set an input value as per user choice
indicator.SetFieldValue("LengthPeriod", 20);

// Refresh internally makes a call of Calculate override method for all existing bar
// This call is optional
indicator.Refresh();

// Refer the value of Indicator at any bar
for (int i = 0; i < _ timeDataSeries.Count; i++)
{
    double upperBandBB = indicator["UpperBand ", i];
    double bb = indicator["BB ", i];
    double lowerBandBB = indicator["LowerBand ", i];
}
```
### What various details are accessed in Indicator Calculate method?

Following code snippet shows various functionality accessed in indicator Calculate method:
```

protected override double Calculate(int index)
{
    

   

    // Get a current bar Close price
    double close = Ref('C', 0);
    
    // Following are different ways to get a latest bar close price
    close = Ref('C', 0);
    close = BarValue('C', index);
    close = Close(index-1);
    close = TimeDataSeries[index].Close;

    // Get an previous bar close price
    double prevClose = Ref('C', -1);

    // Get an average of all close prices of last _lengthPeriod from latest bar
    double average = Average('C', _lengthPeriod, index);

    // Get a 2 std deviation of close prices
    double stddev = StdDev('C', _lengthPeriod, index) * 2.0;

    // Get a Highest high and Lowest Low value of last 10 bars
    double hhv = HHV(10);
    double llv = LLV(10);


    // Get a true range for specific bar index
    double tr = TrueRange(index);

    
    bool isRisingBar = IsRising(index);
    bool isFallingBar = IsFalling(index);

   // Get the current bar time in format HHmmss format i.e 13:40 bar time is represented as 134000
    Int timeNum = TimeNum();
    
    // Get the current bar date in format yyyyMMdd format i.e 1st June 22  bar is represented as 220601
    Int dateNum = DateNum()

    //BarSince method is useful function and return the Bar Index tracking backward from last bar till
    // nth occurance of Evaluate Function is satisfied
    Func<int, bool> Evaluate = (barIndex) => Close(barIndex) > Close(barIndex - 1);
    int barIndexMatched = BarSince(Evaluate, 1);

    // this return highest value of close since the second most recent occurrance of the high being above 50
    Func<int, bool> Evaluate2 = (barIndex) => High(barIndex) > 50;
    double highestVal = HighestSince(Evaluate, 'C', 2);

     // find a close price at bar index when recent bartime is 091600
     Func<int, bool> Evaluate3 = (barIndex) => TimeNum(barIndex) == 091600;
     double closePrice = ValueWhen(Evaluate, 'C', 1);
        
}
```

### What are various quick analytics method supported in TimeDataSeries and DoubleSeries?

If TimeDataSeries and DoubleSeries are directly accessed within an indicator and strategy framework following useful methods are provided.

Ref(“C’)
return the close price of current price index in series

Ref(‘H’, 5)
return High price of 5 bar ago. If no index is available then it return NaN

### Instrument

Instrument object represent a tradable contract such as Equity, Futures, Options.
In any trading framework, Instrument class is key to represent the important properties such as InstrumentID, Exchange InstrumentID, Exchange Segment, Symbol name, Lot Size, Minimum tick Size, Contract multiplier etc.
Every exchange or broker provides these details in structured file format or through an API called contract specifications or  contract master. QXFinLib defines these Instrument class structure to represent your contracts in object form to be used in trading models. The technical analysis functionality uses Instrument class to derive various statistics based on TickSize, multiplier and other properties of Instrument class.

namespace QX.FinLib.Instrument

InstrumentBase is a base class of all instrument types and is abstract class.

# Image

User can create specific Instrument type object depending on needs and register it with InstrumentManager singleton object 
These instrument types holds some additional properties relevant to them i.e OptionsInstrument has addition properties like StrikePrice, OptionsType(CE, PE, CA, PA)

Following are some important instrument properties and must be assigned while define Instrument object:

| | | |
| :--- | :--- | :--- |
| Properties | Data Type | Descriptions |
|InstrumentID |	ulong | It is unique identifier across all instrument in system and it is automatically assigned based on two subkeys ExchangeSegment and Exchange InstrumentID i.e ExchangeSegment is NSECM(1) and exchange token id is 17903. The system assign InstrumentID a value 100000017903. |
| ExchangeSegment |	Enumerated value | NSECM = 1 NSEFO = 2 NSECD = 3, etc |
| ExchangeInstrumentID | uint | The unique identifier provided by exchange for instrument |
| InstrumentType | Enumerated value | Futures =1 Options = 2 Spread = 4 Equity = 8, etc |
| Symbol | string | Example: Reliance, SBIN, ACC, APPL, EUR/USD |
| InstrumentName | string | For Equity segment it is usually same as Symbol name and for FO segment the name is in format BANKBARODA 25MAR21 FUT BANKNIFTY 18FEB21 PE 27700 |
| InstrumentDescription | string | User can provide any descriptive name i.e BANKBARODA21MARFUT, IGL21FEB760PE, RELIANCE INDUSTRIES LTD |
| LowLimit | double	| In some cases, exchanges provides a Low limit price below which order cannot be submitted. |
| HighLimit | double | In some cases, exchanges provides a High limit price above which order cannot be submitted. |
| QtyMultiplier | int | The quantity multiplier is used to get a traded value. In most cases it is 1 but some contracts are quoted at low denomination but traded value is higher, i.e MCX gold quoted price is 10 gram but traded unit is 1 kg. In this case the multiplier is 100. |
| LotSize | | This is minimum shares or contract buy in one transaction i.e Nifty has lot size of 75. You can buy or sell in multiple of 75 |
| MinTickSize |  | Every exchange defines a minimum price movement of a tradable contract. In case of NSECM and NSEFO all major contracts have a minimum price movement also called tick size value as 0.05. For emini S&P 500 the tick size is 0.25 |
| BigPointValue |  | In some case quotes value is different than monetary value of a contract. In most cases the BPP is 1. For For emini S&P 500 the BPP is $12.50 per tick movement. This value is used to derive a contract value in monetary terms. |
| PriceFormat |	Enemerated value | How price can be formatted and represented. Default value is DECIMAL, .i.e DECIMAL THIRTYSECONDS HALFTHIRTYSECONDS QUARTERTHIRTYSECONDS etc. US treasury bond and futures are not traded in decimal price format but in fractional of par value |
| ISIN (EquityInstrument) | string | ISIN (International Securities Identification Number) is a unique identification number for a security across universe, i.e for RELIANCE the ISIN is INE002A01018 |
| ContractExpiration (FuturesInstrument, OptionsInstrument) | Datetime | The date on which contract expire. It is mainly a derivative property. |
| UnderlyingType (FuturesInstrument, OptionsInstrument)	| Enumerated Value | Assign a underline type. Default is General, General = 0 Commodity = 1 Currency = 2 Equity = 3 Index = 4 |
| OptionType (OptionsInstrument) | Enumerated Value | Valid for options contract, CA = 1  (Call American) PA = 2  (Put American) CE = 1  (Call European) CE = 1  (Put European) |
| Strike Price (OptionsInstrument) | double |	Valid for options contract, A strike price is the set price at which a derivative contract can be bought or sold when it is exercised. |

### InstrumentManager
This is singleton class and every instrument instance must be subscribe with Instrument manager. It is a container class for all subscribed instruments and provides access and filter criteria to identify individual or set of instruments meeting specific group creteria.

Every instrument instance must subscribe to InstrumrntManager as follows:

InstrumentManager.Instance.SubscribeInstrument(equityInstrument);

### How to create instrument class and subscribe it with instrument manager?
Here we are taking an example of NSE exchange which provides to brokers an instrument definition files for each segments. Following is example to decode relevant information from exchange provided contract master files and create a Instrument class and register it with **InstrumentManager**.

For equity segment NSE provides security.txt file holding contract master definition and following is code snippet on how to parse the file and identify important attributes to create EquityInstrument object instances and subscribe it with Instrument Manager.


InstrumentManager.Instance.SubscribeInstrument(equityInstrument);

```
string securityFileName = "security.txt";

if (!File.Exists(securityFileName))
    throw new Exception(securityFileName + " file does not exists.");

List<string> fileLines = new List<string>(File.ReadAllLines(securityFileName));
if (fileLines.Count <= 1)
    throw new Exception(securityFileName + " file has no record.");

fileLines.RemoveAt(0);

foreach (string fileLine in fileLines)
{
                string[] splittedString = fileLine.Split(Constant.PIPESEPERATOR);
                if (splittedString.Count() < 54)
                    continue;

                uint exchangeInstrumentID = Convert.ToUInt32(splittedString[0]);

                char deleteFlag = Convert.ToChar(splittedString[51]);
                if (deleteFlag == 'Y')
                {
                    continue;
                }

                string symbol = splittedString[1];
                string series = splittedString[2];                       // EQ
                if (series != "EQ")
                    continue;

                ulong instrumentID = ExchangeCommon.GenerateUniqueTokenID(ExchangeSegment.NSECM, exchangeInstrumentID);

                string highLowRange = splittedString[6];
                string[] highLowPriceTokens = highLowRange.Split(new char[] { '-' });

                int lowerPriceLimit = (int)(Convert.ToDouble(highLowPriceTokens[0]) * 100);
                int higherPriceLimit = (int)(Convert.ToDouble(highLowPriceTokens[1]) * 100);

                // The BoardLot qty is 1 for NSECM instruments
                int boardLotQty = Convert.ToInt32(splittedString[19]);

                // The TickSize is read as 5(Paisa) and interpreted as .05(INR)
                int tickSize = Convert.ToInt32(splittedString[20]);

                // The Tick Per point is evaluated as 20 if tickSize is .05
                int tpp = (int)(1.0 / tickSize);
                string instrumenDescription = splittedString[21];
                string isin = splittedString[53];

                EquityInstrument equityInstrument = new EquityInstrument(ExchangeSegment.NSECM,
                    exchangeInstrumentID,
                    symbol,
                    symbol,
                    instrumenDescription,
                    boardLotQty,
                    tpp);

                equityInstrument.ISIN = isin;
                equityInstrument.LowLimit = lowerPriceLimit * .01;
                equityInstrument.HighLimit = higherPriceLimit * .01;

                InstrumentManager.Instance.SubscribeInstrument(equityInstrument);

}
```

For Futures and Options segment NSE provides contract.txt file holding contract master definition and following is code snippet on how to parse the file and identify important attributes to create FuturesInstrument and optionsInstrument object instances and subscribe it with Instrument Manager.

```

string contractFileName = "contract.txt";

if (!File.Exists(contractFileName))
    throw new Exception(contractFileName + " file does not exists.");

List<string> fileLines = new List<string>(File.ReadAllLines(contractFileName));
if(fileLines.Count <= 1)
    throw new Exception(contractFileName + " file has no record.");

fileLines.RemoveAt(0);

foreach (string fileLine in fileLines)
{
                string[] splittedString = fileLine.Split(Constant.PIPESEPERATOR);
                if (splittedString.Count() < COLUMNCOUNT)
                    continue;

                uint exchangeInstrumentID = Convert.ToUInt32(splittedString[TOKEN_INDEX]);
                uint assetToken = Convert.ToUInt32(splittedString[ASSETTOKEN_INDEX]);
                string instrumenType = splittedString[INSTRUMENTTYPE_INDEX];                //  OPTIDX, FUTIDX, FUTSTK

                string symbol = splittedString[SYMBOL_INDEX];                                              // BANKNIFTY
                string series = splittedString[SERIES_INDEX];                                                 // XX

                string optionType = splittedString[OPTIONTYPE_INDEX];

                DateTime contractExpiration = ExchangeCommon.GetDateTimeFromBaseDateElapsedSeconds(
                             ExchangeSegment.NSEFO, (long)Convert.ToInt32(splittedString[EXPIRYDATE_INDEX]));

                double strikePrice = Convert.ToInt32(splittedString[STRIKEPRICE_INDEX]) / 
                                                                     ExchangeCommon.GetPriceDivisor(ExchangeSegment.NSEFO);

                bool isFutures = true;
                if (optionType == "XX" && strikePrice > 0)
                    continue;
  
                if (instrumenType.StartsWith("OPT") && strikePrice <= 0)
                    continue;

                
                OptionType optionTypeEnum = OptionType.NONE;
                if (optionType == "CE")
                {
                    optionTypeEnum = OptionType.CE;
                    isFutures = false;
                }
                else if (optionType == "PE")
                {
                    optionTypeEnum = OptionType.PE;
                    isFutures = false;
                }
                else if (optionType == "XX")
                    isFutures = true;
                else
                    continue;

                string category = splittedString[CATEGORY_INDEX];
                short caLevel = Convert.ToInt16(splittedString[CALEVEL_INDEX]);

                ulong instrumentID = ExchangeCommon.GenerateUniqueTokenID(ExchangeSegment.NSEFO, exchangeInstrumentID);

                short permittedToTrade = Convert.ToInt16(splittedString[PERMITTEDTOTRADE_INDEX]);
                short issueRate = Convert.ToInt16(splittedString[ISSUERATE_INDEX]);

                int minimumLotQuantity = Convert.ToInt32(splittedString[MINIMUMLOTQUANTITY_INDEX]);
                int boardLotQty = Convert.ToInt32(splittedString[BOARDLOTQUANTITY_INDEX]);
                double tickSize = (double)Convert.ToInt32(splittedString[TICKSIZE_INDEX]) / 
                                                          ExchangeCommon.GetPriceDivisor(ExchangeSegment.NSEFO);
                if (tickSize <= 0)
                {
                    continue;
                }
                
                // The Tick Per point is evaluated as 20 if tickSize is .05
                int tpp = (int)(1.0/tickSize);

                int lowPriceRange = Convert.ToInt32(splittedString[LOWPRICERANGE_INDEX]);
                int highPriceRange = Convert.ToInt32(splittedString[HIGHPRICERANGE_INDEX]);
                string instrumentDisplayName = splittedString[NAME_INDEX];
                
                if (isFutures)
                {
                    string instrumentName = InstrumentBase.GetInstrumnetName(symbol, contractExpiration);

                    FuturesInstrument futuresInstrument = new FuturesInstrument(ExchangeSegment.NSEFO,
                       exchangeInstrumentID,
                       symbol,
                       instrumentName,
                       instrumentDisplayName,
                       contractExpiration,
                       boardLotQty,
                       tpp);

                    futuresInstrument.LowLimit = lowPriceRange * .01;
                    futuresInstrument.HighLimit = highPriceRange * .01;

                    instrumentDict[instrumentID] = futuresInstrument;

                    InstrumentBase underlineInstrument = InstrumentManager.Instance.GetInstrument(ExchangeSegment.NSECM, assetToken);
                    if (underlineInstrument != null)
                        futuresInstrument.UnderlyingInstrument = underlineInstrument;

                    futuresInstrument.ExtendedPropertyList["UnderlyingExchangeInstrumentID"] = assetToken.ToString();
                    InstrumentManager.Instance.SubscribeInstrument(futuresInstrument);
                }
                else
                {
                    string instrumentName = InstrumentBase.GetInstrumnetName(symbol, contractExpiration, optionTypeEnum, strikePrice);

                    OptionsInstrument optionInstrument = new OptionsInstrument(ExchangeSegment.NSEFO,
                       exchangeInstrumentID,
                       symbol,
                       instrumentName,
                       instrumentDisplayName,
                       contractExpiration,
                       optionTypeEnum,
                       strikePrice,
                       boardLotQty,
                       tpp);

                    optionInstrument.LowLimit = lowPriceRange * .01;
                    optionInstrument.HighLimit = highPriceRange * .01;

                    instrumentDict[instrumentID] = optionInstrument;

                    InstrumentBase underlineInstrument = InstrumentManager.Instance.GetInstrument(ExchangeSegment.NSECM, assetToken);
                    if (underlineInstrument != null)
                        optionInstrument.UnderlyingInstrument = underlineInstrument;

                    optionInstrument.ExtendedPropertyList["UnderlyingExchangeInstrumentID"] = assetToken.ToString();
                    InstrumentManager.Instance.SubscribeInstrument(futuresInstrument);

                }
}
```

### How to add custom properties in instrument object? 
Instrument object provides an extended property container to add your custom properties that can be retrieved as when needed. 
```
FuturesInstrument futuresInstrument = InstrumentManager.Instance.Getinstrument(Exchangesegment.NSECM, 10222);
……
……

futuresInstrument.ExtendedPropertyList["UnderlyingExchangeInstrumentID"] =  "23332";
futuresInstrument.ExtendedPropertyList["AssetClass"] =  "Comodities";

……
…...

string assetClass = futuresInstrument.ExtendedPropertyList["AssetClass"]
```
### How to add custom properties in instrument object? 
Instrument object provides an extended property container to add your custom properties that can be retrieved as when needed
```
FuturesInstrument futuresInstrument = InstrumentManager.Instance.Getinstrument(Exchangesegment.NSECM, 10222);
……
……

futuresInstrument.ExtendedPropertyList["UnderlyingExchangeInstrumentID"] =  "23332";
futuresInstrument.ExtendedPropertyList["AssetClass"] =  "Comodities";

……
…...

string assetClass = futuresInstrument.ExtendedPropertyList["AssetClass"]
```

### Accessing Instrument or set of Instruments from Instrument Manager 
InstrumentManager is singleton container of all subscribed instruments and allow a single point access of Instrument instance
```

// Accessing instrument using Exchange Segment and Exchange Instrument ID
FuturesInstrument futuresinstrument1 = (FuturesInstrument)InstrumentManager.Instance.GetInstrument(ExchangeSegment.NSEFO, 48548);

// Accessing instrument using Exchange Segment and Instrument Name
FuturesInstrument futuresinstrument2 = (FuturesInstrument)InstrumentManager.Instance.GetInstrument(ExchangeSegment.NSEFO, 
                                                                                                                                                                             "BANKNIFTY 25MAR21 FUT");

// Accessing instrument using InstrumentID
FuturesInstrument futuresinstrument3 = (FuturesInstrument)InstrumentManager.Instance.GetInstrument(200000048548);

// Accessing all call and put options list of BankNifty with specific expiry month

string symbolName = "BANKNIFTY";
DateTime contractExpiry = new DateTime(2021, 03, 25);
List<OptionsInstrument> callOptionsList = InstrumentManager.Instance.GetAllCallOptionsInstrumentForSymbolAndExpiry(
                                                                                                                                                                  symbolName, contractExpiry);
List<OptionsInstrument> putOptionsList = InstrumentManager.Instance.GetAllPutOptionsInstrumentForSymbolAndExpiry(
                                                                                                                                                                  symbolName, contractExpiry);



// Accessing all call and put options list of BankNifty

List<OptionsInstrument> bankNiftyOptionsList = InstrumentManager.Instance.GetAllOptionsInstrumenttForSymbol(symbolName);

// Apply linq query to filter call options with given expiry month
List<OptionsInstrument> callOptionsListX =
                     bankNiftyOptionsList.Where(iterator => iterator.Symbol.ToUpper() == symbolName.ToUpper() &&
                                                                        (iterator.OptionType == OptionType.CE || iterator.OptionType == OptionType.CA) &&
                                                                        iterator.StrikePrice > 0 &&
                                                                        iterator.ContractExpiration.Date == contractExpiry.Date).
                                                                        OrderBy(iterator => iterator.ContractExpiration).
                                                                        ThenBy(iterator => iterator.StrikePrice).ToList();
```

### Instrument object is serializable and deserializable using a Protobuf? 
All instrument types object are seriazable to byte array and subsequently deserialize byte array to object using a protobuf serialization deserialization mechanism. This is helpful in case you want to preserve the state of Instrument in some binary file or database.

```
Using ProtoBuf;
using Newtonsoft.Json;

using QX.FinLib.Common;
using QX.FinLib.Instrument;

…….

FuturesInstrument futuresinstrument = (FuturesInstrument)InstrumentManager.Instance.GetInstrument(ExchangeSegment.NSEFO, 48548);

// Serialize to byte stream
byte[] bData = ProtoSerialize<FuturesInstrument>(futuresinstrument);

// Deserialzie from byte stream to object
FuturesInstrument futuresinstrumentX = ProtoDeserialize<FuturesInstrument>(bData);

…….
…….

public byte[] ProtoSerialize<T>(T record) where T : class
{
            if (null == record) return null;

            try
            {
                using (var stream = new MemoryStream())
                {
                    Serializer.Serialize(stream, record);
                    return stream.ToArray();
                }
            }
            catch (Exception exp)
            {
                // Log error
                throw exp;
            }
}

public T ProtoDeserialize<T>(byte[] data) where T : class
{
            if (null == data) return null;

            try
            {
                using (var stream = new MemoryStream(data))
                {
                    return Serializer.Deserialize<T>(stream);
                }
            }
            catch (Exception exp)
            {
                // Log error
                throw exp;
            }
}
```
























