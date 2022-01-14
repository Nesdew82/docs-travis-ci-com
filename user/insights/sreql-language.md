---
title: SREQL Language
layout: en_insights

---

While adding a probe you find the **EDIT QUERY** button in the **Query** field. The button displays a window where you can edit and test all your queries.

![probeEditor](/user/images-insights/probeEditor.png) 

Go to the **Plugin** drop-down menu and select the plugin you want to use, click the **LOAD** button and you will see how the Sample JSON displays.

Add any query you want to test in the **Query** space, click the button **RUN QUERY** and you will receive an Output for the specific query. 

## Query Language

Travis Insights uses **SREQL** as a query language, SREQL is a readable and easy scripting language based on SQL syntax that allows you to track state changes and compare to known/desired configurations. 
The language lets you work with selector mechanisms, boolean logic, type system, and string concatenation.

## SREQL Data Types
### Simple Types

|   Data Type      |           Use                                      |     
|:----------------:|:--------------------------------------------------:|
| **number**       | 100 or 2.33                                        |    
| **string**       | "string" or 'string' (Strings are encoded in UTF-8)| 
| **null**         | null or nil                                        |
| **true**         | true or t                                          |
| **false**        | false or f                                         |

> **Info:**
>
> Boolean types and null are case insensitive and can be written in several ways:
>
>     true:  t, true (or trUe),
>     false: f or false,
>     null:  n, none, null, nil.
>
> Negatives are: 
>
>     false, null, 0, "", [], {}
>
>    *Any other value is positive*

### Complex Types
#### Arrays
SREQL define arrays by:

    [val1, val2, ..., valN]

#### Objects 
SREQL define objects by:

    { "atr1":val1, "atr2":val2, ..., "atrN":valN }

    { atr1:val1, atr2:val2, ..., atrN:valN }

### Date/Time/DateTime Types
Dates are handled as datetime objects. There are also **date** and **time** objects which provide support for date and time manipulation. Datetime, date, and time objects support year, month, day, hour, minute, second and microsecond attributes.

     now().year -> 2011.

