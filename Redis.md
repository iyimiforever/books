# **Redis**

**Redis介绍**

- **特点及优点**

```python
1、开源的，使用C编写，基于内存且支持持久化
2、高性能的Key-Value的NoSQL数据库
3、支持数据类型丰富，字符串strings，散列hashes，列表lists，集合sets，有序集合sorted sets 等等
4、支持多种编程语言（C C++ Python Java PHP ... ）
```

- **与其他数据库对比**

```python
1、MySQL : 关系型数据库，表格，基于磁盘，慢
2、MongoDB：键值对文档型数据库，值为JSON文档，基于磁盘，慢，存储数据类型单一
3、Redis的诞生是为了解决什么问题？？
   # 解决硬盘IO带来的性能瓶颈
```

- **应用场景**

```python
1、使用Redis来缓存一些经常被用到、或者需要耗费大量资源的内容，通过这些内容放到redis里面，程序可以快速读取这些内容
2、一个网站，如果某个页面经常会被访问到，或者创建页面时消耗的资源比较多，比如需要多次访问数据库、生成时间比较长等，我们可以使用redis将这个页面缓存起来，减轻网站负担，降低网站的延迟，比如说网站首页等
# redis的诞生是为了解决负载问题
```

- **redis版本**

```python
1、最新版本：5.0
2、常用版本：2.4、2.6、2.8、3.0(里程碑)、3.2、3.4、4.0(教学环境版本)、5.0
3、图形界面管理工具( # 写的一般 )
	RedisDesktopManager
```

- **Redis附加功能**

```python
1、持久化
  将内存中数据保存到磁盘中，保证数据安全，方便进行数据备份和恢复
2、过期键功能
   为键设置一个过期时间，让它在指定时间内自动删除
   <节省内存空间>
   # 音乐播放器，日播放排名，过期自动删除
3、事务功能
   原子的执行多个操作
4、主从复制
5、Sentinel哨兵
```

## **安装**

- Ubuntu

```python
# 安装
sudo apt-get install redis-server
# 服务端启动
sudo /etc/init.d/redis-server status | start | stop | restart
# 客户端连接
redis-cli -h IP地址 -p 6379 -a 密码
```

- Windows

```python
1、下载安装包
   https://github.com/ServiceStack/redis-windows/blob/master/downloads/redis-64.3.0.503.zip
2、解压
3、启动服务端
   双击解压后的 redis-server.exe 
4、客户端连接
   双击解压后的 redis-cli.exe

# Windows下产生的问题：关闭终端后服务终止
# 解决方案：将Redis服务安装到本地服务
1、重命名 redis.windows.conf 为 redis.conf,作为redis服务的配置文件
2、cmd命令行，进入到redis-server.exe所在目录
3、执行：redis-server --service-install redis.conf --loglevel verbose
4、计算机-管理-服务-Redis-启动

# 卸载
到 redis-server.exe 所在路径执行：
1、redis-server --service-uninstall
2、sc delete Redis
```

## **配置文件详解**

- **配置文件所在路径**

```python
1、Ubuntu
	/etc/redis/redis.conf
  mysql的配置文件在哪里？ : /etc/mysql/mysql.conf.d/mysqld.cnf

2、windows 下载解压后的redis文件夹中
	redis.windows.conf 
	redis.conf
```

- **设置连接密码**

```python
1、requirepass 密码
2、重启服务
   sudo /etc/init.d/redis-server restart
3、客户端连接
   redis-cli -h 127.0.0.1 -p 6379 -a 123456
   127.0.0.1:6379>ping
```

- **允许远程连接**

```python
1、注释掉本地IP地址绑定
  69行: # bind 127.0.0.1 ::1
2、关闭保护模式(把yes改为no)
  88行: protected-mode no
3、重启服务
  sudo /etc/init.d/redis-server restart
```

- 远程连接测试

  **Windows连接Ubuntu的Redis服务**

```python
# cmd命令行
1、e:
2、cd Redis3.0
3、redis-cli -h x.x.x.x -a 123456
4、x.x.x.x:6379>ping
```

## **数据类型**

- **通用命令 ==适用于所有数据类型==**

```python
# 切换库(number的值在0-15之间,db0 ~ db15)
select number
# 查看键
keys 表达式  # keys *
# 数据类型
TYPE key
# 键是否存在
exists key
# 删除键
del key
# 键重命名
rename key newkey
# 清除当前库中所有数据（慎用）
flushdb
# 清除所有库中所有数据（慎用）
flushall
```

## **字符串类型(string)**

- **特点**

```python
1、字符串、数字，都会转为字符串来存储
2、以二进制的方式存储在内存中
```

**字符串常用命令-==必须掌握==**

```python
# 1. 设置一个key-value
set key value
# 2. 获取key的值
get key
# 3. key不存在时再进行设置(nx)
set key value nx  # not exists
# 4. 设置过期时间(ex)
set key value ex seconds

# 5. 同时设置多个key-value
mset key1 value1 key2 value2 key3 value3
# 6. 同时获取多个key-value
mget key1 key2 key3 
```

**字符串常用命令-==作为了解==**

```python
# 1.获取长度
strlen key
# 2.获取指定范围切片内容
getrange key start stop
# 3.从索引值开始，value替换原内容
setrange key index value
# 4.追加拼接value的值
append key value

```

**数值操作-==字符串类型数字(必须掌握)==**

```python
# 整数操作
INCRBY key 步长
DECRBY key 步长
INCR key : +1操作
DECR key : -1操作
# 应用场景: 抖音上有人关注你了，是不是可以用INCR呢，如果取消关注了是不是可以用DECR
# 浮点数操作: 自动先转为数字类型，然后再进行相加减，不能使用append
incrbyfloat key step

```

**键的命名规范**

​	mset  wang:email  wangweichao@tedu.cn

```python
127.0.0.1:6379> mset wang:email wangweichao@tedu.cn guo:email guods@tedu.cn
OK
127.0.0.1:6379> mget wang:email guo:email
1) "wangweichao@tedu.cn"
2) "guods@tedu.cn"
127.0.0.1:6379> 

```

**string命令汇总**

```python
# 字符串操作
1、set key value
2、set key value nx
3、get key
3、mset key1 value1 key2 value2
4、mget key1 key2 key3
5、set key value nx ex seconds
6、strlen key 
# 返回旧值并设置新值（如果键不存在，就创建并赋值）
7、getset key value
# 数字操作
7、incrby key 步长
8、decrby key 步长
9、incr key
10、decr key
11、incrbyfloat key number#(可为正数或负数)
# 设置过期时间的两种方式
# 方式一
1、set key value ex 3
# 方式二
1、set key value
2、expire key 5 # 秒
3、pexpire key 5 # 毫秒
# 查看存活时间
ttl key
# 删除过期
persist key

```

- **string数据类型注意**

```python
# key值取值原则
1、key值不宜过长，消耗内存，且在数据中查找这类键值的计算成本高
2、不宜过短，可读性较差
# 值
1、一个字符串类型的值最多能存储512M内容

```

**练习**

