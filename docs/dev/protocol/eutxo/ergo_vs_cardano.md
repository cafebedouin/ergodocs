# Comparing Ergo and Cardano's eUTXO Models

> This document is a work in progress and may contain inaccuracies.

Ergo and Cardano are two prominent blockchain platforms that have successfully implemented the extended UTXO (eUTXO) model. Despite their differences, both platforms leverage the eUTXO model to provide native asset support and distributed states for decentralized applications (dApps), among other features. This article aims to elucidate the key differences between Ergo and Cardano's eUTXO models.


## Programming Languages and Data Storage

Ergo employs a language known as [ErgoScript](ergoscript.md), which draws inspiration from [Scala](scala.md). Conversely, Cardano uses a language named Plutus, which is inspired by Haskell. Ergo stores data in [registers](registers.md), while Cardano uses "datum" for data storage. Despite these differences, both platforms allocate a space within the UTXO to maintain the state of decentralised applications (dApps). Cardano employs a two-layer architecture, utilizing Plutus for smart contracts and Marlowe for financial contracts.

ErgoScript, the scripting language of Ergo, facilitates the development of complex smart contracts and advanced features. Cardano's Plutus language is also robust and powerful, but in some cases, certain complex functionalities may necessitate the incorporation of supplementary components or alternative approaches to implementation.

Here's a comparison of ErgoScript and Plutus (Cardano's smart contract language)

 Feature | ErgoScript (Ergo) | Plutus (Cardano) |
-----------------------------|---------------------------|---------------------------|
 Language design | Based on Scorex framework, influenced by Scala | Based on Haskell |
 Strongly typed | Yes | Yes |
 First-class functions | Yes | Yes |
 Higher-order functions | Yes | Yes |
 Rich data structures | Registers, boxes | Datum, UTXO |
 Zero-knowledge proofs | Supported | Planned for future development |
 Complex authentication schemes | Supported | Supported |
 Transaction Trees | Supported | Not Supported |

Both Ergo and Cardano's models support non-fungible assets and complex types of representation on the blockchain.

## Token Minting Policies on Ergo and Cardano

Minting, the process of creating new tokens on a blockchain network, is a critical feature that enables the creation of native assets, such as NFTs. Both Ergo and Cardano offer native asset support through their respective eUTXO models, but their approach to minting and issuing tokens varies.


### Cardano's Minting Policy

Cardano's minting policy uniquely identifies each native asset with a permanent policy ID, which originates from the policy script. The policy script further defines other attributes, such as the asset's name and amount/value. Since asset names are not unique, Cardano NFTs must be identified by the policy ID, which can be publicly available to distinguish fraudulent/duplicate NFTs from the original tokens. Cardano's minting policy provides a straightforward approach to token issuance and is suitable for assets with fewer attributes. It ensures that NFTs are unique and easily identifiable through the policy ID.


### Ergo's Minting Policy

Ergo's minting policy is versatile and supports a broad range of token attributes, making it suitable for complex assets such as NFTs. Developers can create custom policies to define the conditions and rules for creating and managing tokens, including NFTs with various attributes and properties. Ergo's minting policy also supports off-chain data input, which can trigger conditions for transactions to be executed or prevent them from being included in blocks.

Ergo's minting policy is defined by the [Ergo Improvement Proposal (EIP) 0024](eip24.md), which offers two design versions for artwork issuance: V1 and V2. Both designs involve two important boxes in the issuance process: the issuance box and the issuer box. The main difference between V1 and V2 lies in the issuer box.

In V1, the issuer box includes an Int representing 1000 times the royalty percentage of the artwork and the proposition bytes representing the contract to which the royalty percentage will be sent. V1 primarily focuses on handling royalties for the artist or proxy contract.

In V2, the issuer box contains more attributes, such as the artwork standard version, royalty recipients and their respective percentages, artwork traits (Properties, Levels, and Stats), the token ID of the collection, and additional information, such as explicit content. V2 offers more flexibility and features, such as handling multiple royalty recipients and detailed artwork traits. Ergo's minting policy also supports off-chain data input, which can trigger conditions for transactions to be executed or prevent them from being included in blocks.

In terms of tangible examples, Ergo's minting policy allows for more complex NFTs with a wider range of attributes and detailed artwork traits. For instance, NFTs with dynamic or changing attributes could be created using Ergo's minting policy, which is not currently possible on Cardano. 

## Global State Management

