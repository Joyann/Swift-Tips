# Swift-Tips

Confetti ...

## 其它注意点

+ 在Swift中使用字符串的方式创建一个类，必须要加上__命名空间__.
  
  ``` swift
  // 获取命名空间
  let nameSpace = NSBundale.mainBundle().infoDictionary!["CFBundleExecutable"]
  guard let ns = nameSpace as? String else {
    return
  }
  let cls: AnyClass? = NSClassFromString(ns + "." + controllerName) // AnyClass的本质是AnyObject.Type
  guard let clsType = cls as? UIViewController.Type else {
  	return
  }
  
  let vc = clsType.init() // 根据控制器类型构建控制器对象.
  ...
  ```
  
+ 想要快速创建一个对象，可以提供类方法来创建. 个人认为更好的方式是可以为这个类添加一个Extension来提供创建方法. 在Swift中可以通过在Extension中提供类的`便利构造方法`来快速创建对象(不要忘记调用指定构造器和`convenience`). 
  
  ``` swift
  extension UIButton {
    concenience init(imageNamed: String, backgroundImageNamed: String) {
      self.init()
    
    	setBackgroundImage...
    	setImage...
    	sizeToFit()
    }
  }
  ```

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

---

## 方法

+ 结构体和枚举是__值类型__，一般情况下，值类型的属性不能在它的实例方法中被修改. 如果想要修改，就需要将这个方法__变异(mutating)__.
+ 类方法用`static`标记，如果在Class中想要子类重写父类的实现方法，那么需要使用`class`标记，这一点和`类属性`是一样的.
+ 类方法中可以直接调用其他类方法和类属性而不用加上类名称的前缀.结构体和枚举的类型方法也能够直接访问静态属性/类型方法而不用加上类型名称前缀.

---

## 下标脚本

+ 下标脚本可以定义在__类__，__结构体__，和__枚举__这些目标中.使用`subscript`关键字，显式声明入参(一个或多个)和返回类型.
  
+ 下标脚本相当于__实例方法__和__计算型属性__的混合体.它的声明形式类似于实例方法，不过不是以func声明而是以`subscript`来声明.而下标脚本可以设定为__读写__或__只读__，这和计算型属性类似.
  
  ``` swift
  subscript(index: Int) -> Int {
  	get {
    		// 返回与参数匹配的Int类型的值
  	}
  
      set(newValue) {
      	// 执行赋值操作
  	}
  }
  
  // 和只读计算型属性一样，也有简写
  struct TimesTable {
  	let multiplier: Int
      subscript(index: Int) -> Int {
    		return multiplier * index
  	}
  }
  ```
  
+ 下标脚本允许__任意数量的入参索引__，并且__每个入参类型没有限制__.
  
  下标脚本的__返回值也可以是任意类型__.
  
  下标脚本可以使用__变量参数__和__可变参数__.
  
  下标脚本__不允许__使用`in-out参数`或者`默认参数`.
  
+ > 一个类或结构体可以根据自身需要，提供多个下标脚本实现，在定义下标脚本时通过__入参类型__来进行区分，使用下标脚本时会自动匹配合适的下标脚本实现运行，这就是下标脚本的__重载__.
  
+ 定义多个下标脚本入参.
  
  ``` swift
  struct SomeStruct {
  	subscript(row: Int, column: Int) -> Double {
        get {
            assert(...) // 判断是否越界
            return ...
        }
        set(newValue) {
            assert(...) // 判断是否越界
            ...
        }
  	}
  }
  
  let someStruct = SomeStruct()
  someStruct[1][2] = ...
  ```

---

## 继承

+ 类可以调用和访问以及重写超类的__方法__，__属性__，__下标脚本__.
  
+ 可以为类中继承来的属性添加__属性观察器__，无论它原本是__存储型属性__还是__计算型属性__.
  
+ 访问超类的方法: `super.someMethod()`
  
  访问超类的属性: `super.someProperty`
  
  访问超类的下标脚本: `super[someIndex]`
  
