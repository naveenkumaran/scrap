# scrap
Scrap artifacts required for demo

## Redis Cluster 
Clone the repository and change directory to redis folder.
```bash
git clone https://github.com/naveenkumaran/scrap.git && cd "$(basename $_ .git)"
```
Create symbolic link for redis server and redis-cli
```bash
ln -s redis/redis-server /usr/local/bin/redis-server
ln -s redis/redis-server/src/redis-cli /usr/local/bin/redis-cli
```

Run ./start-cluster.sh to start the redis cluster 
Create a cluster by running the following command
```bash
redis-cli --cluster create 127.0.0.1:8000 127.0.0.1:8001 \
127.0.0.1:8002 127.0.0.1:8003 127.0.0.1:8004 127.0.0.1:8005 \
--cluster-replicas 1
```


Check the health of the cluster with the redis-cli command
```bash
redis-cli --cluster -h 127.0.0.1 -p 8001 
$ CLUSTER INFO'
```

 
