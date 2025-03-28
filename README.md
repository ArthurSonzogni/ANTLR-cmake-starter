# ANTLR Cmake Starter

## Description

A ready to use starter project using ANTLR with cmake.

There are no dependencies: 
- ANTLR jar and the 
- ANTLR git repo (for the ANTLR c++ run-time)   

will be downloaded automatically.

## Instructions

```bash
# Build the project
mkdir build
cd build
cmake ..
make
./src/main

```

## Output example

```bash
Please enter an expression (for instance (1+2)*(3-4)/(5-6))
input = 4/2-3*3+1-1
Result = -9
Tree = 
             
             4
            ╱
           ÷ 
          ╱ ╲
         ╱   2
        ╱    
       ╱        
      -         3
     ╱ ╲       ╱
    ╱   ╲     x 
   ╱     ╲   ╱ ╲
  ╱       ╲ ╱   3
 ╱         +    
-           ╲ 
 ╲           ╲
  ╲           1
   ╲          
    ╲ 
     ╲
      1
      
```
