# MQL5 Programming Reference

This document provides a comprehensive collection of MQL5 code examples and patterns, adapted from common programming concepts to the MQL5 trading platform environment. It covers basic syntax, data structures, resource management, and trading-specific functionality.

## Table of Contents
- [Basic Syntax and Structure](#basic-syntax-and-structure)
- [Data Types and Variables](#data-types-and-variables)
- [Functions](#functions)
- [Arrays and Collections](#arrays-and-collections)
- [Object-Oriented Programming](#object-oriented-programming)
- [Resource Management](#resource-management)
- [Trading Functions](#trading-functions)
- [Event Handling](#event-handling)
- [Error Handling](#error-handling)
- [File Operations](#file-operations)

## Basic Syntax and Structure

### Hello World in MQL5
```mql5
//+------------------------------------------------------------------+ 
//| HelloWorld.mq5                                                   |
//| A basic Hello World script for MetaTrader 5                      |
//+------------------------------------------------------------------+ 

#property copyright "Your Name"
#property link      "https://www.yourwebsite.com"
#property version   "1.00"
#property strict

//--- script entry point
void OnStart() {
    // Print Hello World to the Experts log
    Print("Hello, World from MetaTrader 5!");
}
```

### Basic Function Declaration
```mql5
// Simple function to add two numbers
double AddNumbers(double a, double b) {
    return a + b;
}

// Usage in OnTick or OnStart
void OnTick() {
    double result = AddNumbers(5.5, 3.2);
    Print("Sum: ", result);
}
```

## Data Types and Variables

### Variable Declarations
```mql5
// Basic data types in MQL5
int integerValue = 42;
double floatingPoint = 3.14159;
string text = "MQL5 Trading";
bool condition = true;
color chartColor = clrRed;
datetime currentTime = TimeCurrent();
```

### Constants and Enumerations
```mql5
// Constant definition
#define MAX_RETRIES 3

// Enumeration for trade types
enum ENUM_TRADE_DIRECTION {
    TRADE_BUY = 0,   // Buy operation
    TRADE_SELL = 1   // Sell operation
};
```

## Functions

### Function with Parameters and Return Value
```mql5
// Calculate Fibonacci number
double calculateFibonacci(int n) {
    if(n <= 1)
        return n;
    return calculateFibonacci(n-1) + calculateFibonacci(n-2);
}
```

### Function Overloading (Not supported directly in MQL5, use different names)
```mql5
// Different function names for different parameter types
double calculateAverage(int value1, int value2) {
    return (value1 + value2) / 2.0;
}

double calculateAverageDouble(double value1, double value2) {
    return (value1 + value2) / 2.0;
}
```

## Arrays and Collections

### Array Declaration and Initialization
```mql5
// Static array
double prices[5] = {1.2, 1.3, 1.4, 1.5, 1.6};

// Dynamic array
double dynamicPrices[];

void OnStart() {
    // Resize dynamic array
    ArrayResize(dynamicPrices, 5);
    
    // Fill array with values
    for(int i = 0; i < 5; i++) {
        dynamicPrices[i] = i * 1.1;
    }
    
    // Print array contents
    ArrayPrint(dynamicPrices);
}
```

### Array Operations
```mql5
// Find highest value in array
double FindHighestPrice(double[] prices) {
    double highest = prices[0];
    for(int i = 1; i < ArraySize(prices); i++) {
        if(prices[i] > highest)
            highest = prices[i];
    }
    return highest;
}
```

## Object-Oriented Programming

### Class Definition
```mql5
// Trading strategy class
class CTradingStrategy {
private:
    string strategyName;
    double lotSize;

public:
    // Constructor
    CTradingStrategy(string name, double lot) : strategyName(name), lotSize(lot) {}
    
    // Destructor
    ~CTradingStrategy() {}
    
    // Method to get strategy name
    string GetName() { return strategyName; }
    
    // Virtual method for strategy execution
    virtual bool Execute() { return false; }
};
```

### Class Inheritance
```mql5
// Moving Average strategy class derived from base strategy
class CMovingAverageStrategy : public CTradingStrategy {
private:
    int maPeriod;
    double lastMAValue;

public:
    CMovingAverageStrategy(string name, double lot, int period) 
        : CTradingStrategy(name, lot), maPeriod(period), lastMAValue(0) {}
        
    virtual bool Execute() {
        // Simple MA calculation and trading logic
        double currentMA = iMA(_Symbol, PERIOD_CURRENT, maPeriod, 0, MODE_SMA, PRICE_CLOSE, 0);
        // Implement trading logic here
        return true;
    }
};
```

## Resource Management

### Memory Management with Objects
```mql5
// Using objects for resource management
class CTradeManager {
private:
    CTrade *trade;

public:
    CTradeManager() {
        trade = new CTrade();
    }
    
    ~CTradeManager() {
        delete trade;
    }
};
```

## Trading Functions

### Market Order Execution
```mql5
// Place a buy order
bool PlaceBuyOrder(string symbol, double volume, double price, double sl, double tp, string comment) {
    MqlTradeRequest request= {0};
    MqlTradeResult result= {0};
    
    request.action = TRADE_ACTION_DEAL;
    request.symbol = symbol;
    request.volume = volume;
    request.price = price;
    request.sl = sl;
    request.tp = tp;
    request.comment = comment;
    request.type = ORDER_TYPE_BUY;
    
    return OrderSend(request, result);
}
```

### Position Information
```mql5
// Get position profit
bool GetPositionProfit(ulong ticket, double &profit) {
    if(PositionSelectByTicket(ticket)) {
        profit = PositionGetDouble(POSITION_PROFIT);
        return true;
    }
    return false;
}
```

## Event Handling

### Timer Events
```mql5
// Timer event handler
void OnTimer() {
    // Handle periodic tasks
    Print("Timer event triggered at: ", TimeToString(TimeCurrent(), TIME_DATE|TIME_MINUTES|TIME_SECONDS));
}

// Initialize timer in OnInit
int OnInit() {
    // Set timer to trigger every 5 seconds
    EventSetTimer(5);
    return(INIT_SUCCEEDED);
}

// Clean up timer in OnDeinit
void OnDeinit(const int reason) {
    EventKillTimer();
}
```

### Tick Events
```mql5
// Tick event handler for Expert Advisors
void OnTick() {
    // Get current price
    double currentPrice = SymbolInfoDouble(_Symbol, SYMBOL_ASK);
    Print("Current ASK price for ", _Symbol, ": ", currentPrice);
    
    // Implement trading logic here
}
```

## Error Handling

### Checking Function Results
```mql5
// Check order execution result
bool CheckOrderResult(MqlTradeResult &result) {
    if(result.retcode == TRADE_RETCODE_DONE) {
        Print("Order executed successfully, ticket: ", result.order);
        return true;
    } else {
        Print("Order failed with error #", result.retcode, ": ", GetLastError());
        return false;
    }
}
```

## File Operations

### Writing to File
```mql5
// Write data to CSV file
bool WriteToCsv(string filename, string data) {
    int handle = FileOpen(filename, FILE_WRITE|FILE_CSV|FILE_COMMON);
    if(handle == INVALID_HANDLE) {
        Print("Failed to open file, error: ", GetLastError());
        return false;
    }
    
    FileWrite(handle, data);
    FileClose(handle);
    return true;
}
```

### Reading from File
```mql5
// Read data from CSV file
bool ReadFromCsv(string filename, string &data) {
    int handle = FileOpen(filename, FILE_READ|FILE_CSV|FILE_COMMON);
    if(handle == INVALID_HANDLE) {
        Print("Failed to open file, error: ", GetLastError());
        return false;
    }
    
    data = FileReadString(handle);
    FileClose(handle);
    return true;
}
```

This reference guide provides a foundation for MQL5 programming, covering essential concepts adapted to the trading environment of MetaTrader 5. For more specific functionality, refer to the official MQL5 documentation.
