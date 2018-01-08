---
title: MongoDB的简单使用
---
# 启动
在命令行下进入MongoDB\bin目录(dbpath后面为数据库的位置)
```
mongod --dbpath E:\MongoDB\data
```
默认地址为： http://localhost:27017/

# 使用
在node中安装mongoose
```
npm install mongoose --save
```
引入并连接数据库
```
var mongoose = require('mongoose')
var db = mongoose.connect('mongodb://localhost:27017')
```

# Schema
Schema用来定义数据库文档结构，数据库有什么字段、字段是什么类型、默认值、主键之类的信息。
```
var blogSchema = new mongoose.Schema({
    title:  String,
    comments: [{ body: String, date: Date }],
    date: { type: Date, default: Date.now },
    hidden: Boolean,
    meta: {
        votes: Number,
        favs:  Number
  }
})
```

如需再添加数据，用add方法。
```
blogSchema.add( { author: String, body: String} );
```

# Model
```
var blogModel = mongoose.model(blogSchema);
```

# 增
```
blogModel.create({
    title: '增加'
    ...
}, function(err, data){
    if(err) console.log(err)
    console.log(data)
})
```
增加多条数据：
```
blogModel.insertMany([
    {title: "mongoose1", author: "L"}, 
    {title: "mongoose2", author: "L"}
    ], function(err, data){
        if(err) console.log(err);
        console.log(data);
});
```
返回一条数据：
```
[Model.findOne([conditions], [projection], [options], [callback])]
```


# 查
```
blogModel.find({title: "Mongoose"}, {title: 1}, function(err, data){
    if(err) console.log(err);
    console.log(data);
})
```
查找title为Mongoose的数据，仅返回字段为title的值

# 改
```
blogModel.update({title: "Mongoose"}, {author: "L"}, {multi: true}, function(err, docs){
    if(err) console.log(err);
    console.log('更改成功：' + docs);
})
```
查找title为Mongoose的数据，把author数据该为L，支持多行修改

一次更新多条：
```
[Model.updateMany(conditions, doc, [options], [callback])]
```
一次更新一条：
```
[Model.updateOne(conditions, doc, [options], [callback])]
```

# 删
```
blogModel.remove({author: "L"}, function(err, data){
    if(err) console.log(err);
    console.log(data);
})
```
删除author为L的数据

(简书笔记搬运 写于2017.09.12 23:36)