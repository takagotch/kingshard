### kingshard
---
https://github.com/flike/kingshard

```sql
CREATE TABLE `test_shard_hash_0000` (
  `id` bigint(64) unsigned NOT NULL,
  `str` varchar(256) DEFAULT NULL,
  `f` double DEFAULT NULL,
  `e` enum() DEFAULT NULL,
  `u` tinyint(3) unsigned DEFAULT NULL,
  `i` tinyint(4) DEFAULT NULL,
  PRIMARY KEY(`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8

insert into test_shard_hash(id,str,f,e,u,i) values(15,"flike",3.14,'test2',2,3);
insert into test_shard_hash(id,str,f,e,u,i) values(7,"chen",2.1,'test1',32.3);
insert into test_shard_hash(id,str,f,e,u,i) values(17,"github",2.5,'test1',32,3);
insert into test_shard_hash(id,str,f,e,u,i) values(18,"kingshard",7.3,'test1',32.3);

select * from test_shard_hash where id < 18;
select * from test_shard_hash where id = 18;

update test_shard_hash set i=23 where id =17 or id = 18;
update test_shard_hash set i=23 where id = 15 or id = 18;

/*node1*/show tables;
/*node2*/select * from kingshard_test_conn;
/*node3*/desc kingshard_test_conn;
/*node4*/insert into kingshard_test_conn values(10,"",10.2,'',1,1);
/*node5*/select * form kingshard_test_conn;

select /*master*/ * from kinsghard_test_conn;
select count(id) from test_shard_hash where id > 1;
select sum(id) from test_shard_hash where id > 1;
select * from test_shard_hash where id > 1 order by id;
begin;
insert into test_shard_hash(id,str,f,e,u,i) values(23,'proxy',9.2,'test1',12,3);
commit;

admin node(opt,node,k,v) values('add','node1','slave','127.0.0.1:3306');
admin node(opt,node,k,v) values('del','node1','slave','127.0.0.1:3306');
admin node(opt,node,k,v) values('down','node1','slave','127.0.0.1:3306');
admin node(opt,node,k,v) values('up','node1','slave','127.0.0.1:3306');
admin node(opt,node,k,v) values('down','node1','slave','127.0.0.1:3306');
admin node(opt,node,k,v) values('up','node1','slave','127.0.0.1:3306');


admin server(opt,k,v) vlues('show', 'allow_ip', 'config');
admin server(opt,k,v) values('show', 'black_sql', 'config');
admin server(opt,k,v) values('show', 'node', 'config');
admin server(opt,k,v) values('show', 'schema', 'config');
admin server(opt,k,v) values('show', 'allow_ip', 'config');
admin server(opt,k,v) values('show', 'black_sql', 'config');

admin server(opt,k,v) values('change','log_sql','off');
admin server(opt,k,v) values('change','log_sql','on')
admin server(opt,k,v) values('change','slow_log_time','50');
admin server(opt,k,v) values('add', 'allow_ip','127.0.0.1');
admin server(opt,k,v) values('del', 'allow_ip','127.0.0.1');
admin server(opt,k,v) values('add', 'black_sql', 'select count(*) from sbtest1')
admin server(opt,k,v) values('del', 'black_sql', 'select count(*) from sbtest1')
admin server(opt,k,v) values('save', 'proxy', 'config')

admin server(opt,k,v) values('show', 'proxy', 'status')
admin server(opt,k,v) values('change', 'proxy', 'online')
```

```yml
addr : 0.0.0.0:9696
web_addr : 0.0.0.0:9797
web_user : admin
web_password : admin

user_list:
-
user : kingshard
password : kingshard

log_level : debug

nodes :
-
  name : nodel
  
  max_conns_limit : 8
  
  user : kingshard
  password : kingshard
  
  master : 127.0.0.1:3306
  
  slave :
  down_after_noalive : 32
  
  name : node2
  
  max_conns_limit : 8
  
  user : kingshard
  password : kingshard
  
  master : 192.168.59.103:3307
  
  slave :
  
  down_after_noalive: 32
  
schema_list :
-
  user: kingshard
  nodes: [node1,node2]
  default: node1
  shard:
  -
    db : kingshard
    table: test_shard_hash
    key: id
    nodes: [node1, node2]
    type: hash
    locations: [4,4]
    
    db : kingshard
    table: test_shard_range
    key: id
    type: range
    nodes: [node1, node2]
    locations: [4.4]
    table_row_limit: 10000
```

```
```


