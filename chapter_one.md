## hello world
- Go被安装在 ̃/go，而$GOROOT被设置为GOROOT= ̃/go;
- 希望编译的Go代码放在 ̃/g/src而$GOPATH设置为GOPATH= ̃/g。在使用包的 时候需要用到这个变量

## 变量、类型、关键字
- 一般情况下，不需要使用分号
- go语言中变量的类型在变量名的后面 (a int),当定义了一个变量，它默认赋值为其类型的null值，也就意味着，在
var a int 后，a的值为0.而var s string，意味着s被赋值为零长度字符串，也就是"".
- 在 Go 中，声明和赋值是两过程，但是可以连在一起。比较下面作用相同的代码片段。
Listing 1.2. 用 = 声明
```
var a int 
var b bool 
a = 15
b = false
```

Listing 1.3. 用:= 声明 
```
a :=15
b := false
```

在左边使用了关键字 var 声明变量，然后赋值给它。右边的代码使用了 := 使得在一 步内完成了声明和赋值(这一形式只可用在函数内)。在这种情况下，变量的类型是由 值推演出来的。值 15 表示是 int 类型，值 false 告诉 Go 它的类型应当是 bool。多 个 var 声明可以成组;const 和 import 同样允许这么做。留意圆括号的使用:
```
var (
x int
b bool )

```

- 有相同类型的多个变量同样可以在一行内完成声明:var x, y int 让 x 和 y 都是 int 类型变量。同样可以使用 平行赋值:
`a, b := 20, 16`
让a和b都是整数变量，并且赋值 20 给a，16 给b。

- 一个特殊的变量名是 _(下划线)。任何赋给它的值都被丢弃。在这个例子中，将 35 赋值给 b，同时丢弃 34。
`_, b := 34, 35`
Go 的编译器对声明却未使用的变量在报错。下面的代码会产生这个错误:声明了 i
却未使用

```
package main 
func main() { 
var i int
}

```
- 需要留意的是这些类型全部都是独立的，并且混合用这些类型向变量赋值会引起编译 器错误，例如下面的代码:
Listing 1.4. 相似的类型都是独立的

```
package main
func main() {
    var a int //← 通用整数类型
    var b int32  //← 32 位整数类型 
    a = 15
    b = a + a //← 混合这些类型是非法的
    b = b + 5 //← 5 是一个(未定义类型的)常量，所以这没􏱀问题

}
```

`types.go:7: cannot use a + a (type int) as type int32 in assignment`
赋值可以用八进制、十六进制或科学计数法:077，0xFF，1e3 或者 6.022e23 这些都是合法的


### 常量
常量在 Go 中，也就是 constant。它们在编译时被创建，只能是数字、字符串或布尔
值;
const x = 42 生成 x 这个常量。
可以使用 iota c 生成枚举值。
 
```
const (
    a= iota 
    b= iota
)
```

第一个 iota 表示为 0，因此 a 等于 0，当 iota 再次在新的一行使用时，它的值增加
了 1，因此 b 的值是 1。
也可以像下面这样，省略 Go 重复的 = iota:

```
const (
    a= iota
    b   ← Implicitly b = iota
```

如果需要，可以明确指定常量的类型:

```
const (
    a = 0   ← Is an int now
    b string = "0"
)
```

### 字符串
另一个重要的内建类型是 string。赋值字符串的例子:

 `s := "Hello World!"`
 
字符串在 Go 中是 UTF-8 的由双引号(”)包裹的字符序列。如果你使用单引号(’)则 表示一个字符(UTF-8 编码)——这种在 Go 中不是 string。
一旦给变量赋值，字符串就不能修改了:在 Go 中字符串是不可变的。从 C 来的用户， 下面的情况在 Go 中是非法的。

```
var s string = "hello"
s[0] = 'c' ← 修改第一个字符为’c’，这会报错
```

在 Go 中实现这个，需要下面的方法: 

`s := "hello"`

0. `c := []rune(s)`

1. `c[0] = 'c'`

2. `s2 := string(c) `

3. `fmt.Printf("%s\n", s2) `

>0. 转换 s 为 rune 数组，查阅在第 4 章 “转换” 节、59 页的内容;
>1. 修改数组的第一个元素; 
>2. 创建新的字符串 s2 保存修改; 
>3. 用 fmt.Printf 函数输出字符串。

#### 多行字符串
 基于分号的置入(查阅文档 [8] 的 “分号” 章节)，你需要小心使用多行字符 串。如果这样写:

