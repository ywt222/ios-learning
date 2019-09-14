## Swift
`LoginRequest.swift`
### protocol
#### 什么是Protocol？
Protocol是Swift中的一种自定义类型，可以使用protocol定义某种约定，而不是某一种类型，一般用于表示某种类型的共性。
#### Protocol 用法
定义一个protocol:

```
protocol PersonProtocol {
    func getName()
    func getSex()
}
```
某个class、struct或者enum要遵守这种约定的话，需要实现约定的方法。

```
struct Person: PersonProtocol {
    func getName() {
         print("Bob")
    }
    func getSex() {
         print("boy")
    }
}
```

> 方法的参数不可以有默认值；  
> 属性不可以有默认值；  
> 必须明确指定该属性支持的操作：只读（get）或者是可读写（get set）；  
> 即使属性只有get，没有set，也不可以将属性设置为let，必须设置为var

当protocol中定义了一个只读属性，其实我们也可以在遵守该约定的类型中完成该属性的可读可写。

```
protocol PersonProtocol {
    var height: Int { get set }
    var weight: Int { get }
    func getName()
    func getSex()
    func getAge(age: Int)
}

struct Person: PersonProtocol {
    var height = 178
    var weight = 120
    func getName() {
         print("MelodyZhy")
    }
    func getSex() {
         print("boy")
    }
    func getAge(age: Int) {
        print("age = \(age)")
    }
}

var person = Person()
person.height   // 178
person.height = 180
person.height  // 180

person.weight // 120
// 可以更改（但在以前版本的Swift中是不能直接这样更改的
// 需要借助一个内部的stored property
// 然后把这个属性设计成一个computed property实现 get 和 set 方法）
person.weight = 130 // 130
// 当我们把person从Person转换成PersonProtocol时 他就是只读的了
// ❌Cannot assign to property: 'weight' is a get-only property
(person as PersonProtocol).weight = 120
```

protocol可以继承，当然struct、class、enum都可以同时遵守多个约定。

```
protocol PersonProtocol {
    var height: Int { get set }
    var weight: Int { get }
    func getName()
    func getSex()
    func getAge(age: Int)
}
protocol Engineer: PersonProtocol {
    var good: Bool { get }
}
protocol Animal {
}
struct Person: Engineer, Animal {
    // 省略了该实现约定的方法和属性
}
```

#### protocol extension
`protocol extension` 最关键的一点就是能在 `protocol extension` 方法中获取 `protocol` 的属性，因为Swift编译器知道任何一个遵守 `protocol` 的自定义类型，一定会定义这个 `protocol` 约定的各种属性，既然这样我们就可以在 `protocol extension` 中添加默认的实现了。

```
// 定义一个人属性的 protocol
protocol PersonProperty {
    var height: Int { get } // cm
    var weight: Double { get } // kg
    // 判断体重是否合格的函数
    func isStandard() -> Bool
}

extension PersonProperty {
    // 给 protocol 添加默认的实现
    func isStandard() -> Bool {
        return self.weight == Double((height - 100)) * 0.9
    }
    // 给 protocol 添加默认属性
    var isPerfectHeight: Bool {
        return self.height == 178
    }
}

struct Person: PersonProperty {
    var height: Int
    var weight: Double
    // 如果自定义类型里面创建了遵守的 protocol 中的方法
    // 那么他将覆盖 protocol 中的方法
//    func isStandard() -> Bool {
//        return true
//    }
}
// 创建遵守 PersonProperty 的自定义类型
let p = Person(height: 178, weight: 61.5)
// 那么 p 这个自定义类型 天生就有判断这个人身高体重是否合格的方法
p.isStandard() // false
// 同样天生具有判断是否是 Perfect Height 的属性
p.isPerfectHeight // true
```

--

### @escaping @noescaping 逃逸闭包与非逃逸闭包
闭包只有在函数中做参数时才会区分逃逸闭包和非逃逸闭包。  
Swift 3.0之后，传递闭包到函数中的时候，系统会默认为非逃逸闭包类型（NonescapingClosures) `@noescaping`，逃逸闭包在闭包前要添加`@escaping`关键字。  
##### 从生命周期看两者区别：
* 非逃逸闭包的生命周期与函数相同：  
1，把闭包作为参数传给函数；  
2，函数中调用闭包；  
3，退出函数。结束

* 逃逸闭包的生命周期：  
1，闭包作为参数传递给函数；  
2，退出函数；  
3，闭包被调用，闭包生命周期结束

即逃逸闭包的生命周期长于函数，函数退出的时候，逃逸闭包的引用仍被其他对象持有，不会在函数结束时释放。

--

### “??”
`??` 被命名为空合运算符

```
let username = loginName ?? "Guest"
```
`loginName` 表示为可选性，如果 `loginName` 为空，则使用默认名称 `Guest`

--

###  “...” 和 “..”
`0...5`是一个闭区间`[0,5]`  
`0..<5`是一个前闭后开区间`[0,5)`