+ 重写属性
  
  + 重写属性的Getters和Setters
    
    你可以提供getter(setter)来重写任意继承来的属性，无论是计算型还是存储型.子类并不知道继承来的属性是计算型还是存储型，它只知道继承来的属性会有一个名字和类型.所以在重写一个属性时，必须将它的名字和类型都写出来，这样编译器才去检查你重写的属性和超类中同名同类型的属性是匹配的.
    
    可以将一个继承来的__只读属性__重写为一个__读写属性__，只需要在重写版本里提供getter和setter即可.
    
    但是，__不可以__将一个__读写属性__重写为一个__只读属性__.
    
    __如果重写属性中提供了setter，那么也一定要提供getter.__如果不想在重写版本中的getter里修改继承来的属性，可以直接通过`super.someProperty`来返回继承来的值.
    
  + 重写属性观察器
    
    可以在属性重写中为一个继承来的属性添加属性观察器，无论这个继承来的属性原本是如何实现的.
    
    注意：你__不可以__为继承来的__常量存储型属性或只读计算型属性__添加属性观察器.这些属性的值是不可变的，为它们提供`willSet`或`didSet`是不恰当的.
    
    另外，你__不可以同时提供重写的setter和重写的属性观察器__.这是多余的，如果你想观察属性值的变化，并且你已经为那个属性提供了定制的setter，你在setter中就可以观察到任何值变化了 .
  
+ 防止重写
  
  可以通过把__方法__，__属性__，__下标脚本__标记为`final`来防止它们被重写.
  
  ``` swift
  final var 
  final func
  final class func
  final subscript
  ```
  
  __在类拓展中的方法，属性或下标脚本__也可以在扩展的定义里标记为`final`.
  
  另外，可以用`final`来修饰`class`，这样这个类是不可被继承的.

---

## 构造过程

+ 在为存储型属性__设置默认值__或通过__构造器为其赋值__的时候，是直接赋值的，并不会触发`属性观察者`.
  
+ 如果一个属性总是使用形同的初始值，那么设置默认值比每次在构造器中赋值要好.使用默认值能让构造器更简洁且能自动推导类型；同时，它也能让你充分利用默认构造器、构造器继承等特性.
  
+ 构造器与函数和方法的名字不同，构造器的名字是`init`开头不好辨别，因此在调用构造器的时候主要通过构造器中的参数名和类型里确定需要调用的构造器.__Swift会为每个构造器的参数自动生成一个跟内部名字相同的外部名__.
  
  ``` swift
  struct Color {
    let red, green, blue: Double
    init(red: Double, green: Double, blue: Double) {
    	self.red = red
      self.green = green
      self.blue = blue
    }
  }
  
  let magenta = Color(red: 1.0, green: 0.0, blue: 1.0)
  ```
  
  也就是说，普通的方法会自动为__除第一个参数外的其它参数自动添加一个同名外部变量__；而构造器则是为__所有参数自动添加一个同名外部变量__.
  
+ 使用`_`忽略外部参数名.
  
+ 对于类的实例来说，它的常量属性只能在定义它的类的构造过程中修改，而不能在子类中修改.
  
  ``` swift
  class Person {
  	let name: String
      init() {
      	self.name = "Joyann" // 这里修改是可以的.
  	}
  }
  
  class Student: Person {
  	override init() {
      	self.name = "Rudy" //这里修改会报错
  	}
  }
  ```
  
+ 如果结构体/类所有属性都有默认值，同时没有自定义构造器，那么Swift会给这些结构体/类创建一个默认构造器.
  
  ``` swift
  class ShoppingListItem {
  	var name: String?
      var quantity = 1
      var purchased = false
  }
  
  var item = ShoppingListItem()
  ```
  
  注意，这里有两个条件：所有属性都有默认值，没有自定义构造器.
  
  另外，结构体如果满足上面两个条件，能获得一个`逐一成员构造器`.
  
  ``` swift
  struct size {
    var width = 0.0
    var height = 0.0
  }
  let twoByTwo = Size(width: 2.0, height: 2.0)
  ```
  
+ 构造器代理
  
  构造器可以通过调用其它构造器来完成实例的部分构造过程，这一过程称为构造器代理. 
  
  构造器代理的实现规则和形式在值类型和类类型中有所不同.
  
  值类型不支持继承，所以只能代理给本身提供的其它构造器.
  
  类类型支持继承，这意味着类有责任保证其所有继承的存储型属性在构造时也能正确的初始化.
  
+ __如果你为某个值类型定义了一个定制的构造器，你将无法访问到默认构造器（如果是结构体，则无法访问逐一成员构造器）. 这样保证当你提供了更完备的构造器之后，别人还是错误的使用自动生成的构造器.__
  
  > 如果想要使用默认构造器+逐一成员构造器+定制构造器，有两种方式.第一种是将自己定制的构造器写到extension中，而不是跟值类型定义混在一起，这样即使有定制构造器，也会保留之前默认和逐一成员构造器. 第二种是将定制的构造器写在原始的值类型中，自己手动来实现默认构造器和逐一成员构造器. 注意这里说的是__值类型__，如果是类，那么extension中是不允许定义__指定构造器和析构器__的，只能定义__便利构造器__.
  
