# .bit(dotbit) DID Spec
# Authors

- [Jeff Jing](https://github.com/zgayjjf) ([.bit](https://did.id))

# DID Specification

.bit (dotbit) is a distributed, open-source, and DID naming system on the Nervos blockchain.

.bit's goal is to map human-readable names like `satoshi.bit` to machine-readable identifiers such as blockchain addresses or social profile names.

Unlike other naming systems, .bit allows users to use .bit with different blockchain address, such as Ethereum addresses, Tron addresses or even Dogecoin addresses.


This DID method specification has two purposes:
1. to wrap existing .bit account as DIDs to be interoperable with applications relying on Decentralized Identifiers
2. to define a canonical way to augment .bit names with DID capabilities such as services and verification methods.

## DID Method Name

The name string that shall identify this DID method is: `dotbit`.

A DID that uses this method MUST begin with the following prefix: `did:dotbit`. Per the DID specification, this string MUST be in lowercase. The remainder of the DID, after the prefix, is specified below.

## Method Specific Identifier

.bit DIDs have the following format:

```
  DID     := did:dotbit:<name>
  name    := <.bit-name>
```

The characters of name segment has several charset to choose from. Here is the rules: [Charset](https://docs.did.id/register-das/charsets)

### Examples

```
- did:dotbit:satoshi.bit
- did:dotbit:001.satoshi.bit
- ...
```

## CRUD Operations

.bit accounts can have `custom_key` records. This specification defines some `custom_key` record names that will have an impact on DID resolution of .bit DIDs.

The following named records are defined:

- `profile.w3c.did.service`

  OPTIONAL. A set of [services](https://www.w3.org/TR/did-core/#services) as per W3C DID Core specification. The service `id` property MAY be omitted. In that case the DID resolver will generate a canonical value for the specific service entry.

  > NOTE: the .bit service will be automatically propagated as a service during DID resolution.

- `profile.w3c.did.verificationMethod`

  OPTIONAL. A set of [verification methods](https://www.w3.org/TR/did-core/#verification-methods) as per W3C DID Core specification. Verification method `id` property values MUST be relative DID URIs, e.g., `#my-key-id-1234`.

  >NOTE: the owner of the .bit account will be automatically propagated as a verification method during DID resolution.

- `profile.w3c.did.verificationRelationship`

  OPTIONAL. A map of [verification relationship](https://www.w3.org/TR/did-core/#verification-relationships) as per W3C DID Core specification to a set of verification relationship identifiers (`id` property).

  >NOTE: the verification method that relates to the owner of the .bit account can be used for all verification relationships.

### CREATE

See [.bit](https://docs.did.id/register-das/open-registration-rules) on how to register .bit accounts.

### READ

DID resolution will perform .bit resolution for a given .bit account.
Additionally, the DID resolver will then retrieve the DID specific records for the .bit account and add default values for service endpoints and verification methods.

The default verification method will always include the owner of the .bit account as follows:

```json
{
  "id": "<DID-dotbit>#<sha256-of-blockchainAccountId>",
  "type": "EcdsaSecp256k1RecoveryMethod2020",
  "controller": "<DID-dotbit>",
  "blockchainAccountId": "<CAIP-10>"
}
```

The `id` of the default verification method is the concatenation of the .bit DID followed by the `#` and the hex representation of `sha256(blockchainAccountId)`. todo
Additional verification methods that MAY be added MUST not use that verification method `id` and will be ignored in the DID Document.

The default service will always include the .bit service as follows:

```json
{
  "id":"<DID-dotbit>#Web3PublicProfile-<sha256-of-blockchainAccountId>",
  "type": "Web3PublicProfile", 
  "serviceEndpoint": { 
    "profileService": "dotbit",
    "dotbitAccount": "<dotbit-account>",
    "network": "mainnet" 
  }
}
```

The `id` of the default service is the concatenation of the .bit DID followed by the `#Web3PublicProfile-` and the hex representation of `sha256(blockchainAccountId)`. Additional services that MAY be added MUST not use that service `id` and will be ignored in the DID Document.

If the DID specific records are malformed, the entire record will be ignored in the DID resolution process.

See .bit on how to resolve .bit accounts and how to resolve records for .bit accounts.

#### Example (no records)

For `did:dotbit:satoshi.bit` (with no records added), the DID Document would look as follows:

```json
{
  "@context": [
    "https://www.w3.org/ns/did/v1",
  ],
  "id": "did:dotbit:satoshi.bit",
  "canonicalId": "did:dotbit:satoshi.bit",
  "verificationMethod": [{
    "id": "did:dotbit:satoshi.bit#5d3e82eaba0bf8991c38bd092fa5f5523b5b3bf13e06b4b29c0022a094a528d7",
    "type": "EcdsaSecp256k1RecoveryMethod2020",
    "controller": "did:dotbit:satoshi.bit",
    "blockchainAccountId": "eip155:1:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb"
  }],
  "service": [{
    "id":"did:dotbit:satoshi.bit#Web3PublicProfile-5d3e82eaba0bf8991c38bd092fa5f5523b5b3bf13e06b4b29c0022a094a528d7",
    "type": "Web3PublicProfile", 
    "serviceEndpoint": { 
      "profileService": "dotbit",
      "dotbitName": "satoshi.bit",
      "network": "mainnet" 
    }
  }],
  "authentication": [
    "did:dotbit:satoshi.bit#5d3e82eaba0bf8991c38bd092fa5f5523b5b3bf13e06b4b29c0022a094a528d7"
  ],
  "assertionMethod": [
    "did:dotbit:satoshi.bit#5d3e82eaba0bf8991c38bd092fa5f5523b5b3bf13e06b4b29c0022a094a528d7"
  ],
  "capabilityInvocation": [
    "did:dotbit:satoshi.bit#5d3e82eaba0bf8991c38bd092fa5f5523b5b3bf13e06b4b29c0022a094a528d7"
  ],
  "capabilityDelegation": [
    "did:dotbit:satoshi.bit#5d3e82eaba0bf8991c38bd092fa5f5523b5b3bf13e06b4b29c0022a094a528d7"
  ]
}
```

#### Example (with keyAgreement)

For `did:dotbit:satoshi.bit` with DID specific records added, the DID Document would look as follows:

```json
{
  "@context": [
    "https://www.w3.org/ns/did/v1",
  ],
  "id": "did:dotbit:satoshi.bit",
  "canonicalId": "did:dotbit:satoshi.bit",
  "verificationMethod": [{
      "id": "did:dotbit:satoshi.bit#5d3e82eaba0bf8991c38bd092fa5f5523b5b3bf13e06b4b29c0022a094a528d7",
      "type": "EcdsaSecp256k1RecoveryMethod2020",
      "controller": "did:dotbit:satoshi.bit",
      "blockchainAccountId": "eip155:1:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb"
    },
    {
      "id": "did:dotbit:satoshi.bit#zC9ByQ8aJs8vrNXyDhPHHNNMSHPcaSgNpjjsBYpMMjsTdS",
      "type": "X25519KeyAgreementKey2019", 
      "controller": "did:dotbit:satoshi.bit",
      "publicKeyMultibase": "z9hFgmPVfmBZwRvFEyniQDBkz9LmV7gDEqytWyGZLmDXE" 
    }
  ],
  "service": [{
    "id":"did:dotbit:satoshi.bit#Web3PublicProfile-5d3e82eaba0bf8991c38bd092fa5f5523b5b3bf13e06b4b29c0022a094a528d7",
    "type": "Web3PublicProfile", 
    "serviceEndpoint": { 
      "profileService": "dotbit",
      "dotbitName": "satoshi.bit",
      "network": "mainnet" 
    }
  }],
  "authentication": [
    "did:dotbit:satoshi.bit#5d3e82eaba0bf8991c38bd092fa5f5523b5b3bf13e06b4b29c0022a094a528d7"
  ],
  "assertionMethod": [
    "did:dotbit:satoshi.bit#5d3e82eaba0bf8991c38bd092fa5f5523b5b3bf13e06b4b29c0022a094a528d7"
  ],
  "capabilityInvocation": [
    "did:dotbit:satoshi.bit#5d3e82eaba0bf8991c38bd092fa5f5523b5b3bf13e06b4b29c0022a094a528d7"
  ],
  "capabilityDelegation": [
    "did:dotbit:satoshi.bit#5d3e82eaba0bf8991c38bd092fa5f5523b5b3bf13e06b4b29c0022a094a528d7"
  ],
  "keyAgreement": [
    "did:dotbit:satoshi.bit#zC9ByQ8aJs8vrNXyDhPHHNNMSHPcaSgNpjjsBYpMMjsTdS"  
  ]
}
```

The following records have to be set:

- `org.w3c.did.verificationRelationship`
- `org.w3c.did:verificationMethod`

### UPDATE

See [.bit docs](https://docs.did.id/technical-details/permissions) on how to add records.

When any data (e.g. W3C Verifiable Credentials) is associated with .bit DIDs, sharing that data would also impose sharing the onchain data graph (e.g. transaction history, NFTs etc.) of the blockchain address that owns the .bit account.

Using personal identifiable information as DID Method specific identifiers (e.g. alice.bit) discloses personal information every time the DID is shared with a counter party. This specification DOES NOT endorse the use of .bit accounts that correlate directly with real world human beings.

> NOTE: The .bit community is already using .bit accounts for individuals (e.g. alice.bit).

## Security Considerations

.bit accounts are non-fungible and transferrable. When the owner of the .bit account changes, the authorative keys will also change. This needs to be considered when used in conjunction with verifiable data where the DID is embedded, e.g., W3C Verifiable Credentials.
