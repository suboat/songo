## songo描述

### 参考前端js库[https://github.com/axetroy/songojs](https://github.com/axetroy/songojs)

### [limit格式](#limit)

> 限制获取数据的范围

```bash
_limit  = value:number    // 每页获取xxx个条数据
_page   = value:number    // 获取第xx页的数据(以0开始作为第1页)
```

例子: 每页获取10条数据，获取第0页

http://127.0.0.1/demo?_limit=10&_page=0

### [sort格式](#sort)

> 获取数据的排序方式

```bash
_sort   = value:string[]    // 按照一定的顺序排序
```

例子: 按照创建时间正向排序

http://127.0.0.1/demo?_sort=created

例子: 按照创建时间反向排序**反向排序需要前缀 - 号**

http://127.0.0.1/demo?_sort=-created

例子: 按照多个条件排序(排在前面的优先)

http://127.0.0.1/demo?_sort=created,money,-level

### [query格式](#query)

$**Group**$**Key**=$**Operator**$**value**

#### Group:string

```bash
  name             Group       desc

QueryKeyAnd         = and      // 与
QueryKeyOr          = or       // 或
```
  
#### Operator:string

```bash
  name             Operator  value       desc

QueryKeyEq          = eq     string     // 等于
QueryKeyNe          = ne     string     // 不等于
QueryKeyLt          = lt     string     // 小于
QueryKeyLte         = lte    string     // 小于等于
QueryKeyGt          = gt     string     // 大于
QueryKeyGte         = gte    string     // 大于等于
QueryKeyLike        = like   string     // 模糊搜索
QueryKeyIn          = in     string[]   // 在...之中
QueryKeyBetween     = bt     string[]   // 在...之间
QueryKeyNotBetween  = nbt    string[]   // 不在...之间
```

#### Key:any

#### Value:string | string[]

> string为任意字符串,string[]为只含有字符串的数组

### 完整例子

> 每页获取10个，获取第0页

> 按照创建时间正向排序

> 年=2016 && 月=10 && 日期大于15

```bash
http://127.0.0.1/demo?_limit=10&_page=0&_sort=created&year=$eq$2016&month=$eq$10&date=$gte$15

解析：

_limit  = 10          // 每页获取10个
_page   = 0           // 获取第0页

_sort   = created     // 按照创建时间正向排序

year    = $eq$2016    // 年=2016
month   = $eq$10      // 月=10
date    = $gte$15     // 日期大于15
```

> 每页获取50个，获取第2页

> 按照创建时间正向排序 >> 按照用户金额正向排序 >> 按照用户等级反向排序

> 年=2016 && 在8-11月之间 && 日期等于1号 && (星期六 || 星期天)

```bash
http://127.0.0.1/demo?_limit=50&_page=2&_sort=created,money,-level&year=$eq$2016&month=$bt$8,11&date=$eq$1&day=$in$0,6

解析：

_limit  = 50              // 每页获取50个
_page   = 2               // 获取第2页

_sort   = created,money,-level   // 按照创建时间正向排序 >> 按照用户金额正向排序 >> 按照用户等级反向排序

year    = $eq$2016        // 年=2016
month   = $bt$8,11        // 在8-11月之间
date    = $eq$1           // 日期等于1号
day     = $in$0,6         // 星期六 || 星期天
```

> 每页获取20个，获取第5页

> 按照金额反向排序

> 金额>=100 || 用户等级>10

```bash
http://127.0.0.1/demo?_limit=20&_page=5&_sort=-money&$or$money=$gte$100$or$level=$gt$10

解析：

_limit       = 20               // 每页获取50个
_page        = 5                // 获取第2页

_sort        = -money           // 按照金额反向排序

$or$money    = $gte$100         // 金额>=100
$or$level    = $gt$10           // 用户等级>10
```