+ 类里面所有__存储型属性（包括所有继承自父类的属性）__都必须在构造过程中设置初始值.
  
+ 指定构造器：初始化类中提供的所有属性，并且根据父类链往上调用父类的构造器来实现父类的初始化.__每个类必须拥有至少一个指定构造器，许多类通过继承父类中的指定构造器而满足了这个条件__.
  
  便利构造器：`convenicence`修饰. 可以定义便利构造器来调用同一个类中的指定构造器，并为其参数提供默认值. 只有在必要的时候才提供便利构造器(比如说某种情况下通过使用便利构造器来快捷调用某个指定构造器，能够节省更多开发时间并让类的构造过程更清晰明了.)
  
+ 类的构造器代理规则
  
  + __指定构造器必须总是向上代理__.
  + __便利构造器必须总是横向代理__.
  + 指定构造器调用其直接父类指定构造器，便利构造器必须调用同一类中定义的其它构造器，但是必须最终以调用一个指定构造器结束.
  
+ 两段式构造过程
  
  第一个阶段，每个__存储型属性__通过引入它们的类的构造器来设置初始值. 当每一个存储型属性值被确定后，第二阶段开始.
  
  第二个阶段，每一个类有一次机会再新实例准备使用之前进一步定制它们的存储型属性.
  
+ Swift编译器将执行四种有效的安全检查以确保两段式构造过程顺利完成：
  
  1. 指定构造器必须保证它所在类的所有属性都必须初始化完成，之后才能将其它构造任务向上代理给父类中的构造器. 一个对象的内存只有在其所有存储型属性确定后才能完全初始化.
     
  2. 指定构造器必须向上代理调用父类构造器，然后再为继承的属性设置新值，否则会被父类中的构造器覆盖.
     
  3. 便利构造器必须先代理调用同一类中的其它构造器，然后再为任意属性赋新值，否则会被同一类中其它指定构造器所覆盖.
     
  4. 构造器在第一阶段完成前，__不能调用任何实例方法，不能读取任何实例属性的值，`self`的值不能被引用__.
     
     类实例第一阶段结束以前并不是完全有效，仅能访问属性和调用方法，一旦完成第一阶段，该实例才会声明为有效实例.
  
+ 阶段一
  
  + 某个指定构造器或便利构造器被调用；
  + 完成新实例内存的分配，但此时内存还没有被初始化；
  + 指定构造器确保其所在类引入的所有存储型属性都已赋初值. 存储型属性所属的内存完成初始化；
  + 指定构造器将调用父类的构造器，完成父类属性的初始化；
  + 这个调用父类构造器的过程沿着构造器链一直往上执行，直到到达构造器链的最顶部；
  + 当到达了构造器链最顶部，且已确保所有实例包含的存储型属性都已赋值，这个实例的内存被认为已经完全初始化. 此时阶段一完成.
  
  阶段二
  
  + 从顶部构造器链一直往下，每个构造器链中类的指定构造器都有机会进一步定制实例. 构造器此时可以访问`self`、修改属性并调用实例方法等等.
  + 最终，任意构造器链张的便利构造器可以有机会定制实例和使用`self`.
  
+ 和OC的子类不同，__Swift中的子类不会默认继承父类的构造器__.
  
  这种机制可以防止一个父类的简单构造器被一个更专业的子类继承，并被错误的用来创建子类的实例.
  
  > 父类的构造器仅在确定和安全的情况下被继承.
  
+ 假如你希望自定义的子类实现一个或多个跟父类相同的构造器，你可以在你定制的子类中提供和重写与父类相同的构造器.
  
  + 当你写一个父类中带有指定构造器的子类构造器，你需要重写这个指定的构造器，加上`override`.（子类有可能调用指定构造器，是看得到父类的指定构造器的，所以需要加上`override`）
  + 当你在子类中提供一个匹配父类便利构造器的实现时，不需要加上`override`.（因为子类不能直接调用父类的便利构造器，代表着子类看不到父类的便利构造器，所以无需重写）
  
  也就是说，子类默认不会继承父类的构造器，如果使用到与父类构造器同名的构造器，并且这个构造器是__指定构造器__，那么需要使用`override`修饰；如果这个构造器是__便利构造器__，则不需要`override`修饰.
  
+ 子类可以在初始化时修改继承变量属性，但是不能修改继承过来的常量属性.
  
