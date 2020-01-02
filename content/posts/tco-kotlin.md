 
+++
title = "Tail Call Optimization in Kotlin"
date = 2019-07-11T14:04:27+05:30
categories = ["Kotlin"]
type = ["posts","post"]
[ author ]
  name = "Rohan Maity"
+++

Before understanding Tail Call Optimization. Lets get a brief about “Function Call Stack” and “Recursion”

### Function Call Stack

When ever Java Application starts, a process starts in which our application (main thread) works. With this, Stack is maintained for the function calls. This stack contains Stack Frame which stores data of local variable to that frame.

For example, consider this sample program with three functions.

![Sample](/images/tco-kotlin/sample_java.png)
*Sample Java Program*

When the program starts firstly main() is called. So it gets push to stack. Then in main(), we have called fun1(), so fun1() is pushed to stack over main(). In fun1(), fun2() is called so fun2() is also pushed to stack. And lastly fun3() is pushed.

Order of execution : main()->fun1()->fun2()->fun3()

![funcs](/images/tco-kotlin/funCS.png)
*Function call stack formed. fun3() currently in execution*

>Reason being function call stack maintained is that when the function finishes execution, program would get to know what to execute next, which is possible due to Function Call Stack. Considering our example when fun3() finishes execution it is popped out of stack and start executing from point after the fun3() was called from fun2().

### Recursion

Simply speaking when a function calls itself its called Recursion. Again lets see the example of this and see the function stack formed.

This a simple Factorial program

![factorialkt](/images/tco-kotlin/factorialkt.png)
*factorial.kt*

Here factorial() calls itself again and again until value of num = 0. Now Lets see the function call stack formed for calculating 10! (10 factorial).

![factorial10](/images/tco-kotlin/factorial10.png)
*Factorial Function stack formed 10!*

For calculating 10! , when num reaches 0 , 11 stack frames are created. These frames contains data of variable local to function. **Hence each frame consumes memory. This is some problem with recursion. Though recursion let us write code easily, but this a problem too.**

### Enter the Tail Call Optimization!!

Now Tail call optimization is way in which “For recursive calls to function No New Stack Frame is Allocated.”

But How ???

As explained earlier function stack is maintained so program knows what to execute next . Considering the example of Factorial program above, reason function call stack was maintained because even after the execution of recursive call to factorial, there was some work left to do. That was

> num*result of recursive factorial()

Program was supposed to multiply the result of recursive factorial() call, hence function call stack is maintained.

#### Tail call Optimized Way

![optifacti](/images/tco-kotlin/optifacti.png)
*OptiFactorial.kt*


> Notice here this time , we are simply returning result of recursive call to optiFactorial(). And computing the factorial value on the go,.

> Since now we are not evaluating any value after execution of recursive call, so there is nothing left to do after evaluating the result of recursive call. And since there is nothing left to do . So no Stack is maintained , No allocation to new Stack Frame.

So the result is unlike the previous program No new stack frame should be allocated to recursive calls.

Lets see the Function call stack.

![optifactistack](/images/tco-kotlin/optifactistack.png)
*optiFactorial Function stack*

Wait!! optiFactorial() is supposed to be Tail Call optimized way, so it shouldn’t create new Stack Frames. Then Why???

Reason is

> JVM doesn’t support Tail Call Optimization

But there’s a good reason for this too. JVM doesn’t support Tail Call Optimization for Security Purposes. JVM maintains the whole Stack so that we get to know which function was called from which function. A good example would be Stack Trace which we get when a Exception occurs. This Stack Trace helps us too see at which point exception was thrown in function and shows the whole order of functions at point of Exception.


#### Enter the Kotlin’s tailRec

> tailRec is a keyword in Kotlin which helps Tail Call Optimized Recursive functions to run in Tail Call Optimized way in JVM

![optifactitailrec](/images/tco-kotlin/optifactitailrec.png)
*optiFactorial with TailRec*

Notice the tailRec keyword. Now lets see the function call stack.

![funCSTR](/images/tco-kotlin/funCSTR.png)
*Function Stack After TailRec*

Yayyyyyy!!! Now finally our Tail call optimized function is working as intended.


### is tailRec magic ? What does it do ?

**One thing to understand is that Kotlin is no magic., its syntactical sugar.**

Lets see the kotlin bytecode and see the output after decompiling.

In IntelliJ or Android Studio : Go to Tools->Kotlin->Show Kotlin ByteCode

This will generate Kotlin ByteCode. There would be option of “Decompile”. Just Decompile it to see in a readable way.

![TRBC](/images/tco-kotlin/TRBC.png)
*tailRec optiFactorial() decompiled bytcode*

As we can see with tailRec keyword. Kotlin compiler just converts the Tail Call Optimized function to iterative way. Hence no new stack frame is created.

> With tailRec keyword, you can write the function in Tail Call optimized way, but internally it converts it to iterative function and runs the iterative().

#### Why Not Iterative functions then ?

Iterative functions are not easy to write and understand all the time. as compared to Recursive functions. Simple and good example could be QuickSort.

