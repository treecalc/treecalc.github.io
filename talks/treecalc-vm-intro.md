---
layout: default
title: TreeCalc Virtual Machine
---


# TreeCalc Virtual Machine

Stefan Neubauer

- TU Wien/Fakultät für Informatik
- Virutal Machines / SS 2012 / 185.A49 UE / 28.6.2012 

Contents:

- Setting
- Components
- Tc Intermediate Language
- Tc Assembler
- Tc Bytecode - Formulas
- Tc Virtual machine - Data
- Tc Virtual machine - Execution
- TcVM – Tree access
- Results
- Lessons learned


# Setting

- Starting point
   - TreeCalc language + Java generator
   - Test models and test data (JUnit)
- Goal
   - Virtual Machine with same functionality
   - Implementation in Java


# Components


TODO: Add pic


# Tc Assembler Generator

- Variables
   - Numbers instead of names
   - Enhance symbol tables
- Nice and useful
   - Comments in output (variable names, ...)
   - Constants handled by Assembler
- Short-circuiting and, or
   - label-handling


# Tc Intermediate Language

.tcs:

~~~
A_LI_MainLayers.compute =
  F_LI_Indexation_Perc > 0 ? F_LI_TariffDuration : 1
~~~

TODO: Add Down Arrow   Tc Assembler Generator

.tci:

~~~
.formula formula=506 simple=false ; line 3182
    //start of if statement, line 3182
    : callfunc 65 0 ; F_LI_INDEXATION_PERC
    : pushconst 0
    : cmpbig
    : iffalse L0
    : callfunc 90 0 ; F_LI_TARIFFDURATION
    : goto L1
  L0:
    : pushconst 1
  L1:
    //end of if statement
    : return
.formuladone
~~~


# Tc Assembler

- Configuration: Instructions array

       new Instruction(INSTR_CALLFUNC, "callfunc",
                       +1, -2,
                       "funcid", OPTYPE_INT,
                       "nargs", OPTYPE_BYTE
           ) //arg1 ... arg_nargs -- result

- Main action
    - Parsing (ANTLR) + Error handling
    - Formulas
        - Manage constant pool
        - Resolve labels (backpatching)
        -  Encode bytecode


# Tc Bytecode - Formulas

TODO: Add pic


# Tc Virtual machine - Data

TODO: Add pic


# Tc Virtual machine - Execution

- Decode instruction -> instrid, op1, op2, op3, op4
- switch(instrid) { ... }
- Operand stack
    - set freed elements to null -> automatic GC

          case INSTR_ADD: //a b -- a+b
             stack[sp-1] = V.getInstance(stack[sp-1].doubleValue() + stack[sp].doubleValue());
             stack[sp--] = null;
             break;

- Trace
    - PC, instruction, operands
    - status before+after instruction execution
- Base classes
    - Values: V, VString, VDouble, VList, ...
    - TreeCalc standard functions
    - Table access functions


# TcVM – Tree access

- Macro programs for tree access
    - need special instructions
         - dynamic call of formula
         - get children / linked nodes
         - check if result is defined (own/subnodes)
         - ...
    - quite long (~2 x 200 instructions)
    - hard to debug
- Alternatives
    - recursive call to TcVM machine
    - tree actions / results in extra stacks


# Results

TODO: Add pic


# Lessons learned

- Assembler
    - very useful layer
    - easy to implement

TODO: Add list/block separator < 

- Bytecode
    - compact vs. easy to handle


- Assembler generator
    - locatability of instruction names important
    - simpler than Java source code generator


- Virtual machine
    - for prototype: optimize later
    - trace output essential
    - tree handling: macro programming not best solution
