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
自定义函数1()对应的栈帧：num1[具体变量],num2[具体变量],num3[具体变量]|自定义函数1()对应的栈帧和main()函数对应的栈帧并不相同，两个作用在不同的栈帧，自定义函数1的栈帧在函数执行完成之后就会销毁

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

    - 建议包的名称和外面文件夹里的名称一致

    ```go
    package main
    //文件在main/main.go里
    ```

    - main包是程序的入口包，一般的main函数会放置在main下

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

## 内置函数

>字符串的`len()`就是一个内置函数，api中在buildin里面

常用的内置函数

1. new()函数:分配内存

    返回值是第一个实参对应的指针

    ```go
    package main
    import "fmt"

    func main() {
        var num = new(int)//实参是一个类型而不是具体数值
        fmt.Printf("num的类型:%T,num的值是%v,num的地址是%v,num指向的值是%v", num, num, &num,*num)//num对应的是一个int类型的指针
        fmt.Println(*num)
    }
    ```

    输出

    ```shell
    num的类型:*int,num的值是0xc00000a0b8,num的地址是0xc000074028,num指向的值是00
    ```

    - `var num = new(int)`分配一个int类型的内存，返回一个int类型的指针
    - `fmt.Printf("num的类型:%T,num的值是%v,num的地址是%v,num指向的值是%v", num, num, &num,*num)`打印num的类型、值、地址、指向的值
    - `*num`解引用指针，获取指针指向的值
    - 输出num的类型是*int，num的值是0xc00000a0b8，num的地址是0xc000074028，num指向的值是00
    new()给基本的数据结构分配内存，make()给slice、map、chan分配内存

## 错误处理

Go语言的错误处理机制是通过返回值来实现的，函数的返回值有两个部分，一个是返回值，一个是错误信息。

错误处理/错误捕获机制

go中追求代码的简洁优雅，所以引入机制:defer_recover的错误处理机制。

遇到defer，recover，panic三个关键字的时候，就要想到错误处理机制

在内置函数中就有一个recover，允许我们的程序管理panic。如果在defer函数中调用recover函数，可以捕获到panic的错误信息，然后正常处理。如果recover函数在defer函数之外调用，会直接返回nil

```go
package main

import (
    "fmt"
)

func main() {
    test()
    fmt.Println("main函数")
}

func test() {
    //利用deffer+review来捕获错误
    defer func() {
        //调用recover函数，可以捕获到panic的错误信息
        err := recover()
        //如果没有捕获到错误，返回值为零值：nil
        if err != nil {
            fmt.Println("test函数发生错误", err)
            //如果捕获到错误，可以进行相应的处理
            fmt.Println("error是",err)
        }
    }()
    num1 := 10
    num2 := 0
    res := num1 / num2
    fmt.Println(res)
}
```

输出

```shell
test函数发生错误 runtime error: integer divide by zero
main函数
```

发生了错误，但是后面仍然可以执行，不会让程序轻易地挂掉，提高了程序的健壮性。

### 自定义错误

调用`errors.New()`函数，可以返回一个错误信息

```go
package main

import (
    "errors"
    "fmt"
)

func main() {
//捕获错误
    if err := test(); err != nil {
        fmt.Println(err)
    }
    test()
    fmt.Println("main函数")
}

func test() error {
    num1 := 10
    num2 := 0
    //利用deffer+review来捕获错误
    if num2 == 0 {
        return errors.New("自定义错误")
    }else{
        res := num1 / num2
        fmt.Println(res)
    }
    return nil
}
```

输出

```shell
test函数发生错误 runtime error: integer divide by zero
main函数
```

有一种情况，程序出现错误之后，代码就没有必要执行了，现在需要让程序中断，需要panic函数

### panic函数

调用了`panic()`函数，程序就会中断，不会继续执行

```go
package main

import (
    "errors"
    "fmt"
)

func main() {
//捕获错误
    if err := test(); err != nil {
        fmt.Println(err)
        panic(err)
    }
    test()
    fmt.Println("main函数")
}

func test() error {
    num1 := 10
    num2 := 0
    //利用deffer+review来捕获错误
    if num2 == 0 {
        return errors.New("自定义错误")
    }else{
        res := num1 / num2
        fmt.Println(res)
    }
    return nil
}
```

输出

```shell
test函数发生错误 runtime error: integer divide by zero
自定义错误
```

程序中断，并打印了自定义的错误信息

主要就是两个函数：panic()和error包下的new函数

## 数组

