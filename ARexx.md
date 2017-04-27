# ARexx (.rexx files)
###### ARexx interpreter: SYS:/System/RexxMast
###### Run scripts from command line. Example: ```rx HelloWorld``` (don't need .rexx extension when running)

## Comments
All .rexx files must begin with a comment
```
/* Comment can be anything */
```
Prefix comment with $VER to add version information
```
/* $VER: MyScript.rexx v1.0 (03/03/2017) */
```

## Output
SAY - Equivalent of PRINT. Prints output with a new line. Literals can be in double or single quotes. Single quotes within a single quoted literal must be escaped using 2 single quotes.
```
SAY "I don't like onions"
SAY 'I don''t like onions'
```

## Variables
* Not predefine, can hold any datatype. Not case-sensitive
* If variable's value has not been explicitly set, its value is the uppercase of its name
```
SAY hello
/* prints out: HELLO */
hello = 1234
SAY hello
/* prints out: 1234 */
hello = "Hello World"
SAY hello
/* prints out: Hello World */
```

## Input
PULL - Prompts for input and stores in a variable
```
SAY "What's your name?"
PULL name
SAY "Hello," name "What a cool name."
```

## Arrays (Actually Compound Variables)
Indexes in Rexx start at 1 instead of 0.

Specified

stem.tail ex: ```days.monday = "Monday"```

Or use indexes
```
stem.1 = "First!"
stem.2 = "Second!"
```

###Initialize
```
stem. = 0
```

### Length of 1 dimensional "arrays"
```
stem.0
```

### Copying
```
new_stem. = old_stem.
```

### Iterating
```
DO i = 1 to stem.0
  dothings
END
```

### 2 Dimensional
```
stem.1.1 = "Hot"
stem.1.2 = "Damn"
stem.2.1 = "Cold"
stem.2.1 = "Dam"
```

### Deleting
```
drop stem.
```
## Operators
|Operator | Meaning       |
|:-------:|:-------------:|
|=        | equals        |
|==       | exactly equals|
|>        | greater than  |
|<        | less than     |
|~        | not           |
|//       | modulus       |

concatenate strings with ||
```
fullname = first || " " || last
```

## Conditionals

### IF statements
```
IF (pizza = cold) THEN cook

IF (pizza = cold) THEN
  cook
ELSE
  eat

IF (pizza = cold) THEN
  cook
ELSE IF (pizza = buffalochicken) THEN
  dipInRanch
ELSE
  eat
```

### NOT operator = ~ (tilde)
```
IF ~(pizza = cold) THEN eat
```

### NOP - No operation. Use this instead of actual code if program should do nothing
```
IF (stomach = full) THEN NOP
```

### SELECT/WHEN/OTHERWISE
```
SELECT
  WHEN (x = 1) THEN
    SAY "x = 1"
  WHEN (x = 2) THEN
    SAY "x = 2"
  OTHERWISE
    SAY "x is a mystery"
END
```

## Loops

### DO - Used to block multiple statements
```
IF (pizza = buffalochicken) THEN
  DO
    dipInRanch
    eat
  END

/* DO loop (fixed amount) */
DO 5
  SAY "Hello World"
END

/* DO WHILE loop */
x = 10
DO WHILE x > 1
  x = x - 1
  SAY "Hello World"
END

/* DO UNTIL loop */
x = 10
DO UNTIL x < 1
  x = x -1
  SAY "Hello World"
END

/* DO FOREVER - Loops until reaches a LEAVE clause */

/* DO TO loop (like a FOR loop) */
DO x = 1 TO 10
  SAY "Hello World"
END

/* increment by 2 */
DO x = 1 to 10 by 2
  /* Prints 5 times */
  SAY "Hello World"
END

/* go backwards */
DO x = 10 to 1 by -1
  SAY "Hello World"
END

/* Reading files */
DO WHILE lines( ) > 0
```

## Some Functions
* RANDOM(low, high) - Returns random number between 'low' & 'high' (highest allowed is 1000 though)
* RANDU() - Returns a random float between 0 and 1
* C2D(char) - Converts character to digit. Ex: C2D("A") returns 65
* D2C(int) - Converts digit to character. Ex: D2C(65) returns A
* LEFT(string, int) - Returns substring starting at left for a # of chars
* RIGHT(string, int) - Returns substring starting at right for a # of chars
* LENGTH(string) - Returns length of a string

## Procedures & Functions

### Procedures

Define a procedure by beginning with a label and ending with RETURN statement
```
title_screen:
SAY "*** OMG TITLE SQUEE ***"
return
```

Call a procedure with CALL statement
```
call title_screen
```

This works sort of like a GOTO, except it actually returns back to where it was called from. Place procedures and functions at bottom of file prefixed by an exit/return statement so program doesn't run them automatically when it reaches the "end" of the script.

Can use keyword /procedure/ to privatize the procedure and it can no longer access any variables outside its scope. Can whitelist certain variables using keyword /expose/.
```
title_screen: procedure expose name
```

#### PARSE ARG - defines arguments
```
make_pizza:
PARSE ARG toppings, extracheese, oregano
```

arg() - returns number of arguments
arg(index) - returns value of argument at specific index

### Functions

Make a Procedure into a Function by returning a value
```
say add(1,2)
exit

add:
PARSE ARG a, b
return a + b

/* Outputs: 3 */
```

## Inter process communication
System-wide "clipboard" that stores keys/values and can be accessed by all Arexx processes on system.

SETCLIP(key, value)

GETCLIP(key)
