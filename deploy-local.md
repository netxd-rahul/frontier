## Substrate Node with EVM Compatibility

### Deploy Node on Local Machine

1. `git clone https://github.com/netxd-rahul/frontier/`
2. Check dependencies: `cargo check -p frontier-template-node --release`
3. Build Release: `cargo build --release --features=runtime-benchmarks,rpc-binary-search-estimate` <!-- Non functional: `cargo run --release --features runtime-benchmarks -- benchmark pallet --pallet="*" --extrinsic="*"` -->
4. Run:

```
./target/release/frontier-template-node \
--dev \
--unsafe-rpc-external \
--unsafe-ws-external \
--telemetry-url "wss://telemetry.polkadot.io/submit/ 0" \
--prometheus-external \
--rpc-cors all
```

Or Run with custom accounts pre funded using custom chainSpec.json

```
./target/release/frontier-template-node \
--alice \
--chain ./customSpecRaw.json \
--force-authoring \
--unsafe-rpc-external \
--unsafe-ws-external \
--telemetry-url "wss://telemetry.polkadot.io/submit/ 0" \
--prometheus-external \
--rpc-cors all \
--tmp \
--state-pruning archive-canonical \
--blocks-pruning archive-canonical \
--ws-max-connections 20000 \
--rpc-max-subscriptions-per-connection 10240

```

### Notes

- Passing `dev` flag spins node with `--chain=dev`, `--force-authoring`, `--rpc-cors=all`, `--alice`, and `--tmp` flags. It is also pre configured with some accounts and balances. Session keys for `Alice` are added to keystore.
- Without passing `sealing` flag, it wont produce and finalize blocks

### RPC Invocation

```sh
# To view what all RPC methods are supported by Node
$ curl -H "Content-Type: application/json" -d '{"id":1, "jsonrpc":"2.0", "method": "rpc_methods"}' http://localhost:9933/
```

### Pre-funded Development Accounts

- `./target/release/frontier-template-node build-spec --disable-default-bootnode --chain local > customSpec.json`

- Derivation Mneumonic for below addresses: `bottom drive obey lake curtain smoke basket hold race lonely fit walk`

```
Alith:
Public Address: 0xf24FF3a9CF04c71Dbc94D0b566f7A27B94566cac
Private Key: 0x5fb92d6e98884f76de468fa3f6278f8807c48bebc13595d45af5bdc4da702133

Baltathar:
Public Address: 0x3Cd0A705a2DC65e5b1E1205896BaA2be8A07c6e0
Private Key: 0x8075991ce870b93a8870eca0c0f91913d12f47948ca0fd25b49c6fa7cdbeee8b

Charleth:
Public Address: 0x798d4Ba9baf0064Ec19eB4F0a1a45785ae9D6DFc
Private Key: 0x0b6e18cafb6ed99687ec547bd28139cafdd2bffe70e6b688025de6b445aa5c5b

Dorothy:
Public Address: 0x773539d4Ac0e786233D90A233654ccEE26a613D9
Private Key: 0x39539ab1876910bbf3a223d84a29e28f1cb4e2e456503e7e91ed39b2e7223d68

Ethan:
Public Address: 0xFf64d3F6efE2317EE2807d223a0Bdc4c0c49dfDB
Private Key: 0x7dce9bc8babb68fec1409be38c8e1a52650206a7ed90ff956ae8a6d15eeaaef4

Faith:
Public Address: 0xC0F0f4ab324C46e55D02D0033343B4Be8A55532d
Private Key: 0xb9d2ea9a615f3165812e8d44de0d24da9bbd164b65c4f0573e1ce2c8dbd9c8df

Goliath:
Public Address: 0x7BF369283338E12C90514468aa3868A551AB2929
Private Key: 0x96b8a38e12e1a31dee1eab2fffdf9d9990045f5b37e44d8cc27766ef294acf18

Heath:
Public Address: 0x931f3600a299fd9B24cEfB3BfF79388D19804BeA
Private Key: 0x0d6dcaaef49272a5411896be8ad16c01c35d6f8c18873387b71fbc734759b0ab

Ida:
Public Address: 0xC41C5F1123ECCd5ce233578B2e7ebd5693869d73
Private Key: 0x4c42532034540267bf568198ccec4cb822a025da542861fcb146a5fab6433ff8

Judith:
Public Address: 0x2898FE7a42Be376C8BC7AF536A940F7Fd5aDd423
Private Key: 0x94c49300a58d576011096bcb006aa06f5a91b34b4383891e8029c21dc39fbb8b

// for dev chain only
Gerald:
Public Address: 0x6Be02d1d3665660d22FF9624b7BE0551ee1Ac91b
Private Key: 0x99b3c12287537e38c90a9219d4cb074a89a16e9cdb20bf85728ebd97c343e342
```

