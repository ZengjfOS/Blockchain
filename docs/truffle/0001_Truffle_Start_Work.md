# Truffle Start Work

## Refers

* [truffle serve 发生异常解决办法](http://blog.csdn.net/lovelvyan/article/details/77620054)
* [_pro$ sudo apt-get install automake autoconf](https://ubuntuforums.org/showthread.php?t=2293349)

## Truffle Init Project

```
desk@desk-ubuntu:~/blockchain/docs/truffle/truffle_pro$ truffle init
Downloading...
Unpacking...
Setting up...
Unbox successful. Sweet!

Commands:

  Compile:        truffle compile
  Migrate:        truffle migrate
  Test contracts: truffle test
desk@desk-ubuntu:~/blockchain/docs/truffle/truffle_pro$ tree
.
├── contracts
│   └── Migrations.sol
├── migrations
│   └── 1_initial_migration.js
├── test
├── truffle-config.js
└── truffle.js

3 directories, 4 files
desk@desk-ubuntu:~/blockchain/docs/truffle/truffle_pro$ truffle compile
Compiling ./contracts/Migrations.sol...
Writing artifacts to ./build/contracts

desk@desk-ubuntu:~/blockchain/docs/truffle/truffle_pro$ tree
.
├── build
│   └── contracts
│       └── Migrations.json
├── contracts
│   └── Migrations.sol
├── migrations
│   └── 1_initial_migration.js
├── test
├── truffle-config.js
└── truffle.js

5 directories, 5 files
```

Once completed, you'll now have a project structure with the following items:  
* contracts/: Directory for Solidity contracts
* migrations/: Directory for scriptable deployment files
* test/: Directory for test files for testing your application and contracts
* truffle.js: Truffle configuration file

## Start testrpc

```
desk@desk-ubuntu:~/blockchain/docs/truffle/truffle_pro$ testrpc 
EthereumJS TestRPC v6.0.3 (ganache-core: 2.0.2)

Available Accounts
==================
(0) 0x5776b834244ef5e9f788c4acd627c559f2a395f6
(1) 0x3a1aaf69e2167d0dc9d75619f192632f76e927c5
(2) 0x2596fa73e767b12dfdffb028417d0378e3a65fcf
(3) 0x6cb323738ceb699dc387118e7a28231f31b29d3c
(4) 0x427a34c3e4286ad3c96ff7e28eb0897fe11304e9
(5) 0x2b19550fed9f380d3399bd17db25a3a6cd30c26d
(6) 0xbf51b76c08653d22e02eff60f20c4d0938f3c390
(7) 0xef52484b7413ec2e62b788011b52fe40d8f66a1b
(8) 0x81476d610f6c292cc10b381d8b556bb814356a01
(9) 0x20908a7e7c925933f123981a60307d317042df01

Private Keys
==================
(0) 9b7be695f658b8c77f761083d475c1da7cca6fbfed99384c5e74ede0afe9f6d8
(1) 1b5b9166d17fd982bf8ef901de2024f1f5a29176ce1ec326d0b84dc4d856b5a9
(2) 860a956441adb6ea7ff779cae97e54a5623df76d2ed48f2584ae01fd59b37314
(3) 482f6861f75c7bd0d2422c5cd3bf487bf5a5ffdcb5e518d99331690137a4b5b5
(4) ae4c1d22a159e733c495f9a2acee13ad10ed387c117719f3ca94c184dc3a5863
(5) 546617013a800087836e68bf8767ecb9e028e084cb4da001c44c41fb9b3ffcd2
(6) 593ef7dc7111ad8db1e66c866959cd3b8ec87c6d7133961c2e1ceaec54b2db7d
(7) 037ae7919445319059816e32c75e9dbdea9b70592121a3a71fad7f045ae0c74c
(8) d91cb0a5d2fe2dca0a8ab0a5baf9b8f8aa2c61bb1baeccd3fb21a49e00af1106
(9) 72455d9e268aeb88ac4a0dca9d328abb076a4d7f6ff7a69d56e95869f2aea41f

HD Wallet
==================
Mnemonic:      essence legend fluid sense melt mutual rocket tortoise track inner shop torch
Base HD Path:  m/44'/60'/0'/0/{account_index}

Listening on localhost:8545
```

## Deploy Project

```
desk@desk-ubuntu:~/blockchain/docs/truffle/truffle_pro$ cat truffle.js 
module.exports = {
  // See <http://truffleframework.com/docs/advanced/configuration>
  // to customize your Truffle configuration!
   networks: {
     development: {
       host: "localhost",
       port: 8545,
       network_id: "*" // Match any network id
     }
   }
};
desk@desk-ubuntu:~/blockchain/docs/truffle/truffle_pro$ truffle deploy
Using network 'development'.

Network up to date.
desk@desk-ubuntu:~/blockchain/docs/truffle/truffle_pro$ 
```
