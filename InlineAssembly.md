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
The [volatile] qualitfier tells the compiler to not optimize the asm statement by moving it around. 
The [goto] qualifier tells the compiler that the embedded asm may jump to some label.

The input/output operands parameters define the placement of the operands. Let's look at a first example:

```assembly
#include <stdio.h>

int main(void)
{
        int a = 5;
        int b = 10;
        int sum = 0;

        __asm__("addl %1,%2" : "=r" (sum) : "r" (a), "0" (b));
        printf("a + b = %d\n", sum);
        return 0;
}
```

For the output operands, "=r"(sum) tells the compiler that any general purpose register can be chosen
to store the output as C variable sum. For the input operands, "r" (a) means any GPR can be used for 
variable a, "0" (b) means the register of the #0 register can be reused herer as input. 

