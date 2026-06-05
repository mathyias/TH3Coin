TH3 Core integration/staging tree
=================================

TH3 Core is the reference implementation for TH3Chain, a proof-of-work blockchain based on the Bitcoin UTXO model and forked from Ravencoin technology.

TH3Chain uses KAWPOW mining, TH3 addresses, a native wallet, explorer, API, seed nodes, and mining pool infrastructure.

Useful links
------------

- Website: https://th3chain.cloud
- Wallet: https://wallet.th3chain.cloud
- Explorer: https://explorer.th3chain.cloud
- API: https://api.th3chain.cloud
- Mining: https://miner.th3chain.cloud
- Source: https://github.com/mathyias/TH3Coin

Build
-----

Basic daemon and CLI build:

    ./autogen.sh
    ./configure --disable-tests --disable-bench --without-gui --with-incompatible-bdb
    make -j$(nproc)

After build:

    src/th3d --version
    src/th3-cli --version

Run
---

Start the node:

    src/th3d -daemon

Check network height:

    src/th3-cli getblockcount

Stop the node:

    src/th3-cli stop

Configuration
-------------

The default config file is th3.conf.

See:

    doc/th3-conf.md
    doc/init.md

License
-------

TH3 Core is released under the terms of the MIT license. See COPYING.