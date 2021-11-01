# CRUD

## 创建

###  **`db.collection.insertOne()`**

<img src="https://tva1.sinaimg.cn/large/e6c9d24egy1gojhcoyfezj21r60os4qp.jpg" alt="image-20210314154627014" style="zoom: 33%;" />



###  **`db.collection.insertMany()`**



```shell
db.accounts.insertMany([
    {
        name:'liu',
        balance:500
    },
      {
        name:'liu1',
        balance:5000
    },
    
    ])
```



<img src="/Users/ljun/Library/Application Support/typora-user-images/image-20210314155351122.png" alt="image-20210314155351122" style="zoom:33%;" />



### **`db.collection.insert()`**

既可以插入一个，也可以插入多个。



### **`db.collection.save()`**

和insert()一样。





## 读取

### `db.collection.find()`

`db.collection.find().pretty()`



### 匹配查询：

1.  `db.accounts.find({name:'alice',balance:100})`
2. 符合主键：`db.accounts.find({"_id.type":'saving'})`

###  比较操作

1. 相等：`db.accounts.find({name:{$eq:'alice'}})`

2. 不等：`db.accounts.find({name:{$ne:'alice'}})` 

   1. `db.accounts.find({"_id.type":'saving'})`主键不等时，如果主键中压根没有type属性，也会被查出来。可以添加$exists来确保type属性是存在的。

3. `$gt`: great then

4. `$lt`

5. 读取alice和charlie的账户。

   ```
   db.accounts.find({
   name:{
   	$in:['alice','charlie']   //$nin  not in
        }
   })
   ```

### 逻辑操作符:

1. $not：读取余额不小于的500的文档：

   ```js
   db.accounts.finds({
     blalance:{
       $not:{
         $lt:500
       }
     }
   })
   ```

2. $and

   ```js
   db.accounts.finds({
       $and:[
         {balance:{$gt:100}},
         {name:{$gt:'fred'}},
       ]
   })
   
   //或者
   db.accounts.finds({
         {balance:{$gt:100}},
         {name:{$gt:'fred'}},
   })
   
   //
   db.accounts.finds({balance:{$gt:100,$lt:500}})
   ```

3. $or

4. $nor ：全部不属于

### 字段操作符

1. $exists：某个字段存在

   ```js
   db.accounts.find({"_id.type":'saving',$exists:true})
   ```

2. $type: 

   读取文档主键是字符串的文档：

   ```js
   db.accounts.find({
     _id:{
       $type:'string'   //null ，找到该字段为null的文档。
     }
   })
   ```

   

### 数组操作符

1. $all 必须所有查询条件包括。

   ```js
   db.accounts.find({
     contact:{
       $all:['China','Beijing']
     }
   })
   ```

   

2.  $elementMatch 

   ![image-20210314194829528](https://tva1.sinaimg.cn/large/e6c9d24egy1gojocbh4nej21em0cmqin.jpg)





### 运算操作符

![image-20210314195256543](/Users/ljun/Library/Application Support/typora-user-images/image-20210314195256543.png)







## 游标

查询默认只显示前20条数据。

> 游标在遍历完或者10分钟后会自动关闭。
>
> 如果需要保持游标一直有效，需要：noCursorTimeout()函数来保持。
>
> 之后，需要手动关闭游标。

**`const myCursor = db.accounts.find()`**

1. 直接用游标访问：`myCursor[1]`
2. 保持游标有效：`const myCursor = db.accounts.find().noCursorTimeout()`
3. 游标关闭：`myCursor.close()`



### 游标文档

**sort()最先 skip()居中 limit()最后执行**

#### `hasNext()、next()`

#### `forEach()`

#### `limit()和 skip()`

<img src="https://tva1.sinaimg.cn/large/e6c9d24egy1gojonpxbjkj21500cygw4.jpg" alt="image-20210314195927894" style="zoom:50%;" />



#### `count()`

<img src="https://tva1.sinaimg.cn/large/e6c9d24egy1gojov1ksllj21ei0oy4qp.jpg" alt="image-20210314200629985" style="zoom:33%;" />



#### sort()





## 更新

### `update()`

1. 文档的主键是不能更改的。

<img src="https://tva1.sinaimg.cn/large/e6c9d24egy1gojp6i65idj21e40o84qp.jpg" alt="image-20210314201732198" style="zoom:33%;" />

2. 只更新一个文档：

<img src="https://tva1.sinaimg.cn/large/e6c9d24egy1gojp9tpl0uj21em0ne1kx.jpg" alt="image-20210314202041681" style="zoom:33%;" />



#### 更新特定字段

##### $set



​	<img src="https://tva1.sinaimg.cn/large/e6c9d24egy1gojpe7296pj21c00nkww5.jpg" alt="image-20210314202456918" style="zoom:33%;" />





<img src="https://tva1.sinaimg.cn/large/e6c9d24egy1gojpgluid8j20ow0fk47c.jpg" alt="image-20210314202715293" style="zoom:50%;" />





##### $unset

删除字段

$unset中任何值无效，都会仍然删除该字段。

<img src="https://tva1.sinaimg.cn/large/e6c9d24egy1gojplnf0scj20o60fidol.jpg" alt="image-20210314203206134" style="zoom:40%;" />





* $rename
* $inc
* $mul
* $min
* $max