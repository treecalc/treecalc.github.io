
## Notation

Reserved words and built-in identifers appear in __bold__.
Examples would be __TREE__ or __CALC__.

Punctuation tokens appear in quotes.
Examples would be '{' or '}' or '(' or ')'


## Language Spec / Grammar


compilationunit : ( def )+ -> ^( TT_COMPUNIT ( def )* ) ;


def : (
  __TREE__ nodepath '{' ( nodeinfo )* '}' -> ^( __TREE__ nodepath ( nodeinfo )* ) |  
  __CALC__ nodepath '{' ( resultdef )* '}' -> ^( __CALC__ nodepath ( resultdef )* ) |  
  __INPUT__ id ( ( '{' ( resultdef )* '}' ) | ';' ) -> ^( __INPUT__ id ( resultdef )* ) |  
  __FUNC__ resultdef -> ^( __FUNC__ resultdef ) |  
  __TABLE__ id '(' colnames ')' '{' ( tableline )* '}' -> ^( __TABLE__ id colnames ( tableline )* ) );


nodeinfo :
  ( __NODE__ nodename ( __AS__ id )? ( nodeinclusion )? ( nodetimes )? ( ';' | ( '{' ( nodeinfo )* '}' ) ) -> ^( __NODE__ nodename ( ^( __AS__ id ) )? ( nodeinclusion )? ( nodetimes )? ( nodeinfo )* ) | __LINK__ linkpath ';' -> ^( __LINK__ linkpath ) );


nodeinclusion : kw= KEYWORD_IF formula -> ^( TT_INCLUSIONFORMULA[$kw] formula ) ;


nodetimes : kw= KEYWORD_TIMES formula __AS__ id -> ^( TT_TIMESINFO[$kw] formula id ) ;


resultdef : id ( arguments )? COMPARE_EQUAL formula ';' -> ^( TT_RESULTDEF id ( arguments )? formula ) ;


arguments : '(' id ( COMMA id )* ')' -> ^( TT_ARGDEF ( id )* ) ;


id : ( ID -> ID |kw= keywordAsId -> ID[((CommonTree)kw.tree).getText()] );


keywordAsId : ( __AS__ | __TABLE__ | __TREE__ | __CALC__ | __INPUT__ | __FUNC__ | __NODE__ | KEYWORD_IF | KEYWORD_THEN | KEYWORD_ELSE | KEYWORD_ENDIF | KEYWORD_CASE | KEYWORD_WHEN | KEYWORD_DEFAULT | KEYWORD_ENDCASE | KEYWORD_COLLATE | KEYWORD_EXTRACT | KEYWORD_SUMX | KEYWORD_PRODX | KEYWORD_VECTORX | KEYWORD_CELL | KEYWORD_CELLX | KEYWORD_EXISTS | KEYWORD_INTERPOL | KEYWORD_TABCOLS | KEYWORD_TABROWS | KEYWORD_LOOKUP | KEYWORD_LOOKUPX | KEYWORD_LOOKDOWNX | __FUNC__REF | KEYWORD_DOCALL | KEYWORD_COUNTERLIST );


constantorid : ( constant | id );


tableline : tablecell ( COMMA tablecell )* ';' -> ^( TT_TABLELINE ( tablecell )* ) ;


tablecell : ( constant | id );


nodename : ( constant | id );


nodepath : id ( DOT id )* -> ^( TT_NODEPATH ( id )* ) ;


linkpath : id ( DOT linkpart )* -> ^( TT_NODEPATH id ( linkpart )* ) ;


linkpart : ( id | STRING | ASTERISK | DOUBLEASTERISK );


colnames : colname ( COMMA colname )* -> ^( TT_IDLIST ( colname )* ) ;


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