- Add Predefined Accounts with Balance in customSpec.json

```
[
"0xf24FF3a9CF04c71Dbc94D0b566f7A27B94566cac",
1152921504606846976
],
[
"0x3Cd0A705a2DC65e5b1E1205896BaA2be8A07c6e0",
1152921504606846976
],
[
"0x798d4Ba9baf0064Ec19eB4F0a1a45785ae9D6DFc",
1152921504606846976
],
[
"0x773539d4Ac0e786233D90A233654ccEE26a613D9",
1152921504606846976
],
```

- `./target/release/frontier-template-node build-spec --chain=customSpec.json --raw --disable-default-bootnode > customSpecRaw.json`

### Fix - Downgrade Rust Toolchain

```
rustup install 1.68.0
rustup default 1.68.0

rustup toolchain install nightly-2023-01-30
rustup target add wasm32-unknown-unknown --toolchain nightly-2023-01-30
rustup override set nightly-2023-01-30
```

## Next: Setting up Multi Node environment

Steps Involved:

1. Purge Existing Chains, if you want to upgrade the chain, purging is not necessary.
2. Generate AURA and GRANDPA Keys
3. Update Custom Chain Specification accordingly with updated keys # In current repository, we've already pushed an updated chainSpec.json | To generate: `./target/release/frontier-template-node build-spec --disable-default-bootnode --chain local > customSpec.json` then `./target/release/frontier-template-node build-spec --chain=customSpec.json --raw --disable-default-bootnode > customSpecRaw.json`
4. Spin Nodes

- Node 01: 54.166.168.169
- Node 02: 54.159.243.174
- Node 03: 3.94.204.252

### Purge Any Existing Chain, if any:

`./target/release/frontier-template-node purge-chain --base-path /tmp/alice --chain local`

### Steps to generate a Key 1

1. Generate Key for Aura (--scheme Sr25519): `./target/release/frontier-template-node key generate --scheme Sr25519 --password-interactive`

- Add Random Key password: `12345678`
- Generated Aura Key should look like this below:

```
Secret phrase:       very museum phone decade snack design lock throw brother fly dwarf great
  Network ID:        substrate
  Secret seed:       0xbe9f179926a539cd0e27400f09b3f902826e30d9ef64e0675682c81a4344c616
  Public key (hex):  0x00fb3eb89b326db889521f202fd81e20ba85e755c03ce02b6649c1d1cf15aa6d
  Account ID:        0x00fb3eb89b326db889521f202fd81e20ba85e755c03ce02b6649c1d1cf15aa6d
  Public key (SS58): 5C5zVX5WH5PoHAPR27xus3CvvUVo6egT1VRs2NGZMYRe2o2s
  SS58 Address:      5C5zVX5WH5PoHAPR27xus3CvvUVo6egT1VRs2NGZMYRe2o2s
```

2. Generate Key for Grandpa (--scheme Ed25519): `./target/release/frontier-template-node key inspect --password-interactive --scheme Ed25519 "very museum phone decade snack design lock throw brother fly dwarf great"`

- Add Random Key password: `12345678`
- Generated Grandpa Key should look like this below:

```
Secret phrase:       very museum phone decade snack design lock throw brother fly dwarf great
  Network ID:        substrate
  Secret seed:       0xbe9f179926a539cd0e27400f09b3f902826e30d9ef64e0675682c81a4344c616
  Public key (hex):  0xf434ac5bff2bd59b76b11f3e408d9019c1fe8aaaced3224ff45fd2969f427c01
  Account ID:        0xf434ac5bff2bd59b76b11f3e408d9019c1fe8aaaced3224ff45fd2969f427c01
  Public key (SS58): 5HauBsZXjaRfBDmotWorrEh15W2mGB7c33H7o7mHdCfZGvDf
  SS58 Address:      5HauBsZXjaRfBDmotWorrEh15W2mGB7c33H7o7mHdCfZGvDf
```

