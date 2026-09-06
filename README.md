# Bwassembly
AyeScript is an interesting bitcode (yes, not bytecode) compiled-interpreted language designed as the first language for the BwittleVM, made for fun. It is not too akin to any specific language. This will be general overview on syntax, and features. There will also be documentation on what it compiles down to, being Bwittle Bwinary or just Bwinary, seen as ```.bwvm``` (BWittle Virtual Machine) files.

## Compilation
AyeScript is compiled using an order-of-operations line parser. The leftmost parentheses are executed first for every statement, including inside parenthetical statements. AyeScript compiles to a .acom file, containing all the .abin files generated from every function.

## Registers/ Vars
Variables in AyeScript are scope-less as of now. Later it is wanted for there to be Privates. Registers are temporary and are often replaced in the attempt to give a function input, as built in functions even take in registers. To assign the active register you use:

``0=`` - Sets Target Register to Default/0

``A=`` - Sets Target Register to A

``B=`` - Sets Target Register to B

``C=`` - Sets Target Register to C

``D=`` - Sets Target Register to D

## Special Operation Shortcuts
Commas do not designate a string, rather, they only assign the string to the active register. All variables are stored as strings in the AyeVM, however there are methods to intentionally compress further. For instance you may use:

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
