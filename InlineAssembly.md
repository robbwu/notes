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

Another more complex example is:
```C
uint32_t builtin_popcnt_unrolled_errata_manual(const uint64_t* buf, int len) {
  assert(len % 4 == 0);
  uint64_t cnt[4];
  for (int i = 0; i < 4; ++i) {
    cnt[i] = 0;
  }

  for (int i = 0; i < len; i+=4) {
    __asm__(    
        "popcnt %4, %4  \n\
        "add %4, %0     \n\t"
        "popcnt %5, %5  \n\t"
        "add %5, %1     \n\t"
        "popcnt %6, %6  \n\t"
        "add %6, %2     \n\t"
        "popcnt %7, %7  \n\t"
        "add %7, %3     \n\t" // +r means input/output, r means intput
        : "+r" (cnt[0]), "+r" (cnt[1]), "+r" (cnt[2]), "+r" (cnt[3]) 
        : "r"  (buf[i]), "r"  (buf[i+1]), "r"  (buf[i+2]), "r"  (buf[i+3]));
  }
  return cnt[0] + cnt[1] + cnt[2] + cnt[3];
}
```