### Steps to generate a Key 2

1. Generate Key for Aura (--scheme Sr25519): `./target/release/frontier-template-node key generate --scheme Sr25519 --password-interactive`

- Add Random Key password: `12345678`
- Generated Aura Key should look like this below:

```
Secret phrase:       tide front tattoo nerve kingdom resist organ recipe chicken chimney area then
  Network ID:        substrate
  Secret seed:       0x1d78c2e3243ba2a497dabf1febc9f8a3e60527b5f3857e438864fca4cd568727
  Public key (hex):  0x5c6bc578b45d3c3ff32b2abb7ec5f6e568526ac04a574e87fd795464fe17b104
  Account ID:        0x5c6bc578b45d3c3ff32b2abb7ec5f6e568526ac04a574e87fd795464fe17b104
  Public key (SS58): 5E9tH3DfTK4ocWWvM87N54c3wDzGdh496cRgFAenKvWzrHzz
  SS58 Address:      5E9tH3DfTK4ocWWvM87N54c3wDzGdh496cRgFAenKvWzrHzz
```

2. Generate Key for Grandpa (--scheme Ed25519): `./target/release/frontier-template-node key inspect --password-interactive --scheme Ed25519 "tide front tattoo nerve kingdom resist organ recipe chicken chimney area then"`

- Add Random Key password: `12345678`
- Generated Grandpa Key should look like this below:

```
Secret phrase:       tide front tattoo nerve kingdom resist organ recipe chicken chimney area then
  Network ID:        substrate
  Secret seed:       0x1d78c2e3243ba2a497dabf1febc9f8a3e60527b5f3857e438864fca4cd568727
  Public key (hex):  0xb5be7c5872c6e3f45d0f954a614ccc192e886ec6d6697704fb47a4ff18ec0165
  Account ID:        0xb5be7c5872c6e3f45d0f954a614ccc192e886ec6d6697704fb47a4ff18ec0165
  Public key (SS58): 5GB17MYVuVxtirTXerTR6QCvQaVBkes6cgQRUgJWywY4uVhP
  SS58 Address:      5GB17MYVuVxtirTXerTR6QCvQaVBkes6cgQRUgJWywY4uVhP
```

### Steps to generate a Key 3

1. Generate Key for Aura (--scheme Sr25519): `./target/release/frontier-template-node key generate --scheme Sr25519 --password-interactive`

- Add Random Key password: `12345678`
- Generated Aura Key should look like this below:

```
Secret phrase:       taste seek proof milk scene trumpet account run toilet absorb elegant bullet
  Network ID:        substrate
  Secret seed:       0x44cbfbce4393330fc1da26adf96dc9d54b62aaeba4749925e1e8d43dcf7c5c2f
  Public key (hex):  0x445de49b6c60338c106c2030664dd688b34d6716a00603ebbca12b8560269e2d
  Account ID:        0x445de49b6c60338c106c2030664dd688b34d6716a00603ebbca12b8560269e2d
  Public key (SS58): 5DcM1GJgqKnDKR9kRSDWrdm5Dd6rPDCvuHjcKyasWskiKvRa
  SS58 Address:      5DcM1GJgqKnDKR9kRSDWrdm5Dd6rPDCvuHjcKyasWskiKvRa
```

2. Generate Key for Grandpa (--scheme Ed25519): `./target/release/frontier-template-node key inspect --password-interactive --scheme Ed25519 "taste seek proof milk scene trumpet account run toilet absorb elegant bullet"`

- Add Random Key password: `12345678`
- Generated Grandpa Key should look like this below:

```
Secret phrase:       taste seek proof milk scene trumpet account run toilet absorb elegant bullet
  Network ID:        substrate
  Secret seed:       0x44cbfbce4393330fc1da26adf96dc9d54b62aaeba4749925e1e8d43dcf7c5c2f
  Public key (hex):  0x8619203fea5cead62be3c7cba701ccc3d7bf4a932c093bfa84e8ea67f7ef6845
  Account ID:        0x8619203fea5cead62be3c7cba701ccc3d7bf4a932c093bfa84e8ea67f7ef6845
  Public key (SS58): 5F6XjjpkuyxUnUH3W45VQM6rgo4BcrBrJAi3uwjQCHFFWRCK
  SS58 Address:      5F6XjjpkuyxUnUH3W45VQM6rgo4BcrBrJAi3uwjQCHFFWRCK
```

