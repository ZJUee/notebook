# Go

## function

```go
func function_name (int a,var b string)( string ){ //(int a , var b string) is the parameter list and (string) is the return type
    int c=''
    for(int i = 0 ; i < a ; i++){>
        c += b
    }
    return c
}
```

- 前一个定义形参，后一个定义返回的数据类型

- 返回值就一个的话，可以不写第二个括号（没有也可以不写）

!!! tip "函数命名"
    大写函数名可以被所有包使用，但是小写只能被自己包使用！

- 形参列表可以是0个1个n个，参数列表的作用就是接受外来的数据，然后处理。实际传入的数据叫实参（C语言小复习）

返回值列表，实际上可以返回0-n个数据（和C语言中返回1个不一样：这也是为什么需要使用指针传递的原因）

!!! tip "简写"
    := 是一个短变量声明操作符，它用于在函数内部声明并初始化局部变量。这种声明方式允许你同时声明变量并为其赋值，而且编译器会根据赋值表达式的类型自动推断出变量的类型。

```go
func function_name (int a,var b string)( string ,string){ //(int a , var b string) is the parameter list and (string) is the return type
    int c=''
    int d=''
    for(int i = 0 ; i < a ; i++){>
        c += b
        d=a+b
    }
    return c,d
}
```

- 两个返回值，对应两个数据类型，一个一个对应好久ok了

```go
sum,_:=function_name(a,b)
```

如果有数据不想接受，可以使用`_`来忽略这个数据

函数参数的数量是可变的

```go
func sum(nums...int)(){//可以传入任意个的数据
    fmt.Println("--------------------------------")
    //在处理可变参数的时候，将可变参数当做一个切片来处理（数组）
    for i:=0 ; i< len(nums) ; i++{
        fmt.Println(nums[i])
    }
}
```

基本数据类型都是值传递，在函数中修改不会改变原来的数值

### 内存分析（简易版本）

函数运行的时候，会向计算机申请到一块内存

内存在逻辑上会被划分成三个区域：栈，堆，代码区

栈|堆|代码区
:-|-|-:
基本数据类型（通常）|复杂数据类型：map，切片|存放代码本身
main()函数对应的栈帧：变量1[具体变量]，变量2[具体变量]|
自定义函数1()对应的栈帧：num1[具体变量],num2[具体变量],num3[具体变量]|

自定义函数1()对应的栈帧和main()函数对应的栈帧并不相同，两个作用在不同的栈帧，自定义函数1的栈帧在函数执行完成之后就会销毁

```go
package main
import fmt

func test(num int )(){
    num=10
    fmt.Println("test----",num)
}

func main(){
    var num int =30
    test(num)
    fmt.Println("test----",num)
}
```

输出

```shell

test----10
test----30

```

为什么会是这个结果

步骤|main()|test
:-|-|-:
1|创建num变量，并且赋值为30|无
2|调用test|创建test栈帧，并且在其中定义一个num，传入30的值
3|无|num=30--->num=10，并且输出num=10，函数调用结束，栈帧销毁
4|fmt.Println()输出main函数栈帧中num的值：30|无

如何让函数内外沟通起来成为了关键

### 函数也是一种数据类型

```go
func test(num int )(){
    return 2*num
}

func main(){
    a := test//把test这个类型给一个变量，让变量成为了一个函数
    fmt.Println("a对应的类型是“%T，test函数对应的类型是%T",a,test)
    a(10)//等价于test(10)
}
```

在go语言里，函数类型也可以作为一种形参

```go
//定义一个函数，把另外一个函数作为形参
func test(num int){
    fmt.Println("调用了test")
}
func test01(num int ,num1 int , testFunc fun(int)){
    fmt.Println("调用了test01")
}
```

### 自定义数据类型

为了简化数据类型定义，但是go语言中支持自定义数据类型（理解成起一个别名）

```go
type  myint int //自定义一个int类型，起名为myint
```

