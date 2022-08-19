# Ink! SDK
Ink! SDK makes it easy for ink developers in the Polkadot Ecosystem to use Dante Network to interact with other chains, such as Near, Ethereum, Avalanche, Flow.

Click `Use this template` to start your multi-ecosystem dApp.
* Create your project in [./project](./project/). You can find more details there.
* The examples are [here](./examples/)

Or you can use this SDK as a library by adding  
```rust
ink_sdk = { path = "<local path of ink_sdk>/contracts/", default-features = false, features = ["ink-as-dependency"] }
```
into `Cargo.toml` of your project.

## Library
The library witch is contained in `contracts` is used to develop Ink! application contracts. The library provides two functional modules, practical traits and cross-chain interacting module.

### Practical Traits
#### [MultiDestContracts](./contracts/lib.rs#L37)
This trait can be used when a contract needs to communicate with more than one other chain.

#### [CrossChainSQoS](./contracts/lib.rs#L49)
This trait can be used when a contract has custom SQoS demands.

### [Cross-chain Interacting Module](./contracts/cross_chain_helper.rs)
The cross-chain interaction module is contained in the file `cross_chain_helper.rs`, which mainly provides functions to make cross-contract calls to cross-chain contract, as well as to make cross-chain interaction with other chains.

#### [CrossChainBase](./contracts/cross_chain_helper.rs#L39)
`CrossChainBase` is a trait, which must be implemented by the contract struct to use the Ink! SDK.  
You can use the default implemantation of `CrossChainBase::get_cross_chain_contract_address` like this:
```rust
impl cross_chain_helper::CrossChainBase for Flip {
}
```

Or you can rewrite the method if you want to use another cross-chain contract address.

#### [cross_chain_send_message](./contracts/cross_chain_helper.rs#L87)
The function `cross_chain_send_message` sends a cross-chain message, and returns the message id recorded in the cross-chain contract.

Example is shown below, or you can refer it in the example [greeting](./examples/greeting/lib.rs#L209).
```rust
#[ink(message)]
pub fn send_greeting(&mut self, chain_name: String, greeting: Vec<String>) -> Result<(), Error> {
    ...
    let message = IRequestMessage::new(chain_name, sqos, content);

    cross_chain_helper::cross_chain_send_message(self, message);

    Ok(())
}
```

#### [cross_chain_call](./contracts/cross_chain_helper.rs#L96)
The function `cross_chain_call` sends a cross-chain message, and returns the message id recorded in the cross-chain contract. Later a callback in the application contract will be called.

Example is shown below, or you can refer it in the example [osComputing](./examples/osComputing/lib.rs#L138).
```rust
#[ink(message)]
pub fn send_computing_task(&mut self, chain_name: String, nums: Vec<u32>) -> Result<(), Error> {
    ...
    let message = IRequestMessage::new(chain_name, sqos, content);

    cross_chain_helper::cross_chain_call(self, message, 2_u32.to_be_bytes());

    Ok(())
}
```

#### [cross_chain_respond](./contracts/cross_chain_helper.rs#L104)
The function `cross_chain_respond` responds a cross-chain request, and returns the message id recorded inthe cross-chain contract.

Example is shown below, or you can refer it in the example [osComputing](./examples/osComputing/lib.rs#L158).
```rust
#[ink(message)]
pub fn receive_computing_task(&mut self, payload: MessagePayload) -> String {
    ...
    let message = IResponseMessage::new(sqos, content);
    cross_chain_helper::cross_chain_respond(self, message);

    String::try_from("Ok").unwrap()
}
```

## [Examples](./examples/)
There are two examples in the repo, one is `greeting`, the other is `osComputing`.

### [greeting](./examples/greeting/)
The example shows how to send greeting messages to, and receive greeting messages from other chains with the Ink! SDK.

### [osComputing](./examples/osComputing/)
The example shows a scenario in which sb. want to send a outsource computing task to another chain, and receive the result.

## Usage
### Use Examples
You can use either of the examples as a template, it is the recommended way.

- Copy the example.
- Change the `package name` and `lib name` in Cargo.toml.
- Write your code.

### New Project
You can use the library in a totally new ink! project.
- Create a new ink! project, you can refer it here https://docs.substrate.io/tutorials/v3/ink-workshop/pt1/.
- Change `dependencies`
    ```rust
    ink_primitives = { version = "3.2.0", default-features = false }
    ink_metadata = { version = "3.2.0", default-features = false, features = ["derive"], optional = true }
    ink_env = { version = "3.2.0", default-features = false }
    ink_storage = { version = "3.2.0", default-features = false }
    ink_lang = { version = "3.2.0", default-features = false }
    ink_prelude = { version = "3.2.0", default-features = false }

    scale = { package = "parity-scale-codec", version = "3.1.5", default-features = false, features = ["derive"] }
    scale-info = { version = "2.1.2", default-features = false, features = ["derive"], optional = true }

    payload = { path = "<local path of message_ink>/message-ink/payload/", default-features = false, features = ["ink-as-dependency"] }
    ink_sdk = { path = "<local path of ink_sdk>/contracts/", default-features = false, features = ["ink-as-dependency"] }
    ```
- Use modules in lib.rs, `use ink_sdk::{cross_chain_helper}`, and other modules if you need.
- Implement the trait `cross_chain_helper::CrossChainBase`, the method `get_cross_chain_contract_address` has default implementation.
- Write your code.