```python
1、查看 db0 库中所有的键
  # select 0
  # keys *
2、设置键 trill:username 对应的值为 user001，并查看
  # set trill:username user001
3、获取 trill:username 值的长度
  # strlen trill:username
4、一次性设置 trill:password 、trill:gender、trill:fansnumber 并查看（值自定义）
  # mset trill:password 123 trill:gender M trill:fansnumber 500                   
5、查看键 trill:score 是否存在
  # exists trill:score
6、增加10个粉丝
  # incrby trill:fansnumber 10
7、增加2个粉丝（一个一个加）
  # incr trill:fansnumber
  # incr trill:fansnumber
8、有3个粉丝取消关注你了
  # decrby trill:fansnumber 3
9、又有1个粉丝取消关注你了
  # decr trill:fansnumber
10、思考、思考、思考...,清除当前库
  # flushdb
11、一万个思考之后，清除所有库
  # flushall

```

## **列表数据类型（List）**

- **特点**

```python
1、元素是字符串类型
2、列表头尾增删快，中间增删慢，增删元素是常态
3、元素可重复
4、最多可包含2^32 -1个元素
5、索引同python列表

```

- **列表常用命令**

```python
# 增
1、从列表头部压入元素
	LPUSH key value1 value2 
2、从列表尾部压入元素
	RPUSH key value1 value2
3、从列表src尾部弹出1个元素,压入到列表dst的头部
	RPOPLPUSH src dst
4、在列表指定元素后/前插入元素
	LINSERT key after|before value newvalue

# 查
5、查看列表中元素
	LRANGE key start stop
  # 查看列表中所有元素: LRANGE key 0 -1
6、获取列表长度
	LLEN key

# 删
7、从列表头部弹出1个元素
	LPOP key
8、从列表尾部弹出1个元素
	RPOP key
9、列表头部,阻塞弹出,列表为空时阻塞
	BLPOP key timeout
10、列表尾部,阻塞弹出,列表为空时阻塞
	BRPOP key timeout
  # 关于BLPOP 和 BRPOP
  	1、如果弹出的列表不存在或者为空，就会阻塞
		2、超时时间设置为0，就是永久阻塞，直到有数据可以弹出
		3、如果多个客户端阻塞再同一个列表上，使用First In First Service原则，先到先服务
11、删除指定元素
	LREM key count value
  count>0：表示从头部开始向表尾搜索，移除与value相等的元素，数量为count
	count<0：表示从尾部开始向表头搜索，移除与value相等的元素，数量为count
	count=0：移除表中所有与value相等的值
12、保留指定范围内的元素
	LTRIM key start stop
  LRTIM mylist1 0 2 # 只保留前3条
  # 应用场景: 保存微博评论最后500条
  LTRIM weibo:comments 0 499

# 改
13、LSET key index newvalue

```

**练习**

```python
1、查看所有的键
  # keys *
2、向列表 spider:urls 中以RPUSH放入如下几个元素：01_baidu.com、02_taobao.com、03_sina.com、04_jd.com、05_xxx.com
  # RPUSH spider:urls 01_xxx 02_xxx 03_xxx
3、查看列表中所有元素
  # LRANGE spider:urls 0 -1
4、查看列表长度
  # LLEN spider:urls
5、将列表中01_baidu.com 改为 01_tmall.com
  # LSET spider:urls 0 01_tmall.com
6、在列表中04_jd.com之后再加1个元素 02_taobao.com
  # LINSERT spider:urls after 04_jd.com 02_taobao.com
7、弹出列表中的最后一个元素
  # RPOP spider:urls
8、删除列表中所有的 02_taobao.com
  # LREM spider:urls 0 02_taobao.com
9、剔除列表中的其他元素，只剩前3条
  # LTRIM spider:urls 0 2

```

## **与python交互**

- **模块(redis)**

Ubuntu

```python
sudo pip3 install redis

```

Windows

```python
# 方法1. python -m pip install redis
# 方法2. 以管理员身份打开cmd命令行
        pip install redis

```

- **使用流程**

```python
import redis
# 创建数据库连接对象
r = redis.Redis(host='127.0.0.1',port=6379,db=0,password='123456')

```

- **通用命令代码示例**

```python
import redis

# 创建连接对象
r = redis.Redis(host='192.168.153.146',port=6379,db=0)

# r.keys('*') -> 列表
key_list = r.keys('*')
for key in key_list:
  print(key.decode())

# b'list'
print(r.type('mylist'))
# 返回值: 0 或者 1
print(r.exists('spider:urls'))
# 删除key
r.delete('mylist2')

```

- **python操作list**

```python
import redis

r = redis.Redis(host='192.168.153.146',port=6379,db=0)

# pylist: ['pythonweb','socket','pybase']
r.lpush('pylist','pybase','socket','pythonweb')
# pylist: ['spider','pythonweb','socket','pybase']
r.linsert('pylist','before','pythonweb','spider')
# 4
print(r.llen('pylist'))
# [b'spider', b'pythonweb', b'socket', b'pybase']
print(r.lrange('pylist',0,-1))
# b'pybase'
print(r.rpop('pylist'))
# [b'spider', b'pythonweb']
r.ltrim('pylist',0,1)

while True:
  # 如果列表中为空时,则返回None
  result = r.brpop('pylist',1)
  if result:
    print(result)
  else:
    break


r.delete('pylist')

```

**list案例: 一个进程负责生产url，一个进程负责消费url**

进程1: 生产者

```python
import redis
import time
import random

r = redis.Redis(host='192.168.153.146',port=6379,db=0)

# 生产者开始生产url地址
for page in range(0,67):
  url = 'http://app.mi.com/category/2#page=%s' % str(page)
  r.lpush('spider:urls',url)
  time.sleep(random.randint(1,3))

```

进程2: 消费者

```python
import redis

r = redis.Redis(host='192.168.153.146',port=6379,db=0)

while True:
  # url: (b'spider:urls',b'http://xiaomixxx')
  url = r.brpop('spider:urls',5)
  if url:
    print('正在抓取:',url[1].decode())
  else:
    print('抓取结束')
    break

```

使用进程模块来实现试试？

```python
import redis
import time
import random
from multiprocessing import Process

class Spider(object):
  def __init__(self):
  	r = redis.Redis(host='192.168.153.146',port=6379,db=0)

  def product(self):
    # 生产者开始生产url地址
    for page in range(0,67):
      url = 'http://app.mi.com/category/2#page=%s' % str(page)
      self.r.lpush('spider:urls',url)
      time.sleep(random.randint(1,3))

  def consumer(self):
    while True:
      # url: (b'spider:urls',b'http://xiaomixxx')
      url = self.r.brpop('spider:urls', 5)
      if url:
        print('正在抓取:', url[1].decode())
      else:
        print('抓取结束')
        break

  def run(self):
    p1 = Process(target=self.product)
    p2 = Process(target=self.consumer)
    p1.start()
    p2.start()
    p1.join()
    p2.join()

if __name__ == '__main__':
  spider = Spider()
  spider.run()

```

- **python操作string**

```python
import redis

r = redis.Redis(host='192.168.153.146',port=6379,db=0)

r.set('username','guods')
print(r.get('username'))
# mset参数为字典
r.mset({'username':'xiaoze','password':'123456'})
# 列表: [b'xiaoze', b'123456']
print(r.mget('username','password'))
# 6
print(r.strlen('username'))

# 数值操作
r.set('age','25')
r.incrby('age',10)
r.decrby('age',10)
r.incr('age')
r.decr('age')
r.incrbyfloat('age',3.3)
r.incrbyfloat('age',-3.3)
print(r.get('age'))

r.delete('username')

```


## **位图操作bitmap**

**定义**

```python
1、位图不是真正的数据类型，它是定义在字符串类型中
2、一个字符串类型的值最多能存储512M字节的内容，位上限：2^32
# 1MB = 1024KB
# 1KB = 1024Byte(字节)
# 1Byte = 8bit(位)
```