但是编译识别的时候，认为myint!=int，认为不是一种数据类型，不能把myint赋值给int。转成int类型可以`int(num1)`转换成int类型

我们还可以给函数起别名

`type myfunc func(int)`就是把myfunc定义为一个函数类型，可以作为形参，也可以作为返回值类型

我们也可以定义返回值的数据类型,让返回值和数据类型一一对应

## 包

1. 如何定义一个包

    1. 建议包的名称和外面文件夹里的名称一致

    ```go
    package main
    //文件在main/main.go里
    ```

    2. main包是程序的入口包，一般的main函数会放置在main下

2. 如何导入一个包的函数
    首字母小写，包内的函数不能被其他包访问

    首先要import这个包，然后使用包名.函数名的方式调用

    ```go
    package main
    import "fmt"
    import "go_code/chapter06/funtiondemo"//包名是从$GOPATH/src后面的路径开始写，使用/进行路径分隔
    func main(){
        fmt.Println("hello world")
    }
    ```

    最后，使用包名称.函数名的方式来调用函数

3. 最后`go run main.go`开始运行函数

!!! tip "多个包"
    如果有多个包，建议一次性导入:例如
    ```go
    import {
        "fmt"
        "go_code/chapter06/funtiondemo"
    }
    ```

### 包的细节

1. package进行包的申明，名称建议个文件夹和文件名一样

2. main包作为程序的入口包，所以一般的main函数会放在main包下，否则不会编译执行

    ```go
    //main函数必须放在package main下
    package main
    ```

3. 打包的语法`package 包名`

4. 包的导入

    ```go
    import "fmt"
    import "go_code/chapter06/funtiondemo"
    ```

    也可以一次性导入

    ```go
    import {
        "fmt"
        "go_code/chapter06/funtiondemo"
    }
    ```

5. 引入包的语法中，包名是从$GOPATH/src后面的路径开始写，使用/进行路径分隔

    等会再重新听一下之前的课，看看当时是怎么配置环境的

6. 函数名，变量名首字母大写，函数和变量才能被其他位置访问

7. 一个目录下不能有重复的函数，在同一个目录不同源文件下也不能重复定义

8. 包名可以和文件夹名不一样，但是建议一样

    导入的是包所在文件夹的位置，函数前的定位还是包名.函数名()

9. 一个目录下的同级文件归属于一个包
    所以一个申明为aaa，另外的全部都得`package aaa`

10. 包到底是什么

    我们导入的是包的路径。在源文件层面就是一股味文件夹

    包是一组相关的源代码文件代码模块，这些文件被组织在一起，以便于管理和重用，是一组使用相同的package包名的源文件组成的代码模块

11. 可以给包起别名，但是原名称就不能用了

    ```go
    import fm "fmt"
    ```

    这样就不能使用fmt了，只能使用`fm.Println()`了

## init函数

初始化函数，用来进行一些初始化的操作，每一个源文件都可以包含一个init函数，会在main函数执行之前，被Go运行框架调用

init函数没有参数，没有返回值

```go
package main
import "fmt"
func init(){
    fmt.Println("init函数")
}
func main(){
    fmt.Println("main函数")
}
//注意输出顺序
```

输出

```shell
init函数
main函数
```

init函数先于main函数执行，后于全局变量的定义

多个源文件都有init函数的时候，执行顺序是按照文件名的ASCII码顺序执行的。不过main函数中的代码还是在最后执行

或者说，从上往下，导入的时候执行导入包里面的函数，然后全局变量，然后init函数，最后main函数

main函数|导入的包：aaa包
:-|-
import{</br>"fmt"  </br> "go_code/chapter06/aaa"}|无
无|变量定义</br>init函数</br>函数执行
无|init函数
main()中变量定义|无
init函数|无
main函数|main函数

执行原理：先执行导入的，在执行自己的

## 指针