The management of global state in Ergo and Cardano is influenced by their respective scripting languages and transaction validation processes. Ergo utilizes ErgoScript, a call-by-value, higher-order functional language without recursion. ErgoScript defines a guarding proposition for a coin as a logical formula that combines predicates over a context and cryptographic statements provable via Σ-protocols with AND, OR, k-out-of-n connectives. 

Although ErgoScript is not inherently Turing complete, Turing completeness can be achieved in Ergo using transaction trees, as outlined in this [peer reviewed paper](https://arxiv.org/pdf/1806.10116v1.pdf). A transaction tree is a structure where transactions are organized into a tree, with each transaction referencing its parent transaction(s). This allows for complex, multi-step computations to be executed across multiple transactions, effectively making the Ergo platform Turing complete.

This organization of transactions enables developers to create more complex and flexible smart contracts on the Ergo platform, similar to those written in Cardano's Plutus language. However, this method requires more manual construction of transaction structures and may not be as intuitive as using a Turing-complete language like Plutus.

Ergo's transaction validation uses the ErgoLikeStateContext trait and [ErgoLikeStateContext](https://github.com/ScorexFoundation/sigmastate-interpreter/blob/develop/sdk/shared/src/main/scala/org/ergoplatform/sdk/wallet/protocol/context/ErgoLikeStateContext.scala) case class to represent the blockchain context. The sigmaLastHeaders method provides information about the previous blocks, while the previousStateDigest method provides the UTXO set digest from the last header. The sigmaPreHeader method provides information about the current block being validated.

In contrast, Cardano uses Plutus, a Turing-complete, higher-order functional programming language subset of Haskell, designed specifically for smart contracts. While ErgoScript focuses on the transactional model and guarding propositions for coins, Plutus provides a more general-purpose language for writing smart contracts.

For more information, refer to these foundational papers:

- [Improving authenticated dynamic dictionaries, with applications to cryptocurrencies](https://eprint.iacr.org/2016/994.pdf)
- [Self-reproducing Coins as Universal Turing Machine](https://arxiv.org/pdf/1806.10116)
- [Multi-stage Contracts in the UTXO Model](https://ergoplatform.org/docs/paper_26.pdf)
- [EDRAX: A Cryptocurrency with Stateless Transaction Validation](https://eprint.iacr.org/2018/968.pdf)


## Privacy Features
Both Ergo and Cardano prioritize security and privacy in their designs. Ergo's cryptographic design incorporates [Sigma protocols](sigma.md), providing extensive access to discrete log-based zero-knowledge proofs, offering potential advantages in privacy and security. Zero-knowledge proofs are cryptographic techniques that enable a prover to demonstrate the truth of a statement to a verifier without revealing any additional information. In the context of blockchain technology, zero-knowledge proofs can enhance privacy and security by allowing transactions and smart contracts to be executed without disclosing sensitive data.

Discrete log-based zero-knowledge proofs refer to a class of zero-knowledge proofs that rely on the hardness of the discrete logarithm problem, a foundational concept in modern cryptography. ErgoScript, the scripting language used in Ergo, provides access to Σ-protocols, a subclass of cryptographic proof systems known as non-interactive Σ-protocols. These Σ-protocols include the Schnorr signature scheme and Diffie-Hellman tuple, which can be used to prove knowledge of discrete logarithms. This efficient and flexible implementation of zero-knowledge proofs can improve privacy-enhancing features and applications on the Ergo platform.

Ergo's Sigma Protocols enable the implementation of sophisticated tasks that would otherwise be impossible, risky, or expensive, allowing for self-sovereign application-level privacy. Trustless scripts can access mixers or other functionality without any third parties required. Using Ergo for such applications, users can achieve enhanced privacy and security compared to other platforms.

## Data Inputs

Spending UTXOs is at the core of the extended UTXO smart contract model, and all execution happens when a UTXO is spent. However, having to spend every single UTXO which you wish to read data from has several drawbacks:

- The smart contract of the UTXO with the data must execute, thereby increasing computation complexity/cost.
- The UTXO must be spent, meaning only one tx can use the UTXO data per block/slot.
- Tx fees increase due to needless excess execution & recreation of the output data UTXO.
- Every UTXO which wishes to allow read access through spending must encode the logic directly within their smart contract.
- It is liable to spam attacks by bad actors who wish to wreak havoc on a protocol.
- Increased off-chain complexity in tx creation & finding the latest UTXO.

To address these issues, the core Ergo developers, Alexander Slesarenko, Alex Chepurnoy, and Dmitry Meshkov, introduced the concept of "*read-only inputs*". These allow any transaction to reference any other box(UTXO) currently in the UTXO set and read the data without any problems listed in the previous section.

### Ergo's Data Inputs

Ergo's eUTXO model supports data inputs, which allow transactions to read data from other boxes (UTXOs) without consuming them. This feature enables Ergo to access off-chain data and use oracles, facilitating various applications, such as decentralized finance (DeFi), prediction markets, and data-driven smart contracts. Data inputs can also help minimize transaction fees, as only the necessary boxes are consumed in the transaction process.

### Cardano's Reference Inputs

Cardano introduced reference inputs in the Vasil Hardfork, enabling functionality similar to Ergo's data inputs. In Cardano's eUTXO model, reference inputs allow transactions to access data from other datums without consuming them. This facilitates the integration of off-chain data and oracles into Cardano's smart contracts, expanding the platform's possible applications and use cases.
Collaboration

Ergo and Cardano are pioneers in implementing the extended UTXO (eUTXO) model and have collaborated to advance research and development in this area through the eUTXO Alliance. Cardano’s implementation of data inputs is just one example of how we can work together.

One notable development between the Ergo and Cardano communities is the Rosen Bridge, currently in beta testing. This bridge enables the transfer of wrapped ADA tokens from Cardano to Ergo and vice versa, promoting interoperability and collaboration between the platforms and their communities. Cardano users can access the DeFi ecosystem on Ergo with this bridge using wrapped ADA or native ADA tokens.

Moreover, ErgoMixer, as the only token mixer in the space, enables users to mix wrapped ADA and other native tokens, such as wrapped HOSKY. After mixing the tokens in ErgoMixer, users can bridge them back to Cardano. These native tokens can be used in SigmaFi, SigmaO, or any budding dApps available on sigmaverse.io.

Through these collaborations and developments, Ergo and Cardano can enhance their respective platforms, promote interoperability, and advance the adoption of the eUTXO model in the blockchain community.



## Conclusion

Ergo and Cardano are both innovative blockchain platforms that have implemented the extended UTXO model, offering native asset support, distributed states for decentralized applications, and a range of other features. While ErgoScript and Plutus, their respective scripting languages, have different foundations and design principles, they both enable the creation of sophisticated smart contracts and blockchain applications.

Ergo's approach to minting policies allows for more complex NFTs and token attributes, while Cardano's policy provides a simpler method for token issuance. Both platforms have implemented solutions to access off-chain data and oracles through data inputs, broadening their potential applications and use cases. Furthermore, their collaboration through the eUTXO Alliance and developments like Spectrum, Rosen and Reference Inputs demonstrate the potential for cross-platform cooperation and growth in the blockchain ecosystem.

Both Ergo and Cardano offer unique strengths and capabilities, making them appealing choices for developers, users, and investors alike. As these platforms continue to evolve and collaborate, they will undoubtedly contribute significantly to the advancement of blockchain technology and the adoption of the eUTXO model.

## Frequently Asked Questions

### What are the similarities and differences between Ergo and Cardano's native assets?

Ergo and Cardano both support native assets within their respective blockchain ecosystems. They both utilize the eUTXO model, but they employ different scripting languages (Ergo uses ErgoScript, while Cardano uses Plutus) and have distinct approaches to data storage, global state management, and minting policies. 

### Can Ergo's multiasset ledger interact with Cardano's ledger?

While Ergo and Cardano share similarities due to their use of the eUTXO model, their ledgers are not directly interoperable. This is primarily due to differences in their scripting languages, minting policies, and data handling mechanisms. 

### Does Ergo have a feature similar to Cardano's CIP25?

Ergo has a robust set of features and improvements, but as of now, there is no direct equivalent to Cardano's CIP25 in the Ergo ecosystem.

### Does Ergo offer a feature like Cardano's dbsync?

Yes, Ergo provides a similar feature through its [Explorer](explorer.md) tool, which allows users to synchronize and interact with the Ergo blockchain.

### How do Mithril and NiPoPoWs compare in the context of Ergo?

Both Mithril and NiPoPoWs are important concepts in the blockchain space. For a detailed comparison and understanding, you can refer to this [video](youtube.com/watch?v=tXHids3WAb4) from *PG: Blockchain & Deep Learning* on YouTube.
