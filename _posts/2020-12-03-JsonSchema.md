---
layout: post
title: "Json schema"
date: 2020-12-03
categories: note
tags: [Json, 前端]
---
# Json schema 动态表单组件

#### Json schema

相关库： ajv

```javascript
const Ajv = require('ajv')

const schema = {
  type: 'object',
  properties: {
    name: {
      type: 'string',
      // format: 'email' // format 只针对string类型和number类型
    }
  required: ['name']
}
const ajv = new Ajv()
const validate = ajv.compile(schema)
const valid = validate({
  name: 'finn@123.com'
})
if (!valid)  console.log(validate.errors)
```



##### format

##### addFormat 自定义format规则

```javascript
// 自定义format
ajv.addFormat('test', (data) => {
  console.log(data, '====')
  return data === 'haha'
}) // ajv库的addFormat 方法拓展校验规则 此方法并不是json schema官方规定的
```



##### 自定义关键字

addKeyword

1. validate ，返回boolean值
2. compilation，compile。返回 一个函数
  - metaSchema
3. macro，返回一个Json Schema。 macro 里的schema会被加入到parentSchema中，对数据进行校验
4. inline compilation： inline。 性能最优，代码可阅读性最差。预编译

```javascript
ajv.addKeyword('testk', {
  // 1. validate
  validate: function func(schema, data) {
    console.log(schema, data)
    func.errors = [{
      keyword: 'testk',
      dataPath: '.name',
      schemaPath: '#/properties/name/testk',
      params: {
        keyword: 'testk'
      },
      message: '这是一条自定义的错误信息'
    }]
    return data.length < 5
  }
    
  // 2. compile
  // compile(schema, parentSchema) {
  //   console.log(schema, parentSchema)
  //   return () => true // 必须返回函数
  // },
  // metaSchema: {
  //   type: 'boolean'
  // },
 
  // 3. macro
  // macro(schema, parentSchema) {
  //   return {
  //     minLength: 10,
  //   }
  //   // macro 里的schema会被加入到parentSchema中，对数据进行校验
  // }
})
```



##### 定义错误信息

ajv-i18n

```javascript
const localize = require('ajv-i18n')
if(valid) {
    localize.zh(validate.errors) //内置中文错误信息      
}

// 自定义keyword中
ajv.addKeyword('testk', {
  // 1. validate
  validate: function func(schema, data) {
    console.log(schema, data)
    func.errors = [{
      keyword: 'testk',
      dataPath: '.name',
      schemaPath: '#/properties/name/testk',
      params: {
        keyword: 'testk'
      },
      message: '这是一条自定义的错误信息'
    }]
    return data.length < 5
  }
```

ajv-errors  不支持自定义关键字的错误信息，自定义关键字的错误信息仍需要类似上例去处理

```javascript
// 在schema中定义：errorMessage字段
const schema = {
  type: 'object',
  properties: {
    name: {
      type: 'string',
      // format: 'email' // format 只针对string类型和number类型
      testk: true,
      errorMessage: '这是不对的'
    },
  }
}

name: {
    type: 'string',
    format: 'email', // format 只针对string类型和number类型
    // testk: true,
    errorMessage: {
    type: '必须是字符串',
    format: '电子邮箱格式不正确'
  }
},


const ajv = new Ajv({
  allErrors: true,
  jsonPointers: true
})
require('ajv-errors')(ajv)
```

[ajv官网](https://ajv.js.org/)

[JsonSchema官网](http://json-schema.org/specification.html)

