<H1> Blockchain Visualization in the Wolfram Language
<H2>Introduction</H2>
<p>
In its most basic form, a blockchain is a type of a decentralised 
database that keeps records of digital transactions. Rather than having 
a central administrator, a blockchain has a network of identical 
databases (nodes), kept in sync courtesy of the internet in a peer-to-peer fashion and potentially visible to anyone 
within the specific network. Also, blockchains can be made to either be 
private or public, depending on the use. For example, the Bitcoin network 
is a public blockchain, and the one I present below, is a private one.
</p>

> “A blockchain is a magic computer that anyone can upload programs to 
and leave the programs to self-execute, where the current and all 
previous states of every program are always publicly visible, and 
which carries a very strong crypto economically secured guarantee that 
programs running on the chain will continue to execute in exactly the 
way that the blockchain protocol specifies.”  ~  Vitalik Buterin

<p>
A blockchain network's decentralised, open & cryptographic nature allow 
people to trust each other and transact peer to peer, making the need for 
intermediaries obsolete. This also brings unprecedented security benefits. 
Hacking attacks that commonly impact large centralised intermediaries like
 banks would be very difficult or cost ineffective to pull off on the blockchain. 
 For example  -  if someone wanted to modify a particular confirmed block in a 
 blockchain, a hacker would not only need to attack that specific 
 block, but all of the proceeding blocks going back the entire history and most nodes containing copies of the blockchain, which could be thousands, simultaneously. 
 Not an easy task, by any means.
</p>
<H2> The Project </H2>
<H4> Mining </H4>
<p>
Usually, in blockchains, some nodes crunch data to solve seemingly 
pointless complicated cryptic problems. For example, in the Bitcoin 
Blockchain, the problem is to take the hash (using SHA-256) of the data supplied and the previous block' s hash along with a value, called the nonce, such that the new 
hash has 17 leading zeroes. This pointless problem, i.e. finding the correct nonce, is very 
taxing on the system used to compute it, and so the node which finds 
the hash first, earns bitcoins. This process is called Mining. 
</p>
Now, the code for the Mining function is simple:

```
While[StringTake[IntegerString[Hash[prevHash<>ToString[data]<>ToString[nonce],"SHA256"],16,64],StringLength[patt]]=!=patt), nonce++;];
```

<H4> Keeping Copies </H4>
Now, a blockchain stores copies of the data as files in it's server, which is sent across all nodes. For now, let's stick to the saving part. For this simulation, I save it as a list, which can be easily read with the Wolfram Language. It consists of the number of the node which solved it, the current hash, the nonce, and the data in calculation. But, since we'll have to save many files like these, we need to differentiate them by Timestamp. Here's how we do it: 

    Save[FileNameJoin[{"/Users/Wolfram/Blockchain \Network/Nodes/1", ToString@UnixTime[]}], Flatten[List[blockNumber, currHash, nonce, data]];]
Importing them back can be done in a similar fashion: 

    block = ToExpression[Import[Last[FileNames["*", "/Users/Wolfram/Blockchain \Network/Nodes/1"]]]]
The final result:
 
![File snap][1]

But, while this is all well and good, the following problems rise: how do we make different nodes? How do we get them talk? Where do we get the data from?
<H4> Using Kernels </H4>
<p>Mathematica provides a clean way to run code in isolated entities - using Kernels. It's easy to make some - Under Evaluation on the menu bar, go to Kernel Configuration Options, and add as many as your license allows! To switch the Notebook's Kernel, there's a Notebook's Kernel option under Evaluation - which does the trick. </p>
<H4> Using Channels </H4>
<p> Now, we have Kernels - but they still need to talk. Wolfram Language has a Channel Framework built - in, which makes such communications very simple. Using Channels, you can create a connection between the kernels, and also different computers, through which we can send and receive messages which remain in the Wolfram Language format. That means, we can easily send lists over intact, without converting them to and from strings, which makes inter - kernel node communication convenient. 
<H4> Nodes and Channels in the project </H4> <p>
In this project, I used 2 types of nodes, a Computational node and a Guest node. A computational node is the one which does the actual mining, while the Guest Node supplies data to the computational nodes once they're done. Now, we'd need three kinds of channels for the inter - kernel communication - one to send the data from the Guest Nodes to the computational nodes, another to tell the Guest Nodes to send the data, and one more for a computational nodes to tell the others when it's done.  Once this was figured out, it was all a matter of compiling the ideas and converting them into code. 
<H4>The End Result </H4>
  
  For testing purposes, I used 3 computational nodes and one guest node. Here's a shot of my setup: ![My Setup][2]

The screengrab below is of the Guest Node's GUI. The graph shows which node was the first to compute in green, and soon as that happens, the memory pool is released to the nodes, and they keep on working, forever.

![The Guest Node GUI in Action][3]


Feel free to clone this repo and run your own blockchains!

P.S. Try aborting one of the nodes while it's running, and see what happens to the others. (For the lazy, nothing happens. It's a blockchain, it remains intact.)

  [1]: http://community.wolfram.com//c/portal/getImageAttachment?filename=File.png&userId=1138525
  [2]: http://community.wolfram.com//c/portal/getImageAttachment?filename=SCree.png&userId=1138525
  [3]: http://community.wolfram.com//c/portal/getImageAttachment?filename=GuestNode.png&userId=1138525
