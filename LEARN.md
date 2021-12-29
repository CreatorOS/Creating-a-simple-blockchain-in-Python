# Creating a simple blockchain in Python
This quest describes a simple blockchain in Python. The aim of this quest is to introduce programmers who are looking to get into blockchain technology and are looking for a solid starting point where they can actually put some concepts into practice.

Let’s jump in directly.
## Imports
We’ll need to import some libraries to start out. So open up a new file in your preferred text editor and add the following:

```

from hashlib import sha256

import json

import time

```

The `sha256` function will give us the ability to compute hashes (we’ll see what this means in a bit). We want JSON for parsing our data and time is used for timestamping.
## Defining a class for our blockchain
Let’s use Python’s object-oriented features to our benefit. We’ll create a class that will represent our entire blockchain. You might ask at this point: *What is a blockchain?*

A blockchain is a ledger of “blocks” and blocks are a collection of transactions. Each block in the blockchain is linked to its predecessor. Let’s create a class that will serve as our blockchain:

```

class Chain:

    def __init__(self):

        self.blockchain = []

        self.pending = []

        self.add_block(prevhash="Genesis", proof=123)

```

`blockchain` is the actual list of our blocks and pending is the list of transactions that are yet to be added. Along with that, we have `self.add_block(prevhash="Genesis", 123)`. This is a bit cryptic, so let’s look at this carefully. We’re calling a method add_block (we’ll define the method later) that adds a new block to our blockchain. This is our *genesis block*, the first block in our chain.
## Adding a transaction
When we’re adding transactions to the blockchain, what we’re really doing is just to add it to the list of pending transactions and then after a few transactions have been added, we wrap them up in a block and put it on the blockchain. For example: Alice sends Bob 100 BTC, Charlie sends Alice 5 BTC and Bob sends Eve 45 BTC; all three of these transactions are added to the list of pending transactions (which will then be put in a block). A transaction will be marked as approved only after it has been added to a block.

Let’s write a method for adding a transaction to our list of pending transactions. The transaction will be a dictionary containing the name of the sender, recipient, and the amount to be sent. Add this to your `Chain` class:

```

def add_transaction(self, sender, recipient, amount):

    transaction = {

        "sender": sender,

        "recipient": recipient,

        "amount": amount

    }

    self.pending.append(transaction)

```

## Computing hashes

A hash is a fingerprint of some data. For example, if we pass “hello, world” to SHA-256 (a hashing algorithm), we might get something like 0xd34db33f. Since no two blocks will have the exact same transactions, we can use SHA-256 to create a unique identifier for the block. From now on, we can reference a certain block by using this ID called block hash.

def compute_hash(self, block):

    json_block = json.dumps(block, sort_keys=True).encode()

    curhash = sha256(json_block).hexdigest()

    return curhash

We’re getting a JSON representation of the data in the block and returning the hash of the JSON block.

## Adding blocks

This is the centerpiece of our Chain class. This adds a block to the chain and illustrates some of the central pieces of data we need.

```

def add_block(self, proof, prevhash=None):

    block = {

        "index": len(self.blockchain),

        "timestamp": time.time(),

        "transactions": self.pending,

        "proof": proof,

        "prevhash": prevhash or self.compute_hash(self.blockchain[-1])

    }

    self.pending = []

    self.blockchain.append(block)

```

The `block` dictionary stores the following:

1. `index`: The current index of the block in the blockchain (starting from 0).
2. `timestamp`: The timestamp of when this block was added.
3. `transactions`: The list of the pending transactions.
4. `proof`: This is a number that is used to roughly verify the validity of a block. The process of finding this proof is known as “mining.” We have decided to leave out mining in our blockchain since it requires a lot of computing power.
5. `prevhash`: This is the hash of the previous block in our blockchain. This is how we connect all the blocks in a chain, since each block just stores the hash of last.

Then, we empty the list of pending transactions since they’ve all been packed into our block. Finally, we append the block to our blockchain.

## Testing it out!

Add the following code to the end of the file.

```

chain = Chain()

t1 = chain.add_transaction("Vitalik", "Satoshi", 100)

t2 = chain.add_transaction("Satoshi", "Alice", 10)

t3 = chain.add_transaction("Alice", "Charlie", 34)

chain.add_block(12345)

t4 = chain.add_transaction("Bob", "Eve", 23)

t5 = chain.add_transaction("Dennis", "Brian", 3)

t6 = chain.add_transaction("Ken", "Doug", 88)

chain.add_block(6789)

print(chain.blockchain)

```

We’re declaring a blockchain, adding a few transactions and appending a few blocks to our blockchain. The output should look like the following mess:

```

[{'index': 0, 'timestamp': 1632420052.3950083, 'transactions': [], 'proof': 123, 'prevhash': 'Genesis'}, {'index': 1, 'timestamp': 1632420052.395048, 'transactions': [{'sender': 'Vitalik', 'recipient': 'Satoshi', 'amount': 100}, {'sender': 'Satoshi', 'recipient': 'Alice', 'amount': 10}, {'sender': 'Alice', 'recipient': 'Charlie', 'amount': 34}], 'proof': 12345, 'prevhash': '29b7a19fb6dd8dc4f9cd7eb304b51b30952bd2e6655101e8f656b48ce1b4c02c'}, {'index': 2, 'timestamp': 1632420052.395426, 'transactions': [{'sender': 'Bob', 'recipient': 'Eve', 'amount': 23}, {'sender': 'Dennis', 'recipient': 'Brian', 'amount': 3}, {'sender': 'Ken', 'recipient': 'Doug', 'amount': 88}], 'proof': 6789, 'prevhash': '3596ee87ced42041f5c4e1820c4e8c426e608ea061f1daa4bbac0a0f76cad41a'}]

```
## What next
Great now that you know some basic structure of blockchains, and some jargons associated, check out how the blockhashes change by making small edits to the transaction details - say the amount being sent.
