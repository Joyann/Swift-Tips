# Swift-Tips

Confetti ...

---

## String

#### 访问字符串中某个下标对应的内容

1. 第一种方式 __转换成OC字符串__
   
   ``` swift
   var str = "Hello world!"
   let range = NSRange(location: 0, length: 1)
   let result = (str as NSString).substringWithRange(range)
   ```
   
2. 第二种方式 使用`startIndex`, `endIndex`以及`advanceBy()`
   
   ``` swift
   str[str.startIndex] // 获取第一个字符
   str[str.startIndex.successor()] // 获取第一个字符的下一个字符
   str[str.endIndex.predecessor()] // 获取最后一个字符,注意endIndex的位置并不是最后一个
   str[str.startIndex.advancedBy(0)] // 从第一个字符开始，偏移多少个下标
   str[str.endIndex.advancedBy(-1)] // 从最后一个字符开始，偏移多少个下标,注意向左偏移则为负数
   ```
   
3. 第三种方式 通过字符串的`characters.indices`属性
   
   ``` swift
   for i in str.characters.indices {
       print(str[i])
   }
   ```

#### 下标的使用的小例子

``` swift
let strRange = str.startIndex.advancedBy(5)..<str.endIndex
str.removeRange(strRange)
```

---

## 集合类型

+ 改变数组中某个下标区域的值，即使新数据和原数据数量不一样.但是不能用这种方式在尾部添加新数据.
  
  ``` swift
  var array = [0, 1, 2, 3]
  array[0...2] = [4, 5]
  ```
  
+ 遍历数组的时候如果也想获得索引，可以使用`enumerate()`，这个方法返回index和value组成的元组.
  
  ``` swift
  for (index, value) in array.enumerate() {
      print("index: \(index), value: \(value)")
  }
  ```
  
+ 和数组不同，Set没有简化形式.
  
  ``` swift
  var newSet = Set<Character>()
  newSet.insert("a")
  ```
  
  如果上下文提供了类型信息，我们可以通过一个数组字面量创建一个Set.
  
  ``` swift
  // 此时已知newSet为`Set<Character>`类型，则可以通过一个空数组来将其赋空，但是此时类型扔为`Set<Character>`而不是数组.
  newSet = []
  ```
  
  同样，可以将一个数组赋值给已知类型的Set，但是类型仍为Set. 即：__在确定一个变量是Set的情况下，可以用一个数组来直接给Set赋值，Set的类型不变.__
  
  ``` swift
  newSet = ["1", "2"]
  ```
  
  因此，初始化Set也可以用数组，但是一定要写明类型，否则就会自动推断为数组.这样做事合理的，因为此时可以确定为`Set<String>`类型，接下来用一个数组给其赋值，正是符合刚才我们所的情况.
  
  ``` swift
  var anotherSet: Set<String> = ["a", "b", "c"]
  ```
  
  不过也是因为Swift的自动推断，所以可以不必写明Set中具体的类型：
  
  ``` swift
  var anotherSet: Set = ["a", "b", "c"]
  ```
  
+ 因为Set无序，所以想要有序遍历Set:
  
  ``` swift
  for str in newSet.sort() { // 默认升序
      print(str)
  }
  ```
  
+ > 使用 `==` 判断两个集合中的值是否相同；
  > 
  > 使用 `isSubsetOf(_:)` 判断一个集合中的值是否也被包含在另一个集合中；
  > 
  > 使用 `isSupersetOf(_:)` 判断一个集合中包含另一个集合的所有值；
  > 
  > 使用 `isStrictSubsetOf(_:)` 和 `isStrictSupersetOf(_:)` 判断一个集合是否是另外一个集合的子集合/父集合并且两个集合不相等；
  > 
  > 使用 `isDisjointWith(_:)` 来判断两个集合是否不含有相同的值（即是否两个集合完全没关系）；
  
+ Dictionary的`updateValue(_:forKey:)`方法可以创建/更新字典中的值，与直接修改的方式相比，如果有旧值，使用update会返回修改前的`旧值的可选值`，如果没有旧址则返回nil.
  
+ 可以通过直接赋值nil的方式移除字典中的元素
  
  ``` swift
  dict["a"] = nil // 此时a被移除了
  ```
  
+ 通过字典的key/value构建数组：
  
  ``` swift
  let keyArray = [String](dict.keys)
  let valueArray = [String](dict.values)
  ```
  
+ 字典也是无序的，如果想要特定顺序遍历，需要对keys/values使用sort()方法.

---

## Switch

+ Swift中switch可以匹配多个值
  
  ``` swift
  let value = "a"
  switch value {
  case "a", "b":
      statements
  }
  ```
  
+ 区间匹配
  
  ``` swift
  switch count {
  case 0:
      ...
  case 1..<5:
      ...
  default:
      ...
  }
  ```
  