### Aura Node Addresses

- Node 1: 5C5zVX5WH5PoHAPR27xus3CvvUVo6egT1VRs2NGZMYRe2o2s
- Node 2: 5E9tH3DfTK4ocWWvM87N54c3wDzGdh496cRgFAenKvWzrHzz
- Node 3: 5DcM1GJgqKnDKR9kRSDWrdm5Dd6rPDCvuHjcKyasWskiKvRa

### Grandpa Node Addresses

- Node 1: 5HauBsZXjaRfBDmotWorrEh15W2mGB7c33H7o7mHdCfZGvDf
- Node 2: 5GB17MYVuVxtirTXerTR6QCvQaVBkes6cgQRUgJWywY4uVhP
- Node 3: 5F6XjjpkuyxUnUH3W45VQM6rgo4BcrBrJAi3uwjQCHFFWRCK

### Notes

- Below we'll be spinning 3 nodes on 3 different machines, therefore, we'll be using same PORT WSPORT & RPCPORT across all 3 nodes. In case we need to run 3 instances on same machine, do change ports accordingly.

### Spin Node 1:

- With Built-in Account: ALICE
  ```
  ./target/release/frontier-template-node \
  --base-path /tmp/alice \
  --chain local \
  --alice \
  --port 30333 \
  --ws-port 9945 \
  --rpc-port 9933 \
  --node-key 0000000000000000000000000000000000000000000000000000000000000001 \
  --telemetry-url "wss://telemetry.polkadot.io/submit/ 0" \
  --validator
  ```
- With Custom Account using Custom Chain Specification

  ```
  ./target/release/frontier-template-node \
  --base-path /tmp/node01 \
  --chain ./customSpecRaw.json \
  --port 30333 \
  --ws-port 9945 \
  --rpc-port 9933 \
  --telemetry-url "wss://telemetry.polkadot.io/submit/ 0" \
  --validator \
  --rpc-methods Unsafe \
  --name Node01 \
  --password-interactive \
  --rpc-cors all \
  --unsafe-rpc-external \
  --unsafe-ws-external \
  --prometheus-external \
  --state-pruning archive-canonical \
  --blocks-pruning archive-canonical \
  --ws-max-connections 20000 \
  --rpc-max-subscriptions-per-connection 10240 
  ```

  - Node Identity: 12D3KooWGSn5xc1w7p7f3EdKxL5MWwigpXD5WLS9Dibo9hAcJswq
  - Add keys to the keystore; aura authority keys to enable block production; grandpa authority keys to enable block finalization.

  ```
  ./target/release/frontier-template-node key insert --base-path /tmp/node01 \
    --chain customSpecRaw.json \
    --scheme Sr25519 \
    --suri "very museum phone decade snack design lock throw brother fly dwarf great" \
    --password-interactive \
    --key-type aura

  ./target/release/frontier-template-node key insert \
    --base-path /tmp/node01 \
    --chain customSpecRaw.json \
    --scheme Ed25519 \
    --suri "very museum phone decade snack design lock throw brother fly dwarf great" \
    --password-interactive \
    --key-type gran
  ```

  - Verify that your keys are in the keystore for node: `ls /tmp/node01/chains/local_testnet/keystore`
  - close (ctrl+c) and Restart the chain referring "With Custom Account using Custom Chain Specification"

### Spin Node 2:

- With Built-in Account: BOB
  ```
  ./target/release/frontier-template-node \
  --base-path /tmp/bob \
  --chain local \
  --bob \
  --port 30333 \
  --ws-port 9945 \
  --rpc-port 9933 \
  --node-key 0000000000000000000000000000000000000000000000000000000000000001 \
  --telemetry-url "wss://telemetry.polkadot.io/submit/ 0" \
  --validator
  ```