**强势点**

```python
可以实时的进行统计，极其节省空间。官方在模拟1亿2千8百万用户的模拟环境下，在一台MacBookPro上，典型的统计如“日用户数”的时间消耗小于50ms, 占用16MB内存
```

**设置某一位上的值（setbit）**

```python
# 设置某一位上的值（offset是偏移量，从0开始）
setbit key offset value
# 获取某一位上的值
GETBIT key offset
# 统计键所对应的值中有多少个 1 
BITCOUNT key
```

**示例**

```python
# 默认扩展位以0填充
127.0.0.1:6379> set mykey ab
OK
127.0.0.1:6379> get mykey
"ab"
127.0.0.1:6379> SETBIT mykey 0 1
(integer) 0
127.0.0.1:6379> get mykey
"\xe1b"
127.0.0.1:6379> 
```

**获取某一位上的值**

GETBIT key offset

```python
127.0.0.1:6379> GETBIT mykey 3
(integer) 0
127.0.0.1:6379> GETBIT mykey 0
(integer) 1
127.0.0.1:6379> 
```

**bitcount**

统计键所对应的值中有多少个 1 

```python
127.0.0.1:6379> SETBIT user001 1 1
(integer) 0
127.0.0.1:6379> SETBIT user001 30 1
(integer) 0
127.0.0.1:6379> bitcount user001
(integer) 2
127.0.0.1:6379> 
```

**应用场景案例**

```python
# 网站用户的上线次数统计（寻找活跃用户）
	用户名为key，上线的天作为offset，上线设置为1
# 示例
	用户名为 user1:login 的用户，今年第1天上线，第30天上线
	SETBIT user1:login 0 1 
	SETBIT user1:login 29 1
	BITCOUNT user1:login
```

**代码实现**

```python
import redis

r = redis.Redis(host='127.0.0.1',port=6379,db=0)

# user1: 一年中第5天和200天登录
r.setbit('user1',4,1)
r.setbit('user1',199,1)
# user2: 一年中第100天和第300天登录
r.setbit('user2',99,1)
r.setbit('user2',299,1)
# user3: 登录了100次以上
for i in range(1,366,2):
  r.setbit('user3',i,1)
# user4: 登录了100次以上
for i in range(1,366,3):
  r.setbit('user4',i,1)

user_list = r.keys('user*')

# 存放活跃用户列表
active_users = []
# 存放不活跃用户列表
no_active_users = []

for user in user_list:
  login_count = r.bitcount(user)
  if login_count >= 100:
    active_users.append((user,login_count))
  else:
    no_active_users.append((user,login_count))

print('活跃用户:',active_users)
print('不活跃用户:',no_active_users)
```

## **Hash散列数据类型**

- **定义**

```python
1、由field和关联的value组成的键值对
2、field和value是字符串类型
3、一个hash中最多包含2^32-1个键值对
```

- **优点**

```python
1、节约内存空间
2、每创建一个键，它都会为这个键储存一些附加的管理信息（比如这个键的类型，这个键最后一次被访问的时间等）
3、键越多，redis数据库在储存附件管理信息方面耗费内存越多，花在管理数据库键上的CPU也会越多
```

- **缺点（不适合hash情况）**

```python
1、使用二进制位操作命令:SETBIT、GETBIT、BITCOUNT等，如果想使用这些操作，只能用字符串键
2、使用过期键功能：键过期功能只能对键进行过期操作，而不能对散列的字段进行过期操作
```

- **基本命令操作**

```python
# 1、设置单个字段
HSET key field value
HSETNX key field value
# 2、设置多个字段
HMSET key field value field value
# 3、返回字段个数
HLEN key
# 4、判断字段是否存在（不存在返回0）
HEXISTS key field
# 5、返回字段值
HGET key field
# 6、返回多个字段值
HMGET key field filed
# 7、返回所有的键值对
HGETALL key
# 8、返回所有字段名
HKEYS key
# 9、返回所有值
HVALS key
# 10、删除指定字段
HDEL key field 
# 11、在字段对应值上进行整数增量运算
HINCRBY key filed increment
# 12、在字段对应值上进行浮点数增量运算
HINCRBYFLOAT key field increment
```

**Hash与python交互**

```python
# 1、更新一条数据的属性，没有则新建
hset(name, key, value) 
# 2、读取这条数据的指定属性， 返回字符串类型
hget(name, key)
# 3、批量更新数据（没有则新建）属性,参数为字典
hmset(name, mapping)
# 4、批量读取数据（没有则新建）属性
hmget(name, keys)
# 5、获取这条数据的所有属性和对应的值，返回字典类型
hgetall(name)
# 6、获取这条数据的所有属性名，返回列表类型
hkeys(name)
# 7、删除这条数据的指定属性
hdel(name, *keys)
```

**Python代码hash散列**

```python
'''设置1个字段,更改1个字段,设置多个字段,获取相关信息'''
import redis

r = redis.Redis(host='127.0.0.1',port=6379,db=0)
# 设置
r.hset('user1','name','bujingyun')
# 更新
r.hset('user1','name','kongci')
# 取数据
print(r.hget('user1','name'))
# 一次设置多个field和value
user_dict = {
  'password':'123456',
  'gender':'F',
  'height':'165'
}
r.hmset('user1',user_dict)
# 获取所有数据,字典
print(r.hgetall('user1'))

# 获取所有fields和所有values
print(r.hkeys('user1'))
print(r.hvals('user1'))
```

**应用场景：微博好友关注**

```python
1、用户ID为key，Field为好友ID，Value为关注时间
       key       field    value
	 user:10000   user:606 20190520
	              user:605 20190521
2、用户维度统计
   统计数包括：关注数、粉丝数、喜欢商品数、发帖数
   用户为key，不同维度为field，value为统计数
   比如关注了5人
	 HSET user:10000 fans 5
	 HINCRBY user:10000 fans 1
```

**应用场景: redis+mysql+hash组合使用**

- **原理**

  ```python
  用户想要查询个人信息
  1、到redis缓存中查询个人信息
  2、redis中查询不到，到mysql查询，并缓存到redis
  3、再次查询个人信息
  ```

- **代码实现**

  ```python
  import redis
  import pymysql
  
  # 1. 先到redis中查询
  # 2. redis中没有,到mysql查询,缓存到redis(设置过期时间)
  # 3. 再查询1次
  r = redis.Redis(host='127.0.0.1',port=6379,db=0)
  username = input('请输入用户名:')
  
  result = r.hgetall('user')
  if result:
    print(result)
  else:
    # redis中没有缓存,需要到mysql中查询
    db = pymysql.connect(
      host='localhost',
      user='root',
      password='123456',
      database='userdb',
      charset='utf8'
    )
    cursor = db.cursor()
    sele = 'select username,age,gender from user where username=%s'
    cursor.execute(sele,[username])
    # userinfo: (('guoxiaonao',36,'M'),)
    userinfo = cursor.fetchall()
    if not userinfo:
      print('用户不存在')
    else:
      # 打印输出
      print('mysql',userinfo)
      # 缓存到redis
      user_dict = {
        'username':userinfo[0][0],
        'age':userinfo[0][1],
        'gender':userinfo[0][2]
      }
      r.hmset('user',user_dict)
      # 设置过期时间
      r.expire('user',30)
  ```


**mysql数据库中数据更新信息后同步到redis缓存**

用户修改个人信息时，要将数据同步到redis缓存

