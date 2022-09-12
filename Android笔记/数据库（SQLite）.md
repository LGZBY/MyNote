数据库（SQLite）

- 数据库(SQLite)的创建

- 添加一条数据

- 删除一条数据

- 修改一条数据

- 查询一条数据

## SQL数据库相关概念

- SQL(Structured Query Language):结构化查询语言

- 数据库：

​	MySQL:是一个广泛应用于后端服务器的关系型数据库。其他的还有:Oracle、SQL Server等

​	SQLite:一款轻型的数据库，目标是应用于嵌入式设备（手机）里。

![image-20220821165626961](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20220821165626961.png)

## 数据库中的内容组织形式

- 数据库中的内容是以表的形式存在的

<img src="C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20220821165835813.png" alt="image-20220821165835813" style="zoom:67%;" />

## 简单的SQL语言

- 创建表create tableName (id integer primary key autoincrement, name text, number text);

- 删除表drop table if exists tableName;

- 增加一条数据insert into tableName values(“小明","111");

- 删除一条数据DELETE FROM tableName WHERE name=“小明"”;

- 修改一条数据update tableName set name=“小红” where name=“小明”

- 查询一条数据select * from tableName whare name=“小明”

- 数据库SQLiteOpenHelper、sQLiteDatabase

## Android中数据库的创建与SQL语句的执行

- 数据库类:SQLiteDatabase

- 数据库帮助类:SQLiteOpenHelper

**数据库的创建**

方法一：

- db = SQLiteDatabase.openOrCreateDatabase(DATABASE_NAME,Context.MODE_PRIVATE, null)

方法二：

- db = getContext().openOrCreateDatabase(DATABASE_NAME,Context.MODE_PRIVATE, null)

方法三：（推荐）

- db = SQLiteOpenHelper.getWritableDatabase()

- db = sQLiteOpenHelper.getReableDatabase()

**执行SQL语句**

- String sql = “insert into tableName values(“小明","111");";

- db.execSQL(sql);

## Android中封装好的SQL语句执行方法

增加一条数据db.insert(TABLE_NAME, null, values);

删除一条数据db.delete(TABLE_NAME,selection, selectionArgs)

修改一条数据db.update(TABLE_NAME,values,selection,selectionArgs);

查询一条数据db.query(

​	TABLE_NAME,l/ The table to query

​	projection,		//The array of columns to return (pass null to get all)

​	selection,		  // The columns for the WHERE clause

​	selectionArgs,   //The values for the WHERE clause

​	null,					 // don't group the rows

​	null,					//don't filter by row groups

​	sortOrder		  // The sort order

​	};

## 案例

<img src="C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20220821172213081.png" alt="image-20220821172213081" style="zoom: 67%;" />

<img src="C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20220821172319233.png" alt="image-20220821172319233" style="zoom:67%;" />![image-20220821193805687](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20220821193805687.png)

<img src="C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20220821172319233.png" alt="image-20220821172319233" style="zoom:67%;" />![image-20220821193805687](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20220821193805687.png)

<img src="C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20220821172350322.png" alt="image-20220821172350322" style="zoom:67%;" />

<img src="C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20220821172504200.png" alt="image-20220821172504200" style="zoom:67%;" />

















