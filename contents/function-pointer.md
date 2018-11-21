# C中的函数指针是如何工作的？
我最近在C中使用了函数指针。

所以继续回答自己问题的传统，我决定对那些需要快速深入研究这个主题的人进行一些基本的总结。

## 高票回答：
### C中的函数指针
让我们从一个基本函数开始，我们将*指向*：
```c
int addInt(int n, int m) {
    return n+m;
}
```
首先，让我们定义一个指向函数的指针，该函数接收2个`int`并返回一个`int`：
```c
int (*functionPtr)(int,int);
```
现在我们可以安全地指向我们的函数：
```c
functionPtr = &addInt;
```
现在我们有了一个指向函数的指针，让我们使用它：
```c
int sum = (*functionPtr)(2, 3); // sum == 5
```
将指针传递给另一个函数基本相同：
```c
int add2to3(int (*functionPtr)(int, int)) {
    return (*functionPtr)(2, 3);
}
```
我们也可以在返回值中使用函数指针（尝试跟上，它会变得混乱）：
```c
// this is a function called functionFactory which receives parameter n
// and returns a pointer to another function which receives two ints
// and it returns another int
int (*functionFactory(int n))(int, int) {
    printf("Got parameter %d", n);
    int (*functionPtr)(int,int) = &addInt;
    return functionPtr;
}
```
但是使用`typedef`要好得多：
```c
typedef int (*myFuncDef)(int, int);
// note that the typedef name is indeed myFuncDef

myFuncDef functionFactory(int n) {
    printf("Got parameter %d", n);
    myFuncDef functionPtr = &addInt;
    return functionPtr;
}
```

[原文链接](https://stackoverflow.com/questions/840501/how-do-function-pointers-in-c-work)