```python
'''update数据(mysql)后,同步到redis缓存'''
import redis
import pymysql


def update_mysql(age,username):
  db = pymysql.connect('127.0.0.1','root','123456','userdb',charset='utf8')
  cursor = db.cursor()
  upd = 'update user set age=%s where username=%s'
  try:
    # code: 0 或者 1
    code = cursor.execute(upd,[age,username])
    db.commit()
    if code == 1:
      return True

  except Exception as e:
    db.rollback()
    print('Error',e)
  cursor.close()
  db.close()


def update_redis(age):
  r = redis.Redis(host='127.0.0.1',port=6379,db=0)
  r.hset('user','age',age)
  print('已同步到redis')
  # 设置过期时间
  r.expire('user',30)
  # 测试
  print(r.hget('user','age'))


if __name__ == '__main__':
  username = input('请输入用户名:')
  age = input('请输入更改后的年龄:')
  if update_mysql(age,username):
    update_redis(age)
  else:
    print('用户名有误')
```

## **集合数据类型（set）**

- **特点**

```python
1、无序、去重
2、元素是字符串类型
3、最多包含2^32-1个元素
```

- **基本命令**

```python
# 1、增加一个或者多个元素,自动去重
SADD key member1 member2
# 2、查看集合中所有元素
SMEMBERS key
# 3、删除一个或者多个元素，元素不存在自动忽略
SREM key member1 member2
# 4、元素是否存在
SISMEMBER key member
# 5、随机返回集合中指定个数的元素，默认为1个
SRANDMEMBER key [count]
# 6、弹出成员
SPOP key [count]
# 7、返回集合中元素的个数，不会遍历整个集合，只是存储在键当中了
SCARD key
# 8、把元素从源集合移动到目标集合
SMOVE source destination member

# 9、差集(number1 1 2 3 number2 1 2 4 结果为3)
SDIFF key1 key2 
# 10、差集保存到另一个集合中
SDIFFSTORE destination key1 key2

# 11、交集
SINTER key1 key2
SINTERSTORE destination key1 key2

# 11、并集
SUNION key1 key2
SUNIONSTORE destination key1 key2
```

**案例: 新浪微博的共同关注**

```python
# 需求: 当用户访问另一个用户的时候，会显示出两个用户共同关注过哪些相同的用户
# 设计: 将每个用户关注的用户放在集合中，求交集即可
# 实现:
	user001 = {'peiqi','qiaozhi','danni'}
	user002 = {'peiqi','qiaozhi','lingyang'}
  
user001和user002的共同关注为:
	SINTER user001 user002
	结果为: {'peiqi','qiaozhi'}
```

**python操作set**

```python
# 1、给name对应的集合中添加元素
sadd(name,values)
r.sadd("set_name","tom")
r.sadd("set_name","tom","jim")

# 2、获取name对应的集合的所有成员: python集合
smembers(name)
r.smembers('set_name')

# 3、获取name对应的集合中的元素个数
scard(name)
r.scard("set_name")

# 4、检查value是否是name对应的集合内的元素:True|False
sismember(name, value)
r.sismember('set_name','tom')

# 5、随机删除并返回指定集合的一个元素
spop(name)
member = r.spop('set_name')

# 6、删除集合中的某个元素
srem(name, value) 
r.srem("set_name", "tom")

# 7、获取多个name对应集合的交集
sinter(keys, *args)

r.sadd("set_name","a","b")
r.sadd("set_name1","b","c")
r.sadd("set_name2","b","c","d")

print(r.sinter("set_name","set_name1","set_name2"))
#输出:｛b'b'｝

# 8、获取多个name对应的集合的并集: python集合
sunion(keys, *args)
r.sunion("set_name","set_name1","set_name2")
```

**python代码实现微博关注**

```python
import redis

r = redis.Redis(host='127.0.0.1',port=6379,db=0)

# user1关注的人
r.sadd('user1:focus','peiqi','qiaozhi','danni')
# user2关注的人
r.sadd('user2:focus','peiqi','qiaozhi','lingyang')
# 共同关注: 求交集 {b'qiaozhi', b'peiqi'}
focus_set = r.sinter('user1:focus','user2:focus')

# 创建空集合,存放最终结果
result = set()

for focus in focus_set:
  result.add(focus.decode())

print(result)
```

## **有序集合sortedset**

- **特点**

```
1、有序、去重
2、元素是字符串类型
3、每个元素都关联着一个浮点数分值(score)，并按照分值从小到大的顺序排列集合中的元素（分值可以相同）
4、最多包含2^32-1元素
```

- 示例

  **一个保存了水果价格的有序集合**

| 分值 | 2.0  | 4.0  | 6.0  | 8.0  | 10.0 |
| ---- | ---- | ---- | ---- | ---- | ---- |
| 元素 | 西瓜 | 葡萄 | 芒果 | 香蕉 | 苹果 |

	**一个保存了员工薪水的有序集合**

| 分值 | 6000 | 8000 | 10000 | 12000 |      |
| ---- | ---- | ---- | ----- | ----- | ---- |
| 元素 | lucy | tom  | jim   | jack  |      |

	**一个保存了正在阅读某些技术书的人数**

| 分值 | 300      | 400    | 555    | 666        | 777      |
| ---- | -------- | ------ | ------ | ---------- | -------- |
| 元素 | 核心编程 | 阿凡提 | 本拉登 | 阿姆斯特朗 | 比尔盖茨 |

- **有序集合常用命令**

```python
# 在有序集合中添加一个成员
zadd key score member
# 查看指定区间元素（升序)
zrange key start stop [withscores]
# 查看指定区间元素（降序）
ZREVRANGE key start stop [withscores]
# 查看指定元素的分值
ZSCORE key member

# 返回指定区间元素
# offset : 跳过多少个元素
# count : 返回几个
# 小括号 : 开区间  zrangebyscore fruits (2.0 8.0
zrangebyscore key min max [withscores] [limit offset count]
# 每页显示10个成员,显示第5页的成员信息: 
# limit 40 10
# MySQL: 每页显示10条记录,显示第5页的记录
# limit 40,10
# limit 2,3   显示: 第3 4 5条记录

# 删除成员
zrem key member
# 增加或者减少分值
zincrby key increment member
# 返回元素排名
zrank key member
# 返回元素逆序排名
zrevrank key member
# 删除指定区间内的元素
zremrangebyscore key min max
# 返回集合中元素个数
zcard key
# 返回指定范围中元素的个数
zcount key min max
zcount salary 6000 8000 
zcount salary (6000 8000# 6000<salary<=8000
zcount salary (6000 (8000#6000<salary<8000               
# 并集
zunionstore destination numkeys key [weights 权重值] [AGGREGATE SUM|MIN|MAX]
# zunionstore salary3 2 salary salary2 weights 1 0.5 AGGREGATE MAX
# 2代表集合数量,weights之后 权重1给salary,权重0.5给salary2集合,算完权重之后执行聚合AGGREGATE
                     
# 交集：和并集类似，只取相同的元素
ZINTERSTORE destination numkeys key1 key2 WEIGHTS weight AGGREGATE SUM(默认)|MIN|MAX
```

**python操作sorted set**

