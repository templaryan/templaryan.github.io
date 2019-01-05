---
layout: post
title: Distributed Systems Engineers and Blockchain
---
by Rajesh Nair

## [Why aren’t distributed systems engineers working on blockchain technology?](https://eng.paxos.com/why-arent-distributed-systems-engineers-working-on-blockchain-technology)

![Why aren’t distributed engineers working on blockchain technology?](/images/5a7a9af2155740c9a76988ea701d9656.png "Why aren’t distributed engineers working on blockchain technology?")

For the last 15 years, I have predominantly worked on Distributed Systems of various size and complexity. [Over the last couple of years, I have become more interested in blockchain or Distributed Ledger Technology (DLT)](https://eng.paxos.com/the-blockchain-is-evolutionary-not-revolutionary). Coming from a Distributed Systems background, it seemed natural to me to understand more about this innovative technology. So it’s surprising that, although there has been a lot of buzz around the crypto community about DLT, the Distributed Systems community has largely remained away from the DLT buzz. A few reasons I can think of for this distance:

1.  Distributed Systems engineers are usually dealing with extremely large amounts of data. In comparison 3 txs/second on the Bitcoin blockchain seems very miniscule.
2.  [BFT](https://en.wikipedia.org/wiki/Byzantine_fault_tolerance) consensus algorithms are not widely used(or popular) in the industry. I will touch upon this later in the blog.
3.  The blockchain community is immature and historically unwelcoming.
4.  It feels like a get rich quick scheme - [https://twitter.com/naval/status/878018839044161536](https://twitter.com/naval/status/878018839044161536)

In this article, I will explore why DLT is an extremely interesting technology for Distributed Systems engineers.

_Note: Throughout this post, I will be using the terms “blockchain” and “distributed ledger technology (DLT)” interchangeably._

  

## Commonalities between DLT & Distributed Systems

A lot of design principles used in DLT are also common design patterns in Distributed Systems:

![icon-immutable](/images/56f5001aaf994ec99b9a9ef785f10d39.png "icon-immutable")

#### Immutability

Blockchains are immutable. And for quite some time, distributed systems have relied on immutability to eliminate anomalies. [Log-structured file system](https://en.wikipedia.org/wiki/Log-structured_file_system), [log-structured merge-trees](https://en.wikipedia.org/wiki/Log-structured_merge-tree), and [Copy-On-Write](https://en.wikipedia.org/wiki/Copy-on-write) are common patterns/tricks used in Distributed Systems to model immutable data structures. Blockchains handle transactions in a similar way to [event sourcing](https://docs.microsoft.com/en-us/azure/architecture/patterns/event-sourcing), the common technique used in Distributed Computing to handle facts and actions. Instead of overwriting data, you create an append-only log of all facts/actions that ever happened.

Pat Helland described the importance of immutability in his popular paper [Immutability Changes Everything](http://queue.acm.org/detail.cfm?id=2884038):

> _Accountants don't use erasers; otherwise they may go to jail. All entries in a ledger remain in the ledger. Corrections can be made but only by making new entries in the ledger. When a company's quarterly results are published, they include small corrections to the previous quarter. Small fixes are OK. They are append-only, too._

Blockchains are simply distributed accounting ledgers, hence the name Distributed Ledger Technology.

![Asynchrony](/images/c1cf5f04aac249ecaa5920bcc47b49a7.png "Asynchrony")

#### Asynchrony

Blockchains are designed to run on commodity machines that may be thousands of miles apart. Arriving at a common history of transaction order in this kind of asynchronous network is the classic Distributed Computing problem that Distributed Systems Engineers deal with. All the impossibility results in Distributed Systems like [FLP](https://groups.csail.mit.edu/tds/papers/Lynch/jacm85.pdf) and [CAP](https://groups.csail.mit.edu/tds/papers/Gilbert/Brewer2.pdf) apply to blockchain since it is also a Distributed System.

Much like in Distributed Systems, in blockchains [there is no “now"](http://queue.acm.org/detail.cfm?id=2745385). Clocks of different nodes in a distributed system can drift apart. Thus it is not at all trivial to reason about the global real time ordering of events across all the machines in the cluster. Machine local timestamps will no longer help here since the clocks are no longer assumed to be always in sync. In addition to this, messages can be delayed for arbitrary period of times. The time lag between writing a packet onto the wire to receiving it on the other end could be on the order of milliseconds, seconds, minutes or even days. For the bitcoin blockchain, Satoshi devised a clever way of ordering transactions to prevent the problem of double spending, in the absence of a global clock using a [Distributed Timestamp Server](https://bitcoin.org/bitcoin.pdf). Quoting Satoshi from his [Bitcoin whitepaper](https://bitcoin.org/bitcoin.pdf):

> _The solution we propose begins with a timestamp server. A timestamp server works by taking a hash of a block of items to be timestamped and widely publishing the hash, such as in a newspaper or Usenet post. The timestamp proves that the data must have existed at the time, obviously, in order to get into the hash. Each timestamp includes the previous timestamp in its hash, forming a chain, with each additional timestamp reinforcing the ones before it._

This is similar to a DBMS (database management system) in which transaction logs record all writes to the database. To that extent, blockchain is essentially a Distributed [Transaction Log](https://en.wikipedia.org/wiki/Transaction_log).

  

#### ![consensus](/images/6cb89a21a8c54bddbe3d6943c63ff8bc.png "consensus")

#### Consensus

In the absence of a globally synchronized clock, the only way to decide on the order of transactions is through Distributed Consensus. Coming to consensus on the ordering of events/transactions across distributed machines can be thought of as a logical surrogate for “now”. But coming to consensus in a Distributed System is hard:

> _The_ [_FLP_](https://groups.csail.mit.edu/tds/papers/Lynch/jacm85.pdf) _impossibility result states that in an asynchronous network where messages may be delayed but not lost, there is no consensus algorithm that is guaranteed to terminate in every execution for all starting conditions, if at least one node may fail-stop._

Crash Fault Tolerant consensus algorithms like [Paxos](http://lamport.azurewebsites.net/pubs/lamport-paxos.pdf), [Zab](https://pdfs.semanticscholar.org/b02c/6b00bd5dbdbd951fddb00b906c82fa80f0b3.pdf), [Raft](https://raft.github.io/raft.pdf), [Viewstamped Replication](http://www.pmg.csail.mit.edu/papers/vr.pdf) are all too common in Distributed Systems literature and every major Distributed database or filesystem out there is using one (or a variant) of these algorithms. These crash fault tolerant algorithms are modeled to handle consensus in scenarios where processes or machines can crash or cause delays in message delivery. The aforementioned algorithms are often used in Distributed Systems that are run by one organization.

Blockchains work in much more adversarial conditions and are modeled to handle failures known as the [Byzantine Generals’ Problem](https://en.wikipedia.org/wiki/Byzantine_fault_tolerance#Byzantine_Generals.27_Problem), where some of the nodes could be malicious. This is possible since the nodes are run by different entities/organizations that do not trust each other. Blockchains are modeled under the assumption that your own network is out to get you. Hence you need [Byzantine Fault Tolerant](https://en.wikipedia.org/wiki/Byzantine_fault_tolerance) algorithms to achieve consensus in blockchains. Byzantine Fault Tolerance has been studied in Distributed Systems literature for a long time. In 1999, Miguel Castro and [Barbara Liskov](https://en.wikipedia.org/wiki/Barbara_Liskov) introduced the [Practical Byzantine Fault Tolerance](http://pmg.csail.mit.edu/papers/osdi99.pdf) (PBFT) algorithm, which provides high-performance Byzantine state machine replication, processing thousands of requests per second with sub-millisecond increases in latency. Although the paper was written in 1999, there were no known practical implementations of BFT algorithms until Bitcoin came along in 2008 with the [Proof Of Work](https://en.wikipedia.org/wiki/Proof-of-work_system) algorithm; although the algorithm itself is old and was used in other systems such as [Hashcash](https://en.wikipedia.org/wiki/Hashcash) to limit email spam. The Blockchain has created a renewed interest in BFT algorithms and a whole slew of new BFT algorithms are being researched very actively in academic circles. Some examples include [Proof Of Stake](https://en.wikipedia.org/wiki/Proof-of-stake), [Bitcoin-NG](https://arxiv.org/abs/1510.02037), [Tendermint](https://tendermint.com/static/docs/tendermint.pdf) and [Honey Badger](https://eprint.iacr.org/2016/199.pdf).

  

#### ![network reliability](/images/f3fd9b7dbc7b4fd38b4111d3f26ebdb3.png "network reliability")

#### Network reliability

Contrary to popular beliefs, the [network is _not_ reliable](http://queue.acm.org/detail.cfm?id=2655736). Distributed Systems engineers have to deal with this cold hard fact. Bitcoin and other crypto currencies were built to work over the internet, where network partitions and message loss/reordering are common. Interestingly the blockchain data structure itself is a clever way to detect message loss and reordering. Every block has a pointer back to the previous block similar to a linked list which makes it easy to detect missing blocks( “orphaned blocks” in blockchain lingo). Quoting Satoshi again:

> _New transaction broadcasts do not necessarily need to reach all nodes. As long as they reach many nodes, they will get into a block before long. Block broadcasts are also tolerant of dropped messages. If a node does not receive a block, it will request it when it receives the next block and realizes it missed one._

This is similar in principle to replicating the transaction log or log shipping which is a common technique used to keep replicas(especially read-only replicas) synchronized. As transaction logs are ordered, they provide an easy mechanism to detect gaps and repair replicas. Similarly, the integrity of every block in a blockchain can be validated by checking the merkle root of the block which is a hash of all the transactions in the block. So it’s easy to detect a missing transaction_._ As a reminder, merkle trees are a very common technique used in [anti-entropy](http://www.allthingsdistributed.com/2007/10/amazons_dynamo.html) repair for replica synchronization.

* * *

Blockchains are an exciting technological breakthrough. For the first time ever, you can have a distributed database across entities that do _not_ trust each other. We are still in early days of this interesting technology. In many ways, it’s similar to writing the first distributed NoSQL database like Amazon’s Dynamo or Google’s BigTable. These distributed databases showed us a new way to build large scale databases and opened our eyes to new design patterns and data structures. NoSQL databases have now become commoditized. If you hear about a new NoSQL database, 90% of the patterns and algorithms used are the same. DLTs are going through a similar phase, and they will become commoditized eventually. But these are still early days and we are discovering the best patterns for building them.

The blockchain community has attracted a number of folks from the crypto community, but has relatively few Distributed Systems Engineers. I wrote this post to give a brief overview of the challenges facing DLT engineers and would like to call upon all the Distributed Systems Engineers to get excited about DLTs and start contributing. If you would like to be part of building the next generation DLT and invent the new algorithms and patterns that will be used for years to come, [join us](https://www.paxos.com/careers/full-stack-engineer-bankchain).

![](/images/c33bb833b73c429daa7607f0a4960f30.png "end"){:height="20%" width="20%"}

---
*Originally obtained from [Paxos Engineering](https://eng.paxos.com/why-arent-distributed-systems-engineers-working-on-blockchain-technology)*

