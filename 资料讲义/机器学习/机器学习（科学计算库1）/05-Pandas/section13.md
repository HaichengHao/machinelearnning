# 读写数据操作拓展



## 1 读写excel文件

- Excel文件也是比较常见的用于存储数据的方式,它里面的数据均是以二维表格的形式显示的,可以对数据进行统计、分析等操作。 Excel的文件扩展名有xls和,xlsx两种。
- Pandas中提供了对Excel文件进行读写操作的方法,分别为 to_excel()方法和 read_excel()函数,关于它们的操作具体如下:



### 1.1 to_excel()

to_excel方法的功能是将 Dataframe对象写入到 Excel工作表中,该方法的语法格式如下:

```python
to_excel(excel_writer, 
         sheet_name='Sheet1', 
         na_rep='',
         index=True)
```

上述方法中常用参数表示的含义如下:

* (1) excel_writer：表示读取的文件路径。

- (2) sheet_name：表示工作表的名称，可以接收字符串,默认为“ Sheet1”
- (3) na_rep：表示缺失数据。
- (4) index：表示是否写行索引，默认为True

为了能够让大家更好地理解，接下来，创建一个2行2列的Dataframe对象,之后将该对象写入到 iteast.xlsx文件中，具体代码如下：

```python
import pandas as pd
df1 = pd.DataFrame({'col1': ['传', '智'], 'col2': ['播', '客']})
df1
```

<img src="https://tva1.sinaimg.cn/large/006tNbRwgy1gav9uir2doj306a04oa9y.jpg" alt="image-20200113214907016" style="zoom:50%;" />

```python
df1.to_excel('./data/itcast.xlsx', 'python基础班')
'写入完毕'
```

> tips：如果写入的文件不存在,则系统会自动创建一个文件，反之则会将原文中的内容进行覆盖。



### 1.2 read_excel()

- read_excel()函数的作用是将 Excel文件中的数据读取出来,并转换成 Dataframe对象,其语法格式如下：


```
read_excel(io, sheet_name=0)
```

上述函数中常用参数表示的含义如下:

- (1) io：接收字符串，表示路径对象。
- (2) sheet_name：指定要读取的工作表，可接收字符串或int类型，
    - 字符串指工作表名称，
    - int类型指工作表的索引。

接下来，通过 read_excel() 函数将 itcast.xlsx文件中的数据全部读取出来，示例代码如下。

```python
data = pd.read_excel("./data/itcast.xlsx")
```



## 2 读写数据库

- 大多数情况下，海量的数据是使用数据库进行存储的，这主要是依赖于数据库的数据结构化、数据共享性、独立性等特点。因此，在实际生产环境中，绝大多数的数据都是存储在数据库中。

- Pandas支持 MySQL、Oracle、SQlite等主流数据库的读写操作。

- 为了高效地对数据库中的数据进行读取，这里需要引入 SQLAlchemy。SQLAlchemy是使用Python编写的一款开源软件，它提供的SQL工具包和对象映射工具能够高效地访问数据库。

- 安装方式：

    ```python
    pip3 install sqlalchemy
    ```

- 在使用 SQLAlchemy时需要使用相应的连接工具包，比如Mysql需要安装 mysqlconnector，Oracle则需要安装 cx_Oracle.

    - 在连接 MySQL数据库时，这里使用的是 mysqlconnector驱动，如果当前的 Python环境中没有该模块，则需要使用` python -m pip install mysql-connector`命令安装该模块.

- 下面以read_sql() 函数和to_sql() 方法为例：分别向大家介绍如何读写数据库中的数据，具体内容如下。




### 2.1 read_sql()

read_sql() 函数**既可以读取整张数据表，又可以执行SQL语句**，其语法格式如下:

```python
pandas.read_sql(sql, 
                con, 
                index_col=None, 
                params=None, 
                columns=None)
```

上述函数中常用参数表示的含义如下:

- ​	(1) sql：表示被执行的sql语句

- ​	(2) con：接收数据库连接，表示数据库的连接信息。

- ​	(3) index_col：默认为None，如果传入一个列表，则表示为层次化索引。

