# C语言混乱代码大赛2006。请解释sykes2.c
这个C程序是如何工作的？
```c
main(_){_^448&&main(-~_);putchar(--_%64?32|-~7[__TIME__-_/8%8][">'txiZ^(~z?"-48]>>";;;====~$::199"[_*2&8|_/64]/(_&2?1:8)%8&1:10);}
```
按原样编译（在`gcc 4.6.3`上测试）。它将打印编译时的时间。在我的系统上这是：
```
    !!  !!!!!!              !!  !!!!!!              !!  !!!!!! 
    !!  !!  !!              !!      !!              !!  !!  !! 
    !!  !!  !!              !!      !!              !!  !!  !! 
    !!  !!!!!!    !!        !!      !!    !!        !!  !!!!!! 
    !!      !!              !!      !!              !!  !!  !! 
    !!      !!              !!      !!              !!  !!  !! 
    !!  !!!!!!              !!      !!              !!  !!!!!!
```
来源：[sykes2 - 在一行中显示一个时钟](http://www.ioccc.org/years-spoiler.html#2006)，[sykes2作者提示](http://www.ioccc.org/2006/sykes2/hint.text)

一些提示：默认情况下没有编译警告。使用`-Wall`编译，会发出以下警告：
```
sykes2.c:1:1: warning: return type defaults to ‘int’ [-Wreturn-type]
sykes2.c: In function ‘main’:
sykes2.c:1:14: warning: value computed is not used [-Wunused-value]
sykes2.c:1:1: warning: implicit declaration of function ‘putchar’ [-Wimplicit-function-declaration]
sykes2.c:1:1: warning: suggest parentheses around arithmetic in operand of ‘|’ [-Wparentheses]
sykes2.c:1:1: warning: suggest parentheses around arithmetic in operand of ‘|’ [-Wparentheses]
sykes2.c:1:1: warning: control reaches end of non-void function [-Wreturn-type]
```

## 高票回答：
让我们来对它解除混淆。

缩进：
```c
main(_) {
    _^448 && main(-~_);
    putchar(--_%64
        ? 32 | -~7[__TIME__-_/8%8][">'txiZ^(~z?"-48] >> ";;;====~$::199"[_*2&8|_/64]/(_&2?1:8)%8&1
        : 10);
}
```
引入变量来解开这个烂摊子：
```c
main(int i) {
    if(i^448)
        main(-~i);
    if(--i % 64) {
        char a = -~7[__TIME__-i/8%8][">'txiZ^(~z?"-48];
        char b = a >> ";;;====~$::199"[i*2&8|i/64]/(i&2?1:8)%8;
        putchar(32 | (b & 1));
    } else {
        putchar(10); // newline
    }
}
```
注意因为二进制补码所以`-~i == i+1`。因此，我们有
```c
main(int i) {
    if(i != 448)
        main(i+1);
    i--;
    if(i % 64 == 0) {
        putchar('\n');
    } else {
        char a = -~7[__TIME__-i/8%8][">'txiZ^(~z?"-48];
        char b = a >> ";;;====~$::199"[i*2&8|i/64]/(i&2?1:8)%8;
        putchar(32 | (b & 1));
    }
}
```
现在，请注意[a[b]与b[a]相同](https://stackoverflow.com/questions/1995113/strangest-language-feature/1995156#1995156)，并再次应用`-~ == 1+`更改它：
```c
main(int i) {
    if(i != 448)
        main(i+1);
    i--;
    if(i % 64 == 0) {
        putchar('\n');
    } else {
        char a = (">'txiZ^(~z?"-48)[(__TIME__-i/8%8)[7]] + 1;
        char b = a >> ";;;====~$::199"[(i*2&8)|i/64]/(i&2?1:8)%8;
        putchar(32 | (b & 1));
    }
}
```
将递归转换为循环并稍微简化一下：
```c
// please don't pass any command-line arguments
main() {
    int i;
    for(i=447; i>=0; i--) {
        if(i % 64 == 0) {
            putchar('\n');
        } else {
            char t = __TIME__[7 - i/8%8];
            char a = ">'txiZ^(~z?"[t - 48] + 1;
            int shift = ";;;====~$::199"[(i*2&8) | (i/64)];
            if((i & 2) == 0)
                shift /= 8;
            shift = shift % 8;
            char b = a >> shift;
            putchar(32 | (b & 1));
        }
    }
}
```
每次迭代输出一个字符。每64个字符，它将输出一个换行符。否则，它使用一对数据表来确定要输出的内容，并放置字符32（空格）或字符33（`!`）。第一个表（`">'txiZ^(~z?"`)是一组描述每个字符外观的10个位图，第二个表（`";;;====~$::199"`）选择 从位图显示的适当位。
### 第二个表
让我们从检查第二个表开始，`int shift = ";;;====~$::199"[(i*2&8) | (i/64)];`。`i/64`是行号（6到0），`i*2&8`是8当且仅当`i`是4,5,6或7模8时。

`if((i & 2) == 0) shift /= 8; shift = shift % 8`选择表值的高八位数字（对于`i%8`=0,1,4,5）或低八位数字（对于`i%8`=2,3,6,7）。转换表最终看起来像这样：
```
row col val
6   6-7 0
6   4-5 0
6   2-3 5
6   0-1 7
5   6-7 1
5   4-5 7
5   2-3 5
5   0-1 7
4   6-7 1
4   4-5 7
4   2-3 5
4   0-1 7
3   6-7 1
3   4-5 6
3   2-3 5
3   0-1 7
2   6-7 2
2   4-5 7
2   2-3 3
2   0-1 7
1   6-7 2
1   4-5 7
1   2-3 3
1   0-1 7
0   6-7 4
0   4-5 4
0   2-3 3
0   0-1 7
```
或以表格形式
```
00005577
11775577
11775577
11665577
22773377
22773377
44443377
```
请注意，作者使用null终止符作为前两个表的条目（偷偷摸摸的！）。

这是在七段显示器之后设计的，其中第7段为空白。因此，第一个表中的条目必须定义亮起的段。
### 第一个表
`__TIME__`是预处理器定义的特殊宏。它扩展为包含预处理器运行时间的字符串常量，格式为`"HH:MM:SS"`。注意它包含正好8个字符。请注意，0-9的ASCII值为48到57，并且`:`的ASCII值58。每行输出64个字符，因此`__TIME__`中的每个字将以8个字符替代。

`7 - i/8％8`是当前正在输出的`__TIME__`的索引（需要`7-`因为我们向下迭代`i`轮）。所以，`t`是输出`__TIME__`的字符。

`a`取决于输入`t`，以二进制结束等于以下内容：
```
0 00111111
1 00101000
2 01110101
3 01111001
4 01101010
5 01011011
6 01011111
7 00101001
8 01111111
9 01111011
: 01000000
```
每个数字都是一个*位图*，用于描述在七段显示器中亮起的段。由于字符都是7位ASCII，因此始终清除高位。 因此，段表中的`7`始终作为空白打印。 第二个表看起来像这样，第`7`段为空白：
```
000055  
11  55  
11  55  
116655  
22  33  
22  33  
444433  
```
因此，例如，`4`是`01101010`（位1,3,5和6的集），打印为
```
----!!--
!!--!!--
!!--!!--
!!!!!!--
----!!--
----!!--
----!!--
```
---
为了表明我们真正理解代码，让我们用这个表调整输出：
```
  00  
11  55
11  55
  66  
22  33
22  33
  44
```
这被编码为`"?;;?==? '::799\x07"`。出于艺术目的，我们将为几个字符加上64（因为只使用低6位，这不会影响输出）；这给出了`"?{{?}}?gg::799G"`（注意第8个字符未被使用，所以我们实际上可以做任何我们想要的）。将我们的新表放在原始代码中：
```c
main(_){_^448&&main(-~_);putchar(--_%64?32|-~7[__TIME__-_/8%8][">'txiZ^(~z?"-48]>>"?{{?}}?gg::799G"[_*2&8|_/64]/(_&2?1:8)%8&1:10);}
```
我们得到
```
          !!              !!                              !!   
    !!  !!              !!  !!  !!  !!              !!  !!  !! 
    !!  !!              !!  !!  !!  !!              !!  !!  !! 
          !!      !!              !!      !!                   
    !!  !!  !!          !!  !!      !!              !!  !!  !! 
    !!  !!  !!          !!  !!      !!              !!  !!  !! 
          !!              !!                              !!   
```
就像我们预期的那样。它不像原版一样好，这就解释了为什么作者选择使用他所做的表格。

[原文链接](https://stackoverflow.com/questions/15393441/obfuscated-c-code-contest-2006-please-explain-sykes2-c)