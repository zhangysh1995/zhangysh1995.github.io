---
layout: post
tags: [llvm, notes]
title: "Merge New LLVM Pass to Source Tree"
---

For example, we have the following folders:

````
--- SourceRoot
    --- CMakeLists.txt
    --- MyPass
        --- MyPass.cpp
        --- CMakeLists.txt 

````
## Step 1: change `CMakeLists.txt`
We need to change the following lines in `CMakeLists.txt` (inside MyPass folder):

````
add_library(LLVMmypass MODULE
	MyPass.cpp
)

````

to

````
add_llvm_loadable_module(LLVMmypass MODULE
	MyPass.cpp
)
````

## Step 2: put code into source tree
1. Move `MyPass` folder to `llvm/lib/Transforms` (LLVM source folder).
2. Add to `llvm/lib/Transforms/CMakeLists.txt` a line `add_subdirectory(MyPass)`.

## Notes:

With this we could use `opt -load lib/LLVMmypass.so` to run the pass, **not** necessarily with absolute path for `*.so`.

