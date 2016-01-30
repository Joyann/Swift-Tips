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
  
  ​