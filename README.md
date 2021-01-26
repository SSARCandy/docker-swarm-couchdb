# docker-swarm-couchdb

Source: [https://ssarcandy.tw/2021/01/26/setup-couchdb-using-docker-swarm/](https://ssarcandy.tw/2021/01/26/setup-couchdb-using-docker-swarm/)

## Prerequisites

- Three Linux servers with different Public IP
- All three are installed with docker
- All three machines are set to docker swarm mode

## Deploy

```sh
$ docker stack deploy -c docker-compose.yaml test
$ docker stack ps test                     
ID                  NAME                                     IMAGE               NODE                DESIRED STATE       CURRENT STATE          
ynacuaj8tx35        test_couchdb.7zj2xk3up7ce34atj2nme9rf9   couchdb:3.1.1       docker-node-3       Running             Running 25 seconds ago
5p5w38jtjh7z        test_couchdb.2v2lb55cyes0rf3tbtqe2zp9x   couchdb:3.1.1       docker-node-1       Running             Running 25 seconds ago
bekjdetq739z        test_couchdb.2gedpa6dac3c80ilr3f9ji3fw   couchdb:3.1.1       docker-node-2       Running             Running 26 seconds ago
```

After each node is up, you can make the final configuration. The way to set up the cluster in CouchDB is to add other CouchDB to a certain one through the admin http api.


```sh
$ curl -X PUT "http://admin:admin123@<IP>:5984/_node/_local/_nodes/couchdb@test_couchdb.2v2lb55cyes0rf3tbtqe2zp9x.strqjl8lsdm58tozn59mp8du7" -d {}
{"ok":true,"id":"couchdb@test_couchdb.2v2lb55cyes0rf3tbtqe2zp9x.strqjl8lsdm58tozn59mp8du7","rev":"1-967a00dff5e02add41819138abb3284d"}

$ curl -X PUT "http://admin:admin123@<IP>:5984/_node/_local/_nodes/couchdb@test_couchdb.7zj2xk3up7ce34atj2nme9rf9.u5ce5bl7cmjlhkb2781cye7py" -d {}
{"ok":true,"id":"couchdb@test_couchdb.7zj2xk3up7ce34atj2nme9rf9.u5ce5bl7cmjlhkb2781cye7py","rev":"1-967a00dff5e02add41819138abb3284d"}

$ curl -X POST -H "Content-Type: application/json"  "http://admin:admin123@<IP>:5984/_cluster_setup" -d '{"action": "finish_cluster"}'
{"ok":true}
```

**instance name can be found using `docker ps`**