指针是一种特殊的变量，它指向一个值的内存地址，可以用来读写内存中的值

```go
package main
import "fmt"
func main(){
    var num int = 10
    var ptr *int = &num //获取num的内存地址
    fmt.Println("num的值是",num)
    fmt.Println("num的内存地址是",ptr)
    *ptr = 20 //修改num的值
    fmt.Println("num的值是",num)
}
```

输出

```shell
num的值是 10
num的内存地址是 0xc000014080
num的值是 20
```

指针的声明方式：

```go
var ptr *int = &num
```

- `var ptr *int`声明一个指针变量ptr，指向int类型的值
- `&num`获取num的内存地址，赋值给ptr

指针的使用方式：

```go
*ptr = 20 //修改num的值
```

- `*ptr`解引用指针，获取指针指向的值
- `*ptr = 20`修改指针指向的值

指针的使用场景：

1. 作为函数参数，可以修改函数内的变量值

    ```go
    func swap(num1 *int , num2 *int){
        *num1,*num2 = *num2,*num1
    }
    ```

    - `*num1,*num2 = *num2,*num1`交换两个变量的值

2. 作为结构体字段，可以修改结构体内的变量值

    ```go
    type Person struct{
        name string
        age int
    }
    func main(){
        var p Person
        p.name = "tom"
        p.age = 20
        var ptr *Person = &p
        ptr.name = "jack"
        ptr.age = 30
        fmt.Println(*ptr)
    }
    ```

    输出

    ```shell
    {jack 30}
    ```
    - `var ptr *Person = &p`获取结构体p的内存地址，赋值给ptr
    - `ptr.name = "jack"`修改结构体内的name的值
    - `ptr.age = 30`修改结构体内的age的值
    - `fmt.Println(*ptr)`打印结构体内的name和age的值

## 匿名函数

如果某一个函数只希望使用一次的时候，可以考虑使用匿名函数

```go
package main
import "fmt"
func main(){
    //匿名函数
    func (n1 int , n2 int){
        fmt.Println(n1+n2)
    }(10,20)
}
```

输出

```shell
30
```

- `(n1 int , n2 int)`定义匿名函数，参数为两个int类型
- `(10,20)`调用匿名函数，传入两个参数

### 匿名函数可以赋值给一个变量，通过变量调用

```go
package main
import "fmt"
func main(){
    //匿名函数
    var fun = func (n1 int , n2 int){
        fmt.Println(n1+n2)
    }
    fun(10,20)
}
```

输出

```shell
30
```

- `var fun = func (n1 int , n2 int)`定义匿名函数，赋值给变量fun
- `fun(10,20)`调用匿名函数，传入两个参数

*好蠢的写法，为什么不直接写成函数*。

### 匿名函数可以作为返回值

```go
package main
import "fmt"
func main(){
    //匿名函数
    var fun = func (n1 int , n2 int) int{
        return n1+n2
    }
    fmt.Println(fun(10,20))
}
```

输出

```shell
30
```

- `var fun = func (n1 int , n2 int) int`定义匿名函数，返回值为int类型
- `fmt.Println(fun(10,20))`调用匿名函数，传入两个参数，并打印返回值

### 如何让一个匿名函数在全局中都能用

直接给一个全局变量就OK了

```go
package main
import "fmt"
var fun = func (n1 int , n2 int) int{
    return n1+n2
}
func main(){
    fmt.Println(fun(10,20))
}
```

输出

```shell
30
```

- `var fun = func (n1 int , n2 int) int`定义匿名函数，返回值为int类型
- `fmt.Println(fun(10,20))`调用匿名函数，传入两个参数，并打印返回值

## 闭包

闭包就是一个函数和其相关的引用环境组合而成的一个整体

