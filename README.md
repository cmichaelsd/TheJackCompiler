# The Jack Compiler

### Purpose
The Jack Compiler exists for the purpose of translating the high level programming Jack into bytecode.

### Example Code
```java
class Main {
    function void main() {
        var Array a;
        var int length;
        var int i, sum;
	
	let length = Keyboard.readInt("HOW MANY NUMBERS? ");
	let a = Array.new(length);
	let i = 0;
	
	while (i < length) {
	    let a[i] = Keyboard.readInt("ENTER THE NEXT NUMBER: ");
	    let i = i + 1;
	}
	
	let i = 0;
	let sum = 0;
	
	while (i < length) {
	    let sum = sum + a[i];
	    let i = i + 1;
	}
	
	do Output.printString("THE AVERAGE IS: ");
	do Output.printInt(sum / length);
	do Output.println();
	
	return;
    }
}
```

### Grammar
|   Notation | Description                                           |
|-----------:|:------------------------------------------------------|
|  '__xxx__' | Represents language tokens that appear verbatim       |
|        xxx | Represents names of terminal and nonterminal elements |
|        ( ) | Used for grouping                                     |
| x &#124; y | Either x or y                                         |
|        x y | x is followed by y                                    |
|         x? | x appears 0 or 1 times                                |
|         x* | x appears 0 or more times                             |

|  Lexical elements | The Jack language includes five types of terminal elements (tokens)                                                                                                                                                                                                                                                                                                                                 |
|------------------:|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|           keyword | '__class__' &#124; '__constructor__' &#124; '__function__' &#124; '__method__' &#124; '__field__' &#124; '__static__' &#124; '__var__' &#124; '__int__' &#124; '__char__' &#124; '__boolean__' &#124; '__void__' &#124; '__true__' &#124; '__false__' &#124; '__null__' &#124; '__this__' &#124; '__let__' &#124; '__do__' &#124; '__if__' &#124; '__else__' &#124; '__while__' &#124; '__return__' |
|            symbol | '__{__' &#124; '__}__' &#124; '__(__' &#124; '__)__' &#124; '__\[__' &#124; '__\]__' &#124; '__.__' &#124; '__,__' &#124; '__;__' &#124; '__+__' &#124; '__-__' &#124; '__*__' &#124; '__/__' &#124; '__&__' &#124; '__&#124;__' &#124; '__<__' &#124; '__>__' &#124; '__=__' &#124; '__~__'                                                                                                        |
|   integerConstant | A decimal integer in the range of 0...32767                                                                                                                                                                                                                                                                                                                                                         |
|    StringConstant | '"' A sequence of characters not including double quote or newline '"'                                                                                                                                                                                                                                                                                                                              |
|        identifier | A sequence of letters, digits, and underscore ('_'), not starting with a digit                                                                                                                                                                                                                                                                                                                      |

