# Measures & DAX

This file includes the measures and DAX formulas used across the projects.

## Examples

### ForecastDates
```DAX
ForecastDates =
DATATABLE(
    "Label", STRING,
    "DayIndex", INTEGER,
    {
        {"7 Days Ago", -7},
        {"Today", 0},
        {"7 Days Ahead", 7}
    }
)
```

### TOTAL AMOUNT
```DAX
TOTAL AMOUNT =
VAR QTY = SELECTEDVALUE(quantity_input[Value], 1)
VAR PRICE = SELECTEDVALUE(CRYPTO[current_price])
RETURN
QTY * PRICE
```

### Expected return 7D
```DAX
Expected return 7D =
VAR price = SELECTEDVALUE(CRYPTO[current_price])
VAR Changes7D = SELECTEDVALUE(CRYPTO[price_change_percentage_7d_in_currency], 0)
RETURN
price * (1 + Changes7D / 100)
```

### Forecast Price
```DAX
Forecast Price =
VAR Day = SELECTEDVALUE(ForecastDates[Label])
RETURN
SWITCH(
    Day,
    "7 Days Ago", [Past Price 7D],
    "Today", SELECTEDVALUE(CRYPTO[current_price]),
    "7 Days Ahead", [Future Price 7D]
)
```

### Future Price 7D
```DAX
Future Price 7D =
VAR CurrentPrice = SELECTEDVALUE(CRYPTO[current_price])
VAR Change7D = SELECTEDVALUE(CRYPTO[price_change_percentage_7d_in_currency], 0)
RETURN
CurrentPrice * (1 + Change7D / 100)
```

### Growth Momentum
```DAX
Growth Momentum =
VAR Weekly = SELECTEDVALUE(CRYPTO[price_change_percentage_7d_in_currency])
VAR Daily = SELECTEDVALUE(CRYPTO[price_change_percentage_24h_in_currency])
RETURN
IF(
    ISBLANK(Weekly) || ISBLANK(Daily),
    BLANK(),
    DIVIDE(Weekly - Daily, ABS(Daily))
)
```

### Growth Score
```DAX
Growth Score =
VAR g = SELECTEDVALUE(CRYPTO[price_change_percentage_7d_in_currency], 0)
RETURN
SWITCH(
    TRUE(),
    ISBLANK(g), 0,
    g >= 30, 20,
    g >= 15, 15,
    g >= 5, 10,
    g >= 0, 5,
    g < 0, 1
)
```

### Momentum Rating
```DAX
Momentum Rating =
VAR M = [Growth Momentum]
RETURN
SWITCH(
    TRUE(),
    ISBLANK(M), "No Data",
    M >= 0.50, "Strong Uptrend",
    M >= 0.20, "Moderate Uptrend",
    M >= 0.00, "Sideways / Stable",
    M >= -0.20, "Weak Downtrend",
    "Strong Downtrend / Reversal Risk"
)
```

### Past Price 7D
```DAX
Past Price 7D =
VAR CurrentPrice = SELECTEDVALUE(CRYPTO[current_price])
VAR Change7D = SELECTEDVALUE(CRYPTO[price_change_percentage_7d_in_currency], 0)
RETURN
DIVIDE(CurrentPrice, (1 + Change7D / 100))
```

### Momentum Score
```DAX
Momentum Score =
VAR M = [Growth Momentum]
RETURN
SWITCH(
    TRUE(),
    ISBLANK(M), BLANK(),
    M >= 0.50, 100,
    M >= 0.20, 80,
    M >= 0.00, 60,
    M >= -0.20, 40,
    M >= 0.50, 20,
    10
)
```
