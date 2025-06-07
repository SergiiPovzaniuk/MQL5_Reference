# MQL5 Programming Reference

This document provides a comprehensive collection of MQL5 code examples and patterns, adapted from common programming concepts to the MQL5 trading platform environment. It covers basic syntax, data structures, resource management, and trading-specific functionality.

## Table of Contents
- [Basic Syntax and Structure](#basic-syntax-and-structure)
- [Data Types and Variables](#data-types-and-variables)
    - [Primitive Types](#primitive-types)
    - [Variable Declarations](#variable-declarations)
    - [Constants and Enumerations](#constants-and-enumerations)
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
double addNumbers(double a, double b) {
    return a + b;
}

// Usage in OnTick or OnStart
void OnTick() {
    double result = AddNumbers(5.5, 3.2);
    Print("Sum: ", result);
}
```

## Data Types and Variables

### Primitive Types
```mql5
// MQL5 primitive data types
char   charVar = 'A';     // 8-bit signed integer (-128 to 127)
uchar  ucharVar = 255;    // 8-bit unsigned integer (0 to 255)
short  shortVar = -12345; // 16-bit signed integer (-32768 to 32767)
ushort ushortVar = 54321; // 16-bit unsigned integer (0 to 65535)
int    intVar = -123456;  // 32-bit signed integer (-2147483648 to 2147483647)
uint   uintVar = 1234567; // 32-bit unsigned integer (0 to 4294967295)
long   longVar = -1234567890; // 64-bit signed integer
ulong  ulongVar = 1234567890; // 64-bit unsigned integer
float  floatVar = 3.14f;  // 32-bit floating point (approximate precision)
double doubleVar = 3.1415926535; // 64-bit floating point (higher precision)
bool   boolVar = true;    // Boolean value (true or false)
```

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
double findHighestPrice(double[] prices) {
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
class TradingStrategy {
private:
    string strategyName;
    double lotSize;

public:
    // Constructor
    TradingStrategy(string name, double lot) : strategyName(name), lotSize(lot) {}
    
    // Destructor
    ~TradingStrategy() {}
    
    // Method to get strategy name
    string getName() { return strategyName; }
    
    // Virtual method for strategy execution
    virtual bool execute() { return false; }
};
```

### Class Inheritance
```mql5
// Moving Average strategy class derived from base strategy
class MovingAverageStrategy : public TradingStrategy {
private:
    int maPeriod;
    double lastMAValue;

public:
    MovingAverageStrategy(string name, double lot, int period) 
        : TradingStrategy(name, lot), maPeriod(period), lastMAValue(0) {}
        
    virtual bool execute() {
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
class TradeManager {
private:
    CTrade *trade;

public:
    TradeManager() {
        trade = new CTrade();
    }
    
    ~TradeManager() {
        delete trade;
    }
};
```

## Trading Functions

### Market Order Execution
```mql5
// Place a buy order
bool placeBuyOrder(string symbol, double volume, double price, double sl, double tp, string comment) {
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
bool getPositionProfit(ulong ticket, double profit) {
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
bool checkOrderResult(MqlTradeResult result) {
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
bool writeToCsv(string filename, string data) {
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
bool readFromCsv(string filename, string data) {
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
