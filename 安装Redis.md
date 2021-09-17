```shell
mkdir /usr/james
cd /usr/james
wget http://download.redis.io/releases/redis-4.0.6.tar.gz
tar xzf redis-4.0.6.tar.gz
cd redis-4.0.6
make

mkdir /usr/local/redis
cp redis-server /usr/james/redis 
cp redis-benchmark /usr/james/redis 
cp redis-check-rdb /usr/james/redis
cp redis-sentinel /usr/james/redis
cp redis-cli /usr/james/redis 
cp redis.conf /usr/james/redis 


./bin/redis-cli -h 192.168.211.121 -p 6379
```