```go
package main
import "fmt"
func getsum() func(int) int{
    //返回值为一个函数，且这个函数的参数是一个int类型的参数，返回值也是一个int类型的参数
    var n int = 10
    return func(num int) int{
        n += num
        return n
    }
    //返回了一个匿名函数，这个函数返回一个int类型的参数，接受一个int类型的参数
}

func main(){
    var f := getsum()
    fmt.Println(f(1))
    fmt.Println(f(2))
}
```

输出

```shell
11
13
```

闭包：返回的匿名函数+匿名函数以外的变量（与其相关的引用）=闭包

匿名函数中应用的那个变量会一直保存在内存中，可以一直使用，不会被释放

闭包的本质依旧是一个匿名函数，只是它需要外部的变量（外面的数值），匿名函数+引用的参数和变量====闭包

特点

1. 返回一个匿名函数，匿名函数引用到外部的变量
2. 匿名函数的参数和变量，会一直保存在内存中，不会被释放

所以闭包不能滥用，对内存消耗大

如果不适用闭包呢？

```go
package main
import "fmt"
func getsum(n int)  int{
    var sum int = 0
    sum = sum + n
    return sum
}

func main(){
    var f1 = getsum(10)
    var f2 = getsum(20)
    fmt.Println(f1)
    fmt.Println(f2)
}
```

输出

```shell
11
21
```

这样就不会有闭包的问题了

不适用闭包的时候，我想你保留的值不能反复使用，只能一次性使用

闭包则可以保留上次应用的值，可以传入一次反复使用

## defer语句

为了在函数执行完以后，及时四方资源，Go的设计者提供defer语句

```go
package main
import "fmt"
func add(num1 int,num2 int)int{
    defer fmt.Println("num1",num1)
    defer fmt.Println("num2",num2)
    
    var sum int = num1 + num2
    fmt.Println("sum",sum)
    return sum
}
func main(){
    fmt.Println("start")
    fmt.Println(add(10,20))
    fmt.Println("end")
}
```

遇到deffer之后的语句不会直接执行，需要被压入栈中，之后继续执行函数后面的语句。完事之后再从栈中取出语句执行

!!! tip "defer的执行顺序"
    栈的特点是先进后出,所以是先执行后面进去的，后执行先进去的

输出

```shell
start
sum 30
num1 10
num2 20
30
end
```

- `defer fmt.Println("num1",num1)`压入栈中，等待执行
- `defer fmt.Println("num2",num2)`压入栈中，等待执行
- `var sum int = num1 + num2`执行函数体内的语句，并计算sum的值
- `fmt.Println("sum",sum)`执行函数体内的语句，打印sum的值
- `return sum`返回sum的值，结束函数的执行
- `fmt.Println(add(10,20))`调用函数，打印返回值
- 最后执行`defer`语句，先进后出，先打印num2的值，再打印num1的值
- 然后是main函数执行`fmt.Println("add(10,20)")`，打印函数的返回值
- 最后执行`fmt.Println("end")`，打印end

### 变动

```go
package main
import "fmt"
func add(num1 int,num2 int)int{
    defer fmt.Println("num1",num1)
    defer fmt.Println("num2",num2)
    
    num1+=10//num1=20
    num2+=20//num2=40

    var sum int = num1 + num2
    fmt.Println("sum",sum)
    return sum
}
func main(){
    fmt.Println("start")
    fmt.Println(add(10,20))
    fmt.Println("end")
}
```

输出

```shell
start
sum 60
num2 20
num1 10
60
end
```

压入栈之后输出的仍然是之前的值，实际上封存了一个现场，所以输出的是之前的值

### 应用场景

比如你先关闭某个使用的资源，在使用的时候直接随手关闭defer压入，因为defer有延迟执行机制（函数执行完毕再执行defer压入栈的语句）

相当于你用完了资源，随手写了一个关闭，比较省心省事

## 字符串详解

