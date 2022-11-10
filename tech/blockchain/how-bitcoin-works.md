---
title: How Bitcoin works
description: Some notes about bitcoin
date: 2018-10-15

tags:
- bitcoin
---

## Bitcoin

### Coins

* A coin is not an integer, it is often a fraction, because they are tied to real-world monetary value.
* But it cannot be a completely real number (since computers are discrete).
* In reality, the smallest quantum is 8 decimal places i.e. 0.00000001 bitcoin. also known a "satoshi"

Does this affect the real-world value of a bitcoin?

### Transactions

* A bitcoin transaction consists of:
  * an input - a pointer to a previous transaction (hence "chain"), signed by the sender. The signature is based on elliptic curve cryptography (which is a public/private key crypto approach)
  * a list of outputs - the recipients & amounts the sender is sending the money to
* The outputs are the basis for future transactions.
* Transactions form a DAG, but the transactions themselves do not form the blockchain.

It can be seen that unless there are transactions to consolidate the bitcoins in a particular person's wallet, it can be quite difficult to follow the flow of bitcoins as it splits and merges over a sequence of transactions.

The transaction may consist of an amount sent to the recipient, and the "change" going back to the sender.

### Users

* Users are identified by their addresses which are related to their public keys.
* Loss of the public keys means the user cannot access those bitcoins (frozen assets?)
* Addresses are encoded in Base58Check which minimises risk of mistypes.

### Mining

Mining is how new coins are injected into the system (economy?)

*How to prevent inflation over time?*

### Double-Spend Problem

* How to ensure that each coin is spent only once?
  * The traditional solution is to have a central authority that ensures this e.g. MasterCard, PayPal
  * In BitCoin, the network ("economy") needs to self-govern. This is what gives rise to the "blockchain".
  * The network is made up of many nodes that process the transactions. It doesn't matter who runs the nodes. Each node has all the transactions.
  * New transactions are shared with other nodes.
  * Only the "miner" nodes can update the blockchain.
    * Each miner validates the new transactions (e.g. input and outputs are equal, signatures valid, etc)
    * Multiple valid transactions are inserted into a list - this is the "block"
      * The block is sized at 1 Mb (there have been attempts to double the size but so far these have failed)
    * But there are so many miners and they may not agree on the "block" and its contents.
    * So they have to compete for the right to add the next block to the blockchain. They get some coins as a reward for their hard work (and this grows the "economy")
      * A nonce is added to the miner's "candidate block", and calculate the hash for the (block + nonce).
      * Criteria for winning: find a hash value smaller than a given target value
      * Restatement of the problem: find a nonce such that the hash of (block + nonce) is smaller than target value
      
Mathematically can there be no solution to the winning criteria?
    
Can transactions be lost somehow here by the miners?
* Of course. It's like an uncleared transaction. You'll have to redo the transaction then (probably with a lot of anguish along the way)

Can there be multiple winners to each iteration?
* Yes. To resolve this, a second race is initiated to find the next block. 
* The winner of the next race will determine the winner to the previous race.
* (in theory, this can happen ad infinitum, but very very very very unlikely...)
* The entire network will only build on the longest chain.
* Only when the chain is confirmed will the miners get their reward, so there is incentive for the miners to be honest.
* As long as majority of miners are honest, the blockchain's integrity is safe.
* The only way then for a rogue miner to control the network is to control a majority of the miner nodes (i.e. more dishonest nodes than honest ones - this may become a reality in future as costs become too prohibitive for miners to continue?).

### Controlling the injection of new bitcoins

* Network will automatically lower max block hash value, to maintain steady rate of 6 blocks per hour (or about 1 block every 10 min), regardless of how much computing power the network has
* So the rate of growth of the bitcoin economy is actually predictable.

Mathematically, is it guaranteed that lowering the max block hash value makes it more difficult to find a winning block?
* Intuitively, it should. The total possible solutions would be reduced. It's got nothing to do with whether the max value is smaller or not - it's more about the number of possible solutions i.e. the size of the bullseye.
* But I don't know how to prove this mathematically...

* Also, the rewards are pre-programmed to go down over time
* Miners' reward was 50 bitcoins per block in the beginning in 2009 when bitcoin was launched
* In 2012, this was reduced to 25
* In 2016, this was reduced to 12.5
* ...basically having every 4 years
* eventually, the reward will be insignificant and only transactions fees can support the miners
* But transaction fees do not grow the economy - it's just existing money circulating around

### Transaction processing rate

* Since the block size is ~1 Mb and the size of a transation is about 500 bytes, each block can hold only about 2k transactions
* At 1 block every 10 min, this works out to only 2k txns / 600 secs, or about 3.33 txns/sec.

### Cost of maintaining the bitcoin network

* As mentioned above, the bitcoin network throttles the rate of blocks (and hence new bitcoins) injected into the network - when more miners participate in the mining, the network will make the winning criteria harder, and vice-versa. However, due to the rewards, the miners will try (as long as the price of bitcoin is high enough to justify the costs)
* This means a lot of computing power is wasted in what basically amounts to a game of dice rolling.
* However, the sheer collective computing power of the miners is a safeguard against the 51 percent attack.

### "Improving" bitcoin

* To increase the transaction processing rate, a "obvious" solution is to simply increase the block size.
  * However, larger block sizes means more storage needed for bitcoin nodes to operate
    * Of course, it is a matter of time before the network reaches that size, but the argument against is primarily based on current operating costs (and of course, some people don't agree that it'll be too expensive)
    * This could start a slippery slope of increasing block size everytime we need to increase processing throughput. And this will quickly squeeze out the smaller players, leaving the bitcoin network in the hands of a few large organisations.
    * The "big-blockers" eventually forked off Bitcoin Cash. It is currently not as valuable as the original Bitcoin network, but supporters believe their vision will hold out in the end.
* Without increasing the block size, one way is to move the crypto signatures out of the block so that more transactions can fit into the block
  * This technique is called "segregated witness", where the signatures are the "witness data".
  * This has been adopted by the network since Sept 2017
* For longer term, the Lightning network was created to be layered on top of bitcoin.
  * Basic idea is to have faster moving network on top the slower moving (legacy?) bitcoin network.
  * The bitcoin network thus becomes more of a "settlement layer"

### Bitcoin Exchanges

They actually have a purpose (beyond providing a place for people to speculate prices)

More to come...

### References

* https://arstechnica.com/tech-policy/2017/12/how-bitcoin-works/
