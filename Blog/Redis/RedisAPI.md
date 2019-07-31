# Redis五大类型对应的主要的API

### String

|            command             |        complexity        |        interpretation         |
| :----------------------------: | :----------------------: | :---------------------------: |
|         set key value          |           O(1)           |        设置string键值         |
|    setex key seconds value     |           O(1)           |     设置带过期时间的键值      |
|  setpx key milliseconds value  |           O(1)           |   设置毫秒级过期时间的键值    |
|        setnx key value         |           O(1)           |  键不存在才可设置，用于添加   |
|        setxx key value         |           O(1)           |   键存在才可设置，用于更新    |
|            get key             |           O(1)           |        获取键对应的值         |
|        del key [key...]        | O(k),k为要删除的键的个数 |           删除键值            |
| mset key value [key value ...] | O(k),k为要删除的键的个数 |  批量设置键值(节省网络时间)   |
|       mget key [key ...]       | O(k),k为要删除的键的个数 |          批量获取键           |
|            incr key            |           O(1)           |          整数值自增1          |
|            decr key            |           O(1)           |          整数值自减1          |
|      incrby key increment      |           O(1)           |          整数值自增           |
|      decrby key increment      |           O(1)           |          整数值自减           |
|   incrbyfloat key increment    |           O(1)           |          浮点数自增           |
|        append key value        |           O(1)           | 将value添加到原先字符值的尾部 |
|           strlen key           |           O(1)           |       返回字符值的长度        |
|   setrange key offset value    |           O(1)           |    设置字符值指定位置的值     |
|     getrange key start end     |           O(n)           |   获取字符值指定范围的内容    |



## Hash

|              command              |         complexity          |           interpretation           |
| :-------------------------------: | :-------------------------: | :--------------------------------: |
|       hset key field value        |            O(1)             |            设置hash键值            |
|          hget key field           |            O(1)             |     根据hash的键(field)获取值      |
|    hdel key field [field ...]     | O(k),k为要删除的field的个数 |            删除hash键值            |
|             hlen key              |            O(1)             |     返回一个key对应的hash个数      |
|            hgetall key            |   O(n),n为key的field总数    | 获取一个key对应的hash的所有键和值  |
|      hmget field [field ...]      | O(k),k为要设置的fied的个数  |           批量获取hash值           |
| hmset field value[field value...] | O(k),k为要获取的field的个数 |           批量设置hash值           |
|         hexist key field          |            O(1)             |       检测一个hash值是否存在       |
|             hkeys key             |   O(n),n为key的field总数    |   返回一个key对应的hash的所有键    |
|             havls key             |   O(n),n为key的field总数    |   返回一个key对应的hash的所有值    |
|      hsetnx key field value       |            O(1)             |       hash键不存在才可以设置       |
|    hincrby key field increment    |            O(1)             |       hash值为整数时进行自增       |
| hincrbyfloat key field increment  |            O(1)             |      hash值为浮点数时进行自增      |
|         hstrlen key field         |            O(1)             | 返回某hash键对应的字符hash值的长度 |





## List

| Type |                command                |             complexity              |                        interpretation                        |
| :--: | :-----------------------------------: | :---------------------------------: | :----------------------------------------------------------: |
| 添加 |      rpush key value [value ...]      |      O(k),k为要添加的元素个数       |                       从右增加list键值                       |
| 添加 |      lpush key value [value ...]      |      O(k),k为要添加的元素个数       |                       从左增加list键值                       |
| 添加 | linsert key before\|after pivot value |  O(n),n为pivot距离列表头或尾的距离  |            在list值为pivot的元素前或后增加list值             |
| 查找 |         lrange key start end          | O(s+n),s为start偏移量，n为end-start |                 返回下标从start到end的list值                 |
| 查找 |           lindex key index            |         O(n),n为索引偏移量          |                   返回下标为index的list值                    |
| 查找 |               llen key                |                O(1)                 |                   返回指定键的list值的个数                   |
| 删除 |               lpop key                |                O(1)                 |              删除并返回指定list键最右侧的list值              |
| 删除 |               rpop key                |                O(1)                 |              删除并返回指定list键最左侧的list值              |
| 删除 |         lrem key count value          |          O(n),n为列表长度           | count=0:删除指定list键所有的给定的value值；count>0:从左到右删除指定list键count个数的给定value的值；count<0:从右到左删除指定list键\|count\|个数的给定value的值。 |
| 删除 |          ltrim key start end          |      O(n),n为要剪裁的元素个数       |      保留指定list键从start到end范围内的值，即删除其他值      |
| 修改 |         lset key index value          |         O(n),n为索引偏移量          |                     修改给定下标的list值                     |
| 阻塞 |      blpop key [key ...] timeout      |                O(1)                 |  若列表为空，则等待timeout秒后返回，若不为空立即返回最左值   |
| 阻塞 |      brpop key [key ...] timeout      |                O(1)                 |  若列表为空，则等待timeout秒后返回，若不为空立即返回最右值   |





