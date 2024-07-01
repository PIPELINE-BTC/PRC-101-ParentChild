
# PRC-101: Parent-Child Concept for NFTs on the PIPE Protocol

**Introduction:**
PRC-101 is a new concept for establishing parent-child relationships for PIPE Art assets, which we will refer to as NFTs for simplicity, on the PIPE protocol. The goal is to modernize the creation of these links by leveraging Bitcoin's Taproot technology and its tree and leaf structure.

**Experimental Nature:**
Please note that this concept is experimental and may evolve over time.

**Concept Overview:**
Each NFT created on the PIPE protocol is an extension of the Taproot tree. Therefore, each NFT has its unique leaf used to generate the NFT's reveal transaction.

We can use this leaf and its address to establish a parent-child tree structure where each NFT becomes a parent by default.

**Adding a Child to a Parent:**
To add a child to a parent, a specific action must be called when deploying the NFT. The data must be signed by the issuer of the parent NFT using the leaf used to create the parent NFT.

**Use Cases:**
This concept opens the door to new use cases such as:
- **Web3 Gaming:** Progressive NFTs that evolve based on the player's progress and retain advancement data throughout their lifecycle.
- **Dynamic Websites:** Creating dynamic websites directly on Bitcoin.
- **Supply Chain Management:** Tracking the lifecycle of products by linking child NFTs (representing components) to a parent NFT (representing the final product), ensuring authenticity and traceability.
- **Digital Identity:** Establishing a digital identity system where a parent NFT represents a primary identity and child NFTs represent verified credentials or attributes.
- **Intellectual Property Management:** Managing intellectual property rights where a parent NFT represents an original work and child NFTs represent derivative works or licenses.

**Specifications:**
The additions are solely intended for the deploy PIPE ART functionality and do not apply to traditional tokens.

**OP_RETURN Code:**
```
OP_RETURN
P
D
[BASE26 ENCODED TICKER]
[ID]
[OUTPUT]
[DECIMALS]
[MAX]
[LIMIT]
```

The OP_RETURN part doesn't change; you deploy the NFT like any other deployment on PIPE, please [refer to the PIPE docs](https://github.com/BennyTheDev/pipe-specs/tree/main?tab=readme-ov-file#deploy-rules).

**Signed Witness Data Code (P2TR Tapscript):**
```
[PUBKEY]
OP_CHECKSIG
OP_0
OP_IF
  P
  C # Children Art type
  [SIG] # PUSH PARENT LEAF SIGNATURE
  [SALT] # UNIQUE HASH
  [PUBLIC KEY] # PUSH PARENT LEAF Public Key
  (I|R)
  [MIMETYPE]
  [INLINE DATA | REFERENCE STRING]
  B
  [BENEFICIARY-OUTPUT]
  (empty | T | TR)
  [TRAIT-CONTENT]
  01
OP_ENDIF
```

This script closely resembles the [PIPE ART extension](https://github.com/BennyTheDev/pipe-specs/tree/main?tab=readme-ov-file#optional-pipe--art-rules) script with a few changes.

**Detailed Explanations:**
- **[SIG]:** The signature of the OP_RETURN by the owner of the parent leaf, including a unique hash [SALT] to prevent hash collisions. The [SALT] must be provided by the parent leaf owner along with the [SIG] and [PUBLIC KEY].
- The indexer should record the leaf address and attach it to each deployed PIPE Art.
- The child numbering is automatically managed by the indexer. The indexer should keep track of the previous child and auto-increment the internal id.
- The indexer must concatenate the hash of the OP_RETURN and the unique hash [SALT] before verifying against the signature.
- The parent NFT must be deployed before deploying the child NFT. The parent must be exclusively a PIPE ART; otherwise, the indexer will reject it.
- The parent leaf creation keys must be preserved to add children later.
- One parent/child per Taproot leaf.

---