expression : ( '(' ! formula ')' !| KEYWORD_SUMX '(' id COMMA formula COMMA formula COMMA formula ')' -> ^( KEYWORD_SUMX id ( formula )* ) | KEYWORD_PRODX '(' id COMMA formula COMMA formula COMMA formula ')' -> ^( KEYWORD_PRODX id ( formula )* ) | KEYWORD_VECTORX '(' id COMMA formula COMMA formula COMMA formula ')' -> ^( KEYWORD_VECTORX id ( formula )* ) | KEYWORD_COLLATE '(' id ( '(' formula ( COMMA formula )* ')' )? ')' -> ^( KEYWORD_COLLATE id ( formula )* ) | KEYWORD_EXTRACT '(' id ( '(' formula ( COMMA formula )* ')' )? COMMA formula ')' -> ^( KEYWORD_EXTRACT id ( formula )* ) | KEYWORD_CELL '(' tableref COMMA range COMMA range ')' -> ^( KEYWORD_CELL tableref ( range )* ) | KEYWORD_CELLX '(' tableref COMMA range COMMA range ')' -> ^( KEYWORD_CELLX tableref ( range )* ) | KEYWORD_LOOKUP '(' tableref COMMA formula ')' -> ^( KEYWORD_LOOKUP tableref ( formula )* ) | KEYWORD_LOOKUPX '(' tableref COMMA formula ( COMMA formula )* ')' -> ^( KEYWORD_LOOKUPX tableref ( formula )* ) | KEYWORD_LOOKDOWNX '(' tableref COMMA formula ( COMMA formula )* ')' -> ^( KEYWORD_LOOKDOWNX tableref ( formula )* ) | KEYWORD_EXISTS '(' tableref COMMA formula ( COMMA formula )* ')' -> ^( KEYWORD_EXISTS tableref ( formula )* ) | KEYWORD_INTERPOL '(' tableref COMMA formula ')' -> ^( KEYWORD_INTERPOL tableref formula ) | KEYWORD_TABCOLS '(' tableref ')' -> ^( KEYWORD_TABCOLS tableref ) | KEYWORD_TABROWS '(' tableref ')' -> ^( KEYWORD_TABROWS tableref ) | __FUNC__REF '(' formula ')' -> ^( __FUNC__REF formula ) | KEYWORD_DOCALL '(' formula ( COMMA formula )* ')' -> ^( KEYWORD_DOCALL ( formula )* ) | KEYWORD_COUNTERLIST '(' id ( COMMA id )* ')' -> ^( KEYWORD_COUNTERLIST ( id )* ) | ID -> ^( TT_USEID ID ) | ID DOT id -> ^( TT_INPUTCALCCALLSIMPLE ID id ) | ID index ( columnaccess )? -> ^( TT_INPUTORTABACCESSWITHINDEX ID index ( columnaccess )? ) | dyntable ( index ( columnaccess )? )? -> ^( TT_DYNTABLE dyntable ( index )? ( columnaccess )? ) | ID parameterListe -> ^( TT_FUNORCALCCALL ID parameterListe ) | ifstmt | casestmt | constant );


tableref : ( id | dyntable );


range : formula ( DOTS formula )? -> ^( TT_RANGE ( formula )* ) ;


ifstmt : KEYWORD_IF formula KEYWORD_THEN formula KEYWORD_ELSE formula KEYWORD_ENDIF -> ^( KEYWORD_IF ( formula )* ) ;


casestmt : KEYWORD_CASE formula ( casewhen )* ( casedefault )? KEYWORD_ENDCASE -> ^( KEYWORD_CASE formula ( casewhen )* ( casedefault )? ) ;


casewhen : KEYWORD_WHEN casecompares COLON formula -> ^( KEYWORD_WHEN casecompares formula ) ;


casecompares : casecompare ( COMMA casecompare )* -> ^( TT_CASECONDITION ( casecompare )* ) ;


casecompare : ( caseconstant -> ^( TT_CASECOMPARISON caseconstant ) | caseconstantnumber DOTS caseconstantnumber -> ^( TT_CASERANGE ( caseconstantnumber )* ) | STRING DOTS STRING -> ^( TT_CASERANGE ( STRING )* ) );


casedefault : KEYWORD_DEFAULT COLON formula -> ^( KEYWORD_DEFAULT formula ) ;


caseconstant : ( STRING | caseconstantnumber );


caseconstantnumber : ( NUMBER | MINUS n= NUMBER -> NUMBER["-" + $n.getText()] );


dyntable : KEYWORD_TABREF '(' formula ')' -> formula ;


columnaccess : ( DOT id -> ^( TT_COLNAMESTATIC id ) | '(' formula ')' -> ^( TT_COLNAMEFORMULA formula ) );


comparisonoperator : ( COMPARE_EQUAL | COMPARE_EQUAL_CSTYLE | COMPARE_SMALLER | COMPARE_BIGGER | COMPARE_LESSEQUAL | COMPARE_BIGGEREQUAL | COMPARE_NOTEQUAL | COMPARE_NOTEQUAL_CSTYLE | COMPARE_STR_EQUAL | COMPARE_STR_NOTEQUAL | COMPARE_STR_ALIKE | COMPARE_STR_NOTALIKE | COMPARE_STR_BEFORE | COMPARE_STR_NOTBEFORE | COMPARE_STR_AHEAD | COMPARE_STR_NOTAHEAD | COMPARE_STR_BEHIND | COMPARE_STR_NOTBEHIND | COMPARE_STR_AFTER | COMPARE_STR_NOTAFTER );


parameterListe : '(' ( formula ( COMMA formula )* )? ')' -> ^( TT_PARAMETERS ( formula )* ) ;


index : BRACKET_OPEN formula ( COMMA formula )* BRACKET_CLOSE -> ^( TT_INDEX ( formula )* ) ;

constant : ( STRING | NUMBER );

