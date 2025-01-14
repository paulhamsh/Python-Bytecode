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

## Getting the attributes of a code block

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

Printing the attributes for a real object
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
