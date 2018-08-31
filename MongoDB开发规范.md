#### MongoDB开发规范
##### 库的设计
- 库名全部采用小写，禁止使用除 _ 下划线外的特殊字符
- 库名禁止使用数字打头
- 库名禁止包含特殊字符
- 不使用系统保留库名(admin ,local , config)等

##### 集合的设计
- 集合名全部采用小写，禁止使用除 _ 下划线外的特殊字符
- 集合名称禁止使用数字开头
- 集合名称禁止使用system开头(system为MongoDB系统集合的前缀)
- 集合名称不超过20个字符
- 集合的字段设计不要过长
- 为了避免库级锁带来的问题，应尽量对写入较大的集合使用“单库单集合”的结构，所以对于新增业务应尽量创建新库，而不是在现有库中创建新集合
- 部分日志场景下，可以使用[Capped固定集合]自动淘汰老数据

##### 索引的设计
- 合理创建联合索引
- 联合索引中字段数不超过5个
- 联合索引字段顺序：匹配条件在前，范围条件在后
- 查询表达式，如果用到正则表达式、非操作符()如$nin)，则不会用到索引
- 每个集合最多有64个索引
- 索引名称长度，不超过20个字符
- 需要自动过期的数据，可以加[TTL索引]

#### SQL使用规范
- 正确使用写关注设置 [Write Concern]
 ```MongoDB建议对重要的数据使用 {w:"marjority"} 的选项。{w:"majority"} 可以保证数据在复制到多数节点后才返回成功结果。使用该机制可以有效防止数据回滚的发生。```
- 正确使用读选项设置 [Read Preference]
- 对写操作使用[retry重试机制]
- 创建索引必须以[background]方式创建，避免造成写锁
```db.people.createIndex({zipcode: 1}, {background: true})```
- 创建索引、创建集合必须提工单由DBA创建
- 避免使用短连接(短连接增加额外的conn、auth、close的开销)
- 不确定是否走索引的SQL，可以使用explain查看其执行计划

[capped固定集合]:https://docs.mongodb.com/manual/core/capped-collections/index.html#create-a-capped-collection
[TTL索引]:https://docs.mongodb.com/manual/tutorial/expire-data/index.html
[Write Concern]:https://docs.mongodb.com/manual/reference/write-concern/index.html#write-concern-specification
[Read Preference]:https://docs.mongodb.com/manual/core/read-preference/
[retry重试机制]:https://docs.mongodb.com/manual/core/retryable-writes/index.html
[background]:https://docs.mongodb.com/manual/core/index-creation/index.html#background-construction