1. `len()`:内置函数，不需要导入包。用来获取字符串的长度，返回int类型。一个汉字对应三个字节
2. `for  i ,  c  :=  range  str`:对字符串进行遍历循环
3. `r:=[]rune(str)`

    ```go
    package main
    import "fmt"
    var str = "Hello, 世界"

    func main(){
        var i int
        var c rune//c 是一个 rune 类型变量，用来存储当前遍历到的字符。
        for i, c = range str//遍历str字符，range 在这里返回两个值，索引 i 和字符 c{
            fmt.Printf("%d %c\n", i, c)
        }
        r := []rune(str)//将字符串 str 转换为一个 rune 类型的切片 r。rune 类型在 Go 中用于表示 Unicode 字符，这对于正确处理多字节字符（如中文）是必要的
        for i := 0; i < len(r); i++ {
            //这次是传统的计数器循环，用来遍历 rune 切片 r。
            fmt.Printf("%c", r[i])
        }
    ```

4. 字符串转换函数
    strconv.Atoi(s string)(i int, err error)传string得init

    strconv.Itoa(i int)string 传init的string

    `strconv.Atoi` 函数尝试将一个字符串转换为 `int` 类型，但它的返回值实际上有两个：

    1. 第一个返回值是转换后的 `int` 值。
    2. 第二个返回值是一个 `error` 值，表示转换过程中是否发生错误。

    如果转换成功，`error` 会是 `nil`；如果失败，`error` 会包含错误信息。

    ```go
    var num, err = strconv.Atoi("123")
    if err != nil {
        fmt.Println("转换错误:", err)
    } else {
        fmt.Println("转换成功:", num)
    }
    ```

    在这个修正后的代码中：

    - 我们使用两个变量 `num` 和 `err` 来接收 `strconv.Atoi` 的返回值。
    - 使用 `if err != nil` 来检查是否有错误发生，如果有，打印错误信息。
    - 如果没有错误，打印转换成功的整数值。

    这种错误处理是 Go 语言中常见的模式，确保你的代码能够优雅地处理潜在的错误情况。

    *当然，也可以直接num,_=strconv.Atoi("123")，第二个参数是错误信息，可以忽略*.

5. 统计一个字符串有几个指定的字符串

    ```go
    package main
    import "fmt"
    import "strings"
    func main(){
        var str = "hello,world"
        var num = strings.Count(str,"l")
        fmt.Println(num)
    }
    ```

    输出

    ```shell
    3
    ```

    - `strings.Count(str,"l")`统计字符串str中有几个l
    - 输出3，因为字符串str中有三个l

6. 不分大小写进行字符串的比较
    string.EqualFold(s1,s2)比较两个字符串是否相等，不区分大小写

    ```go
    package main
    import "fmt"
    import "strings"
    func main(){
        var str1 = "Hello, world"
        var str2 = "hello, WORLD"
        if strings.EqualFold(str1,str2){
            fmt.Println("相等")
        }else{
            fmt.Println("不相等")
        }
    }
    ```

    输出

    ```shell
    相等
    ```

    - `strings.EqualFold(str1,str2)`比较两个字符串是否相等，不区分大小写
    - 输出相等，因为两个字符串的大小写不区分

7. 返回的子传在字符串中第一次出现的字符串，如果没有则返回-1

    ```go
    package main
    import "fmt"
    import "strings"
    func main(){
        var str = "hello,world"
        var index = strings.Index(str,"l")
        fmt.Println(index)
    }
    ```

    输出

    ```shell
    2
    ```

8. 字符串的替换：
    
    ```go
    package main
    import "fmt"
    import "strings"
    func main(){
        var str = "hello,world"
        var newstr = strings.Replace(str,"l","*",-1)
        fmt.Println(newstr)
    }
    ```

    输出

    ```shell
    he**o,wor*d
    ```

    - `strings.Replace(str,"l","*",-1)`将字符串str中的l替为*，-1表示全部替换
    - 输出he**o,wor*d，因为l被替换成了*

