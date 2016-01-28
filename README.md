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