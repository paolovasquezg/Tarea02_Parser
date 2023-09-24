# Tarea02_Parser

## Autores:
* Paolo Vásquez (202110379)

## Requerimientos 

* **Modificar el scanner (en svm_parser.cpp) para aceptar comentarios de
una sola línea que empiecen con ‘% ́**

```cpp
if (c == '%'){
    while (c != '\n')
      c = nextChar();
  }
```

* **Modificar el parser (en svm_parser.cpp) para analizar todas las
instrucciones validas en SVM**
   - **parseInstruction() deberá retornar un objeto Instruction**
  ```cpp
    Instruction* Parser::parseInstruction() {
      Instruction* instr = NULL;
      string label = "";
      string jmplabel;
      int argint;
      Token::Type ttype;
      int tipo = 0;
      
      if (match(Token::LABEL)){
        label = previous->lexema;
      }
      
      if (match(Token::SKIP) || match(Token::POP) || match(Token::DUP) || match(Token::SWAP) || match(Token::ADD) || match(Token::SUB) || match(Token::MUL) || match(Token::DIV) || match(Token::PRINT))
      {
        tipo = 0;
        ttype = previous->type;
      }
      
      else if (match(Token::PUSH) || match(Token::STORE) || match(Token::LOAD))
      {
        tipo = 1;
        ttype = previous->type;
      
        if (!match(Token::NUM)){
          cout << "Expecting number" << endl;
          exit(0);
        }
      
        argint = stoi(previous->lexema);
      
      }
      
      else if (match(Token::JMPEQ) || match(Token::JMPGT) || match(Token::JMPGE) || match(Token::JMPLT) || match(Token::JMPLE) || match(Token::GOTO))
      { 
        tipo = 2;
        ttype = previous->type;
      
        if (!match(Token::ID)){
          cout << "Expecting jump label" << endl;
          exit(0);
        }
      
        jmplabel = previous->lexema;
      }
      else
      {
        cout << "Error: no pudo encontrar match para " << current << endl;  
        exit(0);
      }
      if (!match(Token::EOL)) {
      
        if (current->type != 5){
          cout << "Esperaba fin de linea" << endl;
          exit(0);
        }
      }
      else{
        while (match(Token::EOL)){
          current = scanner->nextToken();
        }
      }
      
      if (tipo == 0) {
        instr = new Instruction(label, Token::tokenToIType(ttype));
      } else if (tipo == 1) {
        instr = new Instruction(label, Token::tokenToIType(ttype), argint);
      } else { //
        instr = new Instruction(label, Token::tokenToIType(ttype), jmplabel);
      }
             
      return instr;
      }
  ```
  
  -  **Modificar la funcion tokenToIType para considerar todos los tokens**
  
  ```cpp
  Instruction::IType Token::tokenToIType(Token::Type tt) {
    Instruction::IType itype;
    switch (tt) {
    case(Token::PUSH): itype = Instruction::IPUSH; break;
    case(Token::POP): itype = Instruction::IPOP; break;
    case(Token::DUP): itype = Instruction::IDUP; break;
    case(Token::SWAP): itype = Instruction::ISWAP; break;
    case(Token::ADD): itype = Instruction::IADD; break;
    case(Token::SUB): itype = Instruction::ISUB; break;
    case(Token::MUL): itype = Instruction::IMUL; break;
    case(Token::DIV): itype = Instruction::IDIV; break;
    case(Token::GOTO): itype = Instruction::IGOTO; break;
    case(Token::JMPEQ): itype = Instruction::IJMPEQ; break;
    case(Token::JMPGT): itype = Instruction::IJMPGT; break;
    case(Token::JMPGE): itype = Instruction::IJMPGE; break;
    case(Token::JMPLT): itype = Instruction::IJMPLT; break;
    case(Token::JMPLE): itype = Instruction::IJMPLE; break;
    case(Token::SKIP): itype = Instruction::ISKIP; break;
    case(Token::STORE): itype = Instruction::ISTORE; break;
    case(Token::LOAD): itype = Instruction::ILOAD; break;
    case(Token::PRINT): itype = Instruction::IPRINT; break;
    default: cout << "Error: Unknown Keyword type" << endl; exit(0);
    }
    return itype;
  ```
  

  - **Completar el archivo factorial.svm con una implementación de la función factorial. El programa deberá calcular el factorial del numero arriba de la pila el numero colocado por la instrucción push**
  ```cpp
  % Nombre, apellido
  % Nombre, apellido
  % Nombre, apellido
  push 6
  dup
  L1: push 1
  sub
  dup
  store 1
  mul
  load 1
  dup
  push 1
  jmpgt L1
  pop
  ```

## Especificaciones

Para compilar el parser

```cpp
g++ svm_run.cpp svm.cpp svm_parser.cpp
```

Para ejecutarlo con alguno de los archivos **.svm**

```cpp
./a.out (ejemplo0.svm|ejemplo1.svm|ejemplo2.svm|factorial.svm)
```

Por ejemplo, en caso de **factorial.svm**, probandolo con el número 6, se obtendra lo siguiente:

```
Reading program from file factorial.svm
Program:
push 6
dup 
L1: push 1
sub 
dup 
store 1
mult 
load 1
dup 
push 1
jmpgt L1
pop 
----------------
Running ....
Finished
stack [ 720 ]
```