```python
import redis

r = redis.Redis(host='192.168.43.49',port=6379,password='123456',db=0)
# 注意第二个参数为字典
# 命令行:ZADD salary 6000 tom 8000 jim
r.zadd('salary',{'tom':6000,'jim':8000,'jack':12000})
# 结果为列表中存放元组[(),(),()]
print(r.zrange('salary',0,-1,withscores=True))
print(r.zrevrange('salary',0,-1,withscores=True))
# start:起始值,num:显示条数
print(r.zrangebyscore('salary',6000,12000,start=1,num=2,withscores=True))
# 删除
r.zrem('salary','tom')
print(r.zrange('salary',0,-1,withscores=True))
# 增加分值
r.zincrby('salary',5000,'jack')
print(r.zrange('salary',0,-1,withscores=True))
# 返回元素排名
print(r.zrank('salary','jack'))
print(r.zrevrank('salary','jack'))
# 删除指定区间内的元素
r.zremrangebyscore('salary',6000,8000)
print(r.zrange('salary',0,-1,withscores=True))
# 统计元素个数
print(r.zcard('salary'))
# 返回指定范围内元素个数
print(r.zcount('salary',6000,20000))
# 并集
r.zadd('salary2',{'jack':17000,'lucy':8000})
r.zunionstore('salary3',('salary','salary2'),aggregate='max')
print(r.zrange('salary3',0,-1,withscores=True))
# 交集
r.zinterstore('salary4',('salary','salary2'),aggregate='max')
print(r.zrange('salary4',0,-1,withscores=True))
```

## **有序集合sortedset**

**有序集合的交集与并集**

```python
# 交集（weights代表权重值，aggregate代表聚合方式 - 先计算权重值，然后再聚合）
ZINTERSTORE destination numkeys key1 key2 WEIGHTS weight AGGREGATE SUM|MIN|MAX
# 并集（weights代表权重值，aggregate代表聚合方式 - 先计算权重值，然后再聚合）
ZUNIONSTORE destination numkeys key [weights 权重值] [AGGREGATE SUM|MIN|MAX]
```

**案例1：网易音乐排行榜**

```python
1、每首歌的歌名作为元素
2、每首歌的播放次数作为分值
3、使用ZREVRANGE来获取播放次数最多的歌曲
```

**代码实现**

```python
import redis

r = redis.Redis(host='127.0.0.1',port=6379,db=0)

# 有序集合中添加了8首歌曲
r.zadd('ranking',{'song1':1,'song2':1,'song3':1,'song4':1})
r.zadd('ranking',{'song5':1,'song6':1,'song7':1,'song8':1})
# 指定成员增加分值
r.zincrby('ranking',50,'song3')
r.zincrby('ranking',60,'song4')
r.zincrby('ranking',70,'song8')
# 获取前3名: [('song8',71),(),()]
rlist = r.zrevrange('ranking',0,2,withscores=True)

i = 1
for name in rlist:
  print('第{}名:{} 播放次数:{}'.format(
    i,
    name[0].decode(),
    int(name[1])
  ))
  i += 1

  # 第1名:song8 播放次数:71
  # 第2名:song4 播放次数:61
  # 第3名:song3 播放次数:51
```

**案例2: 京东商品畅销榜**

```python
# 第1天
ZADD mobile-001 5000 'huawei' 4000 'oppo' 3000 'iphone'
# 第2天
ZADD mobile-002 5200 'huawei' 4300 'oppo' 3230 'iphone'
# 第3天
ZADD mobile-003 5500 'huawei' 4660 'oppo' 3580 'iphone'
问题：如何获取三款手机的销量排名？
ZUNIONSTORE mobile-001:003 mobile-001 mobile-002 mobile-003 # 可否？
# 正确
方法1: ZRANGE mobile-003 0 -1 WITHSCORES
方法2: ZUNIONSTORE mobile-001:003 mobile-001 mobile-002 mobile-003 AGGREGATE MAX
```

**python代码实现**

```python
import redis

r = redis.Redis(host='127.0.0.1',port=6379,db=0)

day01_dict = {
  'huawei':5000,
  'oppo':4000,
  'iphone':3000
}
day02_dict = {
  'huawei':5200,
  'oppo':4300,
  'iphone':3230
}
day03_dict = {
  'huawei':5500,
  'oppo':4400,
  'iphone':3600
}

r.zadd('mobile-001',day01_dict)
r.zadd('mobile-002',day02_dict)
r.zadd('mobile-003',day03_dict)

# 并集,第二个参数为元组
r.zunionstore(
  'mobile-001:003',
  ('mobile-001','mobile-002','mobile-003'),
  aggregate='max'
)
# 逆序:[(),(),()]
rlist = r.zrevrange('mobile-001:003',0,2,withscores=True)

for r in rlist:
  print('{}-{}'.format(r[0].decode(),int(r[1])))
```

## **数据持久化**

**持久化定义**

```python
将数据从掉电易失的内存放到永久存储的设备上
```

**为什么需要持久化**

```python
因为所有的数据都在内存上，所以必须得持久化
```

- **数据持久化分类之 - RDB模式（默认开启）**

**默认模式**

```python
1、保存真实的数据
2、将服务器包含的所有数据库数据以二进制文件的形式保存到硬盘里面
3、默认文件名 ：/var/lib/redis/dump.rdb
```

**创建rdb文件的两种方式**

**方式一：**服务器执行客户端发送的SAVE或者BGSAVE命令

```python
127.0.0.1:6379> SAVE
OK
# 特点
1、执行SAVE命令过程中，redis服务器将被阻塞，无法处理客户端发送的命令请求，在SAVE命令执行完毕后，服务器才会重新开始处理客户端发送的命令请求
2、如果RDB文件已经存在，那么服务器将自动使用新的RDB文件代替旧的RDB文件
# 工作中定时持久化保存一个文件

127.0.0.1:6379> BGSAVE
Background saving started
# 执行过程如下
1、客户端 发送 BGSAVE 给服务器
2、服务器马上返回 Background saving started 给客户端
3、服务器 fork() 子进程做这件事情
4、服务器继续提供服务
5、子进程创建完RDB文件后再告知Redis服务器

# 配置文件相关操作
/etc/redis/redis.conf
263行: dir /var/lib/redis # 表示rdb文件存放路径
253行: dbfilename dump.rdb  # 文件名

# 两个命令比较
SAVE比BGSAVE快，因为需要创建子进程，消耗额外的内存

# 补充：可以通过查看日志文件来查看redis都做了哪些操作
# 日志文件：配置文件中搜索 logfile
logfile /var/log/redis/redis-server.log
```

**方式二：**设置配置文件条件满足时自动保存**（使用最多）**

```python
# 命令行示例
redis>save 300 10
  表示如果距离上一次创建RDB文件已经过去了300秒，并且服务器的所有数据库总共已经发生了不少于10次修改，那么自动执行BGSAVE命令
redis>save 60 10000
  表示如果距离上一次创建rdb文件已经过去60秒，并且服务器所有数据库总共已经发生了不少于10000次修改，那么执行bgsave命令

# redis配置文件默认
218行: save 900 1
219行: save 300 10
220行: save 60 10000
  1、只要三个条件中的任意一个被满足时，服务器就会自动执行BGSAVE
  2、每次创建RDB文件之后，服务器为实现自动持久化而设置的时间计数器和次数计数器就会被清零，并重新开始计数，所以多个保存条件的效果不会叠加
```

- **数据持久化分类之 - AOF（AppendOnlyFile，默认未开启）**

**特点**

```python
1、存储的是命令，而不是真实数据
2、默认不开启
# 开启方式（修改配置文件）
1、/etc/redis/redis.conf
  672行: appendonly yes # 把 no 改为 yes
  676行: appendfilename "appendonly.aof"
2、重启服务
  sudo /etc/init.d/redis-server restart
```