| Program structure | A jack program is a collection of classes, each appearing in a separate file. The compilation unit is a class. A class is a sequence of tokens as follows |
|------------------:|:----------------------------------------------------------------------------------------------------------------------------------------------------------|
|             class | '__class__' _className_ '__{__' _classVarDec_* _subroutineDec_* '__}__'                                                                                   |
|       classVarDec | ('__static__' &#124; '__field__') _type_ _varName_ ('__,__' _varName_)* '__;__'                                                                           |
|              type | '__int__' &#124; '__char__' &#124; '__boolean__' &#124; _className_                                                                                       |
|     subroutineDec | ('__constructor__' &#124; '__function__' &#124; '__method__') ('__void__' &#124; _type_) _subroutineName_ '(' _parameterList_ ')' _subroutineBody_        |
|     parameterList | ( (_type_ _varName_) ('__,__' _type_ _varName_)* )?                                                                                                       |
|    subroutineBody | '__{__' _varDec_* _statements_ '__}__'                                                                                                                    |
|            varDec | '__var__' _type_ _varName_ ('__,__' _varName_)* '__;__'                                                                                                   |
|         className | _identifier_                                                                                                                                              |
|    subroutineName | _identifier_                                                                                                                                              |
|           varName | _identifier_                                                                                                                                              |

|      Statements |                                                                                                               |
|----------------:|:--------------------------------------------------------------------------------------------------------------|
 |      statements | _statement_*                                                                                                  |
|       statement | _letStatement_ &#124; _ifStatement_ &#124; _whileStatement_ &#124; _doStatement_ &#124; _returnStatement_     |
|    letStatement | '__let__' _varName_ ('__\[__' _expression_ '__\]__')? '__=__' _expression_ '__;__'                            |
|     ifStatement | '__if__' '__(__' _expression_ '__)__' '__{__' _statements_ '__}__' ('__else__' '__{__' _statements_ '__}__')? |
|  whileStatement | '__while__' '__(__' _expression_ '__)__' '__{__' _statements_ '__}__'                                         |
|     doStatement | '__do__' _subroutineCall_ '__;__'                                                                             |
| returnStatement | '__return__' _expression_? '__;__'                                                                            |

|     Expressions |                                                                                                                                                                                                                         |
|----------------:|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|      expression | _term (op term)_*                                                                                                                                                                                                       |
|            term | _integerConstant_ &#124; _stringConstant_ &#124; _keywordConstant_ &#124; _varName_ &#124; _varName_ '__\[__' _expression_ '__\]__' &#124; '__(__' _expression_ '__)__' &#124; (_unaryOp term_) &#124; _subroutineCall_ |
|  subroutineCall | _subroutineName_ '__(__' _expressionList_ '__)__' &#124; (_className_ &#124; _varName_) '__.__' _subroutineName_ '__(__' _expressionList_ '__)__'                                                                       |
|  expressionList | (_expression_ ('__,__' _expression_)* )?                                                                                                                                                                                |
|              op | '__+__' &#124; '__-__' &#124; '__*__' &#124; '__/__' &#124; '__&__' &#124; '__&#124;__' &#124; '__<__' &#124; '__>__' &#124; '__=__'                                                                                    |
|         unaryOp | '__-__' &#124; '__~__'                                                                                                                                                                                                  |
| keywordConstant | '__true__' &#124; '__false__' &#124; '__null__' &#124; '__this__'                                                                                                                                                       |

### Specification
This project consists of three modules which interact in order to generate either XML code or bytecode.
- Analyzer: The main program that drives the overall syntax analysis process, using the services of a Tokenizer and a CompilationEngine.
- Tokenizer: Ignores all comments and white space in the input stream and enables accessing the input one token at a time. Also, it parses and provides type of each token, as defined by the Jack grammar.
- CompilationEngine: In the syntax analyzer, the compilation engine emits a structured representation of the input source code wrapped in XML tags. In the compiler, the compilation engine will instead emit executable bytecode.

### Analyzer
No API provided for this module.

### Tokenizer
| Routine                   | Arguments           | Returns                                                                                                                                       | Function                                                                                                                                                             |
|---------------------------|---------------------|-----------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Constructor / initializer | Input file / stream | -                                                                                                                                             | Opens the input .jack file / stream and gets ready to tokenize it.                                                                                                   |
| hasMoreTokens             | -                   | boolean                                                                                                                                       | Are there more tokens in the input.                                                                                                                                  |
| advance                   | -                   | -                                                                                                                                             | Gets the next token from the input, and makes it the current token. This method should be called only if hasMoreTokens is true. Initially there is no current token. |
| tokenType                 | -                   | KEYWORD, SYMBOL, IDENTIFIER, INT_CONST, STRING_CONST                                                                                          | Returns the type of the current token, as a constant.                                                                                                                |
| keyWord                   | -                   | CLASS, METHOD, FUNCTION, CONSTRUCTOR, INT, BOOLEAN, CHAR, VOID, VAR, STATIC, FIELD, LET, DO, IF, ELSE, WHILE, RETURN, TRUE, FALSE, NULL, THIS | Returns the keyword which is the current token, as a constant. This method should be called only if tokenType is KEYWORD.                                            |
| symbol                    | -                   | char                                                                                                                                          | Returns the character which is the current token. Should be called only if tokenType is SYMBOL.                                                                      |
| identifier                | -                   | string                                                                                                                                        | Returns the string which is the current token. Should be called only if tokenType is IDENTIFIER.                                                                     |
| intVal                    | -                   | int                                                                                                                                           | Returns the integer value of the current token. Should be called only if tokenType is INT_CONST.                                                                     |
| stringVal                 | -                   | string                                                                                                                                        | Returns the string value of the current token, without the opening and closing double quotes. Should be called only if tokenType is STRING_CONST.                    |

### CompilationEngine
| Routine                   | Arguments                                | Returns | Function                                                                                                                                                                                                                                                                                                                     |
|---------------------------|------------------------------------------|---------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Constructor / initializer | Input file / stream Output file / stream | -       | Creates a new compilation engine with the given input and output. The next routine called (by the Analyzer module) must be compileClass.                                                                                                                                                                                     |
| compileClass              | -                                        | -       | Compiles a complete class.                                                                                                                                                                                                                                                                                                   |
| compileClassVarDec        | -                                        | -       | Compiles a static variable declaration, or a field declaration.                                                                                                                                                                                                                                                              |
| compileSubroutine         | -                                        | -       | Compiles a complete method, function, or constructor.                                                                                                                                                                                                                                                                        |
| compileParameterList      | -                                        | -       | Compiles a (possibly empty) parameter list. Does not handle the enclosing parentheses tokens ( and ).                                                                                                                                                                                                                        |
| compileSubroutineBody     | -                                        | -       | Compiles a subroutine's body.                                                                                                                                                                                                                                                                                                |
| compileVarDec             | -                                        | -       | Compiles a var declaration.                                                                                                                                                                                                                                                                                                  |
| compileStatements         | -                                        | -       | Compiles a sequence of statements. Does not handle the enclosing curly bracket tokens { and }.                                                                                                                                                                                                                               |
| compileLet                | -                                        | -       | Compiles a let statements.                                                                                                                                                                                                                                                                                                   |
| compileIf                 | -                                        | -       | Compiles an if statement, possible with a trailing else clause.                                                                                                                                                                                                                                                              |
| compileWhile              | -                                        | -       | Compiles a while statement.                                                                                                                                                                                                                                                                                                  |
| compileDo                 | -                                        | -       | Compiles a do statement.                                                                                                                                                                                                                                                                                                     |
| compileReturn             | -                                        | -       | Compiles a return statement.                                                                                                                                                                                                                                                                                                 |
| compileExpression         | -                                        | -       | Compiles an expression.                                                                                                                                                                                                                                                                                                      |
| compileTerm               | -                                        | -       | Compiles a term. If the current token is an identifier, the routine must resolve it into a variable, an array element, or a subroutine call. A single lookahead token, which may be [, (, or ., suffices to distinguish between the possibilities. Any other token is not part of this term and should not be advanced over. |
| compileExpressionList     | -                                        | int     | Compiles a (possibly empty) comma-separated list of expressions. Returns the number of expressions in the list.                                                                                                                                                                                                              |