数组的定义:`var score [5]int`

数组的初始化:`score := [5]int{100,80,90,70,80}`

有四种初始化的方式

- `var arr1 [3]int = [3]int{1,2,3}`
- `var arr2 = [3]int{1,2,3}`
- `var arr3 = [...]int{1,2,3}`
- `var arr4 = [...]int{0:1,1:2,2:3}`

数组的长度:`len(score)`，返回数组的长度

数组的遍历:`for i := 0; i < len(score); i++ {fmt.Println(score[i])}`

数组的切片:`score1 := score[1:3]`，切片是一个引用类型，底层仍然指向原数组的内存地址

数组的拷贝:`score2 := score`，拷贝的是数组的值，而不是地址

数组的比较:`if score1 == score2 {fmt.Println("数组相等")}`，数组的比较是值的比较

数组的排序:`sort.Sort(sort.IntSlice(score))`，使用sort包中的IntSlice进行排序

### 数组的内存

`score`对应的地址和`score[0]`的地址一样，然后int规定下一个地址在两个字节之外，每一种数据结构都会开辟对应的内存空间

```go
package main
import "fmt"

func main() {
    var score [5]int
    fmt.Printf("score的地址是%p,score[0]的地址是%p\n", &score, &score[0])
    fmt.Printf("score的地址是%p,score[1]的地址是%p\n", &score, &score[1])
}
```

输出

```shell
score的地址是0xc000010070,score[0]的地址是0xc000010070
score的地址是0xc000010070,score[1]的地址是0xc000010078
```

数组存储的是地址，所以赋值的时候改变的是对应地址的存储内容。数组不存储具体的值

### 数组的遍历

```go
package main
import "fmt"

func main() {
    var score [5]int = [5]int{100, 80, 90, 70, 80}
    for i := 0; i < len(score); i++ {
        fmt.Println(score[i])
    }
}
```

输出

```shell
100
80
90
70
80
```

这是常规的for循环，但是在Go语言中我们还有一种循环叫做键值循环

```go
package main
import "fmt"

func main() {
    var score [5]int = [5]int{100, 80, 90, 70, 80}
    for key ,value := range score//想遍历的数组名 {
        fmt.Printf("key:%v,value:%v\n",key,value)
    }
}
```

输出

```shell
key:0,value:100
key:1,value:80
key:2,value:90
key:3,value:70
key:4,value:80
```

- `for key ,value := range score`键值循环，key是索引，value是值
- `fmt.Printf("key:%v,value:%v\n",key,value)`打印key和value的值
- `key`和`value`属于在这个循环中的局部变量
- 键值循环可以遍历数组、切片、字典、通道等

### 注意事项

1. 数组的长度是数组类型的一部分

    ```go
    package main
    import "fmt"

    func main() {
        var score [5]int
        var score1 [6]int
        fmt.Printf("score的类型是%T,score1的类型是%T\n", score, score1)
    }
    ```

    输出

    ```shell
    score的类型是[5]int,score1的类型是[6]int
    ```

    - `var score [5]int`和`var score1 [6]int`是两个不同的类型
    - 数组的长度是数组类型的一部分

2. Go中数组属于值类型，在默认情况下是值传递，因此会进行值拷贝

    ```go
    package main
    import "fmt"


    func main() {
        var score [5]int = [5]int{100, 80, 90, 70, 80}
        test(score)
        fmt.Println(score)
    }

    func test( score [5]int) {
        score[0] = 90
        fmt.Println(score)
    }
    ```

    输出

    ```shell
    [90 80 90 70 80]
    [100 80 90 70 80]
    ```

    - `test(score)`调用test函数，传入score数组
    - `score[0] = 90`在test函数中修改数组的值
    - `fmt.Println(score)`打印数组的值，输出[90 80 90 70 80]
    - 但是在main函数中打印数组的值，输出[100 80 90 70 80]
    - 因为在test函数中修改的是局部变量。数组传递值的时候相当于在test栈帧中创建了一个新数组，并没有影响到原来的数组，所以输出的是修改后的数组的值

    如果想在修改原来的数组，需要使用指针的方式修改

    ```go
    package main
    import "fmt"

    func main() {
        var score [5]int = [5]int{100, 80, 90, 70, 80}
        test(&score)
        fmt.Println(score)
    }

    func test( score *[5]int) {
        (*score)[0] = 90
        fmt.Println(*score)
    }
    ```

    输出

    ```shell
    [90 80 90 70 80]
    [90 80 90 70 80]
    ```

    - `test(&score)`传入score数组的地址，使用指针的方式修改数组的值
    - `(*score)[0] = 90`使用指针的方式修改数组的值
    - `fmt.Println(*score)`打印数组的值，输出[90 80 90 70 80]
    - 因为在test函数中修改的是原来的数组，所以输出的是修改后的数组的值