9. 字符串按照字符的切割：

    strings.Split(s string, sep string)[]string

    ```go
    package main
    import "fmt"
    import "strings"
    func main(){
        var str = "hello,world"
        var arr = strings.Split(str,",")
        fmt.Println(arr)//用数组进行接受
    }
    ```

    输出

    ```shell
    [hello world]
    ```

    - `strings.Split(str,",")`按照,切割字符串str，返回一个切片
    - 输出[hello world]，因为字符串str被切割成了两个元素，第一个元素是hello，第二个元素是world

10. 大小写切换

    `strings.ToLower(s string)string`:小写切割

    `strings.ToUpper(s string)string`:大写切换

    ```go
    package main
    import "fmt"
    import "strings"
    func main(){
        var str = "Hello,world"
        var newstr = strings.ToLower(str)
        fmt.Println(newstr)
    }
    ```

    输出

    ```shell
    hello,world
    ```

    - `strings.ToLower(str)`将字符串str转换为小写
    - 输出hello,world

11. 去掉字符传两边的空格

    `strings.TrimSpace(s string)string`

    ```go
    package main
    import "fmt"
    import "strings"
    func main(){
        var str = " hello,world "
        var newstr = strings.TrimSpace(str)
        fmt.Println(newstr)
    }
    ```

    输出

    ```shell
    hello,world
    ```

    - `strings.TrimSpace(str)`去掉字符串str两边的空格
    - 输出hello,world

12. 字符串的拼接

    `strings.Join(a[]string,sep string)string`

    ```go
    package main
    import "fmt"
    import "strings"
    func main(){
        var arr = []string{"hello","world"}
        var str = strings.Join(arr," ")
        fmt.Println(str)
    }
    ```

    输出

    ```shell
    hello world
    ```

    - `strings.Join(arr," ")`将数组arr中的元素用空格拼接成一个字符串
    - 输出hello world，因为数组arr中的元素用空格拼接成了字符串str

13. 字符串的比较

    `strings.Compare(s1,s2 string)int`

    ```go
    package main
    import "fmt"
    import "strings"
    func main(){
        var str1 = "hello,world"
        var str2 = "hello,world"
        var res = strings.Compare(str1,str2)
        fmt.Println(res)
    }
    ```

    输出

    ```shell
    0
    ```

    - `strings.Compare(str1,str2)`比较两个字符串是否相等
    - 输出0，因为两个字符串相等

14. 去除字符串左右两边指定的字符串

    `strings.Trim(s string,cutset string)string`两边

    `strings.TrimLeft(s string,cutset string)string`左边

    `strings.TrimRight(s string,cutset string)string`右边

    例如：

    ```go
    package main
    import "fmt"
    import "strings"
    func main(){
        var str = "hello,world"
        var newstr = strings.Trim(str,"h")
        fmt.Println(newstr)
    }
    ```

    输出

    ```shell
    ello,world
    ```

    - `strings.Trim(str,"h")`去掉字符串str两边的h
    - 输出ello,world

15. 判断是否为指定的字符串开头/结尾

    `strings.HasPrefix(s,prefix string)bool`判断是否为指定的字符串开头

    `strings.HasSuffix(s,suffix string)bool`判断是否为指定的字符串结尾

    ```go
    package main
    import "fmt"
    import "strings"
    func main(){
        var str = "hello,world"
        var res = strings.HasPrefix(str,"h")
        fmt.Println(res)
    }
    ```

    输出

    ```shell
    true
    ```

    - `strings.HasPrefix(str,"h")`判断字符串str是否以h开头
    - 输出true，因为字符串str以h开头

16. 字符串的拆分

    `strings.Fields(s string)[]string`

    ```go
    package main
    import "fmt"
    import "strings"
    func main(){
        var str = "hello,world"
        var arr = strings.Fields(str)
        fmt.Println(arr)
    }
    ```

    输出

    ```shell
    [hello world]
    ```

    - `strings.Fields(str)`将字符串str按照空格拆分成一个切片
    - 输出[hello world]，因为字符串str被拆分成了两个元素，第一个元素是hello，第二个元素是world