+ 自动构造器的继承
  
  刚才提到，子类不会默认继承父类的构造器，但是如果特定条件可以满足，父类构造器是可以被自动继承的. (也就是说，继承父类构造器可以是像刚才提到的一样主动重写父类构造器，还有一种是让其满足特定条件自动继承.)
  
  > 规则1
  > 
  > 如果子类没有定义任何指定构造器，它将自动继承所有父类的__指定构造器__.
  > 
  > 规则2
  > 
  > 如果子类提供了所有父类指定构造器的实现(不管是通过规则1继承来的，还是通过自定义实现的)，它将自动继承所有父类的__便利构造器.__
  
  子类可以通过部分满足规则2的方式，使用子类便利构造器来实现父类的指定构造器.
  
  ``` swift
  class Food {
    var name: String
    init(name: String) {
      self.name = name
    }
    convenience init() {
    	self.init(name: "[Unnamed]")
    }
  }
  
  class Recipelngredient: Food {
    var quantity: Int
    init(name: String, quantity: Int) {
      self.quantity = quantity
      super.init(name: name)
    }
    override convenience init(name: String) {
      self.init(name: name, quantity: 1)
    }
  }
  
  class ShoppingListItem: RecipeIngredient {
    var purchased = false
    var description: String {
      var output = "\(quantity)*\(name)"
      output += purchased ? "YES" : "NO"
      return output
    }
  }
  ```
  
  > Food有一个指定构造器( init(name:) )和一个便利构造器( init() ).
  > 
  > ​
  > 
  > RecipeIngredient继承自Food，有一个指定构造器( init(name:quantity:) )和一个便利构造器( init(name:) )，因为此时的便利构造器与父类的指定构造器匹配，所以需要加上`override`关键字. 又因为Recipelngredient实现了父类的所有指定构造器(虽然此时父类的指定构造器已经变为本类的便利构造器，但仍然是实现了)，所以会自动继承父类的便利构造器( init() )，所以此时Recipelngredient有三种构造方式.
  > 
  > ​
  > 
  > ShoppingListItem继承自Recipelngredient，因为ShoppingListItem所有属性都有默认值，并且自己没有定义任何构造器，所以它将自动继承所有父类中的指定构造器和便利构造器，所以此时ShoppingListItem也有三种构造方式.
  
+ 可失败构造器
  
  + 失败的情况下使用`return nil`，成功的情况不要使用`return`.
    
  + 枚举类型的可失败构造器
    
    ``` swift
    init?(symbol: Character) {
      switch symbol {
        case "K":
          self = .Kelvin
        case "C":
          self = .Celsius
        case "F":
          self = .Fahrenheit
        default:
          return nil
      }
    }
    ```
    
    带原始值的枚举类型会自带一个可失败构造器`init?(rawValue:)`.
    
    ``` swift
    enum TemperatureUnit: Character {
      case Kelvin = "K", Celsius = "C", Fahrenheit = "F"
    }
    
    let fahrenheitUnit = TemperatureUnit(rawValue: "F")
    if fahrenheitUnit != nil {
    	...
    }
    ```
    
  + 值类型和类类型的可失败构造器是不一样的.
    
    __值类型(结构体和枚举类型)的可失败构造器，对何时何地触发构造失败这个行为没有任何限制__.
    
    ``` swift
    struct Product {
      let name: String
      init?(name: String) {
        if name.isEmpty { return nil }
        self.name = name
      }
    }
    ```
    
    __类的可失败构造器只能在所有的类属性被初始化后和所有类之间的构造器之间的代理调用完后触发失败行为__.
    
    ``` swift
    class Product {
      let name: String!
      init?(name: String) {
        self.name = name
        if name.isEmpty { return nil }
      }
    }
    ```
    
  + 可失败构造器允许在同一类（结构体/枚举）中横向代理其它的可失败构造器. 子类的可失败构造器也能向上代理基类的可失败构造器. 可失败构造器也可以代理调用其它的非可失败构造器. 但是一个非可失败的构造器永远也不能代理调用一个可失败构造器.
    
  + 子类的非可失败构造器可以重写一个基类的可失败构造器. 但是此时子类的构造器将不能再向上代理父类的可失败构造器. 但是，反过来是不行的，也就是说，是不可以用子类中的可失败构造器重写父类的非可失败构造器.
    
  + 也就是说，可失败构造器可以调用其它非可失败构造器；非可失败构造器可以重写可失败构造器. 反过来都是不行的.
  