## 切片

*slice是go语言中特有的一种数据类型*。

数组必须有明确的长度，长度不可变，切片是建立在数组之上的抽象，是更强大更便捷的功能，是对数组连续片段的一个引用。

```go
package main
import "fmt"

func main() {
    var arr = [5]int{1, 2, 3, 4, 5}//定义一个数组
    var slice []int = arr[1:3]//从index1切到3，不包括3
    /*slice()是切片的名字*/
    /*[]切片的长度，可不定义*/
    /*int类型*/
    fmt.Printf("arr的地址是%p,arr的长度是%d\n", &arr, len(arr))
    fmt.Printf("slice的地址是%p,slice的长度是%d\n", &slice, len(slice))
    fmt.Println(slice)
}
```

输出

```shell
arr的地址是0xc000010070,arr的长度是5
slice的地址是0xc000010080,slice的长度是2
[2 3]
```

- `var slice []int = arr[1:3]`定义一个切片，切片是对数组的引用，从索引1到3，不包括3
- `fmt.Printf("arr的地址是%p,arr的长度是%d\n", &arr, len(arr))`打印数组的地址和长度
- `fmt.Printf("slice的地址是%p,slice的长度是%d\n", &slice, len(slice))`打印切片的地址和长度
- `fmt.Println(slice)`打印切片的值

切片的容量cap是底层数组的长度

### 切片的内存分析

切片的地址和数组的地址是一样的，因为切片是对数组的引用

```go
package main
import "fmt"

func main() {
    var arr = [5]int{1, 2, 3, 4, 5}
    var slice []int /*这一步是定义切片*/= arr[1:3]//这一步是赋值
    fmt.Printf("arr[1]的地址是%p,slice的地址是%p\n", &arr[1], &slice)
}
```

输出

```shell
arr的地址是0xc000010070,slice的地址是0xc000010080
```

- `fmt.Printf("arr的地址是%p,slice的地址是%p\n", &arr, &slice)`打印数组和切片的地址

### 切片的定义

1. 定义一个数组，然后切片引用一个数组
2. 用make()函数创建切片：make函数可以分配并初始化一个切片，映射或者通道的对象，其第一个实参为类型，而非值。make返回的类型与其来兴相同而非它的指针，其具体结果取决于具体的类型

    调用三个参数：`var 切片名 = make([]类型,长度,容量)`

    ```go
    package main
    import "fmt"

    func main() {
        var slice1 = make([]int, 3, 5)//长度为3，容量为5
        var slice2 = make([]int, 3)//长度为3，容量为3
        fmt.Printf("slice1的地址是%p,slice1的长度是%d,slice1的容量是%d\n", &slice1, len(slice1), cap(slice1))
        fmt.Printf("slice2的地址是%p,slice2的长度是%d,slice2的容量是%d\n", &slice2, len(slice2), cap(slice2))
    }
    ```

    输出

    ```shell
    slice1的地址是0xc000010080,slice1的长度是3,slice1的容量是5
    slice2的地址是0xc000010090,slice2的长度是3,slice2的容量是3
    ```

    赋值：`slice1[0] = 1`就可以赋值len(slice1)的值，超过len(slice1)的值会报错

    PS:make()在底层创建一个数组，在外界不能直接访问，是slice对这个数组进行应用。所以不可以直接操作数组，得通过slice切片间接访问各个元素。

    *不可以直接对数组进行维护*。

### 切片的遍历

```go
package main
import "fmt"

func main() {
    var slice = []int{1, 2, 3, 4, 5}
    //普通for循环遍历切片
    for i := 0; i < len(slice); i++ {
        fmt.Println(slice[i])
    }
    fmt.Println("--------------")
    //键值循环遍历切片
    for key, value := range slice {
        fmt.Printf("key:%v,value:%v\n", key, value)
    }
}
```

实际上就是数组的遍历。

### 切片的注意事项

1. 切片定义后不能直接使用，必须要引用到一个数组，或者make到一个空间空切片来使用

2. 切片使用的时候是不能越界的

