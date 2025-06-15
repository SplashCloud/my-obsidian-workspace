
## Disk Index 

### Build

- `_nnodes_per_sector`: `SECTOR_LEN / _max_node_len`
	- `0`表示一个`point`会占据多个`sector`
- `_max_node_len`: 每个节点在磁盘上存储时占用的最大字节数
	- `_disk_bytes_per_point + (1 + _max_degree) * sizeof(uint32_t)`
	- `point`的数据占用的字节数 + 邻居数量 + 邻居ID列表
- `estimate_ram_usage()`
	- 根据数据集大小和`R`判断需要的内存占用
	- 根据构建index时传入的参数`M`判断是否需要分块构建索引
- 


### Search
```c++

this->_data_dim = 128

pq_query_scratch->aligned_query_float = pq_query_scratch->rotated_query = query



```


## Memory Index

### Build

- `index_factory.create_instance()`
	- `data_store`
	- `graph_store`
	- `pq_data_store`

- 构建memory index可以传入`build_PQ_bytes`的参数，也确实生成了压缩后的向量，但是在`search_memory_index`的时候`index->load()`没有支持pq_data_store的加载。
	- ”disk_index的recall降低是pq造成“，可以在memory中

## FreshDiskANN

### MergeInsert

- 成员变量
	- 两个`mem_index`，类型`Index`
		- 由一个`active_index`来指示哪个`mem_index`服务请求
	- 两个`deletion_set`，类型`robin_set`
		- 由一个`activate_delete_set`来指示哪个`deletion_set`服务请求
	- 一个`disk_index`，类型`PQFlashIndex`
- 成员函数
	- `insert`
		- 判断哪个`mem_index`处于活跃状态
		- 调用`mem_index->insert_point()`进行插入
	- `lazy_delete`
		- 判断哪个`deletion_set`处于活跃状态
		- 把数据点加入`deletion_set`
		- 在内存索引中lazy delete：`mem_index->lazy_delete()`
	- `search_sync`
		- 磁盘：`disk_index->cached_beam_search()`
		- 内存：`mem_index->search()`
		- 删除`deletion_set`中的点
	- `final_merge`/`trigger_merge`
		- `save_del_set`
			- 将`active_delete_set`切换到一个空的`deletion_set`
			- 将原来的`deletion_set`中的点加到一个buffer中（后面将其在disk index中删除）
		- `switch_index`
			- 切换到一个空的`mem_index`并且save上一个`mem_index`的内容（服务新请求）
			- 
### tests

`test_concurr_merge_insert`
- `single_file`这个flag到底指示了什么？


---
1. 代码相关
	- merge的代码阅读
		- 插入的时候用什么样的配置进行的搜索
		- 具体是如何删除的
2. 怎么快速搭建测试环境，进行测试，使得协作更加方便！

---
为什么在内存中都是两个`deletion_set`和两个`mem_index`?
- 保证merge的过程中，正常的请求可以同时被服务
- `_active_delete_set`指示哪个`deletion_set`处于活跃状态，可以serve delete request
- 在merge的时候，`_active_delete_set`会进行切换