+ 必要构造器
  
  在类的构造器前加`required`修饰符表明所有该的子类都必须实现该构造器.
  
  在子类重写父类的必要构造器时，必须也加`required`，这是为了保证继承链上子类的构造器也是必要构造器. 不需要加`override`修饰符.
  
  > 如果子类继承的构造器能满足必要构造器的需求，则你无需显示的在子类中提供必要构造器的实现.
  
+ 通过闭包和函数来设置属性的默认值
  
  ``` swift
  class SomeClass {
  	let someProperty: SomeType = {
      	...
      	return someValue
  	}()
  }
  ```
  
  注意，在使用闭包来初始化属性的值，闭包执行时，实例的其它部分都还没有初始化. 这意味着__你不能在这个闭包里访问其它的属性，就算这个属性有默认值也不行. 同样，你也不能使用隐式的`self`属性，或者调用其他的实例方法__.



## 析构函数

+ 不要主动调用析构函数.
+ 子类继承了父类的析构器，并且在子类析构器实现的最后，父类的析构器会被__自动调用__. 即使子类没有提供自己的析构器，父类的析构器也会被调用.

---

## 可空链式调用(Optional Chaining)

+ 可空链式调用的返回结果与原本的返回结果相同类型，__但是被包装成了一个可空类型__ . 当可空链式调用成功时，一个本应该返回`Int`的将会返回`Int?`类型.
  
  ``` swift
  if let roomCount = john.residence?.numberOfRooms {
    ... 
  } else {
    ...
  }
  ```
  
  注意，`numberOfRooms`是一个`Int`类型，但是通过可空链式调用就意味着它将返回一个`Int?`而不再是`Int`类型.
  
+ 通过可空链式调用方法
  
  可以通过可空链式调用方法，并判断是否调用成功，__即使这个方法没有返回值__.
  
  ``` swift
  func printNumberOfRooms() {
    ...
  }
  ```
  
  这个方法没有返回值，隐式返回`Void`类型，即返回`()`或者空的元组. 如果在可空值上通过可空链式调用来调用这个方法，返回类型变为`Void?`而不是`Void`.
  
  ``` swift
  if john.residence?.printNumberOfRooms() != nil {
  	// 方法调用成功
  } else {
  	// 方法调用失败
  }
  ```
  
+ 通过可空链式访问下标
  
  当通过可空链式调用访问可空值的下标的时候，应该将问号放在下标方括号的前面而不是后面.
  
  ``` swift
  if let firstRoomName = john.residence?[0].name {
  	...
  } else {
  	...
  }
  ```
  
+ 访问可空类型的下标
  
  ``` swift
  var testScores = ["Dave": [86, 82, 84], "Bev": [79, 94, 81]]
  testScores["Dave"]?[0] = 91
  testScores["Bev"]?[0]++
  ```
  
+ 多层链接
  
  + 如果你访问的值不是可空的，通过可空链式调用将会放回可空值. (前面的`Int`变成`Int?`的例子)
    
  + 如果你访问的值已经是可空的，通过可空链式调用不会变的"更"可空.
    
  + `访问的值`指的是可空链的最后一个值.
    
  + 举例：
    
    ``` swift
    if let johnsStreet = john.residence?.address?.street {
    	...
    } else {
    	...
    }
    ```
    
    这里的street本身就是一个可选类型`String?`，通过两层可空链式调用，依然是`String?`而不会"更"可空.
    
  + 如果调用一个函数，其返回值本身就是可选类型，同样地，通过可空链式调用方法最终返回值还是`String?`.
    
    ``` swift
    if let buildingIdentifier = john.residence?.address?.buildingIdentifier() {
    	...
    } else {
    	...
    }
    ```
    
    如果要进一步对方法的__返回值__进行可空链式调用：
    
    ``` swift
    if let beginsWithThe = john.residence?.address?.buildingIdentifier()?.hasPrefix("the") {
    	if beginsWithThe {
        	...
    	} else {
        	...
    	}
    }
    ```

---

## 错误处理

+ Swift中，错误用遵循`ErrorType`协议类型的值来表示.
  
  Swift的枚举类型尤为适合塑造一组相关的错误情形，枚举的关联值还可以提供额外信息.
  
  ``` swift
  enum VendingMachineError: ErrorType {
    case InvalidSelection
    case InsuffcientFunds(coinsNeeded: Int)
    case OutOfStock
  }
  ```
  
+ 使用`throw`抛出错误.
  
  ``` swift
  func vend(itemNamed name: String) throws {
    guard var item = inventory[name] else {
    	throw VendingMachineError.InvalidSelection
    }
  }
  ```
  
  可能抛出错误的函数必须使用`throws`标记. `throws`在`-> 返回值`的前面.
  