## Set

|            command             |                     complexity                      |        interpretation         |
| :----------------------------: | :-------------------------------------------------: | :---------------------------: |
| sadd key element [element ...] |                  O(k),k为元素个数                   |       往集合中加入元素        |
| srem key element [element ...] |                  O(k),k为元素个数                   |        删除集合中元素         |
|           scard key            |                        O(1)                         |      返回某集合元素个数       |
|     sismember key element      |                        O(1)                         | 检测某元素是否是某集合的成员  |
|    srandmember key [count]     |                      O(count)                       | 从某集合中随机取出count个元素 |
|            spop key            |                        O(1)                         |    随机弹出集合中一个元素     |
|          smembers key          |                O(n),n为key的元素总数                |     返回某集合的所有元素      |
|      sinter key [key ...]      | O(m*k),k是多个集合中元素最少的集合的个数，k是键个数 |       对多个集合取交集        |
|      sunion key [key ...]      |             O(k),k为多个集合元素个数和              |       对多个集合取并集        |
|      sdiff key [key ...]       |             O(k),k为多个集合元素个数和              |       对多个集合取差集        |



## Zset

|                           command                            |                          complexity                          |                        interpretation                        |
| :----------------------------------------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
|           zadd key score member [score member ...]           |         O(k*log(n)),k为添加成员个数，n为已有成员个数         |                     往有序集合中加入成员                     |
|                          zcard key                           |                             O(1)                             |                  返回指定有序集合的元素个数                  |
|                      zscore key memeber                      |                             O(1)                             |                       返回某成员的分数                       |
|                       zrank key member                       |                  O(log(n)),n为已有成员个数                   |                 返回某成员分数从低到高的排名                 |
|                     zrevrank key member                      |                  O(log(n)),n为已有成员个数                   |                 返回某成员分数从高到低的排名                 |
|                zrem key member [memeber ...]                 |         O(k*log(n)),k为添加成员个数，n为已有成员个数         |                      删除有序集合的成员                      |
|                 zincrby key increment member                 |                  O(log(n)),n为已有成员个数                   |                       增加某成员的分数                       |
|            zrangebyscore key min max [withscores]            |         O(log(n)+k),k为获取成员个数，n为已有成员个数         |  根据分数返回指定返回的成员，加上withscores的话同时返回分数  |
|              zrange key start end [withscores]               |         O(log(n)+k),k为获取成员个数，n为已有成员个数         |  根据排名返回指定返回的成员，加上withscores的话同时返回分数  |
|                      zcount key min max                      |                  O(log(n)),n为已有成员个数                   |                  返回指定分数范围的成员个数                  |
|                zremrangebyrank key start end                 |         O(log(n)+k),k为删除成员个数，n为已有成员个数         |                       根据排名删除成员                       |
|                 zremrangebyscore key min max                 |         O(log(n)+k),k为删除成员个数，n为已有成员个数         |                       根据分数删除元素                       |
| zinterstore destination numkeys key [key ...] [weights weight [weight ...]] [aggregate sum\|min\|max] | O(nk) + O(mlog(m)),n是多个集合中元素最少的集合的个数，k是集合个数,m是结果集中成员个数 | 对多个有序集合做交集，weights表示各成员的权重，aggregate表示成员交集后的分数按sum(默认),min或max进行汇总 |
| zinterstore destination numkeys key [key ...] [weights weight [weight ...]] [aggregate sum\|min\|max] |   O(n) + O(mlog(m)),n是有序集合的个数,m是结果集中成员个数    | 对多个有序集合做并集，weights表示各成员的权重，aggregate表示成员交集后的分数按sum(默认),min或max进行汇总 |