3. 简写方式

    1. `var slice = arr[0:end]`----`var slice = arr[:end]`
    2. `var slice = arr[start:len(arr)]`----`var slice = arr[start:]`
    3. `var slice = arr[0:len(arr)]`----`var slice = arr[:]`

4. 切片可以继续切片。因为切片是引用底层同一个数组的内存，所以改一个=改所有的

5. 切片可以动态增长

    ```go
    package main
    import "fmt"


    func main() {
        var slice = []int{1, 2, 3, 4, 5}
        fmt.Printf("slice的长度是%d,slice的容量是%d\n", len(slice), cap(slice))
        //使用append函数追加元素
        slice = append(slice, 6)
        //实际上是复制一个新的数组，然后再增加一个新数组的元素
        fmt.Printf("slice的长度是%d,slice的容量是%d\n", len(slice), cap(slice))
        //底层的新数组，不能直接维护，需要通过切片间接维护操作
        slice[5] = 7
        fmt.Println(slice)
        //修改切片--->修改数组
    }
    ```

    输出

    ```shell
    slice的长度是5,slice的容量是5
    slice的长度是6,slice的容量是10
    ```

    - `fmt.Printf("slice的长度是%d,slice的容量是%d\n", len(slice), cap(slice))`打印切片的长度和容量
    - `slice = append(slice, 6)`切片的追加，追加的元素是6，容量自动扩充
    - `fmt.Printf("slice的长度是%d,slice的容量是%d\n", len(slice), cap(slice))`打印切片的长度和容量，容量自动扩充到10

    也可以通过`append()`函数追加切片

    ```go
    package main
    import "fmt"

    func main() {
        var slice1 = []int{1, 2, 3}
        var slice2 = []int{4, 5, 6}
        slice1 = append(slice1, slice2...)//这三个...表示将slice2的所有元素都追加到slice1，是必须写的
        fmt.Println(slice1)
    }
    ```

    输出

    ```shell
    [1 2 3 4 5 6]
    ```

6. 切片的拷贝

    ```go
    package main
    import "fmt"


    func main() {
        var slice1 = []int{1, 2, 3}//定义一个切片
        var slice2 = make([]int, 3)//再定义一个切片
        copy(slice2, slice1)//调用内置函数copy
        fmt.Println(slice2)//输出slice2
    }
    ```

    输出

    ```shell
    [1 2 3]
    ```

    - `copy(slice2, slice1)`切片的拷贝，将slice1拷贝到slice2，slice2的长度必须大于等于slice1的长度，否则会报错
    - `fmt.Println(slice2)`打印slice2的值，输出[1 2 3]

7. 切片的比较

    ```go
    package main
    import "fmt"

    func main() {
        var slice1 = []int{1, 2, 3}
        var slice2 = []int{1, 2, 3}
        if slice1 == slice2 {
            fmt.Println("切片相等")
        } else {
            fmt.Println("切片不相等")
        }
    }
    ```

    输出

    ```shell
    切片相等
    ```

    - `if slice1 == slice2`切片的比较，切片的比较是值的比较，而不是地址的比较
    - `fmt.Println("切片相等")`打印切片相等

## 映射：map

map是一种内置的数据结构，它将键值对=相互关联，可以通过key获得对应的value

键值对：一对匹配的信息，就是python里的字典。

基本语法：`var map1 map[key类型]value类型`

1. key和value的类型：bool,init,string,指针,结构体等

2. 定义：`var map1 map[string]int`，定义一个key是string类型，value是int类型的map

    如果只申明map，没有初始化，是没有内存空间的，必须通过make函数进行初始化，才会分配对应的空间

    `map1 = make(map[string]int,10)`10指的是map可以存放10个键值对

    ```go
    package main
    import "fmt"

    func main() {
        var map1 map[string]int
        map1 = make(map[string]int, 10)
        map1["a"] = 100
        fmt.Println(map1)
    }
    ```

    输出

    ```shell
    map[a:100]
    ```

    - `var map1 map[string]int`定义一个key是string类型，value是int类型的map。前面是key，后面是value
    - `map1 = make(map[string]int,10)`初始化map，10指的是map可以存放10个键值对
    - `map1["a"] = 100`给map1中添加一个键值对，键是"a"，值为100
    - `fmt.Println(map1)`打印map1的值，输出map[a:100]
3. 赋值：`map1["a"] = 100`，给map1中添加一个键值对，键是"a"，值为100

4. 取值：`value,ok := map1["a"]`，通过键"a"获取值，ok是bool类型，表示是否存在这个键值对

5. 删除：`delete(map1, "a")`，删除键值对

