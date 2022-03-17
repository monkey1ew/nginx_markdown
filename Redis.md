## Redis

#### **Redis的五大数据类型**

**1.String(字符串)**

​	**(1)：expire key ：**设置key的过期时间

​	**(2)：ttl key：**查看key的过期时间**[-1表示永不过期，-2表示已经过期]**

​	**(3)：del key：**删除key

​	**(4)：append key value：**向key中追加值

```shell
127.0.0.1:6381> get k1
"v1"
127.0.0.1:6381> APPEND k1 abc
(integer) 5
127.0.0.1:6381> get k1
"v1abc"
127.0.0.1:6381> 
```

​	**(5)：incr key：**将key自增1**[有原子性]**

​	**(6)：incrby key step：**将key自增step

​	**(7)：decrby key step：**将key自减step

​	**(8)：getrange key start end：**获取 start 到 end的范围的key的值

​	**(9)：setrange key start end value：**在start 到 end范围内设置值

​	**(10)：setex key 10 value：**设置值并且设置过期时间

​	**(11)：setnx key value：**设置值**[如果不存在]**

​	**(12)：mset k1 v1 k2 v2 k3 v3.......：**设置多个key

​	**(13)：mget k1 k2 k3.......：**获取多个值

​	**(14)：msetnx k1  v1 k2  v2 k3 v3......：** **[部分key存在不成功]**



#### **2.Hash(哈希)：是一个string类型的field和value映射表**

​	**kv模式不变，但是value是一个键值对**

​	**(1)：hset   user  id   11：** 设置user  id 的值

​	**(2)：hget  user  id：** 获取 id的值

​	**(3)：hmset  customer  id  11 name  jack  age  20：**设置多个键值对

​	**(4)：hgetall  customer：**获取所有的value 

​	**(5)：hdel  customer  name：**删除name

​	**(6)：hexists customer  name：**判断是否存在key

​	**(7)：hkeys customer：**获取所有的key

​	**(8)：hvals customer：**获取所有的value

​	**(9)：hincrby customer age 2：**将指定的key增加step

​	**(10)：hsetnx customer age 26：**如果不存在key就设置

​	

**3.List(集合)：底层LinkedList**

​	**(1)：lpush list01 1 2 3 4 5........：** **列表采用头插法**

​	**(2)：lrange list01 0 -1........：** **取值[5,4,3,2,1]**

​	**(3)：rpush	| rrange........：** **采用尾插法**

​	**(4)：lpop list01：** pop出左边的第一个元素

​	**(5)：rpop list01：** pop出右边第一个元素

​	**(6)：ltrim list01 starIndex endIndex：** **截取list01从start到 end的元素重新赋给list01**

​	**(7)：RpopLpush list01 list02：** **从list01的(右端)尾部pop出一个元素加入到list02的(左端)头部**

​	**(8)：lset list02 index value：**将list02的index位置设置值为value

​	**(9)：Linsert key before/after 值1 值2：**把值2插入到值1的前/后



**4.Set(集合)：无序无重复集合**

​	**(1)：sadd set01 1 1 2 2 3 3：**把多个值去重后加入set01

​	**(2)：smembers set01：**查看set01的值

​	**(3)：scard set01：**获取集合的元素个数

​	**(4)：srem set01 value：**删除集合的值

​	**(5)：srandmember set01 3：**在set01中随机抽3个值

​	**(6)：smove set01 set02	5：**将set01的5移动到set02

​	**(7)：sdiff set01 set02：**求set01 和 set02 的差集

​	**(8)：sinter set01 set02：**求交集合

​	**(9)：sunion set01 set02：**求并集合



**5.Zset(sorted set：有序集合)：**有序无重复(**按照内置的[scores]分数排序**)

​	**(1)：zadd  zset01  v1 score v2  score v3 score...........：**给zset01设置多个key并且设置分数**[score]**

​	**(2)：zrange zset01  0   -1 withscores：**列出全部的元素和分数

​	**(3)：zrem zset01  v1：**删除v1

​	**(4)：zcount  zset01  60  80：**统计60-80分的值

​	**(5)：zrank  zset01  v1：**拿到v1的排名(下标值)

​		

​	

​	