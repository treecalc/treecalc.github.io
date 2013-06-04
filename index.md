---
layout: default
title: Welcome
---


## What is TreeCalc?

TreeCalc is a programming language with a focus on calculations
and business rules. Highlights include:

* easy to learn
* no side effects
* data is part of the program
* organization of computations/rules in trees
* combination of trees into directed acyclic graph
* lists (key/value-pairs) can be attached to inputs
* automatic inference if an input is mandatory
* automatic memoization to boost performance
* translation to Java and JavaScript
* simple API
* JavaScript test calculator



Sample:

~~~
TREE carinsurance {
  NODE checks;
  NODE calculation {
    NODE car TIMES I_CarCounter {
      NODE liability;
      NODE damage IF I_Damage_YN {
        LINK damage2013;
      }
    }
  }
}

CALC carinsurance.calculation {
  RX_Prem = R_Prem 
              * 
            IF I_Discount_YN THEN
                 0.8
            ELSE
                 1
            ENDIF;
}

CALC carinsurance.calculation.car.liability {
  R_Prem = I_kw * T_Area[I_Area].fact; 
  ...
}

CALC damage2013.calculation {
  R_Prem = ...
}

TABLE T_Mortality (age, qx, qy) {
   16, 0.0006380, 0.0003980;
   17, 0.0007200, 0.0004160;
   18, 0.0007760, 0.0004060;
   19, 0.0008060, 0.0003720;
   20, 0.0008400, 0.0003580;
    ...
}

TABLE T_Liability_Sum (key, text) {
   1, "€  6.000.000,-";
   2, "€ 12.000.000,-";
}

FUNC F_LI_Lx(age, sex, risk) = 
   IF age <= 0 THEN
      100000
   ELSE
      F_LI_Lx(age - 1, sex, risk)
      * 
      (1 - F_LI_qx(age - 1, sex, risk))
   ENDIF
 ;

FUNC F_LI_qx(age, sex, riskq) = 
     sex = 1
     ? min(T_Mortality[age].qx * (1 + riskq), 1)
     : min(T_Mortality[age].qy * (1 + riskq), 1)
;
~~~


## Language - TreeCalc Source Language (`.tcs`)

[TreeCalc Language Specification](lang/spec.html) - Spec, Grammar

#### Data Types

Dynamic conversions + checks

- String
- Number
- List
- Date: String (Y-M-D, D.M.Y, M/D/Y)
- Boolean: Number (false=0, true=1)
- Internal: Function ref, Table ref, Null




## Compiler

TreeCalc Compiler - code generation for:

1. Java
2. JavaScript
3. TreeCalc Bytecode / TreeCalc Virtual Machine


[More info »](https://github.com/treecalc/compiler)  \|  [API Docs/Javadocs](javadocs/compiler)


## Virtual Machine

Sample:

~~~
case INSTR_ADD: //a b -- a+b
  stack[sp-1] = V.getInstance(stack[sp-1].doubleValue() + stack[sp].doubleValue());
  stack[sp--] = null;
  break;
~~~

[More info »](https://github.com/treecalc/virtual-machine)  \|  [API Docs/Javadocs](javadocs/virtual-machine)


### Intermediate Language (`.tci`) / TreeCalc Assembler

Sample:

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

TBD


### Instructions / Bytecode (`.tcx`) / Binary Format

TBD


## Runtime 

Standard runtime classes:

- Values: `V`, `VString`, `VDouble`, `VList`, ...
- TreeCalc standard functions
- Table access functions


[More info »](https://github.com/treecalc/runtime-java)  \|  [API Docs/Javadocs](javadocs/runtime)




## Talks - Slide Decks

- [TreeCalc Virtual Machine](talks/treecalc-vm-intro.html) [[PDF]](talks/treecalc-vm-intro.pdf), Stefan Neubauer, TU Wien/Fakultät für Informatik (SS 2012), 28.6.2012
- [Adaptive Memoization in the TreeCalc VM](talks/treecalc-vm-adaptive-memoization.html)  [[PDF]](talks/treecalc-vm-adaptive-memoization.pdf), Stefan Neubauer, TU Wien/Fakultät für Informatik (SS 2012),

TBD

