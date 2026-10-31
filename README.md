# FoxScript
**FoxScript Full v2.0**

FoxScript is a lightweight scripting language powered by a custom interpreter written in C11.

It provides its own syntax for variables, mathematical expressions, conditions, loops, functions, and built-in modules.

This release is distributed as a precompiled executable. Users do not need to compile the interpreter themselves.

---

## 1. Getting Started

### Requirements

- A compatible operating system and architecture for the supplied executable.
- `FoxScript.exe`.
- A text editor for writing `.fson` scripts.
- A terminal or command-line environment.

### Installation

1. Download `FoxScript.exe`.
2. Create a folder for your FoxScript projects.
3. Place `FoxScript.exe` inside that folder.
4. Create a script file with the `.fson` extension.

Example folder structure:

```text
MyProject/
|
|-- FoxScript.exe
|-- hello.fson
```

### Running a Script

Open a terminal in the project folder and execute:

```powershell
.\FoxScript.exe hello.fson
```

If the script is in another directory, provide its path:

```powershell
.\FoxScript.exe "scripts\hello.fson"
```

The interpreter accepts a script filename as its command-line argument.

Running the executable without an argument displays its usage information.

---

## 2. Hello World

Create a file named `hello.fson`:

```foxscript
submit</f.stdio>

show("Hello, World!")
```

Run it:

```powershell
.\FoxScript.exe hello.fson
```

Expected output:

```text
FoxScript Full v2.0 | hello.fson
[FoxScript] Module loaded: f.stdio
Hello, World!
```

The exact filename displayed depends on the script you execute.

---

## 3. Module System

FoxScript uses `submit` to import built-in modules.

### Import syntax

```foxscript
submit</f.module>
```

The `f.` prefix is used when importing a module. It is not used when accessing that module in expressions.

For example:

```foxscript
submit</f.stdio>
submit</f.math>

show(math.sqrt(144))
```

The module must be imported before its functions or constants are used.

Importing the same module more than once does not load it repeatedly.

### Available Modules

| Import statement | Module | Purpose |
|---|---|---|
| `submit</f.stdio>` | `stdio` | Standard input and output |
| `submit</f.math>` | `math` | Mathematical operations |
| `submit</f.random>` | `random` | Random number generation |
| `submit</f.time>` | `time` | Time information |
| `submit</f.date>` | `date` | Date information |

These are the built-in modules implemented in this release. External module installation is not supported.

---

## 4. Standard Input and Output

### 4.1 Output: `show()`

Import:

```foxscript
submit</f.stdio>
```

Use `show()` to print a value to the terminal.

```foxscript
submit</f.stdio>

show("Hello!")
show(123)
show(10 + 20)
```

`show()` prints a newline after each output.

The `f.stdio` module must be imported before calling it.

### 4.2 Input: `hear()`

`hear()` reads input from the terminal.

In this release, it is only supported inside a `ger` variable declaration.

```foxscript
submit</f.stdio>

{age ger[hear("Enter your age: ")]}

show(age)
```

Example interaction:

```text
Enter your age: 15
15
```

**Important:** `hear()` currently accepts integer input through `ger`. It is not a general-purpose string input function.

Standalone usage is not supported:

```foxscript
hear("Enter your name: ")
```

---

## 5. Variables

FoxScript uses curly braces and square brackets for variable declarations.

### 5.1 Untyped Variables

```foxscript
submit</f.stdio>

{name["FoxScript"]}
{version[2]}

show(name)
show(version)
```

General syntax:

```foxscript
{variable_name[value]}
```

### 5.2 Declared Types

The interpreter recognizes these type names:

| Type | Current behavior |
|---|---|
| `ger` | Integer validation |
| `str` | Accepted as a declaration type |
| `string` | Accepted as a declaration type |
| `dec` | Accepted as a declaration type |
| `bool` | Accepted as a declaration type |

Example:

```foxscript
submit</f.stdio>

{age ger[15]}
{username str["Fox"]}
{price dec[12.5]}
{active bool[true]}

show(age)
show(username)
show(price)
show(active)
```

Only `ger` performs dedicated integer validation. The other type names do not currently enforce their corresponding value types.

### 5.3 Fixed Variables

Use `fixed` to declare a variable that cannot be modified or deleted.

```foxscript
submit</f.stdio>

fixed version[2]

show(version)
```

Attempting to change a fixed variable produces an error.

---

## 6. Operators and Expressions

FoxScript supports numeric expressions.

### 6.1 Arithmetic Operators

| Operator | Description |
|---|---|
| `+` | Addition |
| `-` | Subtraction |
| `*` | Multiplication |
| `/` | Division |
| `%` | Modulo |

Example:

```foxscript
submit</f.stdio>

show(10 + 5)
show(10 - 5)
show(10 * 5)
show(10 / 5)
show(10 % 3)
```

Division and modulo by zero are rejected.

### 6.2 Comparison Operators

| Operator | Description |
|---|---|
| `==` | Equal to |
| `!=` | Not equal to |
| `>` | Greater than |
| `<` | Less than |
| `>=` | Greater than or equal to |
| `<=` | Less than or equal to |

Example:

```foxscript
submit</f.stdio>

show(10 > 5)
show(10 == 10)
show(5 != 3)
```

### 6.3 Logical Operators

| Operator | Description |
|---|---|
| `and` | Logical AND |
| `or` | Logical OR |
| `!` | Logical NOT |

Example:

