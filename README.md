# .bit (dotbit) DID Spec
# Authors

- [Jeff Jing](https://github.com/zgayjjf) ([.bit](https://did.id))

# DID Specification

.bit (dotbit) is a distributed, open-source, and DID naming system on the Nervos blockchain.

The goal of .bit is to map human-readable names like `satoshi.bit` to machine-readable identifiers, such as blockchain addresses or social profile names.

Unlike other naming systems, .bit allows users to use .bit with different blockchain addresses, such as Ethereum addresses, Tron addresses, or even Dogecoin addresses.

This DID method specification has two purposes:
1. To wrap existing .bit accounts as DIDs to be interoperable with applications relying on Decentralized Identifiers.
2. To define a canonical way to augment .bit names with DID capabilities such as services and verification methods.

## DID Method Name

The name string that shall identify this DID method is: `dotbit`.

A DID that uses this method MUST begin with the following prefix: `did:dotbit`. Per the DID specification, this string MUST be in lowercase. The remainder of the DID, after the prefix, is specified below.

## Method Specific Identifier

.bit DIDs have the following format:

```
  DID     := did:dotbit:<name>
  name    := <.bit-name>
```

The characters of the name segment have several character sets to choose from. Here are the rules: [Charset](https://docs.did.id/register-das/charsets)

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

  OPTIONAL. A set of [services](https://www.w3.org/TR/did-core/#services) as per W3C DID Core specification. The service `id` property MAY be omitted. In that case, the DID resolver will generate a canonical value for the specific service entry.

  > NOTE: the .bit service will be automatically propagated as a service during DID resolution.

- `profile.w3c.did.verificationMethod`

  OPTIONAL. A set of [verification methods](https://www.w3.org/TR/did-core/#verification-methods) as per W3C DID Core specification. Verification method `id` property values MUST be relative DID URIs, e.g., `#my-key-id-1234`.

  >NOTE: the owner of the .bit account will be automatically propagated as a verification method during DID resolution.

- `profile.w3c.did.verificationRelationship`

  OPTIONAL. A map of [verification relationships](https://www.w3.org/TR/did-core/#verification-relationships) as per W3C DID Core specification to a set of verification relationship identifiers (`id` property).

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
The `id` of the default verification method is formed by concatenating the .bit DID with the `#` symbol and the hexadecimal representation of `sha256(blockchainAccountId)`. Additional verification methods that MAY be added MUST NOT use that specific verification method `id` and will be ignored in the DID Document.

The default service will always include the .bit service as follows:

```json
{
  "id":"<DID-dotbit>#Web3PublicProfile-<sha256-of-blockchainAccountId>",
  "type": "Web3PublicProfile", 
  "serviceEndpoint": { 
    "profileService": "dotbit",
    "dotbitAccount": "<dotbit-account>"
  }
}
```

The `id` of the default service is formed by concatenating the .bit DID with the `#Web3PublicProfile-` and the hexadecimal representation of `sha256(blockchainAccountId)`. Additional services that MAY be added MUST NOT use that specific service `id` and will be ignored in the DID Document.

If the DID specific records are malformed, the entire record will be ignored during the DID resolution process.

Refer to .bit documentation on how to resolve .bit accounts and how to resolve records for .bit accounts.

#### Example (no records)

For `did:dotbit:satoshi.bit` (with no records added), the DID Document would appear as follows:

```json
{
  "@context": [
    "https://www.w3.org/ns/did/v1",
  ],
  "id": "did:dotbit:satoshi.bit",
  "verificationMethod": [{
    "id": "did:dotbit:satoshi.bit#5d3e82eaba0bf8991c38bd092fa5f5523b5b3bf13e06b4b29c0022a094a528d8",
    "type": "EcdsaSecp256k1RecoveryMethod2020",
    "controller": "did:dotbit:satoshi.bit"
  }],
  "service": [{
    "id":"did:dotbit:satoshi.bit#Web3PublicProfile-5d3e82eaba0bf8991c38bd092fa5f5523b5b3bf13e06b4b29c0022a094a528d8",
    "type": "Web3PublicProfile", 
    "serviceEndpoint": { 
      "profileService": "dotbit",
      "dotbitName": "satoshi.bit"
    }
  }],
  "authentication": [
    "did:dotbit:satoshi.bit#5d3e82eaba0bf8991c38bd092fa5f5523b5b3bf13e06b4b29c0022a094a528d8"
  ],
  "assertionMethod": [
    "did:dotbit:satoshi.bit#5d3e82eaba0bf8991c38bd092fa5f5523b5b3bf13e06b4b29c0022a094a528d8"
  ],
  "capabilityInvocation": [
    "did:dotbit:satoshi.bit#5d3e82eaba0bf8991c38bd092fa5f5523b5b3bf13e06b4b29c0022a094a528d8"
  ],
  "capabilityDelegation": [
    "did:dotbit:satoshi.bit#5d3e82eaba0bf8991c38bd092fa5f5523b5b3bf13e06b4b29c0022a094a528d8"
  ]
}
```

#### Example

For `did:dotbit:satoshi.bit` with DID specific records added, the DID Document would look as follows:

```json
{
  "@context": [
    "https://www.w3.org/ns/did/v1",
  ],
  "id": "did:dotbit:satoshi.bit",
  "canonicalId": "did:dotbit:satoshi.bit",
  "verificationMethod": [{
      "id": "did:dotbit:satoshi.bit#5d3e82eaba0bf8991c38bd092fa5f5523b5b3bf13e06b4b29c0022a094a528d8",
      "type": "EcdsaSecp256k1RecoveryMethod2020",
      "controller": "did:dotbit:satoshi.bit"
    },
    {
      "id": "did:dotbit:satoshi.bit#zC9ByQ8aJs8vrNXyDhPHHNNMSHPcaSgNpjjsBYpMMjsTd8",
      "type": "X25519KeyAgreementKey2019", 
      "controller": "did:dotbit:satoshi.bit",
      "publicKeyMultibase": "z9hFgmPVfmBZwRvFEyniQDBkz9LmV7gDEqytWyGZLmDX8" 
    }
  ],
  "service": [{
    "id":"did:dotbit:satoshi.bit#Web3PublicProfile-5d3e82eaba0bf8991c38bd092fa5f5523b5b3bf13e06b4b29c0022a094a528d8",
    "type": "Web3PublicProfile", 
    "serviceEndpoint": { 
      "profileService": "dotbit",
      "dotbitName": "satoshi.bit"
    }
  }],
  "authentication": [
    "did:dotbit:satoshi.bit#5d3e82eaba0bf8991c38bd092fa5f5523b5b3bf13e06b4b29c0022a094a528d8"
  ],
  "assertionMethod": [
    "did:dotbit:satoshi.bit#5d3e82eaba0bf8991c38bd092fa5f5523b5b3bf13e06b4b29c0022a094a528d8"
  ],
  "capabilityInvocation": [
    "did:dotbit:satoshi.bit#5d3e82eaba0bf8991c38bd092fa5f5523b5b3bf13e06b4b29c0022a094a528d8"
  ],
  "capabilityDelegation": [
    "did:dotbit:satoshi.bit#5d3e82eaba0bf8991c38bd092fa5f5523b5b3bf13e06b4b29c0022a094a528d8"
  ],
  "keyAgreement": [
    "did:dotbit:satoshi.bit#zC9ByQ8aJs8vrNXyDhPHHNNMSHPcaSgNpjjsBYpMMjsTd8"  
  ]
}
```

The following records must be set:

- `profile.w3c.did.verificationRelationship`
- `profile.w3c.did:verificationMethod`

### UPDATE

Refer to [.bit docs](https://docs.did.id/technical-details/data-container) for information on adding records.

When any data (e.g., W3C Verifiable Credentials) is associated with .bit DIDs, sharing that data also implies sharing the on-chain data graph (e.g., transaction history, NFTs, etc.) of the blockchain address owning the .bit account.

Using personally identifiable information as DID Method-specific identifiers (e.g., alice.bit) discloses personal information each time the DID is shared with a counterparty. This specification DOES NOT endorse the use of .bit accounts that directly correlate with real-world human beings.

> NOTE: The .bit community is already using .bit accounts for individuals (e.g., alice.bit).

## Security Considerations

.bit accounts are non-fungible and transferable. When the owner of the .bit account changes, the authoritative keys will also change. This must be considered when used in conjunction with verifiable data where the DID is embedded, e.g., W3C Verifiable Credentials.