- ​	(4) params：传递给执行方法的参数列表，如 params={'name：‘value’}

- ​	(5) columns：接收list 表示读取数据的列名，默认为None


如果发现数据中存在空值，则会使用NaN进行补全。

假设在MySQL数据库有一张数据表，该表中的内容如下图所示。

<img src="https://tva1.sinaimg.cn/large/006tNbRwgy1gav9xg8lttj30yw0megpu.jpg" alt="image-20200113215156860" style="zoom: 50%;" />

接下来，通过一个示例来演示如何使用read_sql() 函数读取数据库中的数据表 goods，示例代码如下：

```python
import pandas as pd
import pymysql
# 向mysql存储数据的时候，需要数据库的引擎
from sqlalchemy import create_engine

# mysql账号为root  密码为mysql 数据名：jing_dong 
# 数据表名称：goods
engine = create_engine('mysql+pymysql://root:mysql@127.0.0.1/jing_dong')
pd.read_sql('goods',engine)
```



上述示例中，通过 create_engine0函数创建连接数据库的信息，调用 read_sql() 函数读取数据库中的goods数据表，并转换成DataFrame对象。

> 注意：在使用 create_engine函数创建连接时，其格式如下："数据库类型+数据库驱动名称://用户名密码@机器地址/数据库名。

read_sql() 函数还可以执行一个SOL语句

例如，从goods数据表中第选出price值大于3的全部数据，具体的SQL语句如下：

```
engine = create_engine('mysql+pymysql://root:mysql@127.0.0.1/jing_dong')
sql = "select * from goods where price > 7000;"
pd.read_sql(sql,engine)
```

根据上述SQL语句来读取数据库里面的数据，并将执行后的结果转换成 DataFrame对象，示例结果如下：

<img src="https://tva1.sinaimg.cn/large/006tNbRwgy1gava1u30w5j30yi07w0u7.jpg" alt="image-20200113215609003" style="zoom:50%;" />



需要强调的是，这里的SQL语句不仅是用于筛选的SQL语句，其他用于增删改查的SQL语句都是可以执行的。



### 2.2 to_sql()

to_sql() 方法的功能是将 Series或 Dataframe对象以数据表的形式写入到数据库中，其语法格式如下:

```python
to_sql(name， 
       con，
       if_exists ='fail'， 
       index=True)
```

上述方法中，参数所表示的含义如下所示。

- (1) name：表示数据库表的名称。
- (2) con：表示数据库的连接信息。
- (3) if_exists：可以取值为fail、replace或append，默认为fail， 每个取值代表的含义如下：
    - fail：如果表存在，则不执行写入操作。
    - replace：如果表存在，则将源数据库表删除再重新创建。
    - append：如果表存在，那么在原数据库表的基础上追加数据。

- (4) index：表示是否将 DataFrame行索引作为数据传入数据库，默认为True。


接下来，通过一个示例程序来演示如何使用 Pandas向数据库中写入数据。

首先，创建一个名称为 students info的数据库，具体的SOL语句如下。

```
create database students_info charset=utf8；
```

然后，创建一个与下图的表格结构相同的 DataFrame对象，它统计了每个年级中男生和女生的人数。

<img src="https://tva1.sinaimg.cn/large/006tNbRwgy1gava4thpg1j30hz05n7ia.jpg" alt="年纪人数" style="zoom:50%;" />

接着，调用to_sql() 函数将 Dataframe对象写入到名称为四年级studnets的数据表中，具体代码如下.

```python
df = DataFrame({"班级":["一年级","二年级","三年级","四年级"],
                              "男生人数":[25,23,27,30],
                              "女生人数":[19,17,20,20]})
# 创建数据库引擎
# mysql+pymysql 表示使用Mysql数据库的pymysql驱动
# 账号：root 密码：123456 数据库名：studnets_info
# 数据表的名称： students

engine = create_engine('mysql+pymysql://root:mysql@127.0.0.1/students_info')
df.to_sql('students',engine)
```



当程序执行结束后，可以在数据库中查看是否成功创建了数据表，以及数据是否保存成功，这里使用命令行的方式进行验证。

打开navicat图形化界面工具，点击文件，选中查询表，在弹出来的窗口中，输入如下命令，点击“运行”即可。（或者打开命令提示符窗口，在光标位置输人“myql -u数据库账号 -p密码”进行登录。登录成功后，用“use”命令选择 studets_info数据库，然后使用如下命令语句查询 students表中的全部数据）

```
select * from students
```

查询到的结果具体如图所示。

<img src="https://tva1.sinaimg.cn/large/006tNbRwgy1gava4pcfk0j30kw0fpkjl.jpg" alt="查询年纪人数" style="zoom: 67%;" />

> 注意：在使用to_sql() 方法写入数据时，如果写入的数据表名与数据库中其他数据表名相同时，则会返回该数据表已存在的错误。

