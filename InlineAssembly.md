Why use embedded assembly in C? For predicabiliyt, speed, and even portability(?!).
[Hand coded assembly beats intrinsics in speed and portability](http://danluu.com/assembly-intrinsics/)

OK. How to do it on x86/x64 in GCC? Here is a great tutorial 
[Inline assembly](https://github.com/0xAX/linux-insides/blob/master/Theory/asm.md)

Summary here. The general form of the extended asm is like this:

```assembly
__asm__ [volatile] [goto] (AssemblerTemplate
                           [ : OutputOperands ]
                           [ : InputOperands  ]
                           [ : Clobbers       ]
                           [ : GotoLabels     ]);
```
