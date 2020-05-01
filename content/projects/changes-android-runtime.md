 
+++
title = "Changes in Android Runtime"
date = 2019-06-08T14:04:27+05:30
categories = ["Android"]
type = ["posts","post"]
[ author ]
  name = "Rohan Maity"
+++


Hey  guys , Android has been around ten years and since then Google introduced many things in android , improved , changed and so on. So with the Runtime in Android.

In this post , I will tell about how the runtime environment in android changed among the android verisons.
Before we began , lets know about JIT and AOT

**JIT (Just in Time)** — Its the compilation of code on the fly (Just in time) i.e dynamically your code is converted Into native code. JIT converts selective part of code as and when it’s required.

**AOT(Ahead of time)** — Ahead of time compilation, before executing your app, it converts your code to native code , so that machine (android device) can execute it natively .

**DEX files** — DEX files are Dalvik excecutables which actually runs on your device .Our Source Code , Resource along with library dependencies is compiled into DEX file (contains DEX bytecode which android device runs) . And then it is zipped into APK.


![Build Process](/images/changes-android-runtime/cart-build-process.png)
*Build Process* 


### Dalvik and ART

Dalvik and ART are runtime system used in andoid. Dalvik was runtime prior Lollipop and ART is runtime from Lollipop to present version. Though in Kitkat , ART was introduced but it has to be turned on from developer options i.e it was introduced for experimental basis.

### Dalvik + JIT ( From Froyo to KitKat)
Dalvik is the virtual machine optimized for mobile devices .

On Installation

![DalvikRuntime](/images/changes-android-runtime/dalvikruntime.png)
*Dalvik Runtime*


Dex file is converted to **odex file** ( which is Optimized Dex file) by dexOpt process. This odex file runs on android’s Dalvik VM to play your app. Plus more frequently part of your app (Hot code) is converted to **native code dynamically (on the fly) so that , app can perform better.**

## Enter the ART (Android Runtime)

### AOT (Ahead of time compilation) (From Lollipop to Marshmallow)

In the ART with AOT , **during the installation period ,app’s whole code is converted to native code**. So that app can execute app natively.

![artaot](/images/changes-android-runtime/artaot.png)
*ART with AOT only*

Dex file is converted **oat file** which is **binary for AOT (native code)** through dex2oat process. This oat file runs directly on device. This is better than Dalvik+ JIT as here app runs natively while latter interprets odex file each time to play the app.

But there some problems too ... 
as the whole code is converted to native code app’s size becomes large . And as the code is converted to native code during installation , installation period increases.
Users don’t use all the app features all the time. There is always a certain part that users use mostly.
But with AOT all code (even the less frequent acces part) is converted to oat file and takes the space on device.

<br>

### AOT+JIT( Nougat +)

To solve the problem of increasing app’s size on device . Google brought JIT back to rescue plus this it includes profiling.
When dex file is loaded, if oat file is included then ART runs it directly and if no oat file then cold code(Not frequent access code) is run by interpreter and Hot code by JIT.


![jitarch](/images/changes-android-runtime/jitarch.png)
*Jit Architecture*


On first installation , there is no oat file generated . So here the app is run with JIT (converts the Hot code in background thread) which now also **profiles the code (logs the Hot code)**.

![art](/images/changes-android-runtime/ART.png)
*AOT + JIT*

**Compilation daemon service** (Background service) which starts AOT. AOT takes the profiled log and then convert your Hot code (Most frequent part) into native code (creates the oat file) in background. This service runs when your phone is idle or in charging state to save the battery power.
Here the advantage is the part of your app mostly used is already in native code — executes natively and less used part is in JIT mode — cold code is run by interpreter and hot code is converted to native for that time (for that instance) only. Hence less space size on android device plus for your app’s most used part get performance and for even the less used part JIT compensates for that .

Well that’s how the current runtime in android (AOT + JIT) and that’s it.I hope you liked the post. 

Sayonara!!



