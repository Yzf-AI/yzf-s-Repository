# 匿名函数 包 结构体 接口

## 匿名函数

* 匿名函数直接执行
* 匿名函数赋值给变量
* 闭包函数引入外层变量
* 引用方式修改外部变量

***例程如下***

```golang
package main
import"fmt"
func main(){
        a := 1
        b := 2
           func() {
            a = 3  //不需要再次定义，直接使用外层变量
            b = 4
            fmt.Printf("内部：a = %d,b = %d\n", a, b)
}()  //()代表直接调用
            fmt.Printf("外部：a = %d,b = %d\n", a, b)
｝

```

```go
package main
import "fmt"
func ExFunc(n int) func() {
    sum:=n
    a:=func () {             //把匿名函数作为值赋给变量a (Go 不允许函数嵌套。
                                   //然而你可以利用匿名函数实现函数嵌套)
    fmt.Println(sum+1)    //调用本函数外的变量
    }                               //这里没有()匿名函数不会马上执行
    return a
}
func main() {
f1:=ExFunc(10)
f1()
f2:=ExFunc(20)
f2()
f1()
f2()
}
```

```go
package main
import "fmt"
func ExFunc(n int) func() {
    return func() {
    n++ //这里对外部变量加1
    fmt.Println(n)
    }
}
 func main() {
    fx := ExFunc(10)
    fx()         //这里输出11
    fy:= ExFunc(20)
    fy()  //这里输出21
    fx() //12
    fy()//22
}


```

```go
package main
import  "fmt"
func adder() func(int) int {
  sum := 0
  innerfunc := func(x int) int {
    sum += x
    return sum
  }
  return innerfunc
}
func main() {
  pos, neg := adder(), adder()
  for i := 0; i < 10; i++ {
    fmt.Println(pos(i), neg(-2*i))
  }
}

```

## 闭包

>闭包可以作为函数对象或者匿名函数。
***
>支持闭包的多数语言都将函数作为第一级对象，就是说这些函数可以存储到变量中作为参数传递给其他函数
***
>能够被函数动态创建和返回。
***
***例程***

```go
package main
import "fmt"
func test() func() int {
            x := 3
              return func() int {  //返回一个闭包函数
                x++
               return x
}
}
func main() {
            x := test()
             fmt.Println("x =", x())
}

```

## ***结构体***

>要求顺序为每个成员变量指定一个值，这种格式必须记住每个成员变量的顺序
***
>通过指定部分或者全部成员变量的名称和值来初始化结构体变量，如果成员被忽略的话将默认用零值。

1. 初始化结构体
不一样的变量是不一样的结构体

```go
package main
import "fmt"
func main()
type Student struct {  
ID int
Name  string
Address string
Age    int
Class  string
Teacher string
}

```

初始化 不能混用
顺序初始化形式
`type Point struct{ X, Y int }p := Point{1, 2}`
指定成员初始化形式
`lm := Student {ID: 2018002022}`
记得&在变量结构体前面
如果结构体每一个成员都相等那么结构体相等
可以使用嵌套我允许Go定义不带名称的结构体成员，只需要指定类型即可；这种结构体成员叫做匿名成员

```go
type Point struct { X, Y int}
type Circle struct {   Point  Radious int}
type Cylinder struct {  Circle   Height int}
```

* c1 = Cylinder{Circle{Point{8, 8}, 5}, 20}
* c2 = Cylinder{  Circle: Circle{      Point:   Point{X: 8, Y: 8},
 //注意，尾部的逗号是必需的
Radious: 5,  
  },
Height: 20,
}

1. 方法与封装

当一个匿名类型被嵌入到一个结构体中时，这个类型的可见的方法也会被一同纳入到此结构体中，而且被嵌入的结构体能够覆写该匿名类型的同名方法 `func (l *Log) Input(s string) {  *l = Log(s)}func (l Log) Output() {  fmt.Println(l)`

ps 猩猩大佬 据说只能用一种表  QAQ QAQ

- [x] 通过方法的接收者来调用其方法

- [x] 一个接收者类型可以有多个方法，但属于它的方法不能重名

- [x] 方法仍然是函数，所以方法是不能重载的
  
- [x] 拥有相同名字的一个方法可以有两个或多个不同的接收者类型

- [x] 方法的接收者的类型本身不可以是指针，也不可以是接口类型

创建方法的方法
Go语言中为一个特定对象创建一个成员方法的语法如下
`func (r receiver_type) name(parameter_list) (return_value_list) {}`
类似外部函数 一部分是结构体名 另一部分是对结构体的元素 
有无指针的区别
`func (p *Person) SetForename(newForename string)`
`func (p Person) SetLastName(newLastName string)`

***关于封装***
之前定义过的Person结构体及其方法

```go
type Person struct {  forename string lastName string}
func (p Person) GetName() string
{   return p.forename + " " + p.lastName}
```

其中Person和GetName()方法是可导出的，而其成员forename和lastName是不可导出的。
因此我们可以在其他的包中调用:
person.GetName()
但却无法使用:
person.lastName
因为lastName是包私有的，其他包无法访问。
涉及到能否改变能否访问的问题

**嵌入式结构体**
当一个匿名类型被嵌入到结构体中时，这个类型的可见的方法也会被一同纳入到此结构体中，而且被嵌入的结构体能够覆写该匿名类型的同名方法

学习链接  [go](https://pc.yiyouliao.com/msn/article.html?recId=ccb24ea193bf4d098bc6d26fb2a08566_s&infoId=II00F87N7KDW29X)

## ***接口***

这是图床
![06mCzd.png](https://s1.ax1x.com/2020/10/10/06mCzd.png)
