# CMTAT Solana

This document describes the features needed to deploy a CMTAT compliant token on Solana.
CMTAT specifications are available on CMTA website: [https://cmta.ch/standards/cmta-token-cmtat](https://cmta.ch/standards/cmta-token-cmtat)

## Solana introduction

Tokens on Solana are digital assets that represent ownership over diverse categories of assets. Tokenization enables the digitalization of property rights. 

Tokens on Solana are referred to as SPL ([Solana Program Library](https://github.com/solana-program)) Tokens.

### Solana Token

This program defines a common implementation for Fungible and Non Fungible tokens.

#### Key Points

- [Token Programs](https://solana.com/docs/tokens#token-program) contain all instruction logic for interacting with tokens on the network (both fungible and non-fungible).
- A [Mint Account](https://solana.com/docs/tokens#mint-account) represents a specific token and stores global metadata about the token such as the total supply and mint authority (address authorized to create new units of a token).
- A [Token Account](https://solana.com/docs/tokens#token-account) tracks individual ownership of tokens for a specific mint account for a specific owner.
- An [Associated Token Account](https://solana.com/docs/tokens#associated-token-account) is a Token Account created with an address derived from the owner and mint account addresses.

#### Features

- Create a Token Mint – Initialize a new SPL Token mint.
- Create a Token Account – Open an account to hold SPL tokens.
- Mint Tokens – Generate new units of the token and assign them to an account.
- Transfer Tokens – Send tokens between accounts securely.
- Approve Delegate – Grant another account permission to manage tokens on behalf of the owner.
- Revoke Delegate – Remove delegate permissions from an account.
- Set Authority – Change the authority for a mint or token account (e.g., minting, freezing, account management).
- Burn Tokens – Permanently destroy tokens, reducing supply.
- Sync Native – Wrap native SOL into wrapped SOL (WSOL) for token program compatibility.
- Close Token Account – Close an SPL Token account and reclaim its SOL rent.
- Freeze Account – Halt activity on a token account.
- Thaw Account – Reactivate a previously frozen token account.

### Solana Extension (Token-2022)

The Token-2022 Program, also known as Token Extensions, is a superset of the functionality provided by the [Token Program](https://www.solana-program.com/docs/token).

Token extensions introduce a new set of ways to extend the normal token functionality. 

- The original Token program brought the basic capabilities of minting, transferring and freezing tokens. 
- The Token Extensions program includes the same features, but come with additional features such as confidential transfers, custom transfer logic, extended metadata, and much more.
- The [Token Extensions program](https://spl.solana.com/token-2022) has the programID `TokenzQdBNbLqP5VEhdkAS6EPFLC1PHnBqCXEpPxuEb` and is a superset of the original functionality provided by the [Token Program](https://spl.solana.com/token) at `TokenkegQfeZyiNwAJbNbGKPFXCWuBvf9Ss623VQ5DA`.

Here the full list of available extensions on Solana Token-2022 program

#### Mint extensions

Mint extensions currently include:

- confidential transfers
- confidential mint-burn
- transfer fees
- closing mint
- interest-bearing tokens
- non-transferable tokens
- permanent delegate
- transfer hook
- metadata pointer
- metadata
- group pointer
- group
- group member pointer
- group member
- scaled UI amount
- pausable

#### Account extensions

Account extensions currently include:

- memo required on incoming transfers
- immutable ownership
- default account state
- CPI guard

## CMTAT - Solana Requirements

### Comparison tab

This section contains three tab to indicate if a Solana token features or an extension is required or optional by CMTAT.

#### Solana token basic -> CMTAT

| **Solana  Token features**                                   | **Description**                                              | **Solana****CMTAT****Required** | **CMTAT Required** | **CMTAT optional** | **CMTAT corresponding features** | **Note**                                             |
| :----------------------------------------------------------- | :----------------------------------------------------------- | :------------------------------ | :----------------- | :----------------- | :------------------------------- | :--------------------------------------------------- |
| [Create a Token Mint](https://solana.com/docs/tokens/basics/create-mint) | A mint account uniquely represents a token on Solana and stores its global metadata. | &#x2611;                        | N/A                | N/A                | N/A                              | Correspond to deploy the smart contract on EVM chain |
| [Create a Token Account](https://solana.com/docs/tokens/basics/create-token-account) | A token account stores your balance of a specific token.     | &#x2611;                        | N/A                | N/A                | N/A                              | No relevant for EVM based blockchain                 |
| [Mint Tokens](https://solana.com/docs/tokens/basics/mint-tokens) | Minting creates new units of a token using the `MintTo` instruction. | &#x2611;                        | &#x2611;           | &#x2612;           | mint/batchMint                   |                                                      |
| [Transfer Tokens](https://solana.com/docs/tokens/basics/transfer-tokens) |                                                              | &#x2611;                        | &#x2611;           | &#x2612;           | ERC20 transfer                   |                                                      |
| [Approve Delegate](https://solana.com/docs/tokens/basics/approve-delegate) | The `ApproveChecked` instruction grants another account (the delegate) permission to transfer a specific amount of tokens from your token account. | &#x2611;                        | &#x2611;           | &#x2612;           | ERC-20 approve                   |                                                      |
| [Revoke Delegate](https://solana.com/docs/tokens/basics/revoke-delegate) | The `Revoke` instruction removes all transfer permissions from the currently approved delegate. | &#x2611;                        | &#x2611;           | &#x2612;           | ERC-20 approve                   |                                                      |
| [Set Authority](https://solana.com/docs/tokens/basics/set-authority) | The `SetAuthority` instruction changes or revokes authorities on mints and token accounts. | &#x2611;                        | &#x2611;           | &#x2612;           | `RBAC system` `grantRole`        |                                                      |
| [Burn Tokens](https://solana.com/docs/tokens/basics/burn-tokens) | The `BurnChecked` instruction permanently destroys tokens by reducing the balance in a token account. | &#x2611;                        | &#x2612;           | &#x2611;           | `burnFrom`                       |                                                      |
| [Sync Native](https://solana.com/docs/tokens/basics/sync-native) | The `SyncNative` instruction synchronizes a wrapped SOL (WSOL) token account balance with the actual SOL (lamports) stored in it. | &#x2612;                        | &#x2612;           | &#x2612;           | &#x2612;                         |                                                      |
| [Close Token Account](https://solana.com/docs/tokens/basics/close-account) | The `CloseAccount` instruction permanently closes a token account and transfers all remaining SOL (rent) to a specified destination account. | &#x2611;                        | &#x2612;           | &#x2612;           | &#x2612;                         | Solana specific features (but relevant to implement) |
| [Freeze Account](https://solana.com/docs/tokens/basics/freeze-account) | The `FreezeAccount` instruction prevents all token transfers or token burns from a specific token account. | &#x2611;                        | &#x2611;           | &#x2612;           | freeze / setAddressFrozen        |                                                      |
| [Thaw Account](https://solana.com/docs/tokens/basics/thaw-account) | The `ThawAccount` instruction reverses a freeze, restoring full functionality to a previously frozen token account. | &#x2611;                        | &#x2611;           | &#x2612;           | unfreeze / setAddressFrozen      |                                                      |

#### Solana extensions -> CMTAT

| **Solana Extension**                                         | **Description**                                              | **CMTAT Required (Core features)** | **CMTAT optional** | **CMTAT Solidity corresponding features**        | **Note**                                                     |
| :----------------------------------------------------------- | :----------------------------------------------------------- | :--------------------------------- | :----------------- | :----------------------------------------------- | :----------------------------------------------------------- |
| [Mint Close Authority](https://www.solana-program.com/docs/token-2022/extensions#mint-close-authority) | Close mint account                                           | &#x2611;  (= deactivate)           | &#x2612;           | &#x2612;                                         |                                                              |
| [Transfer Fees](https://www.solana-program.com/docs/token-2022/extensions#transfer-fees) | Transferring tokens with a transfer fee                      | &#x2612;                           | &#x2612;           | &#x2612;                                         |                                                              |
| [Default Account State](https://www.solana-program.com/docs/token-2022/extensions#default-account-state) | Force all new token accounts to be frozen.                   | &#x2612;                           | &#x2611;           | CMTAT Allowlist / CMTAT with rule whitelist      | This feature seems similar to a whitelist based system       |
| [Immutable Owner](https://www.solana-program.com/docs/token-2022/extensions#immutable-owner) | Impossible to reassign ownership of an account.              | &#x2612;                           | &#x2612;           | &#x2612;                                         |                                                              |
| [Non-Transferable Tokens](https://www.solana-program.com/docs/token-2022/extensions#non-transferable-tokens) | Allows for "soul-bound" tokens that cannot be moved to any other entity. | &#x2612;                           | &#x2612;           | &#x2612;                                         |                                                              |
| [Required Memo on Transfer](https://www.solana-program.com/docs/token-2022/extensions#required-memo-on-transfer) | Enforces that all incoming transfers must have an accompanying memo instruction right before the transfer instruction. | &#x2612;                           | &#x2611;           | CMTAT ERC-1363                                   |                                                              |
| [Interest-Bearing Tokens](https://www.solana-program.com/docs/token-2022/extensions#interest-bearing-tokens) | Using the `InterestBearingMint` extension and the `amount_to_ui_amount` instruction, you can set an interest rate on your token and fetch its amount with interest at any time. | &#x2612;                           | &#x2612;           | &#x2612;                                         |                                                              |
| [Permanent Delegate](https://www.solana-program.com/docs/token-2022/extensions#permanent-delegate) | Allows to specify a permanent account delegate for a mint. This authority can burn or transfer any amount of tokens. | &#x2612;                           | &#x2611;           | Forced transfer                                  |                                                              |
| [CPI Guard](https://www.solana-program.com/docs/token-2022/extensions#cpi-guard) | CPI Guard is an extension that prohibits certain actions inside cross-program invocations. | &#x2612;                           | &#x2612;           | &#x2612;                                         | Solana specific extension. We should check if this feature is relevant or not |
| [Transfer Hook](https://www.solana-program.com/docs/token-2022/extensions#transfer-hook) | The Transfer Hook Interface is designed to allow token creators to "hook" additional functionality into token transfers. | &#x2612;                           | &#x2611;           | CMTAT with RuleEngine                            |                                                              |
| [Metadata Pointer](https://www.solana-program.com/docs/token-2022/extensions#metadata-pointer) | Allows a token creator to designate an address that describes the canonical metadata. | &#x2611;                           | &#x2612;           | N/A                                              | Requires because should be used alongside Metadata extension |
| [Metadata](https://www.solana-program.com/docs/token-2022/extensions#metadata) | Allows a mint creator to include their token's metadata directly in the mint account. | &#x2611;                           | &#x2612;           | ERC20 name & symbol terms attributes (uri, hash) | To do: create format for the terms                           |
| [Group Pointer](https://www.solana-program.com/docs/token-2022/extensions#group-pointer) | Allows a token creator to designate a group account that describes the mint. | &#x2612;                           | &#x2612;           | &#x2612;                                         |                                                              |
| [Group](https://www.solana-program.com/docs/token-2022/extensions#group) | Token-2022 supports grouping of tokens through the group extension. | &#x2612;                           | &#x2612;           | &#x2612;                                         |                                                              |
| [Member Pointer](https://www.solana-program.com/docs/token-2022/extensions#member-pointer) | The member pointer allows a token creator to designate a member account that describes the mint. | &#x2612;                           | &#x2612;           | &#x2612;                                         |                                                              |
| [Member](https://www.solana-program.com/docs/token-2022/extensions#member) | The configurations for a member (group address and the member's number) mcan be stored directly in the mint itself. | &#x2612;                           | &#x2612;           | &#x2612;                                         |                                                              |
| [Pausable](https://www.solana-program.com/docs/token-2022/extensions#pausable) | "Pause" all activity. During this time, it is not possible transfer, mint, or burn tokens. | &#x2611;                           | &#x2612;           | pause                                            | With CMTAT Solidity, you can still burn and mint while pause all transfers. |
| [Scaled UI Amount](https://www.solana-program.com/docs/token-2022/extensions#scaled-ui-amount) | Change how the UI amount of tokens are represented           | &#x2612;                           | &#x2612;           | &#x2612;                                         |                                                              |

 

#### CMTAT → Solana features(basic/extension)

| **CMTAT Functionalities**   | **CMTAT Core features** | **Solana token basic features**                              | **Solana Extension**                                         | **Other**                                                    |
| :-------------------------- | :---------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| Mint                        | &#x2611;                | [Mint Tokens](https://solana.com/docs/tokens/basics/mint-tokens) | &#x2612;                                                     | -                                                            |
| force burn                  | &#x2611;                | &#x2612;                                                     | [Permanent Delegate](https://www.solana-program.com/docs/token-2022/extensions#permanent-delegate) | -                                                            |
| force Transfer              | &#x2612;                | &#x2612;                                                     | [Permanent Delegate](https://www.solana-program.com/docs/token-2022/extensions#permanent-delegate) | -                                                            |
| freeze address individually | &#x2611;                | &#x2611;  (freeze Authority)                                 | &#x2612;                                                     | -                                                            |
| freeze partial token        | &#x2612;                | &#x2612;                                                     | &#x2612;                                                     | -                                                            |
| Pause                       | &#x2611;                | &#x2612;                                                     | `Pausable`                                                   | -                                                            |
| Deactivate                  | &#x2611;                | &#x2612;                                                     | &#x2611;  (Burn all tokens, close Mint Account, eventually revoke authorities) | -                                                            |
| Metadata                    | &#x2611;                | &#x2612;                                                     | [Metadata](https://www.solana-program.com/docs/token-2022/extensions#metadata) [Metadata Pointer](https://www.solana-program.com/docs/token-2022/extensions#metadata-pointer) See [token-metadata/interface at main · solana-program/token-metadata](https://github.com/solana-program/token-metadata/tree/main/interface) | -                                                            |
| Whitelisting                | &#x2612;                | &#x2612;                                                     | [Default Account State](https://www.solana-program.com/docs/token-2022/extensions#default-account-state) |                                                              |
| RuleEngine / transfer hook  | &#x2612;                | &#x2612;                                                     | [Transfer Hook](https://www.solana-program.com/docs/token-2022/extensions#transfer-hook) (not compatible with Confidential Transfer) |                                                              |
| Upgradibility               | &#x2612;                | No relevant                                                  | No relevant                                                  | -                                                            |
| Feepayer/gasless            | &#x2612;                | &#x2612;                                                     | &#x2612;                                                     | [Sponsoring Solana transactions - Coinbase Developer Documentation](https://docs.cdp.coinbase.com/server-wallets/v2/solana-features/sponsor-transactions) (managed at the transaction level, no link with the token |

## CMTAT Deployment Guide (Token-2022)

This guide includes the deployment of a token on Solana which respects the CMTAT specification and contains the following features

- Mint tokens
- Freeze/unfreeze accounts
- Burn and forced transfer (Permanent Delegate)
- Pause/resume token activity
- On-chain metadata with custom fields (`termsHash`, jurisdiction, issuer)
- Deactivate the token
  - close mint
- Name and Symbol

All the operations have been made on a Linux machine through the SPL command line on a local Solana blockchain

### Set up

#### Environment variable

To simplify command, different values are stored in the current bash session environment.

| **Variable**        | **Value**                                            |
| :------------------ | :--------------------------------------------------- |
| SOLANA_KEYPAIR      | Path to the JSON file containing the admin keypair   |
| USER_SOLANA_KEYPAIR | Path to the JSON file containing the user keypair    |
| TOKEN_MINT          | Token address                                        |
| TOKEN_ACCOUNT       | Admin Token account for the corresponding TOKEN_MINT |
| USER_TOKEN_ACCOUNT  | User Token account for the corresponding TOKEN_MINT  |

#### Generate Admin Keypair

This keypair will act as the mint authority, freeze authority, pause authority, and update authority for your token.

> **Important:** The following command generates a new keypair for demonstration/example purposes.
> Do not use this keypair in production, as it is stored locally and not secured in a hardware wallet or secure key management system. For production, use a **secure key management solution** (e.g., hardware wallet, secure enclave, or custodian service).

```bash
# Generate a new Solana keypair for admin authority 
solana-keygen new -o test_admin.json 
# Export environment variable to use this keypair with SPL commands export 
SOLANA_KEYPAIR=test_admin.json 
# Generate a new Solana keypair for a test user 
solana-keygen new -o test_user.json 
# Export environment variable to use this keypair with SPL commands 
export USER_SOLANA_KEYPAIR=test_user.json
```

- `test_admin.json` is the local file storing your keypair.
- `SOLANA_KEYPAIR` environment variable allows CLI tools (like `spl-token`) to reference it automatically.

------

### Create Token with the required extensions

```bash
spl-token create-token \  --program-id TokenzQdBNbLqP5VEhdkAS6EPFLC1PHnBqCXEpPxuEb \  --decimals  <decimals>\  --mint-authority < Solana Keypair> \  --enable-permanent-delegate \  --enable-pause \  --enable-close \  --enable-metadata  --enable-freeze
```

 

**Note**

-  `TokenzQdBNbLqP5VEhdkAS6EPFLC1PHnBqCXEpPxuEb` is the programId of the Token Extensions program. See [Getting Started with Token Extensions](https://solana.com/tr/developers/guides/token-extensions/getting-started) 
- `decimals`: as part of CMTA specification, decimal numbers must be set to zero (which means that the tokens admit no fractional parts), unless the law governing the tokenized security allows the transfer of fractions.
- You can decide to use a different keypair address for the `mint-authority`
  - At deployment, the command-line does not allow you to set a different key for the other authorities (`freeze`, `pause`,

#### Example

**Command**

> spl-token create-token   --program-id TokenzQdBNbLqP5VEhdkAS6EPFLC1PHnBqCXEpPxuEb   --decimals  0  --mint-authority $SOLANA_KEYPAIR   --enable-permanent-delegate   --enable-pause   --enable-close   --enable-metadata --enable-freeze --url [http://127.0.0.1:8899](http://127.0.0.1:8899/)

**Result**

> Creating token B537Vzh62QUbC8zpCLfyRU9DrewYUcjsePC1omDj6qTp under program TokenzQdBNbLqP5VEhdkAS6EPFLC1PHnBqCXEpPxuEb
> To initialize metadata inside the mint, please run `spl-token initialize-metadata B537Vzh62QUbC8zpCLfyRU9DrewYUcjsePC1omDj6qTp <YOUR_TOKEN_NAME> <YOUR_TOKEN_SYMBOL> <YOUR_TOKEN_URI>`, and sign with the mint authority.
>
> Address:  B537Vzh62QUbC8zpCLfyRU9DrewYUcjsePC1omDj6qTp
> Decimals:  0

Save the mint address as `TOKEN_MINT`.

```
export TOKEN_MINT=B537Vzh62QUbC8zpCLfyRU9DrewYUcjsePC1omDj6qTp
```



##### Verification

**Command**

```bash
spl-token display $TOKEN_MINT
```

**Result**

> SPL Token Mint
>   Address: B537Vzh62QUbC8zpCLfyRU9DrewYUcjsePC1omDj6qTp
>   Program: TokenzQdBNbLqP5VEhdkAS6EPFLC1PHnBqCXEpPxuEb
>   Supply: 0
>   Decimals: 0
>   Mint authority: <Solana account pubkey>
>   Freeze authority: <Solana account pubkey>
> Extensions
>   Close authority: <Solana account pubkey>
>   Permanent delegate: <Solana account pubkey>
>   Metadata Pointer:
>     Authority: <Solana account pubkey>
>     Metadata address: B537Vzh62QUbC8zpCLfyRU9DrewYUcjsePC1omDj6qTp

------

### Initialize On-Chain Metadata

```bash
spl-token initialize-metadata --url <url> \ --program-2022 --mint-authority <Solana Keypair> \ --update-authority <Solana Keypair> <name> <symbol> <Token URI>
```

**Note**

- The CMTA specification only includes the token name, symbol and the terms uri (if relevant)
- Terms uri: Reference to any legally required documentation about the distributed ledger or the smart contract, such as the tokenization terms, the terms of the instrument and other relevant documents (e.g. prospectus or key information document).
- Token-2022 Metadata Structure. See [Token metadata](https://github.com/solana-program/token-metadata/tree/main/interface) 

| **Field**             | **Type**                | **Description**                                              | **Example**                  |
| :-------------------- | :---------------------- | :----------------------------------------------------------- | :--------------------------- |
| `update_authority`    | `OptionalNonZeroPubkey` | Public key allowed to update metadata. If unset, metadata is immutable. | `7gH…kP1`                    |
| `mint`                | `Pubkey`                | The mint address this metadata belongs to (prevents spoofing). | `<address>`                  |
| `name`                | `String`                | Human-readable name of the token.                            | `CMTAT Token`                |
| `symbol`              | `String`                | Short token symbol (≤10 chars).                              | `CMTAT`                      |
| `uri`                 | `String`                | URI pointing to extended metadata or compliance docs (e.g. JSON file, website). | `https://cmta.ch/token.json` |
| `additional_metadata` | `Vec<(String, String)>` | Flexible key–value pairs for extra fields such as compliance info, issuer, or terms hash. | `("termsHash","Qm123…")`     |

#### Example

```bash
spl-token initialize-metadata --url http://127.0.0.1:8899 --program-2022 \ --mint-authority $SOLANA_KEYPAIR --update-authority \ $SOLANA_KEYPAIR $TOKEN_MINT CMTATToken CMTAT https://cmta.ch/token.json
```

 

**Verification**

```bash
spl-token display $TOKEN_MINT
```

> SPL Token Mint
>   Address: B537Vzh62QUbC8zpCLfyRU9DrewYUcjsePC1omDj6qTp
>   Program: TokenzQdBNbLqP5VEhdkAS6EPFLC1PHnBqCXEpPxuEb
>   Supply: 0
>   Decimals: 0
>   Mint authority: <Solana account pubkey>
>   Freeze authority: <Solana account pubkey>
> Extensions
>   Close authority: <Solana account pubkey>
>   Permanent delegate: <Solana account pubkey>
>   Metadata Pointer:
>     Authority: <Solana account pubkey>
>     Metadata address: B537Vzh62QUbC8zpCLfyRU9DrewYUcjsePC1omDj6qTp
>   Metadata:
>     Update Authority: <Solana account pubkey>
>     Mint: B537Vzh62QUbC8zpCLfyRU9DrewYUcjsePC1omDj6qTp
>     Name: CMTATToken
>     Symbol: CMTAT
>     URI: https://cmta.ch/token.json

### Create Token Account

```bash
spl-token create-account [OPTIONS] <TOKEN_MINT_ADDRESS> [ACCOUNT_KEYPAIR]
```

#### Admin

Create the token account for the admin

```bash
spl-token create-account --program-id TokenzQdBNbLqP5VEhdkAS6EPFLC1PHnBqCXEpPxuEb \  $TOKEN_MINT
```

**Result**

> Creating account Hfq8KnY81kfKByAoTy52NyMgQVQJ2tYg8DWYeqnytN9N

We export the token account created in an environment variable:

```bash
export TOKEN_ACCOUNT=Hfq8KnY81kfKByAoTy52NyMgQVQJ2tYg8DWYeqnytN9N 
```

#### User

Create a user token account

```bash
spl-token create-account --program-id TokenzQdBNbLqP5VEhdkAS6EPFLC1PHnBqCXEpPxuEb \ $TOKEN_MINT $USER_SOLANA_KEYPAIR
```

Result:

> Creating account JCh4TLAagtW2vFQwVuUVi3nVipp8HJUcdpQHiauhE6Sc

We export the token account created in an environment variable:

```bash
export USER_TOKEN_ACCOUNT=JCh4TLAagtW2vFQwVuUVi3nVipp8HJUcdpQHiauhE6Sc
```

### Mint Initial Supply

```bash
spl-token mint [OPTIONS] <TOKEN_MINT_ADDRESS> <TOKEN_AMOUNT> [--]  [RECIPIENT_TOKEN_ACCOUNT_ADDRESS]
```

 

#### Example

##### Admin

```bash
spl-token mint  --program-2022 --mint-authority \ $SOLANA_KEYPAIR $TOKEN_MINT 1000 $TOKEN_ACCOUNT
```

**Result**

> Minting 1000 tokens
>   Token: B537Vzh62QUbC8zpCLfyRU9DrewYUcjsePC1omDj6qTp
>   Recipient: Hfq8KnY81kfKByAoTy52NyMgQVQJ2tYg8DWYeqnytN9N

**Verification**

- Check the supply

```bash
spl-token supply $TOKEN_MINT
```

Result: `1000`

-  Check the token account balance

```bash
spl-token balance [OPTIONS] [TOKEN_MINT_ADDRESS] spl-token balance --program-2022 --address $TOKEN_ACCOUNT
```

Result: `1000`

##### User

```bash
spl-token mint  --program-2022 --mint-authority $SOLANA_KEYPAIR \ $TOKEN_MINT 300  $USER_TOKEN_ACCOUNT
```

**Result**

> Minting 300 tokens
>   Token: B537Vzh62QUbC8zpCLfyRU9DrewYUcjsePC1omDj6qTp
>   Recipient: JCh4TLAagtW2vFQwVuUVi3nVipp8HJUcdpQHiauhE6Sc

```bash
spl-token balance --program-2022 --address $USER_TOKEN_ACCOUNT
```

Result: 300

```bash
spl-token supply $TOKEN_MINT
```

Result: 1300

### Freeze / Unfreeze Accounts

The Mint may also contain a `freeze_authority` which can be used to issue `FreezeAccount` instructions that will render an Account unusable. 

- Token instructions that include a frozen account will fail until the Account is thawed using the `ThawAccount` instruction. 
- The `SetAuthority` instruction can be used to change a Mint's `freeze_authority`. 
- If a Mint's `freeze_authority` is set to `None` then account freezing and thawing is permanently disabled and all currently frozen accounts will also stay frozen permanently.

```bash
spl-token freeze [OPTIONS] <TOKEN_ACCOUNT_ADDRESS> spl-token thaw [OPTIONS] <TOKEN_ACCOUNT_ADDRESS>
```

**Example**

```bash
spl-token freeze --freeze-authority $SOLANA_KEYPAIR $USER_TOKEN_ACCOUNT
```

> Freezing account: JCh4TLAagtW2vFQwVuUVi3nVipp8HJUcdpQHiauhE6Sc
>   Token: B537Vzh62QUbC8zpCLfyRU9DrewYUcjsePC1omDj6qTp

**Transfer**

```bash
spl-token transfer [OPTIONS] \ <TOKEN_MINT_ADDRESS> <TOKEN_AMOUNT> \ <RECIPIENT_WALLET_ADDRESS or RECIPIENT_TOKEN_ACCOUNT_ADDRESS>
```

 **Example**

```bash
spl-token transfer --from $USER_SOLANA_KEYPAIR $TOKEN_MINT 100 $TOKEN_ACCOUNT
```

**Result**

> Transfer 100 tokens
>   Sender: JCh4TLAagtW2vFQwVuUVi3nVipp8HJUcdpQHiauhE6Sc
>   Recipient: Hfq8KnY81kfKByAoTy52NyMgQVQJ2tYg8DWYeqnytN9N
> Error: Client(Error { request: Some(SendTransaction), kind: RpcError(RpcResponseError { code: -32002, message: "Transaction simulation failed: Error processing Instruction 0: custom program error: 0x11", data: SendTransactionPreflightFailure(RpcSimulateTransactionResult { err: Some(InstructionError(0, Custom(17))), logs: Some(["Program TokenzQdBNbLqP5VEhdkAS6EPFLC1PHnBqCXEpPxuEb invoke [1]", "Program log: Instruction: TransferChecked", "Program log: Error: Account is frozen", "Program TokenzQdBNbLqP5VEhdkAS6EPFLC1PHnBqCXEpPxuEb consumed 1111 of 1111 compute units", "Program TokenzQdBNbLqP5VEhdkAS6EPFLC1PHnBqCXEpPxuEb failed: custom program error: 0x11"]), accounts: None, units_consumed: Some(1111), loaded_accounts_data_size: Some(684907), return_data: None, inner_instructions: None, replacement_blockhash: None }) }) })

```bash
spl-token thaw --freeze-authority $SOLANA_KEYPAIR $USER_TOKEN_ACCOUNT
```

**Result**

> Thawing account: JCh4TLAagtW2vFQwVuUVi3nVipp8HJUcdpQHiauhE6Sc
>   Token: B537Vzh62QUbC8zpCLfyRU9DrewYUcjsePC1omDj6qTp

Now we can perform our transfer:

```bash
spl-token transfer --from $USER_SOLANA_KEYPAIR $TOKEN_MINT 100 $TOKEN_ACCOUNT
```

**Result**

> Transfer 100 tokens
>   Sender: JCh4TLAagtW2vFQwVuUVi3nVipp8HJUcdpQHiauhE6Sc
>   Recipient: Hfq8KnY81kfKByAoTy52NyMgQVQJ2tYg8DWYeqnytN9N

### Burn Tokens / Forced Transfer (Permanent Delegate)

With Token-2022, it's possible to specify a permanent account delegate for a mint. This authority has unlimited delegate privileges over any account for that mint, meaning that it can burn or transfer any amount of tokens.

```bash
spl-token burn [OPTIONS] <TOKEN_ACCOUNT_ADDRESS> <TOKEN_AMOUNT>
```

#### Burn

```bash
spl-token burn $USER_TOKEN_ACCOUNT 5
```

#####  Signer as admin

**Result**

> Burn 5 tokens
>   Source: JCh4TLAagtW2vFQwVuUVi3nVipp8HJUcdpQHiauhE6Sc



##### User as signer

If I try to burn admin tokens with my user as the signer, I will have the error `InvalidAccountForFee` because my user does not have the delegate authority.

```bash
# Change the signer in the config for the user solana config 
set -k $USER_SOLANA_KEYPAIR solana address 
#result JCh4TLAagtW2vFQwVuUVi3nVipp8HJUcdpQHiauhE6Sc 
# try to burn admin token with the user as signer 
spl-token burn $TOKEN_ACCOUNT 5
```

**Result**

> Burn 5 tokens
>   Source: Hfq8KnY81kfKByAoTy52NyMgQVQJ2tYg8DWYeqnytN9N
> Error: Client(Error { request: Some(SendTransaction), kind: RpcError(RpcResponseError { code: -32002, message: "Transaction simulation failed: This account may not be used to pay transaction fees", data: SendTransactionPreflightFailure(RpcSimulateTransactionResult { err: Some(**InvalidAccountForFee**), logs: Some([]), accounts: None, units_consumed: Some(0), loaded_accounts_data_size: Some(0), return_data: None, inner_instructions: None, replacement_blockhash: None }) }) })

 

#### Forced transfer

A forced transfer is performed in the same way as a standard transfer

```bash
spl-token transfer [OPTIONS] <TOKEN_MINT_ADDRESS> \ <TOKEN_AMOUNT> \ <RECIPIENT_WALLET_ADDRESS or RECIPIENT_TOKEN_ACCOUNT_ADDRESS>
```

**Example**

```bash
# Change the signer in the config for the user solana config 
set -k $SOLANA_KEYPAIR solana address #result JCh4TLAagtW2vFQwVuUVi3nVipp8HJUcdpQHiauhE6Sc 
#  forced transfer tokens from user -> admin with the admin signer key 
spl-token transfer --from $USER_SOLANA_KEYPAIR \ $TOKEN_MINT 10 $TOKEN_ACCOUNT
```

 

**Result**

```
Transfer 10 tokens  Sender: JCh4TLAagtW2vFQwVuUVi3nVipp8HJUcdpQHiauhE6Sc  Recipient: Hfq8KnY81kfKByAoTy52NyMgQVQJ2tYg8DWYeqnytN9N
```

### Pause / Resume All Activity

By enabling the pausable extension on your mint, the program aborts all tranfers, mints, and burns when the `paused` flag is flipped.

####  Pause

```bash
spl-token pause [OPTIONS] <TOKEN_MINT_ADDRESS>
spl-token pause $TOKEN_MINT
```

**Result**

Pausing mint, burn, and transfer for B537Vzh62QUbC8zpCLfyRU9DrewYUcjsePC1omDj6qTp

 

Trying a transfer will generate *Program log: Transferring, minting, and burning is paused on this mint*

```bash
spl-token transfer --from $USER_SOLANA_KEYPAIR $TOKEN_MINT 10 $USER_TOKEN_ACCOUNT 
```

> Transfer 10 tokens  Sender: JCh4TLAagtW2vFQwVuUVi3nVipp8HJUcdpQHiauhE6Sc  
> Recipient: JCh4TLAagtW2vFQwVuUVi3nVipp8HJUcdpQHiauhE6Sc
> Error: Client(Error { request: Some(SendTransaction), kind: RpcError(RpcResponseError { code: -32002, message: "Transaction simulation failed: Error processing Instruction 0: custom program error: 0x43", data: SendTransactionPreflightFailure(RpcSimulateTransactionResult { err: Some(InstructionError(0, Custom(67))), logs: Some(["Program TokenzQdBNbLqP5VEhdkAS6EPFLC1PHnBqCXEpPxuEb invoke [1]", "Program log: Instruction: TransferChecked", "Program log: Transferring, minting, and burning is paused on this mint", "Program TokenzQdBNbLqP5VEhdkAS6EPFLC1PHnBqCXEpPxuEb consumed 2059 of 2059 compute units", "Program TokenzQdBNbLqP5VEhdkAS6EPFLC1PHnBqCXEpPxuEb failed: custom program error: 0x43"]), accounts: None, units_consumed: Some(2059), loaded_accounts_data_size: Some(684669), return_data: None, inner_instructions: None, replacement_blockhash: None }) }) })

------

### Update On-Chain Metadata

To facilitate token-metadata usage, Token-2022 allows a mint creator to include their token's metadata directly in the mint account.

```bash
spl-token update-metadata <TOKEN_MINT_ADDRESS> <FIELD_NAME>
```

 **Example**

```bash
# Update name, symbol, or URI spl-token update-metadata $TOKEN_MINT name "New CMTAT Token" 
spl-token update-metadata $TOKEN_MINT symbol "NEW" 
spl-token update-metadata $TOKEN_MINT uri "https://cmta.ch/token_new.json"  
# Add custom compliance fields 
spl-token update-metadata $TOKEN_MINT TermsUri "https://cmta.ch/token_new.pdf spl-token update-metadata $TOKEN_MINT termsHash \ "0x9c22ff5f21f0b81b113e63f7db6da94fedef11b2119b4088b89664fb9a3cb658" 
spl-token update-metadata $TOKEN_MINT jurisdiction "EU-MiCA" 
spl-token update-metadata $TOKEN_MINT issuer "CMTA Ltd." 
```

**Result**

```bash
spl-token display $TOKEN_MINT
```

> SPL Token Mint
>   Address: B537Vzh62QUbC8zpCLfyRU9DrewYUcjsePC1omDj6qTp
>   Program: TokenzQdBNbLqP5VEhdkAS6EPFLC1PHnBqCXEpPxuEb
>   Supply: 1245
>   Decimals: 0
>   Mint authority: Ao4rvCKFNGqUo5dd79J3Cf9MwG5PyW8nJRi5U6dHyppg
>   Freeze authority: Ao4rvCKFNGqUo5dd79J3Cf9MwG5PyW8nJRi5U6dHyppg
> Extensions
>   Close authority: Ao4rvCKFNGqUo5dd79J3Cf9MwG5PyW8nJRi5U6dHyppg
>   Permanent delegate: Ao4rvCKFNGqUo5dd79J3Cf9MwG5PyW8nJRi5U6dHyppg
>   Metadata Pointer:
>     Authority: Ao4rvCKFNGqUo5dd79J3Cf9MwG5PyW8nJRi5U6dHyppg
>     Metadata address: B537Vzh62QUbC8zpCLfyRU9DrewYUcjsePC1omDj6qTp
>   Metadata:
>     Update Authority: Ao4rvCKFNGqUo5dd79J3Cf9MwG5PyW8nJRi5U6dHyppg
>     Mint: B537Vzh62QUbC8zpCLfyRU9DrewYUcjsePC1omDj6qTp
>     Name: New CMTAT Token
>     Symbol: NEW
>     URI: https://cmta.ch/token_new.json
>     TermsUri: https://cmta.ch/token_new.pdf
>
> termsHash: 0x9c22ff5f21f0b81b113e63f7db6da94fedef11b2119b4088b89664fb9a3cb658
> jurisdiction: EU-MiCA
> issuer: CMTA Ltd.

 

#### Remove a custom field

```bash
spl-token update-metadata $TOKEN_MINT termsHash --remove
```

**Result**

```bash
spl-token display $TOKEN_MINT
```

> SPL Token Mint
>   Address: B537Vzh62QUbC8zpCLfyRU9DrewYUcjsePC1omDj6qTp
>   Program: TokenzQdBNbLqP5VEhdkAS6EPFLC1PHnBqCXEpPxuEb
>   Supply: 1245
>   Decimals: 0
>   Mint authority: Ao4rvCKFNGqUo5dd79J3Cf9MwG5PyW8nJRi5U6dHyppg
>   Freeze authority: Ao4rvCKFNGqUo5dd79J3Cf9MwG5PyW8nJRi5U6dHyppg
> Extensions
>   Close authority: Ao4rvCKFNGqUo5dd79J3Cf9MwG5PyW8nJRi5U6dHyppg
>   Permanent delegate: Ao4rvCKFNGqUo5dd79J3Cf9MwG5PyW8nJRi5U6dHyppg
>   Metadata Pointer:
>     Authority: Ao4rvCKFNGqUo5dd79J3Cf9MwG5PyW8nJRi5U6dHyppg
>     Metadata address: B537Vzh62QUbC8zpCLfyRU9DrewYUcjsePC1omDj6qTp
>   Metadata:
>     Update Authority: Ao4rvCKFNGqUo5dd79J3Cf9MwG5PyW8nJRi5U6dHyppg
>     Mint: B537Vzh62QUbC8zpCLfyRU9DrewYUcjsePC1omDj6qTp
>     Name: New CMTAT Token
>     Symbol: NEW
>     URI: https://cmta.ch/token_new.json
>     TermsUri: https://cmta.ch/token_new.pdf
>
>    jurisdiction: EU-MiCA
>     issuer: CMTA Ltd.

### Deactivate token

#### Burn all tokens

Using the delegate authorities, burn all remaining tokens as seen above.

#### Deactivate Authorities

Then you can optionally deactivate all authorities, except the `MintCloseAuthority`

```bash
spl-token authorize [OPTIONS] <TOKEN_ADDRESS> <AUTHORITY_TYPE> [--] [AUTHORITY_ADDRESS]
```

**Example**

```bash
spl-token authorize $TOKEN_MINT mint --disable 
spl-token authorize $TOKEN_MINT freeze --disable 
spl-token authorize $TOKEN_MINT pause --disable
```

**Result**

> Updating B537Vzh62QUbC8zpCLfyRU9DrewYUcjsePC1omDj6qTp
>   Current mint: Ao4rvCKFNGqUo5dd79J3Cf9MwG5PyW8nJRi5U6dHyppg
>   New mint: disabled
>
>  Updating B537Vzh62QUbC8zpCLfyRU9DrewYUcjsePC1omDj6qTp
>Current freeze: Ao4rvCKFNGqUo5dd79J3Cf9MwG5PyW8nJRi5U6dHyppg
> New freeze: disabled
>   
>   Updating B537Vzh62QUbC8zpCLfyRU9DrewYUcjsePC1omDj6qTp
>Current pause: Ao4rvCKFNGqUo5dd79J3Cf9MwG5PyW8nJRi5U6dHyppg
> New pause: disabled

 

#### Close the Mint

The Token program allows owners to close token accounts, but it is impossible to close mint accounts. In Token-2022, it is possible to close mints by initializing the `MintCloseAuthority` extension before initializing the mint.

Admin must burn all tokens before closing the token mint

```bash
spl-token close-mint $TOKEN_MINT
```

------

## Main difference with EVM Solidity version

**Forced transfer**

- With Solana, there is no difference between a regular transfer and a forced transfer, while on Ethereum it is two distinct functions. Same applies to a force burn.
- Forced transfer is an optional feature of CMTAT. While the EVM version allows to only include the force burn, with Solana, it is not possible to include the force burn without the force transfer.

Note: It is possible to separate force burn from force transfer if the permanent delegate is itself a smart contract that would only allow for one of the two options. 

**Access control**

- With the Solidity version, you can have a super admin which delegates several tasks to different addresses through different roles, for example the minter role to mint tokens.
- With Solana, you can also delegate roles, but it is not possible to delegate them while keeping a super admin.

Note: Similar to forced transfer above, if the role authorities are themselves smart contracts you can create complex control structures with multisigs, admin accounts etc. In order to have a super admin, you can designate all roles to one smart contract which then has its own rules as to who has authority over which role.

**Mint/burn while pause**

- With Ethereum, you can still burn and mint tokens while pausing regular transfers.
- This is not the case with Solana where the pause will also apply to mint and burn operations in addition to regular transfers.

Note: In order to enable this, you can use a [transfer hook](https://www.solana-program.com/docs/token-2022/extensions#transfer-hook) that is set to a program that just fails every transfer. This way you retain all other administrative features but prevent anyone from transferring tokens.

## Schema

### Features

![solana-cmtat-extension-list-color.drawio](./schema/solana-cmtat-extension-list-color.drawio.png)

#### Base

![solana-cmtat-basic.drawio](./schema/solana-cmtat-basic.drawio.png)

#### Extensions

![solana-cmtat-extension.drawio](./schema/solana-cmtat-extension.drawio.png)

### Access Control

Here is a schema representing the different authorities:

![solana-cmtat.drawio](./schema/solana-cmtat.drawio.png) 

## Reference

- [Token](https://www.solana-program.com/docs/token) 

- [Token-2022](https://www.solana-program.com/docs/token-2022) 

- [Getting Started with Token Extensions](https://solana.com/developers/guides/token-extensions/getting-started) 

- [Extension Guide](https://www.solana-program.com/docs/token-2022/extensions#permanent-delegate) 

- [Confidential Transfer](https://solana.com/docs/tokens/extensions/confidential-transfer) 
