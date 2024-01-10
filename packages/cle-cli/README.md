# CLE CLI

## CLI

> Note: Only `full` image will be processed by zkOracle node. `local` (generated by commands ending with `--local` option) means the CLE is compiled locally and only contains partial computation (so that proving and executing will be faster).

The workflow of local CLE development must follow: `Develop` (code in /src) -> `Compile` (get compiled wasm image) -> `Execute` (get expected output) -> `Prove` (generate input and pre-test for actual proving in zkOracle) -> `Verify` (verify proof on-chain).

To upload and publish your CLE, you should `Upload` (upload code to IPFS), and then `Publish` (register CLE on onchain CLE Registry).

### Compile

Compile for Full Image (Link Compiled with Compiler Server).

#### Usage

```bash
cle compile [root]
```

#### Options

| Options                 | Description             |
| ----------------------- | ----------------------- |
| `--local`               | Compile for Local Image |
| `--yaml-path <path>`    | Path to yaml file       |
| `--mapping-path <path>` | Path to mapping file    |

### Execute

Execute Full Image.

Please save the `CLE_STATE_OUTPUT` string for following prove steps.

#### Usage
```bash
cle exec [...params] [root]
```

#### Usage cases
```bash
cle exec <blockId> [root]
cle exec <blockId> <offchainData> [root]
```


#### Arguments

| Arguments        | Description                                     |
| ---------------- | ----------------------------------------------- |
| `<block id>`     | Block number (or block hash) as runtime context |
| `<offchainData>` | offchain data                                   |


#### Options

| Options   | Description         |
| --------- | ------------------- |
| `--local` | Execute Local Image |

### Set Up

Set Up Full Image

- `circuit-size`: Specify the circuit size of image instead of the default recommended. eg. `cle setup -- --circuit-size <size> (eg. 22)`.

#### Usage
```bash
cle setup [root]
```

#### Options

| Options                     | Description                      |
| --------------------------- | -------------------------------- |
| `--local`                   | Set Up Local Image               |
| `-k, --circuit-size <size>` | Circuit size (k in 2^k) of image |

### Prove

Prove Full Image

#### Usage
```bash
cle prove [...params] [root]
```

#### Usage cases
```bash
cle prove <blockId> <expectedStateStr> [root]
cle prove <blockId> <offchainData> <expectedStateStr> [root]
```

#### Arguments

| Arguments          | Description                                     |
| ------------------ | ----------------------------------------------- |
| `<block id>`       | Block number (or block hash) as runtime context |
| `<expected state>` | State output of the CLE execution           |
| `<offchainData>`   | offchain data                                   |


#### Options

| Options          | Description                  |
| ---------------- | ---------------------------- |
| `--local`        | Prove Local Image            |
| `-i, --inputgen` | Run in input generation Mode |
| `-t, --test`     | Run in test Mode             |
| `-p, --prove`    | Run in prove Mode            |

### Upload

Upload CLE (Code and Full Image).

Please save the `ipfs_hash` from the output dialog for following publish steps.

#### Usage
```bash
cle upload [root]
```

#### Options

| Options   | Description                                 |
| --------- | ------------------------------------------- |
| `--local` | Upload Local CLE (Code and Local Image) |


### Verify

Verify Proof Onchain.

#### Usage
```bash
cle verify <prove task id>
```

#### Arguments

| Arguments         | Description           |
| ----------------- | --------------------- |
| `<prove task id>` | Task id of prove task |


### Publish

Publish and Register CLE Onchain.

See also: [Verifier Contract Interface](https://github.com/DelphinusLab/halo2aggregator-s/blob/main/sol/contracts/AggregatorVerifier.sol#L40).

#### Usage
```bash
cle publish <ipfs_hash> [bounty_reward_per_trigger]
```

#### Arguments

| Arguments                     | Description                                                |
| ----------------------------- | ---------------------------------------------------------- |
| `<ipfs hash>`                 | IPFS hash of uploaded CLE                              |
| `[bounty reward per trigger]` | Bounty reward per trigger in ETH                           |

### Deposit

Publish and register CLE Onchain.

#### Usage
```bash
cle deposit <deployed contract address> <deposit amount>
```

#### Arguments

| Arguments                     | Description                                                |
| ----------------------------- | ---------------------------------------------------------- |
| `<deployed contract address>` | Contract address of deployed verification contract address |
| `<deposit amount>`            | Deposit amount in ETH                                      |

## Config

### Configuring CLE

When running CLE from the command line, the tool will automatically attempt to locate a configuration file named cle.config.js in the project’s root directory. It also supports other file extensions such as JS and TS.

The most basic config file looks like this:

```js
// cle.config.js
export default {
  // config options
}
```

You can also explicitly specify a config file to use with the --config CLI option (resolved relative to cwd):

```bash
cle --config my-config.js
```

### Config Intellisense

Since CLE ships with TypeScript typings, you can leverage your IDE's intellisense with jsdoc type hints:

```js
/** @type {import('@hyperoracle/cle-cli-test').UserConfig} */
export default {
  // ...
}
```
Alternatively, you can use the defineConfig helper which should provide intellisense without the need for jsdoc annotations:

```js
import { defineConfig } from '@hyperoracle/cle-cli-test'

export default defineConfig({
  // ...
})
```

CLE also directly supports TS config files. You can use cle.config.ts with the defineConfig helper as well.

### Config Options

#### JsonRpcProviderUrl

- **Type:** `object`
- **Default**: `{ mainet: "", sepolia: "", goerli: ""}`

Update your Etherum JSON RPC provider URL here.
It is recommended to use providers that support debug_getRawReceipts RPC method.  

#### UserPrivateKey

- **Type:** `string`

Update your private key here to sign zkwasm messages.
Please note that (during testnet phrase) your address balance (in zkwasm server) should > 0.


#### ZkwasmProviderUrl

- **Type:** `string`
- **Default**: `https://zkwasm-explorer.delphinuslab.com:8090`

#### CompilerServerEndpoint

- **Type:** `string`
- **Default**: `http://compiler.hyperoracle.io/compile`

#### PinataEndpoint

- **Type:** `string`
- **Default**: `https://api.pinata.cloud/pinning/pinFileToIPFS`

#### PinataJWT

- **Type:** `string`

#### WasmBinPath

- **Type:** `string`
- **Default**: `[root]/build/cle_full.wasm`

CLE CLI Build-In a tag name is `root`.  
The `root` is user project root path.  
Of course, you can also place this tag at any position within the string.  
`LocalWasmBinPath` is the same.

#### LocalWasmBinPath

- **Type:** `string`
- **Default**: `[root]/build/cle_local.wasm`