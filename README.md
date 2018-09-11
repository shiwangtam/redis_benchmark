# redis_benchmark

yum install -y wget gcc
wget http://download.redis.io/releases/redis-4.0.11.tar.gz
tar xvf redis-4.0.11.tar.gz 
cd redis-4.0.11
make
src/redis-server &
cd src

localhost testing

C4.2xlarge

src/redis-benchmark  -q -n 100000
PING_INLINE: 102354.15 requests per second
PING_BULK: 102354.15 requests per second
SET: 103519.66 requests per second
GET: 102249.49 requests per second
INCR: 103092.78 requests per second
LPUSH: 105485.23 requests per second
RPUSH: 104275.29 requests per second
LPOP: 103734.44 requests per second
RPOP: 102774.92 requests per second
SADD: 102774.92 requests per second
HSET: 104493.20 requests per second
SPOP: 104602.52 requests per second
LPUSH (needed to benchmark LRANGE): 104602.52 requests per second
LRANGE_100 (first 100 elements): 58105.75 requests per second
LRANGE_300 (first 300 elements): 25733.40 requests per second
LRANGE_500 (first 450 elements): 18907.17 requests per second
LRANGE_600 (first 600 elements): 15151.52 requests per second
MSET (10 keys): 104712.05 requests per second

M4.4xlarge

src/redis-benchmark -q -n 100000
PING_INLINE: 90334.23 requests per second
PING_BULK: 89525.52 requests per second
SET: 90661.83 requests per second
GET: 90826.52 requests per second
INCR: 91324.20 requests per second
LPUSH: 91407.68 requests per second
RPUSH: 90579.71 requests per second
LPOP: 90826.52 requests per second
RPOP: 91659.03 requests per second
SADD: 89766.61 requests per second
HSET: 91575.09 requests per second
SPOP: 90744.10 requests per second
LPUSH (needed to benchmark LRANGE): 91575.09 requests per second
LRANGE_100 (first 100 elements): 49554.02 requests per second
LRANGE_300 (first 300 elements): 21436.23 requests per second
LRANGE_500 (first 450 elements): 15931.18 requests per second
LRANGE_600 (first 600 elements): 12610.34 requests per second
MSET (10 keys): 91827.37 requests per second



