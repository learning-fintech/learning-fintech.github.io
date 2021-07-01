---
layout: page
title: The ether_real_estate Package
---

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/1JrxzION-IYhCAkk3uGm8ncADZckmTfs2?usp=sharing)

This software simulates a game in which players purchase properities that rent out real estate to businesses.  When leases expire, players submit bids to the firm for a new lease, and the winning player adds the firm to one of their properties for the duration of the lease.

All transactions take place on a private Ethereum blockchain, which allows students to explore blockchain technology as a ledger for recording transactions.

## Loading the Game
The key blockchain component of the game utilizes the [eth-tester](https://github.com/ethereum/eth-tester) module.
The module makes use of the [pyEVM](https://github.com/ethereum/py-evm) backend, which is currently listed in an experimental (alpha) stage.
The `eth-tester` module is Linux-only for now.
There are ways to run the game on Windows by interfacing with something like [Ganache](https://www.trufflesuite.com/ganache) in the Truffle Suite for Ethereum development, and a later version of `ether_real_estate` will enable this functionality.

Install the following packages from PyPI.  Because `ether_real_estate` and some of its core dependencies (`eth-hash`, `eth-tester`, `py-evm`) are in early development, the `ether_real_estate` module is picky about version numbering for dependencies.  Create a virtual environment if you want to use other versions of the dependency packages for other projects that will not make use of `ether_real_estate`.


```python
!pip install ether_real_estate
!pip install eth-hash[pycryptodome]
```

    Requirement already satisfied: ether_real_estate in /mnt/software/anaconda3/lib/python3.8/site-packages (1.1)
    Requirement already satisfied: eth-hash==0.3.1 in /mnt/software/anaconda3/lib/python3.8/site-packages (from ether_real_estate) (0.3.1)
    Requirement already satisfied: pandas in /mnt/software/anaconda3/lib/python3.8/site-packages (from ether_real_estate) (1.2.2)
    Requirement already satisfied: numpy in /mnt/software/anaconda3/lib/python3.8/site-packages (from ether_real_estate) (1.19.2)
    Requirement already satisfied: py-evm==0.4.0a4 in /mnt/software/anaconda3/lib/python3.8/site-packages (from ether_real_estate) (0.4.0a4)
    Requirement already satisfied: eth-tester==0.5.0b4 in /mnt/software/anaconda3/lib/python3.8/site-packages (from ether_real_estate) (0.5.0b4)
    Requirement already satisfied: eth-utils in /mnt/software/anaconda3/lib/python3.8/site-packages (from ether_real_estate) (1.10.0)
    Requirement already satisfied: eth-keys<0.4.0,>=0.2.1 in /mnt/software/anaconda3/lib/python3.8/site-packages (from eth-tester==0.5.0b4->ether_real_estate) (0.3.3)
    Requirement already satisfied: rlp<3,>=1.1.0 in /mnt/software/anaconda3/lib/python3.8/site-packages (from eth-tester==0.5.0b4->ether_real_estate) (2.0.1)
    Requirement already satisfied: semantic-version<3.0.0,>=2.6.0 in /mnt/software/anaconda3/lib/python3.8/site-packages (from eth-tester==0.5.0b4->ether_real_estate) (2.8.5)
    Requirement already satisfied: eth-abi<3.0.0,>=2.0.0b4 in /mnt/software/anaconda3/lib/python3.8/site-packages (from eth-tester==0.5.0b4->ether_real_estate) (2.1.1)
    Requirement already satisfied: cached-property<2,>=1.5.1 in /mnt/software/anaconda3/lib/python3.8/site-packages (from py-evm==0.4.0a4->ether_real_estate) (1.5.2)
    Requirement already satisfied: lru-dict>=1.1.6 in /mnt/software/anaconda3/lib/python3.8/site-packages (from py-evm==0.4.0a4->ether_real_estate) (1.1.7)
    Requirement already satisfied: mypy-extensions<1.0.0,>=0.4.1 in /mnt/software/anaconda3/lib/python3.8/site-packages (from py-evm==0.4.0a4->ether_real_estate) (0.4.3)
    Requirement already satisfied: pyethash<1.0.0,>=0.1.27 in /mnt/software/anaconda3/lib/python3.8/site-packages (from py-evm==0.4.0a4->ether_real_estate) (0.1.27)
    Requirement already satisfied: eth-bloom<2.0.0,>=1.0.3 in /mnt/software/anaconda3/lib/python3.8/site-packages (from py-evm==0.4.0a4->ether_real_estate) (1.0.4)
    Requirement already satisfied: trie==2.0.0-alpha.5 in /mnt/software/anaconda3/lib/python3.8/site-packages (from py-evm==0.4.0a4->ether_real_estate) (2.0.0a5)
    Requirement already satisfied: py-ecc<5.0.0,>=1.4.7 in /mnt/software/anaconda3/lib/python3.8/site-packages (from py-evm==0.4.0a4->ether_real_estate) (4.1.0)
    Requirement already satisfied: blake2b-py<0.2,>=0.1.4 in /mnt/software/anaconda3/lib/python3.8/site-packages (from py-evm==0.4.0a4->ether_real_estate) (0.1.4)
    Requirement already satisfied: eth-typing<3.0.0,>=2.2.0 in /mnt/software/anaconda3/lib/python3.8/site-packages (from py-evm==0.4.0a4->ether_real_estate) (2.2.2)
    Requirement already satisfied: typing-extensions<4,>=3.7.4 in /mnt/software/anaconda3/lib/python3.8/site-packages (from trie==2.0.0-alpha.5->py-evm==0.4.0a4->ether_real_estate) (3.7.4.3)
    Requirement already satisfied: sortedcontainers<3,>=2.1.0 in /mnt/software/anaconda3/lib/python3.8/site-packages (from trie==2.0.0-alpha.5->py-evm==0.4.0a4->ether_real_estate) (2.3.0)
    Requirement already satisfied: hexbytes<0.3.0,>=0.2.0 in /mnt/software/anaconda3/lib/python3.8/site-packages (from trie==2.0.0-alpha.5->py-evm==0.4.0a4->ether_real_estate) (0.2.1)
    Requirement already satisfied: parsimonious<0.9.0,>=0.8.0 in /mnt/software/anaconda3/lib/python3.8/site-packages (from eth-abi<3.0.0,>=2.0.0b4->eth-tester==0.5.0b4->ether_real_estate) (0.8.1)
    Requirement already satisfied: pycryptodome<4,>=3.6.6 in /mnt/software/anaconda3/lib/python3.8/site-packages (from eth-hash==0.3.1->ether_real_estate) (3.10.1)
    Requirement already satisfied: cytoolz<1.0.0,>=0.10.1 in /mnt/software/anaconda3/lib/python3.8/site-packages (from eth-utils->ether_real_estate) (0.11.0)
    Requirement already satisfied: toolz>=0.8.0 in /mnt/software/anaconda3/lib/python3.8/site-packages (from cytoolz<1.0.0,>=0.10.1->eth-utils->ether_real_estate) (0.11.1)
    Requirement already satisfied: six>=1.9.0 in /mnt/software/anaconda3/lib/python3.8/site-packages (from parsimonious<0.9.0,>=0.8.0->eth-abi<3.0.0,>=2.0.0b4->eth-tester==0.5.0b4->ether_real_estate) (1.15.0)
    Requirement already satisfied: python-dateutil>=2.7.3 in /mnt/software/anaconda3/lib/python3.8/site-packages (from pandas->ether_real_estate) (2.8.1)
    Requirement already satisfied: pytz>=2017.3 in /mnt/software/anaconda3/lib/python3.8/site-packages (from pandas->ether_real_estate) (2021.1)
    Requirement already satisfied: eth-hash[pycryptodome] in /mnt/software/anaconda3/lib/python3.8/site-packages (0.3.1)
    Requirement already satisfied: eth-utils<2,>=1 in /mnt/software/anaconda3/lib/python3.8/site-packages (from eth-hash[pycryptodome]) (1.10.0)
    Requirement already satisfied: pycryptodome<4,>=3.6.6 in /mnt/software/anaconda3/lib/python3.8/site-packages (from eth-hash[pycryptodome]) (3.10.1)
    Requirement already satisfied: eth-typing<3.0.0,>=2.2.1 in /mnt/software/anaconda3/lib/python3.8/site-packages (from eth-utils<2,>=1->eth-hash[pycryptodome]) (2.2.2)
    Requirement already satisfied: cytoolz<1.0.0,>=0.10.1 in /mnt/software/anaconda3/lib/python3.8/site-packages (from eth-utils<2,>=1->eth-hash[pycryptodome]) (0.11.0)
    Requirement already satisfied: toolz>=0.8.0 in /mnt/software/anaconda3/lib/python3.8/site-packages (from cytoolz<1.0.0,>=0.10.1->eth-utils<2,>=1->eth-hash[pycryptodome]) (0.11.1)


Then load the module.


```python
import ether_real_estate
```

A couple notes about how the game runs:
* Transactions are handled by the game directly.  In a later version, transactions will be auto-enforced via smart contracts in Vyper so that students can learn about that aspect of blockchain development.
* Gas prices are currently set to zero to keep things simple.  Variable gas prices will be added later to simulate transaction costs.

The game will save/read data from a user-specified directory.

## Instantiating the Game
A new `Game` object, `g`, is created below.  Options for the `Game` object are:
* `num_industry` (default = `5`)
  * Number of industries in the game.  Firm cash flows are partially correlated within an industry.
* `num_players` (default = `5`)
  * Number of human players in the game.  This should be set to either the number of students in the class or the number of groups in the class (if working in teams).
* `num_ai' (default = `0`)
  * Number of AI players in the game.
  * AI players can comprise the full user list if testing the game.
  * Alternatively, AIs can be added to student games for small classes.
* `verbose` (default = `False`)
  * If `True`, print out extra information at each step of the game.  Useful for testing.
* `game_path` (default = `''`)
  * Path to store game data
  * If left blank, the user will be asked to provide one (or a default will be suggested).

Some notes about the economy:
* There will be 3 times as many buildings are there are players.
* There will be 20% more firms than can fit in all of the buildings.

The command below creates an all-AI game for testing purposes.  I specify `5` industries, `0` players, `5` AI players, and a game folder `test_game` set under my home directory.


```python
g = ether_real_estate.Game(num_industry=5, num_players=0, num_ai=5, verbose=True, game_path='/home/james/test_game')
```

    Starting deposit transactions: ('0x25f5c23f5e31bb112d18d980c74912529bde17b83f7068581f6e354ecd08d46d', '0x6c4cfe34d1125d704be2ab60f0e70b152fcaf6a16f8854e8dbc654f48040106a', '0xcdb9b6c4378ce4b2aaf87200c538c68fa37b1a9d5dccd2941b63f3326bc63b72', '0xac8360365d3cb3eabdb7f837ed0f327fecf77d05b224c95ad6c28891cd67e075', '0x92a63e09d3d234a61c8eb27fb3ed58648a1ef146847e36741df451377fea6467', '0x0cd15ba5656ba3f5f33d0538393fc6109eef9bd1d5ce4e701165690d74f4740c', '0xb43b1840dba519f88c863df4a10d4aeab7d380224c0ac09a32eee9a6b009f256', '0xf65187fa5235546912423a7a00455098dd8a01bb9a077ef2e582760a13008d33', '0x262c75e2363d97b550005abb97a3c557bc5c475a388ec8e82ab28789d4ef4700', '0xe2d1d2f4c7903f26c81921ed036b6a46cd5ea081a5d5925e9dc3c424630f5fb4', '0xde9c89250fd7b0bc5924c1f351e750c3d65d3175e881f11c9f1475558086b66b', '0xaabb2eaf031d0031567104a9abd6e8550844b648a95aa926c1c5407e45b48c02', '0x2f33b9d18929154ecfaf7a69e5a803a5c7aa69717d3a932eba62d33165aaf652', '0xc29d2a21b00f98b6b604d93da11c4e0c8bb69265617b0940c1ef6a75c7b239c3', '0xd7ed552ed40e9a997a9632cb7b49aaf75a999e124c4b12edbc3e0051e5d91dd8', '0x4c205bff5765ddbc59795d310fd81cc2d9f75d0579b9c707d2e9eb6ffea4ca48', '0x78f50c87be4d6db9930a440c014e104ef677479403fc54823d3f81ee8d99b1f5', '0x6980ada7ed17b8bab16bbcd86455eee9c6389e692203d18ce6a84449c903d6b0', '0x09cf46bd135a442285c9b985cd9ee1346c0df72f4f4877daf725bb6faabfeb87', '0xe000f137e882230d799b5ce60adf2282dcf01eefcd789098359003f970abc5f6', '0x4a6358fbd8908a7f673cc72e137bb87783514ed220316bdcf25a9290ddd6dc59', '0x9855b92c06a61421a21a489e0789b57f49d682e70209ba7335d81814bcf76c32', '0x7c7e0a959f868805ed84c34bdbed681238802a94515549d9ed5d127671ada634', '0x988a3fdb86e243caaeb9688f9c2388efc97729631e0ff716c5eb48f7b861bc07', '0xd06bd53c8d07683bf29eeaa6b2badf358caaa3a7932c189eb9efc27232145fcc', '0x6b88028d7effba8b28d298586ed1574cbe0ef872f3e0f76c438be4cf260f4079', '0x8cb118131fa2652159a14adc23efed02b3432ce71a761db2be3082f70f324e93', '0xdb7726c42d05d6e2f9db493360abee1b8ba65820965ced8a919ba5e81b805217', '0x910957c1d4c70540e510d609e4ee7bfc525d2a88c2b185765a986a91eaf27d41', '0xaa5fe63420d5f072ba429cd34edfa0087c37a319978bfeed02f7a3d9cee0379b', '0xda8bedb870fb45bab97e0b9c90df2048bc8f7b8bf44a4d6700eb299f4ec98430', '0xe530f1cb0710b2c916a6c55be782071fd87be675f13c5e71a3d278dcc785a03d', '0x205f328fd56d2bc0fd488b4280ce757d92727e88745a68c875df7921f9e4cca1', '0x292e0a339c86ae9d0946aba8e64fa3af68caabbfeaf0dbc40303e5a85fc69990', '0x444fae63fda58789bf699db146be9fb3f2a22c9758a5618d6787e478a0abeb17', '0x55faaa90403ecb0acc7777d468566e9b805d0557c9ab7d92b0f561a395e86cee', '0xe3f5e24a18a86d24b0d5aad2cb27a6957116cb75aa365994f5b37c0983c80d0c', '0x35d380a5b1eb38fb8795c158539817b0f7b3b68bfc35f8385a500cd2986186e6', '0x9836a600327e0313ec5db443ea839f9d53c3273522f7b38be7e55220a12c0b0a', '0x0c90de936140a26a68eccb04befdd36755aa674ae0c7d25efc1749007678ae00', '0x9531712dcc4d4ac7edeb57c97f4d4e3dece942c88db9c108f02de3e0e686c58f', '0xb27d52e98f8309cb47899de91e3115ce6c8406f74f91841f3939245529a39bbb', '0xcaa74ab533d47096aa292b52e8a613e79a2f970a23b8ed4c85b071c3a985da63', '0xeb19c460d46439bb23b616bdc3f358399ad208cd0f6d6defafe43367041388af', '0xf0cfc1e505b2e07f85274656fdc4b30a5d99dbd51f2b37d3e28e11dfde3ce9ca', '0xf454172d7866b25897c0c96f068504fea34ddb101bbd133cab746968a04dbe1d', '0x41abc2b8146e2a0d3c2544ebf352d4f08d0289b77953e98e5754a2917f6e43b3', '0x1d8d63b312db1a9541c91db13fc4d83b87a8fda140a6acae6739a88586341dce', '0x0d8743f563e5a1f222c058098ae817855bfb498fdef305f71bdc2df85ccc3281', '0x9f5271a52017d79477821943ef29174de913709c04cf101bd68ee908705cebd3', '0x9cbd6352404a4753493180ab66c4a668d9080cc033113a38125930971a75d2c3', '0x2aba42d2e8df3b115b8e3ac217d4dded2a7ac6c5c3c081c3b0e2e17b111c1631', '0xc9a443c1388d4afea7aa92885c04df5a426b86fb84d3e305ad965cebbf130920', '0x4c9d8fb61925315e04a0e65659005b8c35ff54bc158f81265db018d1f79a0cf5', '0xf3c89e42f0ad7120c5b041e355a88a4afc34f29db2be92221597cd5103e502d9', '0xa14d2a3011fa90ccd0bed7a7fb81281919289eb6dd73c7d0088f3f96fbeb5692', '0x5043f9e26c62437cae4df087148345eef31733a33b89acc170c60f3465c3b99f', '0x4679bca5104188e608d171b9d0e7e996a6b9ea9448e29107e6f1eba7c72f0e68', '0xbea11c9895ff40e239ba74ff3a4ea5a10cdf1b46567dfe3296fda3ecfa9ab063', '0xb872d90e072fee02964ddf47eeff5464243f35cd5ba2c529f336fedc76051a7f', '0x90ef839570d2ab5fafbc1eaba1968d655ef855543cbdf32b62bb74d9cb419888', '0x0a4fb25f18cb0e8625640b74d3171514bec5f716f85f09867fde359a1d34e56f', '0x69da9e3ba42466ca12978ff415d2ee098c013f8897e9fb37bce6ee081abb0b74', '0xd78eafd39a462ef825f84c8682b5e91f53d0bc0bcb2c7b48c4722e8c2e8df3a6', '0x7ef079fa12efa135a4b89ccd3cff5f2a474246f00c4406c86f06391ca2968dda', '0x2571f5b772384acb98ba6ead13857f65cb5cde3b50f1e9d5add8774bb931a454', '0xd941be6c9a6d5ed212a7f6341d50871e80255ef470c0e69fb6df26f4ffa52e66', '0xe40d7d0df9992f9bc4d2216a9862d6063b31a868094b31b426f17969f0e20fd6', '0xde83b42b6f0ebd1f4e3a6c19117d470f4a17adaaa322245aa83d105c234827ec', '0x80f994c2d6dff5449183a81c5c131a9c83095f674c6fb1df0ced37411179254e', '0x77d09afe4409f3409ddf5cb73d9a517296a12fe9082e6098687873ebebc10a44', '0x228a141821b7ec9e9b5d886ad20c956a73013176a96ffe796a645590999d74b5', '0x19b561c87237d1c16a2dba6fab60cd314ba8e63842c2272b01ebd70af83259f8', '0xf9c4521d1d365d57bb9453c405872cedfeb8870730663deb8168c75203efb56a', '0xdc10913d4abc7c5cf656939bce9730ebf5e91dfaa7acf25b644bb7a9e105b146', '0xab0f0b28ff505043b726f71c74f4cfd330c51f060002cbc9ecaa2b640e160789', '0x2bed42efdb7135474c62013968e10a9cad47756e2d087c01bb5050d5d4591e16', '0x47e43957dad2a1487bafcd72249f5ebcc018e1361a685066d3f9b70aa8c57f8d', '0x89c2a3867153a14aa4ec997879f7016674dfce8484f62a6785932e4990e95561', '0x8797af59c867a65196ba7c3185554978decc61258ec224d26d8fb5f539c243fa', '0xa66b6c9ad8f219281d34ed508ae25c81450a6ebe1817d10c364c97e292a92946', '0xf04e5bbeba1059558ba62a2f1cb3565ba15c1ff632d7a5a0dd4aed744959de6e', '0x5ac7cdd4efa7645ed3ad8094c1db13d05936a35e7b165e4fed197ec53ccf14ca', '0x2044347cd81fec65053df55e0d7fbc4f51f2b63cd06e8099213cd762e043c137', '0xa19dfebced9734a226e6893c2bdeec1d55b5dc7b62876c1c40ec60007c229ed0', '0xa28490e55bc648736ef5239f79205e43db40eb18d76e2ebdfbbb464f62863db9', '0x7f1dfff6592ecacda08875440f5bc15373e0c222408786a4b43f20e4dce098fc', '0x8e4ac45099c21ccfdb1d8eb45cbe75d9eac80a9e06783731612c275ba5ed0a6e', '0x53df65a215fdeaf8c5022ba611519150ebb7ebf4f5c7f855c5901e8158197131', '0x75a6caad3ff69b45d9be676fac939a8b94323bb4fc29ef4f9a2e2739030890f5', '0xe6557add2637f2c25493628e903d909d4bfffe24122451a50a9c41724b64e8c0', '0x57cd60c19232ccc65fd6b60a9afcd6e45d637e5eea3acc4084ed0d1300c5a379', '0x0880ac885ab5b1fa83eec16cd0cdbc62fe885077cfacc70a9bbc17dced32ae37', '0xe35f2de046fe4873ba6a42a67f133534d4ae4de81665f284a67a91c0f5067713', '0x9542e1458aeaf71a2c89992c24872932cd1d99b66e0764aa5d798a36793c1ed2', '0x2ad518e3554a398ee531c63efd00b1e661ce0bc3dfa16e0840c620e0a589bbe3', '0xc33d0d70b8d8780a607412a334fd9585be52779a49ff568668859b195d943510', '0xe9c84dd5939e31e57241570eee459a423935a45734dc7ad44191d332aa75622e', '0x7a7ffe5d179f82c32e08d44502b3036f4543eb1b4d2bb8d30f9c7d54fda51cc9', '0x2dbf90535b8429a33121ac3d6174a152a9a820e6a989a5033ccf49693fbcc1fb', '0x7388194cf94350906b0bfd6090fe671ddd4e60790517ef0fb98eadd094f3a11f', '0x667da3f3a7ce26ae8ca145465abd6f4d9b36db3b14e4ce333e3c0074cfb441bf', '0xa45af75c9bd67893308492ea92f9b350dc6403dad4e61b13b22a8d9b9def5b6a', '0x99ce672031007d10da8e8ae69ae11be05b968b395bd6d8d16a0bbb954b640ded', '0x7f08d13153385af9c3ddf3fbb77a59fe1f7fc8a3016ff1a983e6ccca20cc10fa', '0x4dca0edc35d4fe9fe61641dd4b925b22598cacd49b543aa4f31e22ae06275dba', '0x876c34d7675a785a75a2f6682f0de08561ef4aa979e3ceaa4a9df4dac30800cd', '0x40877206a4ef2e75f6de65a4e118cbd1b62bd54c45ed4dd25e1c256afc54e24e', '0x81acd8078afe6f8117582f94bae09842566dec01aae6b83cfd551f36fd3bffdb', '0x1f7ec6454ff4a75e133d129677e7f038d3f9baba110c98b640cdcc3dd3a6199e', '0x84ea64383d1a965b182515e8f95b48f6a219d770ec4c72d869dfee83706f9465', '0x1076ff3fc148ed90c18a449b054224161b28b6fc3a4e134edcef34a53cc36f90', '0x8feb2ce5f2f08eab040cbe4cce1a7350cded0202154d19bf15206f1d6c92a476', '0x347a24b482dbce3399822f1e1bebe6d7a66e4946779e896f7ad41cb49c06e357', '0xfaec76d10c68fbb68c0090b8fa41e8895cbb59747ed8e7af479012134e018e6f', '0x8f7e0bfa632e4b27725b3fa5d61c992562f4c357aaeac35601e7442f7382849b', '0x1e1596fef5c376e41257a3c90d032c0b0c7265951a5666d7d63639badb0060b3', '0xaa06a10cda3835b2fb9f4818750248fc352057f902011861f16d5e7c13ad3272', '0x7fce8a37d84761142d80e97ab3228877ad2e51ac247087110d81e4599e2a0c2c', '0x7badc88125a135f3a9163e89d9f52c7549a4cdb3405e9d82eeaffb2a422e0643', '0x51247092d80ba6d7180de1afeb73f9b3aa46efedd9b9b4bd5ae6eae3a6a09c1d', '0x9b97a7e0878bf49fda26cf0e660a7fa96901de43e63b3a52248d9780b7337b8b', '0xcb177c2b9767fc0bbd7abe39e9f3c14fd309b3af0eafb82f3b3c9444e0faf56b', '0x8b378be9dda68678cac993e6b7e749b8f2bda6377614102e91a35041a59eb53a', '0xa3c0f215d80a30a699d5e1781ae7948c8c3116ff28f007da7b7baa1055c43af6', '0xadef82a83a8bacb120385374a814b52fbd3e82a765c1c34cf8068e1262602b19', '0x55fc5d68d34f793bac327359aa1ee56a56f9b3a63db95f4d4a4d8dd356d95d0b', '0xd8cfcfe322eac6c58753b496d204e0cdf09d7b16ef1661f8ea3d90c5d5deeb4d', '0xb3f10af3eb8b04ce158f3c345ed886dee38fb05330f95c958f7ab754f70d2eef', '0xc48ac3de961467e6b33a69c210eab933d77d391872f13c29be4a12849aede99b', '0xde7b5e7338f4c3ce2167f12ba8bf0717ea78965c93f6ccbee921a588f70bfd5c', '0x757e1b4633a36ff43ab8c9383710bb4a57d4d052123e319734894c68e7afae30', '0x616718fc2e21067af6af15626e6bc718f4f51da61a936cd6b19e37975238053b', '0xe8f265d7a2cc6baf8a3914e31555cf7d675d6a7012a84355c542380059f75349', '0x53527934680e8c17b67a4e8a7b2299fc5258c626c7133c59203b28a5566f72cf', '0x4bf2843c79ff85ca5fe2ac14257cbde6c14226c73511fd7c98d36820201d56c6', '0x260191d44a805f529a49b4fab2ca8da126df5fa7cf9fdbdf06bcb2704c8788da', '0x246ac57f5fea115cdafbfc83c1c97ce635d7011b5dad13d63bd59b47273e3144', '0xa29e10c7c1810668816a1b98171bb67717b6f28d1a2a7c052ac5b8bf8c686e7e', '0xc1c93e417440f33dfe65a2027deb39e7804b34cc250679e07b04a2f339c7d18b', '0x1efbb6ab252589e9a5203375de7acfade60e0df449c495496898d099ca4ae45e', '0x5d0f1b5a883c00f6c3cde54455554c3a0281d19f21e66af40e63e2ba23af1438', '0xb3e3c3068d7845e69972c3185efbeda3acf3a9423a0dac05805e762cf0883233', '0x8017f9153308af465cabe31a75d538180c01540961b7ad7a5aef0b088b65ba3a', '0x5974b94b122ea1e1866405b203a1f072a9a322b8793dcb2a678fc43798f09f84', '0x1cc65190e39275c616c796d0882e603e80bafed23536c7d5c90d77299334cbea', '0x3ac1956d99ba1369591a3c54b9dc7f2656bffd99bf3b3ff7322a729ffb66478d', '0xc39568d5360b004067602a2f23c4eadb691ab09a0e18d468a65a6301ba30ea0c', '0x715ee0f8f105500be5b1103dd212b04d987f5317eec85a1de3de375b3ff7beb4', '0x41cb73c59ed2492286bf74530d462469e52c9b8a4d04bf462598906e6cf38702', '0x250cfd69611ec470352f32ed99cb5e730ffc5f9092798658313f8931b7c47bb1', '0x229de6694467bbf4d0dc47fc768294b18cb29198dea5e0546f229d96eddcc147', '0x48feeecd2ed6782b47490f42bff2ef2302b4c99db4aa4a7262fc9dd2d6e76f65', '0x5d11fc155fabf263cc491d9ce9836df5a7698f59ca14f6e16429b4a45faccea1', '0xeb294a51bcbd026d93af259ffae81a54f1e947dd77e519b5e92971dc6ad3cf46', '0xfed0c04ea8c48bc9de5ed38d22f890bae16deb7bb6ba23b442178bc35c0dc29d', '0xfa2cbb92dedaa36b3b8a96bd183dda10613dbe9d6cf3211b54dda30946996e46', '0x50a263f01a38378c40276637bc3740fc104e3f62993cf84ea7bdd65c855684ae', '0x7632cd4eaee923e2c9fd6d72412c5cac44f783234b71ac2398a887754f00aaa8', '0x460d4ae3e89d37a3ff0788f608f4408c039393911b7871ec8a0f230c78264d8a', '0xf9ac843208f5e79a360438f38592b13fd24a9a3e1d7b5fd6c58d1319dec00255', '0x662897d1de4cc514b36f7a45509085fc6ed7b3368a8de1a9d90ac4d291a74d96', '0x4b53bc793a37f450ca0589814ea29dae791af5d5f3d7d8bbac05f1b68d7bf6b9', '0x6ff26b86d1809587eb79259648c930d07dee8229796e51c1529e1b6f3fc49bbd', '0x7e39bf0fd04cf360070d5408b42211f717ee5fc751eb10a8f98444597ffcf6d7', '0x0af20258536307045fcb639c3afb1c5dc26c2acd824fe61d0a598795fb37dee3', '0xf674cbf1aa68eafbbfb0f2e7012f08633283b24df9a6b3badbb97b9e1f47a388', '0x6b047a072299da246124bf6c785500b2a8e79a8b1154a556906e9ee117604818', '0xebceb3897f12948ba2d8b5e26aea7a3d504afd14b42863f03c9473746bbe934e', '0xe732e35c651995182f7f9805caacf7595a72141a388fcf1b5e72507cb1316a1c', '0x11974ec52f3ae271ba4806fbe68d393fe88e375311bab048bc34b737e2fead01', '0x8839e3720fa88fe72d94af3cd026d433cbb3694d9509c6e117979b2cc43d3a9c', '0x110543b8d474fc9e37ec772eec8bfbc3e30bee42d1102f41e540d772d9d862f8', '0x524af3fd92dca05436c43a6f9e3c0e34cc63717570c83fceff5bc3c4fbda70ba', '0x9890a92f53597fcd564fd9a372aea0612baf2c945d4fd1caf1e08417da7f5590', '0xb498c11cca2b0e079e772f508e405168419b325b9f0eb98d05ee21a6473d4533', '0xc2ad389893e7ae125060248d714fb5ff51948fcecb49a5378aa7714ccf71a3b9', '0x5455952a7fe3d497bc4b2826361dce7899737a6cb93862c539c8bf0d2f14f481', '0xc8dc29cd58f923faa344bf4211c3f74b0a1ab583d8234817c1f2527a9baea367', '0x94bce3396b6e2cb8d7f21c5947784e49a4a28f788f9c78d55bbd54da7631bd46', '0x3df9589ef3c0bdff9694acf66ee04dfe9696c9b962c6b2ee94298b5bfd1d443b', '0x230367141ae401fdfb3d0dddd7683f8c50404c2e5b793e0d1196a8832fbfd81b', '0x59ce70e6efde4c8f037dcf3c66754f1773348c06c6356f2f8f4f368f29bd9a7b', '0xf5a1aac6d69c57edb750e7faea74301fc173fd8c8f7f27b2b3bdf72387402a6e', '0x7117a24c9874958b9b1ce722c427cb61e4a5f53ce39931ce092c175aeb102294', '0xf3e0be529d61bb3ac0f2c03fdd01fe6b4b3f8d1e15b7be8777b6850f1357d73d', '0xe28c8c535c2b5b0aacca6d96db7bb7fc45095cc4d39f810142de5c0924cb9a49', '0x82a5c22b1b9cef12a842c3075edc7cc5b3b141e3197000b0fe3132c3721616f4', '0x22512f8faea263d46908a50b1899631f962e8fd42a02c3f08ffa18646fc5ace0', '0xca6cd7c9ab73c2f8c59eb6c16b50745e15f4dd59ca9e11e3cb033322ef2f7446', '0x51e27a0b66eb29ae1f7adc425770bb899943296d0f5c2be3e02468b4887e90e6', '0x1766095ecb96b634ef36df5a6d60e3b7c8e7511ef580bf5aaaf484a3edfe620a', '0xb3e282d18e4dd6767334576e36ee72f70ae69a5c26c6d1bb864e1ae1e0671cb7', '0x8f1f97abea0c084a592e3f610c1f68696a7542fdee08d5be41225d2b113c910b', '0x294681c8b130dee53ca917afcf7db03d31cc145f896b2d6273f464d4c1fbf408', '0xb4b70da601407621a34367f107665a3c092f59e56f91585df794df284a8b8fd1', '0x4a0194ec0ec7973feb0a91e0237e2935149af8c8c18f333f910f10577ffc0c0b', '0x1568f0574994ee9cca391d0c47ac023ea72ca4f0812e7079b0c3ca46813b8508', '0xe937a47d30c736c2f422fd4842103187c39e447e14389fe4cce4356e4d22face', '0x6957ea9ae365b7bbd0d4c0f12c7b5f51060cc6c1e21c4b74a1fa9a7990943da5', '0xbe14ccc6066a78b3de933ecad85e2f5e00424f331539bab01b7c667b048cf0f7', '0x7289d93105209be77fb7f30fdb468a1f44193a7e39c4832af309b792d398240b', '0x650be2eb1c4c0514f71cc47d035bec528cbddfa433d94c6807ead627f7dbbb06', '0xa7be9b9fb933acf4f48d57544fa73c9eea91a41d38621a749996a393342d8656', '0x41e71953a6a82ff7abf82d45b344889f8369ea79f2a4ee5342b04626c195c081', '0x2ae8541e39bffaaf08934f11b68c027613ad6c8bfb7e5600a8ce8acd0b097268', '0xb130ba6c471c03251fa7304c11665f677bd22a383837cd48817756e587c3b79c', '0xbbdf7f2377a80461d094c7e098a4c3c0c2660d9c0adc1868a04567fc84b94362', '0x19a7d44618c16ca5191d0b3e83f89fa9c6be09a1ae9f343354e318d00924eeeb', '0x05ccfa68019b34ae78e05b4f92831cd0116448929f79148411e53a7dc2b36f14', '0x86ae36d4632c94b8a5d46f45d0da595613126f81a7f115573ae8b8226319953b', '0x2d98b1aca653f3a3293799a5132fd115c250ce8326b484d6aa18e477e62cb734', '0x0c34e6c9842bada3b2fcd9d064683981a82543281923bbb31ffb0a09f2b79e95', '0x285ecb9822e1aacfa87eb3893b3895942e2026304b5c041ae046fb7a4bbd95ca', '0x0c3848372820c2e4b8e7463ebfec09b6b9e248aee47d3ad9023db2877daeb836', '0x0602b4fb3ef5e4e0ecdd457d7a6ce5bca55886cdef6cd75e37cc45dde6bee72f', '0x6e1a54a3f5836a67c96b138f3001c16a36acb3c123c9d3fbe42df2b10a3a0de1', '0x0309836c603e111b718764337fcc6fe181b6cc291d6031f2657f60ff9909b129', '0xc735fe143ee0d8936847e828039c3310f60c8e8096ff6b17c3d82bd0ee09ca2d', '0x45487a883062839a6a080179d61db31deb0e9b5f15609ec992da03155925d761', '0x99781487a1a5e5a4bf4078ba7746bf354cd7a3c5c57e0786bf88817f35356bff', '0x29a512be466d50370495843c222fc8983c0ef22b23f2fd237bdfd9f104957a2f', '0xcf87d1f5ff2f42bebc162f4810320367baa87d6a7fe54006e45ca424c672b051', '0x7c577d5ff53dc374bd20dfae642dcba88977ecc35a0b5949ee0f9f665d5b6149', '0x5e4f2fdec471cd2a57bd468c43bd9282162297581d174d1430ac323adf3a2318', '0xa990ab885ceb1c7eebfd75b674490b571c4664299ecbe18f3505e7cce1d4a2f4', '0x1dee05f8b56c79c5b7084dfd772467f3c61267d7ca45a3ae026d586111c4baa6', '0x077501f0121d220aaf440520ddcbe299f8c70b78d496c8f911fc7623b35a4ddc', '0x2bb2824f1083cc98035aacd35bd0bc97e6fbb40a28ab52025ff3134f9bdca683', '0x969455011093cc9b6f5cad8bb7970881e28e5fba1df44147e60a3e88212a53fc', '0x1daf3b32237117d83a75bd7f6a05db994d44542dfc9d974c459eb801161a06f1', '0xa6209d0cdbb736555fc07fd626624526129875cd1ab45b84606d72ba5609cff4', '0xe303b0a8943a06b13f65e7e0f876ff628457b7f5a5392fffca159a7ff556b775', '0xec72d2d355a00e0de3e082e0cb1ab48433a5ae25d99de7b8403a12773eb4f3a1', '0x9fa53c012bc9a208ff1654aa12c2af649b16260a8b67db0ecc1daec635ae4419', '0xa9d0871a386698afd384e5cec168e6311028481994e8795ab3172968845a946a', '0xb651f7eab2d66d5089328167f57af6c606c3cf9d49adca464eb46c2e2ed55be1', '0xa413ad5ee6a30aceea403328a990eff8e8c2be068484cf23274d2e18ea9cee56', '0xe37af18018806bb38435a3684959575e0068f7290e6c8665fce439028543c0ed', '0x5be60972faf1536fe0f9bc96077eb1f9e47ea7ab288358045ffa78b44cbed345', '0x9e607d0133d97676412348f7e466615a58adf9440de38329664e7d895a338b20', '0x7cf2e44e0f817d4cf44aa0d15dba7c8375fe5eb9fcbc867bb64ce66ac8d01ac8', '0x928eec35a111109284dda91ddd3c31a05cb24f68c607a85411ae4a82a2a69e5b', '0x67f0a6fdb7e5b7e19708208a1fa9772e668bb9712549ccfa064dd80378529703', '0x69c95e0636ffe01d8ef03e75c03945371e4e79233bc75339e42ee85f619f3c07', '0x73b3cc4719e093e77f2ace3dbde04dc7be17b9e0338ef775166f1e83b4a2c456', '0xb76239ab1e36d710039b9675907e7e26729afcc81bb929d5f240da2be2c6560f', '0x5d9e72603cbefb0017e516901f833612dfb5f3a322b97c3a5835105b627e0386', '0x4390f817a25b3887d50a25e80c7b872d53d064ebd4f743d87cfb8baf0e711114', '0xcca6f18056524af96493c541be2e4ee239204a3add4255526838428b410c7ded', '0xf48ce62aff52e7f8f07d5016c587b710837cb0b4f96f865c4f2b6d6922ebaf80', '0xc9137cf75f45695591ef65799daf102213c021d498810a0625c38e24c4b5bce1', '0x8c7709a37ad4686dd27b9e7cbaf239857610b977cd69ffdac786e2f916daa60e', '0x3cd17aa5d0530e64bbe625fc69260c1d9ca54ac8a361168b0caa97fe552843a9', '0xdf3a3152fe4191632db5025be2b91e38379f6b852e79dd4d60c8f4d3ec6a202e', '0xacf704d96853979a9e7ea523a1ed4a784e4638b8f74714a7628c86421fe4a317', '0xe0d1232226579cfa7030f172ab39a8877aef2d483f85d937955fa432f3c2aa33', '0xf04df87a4461171e9a74119bd8d80bf729c820d7d0ad279234fdfe2122c3b55a', '0xea3bd821c873231810d5677e3c07bceea14787e28abe5e51f014ac83260d3662', '0xb5ee0dda270cf24eb576669b319606e490cfc22a369758b7dd0aafe2f5d68e4d', '0x0cf777764ef648b7c78b3a39c9abe707f7b361cd9e1a69ed15d1d8116e08d7ba', '0xbc9658cb2c48f58cac3b487d67a750d9482ce4501148c0d2fb7bb2ea5ce78142', '0xd3cebe20216365a2526e4db3869e6a47fbd4463ad99b46e2f86729be94e5eacf', '0xfd82b1b0e3eaed21369add698c458ea7ef0d26ceff4b6f0b344f934a0d75d91b', '0x398d1e4e0b0394f07ae2d4aa4037ad97d790c8bec740314c44fd150d6898b2df', '0x01c1423290e8ef57a2795afc9e160724bd1542dc7ddfaea494f7e88881fb8b15', '0x48a2db31cca6722f11721fcf23a48d06e9047e8877ab53389d3f5d923c8f0ced', '0x29b79737d0776f4a7a9265bb860e73062c89fc9b50e0a8034a8bf14ffdd9f4b9', '0x4e00b89b1b6270310c74e2b3779a2288089caeb12a853f11b3a89b4aa13b6918', '0xc1e04dd8a7b22336726c6fed1e7dde12d9cde5255b35d59c060a994e06e72a16', '0xd8d2b923e069131ce3886ebc9fb741f5f704ce8243bface70c30e86dc28bd0a9', '0x7c630a78fb0dc7b5f20587e97c3b285aad6f2490779b4e9272f4dac28f0dabcf', '0x64f635404325924e5d73e4c547d3d427ef7e94bd5bcac5752114dfc351a8fbb6', '0xbc684aa61be649908a81b492b06599304501434a83b5ca0358807894059f68aa', '0x7e7324e2c76e5bc14c044bf25e3fb1e9541d258a9dac51e0acc62aec6762e637', '0x4d8b3d7dd2eec397ff1af32192e5cdbfecb875067cff69e0a98eb9dce3c5c5d5', '0xc455141095debc28a54569a139dab4c0818b4cedc5de8bbcbdfb307815a7e0df', '0x2873a4b75325c12171021a4ea0689a0aacd4d7ad68e1a4a409b5bd0fe24788b4', '0x673a53aa6fc16875bfca9edf95c6ae7d75919f305848b8dd0bd5fa092a91d2f6', '0x3aa38ce8ec76bced25aac08ae7c623e2de0e41aeae03639b7903ca47d9688eb7', '0x174020d68049eb0e6d4485a12189a9fcf54db64c9c370d8213927c6d211cb70e', '0xa9e9b1616956685e6322da8fe5138fb7ae62034319feb8c6aa2cd6cbcf13d53a', '0x15b8b1d4842786b75d414f6802be2501476e70afb00b73c6eefa5d3abddf2dc3', '0x33af4f0f621b5cdb05df6012275a82aba7746a826c28b05bfbb4e68f32b06ad5', '0x1ed73fbd0c21e76f015594ee5e5549959f22513d0679277918ca0c2266c7e01e', '0x7ca3931158c4202943715e8dc5d09c103a494c5f8a32e9ddee13c0c1df0af6d7', '0xaf65ec85309c12c2a8f848711bb3c249296c79c1615af5536bd740f956cf3cfb', '0xb567e17cbc0914c608e3511f72eb948ae0d024b47bcf22e81ddad1501b9af8ff', '0x87b2a32d838094bd09ca8e3df91b5b8df657a9d09c4d001efe1accc0ce6df7cd', '0xea0cec617b12d149919883e59a706e9172b9579119522759cdbdd88c1e8296fa', '0xc785a1e64a82b343da8b6d8212c5fcb2719a00e7d8235fed2a181bb40672fa34', '0x4fe899d5586b1927377d98fcda028b732ac354571426d725c913c77ded6360b5', '0x8edab249639558bf201873992be7f0db1182599927004ca22af3f5bf16a96b64', '0x58fb17bb45f4e02a2c56a57e4b8b6dd69a23ce0f7c4a1a0fc8284462499b42c2', '0x83607675ce9e5a52dbe771bb8df89a0057f7200f8946adf92bdd4a7a87cce8d3', '0x6ac2ab9765f3fcdf32a9b1704a7c7225841bb1d026d25aa96f89eacac759ba92', '0x08e58f3142bc3598fed4755df19be5e7fc8cde59a83b6dccfaa8c734798cf94a', '0xb3675a3958717b8542a03f179be5347c76e0441d380a70f89203312a3f3ef5af', '0xb6293c3f18690a0a401d86bcfbaad8680889cb5eee02cdf3afda7ade10d0c0f6', '0xffc31ce7390af9c32ad4b4d5de314db5cc74efcfc6bdc6ae50f3e5a793ecf121', '0x4c35b4f9bbec288c22291021c9b5c060b13a870c3bddd02cb18379f80e31c34f', '0xf913bcd90bf26bcd59240fa46c3b4d0a11eb09d585c6fe856b9bbf2456fd68d9', '0xb67284768c5faeb493b6bd4e8fb6345f74ea760fd6df9f3ab5a7e69b95d1e690', '0xb068b58232757e7975ec63f6e6bb55c32587e1934b2113924308e59b77ef0702', '0xb8d747f84efd435c7c79c77b4bd7ec74868f94811302fab68eb6b05ad04b420e', '0xdf107c7eebedd9b4ec55fd16be299b1bcc36d9a77e91a2218ad9ee938c653a3b', '0x9dea3ac9e2b205f39f758562ccbeb6956464b4c71582514a0b922464eb1df904', '0x09b2f586cc1466f0582f21d3ae09bde93cf0ad90a8172694ac94494d1070dc45', '0xd0e7a49253cdde0a32f5337c793544513851af06bbed0b45865eed1e223cef12', '0x779cb9224f27d502d8df968f7062821004fa9fd82235b69eec6de26bb05c187e', '0xe0cf68087231503e3f23f0810d850e66c0509bee08dda9ca7d7e477ad32981ba', '0x382fb95087e92b2d5545c580645259232b23591e2f61971bcba523c9f6579349', '0xb55c5342da7c5bce86e107ad2ea43072dff99f854c2f6b6257851c5db17fbe9c', '0xb726159080411539356133978e6cc62d4152d55d3cc13f7357491f816d26a6bd', '0xfd585b8058cf4ec5fe4283c8b9cadc35f62778ad29bbf7b31ab4a56618a261f0', '0xf489f4435e1068dbc142f884f08c5e86f2e2534d1b9bcde7b6481becf528d84e', '0x472c863a5a226dc1309f6ca4cd5acbb3cd6f82973587719ba622bf5ae8208ed1', '0xd7977ba813c3dbd274c7efa52dce1c2c1c676ef043501803090b09c4e707172e', '0x1554df0f928af2c3635d131646f30670b07c47bc2f4dfaa08ee500463e126e3b', '0x8cc4eba9a4853bd82f7b0cc0bd8bcfc4cb452b85896f3374d9457c92518564fc', '0xd95d4229472fede25ba25df40fa54cbad8f916db5aceee8534616b170283d5a3', '0x09e55bc95a066700eb3b1ed2638acaa945fe42bd0c288a3088a77ba91e8327cc', '0x3d68b0860147938e2c48a4c6d2b8118322f5a5fa07148eef3e4cdd49818d4b13', '0x2955068078b4c1385a529465054e39057011d38b661a7435426421512092a534', '0x5431d9731ed12bed90c3c3cb146453617c0ab7af96048721395e9f7b7df5bf6d', '0x07c20563e1e9dac6f4b46d013299006daef4ea82ba1fce5b1b0d601ee22a7c56', '0xbd8d0b07e1c678c31e9e821f994e1d89a79efc41c7f328d7dc5414b8acf75d93', '0x8f825e71a18ae8766d9ee9a05d5d1269a05e2dbf82556f643df7eb56a0fe4a6e', '0xe494e69fa644191b0897a2e4455063eb8f1698655730919c64448b2d14e14b4b', '0xb8d2f9f83b7b6906b54541e0a8f52130bca88fc81e6fb9676ba99f212255befc', '0x0bb7dd1c0f4419453d817c06f5e4e04adc5cbfec3f4d5971d5dab8f09678cf5f', '0x151a7df30a25a88cfeb6d551ad7f90fe532adecf97c4a4ed38714bf312db22dc', '0x8187fb3a6a417c00f49630ec72084127ea5c33ea468f27aba1bb99647e3086de', '0x9164b9ede742cbeb1698c2719d2bad92209dbf17d5f31b501857bdb1fe45b99e', '0xa9133cbf7035754bd99ee6fb0b2ad9f5af036dd96c077e4f26371a97664f0226', '0xefb945de9a459f852405ed709a751de8ec47e258020904b55634e49d74f37dfb', '0x3d5831a016dbcc14ac1ead16bf93b1eed5b653f0df343172b2204e35dc47697b', '0xd46be0300d7b6cac8b95931e420ab17948e3ecb54d5dfdac6fe3f9470a1c75ee', '0xfed003692f69e65e23d4df371edb024d17f92572cff933987edf3c09ab843371', '0x4d7750dee9e2f6665d45ab037c35bf1f5fc1b7b55bf8a372f26acbde0a9be8e0', '0xc8207783ca90b52309f4f9c1004ec977ca70650b2f96cfd1a3aff2987bab6e6b', '0xe4d453568b04190286f8e54a89ac79ef4b03a2140f843808d4b364355bd6010f', '0xc147cc5ed6759832cb973602fd2e417450be36d3c09a964cea54eb5b7244d7d0', '0x77479499e7ec77fc4db51d3e23d638350e069874043ce9faa6ecc55f3a4cd24d', '0x61ed7a06582bb57793ab3e1ccf8331c2b2321dcd1220aceff7b1a4d900bc6022', '0x87983d74407d38ceb5283ce6573aca2b8ba426dc1e8e286fca103efb35dcf5ac', '0x8cc6007e3dfe3759ff963f75344b6c344a26f28ed2f08e990332697bb65ad2a1', '0x6bac3688f6c10fca3bc41957e21ca0e4161564b63f592ec540c543f317403924', '0xff4cac021d2cbc9f66fdd19f467ed76b540eecc0be20b32ae1352356ed5fea8b', '0x19df496ef0b0a644efeaa8d7568ff0404779e9e5fad9ee1f9fd34118f998e867', '0x5fe3a4051e043776ef2553d94d421572807ca276ed7f33564dab4c9b5919ae29', '0xff2e00ca3b99534b056d13bb32150c9628cf9cb8ffa53cd5bc2dadfddfd0f42d', '0xd99ff98eef0f31025d556d9f204c7d82b4f44b5715eb7f085e64132166ada715', '0xdcc1a08ce5ea471e45c25dcf7b93103c246626dc35c90a1a1eb0dc63e5d8a391', '0x263d2999b4bdd945612e59d83512cdc9b455345e6bed701c5cf8ff23d0cce6f0', '0x3993fb3d02f794a3e34a0154361c142b21341cc3d3717102d3133ecd87d42c33', '0x1508ae273f45ae59d87a21b152ec04d086341cdcaa56f110ad74da4d348ddd41', '0xa84d0e785b063a1d13503baad8c9d7f8c7183d9acb1bffa2611717df4ae1bf10', '0xc41e29bf419f211093b90f584f15be7dcdc22f36ba7842263d94a3552f4155be', '0x0f78e2e5c401d1af3c0802c6e84ea445327a530791a5178d20f6378137538270', '0x03603b8be027b1064a883ff4057a1a7f82995b1d6f35ce4c97db59acc1999b2a', '0x0d3b94edfa612f6d5207171282c0e422a1a6743ec42b5372e20eae4c1fe056e8', '0x94de9bd5da215fe7b0d6eda48b0d831b6cbf848f6cceb6ae4661f1713abf0b77', '0x139dba20088a7abc618b54b0af36d5bfb10a88469c4527184a456e83cacd0a6a', '0xff0a63f25404c4a76c0061e2fd08ec15a330bcce193d1b83a896993a0f7ce018')
    Saving the game


All functionality of the `ether_real_estate` module works through the `Game` object.

When a `Game` object is created, there are five directories created under the `game_path` directory that is specified during instantiation.  A brief summary of each subdiretory is listed here.  They will be explored one-by-one deeper into the tutorial.

**1. `building_bids`**: This directory will receive human/AI player bids on unowned buildings.  These buildings will have a portfolio of lease agreements as well as potentially some vacant units, and at each time period players can bid on unowned buildings.  The `Game` object will select the winning bid.

**2. `firm_bids`**: This directory will receive human/AI player bids on firms not currently under a lease contract.  At each time period, players can bid (suggest lease contracts) to firms not under contract.  The `Game` object will select the winning bid, and the winning player assigns the firm to one of the player's buildings.

**3. `stats`**: Summary data for the state of the economy is output each period to this directory.  For each building, a list of which firms currently occupy that building are given.  Firm industry membership and lease agreement (if available) are also provided.

**4. `cash_flows`**:  Historical cash flow data for each firm is recorded here.  Firm cash flows are given by:
$$
y_{i,t} = \sum_{k=1}^6 \beta_{i,k} y_{i,t-K} + \epsilon_{i,t} + \nu_{j,t}
$$
where $\epsilon_{i,t}$ and $\nu_{j,t}$ are firm and industry-specific shocks.  Players should use these historical cash flows to determine rental payments and default risk.

**5. `blocks`**:  Each mined block of the Ethereum blockchain on which transactions occur is saved here for easy access by players.  Because bidding for buildings and firms is a competitive process, players would do well to review the blockchain and determine the financial strength of their rivals.

## Bidding

At the start of the game (period `0`), no buildings are owned.  Players must bid for them.  Building data is stored in the `stats` directory.  A snapshot of this data is shown below.


```python
import pandas as pd

building_stats = pd.read_csv(g.path + '/stats/0/buildings.csv')
building_stats.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>building</th>
      <th>size</th>
      <th>firm</th>
      <th>industry</th>
      <th>firm_size</th>
      <th>rent</th>
      <th>maturity</th>
      <th>cash_holdings</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>B0</td>
      <td>20</td>
      <td>F0</td>
      <td>0</td>
      <td>1</td>
      <td>4</td>
      <td>2</td>
      <td>4.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>B0</td>
      <td>20</td>
      <td>F1</td>
      <td>1</td>
      <td>1</td>
      <td>5</td>
      <td>2</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>B0</td>
      <td>20</td>
      <td>F2</td>
      <td>2</td>
      <td>1</td>
      <td>5</td>
      <td>2</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>B0</td>
      <td>20</td>
      <td>F3</td>
      <td>3</td>
      <td>1</td>
      <td>4</td>
      <td>3</td>
      <td>4.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>B0</td>
      <td>20</td>
      <td>F4</td>
      <td>4</td>
      <td>1</td>
      <td>3</td>
      <td>3</td>
      <td>3.0</td>
    </tr>
  </tbody>
</table>
</div>



All buildings are given a size of `20`.  Firm sizes start at `1` and grow with cash holdings.  Firm size increments to 2 when cash holdings exceed `5` at the *end* of the period.  Firm size increments at each multiple of `5` thereafter.

It is possible for the size of all firms in a building to exceed building occupancy.  In this case, the `Game` object randomly selects from the set of firms that increased in size in that period and removes them from the building until occupancy is within limits.  This can create strategic value in reserving some spare occupancy in a building.

Human players create bids for buildings and submit these bids as a .csv file to the `building_bids` directory.  Once all human players have submitted bids, the game organizer (e.g. instructor) can instruct the `Game` object to create AI bids and then select winning bids for each building.


```python
g.get_building_bids()
```

    	 Property acquisition completed:
    	 player: p0 eth: 14.44375106499403 properties: ['B2', 'B7', 'B8']
    	 player: p1 eth: 204.3377445424868 properties: ['B4', 'B6']
    	 player: p2 eth: 354.48224739631064 properties: ['B5']
    	 player: p3 eth: 153.5332989721354 properties: ['B3', 'B9']
    	 player: p4 eth: 190.28382811566786 properties: ['B12', 'B13']


Here, the `5` AI players have bid on buildings and the winning bids were selected.  Note that winning more or fewer buildings at period 0 does not necessarily set the player up for failure.  Over many simulations of real estate games, I've found players with one building to win the game as well as players with three buildings to win the game.  Success depends on building management (contract selection) over the following periods.

A snapshot of bids for player `p0` is shown below.


```python
p0_building_bids = pd.read_csv(g.path + '/building_bids/0/p0.csv', header=None)

p0_building_bids.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0</th>
      <th>1</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>B2</td>
      <td>175.342916</td>
    </tr>
    <tr>
      <th>1</th>
      <td>B7</td>
      <td>175.131356</td>
    </tr>
    <tr>
      <th>2</th>
      <td>B8</td>
      <td>172.942416</td>
    </tr>
  </tbody>
</table>
</div>



Once buildings are awarded, players now have a knowledge of the amount of spare occupancy in their buildings.  For players with spare space, bids for firms not currently under contract with a building can be bid for.

Human players submit their bids to the `firm_bids` directory.  After all humans have submitted bids, the game organizer can instruct that `Game` object to create AI bids and then select winning bids for each firm.

Bids on firms are a rent-duration pair that specify the terms of the contract.  The `Game` object will automatically determine the best contract to select a winning bid (based on the expected contract cost).  Because some contracts will propose shorter durations than others, the `Game` object will make a naive guess at contract renewal rates in order to make short and long-term contracts more comparable.


```python
g.get_firm_bids()
```

A snapshot of firm bids from player `p0` is shown below.


```python
p0_firm_bids = pd.read_csv(g.path + '/firm_bids/0/p0.csv', header=None)
p0_firm_bids.columns = ['firm name', 'rent', 'duration']

p0_firm_bids.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>firm name</th>
      <th>rent</th>
      <th>duration</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>F270</td>
      <td>4</td>
      <td>2</td>
    </tr>
    <tr>
      <th>1</th>
      <td>F271</td>
      <td>5</td>
      <td>3</td>
    </tr>
    <tr>
      <th>2</th>
      <td>F272</td>
      <td>3</td>
      <td>2</td>
    </tr>
    <tr>
      <th>3</th>
      <td>F273</td>
      <td>3</td>
      <td>3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>F274</td>
      <td>4</td>
      <td>2</td>
    </tr>
  </tbody>
</table>
</div>



Players can bid on as many firms as they like (from the set of firms not under contract).  The `Game` object randomly selects firms from the bid list and awards winners.  As soon as a player has completely filled their building occupancy, additional firm bids from that player are discarded.

## Saving the Game

The `Game` object can be saved so that it can be resumed at a later date.  For example, if the game organizer is running the game on Google Colab then Python will time out after so many hours.  Before the kernel times out, the organizer should save the game, which pickles a number of features about the game so that it can be resumed later.


```python
g.save_game()
```

    Saving the game


A saved game can be resumed by specifying the path to the saved game.


```python
g = ether_real_estate.load_game(game_path='/home/james/test_game')
```

    Loading the game at path /home/james/test_game


## Incrementing the Game

After building and firm bids have been awarded, the remainder of the period runs on autopilot.  The following sequence of events occurs:
1. Firms earn cash flows
2. Firms pay rent
3. Players pay taxes (both on income from rent and on property tax for buildings)
4. Defaulted firms are evicted from buildings
5. Any unowned buildings are randomly allocated more firms
6. A new set of subdirectories are created for the next period (e.g. `building_bids/1` is added if the game is incrementing to period `1`)
7. All transactions on the blockchain for the ending period are written to the `blocks` directory for easy inspection

Because the `Game` was instantiated with the `verbose` option set to `True`, the following command will print out lots of details about the incrementation of the game.


```python
g.increment_game()
```

    	 Collected rent:
    	 player: p0 eth: 236.44375106499402
    	 player: p1 eth: 343.3377445424868
    	 player: p2 eth: 426.48224739631064
    	 player: p3 eth: 285.5332989721354
    	 player: p4 eth: 327.28382811566786
    	 End of stage:
    	 player: p0 eth: 207.0476726336215
    		 B2 ['F36', 'F37', 'F38', 'F39', 'F40', 'F41', 'F42', 'F43', 'F44', 'F45', 'F46', 'F47', 'F48', 'F49', 'F50', 'F51', 'F52', 'F53', 'F283', 'F284'] 20 20
    		 B7 ['F126', 'F127', 'F128', 'F129', 'F130', 'F131', 'F132', 'F133', 'F134', 'F135', 'F136', 'F137', 'F138', 'F139', 'F140', 'F141', 'F142', 'F143', 'F287', 'F288'] 20 20
    		 B8 ['F144', 'F145', 'F146', 'F147', 'F148', 'F149', 'F150', 'F151', 'F152', 'F153', 'F154', 'F155', 'F156', 'F157', 'F158', 'F159', 'F160', 'F161', 'F286', 'F289'] 20 20
    	 player: p1 eth: 324.8122543464084
    		 B4 ['F72', 'F73', 'F74', 'F75', 'F76', 'F77', 'F78', 'F79', 'F80', 'F81', 'F82', 'F83', 'F84', 'F85', 'F86', 'F87', 'F88', 'F89', 'F278', 'F281'] 20 20
    		 B6 ['F108', 'F109', 'F110', 'F111', 'F112', 'F113', 'F114', 'F115', 'F116', 'F117', 'F118', 'F119', 'F120', 'F121', 'F122', 'F123', 'F124', 'F125', 'F276', 'F280'] 20 20
    	 player: p2 eth: 416.9387179845459
    		 B5 ['F90', 'F91', 'F92', 'F93', 'F94', 'F95', 'F96', 'F97', 'F98', 'F99', 'F100', 'F101', 'F102', 'F103', 'F104', 'F105', 'F106', 'F107', 'F275', 'F277'] 20 20
    	 player: p3 eth: 267.167808776057
    		 B3 ['F54', 'F55', 'F56', 'F57', 'F58', 'F59', 'F60', 'F61', 'F62', 'F63', 'F64', 'F65', 'F66', 'F67', 'F68', 'F69', 'F70', 'F71', 'F279', 'F285'] 20 20
    		 B9 ['F162', 'F163', 'F164', 'F165', 'F166', 'F167', 'F168', 'F169', 'F170', 'F171', 'F172', 'F173', 'F174', 'F175', 'F176', 'F177', 'F178', 'F179', 'F273', 'F282'] 20 20
    	 player: p4 eth: 306.8006908607659
    		 B12 ['F216', 'F217', 'F218', 'F219', 'F220', 'F221', 'F222', 'F223', 'F224', 'F225', 'F226', 'F227', 'F228', 'F229', 'F230', 'F231', 'F232', 'F233', 'F271', 'F272'] 20 20
    		 B13 ['F234', 'F235', 'F236', 'F237', 'F238', 'F239', 'F240', 'F241', 'F242', 'F243', 'F244', 'F245', 'F246', 'F247', 'F248', 'F249', 'F250', 'F251', 'F270', 'F274'] 20 20
    filling building B0
    16 20 182
    39 <ether_real_estate.firm.Firm object at 0x7fe3287b0160> 2
    filling building B1
    filling building B10
    filling building B11
    filling building B14


A snapshot of the blockchain data is shown below.


```python
block_0 = pd.read_csv(g.path + '/blocks/0/blocks.csv')

block_0.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>block</th>
      <th>from</th>
      <th>to</th>
      <th>value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>377</td>
      <td>0x0CCcC96D89f2C203939CC0629caEd7C59f2543af</td>
      <td>0x0D3A4C9538e94fC132701df365e61466B797f17b</td>
      <td>3.493930</td>
    </tr>
    <tr>
      <th>1</th>
      <td>378</td>
      <td>0x0D3A4C9538e94fC132701df365e61466B797f17b</td>
      <td>0xe637958EdAB4A6b142a9E7a8F1fC27869A4C893c</td>
      <td>6.465874</td>
    </tr>
    <tr>
      <th>2</th>
      <td>379</td>
      <td>0x0D3A4C9538e94fC132701df365e61466B797f17b</td>
      <td>0x50D64774DddCDB8c3346b127d211F93F0e2C4CCA</td>
      <td>2.366918</td>
    </tr>
    <tr>
      <th>3</th>
      <td>380</td>
      <td>0x0D3A4C9538e94fC132701df365e61466B797f17b</td>
      <td>0x2B8a7cE3D79da4a31e175ff360E34B07a3b76b25</td>
      <td>23.017025</td>
    </tr>
    <tr>
      <th>4</th>
      <td>381</td>
      <td>0x0D3A4C9538e94fC132701df365e61466B797f17b</td>
      <td>0xa553c5C42aCdE9BC5511396FDe73Ce800ff58828</td>
      <td>7.903728</td>
    </tr>
  </tbody>
</table>
</div>



The first set of transaction are all from the same account.  This is the "banker" account.  Firm cash flows arrive from the banker.


```python
block_0.tail()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>block</th>
      <th>from</th>
      <th>to</th>
      <th>value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>540</th>
      <td>917</td>
      <td>0x9394090a2eD31Ec8C88b6926f399f8b719b76E8c</td>
      <td>0x0D3A4C9538e94fC132701df365e61466B797f17b</td>
      <td>9.543529</td>
    </tr>
    <tr>
      <th>541</th>
      <td>918</td>
      <td>0xC1656f56829cdDC502B53ccDfaa4e29E65040748</td>
      <td>0x0D3A4C9538e94fC132701df365e61466B797f17b</td>
      <td>10.636863</td>
    </tr>
    <tr>
      <th>542</th>
      <td>919</td>
      <td>0xC1656f56829cdDC502B53ccDfaa4e29E65040748</td>
      <td>0x0D3A4C9538e94fC132701df365e61466B797f17b</td>
      <td>7.728627</td>
    </tr>
    <tr>
      <th>543</th>
      <td>920</td>
      <td>0x0eBC438EF99b4Ab21D5633037D6B05e44701185a</td>
      <td>0x0D3A4C9538e94fC132701df365e61466B797f17b</td>
      <td>10.790588</td>
    </tr>
    <tr>
      <th>544</th>
      <td>921</td>
      <td>0x0eBC438EF99b4Ab21D5633037D6B05e44701185a</td>
      <td>0x0D3A4C9538e94fC132701df365e61466B797f17b</td>
      <td>9.692549</td>
    </tr>
  </tbody>
</table>
</div>



The final set of transactions all send to the banker.  That's because the banker is responsible for collecting taxes from players.

## Ending the Game

The game ends whenever the organizer determines enough periods have passed.  The winning player is, of course, the one with the most money at the end.
