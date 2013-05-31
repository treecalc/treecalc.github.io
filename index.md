---
layout: default
title: Welcome
---


## What is TreeCalc?

TreeCalc is a programming language with focus on calculations
and business rules. The most important features are:

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
TREE mytree {
  NODE m {
    NODE maincomputes {
      NODE sub1;
      NODE sub2;
      NODE sub3 {
        NODE sub31;
        NODE sub32;
      }
    }
    NODE optional_init;
    NODE optional IF I_INCLUDE_LEVEL1 {
      NODE opt1;
      NODE opt2 IF I_include_level2;
    }
    NODE funny;
  }
  NODE n IF I_include_level1;
  NODE z {
      NODE zsub;
  }
}

INPUT I_INCLUDE_LEVEL1;
INPUT I_include_level2;
CALC mytree.n {
  P_n = "n";
}
CALC mytree.z {
    P_z = "z";
    P_fac(n) = n<=0 ? 1 : P_Fac(n-1) * n ;
    P_zsub = P_zsub;
}                   
CALC mytree.z.zsub {
    P_zsub = 1;
}
CALC mytree.m.funny {
    P_Mult(i) = i;
    P_Mult2(ind) = P_Mult(ind);
}

FUNC F_testfunny(i) = P_Mult(i);
~~~



## Language

[Grammar](lang/grammar.html)


## Compiler

[More info »](https://github.com/treecalc/compiler)  [[API Docs/Javadocs]](javadocs/compiler)


## Virtual Machine

[More info »](https://github.com/treecalc/virtual-machine)  [[API Docs/Javadocs]](javadocs/virtual-machine)


## Runtime 

[More info »](https://github.com/treecalc/runtime-java)  [[API Docs/Javadocs]](javadocs/runtime)



## Talks - Slide Decks

- [TreeCalc Virtual Machine Intro](talks/treecalc-vm-intro.html) [[PDF]](talks/treecalc-vm-intro.pdf), Stefan Neubauer, TU Wien/Fakultät für Informatik (SS 2012), 28.6.2012
- [TreeCalc Virtaul Machine Adaptive Memoization](talks/treecalc-vm-adaptive-memoization.html)  [[PDF]](talks/treecalc-vm-adaptive-memoization.pdf), Stefan Neubauer, TU Wien/Fakultät für Informatik (SS 2012),



TBD


