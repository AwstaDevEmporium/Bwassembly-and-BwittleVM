# Bwassembly
Bwassembly is an interesting bitcode (yes, not bytecode) low-level compiled-interpreted language designed as the first language for the BwittleVM, made for fun. It is not too akin to any specific language. This will be general overview on syntax, and features. There will also be documentation on what it compiles down to, being Bwittle Bwinary or just Bwinary, seen as ```.bwvm``` (BWittle Virtual Machine) files. Why is it called Bwittle? Because it is 'Little Bits', it has very small compiled output and the BWVM is only about 1 megabyte, and it has plenty room for more features via the application of DLLs. Note Bwassembly functions and variables are compiled down to integers. Later higher-level languages are planned to be made that compile into Bwassembly. Later on it is planned to double the amount of Opcodes by swapping id length from 5 -> 6 (already done as of now), this would allow more specificity (such as replacing SwapRegister with variants in regards to each register) which would actually, in a backwards-ish way, make code even SHORTER. ***NOTE: ANYONE WHO MAKES A SMALLER FIBBO SCRIPT THAT DOES THE SAME THING AS MINE GETS A BIG PRIZE THINGY, current record is 32 bytes.***

## Compilation
Bwassembly is compiled using an order-of-operations line parser. The leftmost parentheses are executed first for every statement, including inside parenthetical statements. Bwassembly compiles to many ```.bwvm``` files corresponding to every function. These are lazily loaded and cached by the BwittleVM. To further optimize the compiled output, a 3-byte config is in the project's bin directory. This contains instructions for the VM to interpret your code. Without it, your code is uninterpretable. The format of this is as such:
```
First 4 bits represent - VariableID bit width
Next 4 bits represent - StringByteLengthID bit width
Next 7 bits represent - Integer bit width
Next 4 bits represent - FixedDeciPos bit width
Next 5 bits represent - FunctionID bit width
```
Later this might be incorporated into the PROGRAM file, so that it benefits from the bitpacking.

## CompileTime Assets (files)
Using the ``#AddCTAsset`` and specifying a path and then a name, the file will be added to a folder in the compiled program with a number as its name. You can get the number from within the code by using ``CTAsset AssetName``, which is resolved at compiletime. These can be used interchangably with paths in official DLLs and in the VM.

