# redis_benchmark


yum install -y wget gcc
wget http://download.redis.io/releases/redis-4.0.11.tar.gz
tar xvf redis-4.0.11.tar.gz 
cd redis-4.0.11
make
src/redis-server &
cd src

localhost testing
~~~
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
~~~
~~~
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
~~~

How to Set remote lister

~~~
# cat /etc/myredis.conf
# [Redis](http://yijiebuyi.com/category/redis.html) 配置文件

# 当配置中需要配置内存大小时，可以使用 1k, 5GB, 4M 等类似的格式，其转换方式如下(不区分大小写)
#
# 1k => 1000 bytes
# 1kb => 1024 bytes
# 1m => 1000000 bytes
# 1mb => 1024*1024 bytes
# 1g => 1000000000 bytes
# 1gb => 1024*1024*1024 bytes
#
# 内存配置大小写是一样的.比如 1gb 1Gb 1GB 1gB

# daemonize no 默认情况下，redis不是在后台运行的，如果需要在后台运行，把该项的值更改为yes
daemonize yes

#这个要写，不然就有这种错误
#-DENIED Redis is running in protected mode because protected mode is enabled, no bind address was specified, no #authentication password is requested to clients. In this mode connections are only accepted from the loopback interface. If #you want to connect from external computers to Redis you may adopt one of the following solutions: 1) Just disable protected #mode sending the command 'CONFIG SET protected-mode no' from the loopback interface by connecting to Redis from the same host #the server is running, however MAKE SURE Redis is not publicly accessible from internet if you do so. Use CONFIG REWRITE to #make this change permanent. 2) Alternatively you can just disable the protected mode by editing the Redis configuration file, #and setting the protected mode option to 'no', and then restarting the server. 3) If you started the server manually just for #testing, restart it with the '--protected-mode no' option. 4) Setup a bind address or an authentication password. NOTE: You #only need to do one of the above things in order for the server to start accepting connections from the outside.

protected-mode no

# 当redis在后台运行的时候，[Redis](http://yijiebuyi.com/so.html?k=redis)默认会把pid文件放在/var/run/redis.pid，你可以配置到其他地址。
# 当运行多个redis服务时，需要指定不同的pid文件和端口
pidfile /var/run/redis.pid

# 指定redis运行的端口，默认是6379
port 6379

# 指定redis只接收来自于该IP地址的请求，如果不进行设置，那么将处理所有请求，
# 在生产环境中最好设置该项
# bind 127.0.0.1

# Specify the path for the unix socket that will be used to listen for
# incoming connections. There is no default, so Redis will not listen
# on a unix socket when not specified.
#
# unixsocket /tmp/redis.sock
# unixsocketperm 755

# 设置客户端连接时的超时时间，单位为秒。当客户端在这段时间内没有发出任何指令，那么关闭该连接
# 0是关闭此设置
timeout 0

# 指定日志记录级别
# Redis总共支持四个级别：debug、verbose、notice、warning，默认为verbose
# debug 记录很多信息，用于开发和测试
# varbose 有用的信息，不像debug会记录那么多
# notice 普通的verbose，常用于生产环境
# warning 只有非常重要或者严重的信息会记录到日志
loglevel debug

# 配置log文件地址
# 默认值为stdout，标准输出，若后台模式会输出到/dev/null
#logfile stdout
logfile /var/log/redis/redis.log

# To enable logging to the system logger, just set 'syslog-enabled' to yes,
# and optionally update the other syslog parameters to suit your needs.
# syslog-enabled no

# Specify the syslog identity.
# syslog-ident redis

# Specify the syslog facility.  Must be USER or between LOCAL0-LOCAL7.
# syslog-facility local0

# 可用数据库数
# 默认值为16，默认数据库为0，数据库范围在0-（database-1）之间
databases 16

################################ 快照  #################################
#
# 保存数据到磁盘，格式如下:
#
#   save <seconds> <changes>
#
#   指出在多长时间内，有多少次更新操作，就将数据同步到数据文件rdb。
#   相当于条件触发抓取快照，这个可以多个条件配合
#    
#   比如默认配置文件中的设置，就设置了三个条件
#
#   save 900 1  900秒内至少有1个key被改变
#   save 300 10  300秒内至少有300个key被改变
#   save 60 10000  60秒内至少有10000个key被改变