+ 元组匹配
  
  ``` swift
  let point = (1, 1)
  switch point {
  case (0, 0):
      ...
  case (_, 0): // _代表匹配所有可能的值
      ...
  default:
      ...
  }
  ```
  
+ 补充
  
  ``` swift
  if #available(iOS 9, *) { // iOS 9及以上才可用
    // statements
  } else{
    // statements
  }
  ```

---

## 函数

+ 可变参数在函数内部即为`数组`，并且__一个函数最多只能有一个可变参数__.
  
  ``` swift
  func testFunc(numbers: Double...) {
      print(numbers)
  }
  ```
  
+ 如果一个函数有一个或多个默认参数，而且还有一个可变参数，那么把可变参数放在最后.
  
+ 在使用In-Out参数的时候，只能传递变量给输入输出参数，不能传递常量或字面量，因为In-Out参数可能会被函数修改，然后被传出函数替换原来的值.在给In-Out传递变量的时候，不要忘记在变量前使用`&`. 用inout标记一个参数，这个参数不能被var或者let标记.

---

## 闭包

+ 闭包的分类
  
  1. 全局函数 
     
     是一个有名字的闭包，不会捕获任何值
     
  2. 嵌套函数
     
     是一个有名字的闭包，会捕获其封闭函数域内的值
     
  3. 闭包表达式
     
     匿名闭包，会捕获上下文中变量或常量的值
  
+ 函数和闭包都是`引用类型`
  
  无论将函数或闭包赋值给变量还是常量，实际上都是将变量或常量的值设置为函数或闭包的引用，而非函数/闭包本身.这意味着将闭包赋值给两个不同的变量/常量，实际上它们都指向同一闭包.
  
+ 逃逸闭包
  
  当闭包作为参数传到函数中，但是这个闭包是在函数返回后才会执行，而不是在函数中执行.比如：网络请求的函数会在异步操作之后立即返回，但是其`compleion hanlder`则是在异步操作完成之后才会被调用.这种情况下，闭包需要"逃逸"出函数，因为闭包是在函数返回后才被调用的.还有类似界面传值的时候，A界面展示出B界面，在初始化B的时候将后续操作保存在B界面的某个闭包变量中，以后在需要的时候才调用这个闭包，很明显这个闭包是在B的初始化函数结束后才会执行，那么即为`逃逸闭包`
  
+ 非逃逸闭包(Nonescaping Closures)
  
  与逃逸闭包相对，是指在函数结束后闭包不需要再调用，因为不需要"逃逸"出函数.例如: `sort(_:)`方法的实现，传入的闭包直接在函数中执行，而不是在函数结束后才执行，因此是`非逃逸闭包`.
  
+ 闭包作为函数参数，若确定为`非逃逸闭包`，将闭包标注为`@noescape`可以进行优化.
  
  ``` swift
  func testFunc(@noescape closure: ()->Void) {
      closure()
  }
  ```
  
+ 标记为@noescape可以使你在闭包中__隐式地引用self.__
  
  ``` swift
  class someClass {
      var x = 10
      func doSometing() {
          someFunctionWithEscapeClosure { self.x = 100 }
          someFunctionWithNoEscapeClosure { x = 200 }
      }
  }
  ```
  
+ 自动闭包
  
  将闭包参数标记为`@autoclosure`可以将传入的参数自动转换为一个闭包.
  
  ``` swift
  func testFunc(@autoclosure closure: () -> Int) {
      // ...
  }
  testFunc(array.removeLast())
  ```
  
  自动闭包可以省略闭包的大括号，并且达到延迟执行的目的.比如此例中，`array.removeLast()`会返回被移除的最后一个元素，作为自动闭包的参数，移除操作不是马上执行，而是在函数中调用`closure()`才会真正的执行移除操作，即延迟执行. 自动闭包在使用的时候注意类型要一致.
  
+ @autoclosure特性暗含了`@noescape`特性，即默认是函数执行完成闭包不再执行，而是直接在函数中执行. 如果是`自动闭包`，又需要`逃逸`(比如先将这个闭包存起来以后再用，而不是在函数中直接执行)，那么需要使用`@autoclosure(escaping)`来修饰.

---

## 枚举

+ 枚举的__原始值__可以是__字符串__，__字符__，__整型__，__浮点数__.
  
  枚举的__关联值__可以是__任意值__.
  
+ Swift的枚举成员在被创建时不会被赋予一个默认的整型值.
  
  ``` swift
  enum CompassPoint {
  	case North
      case South
      case East
      case West
  }
  ```
  
  `North`，`South`，`East`，`West`不会隐式地被赋值成`0`，`1`，`2`，`3`.这些枚举成员本身就是完备的值，这些值的类型是已经明确定义好的`CompassPoint`类型.
  
  枚举成员也可以写在同一行：
  
  ``` swift
  enum CompassPoint {
  	case North, South, East, West
  }
  ```
  
