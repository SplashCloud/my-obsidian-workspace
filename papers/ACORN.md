
解决hybrid-search场景搜索效率的问题
- hybrid search就是在包含向量和标量的场景下进行搜索，比如说 和这条裙子类似的、价格低于100元的裙子
现有方法的问题
- pre-filtering
	- 现根据predicate把先数据集筛一遍，然后**遍历剩下的点**进行暴搜
	- 适合predicate selectivity比较小的场景
- post-filtering
	- 先使用ANNS算法搜出距离query vector最近的顶点集合，然后在使用predicate过滤
	- 如何保证第一步搜索出来的集合大小呢？
ACRON：保证$X_p$（满足predicate的数据集）对应的子图索引依旧可以保持原来HNSW那样的导航性和搜索鲁棒性
- 在构建HNSW的时候，增加每个顶点的邻居数量，使得整个图变得更加密集
- 使用一种新的剪枝方法
- 在搜索过程中，**获取邻居的时候会只选择通过了predicate的那些邻居顶点**