```
CS

// How to get the number of Bars count in TimeDataSeries
int barCount = tdSeries.Count;

// Access a BarData at specific index 
IBarData barData = tdSeries[5];

// Accessing bar time in unix epochtime i.e elapsed seconds since 1970
int barTime = barData.DateTime;

// Accessing bar time in DateTime structure 
DateTime barTimeDT = barData.DateTimeDT;

// Accessing a bar value at specific index
double closePrice = tdSeries[0].Close;

// Open price of first bar
double openPrice = tdSeries.BarValue(‘O’, 0);  // H-High, L-Low, C-Close, V-Volume

// Get a BarIndex at specific bar datetime
int index = tdSeries.BarDataIndexAtDateTime(DateTime barDT);

//Iterate through each BarData in TimeDataSeries
int barCount = tdSeries.Count;
for (int i = 0; i < barCount; i++)
{
    IBarData barData = tdSeries[i];
}
```