+ 在处理错误的时候，有四种方式：
  
  1. 将错误继续传递
     
     使用`try`来将可能发生的错误继续传递到调用这个函数的代码处，注意这个函数使用了`try`所以可能抛出错误，需要用`throws`修饰.
     
     ``` swift
     func buyFavoriteSnack(person: String, vendingMachine: VendingMachine) throws {
       let snackName = favoriteSnacks[person] ?? "Candy Bar"
       try vendingMachine.vend(itemNamed: snackName)
     }
     ```
     
     任何由`vend(itemNamed:)`方法抛出的错误会一直被传递到`buyFavoriteSnack(_:vendingMachine)`函数被调用的地方.
     
  2. 使用`do-catch`立即处理错误
     
     ``` swift
     do {
       try buyFavoriteSnack("Alice", vendingMachine: vendingMachine)
       ...
     } catch VendingMachineError.InvalidSelection {
     
     } catch VendingMachineError.OutOfStock {
     
     } catch VendingMachineError.InsufficientFunds(let coinsNeeded) {
     }
     ```
     
     尝试执行语句，一旦出错立即处理(进入catch中). 如果没有错误，则继续执行do语句余下的语句.
     
  3. 将错误作为可选类型立即处理
     
     ``` swift
     func someThrowingFunction() throws -> Int {
     	...
     }
     
     let x = try? someThrowingFunction()
     
     let y: Int?
     do {
       y = try someThrowingFunction()
     } catch {
       y = nil
     }
     
     func fetchData() -> Data? {
       if let data = try? fetchDataFromDisk() {
         return data
       }
       if let data = try? fetchDataFromServer() {
         return data
       }
       return nil
     }
     ```
     
  4. 断言此错误根本不会发生，立即处理错误
     
     ``` swift
     let photo = try! loadImage("...")
     ```
  
+ 指定清理操作
  
  使用`defer`语句在代码执行到要离开当前的代码段之前去执行一套语句，该语句能让你做一些清理工作.（无论是由于抛出错误离开还是`return`,`break`离开）
  
  ``` swift
  func processFile(filename: String) throws {
  	if esists(filename) {
      	let file = open(filename)
          defer {
          	close(file)
  		}
          while let line = try file.readline() {
          	// 处理文件
  		}
          // 在这里会执行defer语句中的`close(file)`
  	}
  }
  ```
  
  `defer`语句只在当前作用域中起作用，在即将离开作用域的时候会执行`defer`中的语句. 注意，即使没有涉及到错误处理代码，依然可以使用`defer`语句.
  
  如果有多条`defer`语句，是从下向上执行的. (第一条`defer`语句中的代码是在第二条`defer`语句代码执行后再执行)

---

## 检查类型 & 向下转型

+ `is`来检查类型，`as`来向下转型(`as?`和`as!`).
  
+ 在`switch`中使用`is`和`as`并且赋值：
  
  ``` swift
  // things是[Any]
  for thing in things {
  	switch thing {
      	case 0 as Int: 
          case 0 as Double: 
          case let someInt as Int: 
          case let someDouble as Double where someDouble > 0: 
          case is Double: 
          case let someString as String: 
          case let (x, y) as (Double, Double): 
          case let movie as Movie: 
          case let stringConverter as String -> String: 
          default:
          ...
  	}
  }
  ```

---

## 扩展

+ Swift中的扩展可以：
  
  + 添加__计算型属性__和__计算型静态属性(类型属性)__
  + 定义实例方法和类型方法
  + 提供新的构造器（如果是类类型，只能是便利构造器）
  + 定义下标
  + 定义和使用新的嵌套类型
  + 使一个已有类型符合某个协议
  
+ 扩展可以对一个类型添加新的功能，但是不能重写已有的功能.
  
+ __扩展不可以添加存储属性，也不可以向已有属性添加属性观察器__.
  
+ 扩展可以向已有类型添加新的构造器. 
  
  + 类类型：可以添加新的__便利构造器__，但是__不能添加指定构造器或析构器__.
    
  + 值类型：可以添加新的__指定构造器__，如果该值类型已经向所有存储属性提供默认值，并且没有定义任何定制构造器，此时会保留值类型的默认构造器和逐一成员构造器，这也是我们之前说的保留默认构造器和逐一成员构造器的一种方法. (另一种方法是自己显示手写).
    
    但是如果在原类中你提供了定制构造器，那么默认构造器和逐一成员构造器会被干掉，此时只剩下你提供的定制构造器和在扩展中添加的构造器.
  
