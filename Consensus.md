
## Viewstamp Replication



## Paxos

- 针对某一个**决策实例**，在分布式节点之间达成共识
	- 比如：”这个月团建的目的地“。那么”下个月团建的目的地“就是另一个决策实例，需要运行新的paxos算法来达成共识
- 三种角色
	- Proposer
	- Acceptor
	- Learner：为什么需要learner？
		- 这个角色在算法中只是作为被通知者，被告知已经达成共识的值。
		- “关注点/职责分离”：在一个庞大的系统中，并不是每一个节点都需要参与到决策中的，参与决策可能只是必要的一些节点，其他节点只需要知道最后达成共识的值是什么就可以了。
- 两阶段
	- Perpare阶段
		- Proposer生成一个新的`proposal number(N)`，向所有的Acceptor发送Perpare请求
		- Acceptor接受到请求之后判断携带的`proposal number(N)`是否大于它响应过的最大`proposal number`（Perpare阶段）
			- 没有：发送自己接受过的值中`proposal number`最大的`<proposal_number, accepted_value>`（如果有接受的话）
			- 有：忽略该请求
	- Accept阶段
		- Proposer收集来自Acceptors的响应，如果响应数达到了大多数（quorum）
			- j检查在这些响应中存在已经被接受的值，如果有的话选择`proposal_number`最大的那一个值作为本次要proposal的值；如果没有的话，自由proposal一个新的值。
			- 然后将要proposal的值和本次的`proposal number(N)`一起向Acceptors发起请求
		- Acceptor接受到请求之后，判断自己是否在Prepare阶段对这个`proposal number(N)`做出了承诺，并且在此期间没有收到比`N`更大的`proposal number`
			- 是的话，接受这个proposal，并且将值和`N`持久化
			- 不是的话，忽略该请求


## Raft