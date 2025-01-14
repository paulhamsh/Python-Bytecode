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

## Running some bytecode

Bytecode needs a context to run in - the 'frame' - which has all the correct constants, names and variables.   
From this we can create a function, which can be run.   

```





```