```
s := "Starting part"
    + "Ending part"
```

会被转换为:

```
s := "Starting part"; 
    + "Ending part";

```

这是错误的语法，应当这样写:

```
s := "Starting part" +
     "Ending part"
```

Go 就不会在错误的地方插入分号。另一种方式是使用反引号\` 作为原始字符串符号:

```
s := `Starting part
     Ending part`

```

留意最后一个例子 s 现在也包含换行。不像转义字符串标识 ，原始字符串标识的值 在引号内的字符是不转义的。

#### rune
>Rune 是 int32 的别名。用 UTF-8 进行编码。
这个类型在什么时候使用呢?
例如需要遍 历字符串中的字符。
可以循环每个字节(仅在使用 US ASCII 编码字符串时与字符等价， 而它们在 Go 中不存在!)。因此为了获得实际的字符，需要使用 rune 类型。

#### 复数
Go原生支持复数。它的变量类型是 complex128 (64 位虚数部分)。如果需要小一些 的，还有 complex64 – 32 位的虚数部分。复数写为 re + imi，re 是实数部分，im 是 虚数部分，而 i 是标记 ’i’ (√−1)。使用复数的一个例子:

`var c complex64 = 5+5i;fmt.Printf("Value is: %v", c)`

将会打印:(5+5i)
>Printf() 的 %v 参数 含义是 “用默认格 式打印这个值”。

#### 错误
任何足够大的程序或多或少都会需要使用到错误报告。因此 Go 有为了错误而存在的
内建类型，叫做 error。

>var e error 定义了一个 error 类型的变量 e，其的值是 nil。这个 error 类型是一个接
口——在第 “接口” 章将会对此进行解释。


### 运算符和内建函数
Go 支持普通的数字运算符，表格 1.1 列出了当前支持的运算符，以及其优先级。它们 全部是从左到右结合的。
Table 1.1. 运算优先级 

Precedence Operator(s)

Highest * / % << >> & &^ +-|^
           == != < <= > >= <-
           && 
           
Lowest ||

`+ - * / 和 % `会像你期望的那样工作，
`& | ^ 和 &^` 分别表示位运算符按位与，
按位 或，按位异或和位清除。&& 和 || 运算符是逻辑与和逻辑 或。
表格中没有列出的是逻 辑非:!。

虽然 Go 不支持运算符重载(或者方法重载)，而一些内建运算符却支持重载。例如 + 可以用于整数、浮点数、复数和字符串(字符串相加表示串联它们)。


### Go关键字
Table 1.2. Go 中的关键字：
>break case chan const continue default func defer go else goto fallthrough if for import interface map package range return select struct switch type var

表格 1.2 列出了 Go 中所有的关键字。在下面的段落和章节中会介绍它们。其中有一些 已经遇到过了。

- var 和 const 参阅 “变量、类型和关键字” 在第 3 页;
- 在“HelloWorld”部分，package和import已经有过短暂的接触。在第3章对其有详细的描述。

其他都有对应的介绍和章节:

- func 用于定义函数和方法;
- return 用于从函数返回，func 和 return 参阅第 2 章了解详细信息; • go 用于并行，参阅第 6 章;
- select 用于选择不同类型的通讯，参阅第 6 章;
- interface 参阅第 5 章;
- struct 用于抽象数据类型，参阅第 4 章;
- type 同样参阅第 4 章。

### 控制结构
在 Go 中只有很少的几个控制结构。

例如这里没有 do 或者 while 循环，只有 for。

有 (灵活的)switch 语句和 if，而 switch 接受像 for 那样可选的初始化语句。

还有叫做类型选择和多路通讯转接器的 select(参阅第 6 章)。语法有所不同(同 C 相比): 无需圆括号，而语句体必须总是包含在大括号内。

>if-eles
```
 i f x > 0 { 
    return y
 } else { 
    return x
 }

``` 

>强制大括号鼓励将简单的 if 语句写在多行上。无论如何，这都是一个很好的形式，尤
其是语句体中含有控制语句，例如 return 或者 break。

if 和 switch 接受初始化语句，通常用于设置一个(局部)变量。

```
if err := Chmod(0664); err != nil { ←nil 与 C 的 NULL 类似 
    fmt.Printf(err) ← err 的作用域被限定在 if 内
    return err
}

```

可以像通常那样使用逻辑运算符(参考 1.1 表格):


```