save 900 1
save 300 10
save 60 10000

# 存储至本地数据库时（持久化到rdb文件）是否压缩数据，默认为yes
rdbcompression yes


# 本地持久化数据库文件名，默认值为dump.rdb
dbfilename dump.rdb

# 工作目录
#
# 数据库镜像备份的文件放置的路径。
# 这里的路径跟文件名要分开配置是因为redis在进行备份时，先会将当前数据库的状态写入到一个临时文件中，等备份完成时，
# 再把该该临时文件替换为上面所指定的文件，而这里的临时文件和上面所配置的备份文件都会放在这个指定的路径当中。
# 
# AOF文件也会存放在这个目录下面
# 
# 注意这里必须制定一个目录而不是文件
dir ./

################################# 复制 #################################

# 主从复制. 设置该数据库为其他数据库的从数据库. 
# 设置当本机为slav服务时，设置master服务的IP地址及端口，在Redis启动时，它会自动从master进行数据同步
#
# slaveof <masterip> <masterport>

# 当master服务设置了密码保护时(用requirepass制定的密码)
# slav服务连接master的密码
# 
# masterauth <master-password>


# 当从库同主机失去连接或者复制正在进行，从机库有两种运行方式：
#
# 1) 如果slave-serve-stale-data设置为yes(默认设置)，从库会继续相应客户端的请求
# 
# 2) 如果slave-serve-stale-data是指为no，出去INFO和SLAVOF命令之外的任何请求都会返回一个
#    错误"SYNC with master in progress"
#
slave-serve-stale-data yes

# 从库会按照一个时间间隔向主库发送PINGs.可以通过repl-ping-slave-period设置这个时间间隔，默认是10秒
#
# repl-ping-slave-period 10

# repl-timeout 设置主库批量数据传输时间或者ping回复时间间隔，默认值是60秒
# 一定要确保repl-timeout大于repl-ping-slave-period
# repl-timeout 60   
~~~

mkdir /var/log/redis
touch /var/log/redis/redis.log
./redis-server /etc/myredis.conf

Client: C4.2X -> C4.2X

~~~
./redis-benchmark -q -n 100000 -h 172.31.40.47
PING_INLINE: 80192.46 requests per second
PING_BULK: 81103.00 requests per second
SET: 81900.09 requests per second
GET: 80971.66 requests per second
INCR: 82508.25 requests per second
LPUSH: 82372.32 requests per second
RPUSH: 82576.38 requests per second
LPOP: 81900.09 requests per second
RPOP: 82034.45 requests per second
SADD: 70921.98 requests per second
HSET: 82304.52 requests per second
SPOP: 71428.57 requests per second
LPUSH (needed to benchmark LRANGE): 81499.59 requests per second
LRANGE_100 (first 100 elements): 61500.61 requests per second
LRANGE_300 (first 300 elements): 26130.13 requests per second
LRANGE_500 (first 450 elements): 19778.48 requests per second
LRANGE_600 (first 600 elements): 16310.55 requests per second
MSET (10 keys): 95693.78 requests per second
~~~

Client: C4.2x -> M4.4X

./redis-benchmark -q -n 100000 -h 172.31.43.161
PING_INLINE: 81300.81 requests per second
PING_BULK: 80450.52 requests per second
SET: 81766.15 requests per second
GET: 81967.21 requests per second
INCR: 82440.23 requests per second
LPUSH: 82644.62 requests per second
RPUSH: 83333.33 requests per second
LPOP: 81103.00 requests per second
RPOP: 82372.32 requests per second
SADD: 82781.46 requests per second
HSET: 82987.55 requests per second
SPOP: 81566.07 requests per second
LPUSH (needed to benchmark LRANGE): 82576.38 requests per second
LRANGE_100 (first 100 elements): 61087.36 requests per second
LRANGE_300 (first 300 elements): 25987.53 requests per second
LRANGE_500 (first 450 elements): 20288.09 requests per second
LRANGE_600 (first 600 elements): 15153.81 requests per second
MSET (10 keys): 88417.33 requests per second

Seems networking on client side is the bottleneck