**RDB缺点**

```python
1、创建RDB文件需要将服务器所有的数据库的数据都保存起来，这是一个非常消耗资源和时间的操作，所以服务器需要隔一段时间才创建一个新的RDB文件，也就是说，创建RDB文件不能执行的过于频繁，否则会严重影响服务器的性能
2、可能丢失数据
```

**AOF持久化原理及优点**

```python
# 原理
   1、每当有修改数据库的命令被执行时，服务器就会将执行的命令写入到AOF文件的末尾
   2、因为AOF文件里面存储了服务器执行过的所有数据库修改的命令，所以给定一个AOF文件，服务器只要重新执行一遍AOF文件里面包含的所有命令，就可以达到还原数据库的目的

# 优点
  用户可以根据自己的需要对AOF持久化进行调整，让Redis在遭遇意外停机时不丢失任何数据，或者只丢失一秒钟的数据，这比RDB持久化丢失的数据要少的多
```

**安全性问题考虑**

```python
# 因为
  虽然服务器执行一个修改数据库的命令，就会把执行的命令写入到AOF文件，但这并不意味着AOF文件持久化不会丢失任何数据，在目前常见的操作系统中，执行系统调用write函数，将一些内容写入到某个文件里面时，为了提高效率，系统通常不会直接将内容写入硬盘里面，而是将内容放入一个内存缓存区（buffer）里面，等到缓冲区被填满时才将存储在缓冲区里面的内容真正写入到硬盘里

# 所以
  1、AOF持久化：当一条命令真正的被写入到硬盘里面时，这条命令才不会因为停机而意外丢失
  2、AOF持久化在遭遇停机时丢失命令的数量，取决于命令被写入到硬盘的时间
  3、越早将命令写入到硬盘，发生意外停机时丢失的数据就越少，反之亦然
```

**策略 - 配置文件**

```python
# 打开配置文件:/etc/redis/redis.conf，找到相关策略如下
1、701行: alwarys
   服务器每写入一条命令，就将缓冲区里面的命令写入到硬盘里面，服务器就算意外停机，也不会丢失任何已经成功执行的命令数据
2、702行: everysec（# 默认）
   服务器每一秒将缓冲区里面的命令写入到硬盘里面，这种模式下，服务器即使遭遇意外停机，最多只丢失1秒的数据
3、703行: no
   服务器不主动将命令写入硬盘,由操作系统决定何时将缓冲区里面的命令写入到硬盘里面，丢失命令数量不确定

# 运行速度比较
always：速度慢
everysec和no都很快，默认值为everysec
```

**AOF文件中是否会产生很多的冗余命令？**

```python
为了让AOF文件的大小控制在合理范围，避免胡乱增长，redis提供了AOF重写功能，通过这个功能，服务器可以产生一个新的AOF文件
  -- 新的AOF文件记录的数据库数据和原由的AOF文件记录的数据库数据完全一样
  -- 新的AOF文件会使用尽可能少的命令来记录数据库数据，因此新的AOF文件的提及通常会小很多
  -- AOF重写期间，服务器不会被阻塞，可以正常处理客户端发送的命令请求
```

**示例**

| 原有AOF文件                | 重写后的AOF文件                         |
| -------------------------- | --------------------------------------- |
| select 0                   | SELECT 0                                |
| sadd myset peiqi           | SADD myset peiqi qiaozhi danni lingyang |
| sadd myset qiaozhi         | SET msg 'hello tarena'                  |
| sadd myset danni           | RPUSH mylist 2 3 5                      |
| sadd myset lingyang        |                                         |
| INCR number                |                                         |
| INCR number                |                                         |
| DEL number                 |                                         |
| SET message 'hello world'  |                                         |
| SET message 'hello tarena' |                                         |
| RPUSH mylist 1 2 3         |                                         |
| RPUSH mylist 5             |                                         |
| LPOP mylist                |                                         |

**AOF文件重写方法触发**

```python
1、客户端向服务器发送BGREWRITEAOF命令
   127.0.0.1:6379> BGREWRITEAOF
   Background append only file rewriting started

2、修改配置文件让服务器自动执行BGREWRITEAOF命令
  auto-aof-rewrite-percentage 100
  auto-aof-rewrite-min-size 64mb
  # 解释
    1、只有当AOF文件的增量大于100%时才进行重写，也就是大一倍的时候才触发
        # 第一次重写新增：64M
        # 第二次重写新增：128M
        # 第三次重写新增：256M（新增128M）
```



**RDB和AOF持久化对比**

| RDB持久化                                                    | AOF持久化                                                    |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 全量备份，一次保存整个数据库                                 | 增量备份，一次保存一个修改数据库的命令                       |
| 保存的间隔较长                                               | 保存的间隔默认为一秒钟                                       |
| 数据还原速度快                                               | 数据还原速度一般，冗余命令多，还原速度慢                     |
| 执行SAVE命令时会阻塞服务器，但手动或者自动触发的BGSAVE不会阻塞服务器 | 无论是平时还是进行AOF重写时，都不会阻塞服务器                |
| 更适合数据备份                                               | 更适合用来保存数据，通常意义上的数据持久化，在appendfsync always模式下运行 |

```python
# 用redis用来存储真正数据，每一条都不能丢失，都要用always，有的做缓存，有的保存真数据，我可以开多个redis服务，不同业务使用不同的持久化，新浪每个服务器上有4个redis服务，整个业务中有上千个redis服务，分不同的业务，每个持久化的级别都是不一样的。
```

**数据恢复（无需手动操作）**

```python
既有dump.rdb，又有appendonly.aof，恢复时找谁？
先找appendonly.aof
```

**配置文件常用配置总结**

```python
# 设置密码
1、requirepass password
# 开启远程连接
2、bind 127.0.0.1 ::1 注释掉
3、protected-mode no  把默认的 yes 改为 no
# rdb持久化-默认配置
4、dbfilename 'dump.rdb'
5、dir /var/lib/redis
# rdb持久化-自动触发(条件)
6、save 900 1
7、save 300 10 
8、save 60  10000
# aof持久化开启
9、appendonly yes
10、appendfilename 'appendonly.aof'
# aof持久化策略
11、appendfsync always
12、appendfsync everysec # 默认
13、appendfsync no
# aof重写触发
14、auto-aof-rewrite-percentage 100
15、auto-aof-rewrite-min-size 64mb
# 设置为从服务器
16、salveof <master-ip> <master-port>
```

**Redis相关文件存放路径**

```python
1、配置文件: /etc/redis/redis.conf
2、备份文件: /var/lib/redis/*.rdb|*.aof
3、日志文件: /var/log/redis/redis-server.log
4、启动文件: /etc/init.d/redis-server
# /etc/下存放配置文件
# /etc/init.d/下存放服务启动文件
```



## **Redis主从复制**

- **定义**

```python
1、一个Redis服务可以有多个该服务的复制品，这个Redis服务成为master，其他复制品成为slaves
2、master会一直将自己的数据更新同步给slaves，保持主从同步
3、只有master可以执行写命令，slave只能执行读命令
```

- **作用**

```python
分担了读的压力（高并发）
```

- **原理**

```python
从服务器执行客户端发送的读命令，比如GET、LRANGE、SMEMMBERS、HGET、ZRANGE等等，客户端可以连接slaves执行读请求，来降低master的读压力
```

- **两种实现方式**

  **方式一**（Linux命令行实现1）

  redis-server --slaveof <master-ip> <master-port>