```foxscript
submit</f.stdio>

show(10 > 5 and 5 > 2)
show(10 < 5 or 5 > 2)
show(!false)
```

### 6.4 Boolean and Null Literals

The expression parser recognizes:

```foxscript
true
false
null
```

For numeric expression evaluation, `true` is represented as `1`, while `false` and `null` are represented as `0`.

---

## 7. Conditions

Use `when` to execute a block when a condition is true.

```foxscript
submit</f.stdio>

{age ger[15]}

when age >= 18 next
    show("Adult")
otherwise
    show("Not an adult")
end
```

The `otherwise` block runs when the condition is false.

### Conditional Syntax

```foxscript
when condition next
    # Statements
otherwise
    # Alternative statements
end
```

**Current limitation:** Conditional branching is basic. Although `elif` is recognized by the interpreter, chained `elif` branches are not reliably supported in this release. The documented, recommended structure is `when` with an optional `otherwise`.

---

## 8. Loops

### 8.1 Repeat

`repeat` executes a block a specified number of times.

```foxscript
submit</f.stdio>

repeat 5 next
    show("Hello!")
end
```

The repeat count is evaluated numerically. Negative values are rejected, and counts above 100,000 are capped.

### 8.2 Until

`until` executes a block while its condition is false. It stops when the condition becomes true.

```foxscript
submit</f.stdio>

{count ger[0]}

until count >= 5 next
    show(count)
    count = count + 1
end
```

The condition is checked before each iteration.

The interpreter limits an `until` loop to 100,000 iterations.

### 8.3 Stop

`stop` terminates the current loop.

```foxscript
submit</f.stdio>

repeat 10 next
    show("Running")
    stop
end
```

### 8.4 Skip

`skip` skips the remaining statements in the current loop iteration.

```foxscript
submit</f.stdio>

repeat 3 next
    skip
    show("This line will be skipped")
end
```

---

## 9. Functions

Functions are declared using `craft`.

### 9.1 Function Declaration

```foxscript
submit</f.stdio>

craft greet()
    show("Hello from FoxScript!")
end

greet()
```

### 9.2 Parameters

Functions can receive arguments.

```foxscript
submit</f.stdio>

craft greet(name)
    show(@"Hello, {name}")
end

greet("Fox")
```

### 9.3 Return Values

Use `giveback` to return a value from a function.

```foxscript
submit</f.stdio>

craft add(a, b)
    giveback a + b
end

show(add(10, 20))
```

### 9.4 Recursion

Functions can call themselves.

The interpreter has a maximum function call depth of 32 to limit excessive recursion.

---

## 10. Strings

Strings are written using double quotation marks.

```foxscript
submit</f.stdio>

{name["FoxScript"]}

show(name)
```

### 10.1 String Interpolation

Use `@"..."` to insert variable values into a string.

```foxscript
submit</f.stdio>

{name["FoxScript"]}
{version[2]}

show(@"Language: {name}")
show(@"Version: {version}")
```

Variables are referenced using curly braces inside the interpolated string.

### 10.2 String Methods

The following methods are implemented:

| Method | Description |
|---|---|
| `.length()` | Returns the string length |
| `.upper()` | Converts the string to uppercase |
| `.lower()` | Converts the string to lowercase |
| `.reverse()` | Reverses the string |
| `.contains("text")` | Checks whether the string contains the specified text |
| `.clear()` | Returns an empty string |
| `.type()` | Returns the text `string` |

Example:

```foxscript
submit</f.stdio>

{name["FoxScript"]}

show(name.length())
show(name.upper())
show(name.lower())
show(name.reverse())
show(name.contains("Fox"))
show(name.clear())
show(name.type())
```

These methods are evaluated on existing string variables. They do not provide a complete object-oriented string API.

---

## 11. Built-in Modules Reference

### 11.1 Math Module

Import:

```foxscript
submit</f.math>
```

The module is accessed using `math`.

| Function or constant | Description |
|---|---|
| `math.sqrt(x)` | Square root; requires a non-negative value |
| `math.abs(x)` | Absolute value |
| `math.floor(x)` | Rounds down |
| `math.ceil(x)` | Rounds up |
| `math.round(x)` | Rounds to the nearest integer |
| `math.sin(x)` | Sine |
| `math.cos(x)` | Cosine |
| `math.tan(x)` | Tangent |
| `math.pow(x, y)` | Raises x to the power of y |
| `math.min(x, y)` | Returns the smaller value |
| `math.max(x, y)` | Returns the larger value |
| `math.pi` | Pi constant |

Example:

```foxscript
submit</f.stdio>
submit</f.math>

show(math.sqrt(144))
show(math.abs(-10))
show(math.floor(5.8))
show(math.ceil(5.2))
show(math.round(5.6))
show(math.sin(1))
show(math.cos(1))
show(math.tan(1))
show(math.pow(2, 8))
show(math.min(10, 20))
show(math.max(10, 20))
show(math.pi)
```

Trigonometric functions use radians.

### 11.2 Random Module

Import:

```foxscript
submit</f.random>
```

| Function | Description |
|---|---|
| `random.random()` | Generates a random number from 0 up to, but not including, 1 |
| `random.randint(min, max)` | Generates an integer within the specified inclusive range |

Example:

```foxscript
submit</f.stdio>
submit</f.random>

show(random.random())
show(random.randint(1, 100))
```

`randint()` requires integer bounds in ascending order. The supported range difference is limited to 100,000,000.

### 11.3 Time