## SREQL Operators
### Operator precedence
Operations are executed in the following order:

     (. = [ = () -> (+ (prefix) = - (prefix)) -> (* = /) -> (+ (infix) = - (infix)) -> (in = not = is = < = > = <= = >=) -> not -> and -> or

Other operators and tokens are precedence-neutral.

### Arithmetic operators

|   Operator   |           Description                                                                                              |     
|:------------:|:------------------------------------------------------------------------------------------------------------------:|
| **+**        | addition (+ is also a concatenation operator for strings and arrays)                                               |    
| **-**        | subtraction                                                                                                        | 
| *            | multiplication (Alternative use is select all objects from array)                                                  |
| **/**        | division (Integer division results in floating point number. Use int() function to turn it into an integer again.) |
| **%**        | modulo                                                                                                             |

### Boolean Logic Operators

|   Operator   |           Description                                                                                                                               |     
|:------------:|:---------------------------------------------------------------------------------------------------------------------------------------------------:|
| **not**      |negation (not always casts result to boolean type)                                                                                                   |    
| **and**      |conjunction (and evaluates the expression on the left-hand side, if negative it is returned; the expression on the right-hand side is not evaluated.)|
| **or**       |alternation (or evaluates the expression on the left-hand side, if positive it is returned; the expression on the right-hand side is not evaluated.) |

### Comparison Operators

|   Operator    |           Description                                                                                               |     
|:-------------:|:-------------------------------------------------------------------------------------------------------------------:|
| **is**        |equality                                                                                                             |    
| **is not**    |equality negation                                                                                                    |
| **>**         |greater than                                                                                                         |
| **>=**        |greater than or equal                                                                                                |
| **<**         |less than                                                                                                            |
| **<=**        |less than or equal                                                                                                   |

### Membership Tests

|   Operator       |           Description     |     
|:----------------:|:-------------------------:|
| **in**           |checks if the result of the left side of the expression is in the array, object, or string (In objects, keys are matched.)    |    
| **not in**       |opposite behavior to in; Equivalent to not expr in array                                                                      |

### Concatenation operator +
Besides standard addition of numbers, + concatenates strings, arrays, and objects.

If two arrays are concatenated, right array elements are added to the end of the left array.

    [1, 2, 4] + [3, 5] -----> [1, 2, 4, 3, 5]

If the string is concatenated with an array, it is added to the beginning or end of the array depending on the order:

    "aaa"+["bbb"] -----> ["aaa", "bbb"]

    ["bbb"]+"aaa" -----> ["bbb", "aaa"]

Objects are merged so that the right object overwrites existing elements of the left object. 

Object concatenation is not deep. It means that only direct child elements of the root element are overwritten rather than leaf nodes.

    {"a":1, "b":2} + {"a":2, "c":3} -----> {"a":2, "b":2, "c":3}

## Built-in functions
### Casting functions

  - **str(ANY)**
  - **int(NUMBER/STRING)**
  - **float(NUMBER/STRING)**

### Arithmetic functions
  - **sum(ARRAY)** (Argument is a list of numbers. If there are float numbers in the list, sum() returns float.)
  - **max(ARRAY)**
  - **min(ARRAY)**
  - **avg(ARRAY)** (Equivalent to sum(array)/len(array))
  - **round(FLOAT, INTEGER)** (Always returns float. Second argument defines the precision of the round.)

### String functions 
- **replace(STRING, toReplace, replacement)**
- **upper(STRING)**
- **lower(STRING)**
- **capitalize(STRING)**
- **title(STRING)** (title('aaa bbb') -> 'Aaa Bbb')
- **split(STRING <, sep>)**
- **slice(STRING, [start, end])**
- **slice(STRING, [[start, end], [start,end], ...])**

### Array functions
- **sort(ARRAY <, key>)** (If key is provided, will sort array of objects by key.)
- **reverse(array)**
- **count(ARRAY), len(ARRAY)**
- **join(ARRAY <, joiner>)** (join(['c', 'b', 'a']) -> 'cba', join(['c', 'b', 'a'], '.') -> 'c.b.a')

### Date and time functions
- **now()** (gets current UTC time.)
- **date(arg)** (arg can be array of structure [yyyy,mm,dd] or datetime object . If no arg is specified then date() defaults to current UTC date.)
- **time(arg)** (arg can be an array of structure [hh,mm,ss,mmmmmm] where only hour is required or datetime object . If no arg is specified then time() defaults to current UTC time.)
- **dateTime(args)** (args: if one argument is specified then it needs to be a datetime object or [yyyy, mm, dd, hh, mm, ss, mmmmmm] where year, month, day, hour and minute are required. If two arguments are specified, the first argument can be date object or [yyyy, mm, dd] array, second can be time object or [hh, mm, ss, mmmmmm] array where only hour is required)
- **dateTime(STRING, STRING)** (parse string into datetime object. First args - string to parse, second arg - datetime format. 

  Example: 

      dateTime("2020-09-10T00:00:UTC", "%Y-%m-%dT%H:%M:%SZ"))

- **toMillis(time)** (Counts milliseconds since epoch.)

## Examples 

### One set results queries

SREQL queries: 

#### Example 1

*SREQL query:*

      FROM $..standardDeviation
      ASSERT @ IS 0.0

#### Example 2

*SREQL query:*

      FROM $.Milestones
      ASSERT COUNT < 6

#### Example 3

*SREQL query:*

      FROM $.Milestones
      WHERE @.is_completed is false
      ASSERT COUNT < 6

#### Example 4

*SREQL query:*

      FROM $.PageViewsDuration
      ASSERT AVG("pageViews/duration") 
      IS NOT NULL AND AVG("pageViews/duration") < 4000

#### Example 5

*SREQL query:*

      ASSERT AVG($.PageViewsDuration ."pageViews/duration") 
      IS NOT NULL AND AVG($.PageViewsDuration ."pageViews/duration") < 4000

#### Example 6

*SREQL query:*

      FROM $.Builds
      ASSERT COUNT(@.status is "failed") / COUNT < 0.05

#### Example 7

*SREQL query:*

      ASSERT COUNT($.Builds[@.status is "failed") / COUNT($.Builds) < 0.05


### Two sets results queries

SREQL queries:

#### Example 1

*SREQL query:*

      SELECT application_name
      FROM $.EmailDigests
      ASSERT @.enabled IS true

#### Example 2

*SREQL query:*

      SELECT application_name
      FROM $.EmailDigests
      WHERE @.type IS "marketing"
      ASSERT @.enabled IS true

#### Example 3

*SREQL query:*

      SELECT space_name
      FROM $.Milestones
      WHERE @.is_completed IS false
      ASSERT toMillis(dateTime(@.due_date, "%Y-%m-%d")) > toMillis(now()) - 24*60*60*1000

#### Example 4

*SREQL query:*

      SELECT plan.name
      FROM $.Builds
      ASSERT toMillis(dateTime(@.buildCompletedTime, "%Y-%m-%dT%H:%M:%S.%fZ")) > toMillis(now()) - 7*24*60*60*1000) AND @.buildState IS NOT "Successful"

#### Example 5

*SREQL query:*

      SELECT name
      FROM $.SecurityContacts
      ASSERT len(@.properties.phone) > 0