if true&&true {
    fmt.Println("true")
}
if !false{
    fmt.Println("true")
}

```

在 Go 库中，你会发现当一个 if 语句不会进入下一个语句流程 – 也就是说，语句体结
束于 break，continue，goto 或者 return – 不必要的 else 会被省略。

```
f, err := os.Open(name, os.O_RDONLY, 0) if err !=nil {
return err }
doSomething(f)

```

>这个例子通常用于检测可能的错误序列。成功的流程一直执行到底部使代码很好读， 
当遇到错误的时候就排除它。这样错误的情况结束于 return 语句，这样就无须 else 语句。

```
f, err := os.Open(name, os.O_RDONLY, 0) 
if err !=nil {
    return err 
}
d, err := f.Stat() 
if err !=nil {
    return err 
}
doSomething(f, d)

```

下面的语法在 Go 中是非法的: 

```
if err !=nil
 { }
← 必须同 if 在同一行
return err
```

#### goto

Go 有 goto 语句——明智的使用它。用 goto 跳转到一定是当前函数内定义的标签。例 如假设这样一个循环:

```
func myfunc() { 
    i := 0
 Here: //← 这行的第一个词，以分号结束作为标签 
    println(i)
    i++
    goto Here   //← 跳转

}
```

标签名是大小写敏感的。

#### for

Go 的 for 循环有三种形式，只有其中的一种使用分号。

`for init; condition; post { }`

`for condition { }`

`for {}`

短声明使得在循环中声明一个序号变量更加容易。

```
sum := 0
for i:=0; i<10; i++ {
    sum+=i ←sum = sum + i 的简化写法 
} ← i 实例在循环结束会􏱅失
```

最后，由于 Go 没有逗号表达式，而 ++ 和 – 是语句而不是表达式，如果你想在 for 中 执行多个变量，应当使用 平行赋值。

```
// Reverse a
for i,j:=0, len(a)-1; i<j; i,j=i+1,j-1 {
    a[i], a[j] = a[j], a[i] ← 平行赋值 
}
```

#### break 和 continue

利用 break 可以提前退出循环，break 终止当前的循环。

```
for i:=0; i<10; i++ { 
    if i>5{
        break ← 终止这个循环，只打印 0 到 5 
    }
    println(i)
}
```

循环嵌套循环时，可以在 break 后指定标签。用标签决定哪个循环被终止:

```
J: for j:=0; j<5; j++ {
    for i:=0; i<10; i++ {
        if i>5{ 
            break J //← 现在终止的是 j 循环，而不是 i 的那个
        }
        println(i)
    }
}

