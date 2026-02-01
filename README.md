# Minimal Forth Interpreter in C

A lightweight **[Forth](https://en.wikipedia.org/wiki/Forth_(programming_language))** interpreter implementation in C (~250 lines), featuring the core functionality of the Forth language: stack operations, word definitions, and basic I/O.

## Building and Running

```bash
gcc -o forth_mini forth_mini.c

./forth_mini

or

./forth_mini < file.f 
```

## Features

### Basic Arithmetic
- `+` - Addition (a b -- a+b)
- `-` - Subtraction (a b -- a-b)
- `*` - Multiplication (a b -- a*b)
- `/` - Division (a b -- a/b)
- `MOD` - Modulo (a b -- a%b)

**Example:**
```forth
ok> 5 3 + .
8 ok>
ok> 15 4 / .
3 ok>
ok> 15 4 MOD .
3 ok>
```

### Stack Operations
- `DUP` - Duplicate top item (n -- n n)
- `DROP` - Remove top item (n -- )
- `SWAP` - Swap top two items (a b -- b a)
- `OVER` - Copy second item to top (a b -- a b a)
- `ROT` - Rotate top three items (a b c -- b c a)
- `.S` - Show stack contents (non-destructive)
- `.` - Pop and print top item

**Example:**
```forth
ok> 1 2 3 .S
<sp=3> 1 2 3
ok> DUP .S
<sp=4> 1 2 3 3
ok> SWAP .S
<sp=4> 1 2 3 3
ok> DROP DROP .S
<sp=2> 1 2
ok>
```

### Comparison Operators
- `=` - Equal (a b -- flag)
- `<` - Less than (a b -- flag)
- `>` - Greater than (a b -- flag)

Returns -1 (all bits set) for true, 0 for false.

**Example:**
```forth
ok> 5 5 = .
-1 ok>
ok> 3 7 < .
-1 ok>
ok> 10 5 > .
-1 ok>
```

### Bitwise Logic
- `AND` - Bitwise AND (a b -- a&b)
- `OR` - Bitwise OR (a b -- a|b)
- `NOT` - Bitwise NOT (a -- ~a)

**Example:**
```forth
ok> 12 10 AND .
8 ok>
ok> 12 10 OR .
14 ok>
```

### I/O Operations
- `.` - Print number and space
- `EMIT` - Print character from ASCII value
- `CR` - Print newline
- `.S` - Display stack contents

**Example:**
```forth
ok> 72 EMIT 101 EMIT 108 EMIT 108 EMIT 111 EMIT CR
Hello
ok>
ok> 42 EMIT CR
*
ok>
```

### Word Definitions
Define new words (functions) using `:` to start and `;` to end.

**Syntax:**
```forth
: WORD_NAME definition... ;
```

**Examples:**
```forth
ok> : SQUARE DUP * ;
ok> 7 SQUARE .
49 ok>

ok> : CUBE DUP DUP * * ;
ok> 3 CUBE .
27 ok>

ok> : ABS DUP 0 < IF 0 SWAP - THEN ;
ok> -5 ABS .
5 ok>

ok> : MAX 2DUP < IF SWAP THEN DROP ;
ok> 5 3 MAX .
5 ok>

ok> : MIN 2DUP > IF SWAP THEN DROP ;
ok> 5 3 MIN .
3 ok>
```

### Useful Compound Words
```forth
ok> : 2DUP OVER OVER ;
ok> 3 4 2DUP .S
<sp=4> 3 4 3 4

ok> : NIP SWAP DROP ;
ok> 5 7 NIP .
7 ok>

ok> : TUCK SWAP OVER ;
ok> 3 4 TUCK .S
<sp=3> 4 3 4
```

## Stack Notation

Forth uses stack effect notation to document word behavior:
- `( before -- after )` - Shows stack state transformation
- Examples:
  - `DUP ( n -- n n )` - duplicates top item
  - `+ ( a b -- sum )` - adds two numbers
  - `SWAP ( a b -- b a )` - swaps top two items

## Complete Examples

### Hello World
```forth
ok> : HELLO 72 EMIT 101 EMIT 108 EMIT 108 EMIT 111 EMIT 33 EMIT CR ;
ok> HELLO
Hello!
ok>
```

### Calculate Average of Two Numbers
```forth
ok> : AVG + 2 / ;
ok> 10 20 AVG .
15 ok>
```

### Absolute Value
```forth
ok> : ABS DUP 0 < IF 0 SWAP - THEN ;
ok> -42 ABS .
42 ok>
ok> 17 ABS .
17 ok>
```

### Sign Function (-1, 0, or 1)
```forth
ok> : SIGN DUP 0 = IF DROP 0 ELSE 0 < IF -1 ELSE 1 THEN THEN ;
ok> -5 SIGN .
-1 ok>
ok> 0 SIGN .
0 ok>
ok> 7 SIGN .
1 ok>
```

### Drawing Stars
```forth
ok> : STAR 42 EMIT ;
ok> : STARS STAR STAR STAR STAR STAR CR ;
ok> STARS
*****
ok>
```

## Implementation Details

### Technical Specifications
- **Stack size:** 256 items
- **Word name max length:** 32 characters
- **Dictionary:** Linked list structure
- **Compilation:** Words are compiled into arrays of pointers
- **Number encoding:** Uses pointer tagging (LSB=1 for numbers)
- **Case sensitivity:** Case-insensitive word lookup

### Architecture
1. **Data Stack:** Holds integer values for computation
2. **Return Stack:** Used internally for control flow
3. **Dictionary:** Linked list of word definitions
4. **Compiler:** Converts word definitions to executable sequences

### Pointer Tagging
The interpreter uses pointer tagging to distinguish between numbers and word pointers:
- Numbers: Stored with LSB set to 1 (shifted left and OR'd with 1)
- Word pointers: Even addresses (LSB naturally 0)

This allows both to coexist in the same data array.

## Limitations

- **No control flow:** No IF/THEN, loops, or conditionals in this minimal version
- **Integer only:** No floating-point arithmetic
- **No variables:** No memory allocation or variables
- **No strings:** Only character-by-character output
- **Fixed sizes:** Stack and dictionary are fixed size
- **No error recovery:** Crashes on stack underflow/overflow
- **No file I/O:** Interactive mode only


## Resources

- **[ANS Forth Standard:](https://forth-standard.org/)** Official language specification

