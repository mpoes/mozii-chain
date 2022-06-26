# 关于mozii-chain

智能合约及区块链技术应用探索试验平台链，抛弃金融区块链金融属性，让技术回归应用。

# 搭建mozii-chain（灵境）节点


## 1.安装geth并创建账户

```
pkg install -y go-ethereum
```
## 1.安装geth并创建账户

```
geth account new --datadir ./node
```

## 2.准备创世纪文件

```
{
  "config": {
    "chainId": 1949,
    "homesteadBlock": 0,
    "eip150Block": 0,
    "eip150Hash": "0x0000000000000000000000000000000000000000000000000000000000000000",
    "eip155Block": 0,
    "eip158Block": 0,
    "byzantiumBlock": 0,
    "constantinopleBlock": 0,
    "petersburgBlock": 0,
    "istanbulBlock": 0,
    "clique": {
      "period": 15,
      "epoch": 30000
    }
  },
  "nonce": "0x0",
  "timestamp": "0x62b58835",
  "extraData": "0x000000000000000000000000000000000000000000000000000000000000000079e18fb587605fa144476584e7c7820cc9ecda790000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
  "gasLimit": "0x47b760",
  "difficulty": "0x1",
  "mixHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
  "coinbase": "0x0000000000000000000000000000000000000000",
  "alloc": {
    "79e18fb587605fa144476584e7c7820cc9ecda79": {
      "balance": "1411780000000000000000000000"
    }
  },
  "number": "0x0",
  "gasUsed": "0x0",
  "parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
  "baseFeePerGas": null
}
```

## 3.初始化

```
geth --datadir ./node init mozii.org.json
```

## 4.启动节点

指定ip如`172.16.1.4`

```
geth --datadir node2 --maxpeers 3 --networkid 1949 --nodiscover --port '30300'  --ipcdisable  --http.port 8545 --http --http.api "admin,eth,txpool,personal,web3" --ws.port 8546 --port 30303 --allow-insecure-unlock --nat=extip:172.16.1.4 --syncmode full console
```

如果挖矿需要指定账户信息，如：

```
geth --datadir ./node --maxpeers 3 --networkid 1949 --nodiscover --port '30300' --ipcdisable  --miner.etherbase 0x79E18fB587605fa144476584e7C7820CC9eCda79 --unlock 0x79E18fB587605fa144476584e7C7820CC9eCda79   --mine  --http.port 8545 --http --http.api "admin,eth,txpool,personal,web3" --ws.port 8546 --port 30303 --allow-insecure-unlock --nat=extip:172.16.1.4 --syncmode full console
```

## 5.查看节点链接信息

```
> admin.nodeInfo.enode 
```
的到结果如下：
```
"enode://c478816a24cb304e9cfc09aa29f481ed9964acf6149804965c3420a2761ff5f28a89a580ea273f264f4209b6dfe5d6182083fe3a750e0dd7e5fc09d046a35d5a@172.16.1.4:30303?discport=0"
```


## 6.添加其他节点

admin.addPeer("enode信息")

例如:

```
admin.addPeer("enode://86c05cbebac1e900cee12c9d7972cc033ec949450a85cd9eee466f1ddafe15cc329b634d7f3cab2ad9edcb5d3a964607b3cb10dccdef51af53a94e986efb5991@47.242.20.105:30303?discport=0")
```

## 7.常用命令

```

查看链接节点
admin.peers

投票

clique.propose("0x79e18fb587605fa144476584e7c7820cc9ecda79",true)


clique.proposals

查询余额

web3.fromWei(eth.getBalance(eth.accounts[0]),"ether")

web3.fromWei(eth.getBalance("0x79E18fB587605fa144476584e7C7820CC9eCda79"),"ether")


查看当前挖矿账户

eth.coinbase

# 交互式控制台中

# 向第二个账户转账 5000 wei。报错 authentication needed: password or unlock。
eth.sendTransaction({'to': eth.accounts[1], 'from': eth.accounts[0], 'value': 5000})

# 转账前解锁
personal.unlockAccount(eth.accounts[0])

# 再转账则成功
eth.sendTransaction({'to': eth.accounts[1], 'from': eth.accounts[0], 'value': 5000})

# 开启挖矿对交易进行打包。打包一个区块之后就停止挖矿。（转账指令执行后进行一次挖矿，否则交易没有效果）
miner.start(1);admin.sleepBlocks(1);miner.stop();


#默认假设这个账户里有余额，想要给其他账户转账，先要进行账户解锁，最后一位的60是解锁时间，单位是秒，不填的话默认300，可以根据实际需要填写时间值

personal.unlockAccount(eth.accounts[0],'pwd',500);

personal.unlockAccount('79e18fb587605fa144476584e7c7820cc9ecda79');

#转账操作，转账1个eth，会消耗一定量的旷工费，测试多次结果为，旷工费是随机的，目前测试发现这种方式消耗旷工费很少，如果执行成功的话，会返回一串hash值，交易id，根据此id可以去区块浏览器中查询，查询时可能会发现，有的时候回出现pending状态，可能需要等待一段时间才会成功，也可能失败，失败的话，消耗的旷工费就不返还了

eth.sendTransaction({from:eth.accounts[0],to:eth.accounts[1],value:web3.toWei(10,“ether”)});


personal.unlockAccount(eth.accounts[0])


eth.sendTransaction({from:'79e18fb587605fa144476584e7c7820cc9ecda79',to:'0xeb1e25b9f245486e9939405d215157d277594d1a',value:web3.toWei(10,"ether")});


#查询交易

eth.getTransaction(“交易id”);
查看高度

eth.blockNumber
新建账户

personal.newAccount()
解锁账户挖矿

personal.unlockAccount(eth.accounts[0])
重新指定挖矿账户（默认挖矿账户为eth.accounts[1]）：

miner.setEtherbase(eth.accounts[0])
挖矿

miner.start():开始挖矿
miner.stop():停止挖矿
```
