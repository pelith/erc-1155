# EIP-1155 Adapter

See also **[another, more advanced ERC-1155 ↔ ERC-20 converter dApp](https://erc1155.portonvictor.org)** ([source](https://github.com/vporton/wrap-tokens)).

**[Read this contract in master branch](blob/master/contracts/ERC1155Adapter.sol)**

This is an EIP-1155 wrapper contract of its reference implementation by Enjin ([https://github.com/enjin/erc-1155](https://github.com/enjin/erc-1155)). It is designed to be backward-compatible with existing interfaces, making it easier to adopt to token ecosystems.

## Concepts

Some significant improvements over the original work are: 

* **ERC-20 compatibility**: Calling `createAdapter` maps individual token to a separate ERC-20 token contract. The adapter contract makes it possible to interact with existing wallets, to be tradable on dapps like Uniswap, as a regular ERC-20 token.
* **Proxy included**: It is expensive to deploy an adapter for each individual token. To save gas, the typical solution is to keep a single adapter and deploy multiple proxies, forwarding any transactions to that adapter. Thanks to [EIP-1167]([http://eips.ethereum.org/EIPS/eip-1167](http://eips.ethereum.org/EIPS/eip-1167)), we have a simple and canonical way to include a proxy contract. Proxy contracts are deployed with  `createClone` function.
* **Verbose events**: To be distinguish from non-wrappers, it emits both `TransferSingle` and `Transfer` events when transferring with adapters. 
* **Additional checking bypass**: In the reference implementation, when you attempt to transfer tokens to a contract address, it calls the function `onERC1155Received` at destination and requires its return value to be a magic number (=`sha3(some_func_sig)`) for safety. This is indeed a security mechanism, but it keep arbitrary contract addresses, like Gnosis Safes or mulit-sig wallets, from receiving ERC-1155 tokens. To better interoperate, the checking is not performed in this adapter.

The ideas are more clear when reading along with code.
