---
layout: default
title: TreeCalc Language Specification
---


## Notation

Reserved words and built-in identifers appear in __bold__.
Examples would be __TREE__ or __CALC__.

Punctuation tokens appear in quotes.
Examples would be '{' or '}' or '(' or ')'


## Language Specification / Grammar


> To Iterate Is Human, To Recurse Divine    - Peter Deutsch



compilationunit : ( def )+


def : (  
  __TREE__ nodepath '{' ( nodeinfo )* '}' |  
  __CALC__ nodepath '{' ( resultdef )* '}' |  
  __INPUT__ id ( ( '{' ( resultdef )* '}' ) | ';' ) |  
  __FUNC__ resultdef |  
  __TABLE__ id '(' colnames ')' '{' ( tableline )* '}'  
  )


nodeinfo : (
  __NODE__ nodename ( __AS__ id )? ( nodeinclusion )? ( nodetimes )? ( ';' | ( '{' ( nodeinfo )* '}' ) ) |  
  __LINK__ linkpath ';'  
  )


nodeinclusion :  
  __IF__ formula


nodetimes :  
  __TIMES__ formula __AS__ id


resultdef :  
  id ( arguments )? '=' formula ';'


arguments :  
  '(' id ( ',' id )* ')'


id :  ID




keywordAsId : (  
  __AS__ | __TABLE__ | __TREE__ | __CALC__ | __INPUT__ | __FUNC__ | __NODE__ | __IF__ | __THEN__ | __ELSE__ | __ENDIF__ | __CASE__ | __WHEN__ | __DEFAULT__ | __ENDCASE__ |
  __collate__ | __extract__ |
  __sumx__ | __prodx__ | __vectorx__ | __cell__ | __cellx__ | __exists__ | __INTERPOL__ |
  __TABCOLS__ | __TABROWS__ |
  __LOOKUP__ | __LOOKUPX__ | __LOOKDOWNX__ |
  __FUNCREF__ | __DOCCALL__ | __COUNTERLIST__  
  )


constantorid : ( constant | id );


tableline : tablecell ( ',' tablecell )* ';' -> ^( TT_TABLELINE ( tablecell )* ) ;


tablecell : ( constant | id );


nodename : ( constant | id );


nodepath : id ( DOT id )* -> ^( TT_NODEPATH ( id )* ) ;


linkpath : id ( DOT linkpart )* -> ^( TT_NODEPATH id ( linkpart )* ) ;


linkpart : ( id | STRING | ASTERISK | DOUBLEASTERISK );


colnames : colname ( ',' colname )* -> ^( TT_IDLIST ( colname )* ) ;


colname : ( id | NUMBER | STRING );


vpmsformula : formula EOF ;


formula : formula2 ( QUESTIONMARK ^ formula2 COLON ! formula2 )* ;


formula2 : formula3 ( LOGICAL_OR ^ formula3 )* ;


formula3 : formula4 ( LOGICAL_AND ^ formula4 )* ;


formula4 : formula5 ( LOGICAL_XOR ^ formula5 )* ;


formula5 : formula6 ( comparisonoperator ^ formula6 )* ;


formula6 : formula7 ( STRCAT ^ formula7 )* ;


formula7 : formula8 ( ( PLUS | MINUS ) ^ formula8 )* ;


formula8 : formula9 ( ( ASTERISK | SLASH | DIV | MOD ) ^ formula9 )* ;


formula9 : formula10 ( POWER ^ formula9 )? ;


formula10 : ( PLUS ^| MINUS ^)* expression ;


expression : (
  '(' ! formula ')' !| __sumx__ '(' id ',' formula ',' formula ',' formula ')' |  
  __prodx__ '(' id ',' formula ',' formula ',' formula ')' |  
  __vectorx__ '(' id ',' formula ',' formula ',' formula ')' |  
  __collate__ '(' id ( '(' formula ( ',' formula )* ')' )? ')' |  
  __extract__ '(' id ( '(' formula ( ',' formula )* ')' )? ',' formula ')' |  
  __cell__ '(' tableref ',' range ',' range ')' |  
  __cellx__ '(' tableref ',' range ',' range ')' |  
  __LOOKUP__ '(' tableref ',' formula ')' |  
  __LOOKUPX__ '(' tableref ',' formula ( ',' formula )* ')' |  
  __LOOKDOWNX__ '(' tableref ',' formula ( ',' formula )* ')' |  
  __exists__ '(' tableref ',' formula ( ',' formula )* ')' |  
  __INTERPOL__ '(' tableref ',' formula ')' |  
  __TABCOLS__ '(' tableref ')' |  
  __TABROWS__ '(' tableref ')' |  
  __FUNC__REF '(' formula ')' |  
  __DOCCALL__ '(' formula ( ',' formula )* ')' |  
  __COUNTERLIST__ '(' id ( ',' id )* ')' |  
  ID |  
  ID DOT id |  
  ID index ( columnaccess )? |  
  dyntable ( index ( columnaccess )? )? |  
  ID parameterListe |  
  ifstmt |  
  casestmt |  
  constant  
  )


tableref : ( id | dyntable )


range : formula ( DOTS formula )?


ifstmt :  
  __IF__ formula __THEN__ formula __ELSE__ formula __ENDIF__


casestmt : __CASE__ formula ( casewhen )* ( casedefault )? __ENDCASE__ -> ^( __CASE__ formula ( casewhen )* ( casedefault )? ) ;


casewhen : __WHEN__ casecompares COLON formula -> ^( __WHEN__ casecompares formula ) ;


casecompares : casecompare ( ',' casecompare )* -> ^( TT_CASECONDITION ( casecompare )* ) ;


casecompare :  
  ( caseconstant -> ^( TT_CASECOMPARISON caseconstant ) |  
  caseconstantnumber DOTS caseconstantnumber -> ^( TT_CASERANGE ( caseconstantnumber )* ) | STRING DOTS STRING -> ^( TT_CASERANGE ( STRING )* )  
);


casedefault : __DEFAULT__ COLON formula -> ^( __DEFAULT__ formula ) ;


caseconstant : ( STRING | caseconstantnumber );


caseconstantnumber : ( NUMBER | MINUS n= NUMBER -> NUMBER["-" + $n.getText()] );


dyntable : KEYWORD_TABREF '(' formula ')' -> formula ;


columnaccess : ( DOT id -> ^( TT_COLNAMESTATIC id ) | '(' formula ')' -> ^( TT_COLNAMEFORMULA formula ) );


comparisonoperator : ( '=' | '='_CSTYLE | COMPARE_SMALLER | COMPARE_BIGGER | COMPARE_LESSEQUAL | COMPARE_BIGGEREQUAL | COMPARE_NOTEQUAL | COMPARE_NOTEQUAL_CSTYLE | COMPARE_STR_EQUAL | COMPARE_STR_NOTEQUAL | COMPARE_STR_ALIKE | COMPARE_STR_NOTALIKE | COMPARE_STR_BEFORE | COMPARE_STR_NOTBEFORE | COMPARE_STR_AHEAD | COMPARE_STR_NOTAHEAD | COMPARE_STR_BEHIND | COMPARE_STR_NOTBEHIND | COMPARE_STR_AFTER | COMPARE_STR_NOTAFTER );


parameterListe : '(' ( formula ( ',' formula )* )? ')' -> ^( TT_PARAMETERS ( formula )* ) ;


index : BRACKET_OPEN formula ( ',' formula )* BRACKET_CLOSE -> ^( TT_INDEX ( formula )* ) ;

constant : ( STRING | NUMBER );