+ 每一个枚举都定义了一个全新的类型，所以__枚举名要大写__.给枚举起一个__单数名字__而不是复数名字，以便阅读起来更方便.
  
+ 每一个__原始值__在枚举声明中必须是唯一的.
  
+ 原始值和关联值
  
  > 原始值和关联值是不同的。
  > 
  > 原始值是定义枚举时被预先填充的值.对于一个特定的枚举成员，它的原始值始终不变.
  > 
  > 关联值是创建一个基于枚举成员的变量或常量时才会设置的值，枚举成员的关联值可以变化.
  
  也就是说，原始值是在声明枚举的时候就确定，并且始终不变.关联值是在声明枚举的时候声明类型，在真正定义枚举成员的时候才会设置值，并且可以变化.
  
+ 原始值的隐式赋值
  
  在使用`整数`或者`字符串`类型的原始值时，Swift会自动为你赋值.
  
  + 当使用`整数`作为原始值，隐式赋值的值依次+1.如果第一个枚举成员没有设置原始值，则默认为0.
  + 当使用`字符串`作为原始值，默认为`该枚举成员的名称`.
  
+ 递归枚举
  
  ``` swift
  indirect enum ArithmeticExpression { // 表示所有成员都是可递归的
  	case Number(Int)
      case Addition(ArithmeticExpression, ArithmeticExpression)
      case Multiplication(ArithmeticExpression, ArithmeticExpression)
  }
  
  func evaluate(expression: ArithmeticExpression) -> Int {
  	switch expression {
      	case .Number(let value):
          	return value
          case .Addition(let left, let right):
          	return evaluate(left) + evaluate(right)
  		case .Multiplication(let left, let right):
          	return evaluate(left) * evaluate(right)
  	}
  }
  
  // 计算 (5 + 4) * 2
  let five = ArithmeticExpression.Number(5)
  let four = ArithmeticExpression.Number(4)
  let sum  = ArithmeticExpression.Addition(five, four)
  let product = ArithmeticExpression.Multiplication(sum, ArithmeticExpression.Number(2))
  print(evaluate(product))
  ```

---

## 类和结构体

+ 结构体自带有一个`成员逐一构造器`.
  
  类实例是没有的.
  
+ 结构体不可以继承.
  
  类可以继承.
  
+ 结构体是`值类型`.
  
  类是`引用类型`.

---



## 属性

+ 计算属性可以用于__类__，__结构体__，__枚举__.
  
  存储属性只能用于__类__，__结构体__.
  
+ __只读计算属性__是指只有`getter`没有`setter`的__计算属性__. 并且可以省略`get`关键字和花括号.
  
  ``` 
  struct Cuboid {
  	var width = 0.0, height = 0.0, depth = 0.0
      var volume: Double {
      	return width * height * depth
  	}
  }
  ```
  
+ 如果在一个属性的`didSet`观察器里为这个属性赋值，这个新的值会替换该观察器之前设置的值.也就是说，在didSet观察器里面是可以设置这个属性自己的值的，而在计算属性的`set`中是无法设置这个属性自己的值的，通常是在`set`中设置其它属性的值.
  
+ __全局变量/局部变量__都可以使用__计算属性和属性观察器的模式__.
  
  注意，__全局变量/常量__都是延迟计算的，跟延迟存储属性相似，但是不必标记`lazy`.而局部变量/常量不会延迟计算.
  
+ 类型属性
  
  + 类型属性就是`类属性`或者类中的`静态变量`，是指所有实例共享的数据.
    
    在C或Objective-C中，与某个类型关联的静态常量/变量是作为`全局静态变量/常量`定义的，但是在Swift中，类型属性是作为类型定义的一部分写在类中，因此它的作用范围也就在类支持的范围内.
    
  + 使用`static`来定义类型属性. 
    
    在为类定义`计算型类型属性`时，可以使用`class`来支持子类对父类的实现进行重写.
    
    也就是说，在`Struct`, `Enum`和`Class`中，如果想要定义`类属性`，那么使用`static`（无论是计算型类属性还是存储型类属性）；如果想要在`Class`中，想要让子类重写父类的`计算型类属性`，那么使用`class`.
  
  
  + __存储型类型属性__可以是变量或常量，但是必须要有默认值(这一点和实例的存储属性不同)，因为构造器无法给类型属性赋值.并且，存储性类型属性是__延迟初始化__的，不需要`lazy`修饰符来修饰.
  
  
  + __计算性类型属性__和实例的计算属性一样，只能定义成变量.
  
  ​