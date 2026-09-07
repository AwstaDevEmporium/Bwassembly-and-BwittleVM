# Bwassembly
Bwassembly is an interesting bitcode (yes, not bytecode) low-level compiled-interpreted language designed as the first language for the BwittleVM, made for fun. It is not too akin to any specific language. This will be general overview on syntax, and features. There will also be documentation on what it compiles down to, being Bwittle Bwinary or just Bwinary, seen as ```.bwvm``` (BWittle Virtual Machine) files. Why is it called Bwittle? Because it is 'Little Bits', it has very small compiled output and the BWVM is only about 1 megabyte, and it has plenty room for more features via the application of DLLs. Note Bwassembly functions and variables are compiled down to integers. Later higher-level languages are planned to be made that compile into Bwassembly. Later on it is planned to double the amount of Opcodes by swapping id length from 5 -> 6, this would allow more specificity (such as replacing SwapRegister with variants in regards to each register) which would actually, in a backwards-ish way, make code even SHORTER. ***NOTE: ANYONE WHO MAKES A SMALLER FIBBO SCRIPT THAT DOES THE SAME THING AS MINE GETS A BIG PRIZE THINGY***

## Compilation
Bwassembly is compiled using an order-of-operations line parser. The leftmost parentheses are executed first for every statement, including inside parenthetical statements. Bwassembly compiles to many ```.bwvm``` files corresponding to every function. These are lazily loaded and cached by the BwittleVM. To further optimize the compiled output, a 3-byte config is in the project's bin directory. This contains instructions for the VM to interpret your code. Without it, your code is uninterpretable. The format of this is as such:
```
First 4 bits represent - VariableID bit width
Next 4 bits represent - StringByteLengthID bit width
Next 7 bits represent - Integer bit width
Next 4 bits represent - FixedDeciPos bit width
Next 5 bits represent - FunctionID bit width
```

## Registers/ Vars
Variables in Bwassembly are scope-less. Registers are temporary and are often replaced in the attempt to give a function input, as built in functions even take in registers. To assign the active register you use:

``0=`` - Sets Target Register to Default/0

``A=`` - Sets Target Register to A

``B=`` - Sets Target Register to B

``C=`` - Sets Target Register to C

``D=`` - Sets Target Register to D

## Special Operation Shortcuts
Commas do not JUST designate a string, rather, they assign the string to the active register. All variables are stored as strings in the BwittleVM, however there are methods to intentionally compress further. For instance you may use:

``#"123"``

This sets register to a string that is 123. The difference is how it is compressed into the binary. In this case it is stored as an integer.

``#"123.0"``

And this does similar, but instead stores it as an integer and a fixed decimal integer too.

## Script Example
```
Function Program
	PrintLine (0=)(#"100")
EndFunction
```

This, as PrintLine takes in Register Default/0, outputs the string "100"

# Bwinary and Bwessembly Instructions

```
GetInput = 0
SetTempToVar = 1
SetTempToString = 2
PrintTemp = 3
PrintLnTemp = 4
SetVariableToTemp = 5
RegisterToDefault = 6
RegisterToA = 7
RegisterToB = 8
RegisterToC = 9
RegisterToD = 10
SetTempToInt = 11
SetTempToFixedDeci = 12
ExecuteFunction = 13
IssueWhileReg = 14
EndWhileOrIf = 15
SetTempToBool = 16
BreakWhile = 17
IssueIfReg = 18
IssueWhileVar = 19
IssueIfVar = 20
IssueIfNotVar = 21
IssueIfNotReg = 22
IssueWhileNotVar = 23
IssueWhileNotReg = 24
Add = 25
Subtract = 26
Multiply = 27
Divide = 28
SwapRegister = 29 
VarRemove = 30 (also used to unload functions thatre lazyloaded)
ExternalLibrary = 31
```

Equivalents in Bwessembly:

```
GetInput - GetInput (sets the current register to the result)
SetTempToVar - $ [VarName] (sets the current register to the variable's info)
SetTempToString - "[String inside here]" 
PrintTemp - Print (prints the string held in the active register)
PrintLnTemp - PrintLine (same as Print but prints a line)
SetVariableToTemp - SetVar [VarName] (sets the labeled var to the current register's value)
RegisterToDefault - 0= (all of these RegisterTos set the active register)
RegisterToA - A= 
RegisterToB - B=
RegisterToC - C=
RegisterToD - D=
SetTempToInt - #"[integer in here]" (the active register will STILL BE A STRING, the difference is the string is encoded as an integer)
SetTempToFixedDeci - #"[fixed decimal in here" (same syntax as ToInt, the compiler infers which your trying to do)
ExecuteFunction - Invoke [FunctionName] | [FunctionName] 
IssueWhileReg - While [Reg0 | RegA | RegB | RegC | RegD] (uses Register0 to hold the value that is considered to make the statemnet 'true')
EndWhileOrIf  - EndWhile | EndIf 
SetTempToBool - True | False
BreakWhile - Break (also breaks if statements)
IssueIfReg - If [Reg0 | RegA | RegB | RegC | RegD] (same as a loop but auto breaks at the end)
IssueWhileVar - While [varname] (same as while but uses a variable)
IssueIfVar - If [varname] (ditto-kinda)
IssueIfNotVar - If-Not [varname]
IssueIfNotReg - If-Not [Reg0 | RegA | RegB | RegC | RegD]
IssueWhileNotVar - While-Not [varname]
IssueWhileNotReg - While-Not [Reg0 | RegA | RegB | RegC | RegD]
Add - Add (adds the value of Register A and Register B together if both can be converted to floats at runtime, if not, it concats them. outputs to selected register)
Subtract - Subtract (ditto)
Multiply - Multiply (ditto)
Divide - Divide (ditto)
SwapRegister - Reg0 | RegA | RegB | RegC | RegD (sets active register to the value of another register)
VarRemove - DestroyVar [varname]
			UnloadFunc [functionname]
ExternalLibrary - tbd
```

# Examples:

```
Function Program
	PrintLine (0=)(#"-10")
	While-Not RegA (0=)(True)
	
	EndWhile
EndFunction
```

Compiles to: ```000110001011100000000000010100001000001100100001011000001001111``` w/ 3BConfig of ```011111110001111011100111```
