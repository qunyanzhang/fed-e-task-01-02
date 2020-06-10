# fed-e-task-01-02

## 简答题
### 1. 描述引用计数的工作原理和优缺点
引用计数工作原理：判断当前对象的引用数是否为 0，如果为 0，则将其视为垃圾对象，回收内存空间。

#### 优点：

- 发现垃圾时立即回收
- 最大限度减少程序暂停
#### 缺点

- 无法回收循环引用的对象
- 时间开销大
### 2. 描述标记整理算法的工作流程
第一阶段（标记阶段）：遍历对象将当前的可达活动对象进行标记 第二阶段（清除阶段）：先执行整理，移动对象位置，以便整理后空闲内存连续，最大化利用内存剩余空间；同时结合遍历对象清除没有标记的对象，同时抹去已标记对象

### 3. 描述 V8 中新生代存储区垃圾回收的流程
- 回收过程会用到复制算法和标记整理算法
- 新生代存储区会分为两个等大小空间（使用空间：From，空闲空间：To）
- 首先将所有活动对象分配到 From 空间
- 标记整理后将活动对象复制到 To
- From 空间释放
### 4. 描述增量标记算法在何时使用，及工作原理
增量标记算法适用于 V8 的老生代存储区域
增量标记算法在老生代存储区域需要优化内存回收效率时使用
在垃圾回收过程中，如果一次性回收会阻塞程序执行，增量标记的原理是将回收操作分段处理，这样就可以让程序执行和垃圾回收交替处理，从而达到效率优化

## 代码题1
#### 练习1:
使用函数组合 fp.flowRight() 重新实现下面这个函数

```
let isLastInStock = function(cars) {
  // 获取最后一条数据
  let last_car = fp.last(cars)
  // 获取最后一条数据的 in_stock 属性值
  return fp.prop('in_stock', last_car)
}

// 实现
const isInStock = car => fp.prop('in_stock', car)
let isLastInStock = fp.flowRight(isInStock, fp.last)(cars)
```

#### 练习2:
使用函数组合 fp.flowRight()、fp.prop() 和 fp.first() 获取第一个 car 的 name

``` 
// 实现
const carName = car => fp.prop('name', car)
let firstCarName = fp.flowRight(carName, fp.first)(cars)
```

#### 练习3:
使用帮助函数 _average 重构 averageDollarValue，使用函数组合的方式实现

``` 
let _average = function(xs) {
  return fp.reduce(fp.add, 0, xs) / xs.length
}  

let averageDollarValue = function(cars) {
  let dollar_values = fp.map(function(car){
    return car.dollar_value
  }, cars)
  return _average(dollar_values)
}
// 实现
let averageDollarValue = fp.flowRight(_average, fp.map(function(car){
  return car.dollar_value
}))(cars)
```

#### 练习4:
使用 flowRight 写一个 sanitizeNames() 函数，返回一个下划线连接的小写字符串，把数组中的 name 转换为这种形式：例如：sanitizeNames(["Hello World"]) => ["hello_world"]

``` 
let _underscore = fp.replace(/\W+/g, '_')
// 实现
let sanitizeNames = fp.flowRight(
  fp.map(item => ({ ...item, name: _underscore(item.name) })),
  fp.map(item => ({ ...item, name: fp.lowerCase(item.name) }))
)
```
## 代码题2
#### 练习1:
使用 fp.add(x,y) 和 fp.map(f,x) 创建一个能让 functor 里的值增加的函数 ex1

``` 
const fp = require('lodash/fp)
const {Maybe, Contaiber} = require('./support)

let maybe = Maybe.of([5,6,1])
// 每个元素增加 x
let ex1 = x => maybe.map(fp.map(item => fp.add(item, x)))
```

#### 练习2:
实现一个函数 ex2，能够使用 fp.first 获取列表的第一个元素

``` 
const fp = require('lodash/fp)
const {Maybe, Contaiber} = require('./support)

let xs = Container.of(['do', 'ray', 'me', 'fa', 'so', 'la', 'ti', 'do'])
let ex2 = xs.map(fp.first)
```

#### 练习3:
实现一个函数 ex3，能够使用 safeProp 和 fp.first 找到 user 的名字的首字母

``` 
const fp = require('lodash/fp)
const {Maybe, Contaiber} = require('./support)

let safeProp = fp.curry(function(x,o){
  return Maybe.of(o[x])
})
let user = {id: 2, name: 'Albert'}
let ex3 = safeProp('name')(user).map(fp.first)
```

#### 练习4:
实现 Maybe 重写 ex4，不要有 if 语句

``` 
const fp = require('lodash/fp)
const {Maybe, Contaiber} = require('./support)

let ex4 = function (n) {
  if (n) {return parseInt(n)}
}

// 实现
let ex4 = n => Maybe.of(n).map(parseInt)
```