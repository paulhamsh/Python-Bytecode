## 01 Basic Instructions

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
code =  ( ('LOAD_CONST', 1), ('STORE_FAST', 0), ('LOAD_CONST', 2), ('STORE_FAST', 1 ),
          ('LOAD_FAST', 0), ('LOAD_FAST', 1),
          ('BINARY_ADD', 0),
          ('STORE_FAST', 2),
          ('LOAD_FAST', 2),
          ('RETURN_VALUE', 0))

bytecode = assemble(code)

```
