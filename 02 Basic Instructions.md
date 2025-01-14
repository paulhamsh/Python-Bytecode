## 01 Basic Instructions
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
