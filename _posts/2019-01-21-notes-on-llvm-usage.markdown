---
layout: post
tags: [llvm, notes]
title:  "Use LLVM/Clang to generate code"
---

# Basic arguments

1.  Generate IR

`` clang -emit-llvm -S xx.cpp ``

2.  Generate bitcode (*.bc)

`` clang -emit-llvm -o xx.bc xx.cpp ``

3.  Load pass (optimization)

`` opt -load libxx.so -xx < xx.bc > /dev/null ``

4.  Load pass (with Clang)

`` Clang ++ -Xclang -load -Xclang *.so xx.cpp ``
