## 构建python链接MySQL的模块(db.py)

```python
import pymysql
"""
模块：读写MySQL
"""
def get_conn():
    """
    获取MySQL的链接
    return：mysql connection
    """
    return pymysql.connect(host='localhost',
                           user='root',
                           passwd='56835683+-',
                           db='test_db',
                           charset='utf8') # 选好数据库

def query_data(sql):
    """
    根据SQL查询数据并且返回
    parameter:SQL语句
    return:list[dict]
    """
    conn = get_conn()
    try:
        cursor = conn.cursor(pymysql.cursors.DictCursor)
        cursor.execute(sql)
        return cursor.fetchall()
    finally:
        conn.close()

def insert_or_update_data(sql):
    """
    执行新增insert或update的SQL
    parameter:insert或update SQL语句
    return:不返回内容
    """
    conn = get_conn()
    try:
        cursor = conn.cursor()
        cursor.execute(sql)
        conn.commit()
    except:
        # 发生错误时回滚
        conn.rollback()
    finally:
        conn.close()
```



## 查询操作

```python
import db

query_sql = "select * from tmp10"
db.query_data(query_sql)
```

结果：

![image-20210115163328194](C:\Users\GUMP\AppData\Roaming\Typora\typora-user-images\image-20210115163328194.png)

## 插入或更新操作

```python
import db

update_sql = "update tmp10 set soc = 85 where level = 'good'"
mysql.insert_or_update_data(update_sql)
query_sql = "select * from tmp10"
mysql.query_data(query_sql)
```

结果：

![image-20210115163602301](C:\Users\GUMP\AppData\Roaming\Typora\typora-user-images\image-20210115163602301.png)