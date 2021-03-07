## C语言枚举 enum

### 枚举类型

枚举是 C 语言中的一种基本数据类型，它可以让数据更简洁，更易读。

枚举语法定义格式为：

```c
enum　枚举名　{枚举元素1,枚举元素2,……};
```

实例

```c
enum DAY
{
      MON=1, TUE, WED, THU, FRI, SAT, SUN
};
```

第一个枚举成员的默认值为整型的 0，后续枚举成员的值在前一个成员上加 1。

*可以在定义枚举类型时改变枚举元素的值：*

```
enum season {spring, summer=3, autumn, winter};
```

*没有指定值的枚举元素，其值为前一元素加 1。也就说 spring 的值为 0，summer 的值为 3，autumn 的值为 4，winter 的值为 5*

### 枚举变量

**1、先定义枚举类型，再定义枚举变量**

```
enum DAY
{
      MON=1, TUE, WED, THU, FRI, SAT, SUN
};
enum DAY day;
```

**2、定义枚举类型的同时定义枚举变量**

```
enum DAY
{
      MON=1, TUE, WED, THU, FRI, SAT, SUN
} day;
```

**3、省略枚举名称，直接定义枚举变量**

```
enum
{
      MON=1, TUE, WED, THU, FRI, SAT, SUN
} day;
```

实例

```
#include <stdio.h>
 
enum DAY
{
      MON=1, TUE, WED, THU, FRI, SAT, SUN
};
 
int main()
{
    enum DAY day;
    day = WED;
    printf("%d",day);
    return 0;
}
```

结果

```
3
```

在C 语言中，枚举类型是被当做 int 或者 unsigned int 类型来处理的，所以按照 C 语言规范是没有办法遍历枚举类型的。

当枚举类型时连续的时候可以遍历，不连续的时候不可以

```
#include <stdio.h>
 
enum DAY
{
      MON=1, TUE, WED, THU, FRI, SAT, SUN
} day;
int main()
{
    // 遍历枚举元素
    for (day = MON; day <= SUN; day++) {
        printf("枚举元素：%d \n", day);
    }
}
```

结果

```
枚举元素：1 
枚举元素：2 
枚举元素：3 
枚举元素：4 
枚举元素：5 
枚举元素：6 
枚举元素：7
```

以下枚举类型不连续，这种枚举无法遍历。

```
enum
{
    ENUM_0,
    ENUM_10 = 10,
    ENUM_11
};
```

枚举在 switch 中的使用：

## 实例

\#include <stdio.h> #include <stdlib.h> int main() {     enum color { red=1, green, blue };     enum  color favorite_color;     /* 用户输入数字来选择颜色 */    printf("请输入你喜欢的颜色: (1. red, 2. green, 3. blue): ");    scanf("%u", &favorite_color);     /* 输出结果 */    switch (favorite_color)    {    case red:        printf("你喜欢的颜色是红色");        break;    case green:        printf("你喜欢的颜色是绿色");        break;    case blue:        printf("你喜欢的颜色是蓝色");        break;    default:        printf("你没有选择你喜欢的颜色");    }     return 0; }

以上实例输出结果为：

```
请输入你喜欢的颜色: (1. red, 2. green, 3. blue): 1
你喜欢的颜色是红色
```