+ 通过扩展添加的实例方法可以修改实例本身. 结构体和枚举类型中修改`self`或者其属性的方法必须将该实例方法标注为`mutating`.
  
  ``` swift
  extension Int {
    mutating func square*() {
    	self = self * self
    }
  }
  ```

---

## 协议

+ 如果类在遵循协议的同时拥有父类，应该将父类名放在协议名之前.
  
+ 协议中的属性需要表明是`只读`还是`可读可写`，但是不用指定是`存储型属性`还是`计算型属性`.
  
  如果协议规定属性是`可读可写`的，那么这个属性在实现的时候不能是常量或只读的计算属性.
  
  如果协议规定属性是`只读`的，那么这个属性在实现的时候可以不止只读，如果代码需要，也可以是可写的.
  
  ``` swift
  protocol SomeProtocol {
  	var someProperty: String { get }
      var anthorProperty: String { get set }
  }
  ```
  
  ​
  
+ 在协议中可以定义可变参数的方法，但是不支持参数默认值.
  
+ 可以在协议中声明类的__指定构造器__和__便利构造器__，但是在实现这个构造器的时候都必须加上`required`修饰符. 
  
  > 如果类已经被标记为`final`，那么不需要`required`修饰符，因为final类不能有子类.
  
  ``` swift
  protocol SomeProtocol {
  	init(someParameter: Int)
  }
  
  class SomeClass: SomeProtocol {
  	required init(someParameter: Int) {
      	// ...
  	}
  }
  ```
  
  ​
  
+ 如果一个子类重写了父类的指定构造器，并且该构造器遵循了某个协议的规定，那么该构造器的实现需要被同时标示`required`和`override`.
  
  ``` swift
  protocol SomeProtocol {
  	init()
  }
  
  class SomeSuperClass {
    	init() {
      // 构造器的实现
  	}
  }
  
  class SomeSubClass: SomeSuperClass, SomeProtocol {
  	// 因为遵循协议，需要加上`required`;因为继承自父类，需要加上`override`
      required override init() {
      	// 构造器的实现
  	}
  }
  ```
  
+ 如果在协议中定义一个__可失败构造器__，则在遵循该协议的类型中必须添加__同名通参数的可失败构造器或非可失败构造器__. 
  
  如果在协议中定义一个__非可失败构造器__，则在遵循该协议的类型中必须添加__同名同参数的非可失败构造器或隐式解析类型的可失败构造器(init!)__.
  
+ __尽管协议本身并不实现任何功能，但是协议可以当作类型来使用（类似于`类`）__.
  
+ 协议允许多继承.
  
  ``` swift
  protocol InheritingProtocol: SomeProtocol, AnotherProtocol {
  	...
  }
  ```
  
  ​
  
+ 类专属协议：可以在协议的继承列表中，通过添加`class`来限制协议只能适配到类类型. 注意，`class`关键字必须是第一个出现在协议的继承列表中，其后才是其他继承协议.
  
  ``` swift
  protocol SomeClassOnlyProtocol: class, SomeInheritedProtocol {
    ...
  }
  ```
  
+ 协议合成
  
  ``` swift
  protocol Named {
    var name: String { get }
  }
  
  protocol Aged {
    var age: Int { get }
  }
  
  struct Person: Named, Aged {
    var name: String
    var age: Int
  }
  
  func wishHappyBirthday(celevrator: protocol<Named, Aged>) {
    ...
  }
  
  let birthdayPerson = Person()
  wishHappyBirthday(birthdayPerson)
  ```
  
  `Person`类遵循了两个协议.
  
  `withHappyBirthday`的`celebrator`形参可以传入任意遵循这两个协议的类型的实例.
  
  > 注意，在Person遵守协议的地方是不能使用`protocol<Named, Aged>`这种形式的. 在作为参数的类型的时候是可以使用的.
  > 
  > 协议合成并不会生成一个新协议类型，而是将多个协议合成为一个临时的协议，超出范围后立即失效.
  
+ `is`检查实例是否遵循了某个协议.
  
  `as(as?和as!)`用于向下转型. 当实例遵循某个协议时，`as?`返回该协议类型的值.
  
