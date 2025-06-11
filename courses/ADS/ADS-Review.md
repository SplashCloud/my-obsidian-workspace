
## Distributed Data Store

- ACID Principle
	- Atomicity: All-or-nothing respect to failures
		- Logging
			- DO/UNDO/REDO Log
		- Checkpoint: avoid aborting long txn / not recovery from a blank state
	- Consistency
		- Sequential Consistency: Every operation will follow a global order, and need to be visible to others.
			- Rule 1: 处理器必须按照程序指定的顺序发送请求
			- Rule 2: 针对同一个内存地址的请求需要按照FIFO的顺序服务（确保所有服务器看到的顺序都是一致的）
		- Release Consistency
			- sync when call `acquire()` and `release()`
		- Eventual Consistency
			- Optimistic/Performance/
	- Isolation - Concurrency Control
		- `2PL`: Serializable -> Through Lock -> how to acquire and release R/W Lock per item
		- `MVCC`(Snapshot Isolation)
			- Anomaly: Write Skew.
			- 2 Rooms (need 1 reserved) => 2 Txn concurrently book the rooms => all 2 rooms are booked.
	- Durability
- Distributed Commitment
	- Why need it?
	- How to do that?
		- Phase 1: Voting. Ask every participant `canCommit(T)`?
		- Phase 2: Committing. If **all** "Yes", `doCommit(T)`; else `doAbort(T)`
	- Issue?
		- Timeout on TC/Participant
		- Crash and Reboot
- Consensus Protocol: Paxos
	- How it works?

## Distributed Computation

