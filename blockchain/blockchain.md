##Block Chain##
It's been invented by Satoshi Nakamoto in 2008 to serve as the public ledger of the cryptocurrency bitcoin.
A original blockchain is a growing list of records,an linked,ordered and timestamped blocks.Basiclly,it may be considered as a kind of storage mechanism,which is distributed,immutable and decentralized.

**Byzantine fault tolerance(BFT)**
It's the dependability of a fault-tolerant computer system,particularly distributed computing systems,where components may fail and there is imperfect information on whether a component has failed.In 'Byzantine failure',a component such as a server can inconsistently appear both failed and functioning to failure-detection system,presenting different symptoms to different observers.
Basiclly,a Byzantine fault is any fault presenting different symptoms to different observers.

**Consensus**
When several nodes all have the same blocks in their blockchain,they're considered to be in consensus.The validation rules these nodes follow to maintain consensus are called **consensus rules**.

**Transaction Data**
Every block must include one or more transactions.The first one is called a generation transaction,which should collect and spend the block reward.Each transaction has input and output,output cannot exceed input,but input can exceed output,the difference between the value may be claimed as a transaction fee by the Bitcoin miner who create the block.
The UTXO(Unspent Transaction Output) of a coinbase cannot be spent for at least 100 blocks,this temporarily prevents miner from spending the transaction fees and block reward from a block that may later be determined to be stale after a chain fork.

**Fork**
Hard fork:
A change of rules that creates blocks recognized as invalid by the old software.

Soft fork:
A change of rules that creates blocks recognized as valid by the old software,it is backwards-compatible.

##Transaction##



The most majoy fucntion of blockchain:
1.Saving data,using blockchain as a time-line database,based on 