+ 可选协议
  
  使用`optional`作为前缀来定义可选成员.
  
  可选协议在调用时使用`可选链`. 你可以在可选方法名称后面加上`?`. 比如：`someOptionalMethod?(someArgument)`. 如果实现了该协议中的可选方法，那么就会调用这个方法；如果没有实现，则返回nil.
  
  > 注意，可选协议只能在含有`@objc`的协议中生效. (协议中有可选属性/可选方法就需要用`objc`修饰.) `@objc`的协议只能由继承自Objective-C类的类或者其他的`@objc`类来遵循，并且不能被结构体和枚举遵循.
  
  ``` swift
  @objc protocol CounterDataSource {
    optional func incrementForCount(count: Int) -> Int
    optional var fixedIncrement: Int { get }
  }
  
  @objc Class Counter {
    var count = 0
    var dataSource: CounterDataSource?
    func increment() {
      if let amount = dataSource?.incrementForCount?(count) {
    	  count += amount
  	} else if let amount = dataSource?.fixedIncrement? {
        count += amount
  	}
    }
  }
  ```
  
+ 协议也支持扩展，可以通过`extension`来扩展协议，增加属性和方法的实现.
  
  ``` swift
  extension RandomNumberGenerator {
  	func randomBool() -> Bool {
      	return random() > 0.5
  	}
  }
  ```
  
+ 为协议扩展添加限制条件
  
  ``` swift
  extension CollectionType where Generator.Element: TextRepresentable {
  	var textualDescription: String {
      	let itemsAsText = self.map {
          	$0.textualDescription
  		}
          return "[" + itemsAsText.joinWithSeparator(",") + "]"
  	}
  }
  ```
  
  扩展`CollectionType`协议，但是只适用于元素遵循`TextRepresentable`的情况.
  
  ``` swift
  let murrayTheHamster = Hamster(name: "Murray")
  let morganTheHamster = Hamster(name: "Morgan")
  let mauriceTheHamster = Hamster(name: "Maurice")
  let hamsters = [murrayTheHamster, morganTheHamster, mauriceTheHamster]”
  
  print(hamsters.textualDescription)
  ```
  
  `hamsters`遵循`CollectionType`协议，数组的元素(Hamster的实例)又遵循`TextRepresentable`协议，所以数组可以使用`textualDescription`属性.

---

## 泛型

+ 当你拓展一个泛型类型的时候，并不需要在扩展的定义中提供类型参数列表.
  
+ 有时候会对泛型进行约束. 如`Dictionary`的键类型必须是`可哈希(遵循Hashable协议)`的，也就是说，必须有一种方法可以使其被唯一的表示. 所有的Swift基本类型(`String`, `Int`, `Double`, `Bool`)默认都是可哈希.
  
  ``` swift
  func someFunction<T:SomeClass, U: SomeProtocol>(someT: T, someU: U) {
    ...
  }		
  ```
  
+ 当定义一个协议时，需要声明一个或多个__关联类型__作为协议定义的一部分.
  
  ``` swift
  protocol Container {
    typealias ItemType
    mutating func append(item: ItemType)
    var count: Int{ get }
    subscript(i: Int) -> ItemType( get )
  }
  ```
  
  因为协议中用到了某一种数据类型，但是并没有指定是哪一种具体的类型. 所以需要一个类型的`代号`.
  
  ``` swift
  struct IntStack: Container {
  	typealias ItemType = Int
      mutating func append(item: Int){ ... }
      var count: Int { return items.count }
      subscript(i: Int) -> Int { return items[i] }
  }
  ```
  
  因为Swift可以推断出`ItemType`，所以删除`typealias ItemType = Int`这一行，一切仍旧可以工作.
  
  ``` swift
  struct Stack<T>: Container {
    var items = [T]()
    mutating func append(item: T) { ... }
    var count: Int { return items.count }
    subscript(i: Int) -> T { return items[i] }
  }
  ```
  
  以上是使用遵循`Container协议`的泛型`Stack`类型.
  
+ 对关联类型定义约束是非常有用的(类型约束)
  
  ``` swift
  func allItemsMatch<C1: Container, C2: Container where C1.ItemType == C2.ItemType, C1.ItemType: Equatable> (someContainer: C1, anoterContainer: C2) -> Bool {
  	// 检查两个Container的元素个数是否相同
  	if someContainer.count != anoterContainer.count { return false }
      
      // 检查两个Container相应位置的元素彼此是否相等
      for i in 0..<someContainer.count {
    		for someContainer[i] != anoterContainer[i] { return false }
  	}
      // 如果所有元素检查都相同则返回true
      return true
  }
  ```
  
  + `C1`必须遵循`Container`协议 （`C1: Container`）
  + `C2`必须遵循`Container`协议 （`C2: Container`）
  + `C1`的`ItemType`同样是`C2`的`ItemType` （`C1.ItemType == C2.ItemType`）
  + `C1`的`ItemType`必须遵循`Equatable`协议 （`C1.ItemType: Equatable`）