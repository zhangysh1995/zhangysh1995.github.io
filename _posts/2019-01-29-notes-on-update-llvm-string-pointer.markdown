---
layout: post
tags: [llvm, notes]
title: "Create, update and use LLVM String Pointer"
---

We want to create a global string pointer, and update its "value" (literal) on demand, and use it.

## Create a String Pointer

Basically, there are two ways of creating a global variable (`Module` and `IRbuilder`). The second one is shown here. 

{% highlight cpp %}
// create IR builder
IRBuilder<> builder(*context);
builder.SetInsertPoint(&*i);

StringRef myStr = ...

// insert string pointer to Module
Value result = builder->CreateGlobalStringPtr(myStr, "myStr");

{% endhighlight %}


## Update the "value" pointed

{% highlight cpp %}

// declare and allocate `funcName`
StringRef funcName = xxfunciont()->getName();
Value* name = ConstantDataArray::getString(*context, funcName, true);
Value* mem = builder->CreateAlloca(name->getType(), 
	                           ConstantExpr::getSizeOf(name->getType()));

// calculate pointer to `myStr`
std::vector<Value*> index_vector;
index_vector.push_back(ConstantInt::get(Type::getInt32Ty(*context), 0));
auto valueAsPtr = builder->CreateGEP(mem, index_vector, "tmp");

// store funcName to `myStr`
builder->CreateStore(name, valueAsPtr);

{% endhighlight %}


Why should we use **GEP** (GetElementPointer)? 

Because we need to calculate the index, please refer to [The Often Misunderstood GEP Instruction](https://llvm.org/docs/GetElementPtr.html).
 
## Use the string pointer

{% highlight cpp %}
// `printf` declaration
Value* blkv = M.getOrInsertFunction("printf",
        FunctionType::get(IntegerType::getInt8PtrTy(*context),
        PointerType::get(Type::getInt8Ty(*context), 0),
	true /* this is var arg func type*/));

// print the new string
builder->CreateCall(blkv, result);

{% endhighlight %}

