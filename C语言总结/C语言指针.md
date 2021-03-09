## C语言指针

**指针**是一个变量，其值为另一个变量的地址，即，内存位置的直接地址。就像其他变量或常量一样，您必须在使用指针存储其他变量地址之前，对其进行声明。

所有实际数据类型，不管是整型、浮点型、字符型，还是其他的数据类型，对应指针的值的类型都是一样的，都是一个代表内存地址的长的十六进制数。

不同数据类型的指针之间唯一的不同是，指针所指向的变量或常量的数据类型不同。

每一个变量都有一个内存位置，每一个内存位置都定义了可使用 **&** 运算符访问的地址，它表示了在内存中的一个地址。

### 声明



```c
type *var-name;
```

**type** 是指针的基类型，它必须是一个有效的 C 数据类型

**var-name** 是指针变量的名称。

用来声明指针的星号 ***** 与乘法中使用的星号是相同的。

但是，在这个语句中，星号是用来指定一个变量是指针。

### 使用

- 定义一个指针变量

  ```c
  int *p;    //定义一个指向int类型的指针变量
  ```

  

- 把变量地址赋值给指针

  ```c
  int a;         //定义一个int类型变量
  p = &a;        //将int型变量a赋给p  ，  通过p就可以对a进行各种操作
  ```

  

- 访问指针变量中可用地址的值。

  ```c
  int b = *p ;   //使用*解引用，将指针p指向的a的值  赋给b
  ```

**tip**

声明一个指针变量中的*

代表声明的是一个指针变量

解引用一个指针变量中的*

代表解引用

两者一个符号但是意义不同

### C 中的 NULL 指针

在变量声明的时候，如果没有确切的地址可以赋值，为指针变量赋一个 NULL 值是一个良好的编程习惯。赋为 NULL 值的指针被称为**空**指针。

NULL 指针是一个定义在标准库中的值为零的常量。

#### java中的null指针

Java中的任何引用变量都将null作为默认值。

null既不是对象也不是一种类型，它仅是一种特殊的值，你可以将其赋予任何引用类型，你也可以将null转化成任何类型

```java
String str = null; // null can be assigned to String
Integer itr = null; // you can assign null to Integer also
Double dbl = null;  // null can also be assigned to Double
String myStr = (String) null; // null can be type cast to String
Integer myItr = (Integer) null; // it can also be type casted to Integer
Double myDbl = (Double) null; // yes it's possible, no error
```

### c传递指针给函数

C 语言允许您传递指针给函数，只需要简单地声明函数参数为指针类型即可。

```c
#include <stdio.h>
#include <time.h>
 
void getSeconds(unsigned long *par);

int main ()
{
   unsigned long sec;


   getSeconds( &sec );

   /* 输出实际值 */
   printf("Number of seconds: %ld\n", sec );

   return 0;
}

void getSeconds(unsigned long *par)
{
   /* 获取当前的秒数 */
   *par = time( NULL );
   return;
}
```

运行结果：

```c
Number of seconds :1294450468
```

### 指向指针的指针

通常，一个指针包含一个变量的地址。当我们定义一个指向指针的指针时，第一个指针包含了第二个指针的地址，第二个指针指向包含实际值的位置。

![C 中指向指针的指针](https://www.runoob.com/wp-content/uploads/2014/09/pointer_to_pointer.jpg)

这是一个指向int型指针的指针

```c
int **var;
```

访问这个被int型指针指向的int值需要两步解引用

```c
int **var;
```

### 数组指针

可能有一种情况，我们想要让数组存储指向 int 或 char 或其他数据类型的指针。下面是一个指向整数的指针数组的声明：

```
int *ptr[MAX];
```

在这里，把 **ptr** 声明为一个数组，由 MAX 个整数指针组成。因此，ptr 中的每个元素，都是一个指向 int 值的指针。下面的实例用到了三个整数，它们将存储在一个指针数组中，如下所示：

```c
#include <stdio.h>
 
const int MAX = 3;
 
int main ()
{
   int  var[] = {10, 100, 200};
   int i, *ptr[MAX];
 
   for ( i = 0; i < MAX; i++)
   {
      ptr[i] = &var[i]; /* 赋值为整数的地址 */
   }
   for ( i = 0; i < MAX; i++)
   {
      printf("Value of var[%d] = %d\n", i, *ptr[i] );
   }
   return 0;
}
```

结果

```c
Value of var[0] = 10
Value of var[1] = 100
Value of var[2] = 200
```