- With Custom Account using Custom Chain Specification

  ```
  ./target/release/frontier-template-node \
  --base-path /tmp/node02 \
  --chain ./customSpecRaw.json \
  --port 30333 \
  --ws-port 9945 \
  --rpc-port 9933 \
  --telemetry-url "wss://telemetry.polkadot.io/submit/ 0" \
  --validator \
  --rpc-methods Unsafe \
  --name Node02 \
  --password-interactive \
  --rpc-cors all \
  --unsafe-rpc-external \
  --unsafe-ws-external \
  --prometheus-external \
  --state-pruning archive-canonical \
  --blocks-pruning archive-canonical \
  --ws-max-connections 20000 \
  --rpc-max-subscriptions-per-connection 10240 \
  --bootnodes /ip4/54.166.168.169/tcp/30333/p2p/12D3KooWGSn5xc1w7p7f3EdKxL5MWwigpXD5WLS9Dibo9hAcJswq
  ```

  - Node Identity: 12D3KooWMTZY6rmcfe5a9QeyJ1Ej34UbWQZ2wAf3UnMdASg9WDkA
  - Add keys to the keystore; aura authority keys to enable block production; grandpa authority keys to enable block finalization.

  ```
  ./target/release/frontier-template-node key insert --base-path /tmp/node02 \
    --chain customSpecRaw.json \
    --scheme Sr25519 \
    --suri "tide front tattoo nerve kingdom resist organ recipe chicken chimney area then" \
    --password-interactive \
    --key-type aura

  ./target/release/frontier-template-node key insert \
    --base-path /tmp/node02 \
    --chain customSpecRaw.json \
    --scheme Ed25519 \
    --suri "tide front tattoo nerve kingdom resist organ recipe chicken chimney area then" \
    --password-interactive \
    --key-type gran
  ```

  - Verify that your keys are in the keystore for node: `ls /tmp/node02/chains/local_testnet/keystore`
  - close (ctrl+c) and Restart the chain referring "With Custom Account using Custom Chain Specification"

### Spin Node 3:

- With Built-in Account: CHARLIE
  ```
  ./target/release/frontier-template-node \
  --base-path /tmp/charlie \
  --chain local \
  --charlie \
  --port 30333 \
  --ws-port 9945 \
  --rpc-port 9933 \
  --node-key 0000000000000000000000000000000000000000000000000000000000000001 \
  --telemetry-url "wss://telemetry.polkadot.io/submit/ 0" \
  --validator
  ```
- With Custom Account using Custom Chain Specification
  ```
    ./target/release/frontier-template-node \
  --base-path /tmp/node03 \
  --chain ./customSpecRaw.json \
  --port 30333 \
  --ws-port 9945 \
  --rpc-port 9933 \
  --telemetry-url "wss://telemetry.polkadot.io/submit/ 0" \
  --validator \
  --rpc-methods Unsafe \
  --name Node03 \
  --password-interactive \
  --rpc-cors all \
  --unsafe-rpc-external \
  --unsafe-ws-external \
  --prometheus-external \
  --state-pruning archive-canonical \
  --blocks-pruning archive-canonical \
  --ws-max-connections 20000 \
  --rpc-max-subscriptions-per-connection 10240 \
  --bootnodes /ip4/54.166.168.169/tcp/30333/p2p/12D3KooWGSn5xc1w7p7f3EdKxL5MWwigpXD5WLS9Dibo9hAcJswq
  ```

  - Node Identity: 12D3KooWRm3N6AAUMHPQXjLVmFWAM9SWAX6uzGSpKYpabwwyU5bw
  - Add keys to the keystore; aura authority keys to enable block production; grandpa authority keys to enable block finalization.

  ```
  ./target/release/frontier-template-node key insert --base-path /tmp/node03 \
    --chain customSpecRaw.json \
    --scheme Sr25519 \
    --suri "taste seek proof milk scene trumpet account run toilet absorb elegant bullet" \
    --password-interactive \
    --key-type aura

  ./target/release/frontier-template-node key insert \
    --base-path /tmp/node03 \
    --chain customSpecRaw.json \
    --scheme Ed25519 \
    --suri "taste seek proof milk scene trumpet account run toilet absorb elegant bullet" \
    --password-interactive \
    --key-type gran
  ```

  - Verify that your keys are in the keystore for node: `ls /tmp/node03/chains/local_testnet/keystore`
  - close (ctrl+c) and Restart the chain referring "With Custom Account using Custom Chain Specification"

### Notes

- you should see the same genesis block and state root hashes.
- state root hash: 0x2dd7046e55d09c05af2c4126f3a5715f2d5fa808a553548bab5a1a97f4babb02
- Genesis block/state (state: 0x2dd7046e55d09c05af2c4126f3a5715f2d5fa808a553548bab5a1a97f4babb02, header-hash: 0x2dd7…bb02)