```python
# 从服务端
redis-server --port 6300 --slaveof 127.0.0.1 6379
# 从客户端
redis-cli -p 6300
127.0.0.1:6300> keys * 
# 发现是复制了原6379端口的redis中数据
127.0.0.1:6300> set mykey 123
(error) READONLY You can't write against a read only slave.
127.0.0.1:6300> 
# 从服务器只能读数据，不能写数据
```

**方式一**（Redis命令行实现2）

```python
# 两条命令
1、>slaveof IP PORT
2、>slaveof no one
```

**示例**

```python
# 服务端启动
redis-server --port 6301
# 客户端连接
tarena@tedu:~$ redis-cli -p 6301
127.0.0.1:6301> keys *
1) "myset"
2) "mylist"
127.0.0.1:6301> set mykey 123
OK
# 切换为从
127.0.0.1:6301> slaveof 127.0.0.1 6379
OK
127.0.0.1:6301> set newkey 456
(error) READONLY You can't write against a read only slave.
127.0.0.1:6301> keys *
1) "myset"
2) "mylist"
# 再切换为主
127.0.0.1:6301> slaveof no one
OK
127.0.0.1:6301> set name hello
OK
```

**方式二**(修改配置文件)

```python
# 每个redis服务,都有1个和他对应的配置文件
# 两个redis服务
  1、6379 -> /etc/redis/redis.conf
  2、6300 -> /home/tarena/redis_6300.conf

# 修改配置文件
vi redis_6300.conf
slaveof 127.0.0.1 6379
port 6300
# 启动redis服务
redis-server redis_6300.conf
# 客户端连接测试
redis-cli -p 6300
127.0.0.1:6300> hset user:1 username guods
(error) READONLY You can't write against a read only slave.
```

**问题总结：master挂了怎么办？**

```python
1、一个Master可以有多个Slaves
2、Slave下线，只是读请求的处理性能下降
3、Master下线，写请求无法执行
4、其中一台Slave使用SLAVEOF no one命令成为Master，其他Slaves执行SLAVEOF命令指向这个新的Master，从它这里同步数据
# 以上过程是手动的，能够实现自动，这就需要Sentinel哨兵，实现故障转移Failover操作
```

**演示**

```python
1、启动端口6400redis，设置为6379的slave
   redis-server --port 6400
   redis-cli -p 6400
   redis>slaveof 127.0.0.1 6379
2、启动端口6401redis，设置为6379的slave
   redis-server --port 6401
   redis-cli -p 6401
   redis>slaveof 127.0.0.1 6379
3、关闭6379redis
   sudo /etc/init.d/redis-server stop
4、把6400redis设置为master
   redis-cli -p 6401
   redis>slaveof no one
5、把6401的redis设置为6400redis的salve
   redis-cli -p 6401
   redis>slaveof 127.0.0.1 6400
# 这是手动操作，效率低，而且需要时间，有没有自动的？？？
```

## **官方高可用方案Sentinel**

**Redis之哨兵 - sentinel**

```python
1、Sentinel会不断检查Master和Slaves是否正常
2、每一个Sentinel可以监控任意多个Master和该Master下的Slaves
```

**案例演示**

	**1、**环境搭建

```python
# 共3台redis的服务器，如果是不同机器端口号可以是一样的
1、启动6379的redis服务器
   	sudo /etc/init.d/redis-server start
2、启动6380的redis服务器，设置为6379的从
    redis-server --port 6380
    tarena@tedu:~$ redis-cli -p 6380
    127.0.0.1:6380> slaveof 127.0.0.1 6379
    OK
3、启动6381的redis服务器，设置为6379的从
   	redis-server --port 6381
   	tarena@tedu:~$ redis-cli -p 6381
   	127.0.0.1:6381> slaveof 127.0.0.1 6379
```

	**2、**安装并搭建sentinel哨兵

```python
# 1、安装redis-sentinel
sudo apt install redis-sentinel
验证: sudo /etc/init.d/redis-sentinel stop
# 2、新建配置文件sentinel.conf
port 26379
Sentinel monitor tedu 127.0.0.1 6379 1

# 3、启动sentinel
方式一: redis-sentinel sentinel.conf
方式二: redis-server sentinel.conf --sentinel

#4、将master的redis服务终止，查看从是否会提升为主
sudo /etc/init.d/redis-server stop
# 发现提升6381为master，其他两个为从
# 在6381上设置新值，6380查看
127.0.0.1:6381> set name tedu
OK

# 启动6379，观察日志，发现变为了6381的从
主从+哨兵基本就够用了
```

sentinel.conf解释

```python
# sentinel监听端口，默认是26379，可以修改
port 26379
# 告诉sentinel去监听地址为ip:port的一个master，这里的master-name可以自定义，quorum是一个数字，指明当有多少个sentinel认为一个master失效时，master才算真正失效
sentinel monitor <master-name> <ip> <redis-port> <quorum>
```

**生产环境中设置哨兵sentinel**

```python
1、安装sentinel
  sudo apt-get install redis-sentinel
2、创建配置文件 sentinel.conf
  port 26379
  Sentinel monitor 名字 IP PORT 投票数
3、启动sentinel开始监控
  redis-sentinel sentinel.conf
```



## **分布式锁  **

**高并发产生的问题？**

```python
1、购票: 多个用户抢到同一张票？
2、购物: 库存只剩1个,被多个用户成功买到？
... ...
```

**怎么办？**

```python
在不同进程需要互斥地访问共享资源时，分布式锁是一种非常有用的技术手段
```

**原理**

```python
1、多个客户端先到redis数据库中获取一把锁,得到锁的用户才可以操作数据库
2、此用户操作完成后释放锁，下一个成功获取锁的用户再继续操作数据库
```

**实现**

```python
set key value nx ex 3
```

![](分布式锁.png)

## **博客项目解决高并发问题**

1、在数据库中创建库 blog，指定字符编码utf8

```mysql
mysql -uroot -p123456
mysql>create database blog charset utf8;
```

2、同步数据库，并在user_profile中插入表记录

```python
1、python3 manage.py makemigrations
2、python3 manage.py migrate
3、insert into user_profile values ('guoxiaonao','guoxiaonao','guoxiaonao@tedu.cn','123456','aaaaaaaa','bbbbbbbb','cccccccc');
```

3、启动django项目，并找到django路由测试 test函数

```python
1、python3 manage.py runserver
2、查看项目的 urls.py 路由，打开firefox浏览器输入地址：http://127.0.0.1:8000/test
# 返回结果：	HI HI HI 
```

4、在数据库表中创建测试字段score

```python
1、user/models.py添加:
   score = models.IntegerField(verbose_name=u'分数',null=True,default=0)
2、同步到数据库
   python3 manage.py makemigrations user
   python3 manage.py migrate user
3、到数据库中确认查看
```

3、在blog/views.py中补充 test函数，对数据库中score字段进行 +1 操作

```python
from user.models import UserProfile
def test(request):
    

    u = UserProfile.objects.get(username='guoxiaonao')
    u.score += 1
    u.save()

    return JsonResponse('HI HI HI')
```

4、启多个服务端，模拟30个并发请求

(1)多台服务器启动项目

```python
python3 manage.py runserver 127.0.0.1:8000
python3 manage.py runserver 127.0.0.1:8001
```

(2)在tools中新建py文件 test_api.py，模拟30个并发请求

