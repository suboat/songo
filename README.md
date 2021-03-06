# songo格式

# v0.1.0

## Overview

* 将mongodb的query格式稍加拓展，开发出可以方便传输并易于解析成sql语句的query格式。
* 我们不生产格式，我们只是格式的搬运工。

## Reference

URL传输songo规范(https://github.com/suboat/songo/blob/master/desc.md)

## Documents

1. 核心描述。
    1. 将mongo中的$xx关键词末尾添加$后使用，如$lte转为$lte$。 加第二个边界$符号，以便算法识别关键词。
    2. 大于等于小于等比较全部放在同一级。如 {age: { $lt: 30 }} 表示为 {age: "$lt$30"}或{"age$lt$":30}。
    3. 与或标识放key前面，比较标识放key后面或者val前面。
    4. 支持的关键词：$and$, $or$, $ne$, $lt$, $lte$, $gt$, $gte$


## Examples

* 实例1:
```javascript
// songo格式(推荐)
var query = {
  limit: 10,
  skip: 20,
  age: "$gte$30"
};
// 或
var query = {
  limit: 10,
  skip: 20,
  age$gte$: 30
};
// 或
var query = {
  limit: 10,
  skip: 20,
  age: {
      $gte$: 30
  }
};

// 解析成的mongo格式
var queryMongo = {
  limit: 10,
  skip: 20,
  age: {
      $gt: 30
  }
};

// 解析成的sql格式
var querySqlWhere = "limit=$1 AND skip=$3 AND age > $3";
var queryValues = [10, 20, 30];
```

* 实例2:
```javascript
// songo格式(推荐)
var query = {
  limit: 10,
  skip: 20,
  $and$age: ["$gte$30", "$lte$40"]
};
// 或
var query = {
  limit: 10,
  skip: 20,
  $and$: [
      {age: "$gte$30"},
      {age: "$lte$40"}
  ],
};
// 或
var query = {
  limit: 10,
  skip: 20,
  $and$: [
      {age$gte$: 30},
      {age$lte$: 40}
  ],
};
// 或
var query = {
  limit: 10,
  skip: 20,
  $and$: [
      {age: {$gte$: 30}},
      {age: {$lte$: 40}}
  ],
};

// 解析成的mongo格式
var queryMongo = {
  limit: 10,
  skip: 20,
  $and: [
      {age: {$gte: 30}}, 
      {age: {$lte: 40}}
  ]
};

// 解析成的sql格式
var querySqlWhere = "limit=$1 AND skip=$3 AND (age > $3 AND age < $4)";
var queryValues = [10, 20, 30, 40];
```

* 实例3:
```javascript
// songo格式(推荐)
var query = {
  limit: 10,
  skip: 20,
  $or$age: ["$lte$30", "$gte$40"]
};
// 或
var query = {
  limit: 10,
  skip: 20,
  $or$: [
      {age: "$lte$30"},
      {age: "$gte$40"}
  ],
};
// 或
var query = {
  limit: 10,
  skip: 20,
  $or$: [
      {age$lte$: 30},
      {age$gte$: 40}
  ],
};
// 或
var query = {
  limit: 10,
  skip: 20,
  $or$: [
      {age: {$lte$: 30}},
      {age: {$gte$: 40}}
  ],
};

// 解析成的mongo格式
var queryMongo = {
  limit: 10,
  skip: 20,
  $or: [
      {age: {$lte: 30}}, 
      {age: {$gte: 40}}
  ]
};

// 解析成的sql格式
var querySqlWhere = "limit=$1 AND skip=$3 AND (age < $3 OR age > $4)";
var queryValues = [10, 20, 30, 40];
```

* 实例4:
```javascript
// songo格式(推荐)
var query = {
  "$and$": [
    {
      "$or$": [
        {
          "category": "type1"
        },
        {
          "category": "type2"
        }
      ]
    }
  ],
  "$or$": [
    {
      "$and$": [
        {
          "active": true,
          "status": 0
        }
      ]
    },
    {
      "$and$": [
        {
          "active": false,
          "status": 1
        }
      ]
    }
  ],
  "uid": "11111111-1111-1111-1111-111111111111"
}

// 解析成的mongo格式
var queryMongo = {
  "$and": [
    {
      "$or": [
        {
          "category": "type1"
        },
        {
          "category": "type2"
        }
      ]
    }
  ],
  "$or": [
    {
      "$and": [
        {
          "active": true,
          "status": 0
        }
      ]
    },
    {
      "$and": [
        {
          "active": false,
          "status": 1
        }
      ]
    }
  ],
  "uid": "11111111-1111-1111-1111-111111111111"
};

// 解析成的sql格式
var querySqlWhere = "((category = $1 OR category = $2)) AND ((status = $3 AND active = $4) OR (status = $5 AND active = $6)) AND (uid = $7)";
var queryValues = ["type1","type2",0,true,1,false,"11111111-1111-1111-1111-111111111111"];
```
