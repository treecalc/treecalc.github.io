---
layout: default
title: TreeCalc Language Specification
---


## Notation

Reserved words and built-in identifers appear in __bold__.
Examples would be __TREE__ or __CALC__.

Punctuation tokens appear in quotes.
Examples would be '{' or '}' or '(' or ')'



<style>
  /**** todo: add indent for 2nd..n-th line - how? possible? ****/

  #spec p {
  }
  
  #spec p:first-line  {
     background-color:  #fffeca;  /****#8AC007;****/  /**** lime; ****/   /**** green; ****/
     font-weight: bold;
  }

 blockquote {
    border-left: 5px solid #ddd;
    color: #555;
    margin: 0 0 1em;
    padding-left: 0.6em;
  }

</style>


## Language Specification / Grammar

> To Iterate Is Human, To Recurse Divine    - Peter Deutsch


<div id='spec' markdown='1'>


compilationUnit:    
  def+


def:    
  __TREE__ nodePath '__{__' nodeInfo* '__}__'  |    
  __CALC__ nodePath '__{__' resultDef* '__}__'  |     
  __INPUT__ id ( '__{__' resultDef* '__}__' \| '__;__' )  |    
  __FUNC__ resultDef   |    
  __TABLE__ id '__(__' colNames '__)__' '__{__' tableLine* '__}__'


nodeInfo:    
  __NODE__ nodeName ( __AS__ id )? nodeInclusion? nodeTimes? ( '__;__' \| '__{__' nodeInfo* '__}__' ) |     
  __LINK__ linkPath '__;__'


nodeInclusion:    
  __IF__ formula


nodeTimes:    
  __TIMES__ formula __AS__ id


resultDef:    
  id arguments? '__=__' formula '__;__'


arguments:    
  '__(__' id ( '__,__' id )* '__)__'


id:    
  __\<ID\>__  |    
  keywordAsId



keywordAsId:    
  __AS__
 |  __TABLE__
 |  __TREE__
 |  __CALC__
 |  __INPUT__
 |  __FUNC__
 |  __NODE__
 |  __IF__
 |  __THEN__
 |  __ELSE__
 |  __ENDIF__
 |  __CASE__
 |  __WHEN__
 |  __DEFAULT__
 |  __ENDCASE__
 |  __collate__
 |  __extract__
 |  __sumx__
 |  __prodx__
 |  __vectorx__
 |  __cell__
 |  __cellx__
 |  __exists__
 |  __interpol__
 |  __tabcols__
 |  __tabrows__
 |  __lookup__
 |  __lookupx__
 |  __lookdownx__
 |  __funcref__
 |  __docall__
 |  __counterlist__


constantOrId:    
  constant  |  id


tableLine:     
  tableCell ( ',' tableCell )* ';'


tableCell:    
  constant  |  id


nodenName:    
   constant  |  id


nodePath:    
  id ( '.' id )*


linkPath:    
  id ( '.' linkPart )*


linkPart:    
  id \| __\<STRING\>__ \| '*' \| '**'


colNames:    
  colName ( ',' colName )* 


colName:    
  id \| __\<NUMBER\>__ \| __\<STRING\>__ 


vpmsFormula:    
  formula __\<EOF\>__


formula:    
  formula2 ( '__?__' formula2 '__:__' formula2 )*


formula2:    
  formula3 ( '__\|\|__' formula3 )*


formula3:    
  formula4 ( '__&&__' formula4 )*


formula4:    
  formula5 ( '__XOR__' formula5 )*


formula5:    
  formula6 ( comparisonOperator formula6 )*


formula6:    
  formula7 ( '__&__' formula7 )*


formula7:    
  formula8 ( ( '+' \| '-' ) formula8 )*


formula8:    
  formula9 ( ( '*' \| '/' \| 'DIV' \| 'MOD' ) formula9 )*


formula9:    
  formula10 ( '^' formula9 )?


formula10:    
  ( '+' \| '-' )* expression


expression:    
  '(' formula ')'  |    
  __sumx__ '(' id ',' formula ',' formula ',' formula ')'  |    
  __prodx__ '(' id ',' formula ',' formula ',' formula ')'  |    
  __vectorx__ '(' id ',' formula ',' formula ',' formula ')'  |    
  __collate__ '(' id ( '(' formula ( ',' formula )* ')' )? ')'  |    
  __extract__ '(' id ( '(' formula ( ',' formula )* ')' )? ',' formula ')'  |    
  __cell__ '(' tableRef ',' range ',' range ')'  |    
  __cellx__ '(' tableRef ',' range ',' range ')'  |    
  __lookup__ '(' tableRef ',' formula ')'  |    
  __lookupx__ '(' tableRef ',' formula ( ',' formula )* ')'  |    
  __lookdownx__ '(' tableRef ',' formula ( ',' formula )* ')'  |    
  __exists__ '(' tableRef ',' formula ( ',' formula )* ')'  |    
  __interpol__ '(' tableRef ',' formula ')'  |    
  __tabcols__ '(' tableRef ')'  |    
  __tabrows__ '(' tableRef ')'  |    
  __funcref__ '(' formula ')'  |    
  __docall__ '(' formula ( ',' formula )* ')'  |    
  __counterlist__ '(' id ( ',' id )* ')'  |    
  __\<ID\>__  |    
  __\<ID\>__ '.' id  |    
  __\<ID\>__ index columnAccess?  |    
  dynTable ( index columnAccess? )?  |    
  __\<ID\>__ parameterListe  |    
  ifStmt  |    
  caseStmt  |    
  constant


tableRef:    
  id \| dynTable


range:    
 formula ( '__..__' formula )?


ifStmt:    
  __IF__ formula __THEN__ formula __ELSE__ formula __ENDIF__


caseStmt:    
  __CASE__ formula  caseWhen*  caseDefault? __ENDCASE__


caseWhen:    
  __WHEN__ caseCompares '__:__' formula


caseCompares:    
  caseCompare ( '__,__' caseCompare )*


caseCompare:    
  caseConstant  |      
  caseConstantNumber '..' caseConstantNumber  |    
  __\<STRING\>__ '..' __\<STRING\>__


caseDefault:    
  __DEFAULT__ ':' formula


caseConstant:    
  __\<STRING\>__ | caseConstantNumber


caseConstantNumber:    
  __\<NUMBER\>__ \| '__-__' __\<NUMBER\>__


dynTable:     
  __tabref__ '__(__' formula '__)__'


columnAccess:     
  '__.__' id   |  '__(__' formula '__)__'


comparisonOperator:    
  '='
  | '=='
  |  '<'
  |  '>'
  |  '<='
  |  '>='
  |  '<>'
  |  '!='
  |  's='
  |  's<>'
  |  'si='
  |  'si<>'
  |  's<'
  |  's>='
  |  'si<'
  |  'si>='
  |  's>'
  |  's<='
  |  'si>'
  |  'si<='



parameterListe:    
  '__(__' ( formula ( '__,__' formula )* )? '__)__'


index:    
  '__\[__' formula ( ',' formula )* '__\]__'

constant:    
  __\<STRING\>__ \| __\<NUMBER\>__


</div><!-- #spec -->