```python
import threading
import requests
import random


def getRequest():
    url='http://127.0.0.1:8000/test'
    url2='http://127.0.0.1:8001/test'
    get_url = random.choice([url, url2])
    requests.get(get_url)

ts = []
for i in range(30):

    t=threading.Thread(target=getRequest,args=())
    ts.append(t)

if __name__ == '__main__':

    for t in ts:
        t.start()

    for t in ts:
        t.join()
```

  (3) python3 test_api.py

 (4) 在数据库中查看 score 字段的值

```python
并没有+30，而且没有规律，每次加的次数都不同，如何解决？？？
```

**解决方案：redis分布式锁**

```python
def test(request):
	# 解决方法二:redis分布式锁
    import redis
    pool = redis.ConnectionPool(host='localhost', port=6379, db=0)
    r = redis.Redis(connection_pool=pool)
    while True:
        try:
            with r.lock('guoxiaonao', blocking_timeout=3) as lock:
                u = UserProfile.objects.get(username='guoxiaonao')
                u.score += 1
                u.save()
            break
        except Exception as e:
            print('lock is failed')
    
    return HttpResponse('HI HI HI')
```

## **Redis事务**

**特点**

```python
1. 单独的隔离操作：事务中的所有命令会被序列化、按顺序执行，在执行的过程中不会被其他客户端发送来的命令打断
2. 不保证原子性：redis中的一个事务中如果存在命令执行失败，那么其他命令依然会被执行，没有回滚机制
```

**事务命令**

```python
1、MULTI  # 开启事务
2、命令1  # 执行命令
3、命令2 ... ...
4、EXEC  # 提交到数据库执行
5、DISCARD # 取消事务
```

**使用步骤**

```python
# 开启事务
127.0.0.1:6379> MULTI
OK
# 命令1入队列
127.0.0.1:6379> INCR n1
QUEUED
# 命令2入队列
127.0.0.1:6379> INCR n2
QUEUED
# 提交到数据库执行
127.0.0.1:6379> EXEC
1) (integer) 1
2) (integer) 1
```

**事务中命令错误处理**

```python
# 1、命令语法错误，命令入队失败，直接自动discard退出这个事务
  这个在命令在执行调用之前会发生错误。例如，这个命令可能有语法错误（错误的参数数量，错误的命令名）
  处理方案：客户端发生了第一个错误情况，在exec执行之前发生的。通过检查队列命令返回值:如果这个命令回答这个队列的命令是正确的，否者redis会返回一个错误。如果那里发生了一个队列命令错误，大部分客户端将会退出并丢弃这个事务

# 2、命令语法没错，但类型操作有误，则事务执行调用之后失败，无法进行事务回滚
   从我们施行了一个由于错误的value的key操作（例如对着String类型的value施行了List命令操作）
   处理方案：发生在EXEC之后的是没有特殊方式去处理的：即使某些命令在事务中失败，所有的其他命令都将会被执行。
127.0.0.1:6379> MULTI
OK
127.0.0.1:6379> set num 10
QUEUED
127.0.0.1:6379> LPOP num
QUEUED
127.0.0.1:6379> exec
1) OK
2) (error) WRONGTYPE Operation against a key holding the wrong kind of value
127.0.0.1:6379> get num
"10"
127.0.0.1:6379> 
```

**为什么redis不支持事务回滚**

- 观点


```python
1、Redis的内部极其简单和快速，来源于它不需要回滚功能
2、在生产环境中，通常回滚并不能解决来自编程的错误。举个例子，你本来想+1，却+2了，又或者+在错误的类型上,回滚并不能解决。由于无法提供一个避免程序员自己的错误，而这种错误在产品中并不会出现，所以选择一个简单和快速的方法去支持事务
```

## **pipeline补充**

 **python使用pipeline()与execute()批量进行批量操作**

示例

```python
import redis

# 创建连接池并连接到redis
pool = redis.ConnectionPool(host = '192.168.153.150',db=0,port=6379)
r = redis.Redis(connection_pool=pool)

# 第一组
pipe = r.pipeline()
pipe.set('fans',50)
pipe.incr('fans')
pipe.incrby('fans',100)
pipe.execute()

# 第二组
pipe.get('fans')
pipe.get('pwd')
# [b'151', b'123']
result = pipe.execute()
print(result)
```



## **Redis常见问题汇总**

- **Redis优点**

```python
1、读写速度快. 数据存放在内存中
2、支持数据类型丰富,string,hash,list,set,sorted
3、支持事务
4、可以用于缓存,消息队列,按key设置过期时间,到期后自动删除
5、支持数据持久化(将内存数据持久化到磁盘),支持AOF和RDB两种持久化方式,从而进行数据恢复操作,可以有效地防止数据丢失
5、支持主从(master-slave)复制来实现数据备份,主机会自动将数据同步到从机
```

- **来介绍一下redis中的数据类型**

  |   类型    |                             特点                             |                           使用场景                           |
  | :-------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
  |  string   |           简单key-value类型，value可为字符串和数字           |               常规计数（微博数, 粉丝数等功能）               |
  |   hash    | 是一个string类型的field和value的映射表，hash特别适合用于存储对象 |          存储部分可能需要变更的数据（比如用户信息）          |
  |   list    |                        有序可重复列表                        |                关注列表，粉丝列表，消息队列等                |
  |    set    |                       无序不可重复列表                       | 存储并计算关系（如微博，关注人或粉丝存放在集合，可通过交集、并集、差集等操作实现如共同关注、共同喜好等功能） |
  | sortedset |                    每个元素带有分值的集合                    |                          各种排行榜                          |

- **redis中的持久化方案**

```python
# RDB
快照形式，定期把内存中的数据保存到磁盘。Redis默认支持的持久化方案。速度快但是服务器断电的时候会丢失部分数据

# AOF
把所有对redis数据库增删改操作的命令保存到文件中。数据库恢复时把所有的命令执行一遍即可。
# 两种持久化方案同时开启使用AOF文件来恢复数据库.能保证数据的完整性,但是速度慢。
```

- **使用过Redis分布式锁么，它是什么回事？**

  ```python
  1、从redis2.8开始，set命令集成了两个参数，nx和ex，先拿nx来争抢锁，抢到之后，再用ex参数给锁加一个过期时间防止锁无法释放，造成死锁
  2、redis分布式锁原理见图
  ```

- **缓存穿透**

```python
# 原理
缓存和数据库都没有的数据，而用户反复发起请求， 如 假的用户ID

# 场景
比如发起为id为“-1”的数据或id为特别大不存在的数据。这时的用户很可能是攻击者，攻击会导致数据库压力过大

# 解决方案：
   1、请求校验，接口层增加校验，如对id做基础校验，id<=0的直接拦截
   2、都无法取到数据时也可以将key-value对写为key-null，缓存有效时间比如30秒左右，这样可以防止攻击用户反复用同一个id暴力攻击
```

- **缓存击穿** 

```python
# 原理
缓存没有，数据库有，一般是缓存时间到期， 顺势并发太大
  
#解决方案
1、热点数据不过期  
2、上锁: 重新设计缓存的使用方式，当我们通过key去查询数据时，首先查询缓存，如果没有，就通过分布式锁进行加锁，取得锁的进程查DB并设置缓存，然后解锁；其他进程如果发现有锁就等待，然后等解锁后返回缓存数据或者再次查询DB
```

- **缓存雪崩**

```python
# 原理
缓存中大批量数据过期，导致瞬时大批量不同请求注入DB
  
# 解决方案
解决方案
1、缓存设置随机时间（避免缓存设置相近的有效期；为有效期增加随机值）
2、热点数据不过期
```

   