6. 遍历：`for key, value := range map1`，键值循环遍历map

map的特点

1. map在使用前一定要make

2. key和value是无序存储的

3. key的部分是不能重复的，value是可以重复的（如果重复，后面写入的会把前面的替换了）

4. size是可以省略不写的，默认情况会分配一个空间（显然，这是不够用的）

### map的三种创建方式

1. `var a map[int]string`和`a = make(map[int]string,10)`的方式
2. `b := make(map[int]string)`合成一句话，并且没有定义长度
3. `c := map[int]string{1:"a",2:"b",}`直接定义一个map(初始化的时候每一个后面都要加逗号)

如何继续添加呢？

### 对map的操作：增删改查

1. 增加`map["key"]=value`原来有的话=修改，没有的话=增加
2. 删除：使用delete()内置函数`delete(map_name,"key_name")`如果没有这个key，也不会报错，不执行任何操作
3. 修改：直接赋值`map["key"]=value`
4. 查找：使用key直接获取`value,ok := map["key"]`，ok是bool类型，表示是否存在这个键值对
5. 清空操作；需要遍历map逐个删除。或者重新make一个新的map然后扔掉老的map就行了

6. 遍历：`for key, value := range map1`，键值循环遍历map(只支持键值循环)

```go
package main
import "fmt"

func main() {
    var map1 map[string]int
    map1 = make(map[string]int, 10)
    map1["a"] = 100
    map1["b"] = 200
    fmt.Println(map1)
    value, ok := map1["a"]
    if ok {
        fmt.Println(value)
    } else {
        fmt.Println("key不存在")
    }
    delete(map1, "a")
    fmt.Println(map1)
    for key, value := range map1 {
        fmt.Printf("key:%v,value:%v\n", key, value)
    }
}
```

输出

```shell
map[a:100 b:200]
100
map[b:200]
key:b,value:200
```

- `value, ok := map1["a"]`通过键"a"获取值，ok是bool类型，表示是否存在这个键值对
- `if ok {fmt.Println(value)} else {fmt.Println("key不存在")}`判断是否存在这个键值对，存在的话打印值，不存在的话打印"key不存在"
- `delete(map1, "a")`删除键值对
- `for key, value := range map1`键值循环遍历map

加深难度

```go
package main
import "fmt"

func main() {
    var map1 map[string]map[string]string
    map1 = make(map[string]map[string]string)
    map1["a"] = make(map[string]string)
    map1["b"] = make(map[string]string)
    map1["b"]["name"] = "李四"
    map1["b"]["age"] = "20"
    map1["b"]["address"] = "上海"

    map1["c"] = make(map[string]string)
    map1["c"]["name"] = "王五"
    map1["c"]["age"] = "22"
    map1["c"]["address"] = "广州"
    
    map1["a"]["name"] = "张三"
    map1["a"]["age"] = "18"
    map1["a"]["address"] = "北京"
    fmt.Println(map1)
    for key, value := range map1 {
        for key1, value1 := range value {
            fmt.Printf("\t%v:%v\n", key1, value1)
        }
    }
}
```

输出

```shell
a
        name:张三
        age:18
        address:北京
b
        name:李四
        age:20
        address:上海
c
        name:王五
        age:22
        address:广州
```

- `var map1 map[string]map[string]string`定义一个key是string类型，value是map类型的map
- `map1["a"] = make(map[string]string)`给map1中添加一个键值对，键是"a"，值为一个新的map
- `map1["a"]["name"] = "张三"`给map1中添加一个键值对，键是"name"，值为"张三"
- `fmt.Println(map1)`打印map1的值
- `for key, value := range map1`键值循环遍历map，打印key和value
- `for key1, value1 := range value`再次键值循环遍历value，打印key1和value1

数组中可以装数组，切片中可以切切片，map中也可以装map

## 面相对象编程

1. Go也支持面向对象变成（oop），但是和传统的面相对象编程有区别，并不是纯粹的面相对象编程语言。所以是支持面向对象特性是比较准确的
2. Golang没有类的概念（class）Go语言的结构体和其他编程语言的class有同等的地位，可以理解为Go语言是基于结构体来实现面向对象编程的。
3. Go语言面向对象编程非常简洁，去掉了传统面向对象语言的继承、方法重载、构造函数和析构函数、隐藏的this指针等等。Go语言仅支持封装、继承和多态特性
4. Go语言仍然有面向对象编程的继承，封装，和多态的特性，只是实现的方式和其他OOP语言不一样。