```


利用 continue 让循环进入下一个迭代，而略过剩下的所有代码。下面循环打印了 0 到 5。

```
for i:=0; i<10; i++ { 
    if i>5{
        continue ← 跳过循环中所有的代码 println(i)
    }

```

#### range
>关键字 range 可用于循环。
它可以在 slice、array、string、map 和 channel(参阅第 6 章)。range 是个迭代器，当被调用的时候，从它循环的内容中返回一个键值对。基于 不同的内容，range 返回不同的东西。

>当对 slice 或者 array 做循环时，range 返回序号作为键，这个序号对应的内容作为值。 考虑这个代码:

```
0  list := []string {"a", "b", "c", "d", "e", "f"}
1  for k, v := range list{
2  // 对 k 和 v 做想做的事情
   }

```
>0 创建一个字符串的 slice(参阅 “array、slices 和 map” 的第 14 页)。

>1 用 range 对其进行循环。每一个迭代，range 将返回 int 类型的序号，string
类型的值，以 0 和 “a” 开始。

>2 k的值为0...5，而v在循环从“a”...“f”。

也可以在字符串上直接使用 range。这样字符串被打散成独立的 Unicode 字符 e 并且 起始位按照 UTF-8 解析。循环:

```
for pos, char := range "aΦx" {
    fmt.Printf("character '%c' starts at byte position %d\n", char
, pos)
}

```

#### 打印
 
 ```
  character 'a' starts at byte position 0
  character 'Φ' starts at byte position 1
  character 'x' starts at byte position 3 ← Φ took 2 bytes

 ```
 

 #### switch
 Go 的 switch 非常灵活。表达式不必是常量或整数，执行的过程从上至下，直到找到匹 配项，而如果 switch 没有表达式，它会匹配 true 。这产生一种可能——使用 switch 编写 if-else-if-else 判断序列。
 
 ```
 func unhex(c byte) byte { 
    switch {
        case '0'<=c&&c<='9': 
            return c - '0'
        case 'a'<=c&&c<='f': 
            return c - 'a' + 10
        case 'A'<=c&&c<='F': 
            return c - 'A' + 10
    }
    return 0 
 }
 ```
 
 它不会匹配失败后自动向下尝试，但是可以使用 fallthrough 使其这样做。没 有 fallthrough:
 
 ```
 switch i { 
    case 0: // 空的 case 体
    case 1: 
        f() // 当 i==0 时，f 不会被调用!
 }
 ```
 
 而这样:
 
 ```
 switch i { 
    case 0: fallthrough
    case 1: 
        f()  // 当 i==0 时，f 会被调用!
 }
 ```
 
 用 default 可以指定当其他所有分支都不匹配的时候的行为。
 
 ```
  switch i { 
    case 0: 
    case 1:
        f()
    default:
        g() //当i不等于0或1时调用
  }

 ```

 分支可以使用逗号分隔的列表。

```
 func shouldEscape(c byte) bool { 
    switch c {
        case ' ', '?', '&', '=', '#', '+': 
            return true
    }
    return false 
 }
 ← , as ”or”
```

 这里有一个使用两个 switch 对字节数组进行比较的例子: 
 
 ```
0
 func Compare(a, b []byte) int {
    for i:=0; i< len(a)&&i< len(b); i++ {
    switch {
    case a[i] > b[i]:
        return 1
    case a[i] < b[i]:
        return -1 
    }
 }
1 switch { 
    case len(a) < len(b):
        return -1
    case len(a) > len(b):
        return 1 }
2    return 0
}
```

 >0 比较返回两个字节数组字典数序先后的整数。
 如果 a == b 返回 0，如果 a < b 返回 -1，而如果 a > b 返回 +1; 
 
 >1 长度不同，则不相等;
 
 >2 字符串相等。 
 
 ### 内建函数
 
 预定义了少数函数，这意味着无需引用任何包就可以使用它们。表格 1.3 列出了所有 的内建函数。f
 
 >Table 1.3. Go 中的预定义函数
 
 >close new panic complex delete make recover real len append print imag cap copy println


 这些内建函数的文档记录在跟随最近的 Go 版本一起发布的伪包 builtin 中。
 
 #### close
 用于 channel 通讯。使用它来关闭 channel，参阅第 6 章了解更多。 
 
 #### delete
 用于在 map 中删除实例。
 
 #### len 和 cap
 可用于不同的类型，len 用于返回字符串、slice 和数组的长度。参阅 “array、slices 和 map” 小节了解更多关于 slice、数组和函数 cap 的详细信息。
 
 #### new
 用于各种类型的内存分配。参阅 “用 new 分配内存” 的第 55 页。 
 
 #### make
 用于内建类型(map、slice 和 channel)的内存分配。参阅 “用 make 分配内存” 的第 55 页。
 
 #### copy
 用于复制 slice。参阅本章的 “slice”。 
 
 #### append
 用于追加 slice。参阅本章的 “slice”。
 
 #### panic 和 recover
 用于异常处理机制。参阅 “恐慌(Panic)和恢复(Recover)” 的第 32 页了解更 多信息。
 
 #### print 和 println
 是底层打印函数，可以在不引入 fmt 包的情况下使用。它们主要用于调试。
 
 #### complex、real 和 imag
 全部用于处理 复数。有了之前给的简单的例子，不用再进一步讨论复数了。
 
 ### array、slices 和 map
 可以利用 array 在列表中进行多个值的排序，或者使用更加灵活的:slice。字典或哈希
 类型同样可以使用，在 Go 中叫做 map。 
 
 #### array
 array 由 [n]<type> 定义，n 标示 array 的长度，而 <type> 标示希望存储的内容的类 型。对 array 的元素赋值或索引是由方括号完成的:
 
 ```
 var arr [10]int
 arr[0] = 42
 arr[1] = 13
 fmt.Printf("The first element is %d\n", arr[0])
 ```
 
 
>像 var arr = [10]int 这样的数组类型有固定的大小。
 大小是类型的一部分。由于不 同的大小是不同的类型，因此不能改变大小。
 数组同样是值类型的:将一个数组赋值给另一个数组，会复制所有的元素。尤其是当向函数内传递一个数组的时候，它会获得一个数组的副本，而不是数组的指针。
 
 可以像这样声明一个数组:`var a [3]int`，如果不使用零来初始化它，则用复合声明: `a := [3]int{1, 2, 3}` 也可以简写为 `a := [...]int{1, 2, 3}`，Go 会自动统计元素 的个数。
 
 注意，所有项目必须都指定。因此，如果你使用多维数组，有一些内容你必须录入:
 
 `a := [3][2]int { [2]int {1,2}, [2]int {3,4}, [2]int {5,6} }`
 
 类似于:

 `a := [3][2]int { [...]int {1,2}, [...]int {3,4}, [...]int {5,6} }`
 
 声明一个 array 时，你必须在方括号内输入些内容，数字或者三个点 (...)。在很久之前，这个语法被进一步简化，这里是来自之前的发布日志:
 >array、slice 和 map 的复合声明变得更加简单。使用复合声明的 array、slice 和 map，元素复合声明的类型与外部一致，则可以省略。
 
 这表示上面的例子可以修改为:
 
 `a := [3][2]int { {1,2}, {3,4}, {5,6} }`
 
 #### slice
 >slice 与 array 接近，但是在新的元素加入的时候可以增加长度。slice 总是指向底层的 一个 array。slice 是一个指向 array 的指针，这是其与 array 不同的地方;slice 是引用 类型，这意味着当赋值某个 slice 到另外一个变量，两个引用会指向同一个 array。例 如，如果一个函数需要一个 slice 参数，在其内对 slice 元素的修改也会体现在函数调 用者中，这和传递底层的 array 指针类似。通过:
 
 `sl := make([]int, 10)`
 
 >创建了一个保存有 10 个元素的 slice。需要注意的是底层的 array 并无不同。slice 总 是与一个固定长度的 array 成对出现。其影响 slice 的容量和长度。 图 1.1 描述了下面 的 Go 代码。首先创建了 m 个元素长度的 array，元素类型 int:var array[m]int 然后对这个 array 创建 slice:slice := array[0:n]
 
 然后现在有:
 ![](https://cdn.nlark.com/yuque/0/2020/png/264652/1580558493439-12cdecc4-3a45-45bd-9e38-fafe74a0f7c3.png)
 
 给定一个 array 或者其他 slice，一个新 slice 通过 a[I:J] 的方式创建。这会创建一个 新的 slice，指向变量 a，从序号 I 开始，结束在序号 J 之前。长度为 J - I。
 
 ```
 // array[n:m] 从 array 创建了一个 slice，具有元素 n 到 m-1 
 0 a := [...]int {1, 2, 3, 4, 5} 
 1 s1 := a[2:4] 
 2 s2 := a[1:5]
 3 s3 := a[:]
 4 s4 := a[:4]
 5 s5 := s2[:]
  ```
 
 0 定义一个 5 个元素的 array，序号从 0 到 4;
 
 1 从序号 2 至 3 创建 slice，它包含元素 3, 4; 
 
 2 从序号1至4创建，它包含元素2,3,4,5; 
 
 3 用 array 中的所有元素创建 slice，这是 a[0:len(a)] 的简化写法; 
 
 4  从序号 0 至 3 创建，这是 a[0:4] 的简化写法，得到 1, 2, 3, 4; 
 
 5 从 slice s2 创建 slice，注意 s5 仍然指向 array a。


>在 1.5 列出的代码中，我们在第八行尝试做一些错误的事情，让一些东西超出范围(底
层 array 的最大长度)，然后得到了一个运行时错误。 

Listing 1.5. array 和 slice

```
package main
func main() {
    var array [100]int //← Create array, index from 0 to 99
    slice := array[0:99] //← Create slice, index from 0 to 98
    slice[98] = 'a' //0k
    slice[99] = 'a' //← Error: ”throw: index out of range” 8
}

```

如果你想要扩展 slice，有一堆内建函数让你的日子更加好过一些:append 和 copy。来自于 [10]:
>函数 append 向 slice s 追加零值或其他 x 值，并且返回追加后的新的、与 s 有相同类型的 slice。如果 s 没有足够的容量存储追加的值，append 分配一 个足够大的、新的 slice 来存放原有 slice 的元素和追加的值。因此，返回 的 slice 可能指向不同的底层 array


  
s0:= []int {0, 0}

0 s1:= append(s0, 2) 

1 s2 := append(s1, 3, 5, 7) 

2 s3:= append(s2, s0...) 


0 追加一个元素，s1 == []int{0, 0, 2};

1 追加多个元素，s2 == []int{0, 0, 2, 3, 5, 7};

2 追加一个 slice，s3 == []int{0, 0, 2, 3, 5, 7, 0, 0}。注意这三个点!


还有
>函数 copy 从源 slice src 复制元素到目标 dst，并且返回复制的元素的个 数。源和目标可能重􏱆。元素复制的数量是 len(src) 和 len(dst) 中的最 小值。

```
var a=[...]int{0,1,2,3,4,5,6,7}
var s = make([]int, 6)
n1 := copy(s, a[0:]) ←n1 == 6, s == []int{0, 1, 2, 3, 4, 5} 
n2 := copy(s, s[2:]) ←n2 == 4, s == []int{2, 3, 4, 5, 4, 5}
```

#### map
许多语言都内建了类似的类型，例如 Perl 有哈希，Python 有字典，而 C++ 同样也 有 map(作为库)。在 Go 中有 map 类型。map 可以认为是一个用字符串做索引的数 组(在其最简单的形式下)。下面定义了 map 类型，用于将 string (月的缩写)转换 为 int – 那个月的天数。一般定义 map 的方法是:`map[<from type>]<to type>`

```
monthdays := map[string]int {
    "Jan": 31, "Feb": 28, "Mar": 31,
    "Apr": 30, "May": 31, "Jun": 30,
    "Jul": 31, "Aug": 31, "Sep": 30,
    "Oct": 31, "Nov": 30, "Dec": 31, ← 逗号是必须的
 }

```

留意，当只需要声明一个 map 的时候，使用 make 的形式:`monthdays := make(map [string]int)`

当在 map 中索引(搜索)时，使用方括号。例如打印出 12 月的天数:`fmt.Printf(" %d\n", monthdays["Dec"])`

当对 array、slice、string 或者 map 循环遍历的时候，range 会帮助你，每次调用，它 都会返回一个键和对应的值。

```
year := 0
for _, days := range monthdays { ← 键没有使用，因此用 _, days
    year += days
}
fmt.Printf("Numbers of days in a year: %d\n", year) 
```

向 map 增加元素，可以这样做:

```
monthdays["Undecim"] = 30 ← 添加一个月 
monthdays["Feb"] = 29 ← 􏱇年时重写这个元素
```

检查元素是否存在，可以使用下面的方式 [19]:

```
var value int
var present bool
value, present = monthdays["Jan"] ← 如果存在，present 则有值 true ← 或者更接近 Go 的方式
v, ok := monthdays["Jan"] ← “逗号 ok”形式
``` 

也可以从 map 中移除元素:

`delete(monthdays, "Mar") ← 删除”Mar” 吧，总是下􏱁的月􏱂`

通常来说语句 delete(m, x) 会删除 map 中由 m[x] 建立的实例。


## QAQ
### Q1. (0) For-loop
1. 创建一个基于for的简单的循环。使其循环10次，并且使用fmt包打印出计数器的值。
2. 用goto改写1的循环。关键字for不可使用。
3. 再次改写这个循环，使其遍历一个array，并将这个array打印到屏幕上。

### Q2. (0) FizzBuzz
1.解决这个叫做Fizz-Buzz[23]的问题:
>编写一个程序，打印从 1 到 100 的数字。当是三个􏱃数就打印 “Fizz” 代替数字，当是􏱄的􏱃数就打印 “Buzz”。当数字同时是三和􏱄的􏱃数 时，打印 “FizzBuzz”。
    
  
### Q3. (1)字符串
1.建立一个Go程序打印下面的内容(到100个字符): 
A
AA
AAA
AAAA
AAAAA
AAAAAA
AAAAAAA
...

2.建立一个程序统计字符串里的字符数量:

`asSASA ddd dsjkdsjs dk`

同时输出这个字符串的字节数。提示: 看看 unicode/utf8 包。 

3.扩展/修改上一个问题的程序，替换位置4开始的三个字符为“abc”。

4.编写一个 Go 程序可以逆转字符串，例如 “foobar” 被打印成 “raboof”。提示:不 幸的是你需要知道一些关于转换的内容，参阅 “转换” 第 59 页的内容。
平均值

### Q4.(1)平均值
>编写计算一个类型是 float64 的 slice 的平均值的代码。在稍候的练习 Q5 中 将会改写为函数。