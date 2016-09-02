###kfifo中求最接近一个数的2的指数幂
环形队列分配  
内核中使用bsrl指令实现(arch/x86/include/asm/bitops.h)  
此处只讨论32位系统，即`sizeof(unsigned long) == 4`
```
int fls(int x)
```
c语言实现版本  
思路：  
考虑0的特殊性，将要求的数字减1，不断右移，得出位数，将得到位数加1，再将1左移这个位数即得到最接近的2的指数幂。  
参数0：fls(~0),返回32,1左移32位还是0。

```
static inline int fls(int x)
{
    int position;
    int i;
    if(0 != x){
        for (i = (x >> 1), position = 0; i != 0; ++position)
            i >>= 1;
    }       
    else{
        position = -1;
    }   
    return position+1;
}   

static inline unsigned int roundup_pow_of_two(unsigned int x)
{
    return 1UL << fls(x - 1);
}
```