### 结构体

结构体的引入：

具体的对象，有很多的属性，这些属性是可以被封装的，这个时候就需要结构体

可以使用变量来处理，但是变量的缺点是：不够直观，不够清晰，不利于数据的管理和维护。

创建一个结构体

```go
type Person struct {
    Name string
    Age  int
}
```

- `type Person struct`定义一个结构体，结构体的名字叫Person
- `Name string`定义一个string类型的属性，名字叫Name
- `Age  int`定义一个int类型的属性，名字叫Age

#### 结构体的使用

```go
package main

import "fmt"

type Person struct {
    Name string
    Age  int
}

func main() {
    var p1 Person//定义Person这种数据结构的结构体的一个变量，在未赋值的时候默认为0
    p1.Name = "张三"
    p1.Age = 20
    fmt.Println(p1)
}
```

分析一下内存

在内存中有一个p1的变量，指向一个内存空间（是一个值类型）。在内存空间里有name，age两个种类，指向不同的内存空间。最开始是空，赋值的过程就是把空间从空变成各种值

#### 结构体实例的创建

1. 直接创建（上面）
2. `var p1 Person = Person={"monkey",21,"南京理工"}`
3. 返回的是结构体的一个指针
    `var p *Perple = new(Teacher)`
    p是一个指针，实际上就是地址，应该给这个地址指向的对象的字段进行复制操作
    `*p`获取到指针对应的值，`(*p).name="jjy"`
    *的作用获取给地址指向的值。取到那个对象之后，对这个值进行过赋值

    为了符合程序员的编程习惯，提供了简化的复制方式，也可直接给p.age赋值。编译器实际上对p.age进行了转化

```go
type Person struct{
    Name string
    age init
}
```

这个不能少

这四种方式，都得会，得看明白是什么意思

#### 结构体的一个转换

1. 结构体是用户单独定义的一个类型，和其他的类型转换时，必须有完全相同的字段
2. 结构体进行type转换重新定义，相当于取别名，Golang认为是最新的数据类型，但是相互之间可以强转。

```go
type Person struct {
    Name string
    Age  int
}
type Student struct {
    Name string
    Age  int
}

func main() {
    var p1 Person
    p1.Name = "张三"
    p1.Age = 20
    var stu Student
    stu = Student(p1)//把p强制转化成Student类型
    fmt.Println(stu)
}
```

`type Stu Student`也不能使Stu类型和Student类型相同

所以还是得强制转换

#### 结构体的方法

方法是作用在指定的数据类型上，和指定的数据类型绑定的，因此自定义类型都可以有方法，而不仅仅是struct

```go
type Person struct {
    Name string
    Age  int
}
//给oerson结构体绑定一个方法
func (p Person) SayHello() {
    fmt.Println("大家好，我是", p.Name, "我今年", p.Age, "岁")
}

func main() {
    var p1 Person
    p1.Name = "张三"
    p1.Age = 20
    p1.SayHello()
}
```

- `func (p Person) SayHello()`定义一个方法，方法的名字叫SayHello，参数是Person类型，用的时候用`p.SayHello()`调用
- `p.Name`和`p.Age`是Person类型的方法可以访问的属性
- `p.SayHello()`调用Person类型的方法

输出

```shell
大家好，我是 张三 我今年 20 岁
```

1. 参数方法的名字随意起名
2. 结构体和方法绑定，只能在指定的数据类型进行绑定
3. 如果其他类型的变量调用，会报错
4. 这个结构体的对象传入的方法中，实际上是一个值传递（结构体都是值传递）和函数的参数传递一致，传入的是一个副本，不会改变原来的值

实际上基本的数据类型也可以创造自己的方法

```go
package main
import "fmt"

type MyInt int//对int类型重新定义，用来绑定方法

func (m MyInt) Add(n MyInt) MyInt {
    return m + n
    //给int类型绑定换一个方法，用m.Add(n)调用。
}

func main() {
    var m1 MyInt
    m1 = 1//m在原方程中是形参，这里m1才是实参
    fmt.Println(m1.Add(2))
}
```

- `type MyInt int`定义一个MyInt类型，int的别名
- `func (m MyInt) Add(n MyInt) MyInt`给MyInt类型绑定一个方法，方法的名字叫Add，参数是MyInt类型，返回值也是MyInt类型
- `m1.Add(2)`调用MyInt类型的方法，返回值是MyInt类型

输出

```shell
3
```

方法的访问控制权限也是小写只能在本包内被访问
