







* 唯一索引
* 一般索引



```sql
begin
	for i in 1..1000000 loop
		insert into t values (i);
	if mod(i,100) = 0 then
		commit;
	end if;
	end loop;
end;
```

每一百条提交一次
分批提交 降低IO

## 索引碎片

分析索引

```sql
analyze index inde_1 validate structure;
```

index_stats 视图里 三种情况出现一个

* HEIGHT >= 4
* PCT_USED<50%
* DEL_LF_ROWS/LF_ROWS>0.2

就出现碎片了

重建索引

```sql
alter index ind_1 rebuild [online] [tablespace name];
```





## 表设计

### 数据库表的设计

* 业务需要切分
* 逻辑分层（数据库分层）
* 数据库表结构设计与拆分
  * mysql水平拆分
  * Oracle分区
  * 物化视图
  * 中间表的方案
  * 设计的方案
    * 业务的表设计

### 对于结构优化的设计

* 建立索引
  * 规则索引
  * 索引 加函数使用索引，则索引失效
* 建立普通索引
* 建立规则索引
* 建立符合索引
* 数据规则 添加你认为必要的字段  四个字段：
  * create by
  * create time
  * update by
  * update time
* 预留字段（关联其他业务）
* 合理的冗余





## 物化视图

```sql
create materialized view my_name [N] 
as 
select * from table_name;

[N]:
[选项1] build [IMMEDIATE,DEFERRED]是否在创建视图时生成数据，默认是生成，deferred为不生成，在需要的时候生成。
[选项2] refresh [fast,complete,force,never]fast是增量刷新，快速刷新；complete全表刷新；force是如果增量刷新可以使用，就增量，否则全部；never，从不刷新。
[选项3] on [Demand,commit] 手动刷新 和 提交时刷新
[选项4] start with 通知数据库完成从主表到本地表第一次复制的时间
[选项5] next 刷新的时间间隔
```

例子

```sql
create materialized view v_ab
refresh force on commit
as
select ...
```

增量刷新

```sql
create materizlized view log on table_name_1 with rowid;
create materizlized view log on table_name_2 with rowid;

create materizlized view my_view
refresh fast on demand
	start with sysdate
	next sysdate+1/1440
as
	select table_name_1.rowid,
			table_name_2.rowid,
	from 
```



## Oracle 分表 分区

* OLTP 联机事务处理：面向交易，

* OLAP 联机分析处理：多维的数据分析、查询和报表

