# Joyboy Contracts

## Overview

Decentralized social built with Nostr and powered by Starknet account abstraction.

## Architecture

### Tips
There are two alternatives: WalletConnect, one which expects Nostr users to set up oridinary Starknet wallet like Argent or Braavos, and so called SocialPay which uses custom Account Abstraction contract based on bip340 signature scheme (Nostr native).

#### Wallet Connect
##### Tip to a recipient that never accepted the tip and don't have an account on SN
```mermaid
sequenceDiagram
    actor Alice
    actor Bob
    participant backend as Backend
    participant transfer as Deposit Contract
    participant erc20 as ERC20
    
    Note over Alice,erc20: Alice tip is escrowed by the Deposit Contract
    activate Alice
    Alice->>erc20: approve(Deposit Contract, x)
    Alice->>transfer: transfer(Bob Nostr Address, x)
    transfer->>erc20: transferFrom(Alice, Deposit Contract, x)
    transfer->>Alice: deposit_id
    deactivate Alice


    Note over Alice,erc20: Bob claims the tip with his Nostr identity
    activate Bob
    Bob->>backend: claim(deposit_id)
    Note right of Bob: deposit_id is signed<br/> with Nostr key
    backend->>transfer: claim(deposit_id)
    transfer->>erc20: transferFrom(Transfer Contract, Bob, x)
    deactivate Bob 
```

##### Tip to a recipient that earlier accepted the tip
```mermaid
sequenceDiagram
    actor Alice
    participant transfer as Deposit Contract
    participant erc20 as ERC20
    
    activate Alice
    Alice->>erc20: approve(Deposit Contract, x)
    Alice->>transfer: transfer(Bob Nostr Address, x)
    Note left of transfer: Deposit Contract knows<br/> Bob's SN address
    transfer->>erc20: transferFrom(Alice, Bob, x)
    deactivate Alice
```

#### SocialPay

##### Alice sends tokens to Bob

```mermaid
sequenceDiagram
    actor Alice
    actor Bob
    participant SocialPay gateway
    participant Starknet Node
    participant Alice Account
    participant STRK Token

    Alice->>SocialPay gateway: @joyboy send 10 STRK to @bob
    activate SocialPay gateway
    SocialPay gateway->>Starknet Node: SocialPay transaction
    Starknet Node->>Alice Account: SocialPay handler
    Alice Account->>STRK Token: transfer
    Starknet Node->>SocialPay gateway: SocialPay transaction complete
    SocialPay gateway->>Bob: @bob you just received 10 STRK from @alice
    SocialPay gateway->>Alice: @alice transfer of 10 STRK to @bob is complete
    deactivate SocialPay gateway
```

## Building and Testing

The onchain components of the project are implemented as a suite of Starknet smart contracts.

### Build

To build the project, run:

```bash
scarb build
```

### Test

To test the project, run:

```bash
snforge test
```

</details>

## Resources

- [Starknet](https://starknet.io/)
- [Nostr](https://www.nostr.com/)
- [Nostr Implementation Possibilities](https://github.com/nostr-protocol/nips)
- [JoinStr: Decentralized CoinJoin Implementation Using Nostr](https://www.nobsbitcoin.com/joinstr-decentralized-coinjoin-implementation-using-nostr/)
- [Smart Vaults - Bitcoin multi-custody signature orchestration - website](https://www.smartvaults.io/)
- [Smart Vaults - Bitcoin multi-custody signature orchestration - repo](https://github.com/smartvaults/smartvaults)