17. 字符串的复制

    `strings.Repeat(s string,count int)string`

    ```go
    package main
    import "fmt"
    import "strings"
    func main(){
        var str = "hello,world"
        var newstr = strings.Repeat(str,3)
        fmt.Println(newstr)
    }
    ```

    输出

    ```shell
    hello,worldhello,worldhello,world
    ```

    - `strings.Repeat(str,3)`将字符串str重复3次
    - 输出hello,worldhello,worldhello,world

## 时间和日期的比较

首先，需要导入time包，要获取当前时间，可以使用`time.Now()`函数

```go
package main
import (
    "fmt"
    "time"
)

func main() {
    now := time.Now()
    fmt.Println(now)
}
```

输出

```shell
2021-12-12 15:30:23.123456789 +0800 CST m=+0.000000001
```

- `time.Now()`获取当前时间
- `fmt.Println(now)`打印当前时间

获取指定时间

```go
package main
import (
    "fmt"
    "time"
)

func main() {
    now := time.Now()
    fmt.Println(now)

    year, month, day := now.Date()
    hour, minute, second := now.Clock()
    year = now.Year()
    month = int(now.Month())
    day = now.Day()
    hour = now.Hour()
    minute = now.Minute()
    second = now.Second()//调用结构体中的方法
    fmt.Printf("year:%d,month:%d,day:%d,hour:%d,minute:%d,second:%d\n", year, month, day, hour, minute, second)
}
```

输出

```shell
2024-06-24 21:27:39.0311174 +0800 CST m=+0.002217701
year:2024,month:6,day:24,hour:21,minute:27,second:39
```

now函数返回的是一个结构体，可以通过`now.Date()`获取年月日，`now.Clock()`获取时分秒

对应的类型是`time.Time`，可以通过`time.Time`的方法获取年月日时分秒

甚至可以直接`int(time.month())`获取数字月份

### 日期的格式化

1. 将日期以年月日时分秒按照格式输出位字符串的形式

    我们可以用`fmt.Sprintf`函数来格式化日期

    ```go
    package main
    import (
        "fmt"
        "time"
    )

    func main() {
        now := time.Now()
        fmt.Println(now)

        str := fmt.Sprintf("%d-%02d-%02d %02d:%02d:%02d", now.Year(), now.Month(), now.Day(), now.Hour(), now.Minute(), now.Second())
        fmt.Println(str)
    }
    ```

    输出

    ```shell
    2024-06-24 21:27:39.0311174 +0800 CST m=+0.002217701
    2024-06-24 21:27:39
    ```

    - `fmt.Sprintf("%d-%02d-%02d %02d:%02d:%02d", now.Year(), now.Month(), now.Day(), now.Hour(), now.Minute(), now.Second())`格式化日期

    `printf`和`sprintf`的区别:

    - `Sprintf`：`fmt.Sprintf` 函数将格式化的字符串作为返回值返回，不直接打印到控制台或任何输出流。
    - `printf`：`fmt.Printf` 函数将格式化的字符串打印到标准输出（通常是控制台），不返回值。

2. 指定一个具体的格式
    `now.Format("2006-01-02 15:04:05")`

    ```go
    package main
    import (
        "fmt"
        "time"
    )

    func main() {
        now := time.Now()
        fmt.Println(now)

        str := now.Format("2006-01-02 15:04:05")
        fmt.Println(str)
    }
    ```

    输出

    ```shell
    2024-06-24 21:27:39.0311174 +0800 CST m=+0.002217701
    2024-06-24 21:27:39
    ```

    - `now.Format("2006-01-02 15:04:05")`指定日期的格式，输出为`2024-06-24 21:27:39`
    - 这个格式是固定的，不能随便写，字符串中各个数字必须是固定的，不能随便写。