## Runtime dynamic storage v. Compiletime static storage
When your making shtuff, be careful. ``SetVar``, ``$``, ``If [varname]``, ``While [varname]``, and etc all use variable names thatre resolved to numbers at compiletime. Meaning you cannot dynamically access these. The solution is pointers, as pointers allow you to manage runtime variable storage directly w/o compiler abstraction. Now, these are primarily used in classes/dynamically-generated classes as class members are determined by offset. Classes also are purely compile time and do not exist in the VM, and can only be used in CompileTimeConstruct creation. However for simplicity dynamically created/ runtime/ pointer-built class-likes thatre compatible with their compiletime class equivalents are also called classes. class set example:
```
Function Vector3.CreateDynamic
	SetVar TempX
	SetVar TempY (0=)(RegA)
	SetVar TempZ (0=)(RegB)

	// get novel pointer a the top of the variable list/stack thing
	(G=)(FrontierPointer)
	
	Add (A=)(RegG) (B=)(#"1") (F=)
	SetValAtPoint (0=)(RegF) (A=)($ TempX)

	Add (A=)(RegG) (B=)(#"2") (F=)
	SetValAtPoint (0=)(RegF) (A=)($ TempY)
	
	Add (A=)(RegG) (B=)(#"3") (F=)
	SetValAtPoint (0=)(RegF) (A=)($ TempZ)
	
	(0=)(RegG)
EndFunction

Class Vector3
	SetVar <This>
	SetVar <This>.x
	SetVar <This>.y (0=)(RegA)
	SetVar <This>.z (0=)(RegB)
	
	// takes input of a pointer to another Vec3 class's base <This> var in reg0
	DynamicFunction <This>.Add
		// store the vec3 pointer in register G
		(G=)(Reg0)
		
		Add (A=)(RegG) (B=)(#"1") (F=)

		GetValAtPoint (0=)(RegF) (A=)		
		SetVar <This>.x ( Add (B=)($ <This>.x) (0=) )
		
		Add (A=)(RegG) (B=)(#"2") (F=)
		
		GetValAtPoint (0=)(RegF) (A=)
		SetVar <This>.y ( Add (B=)($ <This>.y) (0=) )
		
		Add (A=)(RegG) (B=)(#"3") (F=)
		
		GetValAtPoint (0=)(RegF) (A=)
		SetVar <This>.z ( Add (B=)($ <This>.z) (0=) )
	EndDynamicFunction
	
	DynamicFunction <This>.Sub
		// store the vec3 pointer in register G
		(G=)(Reg0)
		
		Add (A=)(RegG) (B=)(#"1") (F=)

		GetValAtPoint (0=)(RegF) (A=)		
		SetVar <This>.x ( Sub (B=)($ <This>.x) (0=) )
		
		Add (A=)(RegG) (B=)(#"2") (F=)
		
		GetValAtPoint (0=)(RegF) (A=)
		SetVar <This>.y ( Sub (B=)($ <This>.y) (0=) )
		
		Add (A=)(RegG) (B=)(#"3") (F=)
		
		GetValAtPoint (0=)(RegF) (A=)
		SetVar <This>.z ( Sub (B=)($ <This>.z) (0=) )
	EndDynamicFunction
	
	DynamicFunction <This>.Multiply
		// store the vec3 pointer in register G
		(G=)(Reg0)
		
		Add (A=)(RegG) (B=)(#"1") (F=)

		GetValAtPoint (0=)(RegF) (A=)		
		SetVar <This>.x ( Multiply (B=)($ <This>.x) (0=) )
		
		Add (A=)(RegG) (B=)(#"2") (F=)
		
		GetValAtPoint (0=)(RegF) (A=)
		SetVar <This>.y ( Multiply (B=)($ <This>.y) (0=) )
		
		Add (A=)(RegG) (B=)(#"3") (F=)
		
		GetValAtPoint (0=)(RegF) (A=)
		SetVar <This>.z ( Multiply (B=)($ <This>.z) (0=) )
	EndDynamicFunction
	
	DynamicFunction <This>.Divide
		// store the vec3 pointer in register G
		(G=)(Reg0)
		
		Add (A=)(RegG) (B=)(#"1") (F=)

		GetValAtPoint (0=)(RegF) (A=)		
		SetVar <This>.x ( Divide (B=)($ <This>.x) (0=) )
		
		Add (A=)(RegG) (B=)(#"2") (F=)
		
		GetValAtPoint (0=)(RegF) (A=)
		SetVar <This>.y ( Divide (B=)($ <This>.y) (0=) )
		
		Add (A=)(RegG) (B=)(#"3") (F=)
		
		GetValAtPoint (0=)(RegF) (A=)
		SetVar <This>.z ( Divide (B=)($ <This>.z) (0=) )
	EndDynamicFunction
	
	// takes input of a pointer leading to a vector3 class-equiv, whether dynamically made or CTConstruct
	// useful if you want to interface a runtime/dynamic class
	// like how you with these CompileTimeConstructs, temporarily
	DynamicFunction <This>.SetMeTo
		(G=)(Reg0)
		
		Add (A=)(RegG) (B=)(#"1") (F=)

		GetValAtPoint (0=)(RegF) (0=)		
		SetVar <This>.x
		
		Add (A=)(RegG) (B=)(#"2") (F=)

		GetValAtPoint (0=)(RegF) (0=)		
		SetVar <This>.y
		
		Add (A=)(RegG) (B=)(#"3") (F=)

		GetValAtPoint (0=)(RegF) (0=)		
		SetVar <This>.z
	EndDynamicFunction
	
	DynamicFunction <This>.SetItTo
		(G=)(Reg0)
		
		Add (A=)(RegG) (B=)(#"1") (F=)

		SetValAtPoint (0=)(RegF) (A=)($ <This>.x)
		
		Add (A=)(RegG) (B=)(#"2") (F=)

		SetValAtPoint (0=)(RegF) (A=)($ <This>.y)
		
		Add (A=)(RegG) (B=)(#"3") (F=)

		SetValAtPoint (0=)(RegF) (A=)($ <This>.z)
	EndDynamicFunction
EndClass
```

## Registers/ Vars
Variables in Bwassembly are scope-less. Registers are temporary and are often replaced in the attempt to give a function input, as built in functions even take in registers. To assign the active register you use:

``0=`` - Sets Target Register to Default/0, used for all operations that use only 1 operand

``A=`` - Sets Target Register to A, used for all operations that use 2 operands as the first operand

``B=`` - Sets Target Register to B, used for all operations that use 2 operands as the second operand

``C=`` - Sets Target Register to C

``D=`` - Sets Target Register to D

``E=`` - Sets Target Register to E

``F=`` - Sets Target Register to F

``G=`` - Sets Target Register to G

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