Try M4.4X to C4.2X

src/redis-benchmark -q -n 100000 -h 172.31.40.47
PING_INLINE: 70721.36 requests per second
PING_BULK: 100401.61 requests per second
SET: 84602.37 requests per second
GET: 99800.40 requests per second
INCR: 95057.03 requests per second
LPUSH: 75471.70 requests per second
RPUSH: 80321.28 requests per second
LPOP: 71736.01 requests per second
RPOP: 67659.00 requests per second
SADD: 87489.06 requests per second
HSET: 76923.08 requests per second
SPOP: 96432.02 requests per second
LPUSH (needed to benchmark LRANGE): 72992.70 requests per second
LRANGE_100 (first 100 elements): 53248.14 requests per second
LRANGE_300 (first 300 elements): 23446.66 requests per second
LRANGE_500 (first 450 elements): 16276.04 requests per second
LRANGE_600 (first 600 elements): 13354.70 requests per second
MSET (10 keys): 78369.91 requests per second


Elasticache

No replica, Same AZ

C4.2x -> Elasticache M4.4X

./redis-benchmark -q -n 100000 -h redism4.viiiod.0001.apne1.cache.amazonaws.com
PING_INLINE: 84530.86 requests per second
PING_BULK: 98039.22 requests per second
SET: 90252.70 requests per second
GET: 102880.66 requests per second
INCR: 100603.62 requests per second
LPUSH: 85689.80 requests per second
RPUSH: 88417.33 requests per second
LPOP: 94339.62 requests per second
RPOP: 88028.16 requests per second
SADD: 93545.37 requests per second
HSET: 100100.10 requests per second
SPOP: 111731.84 requests per second
LPUSH (needed to benchmark LRANGE): 93457.94 requests per second
LRANGE_100 (first 100 elements): 60313.63 requests per second
LRANGE_300 (first 300 elements): 24709.66 requests per second
LRANGE_500 (first 450 elements): 18467.22 requests per second
LRANGE_600 (first 600 elements): 15439.25 requests per second
MSET (10 keys): 94607.38 requests per second

C4.2x -> Elasticache R4.2x

./redis-benchmark -q -n 100000 -h  redis.viiiod.0001.apne1.cache.amazonaws.com
PING_INLINE: 84033.61 requests per second
PING_BULK: 86655.11 requests per second
SET: 82508.25 requests per second
GET: 84817.64 requests per second
INCR: 83822.30 requests per second
LPUSH: 82236.84 requests per second
RPUSH: 83963.05 requests per second
LPOP: 75075.07 requests per second
RPOP: 84317.03 requests per second
SADD: 83402.84 requests per second
HSET: 81833.06 requests per second
SPOP: 86805.56 requests per second
LPUSH (needed to benchmark LRANGE): 82034.45 requests per second
LRANGE_100 (first 100 elements): 56915.20 requests per second
LRANGE_300 (first 300 elements): 25329.28 requests per second
LRANGE_500 (first 450 elements): 19798.06 requests per second
LRANGE_600 (first 600 elements): 14695.08 requests per second
MSET (10 keys): 86281.27 requests per second


C4.2x -> Elasticache R4.4x


./redis-benchmark -q -n 100000 -h  redis4x.viiiod.0001.apne1.cache.amazonaws.com
PING_INLINE: 96618.36 requests per second
PING_BULK: 100502.52 requests per second
SET: 93283.58 requests per second
GET: 108108.11 requests per second
INCR: 117924.53 requests per second
LPUSH: 114285.71 requests per second
RPUSH: 107411.38 requests per second
LPOP: 84033.61 requests per second
RPOP: 101522.84 requests per second
SADD: 108577.63 requests per second
HSET: 94966.77 requests per second
SPOP: 114416.48 requests per second
LPUSH (needed to benchmark LRANGE): 104166.67 requests per second
LRANGE_100 (first 100 elements): 60569.35 requests per second
LRANGE_300 (first 300 elements): 26021.34 requests per second
LRANGE_500 (first 450 elements): 19809.83 requests per second
LRANGE_600 (first 600 elements): 14932.06 requests per second
MSET (10 keys): 92936.80 requests per second



Testing using different AZ










