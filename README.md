# Python-Bytecode
Documentation about Python bytecode

## A note on Python versions

Most of the examples here use python 3.10.8.   
From 3.11 the format of bytecode really changes - there are new CACHE spaces in the bytecode and other performance improvements. This is an added complexity which make learning the actual bytecode harder, so is avoided for most of this discussion. The relevant PEP is PEP 659 – Specializing Adaptive Interpreter (https://peps.python.org/pep-0659/)    

## Useful links for information

### Books

CPython Internals Book  (https://realpython.com/products/cpython-internals-book/)    
Inside The Python Virtual Machine (https://leanpub.com/insidethepythonvirtualmachine/read)    


### Websites

#### Python innards series

https://tech.blog.aknin.name/category/my-projects/pythons-innards/    
https://tech.blog.aknin.name/2010/04/02/pythons-innards-introduction/    
https://tech.blog.aknin.name/2010/05/12/pythons-innards-objects-101/    
https://tech.blog.aknin.name/2010/05/19/pythons-innards-objects-102/    
https://tech.blog.aknin.name/2010/05/20/correction-for-pythons-innards-objects-102/    
https://tech.blog.aknin.name/2010/05/26/pythons-innards-pystate/    
https://tech.blog.aknin.name/2010/06/05/pythons-innards-naming/    
https://tech.blog.aknin.name/2010/07/03/pythons-innards-code-objects/    
https://tech.blog.aknin.name/2010/07/04/pythons-innards-for-my-wife/    
https://tech.blog.aknin.name/2010/07/22/pythons-innards-interpreter-stacks/    
https://tech.blog.aknin.name/2010/09/02/pythons-innards-hello-ceval-c-2/    


#### Python behind the scenes

https://tenthousandmeters.com/tag/python-behind-the-scenes/    
https://tenthousandmeters.com/blog/python-behind-the-scenes-1-how-the-cpython-vm-works/    
https://tenthousandmeters.com/blog/python-behind-the-scenes-2-how-the-cpython-compiler-works/    
https://tenthousandmeters.com/blog/python-behind-the-scenes-3-stepping-through-the-cpython-source-code/    
https://tenthousandmeters.com/blog/python-behind-the-scenes-4-how-python-bytecode-is-executed/    
https://tenthousandmeters.com/blog/python-behind-the-scenes-5-how-variables-are-implemented-in-cpython/    
https://tenthousandmeters.com/blog/python-behind-the-scenes-6-how-python-object-system-works/    
https://tenthousandmeters.com/blog/python-behind-the-scenes-7-how-python-attributes-work/    
https://tenthousandmeters.com/blog/python-behind-the-scenes-8-how-python-integers-work/    
https://tenthousandmeters.com/blog/python-behind-the-scenes-9-how-python-strings-work/    
https://tenthousandmeters.com/blog/python-behind-the-scenes-10-how-python-dictionaries-work/    
https://tenthousandmeters.com/blog/python-behind-the-scenes-11-how-the-python-import-system-works/    
https://tenthousandmeters.com/blog/python-behind-the-scenes-12-how-asyncawait-works-in-python/    
https://tenthousandmeters.com/blog/python-behind-the-scenes-13-the-gil-and-its-effects-on-python-multithreading/    


#### Bytecode 

https://bytecode.readthedocs.io/en/latest/index.html    
https://github.com/MatthieuDartiailh/bytecode    

#### Python documents

https://github.com/python/cpython/tree/main/InternalDocs
https://docs.python.org/3.4/library/dis.html    
https://docs.python.org/3/reference/datamodel.html

## How to run different versions of Python

The latest installers (version 3.3 and above) come with pylauncher, so you can type ```py -version```

Examples
```
py -3.10
py -3.13
```


## Geting some bytecode

A good place to start is with a function you create and the ```dis``` module.   

```python
def print_hello():
    print("Hello")
```
```python
import dis
dis.dis(print_hello)
```
```python

  2           0 LOAD_GLOBAL              0 (print)
              2 LOAD_CONST               1 ('Hello')
              4 CALL_FUNCTION            1
              6 POP_TOP
              8 LOAD_CONST               0 (None)
             10 RETURN_VALUE
```
```python
print(dis.code_info(print_hello))
```
```python
Name:              print_hello
Filename:          <pyshell#8>
Argument count:    0
Positional-only arguments: 0
Kw-only arguments: 0
Number of locals:  0
Stack size:        2
Flags:             OPTIMIZED, NEWLOCALS, NOFREE
Constants:
   0: None
   1: 'Hello'
Names:
   0: print
```

Within CPython each function has a code object, which is accessible as `.__code__.`    
This has multiple attributes, the key one being the actual `bytearray` of the bytecode.    

```python
print(list(print_hello.__code__.co_code))
```
```python
[116, 0, 100, 1, 131, 1, 1, 0, 100, 0, 83, 0]
```



## Running some bytecode

Bytecode needs a context to run in - the 'frame' - which has all the correct constants, names and variables.   
From this we can create a function, which can be run.   

```python
from types import FunctionType, CodeType
from dis import COMPILER_FLAG_NAMES

my_code = CodeType (
    0,                   # argcount
    0,                   # posonlyargcount
    0,                   # kwonlyargcount
    0,                   # nlocals
    3,                   # stacksize
    0x43,                # flags CO_OPTIMIZED | CO_NEWLOCALS | CO_NOFREE

    bytes([116, 0, 100, 1, 131, 1, 1, 0, 100, 0, 83, 0]),  # codestring
    (None, 'Hello'),     # constants
    ('print',),          # names - of constants or global
    (),                  # varnames - variable names 
    'no_file',           # filename
    'my_print_hello',    # name (code name / function)
     1,                  # firstlineno (First line where this code appears)
     b'',                # linetable
     (),                 # freevars
     ()                  # cellvars
    )

my_print_hello = FunctionType(my_code, {})
my_print_hello()
```

### Getting the attributes of a code block

```python
help(type(((lambda:0).__code__)))
```
```python
class code(object)
 |  code(argcount,
    posonlyargcount,
    kwonlyargcount,
    nlocals,
    stacksize,
    flags,
    codestring,
    constants,
    names,
    varnames,
    filename,
    name,
    firstlineno,
    linetable,
    freevars=(),
    cellvars=(),
     /)
 |  
 |  Create a code object.  Not for the faint of heart.
 |  
 ....
 |
 |  Data descriptors defined here:
 |  co_argcount
 |  co_cellvars
 |  co_code
 |  co_consts
 |  co_filename
 |  co_firstlineno
 |  co_flags
 |  co_freevars
 |  co_kwonlyargcount
 |  co_linetable
 |  co_lnotab
 |  co_name
 |  co_names
 |  co_nlocals
 |  co_posonlyargcount
 |  co_stacksize
 |  co_varnames
```

### Printing the attributes for a real object

```python
def obj():
    print("Hello")
```
```python
import inspect
for name, value in inspect.getmembers(obj):
    if name.startswith('co_') and name is not 'co_lines':
        print(f"{name:30}: {value}")

for i in  obj.__code__.co_lines():
    print(f"{'co_lines':30}: {i}")
```
```python
co_argcount                   : 0
co_cellvars                   : ()
co_code                       : b't\x00d\x01\x83\x01\x01\x00d\x00S\x00'
co_consts                     : (None, 'Hello')
co_filename                   : test.py
co_firstlineno                : 17
co_flags                      : 67
co_freevars                   : ()
co_kwonlyargcount             : 0
co_linetable                  : b'\x0c\x01'
co_lnotab                     : b'\x00\x01'
co_name                       : obj
co_names                      : ('print',)
co_nlocals                    : 0
co_posonlyargcount            : 0
co_stacksize                  : 2
co_varnames                   : ()
co_lines                      : (0, 12, 18)
```

### Simple assembler

```python
code = (('LOAD_GLOBAL',0), ('LOAD_CONST', 1), ('CALL_FUNCTION', 1), ('POP_TOP', 0), ('LOAD_CONST', 0), ('RETURN_VALUE', 0))

import dis
def assemble(code):
    bytecode = []
    for inst in code:
        opcode = dis.opmap[inst[0]]
        bytecode.append(opcode)
        for arg in inst[1:]:
            bytecode.append(arg)
    return (bytecode)

print(assemble(code))
```
```python
[116, 0, 100, 1, 131, 1, 1, 0, 100, 0, 83, 0]
```

### Simple disassembler

```
def disassemble(code):
    dis_val = []
    for op_code, arg in zip(code[::2], code[1::2]):
        dis_val.append((dis.opname[op_code], arg))
    return tuple(dis_val)

def disassemble_nice(code, varnames, names, consts):
    dis_val = []
    for op_code, arg in zip(code[::2], code[1::2]):
        op_name = dis.opname[op_code]
        if op_name in ('LOAD_CONST', 'STORE_CONST'):
            text = consts[arg]
        elif op_name in ('LOAD_FAST', 'STORE_FAST'):
            text = varnames[arg]
        elif op_name in ('LOAD_GLOBAL', 'STORE_GLOBAL'):
            text = names[arg]
        else:
                text = ''
        dis_val.append((dis.opname[op_code], arg, text))
    return tuple(dis_val)

bytecode = [116, 0, 100, 1, 131, 1, 1, 0, 100, 0, 83, 0]
print(disassemble(bytecode))
print(disassemble_nice(bytecode, (), ('print'), (None, 'Hello')))
```

```
(('LOAD_GLOBAL', 0),
('LOAD_CONST', 1),
('CALL_FUNCTION', 1),
('POP_TOP', 0),
('LOAD_CONST', 0),
('RETURN_VALUE', 0))
```
```
(('LOAD_GLOBAL', 0, 'p'),
('LOAD_CONST', 1, 'Hello'),
('CALL_FUNCTION', 1, ''),
('POP_TOP', 0, ''),
('LOAD_CONST', 0, None),
('RETURN_VALUE', 0, ''))
```

### Listing the bytecode mnemonics and bytecode values
```python
import dis
dis.HAVE_ARGUMENT
```
```python
90
```
```python
dis.opmap
```
```python
{'POP_TOP': 1, 'ROT_TWO': 2, 'ROT_THREE': 3, 'DUP_TOP': 4, 'DUP_TOP_TWO': 5, 'ROT_FOUR': 6, 'NOP': 9,
'UNARY_POSITIVE': 10, 'UNARY_NEGATIVE': 11, 'UNARY_NOT': 12, 'UNARY_INVERT': 15, 'BINARY_MATRIX_MULTIPLY': 16,
'INPLACE_MATRIX_MULTIPLY': 17, 'BINARY_POWER': 19, 'BINARY_MULTIPLY': 20, 'BINARY_MODULO': 22, 'BINARY_ADD': 23,
'BINARY_SUBTRACT': 24, 'BINARY_SUBSCR': 25, 'BINARY_FLOOR_DIVIDE': 26, 'BINARY_TRUE_DIVIDE': 27,
'INPLACE_FLOOR_DIVIDE': 28, 'INPLACE_TRUE_DIVIDE': 29, 'GET_LEN': 30, 'MATCH_MAPPING': 31, 'MATCH_SEQUENCE': 32,
'MATCH_KEYS': 33, 'COPY_DICT_WITHOUT_KEYS': 34, 'WITH_EXCEPT_START': 49, 'GET_AITER': 50, 'GET_ANEXT': 51,
'BEFORE_ASYNC_WITH': 52, 'END_ASYNC_FOR': 54, 'INPLACE_ADD': 55, 'INPLACE_SUBTRACT': 56, 'INPLACE_MULTIPLY': 57,
'INPLACE_MODULO': 59, 'STORE_SUBSCR': 60, 'DELETE_SUBSCR': 61, 'BINARY_LSHIFT': 62, 'BINARY_RSHIFT': 63, 'BINARY_AND': 64,
'BINARY_XOR': 65, 'BINARY_OR': 66, 'INPLACE_POWER': 67, 'GET_ITER': 68, 'GET_YIELD_FROM_ITER': 69, 'PRINT_EXPR': 70,
'LOAD_BUILD_CLASS': 71, 'YIELD_FROM': 72, 'GET_AWAITABLE': 73, 'LOAD_ASSERTION_ERROR': 74, 'INPLACE_LSHIFT': 75,
'INPLACE_RSHIFT': 76, 'INPLACE_AND': 77, 'INPLACE_XOR': 78, 'INPLACE_OR': 79, 'LIST_TO_TUPLE': 82, 'RETURN_VALUE': 83,
'IMPORT_STAR': 84, 'SETUP_ANNOTATIONS': 85, 'YIELD_VALUE': 86, 'POP_BLOCK': 87, 'POP_EXCEPT': 89,

'STORE_NAME': 90, 'DELETE_NAME': 91, 'UNPACK_SEQUENCE': 92, 'FOR_ITER': 93, 'UNPACK_EX': 94, 'STORE_ATTR': 95,
'DELETE_ATTR': 96, 'STORE_GLOBAL': 97, 'DELETE_GLOBAL': 98, 'ROT_N': 99, 'LOAD_CONST': 100, 'LOAD_NAME': 101,
'BUILD_TUPLE': 102, 'BUILD_LIST': 103, 'BUILD_SET': 104, 'BUILD_MAP': 105, 'LOAD_ATTR': 106, 'COMPARE_OP': 107,
'IMPORT_NAME': 108, 'IMPORT_FROM': 109, 'JUMP_FORWARD': 110, 'JUMP_IF_FALSE_OR_POP': 111, 'JUMP_IF_TRUE_OR_POP': 112,
'JUMP_ABSOLUTE': 113, 'POP_JUMP_IF_FALSE': 114, 'POP_JUMP_IF_TRUE': 115, 'LOAD_GLOBAL': 116, 'IS_OP': 117,
'CONTAINS_OP': 118, 'RERAISE': 119, 'JUMP_IF_NOT_EXC_MATCH': 121, 'SETUP_FINALLY': 122, 'LOAD_FAST': 124,
'STORE_FAST': 125, 'DELETE_FAST': 126, 'GEN_START': 129, 'RAISE_VARARGS': 130, 'CALL_FUNCTION': 131,
'MAKE_FUNCTION': 132, 'BUILD_SLICE': 133, 'LOAD_CLOSURE': 135, 'LOAD_DEREF': 136, 'STORE_DEREF': 137,
'DELETE_DEREF': 138, 'CALL_FUNCTION_KW': 141, 'CALL_FUNCTION_EX': 142, 'SETUP_WITH': 143, 'EXTENDED_ARG': 144,
'LIST_APPEND': 145, 'SET_ADD': 146, 'MAP_ADD': 147, 'LOAD_CLASSDEREF': 148, 'MATCH_CLASS': 152,
'SETUP_ASYNC_WITH': 154, 'FORMAT_VALUE': 155, 'BUILD_CONST_KEY_MAP': 156, 'BUILD_STRING': 157,
'LOAD_METHOD': 160, 'CALL_METHOD': 161, 'LIST_EXTEND': 162, 'SET_UPDATE': 163, 'DICT_MERGE': 164, 'DICT_UPDATE': 165}
```

### Basic assembler and disassembler
```python
import dis

def dis_show(code):
    print("    co_code       ", list(code.__code__.co_code))
    print("    co_consts     ", code.__code__.co_consts)
    print("    co_stacksize  ", code.__code__.co_stacksize)
    print("    co_varnames   ", code.__code__.co_varnames)
    print("    co_flags      ", code.__code__.co_flags)
    print("    co_name       ", code.__code__.co_name)
    print("    co_names      ", code.__code__.co_names)

def code():
    a = 1
    b = 2
    c = a + b
    return c

dis.dis(code)
dis.show_code(code)
dis_show(code)
```

```python
 23           0 LOAD_CONST               1 (1)
              2 STORE_FAST               0 (a)

 24           4 LOAD_CONST               2 (2)
              6 STORE_FAST               1 (b)

 25           8 LOAD_FAST                0 (a)
             10 LOAD_FAST                1 (b)
             12 BINARY_ADD
             14 STORE_FAST               2 (c)

 26          16 LOAD_FAST                2 (c)
             18 RETURN_VALUE

Name:              code
Filename:          test.py
Argument count:    0
Positional-only arguments: 0
Kw-only arguments: 0
Number of locals:  3
Stack size:        2
Flags:             OPTIMIZED, NEWLOCALS, NOFREE
Constants:
   0: None
   1: 1
   2: 2
Variable names:
   0: a
   1: b
   2: c

    co_code        [100, 1, 125, 0, 100, 2, 125, 1, 124, 0, 124, 1, 23, 0, 125, 2, 124, 2, 83, 0]
    co_consts      (None, 1, 2)
    co_stacksize   2
    co_varnames    ('a', 'b', 'c')
    co_flags       67
    co_name        code
    co_names       ()
```

```python

from types import FunctionType, CodeType
from dis import COMPILER_FLAG_NAMES
import dis

def assemble(code):
    bytecode = []
    for inst in code:
        opcode = dis.opmap[inst[0]]
        bytecode.append(opcode)
        for arg in inst[1:]:
            bytecode.append(arg)
    return (bytecode)

code =  ( ('LOAD_CONST', 1), ('STORE_FAST', 0), ('LOAD_CONST', 2), ('STORE_FAST', 1 ),
          ('LOAD_FAST', 0), ('LOAD_FAST', 1),
          ('BINARY_ADD', 0),
          ('STORE_FAST', 2),
          ('LOAD_FAST', 2),
          ('RETURN_VALUE', 0))

bytecode = assemble(code)
print(bytecode)

my_code = CodeType (
    0,                   # argcount
    0,                   # posonlyargcount
    0,                   # kwonlyargcount
    3,                   # nlocals
    2,                   # stacksize
    0x43,                # flags CO_OPTIMIZED | CO_NEWLOCALS | CO_NOFREE
    bytes(bytecode),     # codestring
    (None, 1, 2),        # constants
    (),                  # names - of constants or global
    ('a', 'b', 'b'),     # varnames - variable names 
    'no_file',           # filename
    'my_func',           # name (code name / function)
     1,                  # firstlineno (First line where this code appears)
     b'',                # linetable
     (),                 # freevars
     ()                  # cellvars
    )

my_func = FunctionType(my_code, {})
print(my_func())
```