#compile
```

This, as PrintLine takes in Register Default/0, outputs the string "100"

# Bwinary and Bwessembly Instructions

```
GetInput = 0;
SetTempToVar = 1;
SetTempToString = 2;
PrintTemp = 3;
PrintLnTemp = 4;
SetVariableToTemp = 5;
RegisterToDefault = 6;
RegisterToA = 7;
RegisterToB = 8;
RegisterToC = 9;
RegisterToD = 10;
SetTempToInt = 11;
SetTempToFixedDeci = 12;
ExecuteFunction = 13;
IssueWhileReg = 14;
EndWhileOrIf = 15;
SetTempToBool = 16;
BreakWhile = 17;
IssueIfReg = 18;
IssueWhileVar = 19;
IssueIfVar = 20;
IssueIfNotVar = 21;
IssueIfNotReg = 22;
IssueWhileNotVar = 23;
IssueWhileNotReg = 24;
Add = 25;
Subtract = 26;
Multiply = 27;
Divide = 28;
SwapRegisterDefault = 29; // we have to make this take extra args :(
VarRemove = 30;
ExternalLibrary = 31;
UnloadFunction = 32;
SwapRegisterA = 33;
SwapRegisterB = 34;
SwapRegisterC = 35;
SwapRegisterD = 36; 
RegGreaterThan = 37; // takes in Register::A and Register::B and then checks if A is greatuh than B via turning them to floats
RegLessThan = 38; // takes in Register::A and Register::B and then checks if A is less than B via turning them to floats
RegEqualTo = 39; // takes in Register::A and Register::B and then checks if both are equal stringwise
RegOr = 40;  // takes in Register::A and Register::B and then checks if either is equal to "True"
SwapRegisterE = 41;
SwapRegisterF = 42;
RegisterToE = 43;
RegisterToF = 44;
SwapRegisterG = 45;
RegisterToG = 46; 
RegNot = 47; // takes in Register::0 and then reverses its Booleanarity
SegmentString = 48; // takes a segment of a string in Reg::0 and outputs it to selected register. arg1 is an integer, in Reg::A
LoadLibrary = 49;
UnloadLibrary = 50;
Delay = 51;
GetInput = 52;
ExecuteFunctionAsync = 53;
SetLocalVariableToTemp = 54;
SetTempToLocalVariable = 55;
DynamicVarSetToTemp = 56; // uses pointers
DynamicVarGetToTemp = 57;
GetNewPointer = 58;
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
SetTempToFixedDeci - #"[fixed decimal in here]" (same syntax as ToInt, the compiler infers which your trying to do)
ExecuteFunction - Invoke [FunctionName] | [FunctionName] 
IssueWhileReg - While [Reg0 | RegA | RegB | RegC | RegD | RegE | RegF | RegG] (uses Register0 to hold the value that is considered to make the statemnet 'true')
EndWhileOrIf  - EndWhile | EndIf 
SetTempToBool - True | False
BreakWhile - Break (also breaks if statements)
IssueIfReg - If [Reg0 | RegA | RegB | RegC | RegD | RegE | RegF | RegG] (same as a loop but auto breaks at the end)
IssueWhileVar - While [varname] (same as while but uses a variable)
IssueIfVar - If [varname] (ditto-kinda)
IssueIfNotVar - If-Not [varname]
IssueIfNotReg - If-Not [Reg0 | RegA | RegB | RegC | RegD | RegE | RegF | RegG]
IssueWhileNotVar - While-Not [varname]
IssueWhileNotReg - While-Not [Reg0 | RegA | RegB | RegC | RegD | RegE | RegF | RegG]
Add - Add (adds the value of Register A and Register B together if both can be converted to floats at runtime, if not, it concats them. outputs to selected register)
Subtract - Subtract (ditto)
Multiply - Multiply (ditto)
Divide - Divide (ditto)
SwapRegister - Reg0 | RegA | RegB | RegC | RegD | RegE | RegF | RegG (sets active register to the value of another register)
VarRemove - DestroyVar [varname]
UnloadFunction - UnloadFunc [functionname]
ExternalLibrary - tbd
RegNot - Not (takes in value in Reg0 and outputs it to the active register)
RegOr - Or (takes in a value in RegA and one in RegB and sets its result to the active register)
RegGreaterThan - GreaterThan (ditto)
RegLessThan - LessThan (ditto)
RegEqualTo - Equal (ditto)
GetNewPointer - FrontierPointer
DynamicVarSetToTemp - SetValAtPoint (register 0 is the pointer number, and register A is the value)
DynamicVarGetToTemp - GetValAtPoint (register 0 is the pointer number)
```

And things thatre purely compiletime:
```
CompileTimeConstruct [classname] [name] - creates a static storage compile-time class, as opposed to dynamic classes/objects made with pointers
Class [classname]/ EndClass - all code within these is stored as a string, and when CompileTimeConstruct is called all instances of <This> in the string will be replaced with the name of the object being made
Function [functionname]/ EndFunction - determines bounds of a function
DynamicFunction [functionname]/ EndDynamicFunction - determines bounds of a function that is a compiled like an instruction, meaning, it cannot be accessed before its defined. Used primary for functions in compiletime Classes.
#append [filename] - appends the bwasm code in this file to the compiler
#compile - MAJOR KEYWORD, indicates fro the compiler to finish compiling
Pointer [varname] - on compiletime itll replace this with the pointer value of the varname
```

# Examples:

```
Function Program
	PrintLine (0=)(#"-10")
	While-Not RegA (0=)(True)
	
	EndWhile
EndFunction

#compile
```

Compiles to: ```000110001011100000000000010100001000001100100001011000001001111``` w/ 3BConfig of ```011111110001111011100111```
