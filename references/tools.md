# Tool reference — all 55 VEYA MCP tools (52 without Metered Call Guard)

Generated from the tool registrations in `veya-mcp/src/tools/*.ts`. Names, argument names, types and required flags match the server code.

**Reply shape.** Every tool returns one MCP text item containing JSON. Errors set `isError: true` with `{ "error": "…" }` (or a `refusal` field for `veya_prove` / `veya_seal_decision`). Product API and registry tools wrap the response as `{ "httpStatus": 200, "body": { … } }`. API timeout is 20 s (`veya_api_health`: 8 s).

**apiKey.** The MCP schema marks `apiKey` and `payerPrivateKey` optional so clients can pass them per call; the server still refuses scoped calls without them (`apiKey required`). "yes" in the tables below means the server needs it. Never put keys in `mcp.json`.

**Hex.** A `0x` prefix is optional. UUIDs are 16 bytes (32 hex). Digests are 32 bytes (64 hex).

## Index

| # | Tool | Group | Access |
|---|---|---|---|
| 1 | [`veya_describe`](#veya_describe) | [Service and chain](service-and-chain.md) | Public |
| 2 | [`veya_ping_chain`](#veya_ping_chain) | [Service and chain](service-and-chain.md) | Public |
| 3 | [`veya_api_health`](#veya_api_health) | [Service and chain](service-and-chain.md) | Public |
| 4 | [`veya_writes_status`](#veya_writes_status) | [Service and chain](service-and-chain.md) | Public |
| 5 | [`veya_prove`](#veya_prove) | [Proofs](proofs.md) | Public · anchor: API key + wallet |
| 6 | [`veya_hash_blake3`](#veya_hash_blake3) | [Proofs](proofs.md) | Public |
| 7 | [`veya_seal_decision`](#veya_seal_decision) | [Proofs](proofs.md) | API key · wallet or host signer |
| 8 | [`veya_anchor_proof`](#veya_anchor_proof) | [Proofs](proofs.md) | API key + wallet |
| 9 | [`veya_list_proofs`](#veya_list_proofs) | [Proofs](proofs.md) | API key (guest: listing) |
| 10 | [`veya_verify_proof_api`](#veya_verify_proof_api) | [Proofs](proofs.md) | Public |
| 11 | [`veya_verify_transaction`](#veya_verify_transaction) | [Verification](verification.md) | Public |
| 12 | [`veya_verify_commitment_onchain`](#veya_verify_commitment_onchain) | [Verification](verification.md) | Public |
| 13 | [`veya_commitment_exists`](#veya_commitment_exists) | [Verification](verification.md) | Public |
| 14 | [`veya_read_environment_onchain`](#veya_read_environment_onchain) | [Verification](verification.md) | Public |
| 15 | [`veya_read_agent_onchain`](#veya_read_agent_onchain) | [Verification](verification.md) | Public |
| 16 | [`veya_pq_keygen`](#veya_pq_keygen) | [Post-quantum identity](identity.md) | Public |
| 17 | [`veya_pq_sign`](#veya_pq_sign) | [Post-quantum identity](identity.md) | Public |
| 18 | [`veya_pq_verify`](#veya_pq_verify) | [Post-quantum identity](identity.md) | Public |
| 19 | [`veya_pq_fingerprint`](#veya_pq_fingerprint) | [Post-quantum identity](identity.md) | Public |
| 20 | [`veya_guest_login`](#veya_guest_login) | [Accounts, environments, agents](workspaces.md) | Public |
| 21 | [`veya_account`](#veya_account) | [Accounts, environments, agents](workspaces.md) | API key |
| 22 | [`veya_tier`](#veya_tier) | [Accounts, environments, agents](workspaces.md) | Public table · API key for your tier |
| 23 | [`veya_list_environments`](#veya_list_environments) | [Accounts, environments, agents](workspaces.md) | API key (guest: listing) |
| 24 | [`veya_get_environment`](#veya_get_environment) | [Accounts, environments, agents](workspaces.md) | API key (guest: listing) |
| 25 | [`veya_create_environment`](#veya_create_environment) | [Accounts, environments, agents](workspaces.md) | API key |
| 26 | [`veya_list_agents`](#veya_list_agents) | [Accounts, environments, agents](workspaces.md) | API key (guest: listing) |
| 27 | [`veya_deploy_agent`](#veya_deploy_agent) | [Accounts, environments, agents](workspaces.md) | API key |
| 28 | [`veya_set_tool_policy`](#veya_set_tool_policy) | [Policy and routing](policy-and-routing.md) | API key (owner) |
| 29 | [`veya_route_message`](#veya_route_message) | [Policy and routing](policy-and-routing.md) | API key (owner) |
| 30 | [`veya_route_secure_message`](#veya_route_secure_message) | [Policy and routing](policy-and-routing.md) | API key (owner) |
| 31 | [`veya_verify_secure_message`](#veya_verify_secure_message) | [Policy and routing](policy-and-routing.md) | Public |
| 32 | [`veya_boundnet_invoke`](#veya_boundnet_invoke) | [Policy and routing](policy-and-routing.md) | API key |
| 33 | [`veya_run_consensus`](#veya_run_consensus) | [Execution](execution.md) | API key · 30/min per key |
| 34 | [`veya_client_run_consensus`](#veya_client_run_consensus) | [Execution](execution.md) | API key · 30/min per key |
| 35 | [`veya_sealed_execute`](#veya_sealed_execute) | [Execution](execution.md) | API key · 30/min per key |
| 36 | [`veya_run_protected_execution`](#veya_run_protected_execution) | [Execution](execution.md) | API key |
| 37 | [`veya_list_executions`](#veya_list_executions) | [Execution](execution.md) | API key (guest: listing) |
| 38 | [`veya_store_memory`](#veya_store_memory) | [Memory](memory.md) | API key (owner) |
| 39 | [`veya_read_memory`](#veya_read_memory) | [Memory](memory.md) | API key (owner) |
| 40 | [`veya_invalidate_memory`](#veya_invalidate_memory) | [Memory](memory.md) | API key (owner) |
| 41 | [`veya_list_api_memory`](#veya_list_api_memory) | [Memory](memory.md) | API key (guest: listing) |
| 42 | [`veya_public_stats`](#veya_public_stats) | [Public registry](registry.md) | Public |
| 43 | [`veya_public_list_agents`](#veya_public_list_agents) | [Public registry](registry.md) | Public |
| 44 | [`veya_public_get_agent`](#veya_public_get_agent) | [Public registry](registry.md) | Public |
| 45 | [`veya_public_list_certificates`](#veya_public_list_certificates) | [Public registry](registry.md) | Public |
| 46 | [`veya_public_get_certificate`](#veya_public_get_certificate) | [Public registry](registry.md) | Public |
| 47 | [`veya_public_get_execution`](#veya_public_get_execution) | [Public registry](registry.md) | Public |
| 48 | [`veya_store_commitment`](#veya_store_commitment) | [On-chain writes](onchain-writes.md) | API key + wallet |
| 49 | [`veya_attest_execution`](#veya_attest_execution) | [On-chain writes](onchain-writes.md) | API key + wallet |
| 50 | [`veya_register_environment`](#veya_register_environment) | [On-chain writes](onchain-writes.md) | API key + wallet |
| 51 | [`veya_register_pq_onchain`](#veya_register_pq_onchain) | [On-chain writes](onchain-writes.md) | API key + wallet |
| 52 | [`veya_anchor_pq_attestation`](#veya_anchor_pq_attestation) | [On-chain writes](onchain-writes.md) | API key + wallet |
| 53 | [`veya_paid_discover`](#veya_paid_discover) | [Metered Call Guard](call-guard.md) | Public |
| 54 | [`veya_paid_call`](#veya_paid_call) | [Metered Call Guard](call-guard.md) | API key + your wallets |
| 55 | [`veya_paid_run`](#veya_paid_run) | [Metered Call Guard](call-guard.md) | API key |

## Service and chain

Workflow guide: [service-and-chain.md](service-and-chain.md)

### `veya_describe`

Service info: AES-256-GCM sealed execution and the selected settlement profile

- **Access:** Public
- **Use when:** First call after connecting; confirms network, contract and write policy.
- **Returns:** `name`, `version`, `publicUrl`, `settlement` {`network`,`contract`,`explorer`,`rpc`}, `productApi`, `sealed`, `mainnet`, `writes`, `toolSurface`, `fleet`, `publicPasteUrl`, `paidCallGuard` (when the Metered Call Guard is on)

_No arguments._

```json
{"tool": "veya_describe", "arguments": {}}
```

### `veya_ping_chain`

Ping Robinhood Chain RPC — block, contract

- **Access:** Public
- **Use when:** Live RPC check before any verify or write.
- **Returns:** `blockNumber` and `config` {`network`,`rpcUrl`,`contractAddress`,`explorerUrl`,`payerConfigured`,`requestTimeoutMs`,`validators`,`sealedNodeConfigured`}

_No arguments._

```json
{"tool": "veya_ping_chain", "arguments": {}}
```

### `veya_api_health`

Check the VEYA API /health status (api.veyanet.tech), including fleet status

- **Access:** Public
- **Use when:** Before consensus, sealed or product calls; shows fleet status.
- **Returns:** `{ httpStatus, body }`; body has `status` (`ok`/`degraded`), `validators.reachable`, `sealed.reachable`

_No arguments._

```json
{"tool": "veya_api_health", "arguments": {}}
```

### `veya_writes_status`

Reports how on-chain writes work on this server: your own wallet sends them, the host signer does not

- **Access:** Public
- **Use when:** Before a write, to show the user which wallet sends the write.
- **Returns:** `userPaidWrites`, `operatorRelayerWrites`, `requires` {`apiKey`,`payerPrivateKey`}, `gasPayer`, `emptyWallet`, `faucetUrl` (Testnet)

_No arguments._

```json
{"tool": "veya_writes_status", "arguments": {}}
```

## Proofs

Workflow guide: [proofs.md](proofs.md)

### `veya_prove`

Prove text, canonical JSON, or a receipt hash. Anchor writes only when a product API key and a payer key are passed.

- **Access:** Public · anchor: API key + wallet
- **Use when:** One proof object from text, JSON, or a receipt hash.
- **Returns:** `ok`, `mode` (`digest`/`verified`/`anchored`/`refused`), `inputKind`, `digestHex`, `anchored`, `network`, `contractAddress`, `txHash`, `explorerUrl`, `event`, `blockNumber`, `refusal`
- **Notes:** Pass exactly one of `text`, `json`, `txHash`. With `anchor: true` also pass `apiKey`, `environmentId` (an environment already on Veya.sol; UUID or 32-hex) and the funded wallet. Same JSON object with keys in another order hashes the same. Text `hello` → digest `ea8f163db38682925e4491c5e58d4bb3506ef8c14eb78a86e908c5624a67200f`.

| Arg | Type | Required | Notes |
|---|---|---|---|
| `text` | string | one of text/json/txHash |  |
| `json` | string | one of text/json/txHash |  |
| `txHash` | string | one of text/json/txHash | `0x` + 64 hex |
| `anchor` | boolean | no |  |
| `environmentId` | string | when `anchor` is true |  |
| `apiKey` | string | when `anchor` is true | product key `veya_dev_…` / `veya_live_…` |
| `payerPrivateKey` | string | when `anchor` is true | funded wallet bound to the API key (or `VEYA_PAYER_PRIVATE_KEY` on a self-hosted server) |

```json
{"tool": "veya_prove", "arguments": {"text": "hello"}}
```

### `veya_hash_blake3`

Compute BLAKE3 hex digest (commitment helper)

- **Access:** Public
- **Use when:** Preview a BLAKE3-256 digest before storing a commitment.
- **Returns:** `{ hash }` (64 hex chars)

| Arg | Type | Required | Notes |
|---|---|---|---|
| `data` | string | yes |  |

```json
{"tool": "veya_hash_blake3", "arguments": {"data": "hello"}}
```

### `veya_seal_decision`

Seal an evidence id and a decision. attestExecution is sent only when a payer key is passed, or when this host's seal-decision host-signer switch is on. On Mainnet pass environmentId or set VEYA_SEAL_DECISION_ENVIRONMENT_ID. Without either, the result is SEAL_ENVIRONMENT_REQUIRED and nothing is sent.

- **Access:** API key · wallet or host signer
- **Use when:** Seal an evidence id + decision object with a sealed fingerprint, 2-of-3 ML-DSA quorum, and an `attestExecution` receipt.
- **Returns:** `ok`, `evidenceId`, `sealed`, `attestation`, `chain` (receipt or null). Sealed node offline: `code` `SEALED_UNREACHABLE`, `transaction` null
- **Notes:** `decision` is a string that parses to a JSON object. The receipt is written when `payerPrivateKey` is passed, or when the host enables `VEYA_SEAL_DECISION_RELAYER` (then 20 per key and 100 per host per UTC day). Otherwise `chain` is null. **Mainnet:** pass `environmentId` of an environment registered on Robinhood Chain (`veya_create_environment` → `veya_register_environment`), unless the host sets `VEYA_SEAL_DECISION_ENVIRONMENT_ID`. Without one the call returns `SEAL_ENVIRONMENT_REQUIRED` and nothing is sent.

| Arg | Type | Required | Notes |
|---|---|---|---|
| `evidenceId` | string | yes |  |
| `decision` | string | yes |  |
| `apiKey` | string | yes | product key `veya_dev_…` / `veya_live_…` |
| `payerPrivateKey` | string | no (writes the receipt when passed) | funded wallet bound to the API key (or `VEYA_PAYER_PRIVATE_KEY` on a self-hosted server) |
| `environmentId` | string (UUID) | mainnet: yes, unless the host sets `VEYA_SEAL_DECISION_ENVIRONMENT_ID` | environment registered on the active network |

```json
{"tool": "veya_seal_decision", "arguments": {"evidenceId": "ticket-1042", "decision": "{\"action\":\"approve\"}", "apiKey": "veya_live_…", "environmentId": "…"}}
```

### `veya_anchor_proof`

Anchor a content proof on Veya.sol from YOUR wallet (apiKey + payerPrivateKey). Does not use the hosted signer.

- **Access:** API key + wallet
- **Use when:** Stamp labeled content on Veya.sol; hashes `label\ncontent` with BLAKE3 and calls `storeCommitment`.
- **Returns:** `{ txHash, explorer, from, hash, label, recorded }` (plus `recordError` if the proof list could not save it)

| Arg | Type | Required | Notes |
|---|---|---|---|
| `apiKey` | string | yes | product key `veya_dev_…` / `veya_live_…` |
| `sessionToken` | string | no | guest JWT, listing only |
| `payerPrivateKey` | string | yes (or server env) | funded wallet bound to the API key (or `VEYA_PAYER_PRIVATE_KEY` on a self-hosted server) |
| `label` | string | yes |  |
| `content` | string | yes |  |
| `environmentId` | string | no | omit to use the shared proof environment; pass an environment registered on the active network to anchor under it |

```json
{"tool": "veya_anchor_proof", "arguments": {"apiKey": "veya_live_…", "label": "contract-v2", "content": "…"}}
```

### `veya_list_proofs`

List Use-mode proof anchors for the account

- **Access:** API key (guest: listing)
- **Use when:** List Use-mode proof anchors for the account.
- **Returns:** `{ httpStatus, body }`

| Arg | Type | Required | Notes |
|---|---|---|---|
| `apiKey` | string | yes (or `sessionToken` for listing) | product key `veya_dev_…` / `veya_live_…` |
| `sessionToken` | string | no | guest JWT, listing only |

```json
{"tool": "veya_list_proofs", "arguments": {"apiKey": "veya_live_…"}}
```

### `veya_verify_proof_api`

Check a commitment or anchor transaction through the product API. A registration transaction is not a proof and returns NOT_VEYA_TX.

- **Access:** Public
- **Use when:** Verify an anchored proof through the product API (`GET /api/verify/:signature`).
- **Returns:** `{ httpStatus, body }` with `valid`
- **Notes:** `signature` is the tx hash of a commitment write (`veya_prove` with `anchor`, `veya_store_commitment`, `veya_anchor_proof`, `veya_seal_decision`). A registration tx (`veya_register_environment` / `veya_deploy_agent`) returns `NOT_VEYA_TX` (HTTP 404); check those with `veya_verify_transaction`.

| Arg | Type | Required | Notes |
|---|---|---|---|
| `signature` | string | yes |  |

```json
{"tool": "veya_verify_proof_api", "arguments": {"signature": "0x…"}}
```

## Verification

Workflow guide: [verification.md](verification.md)

### `veya_verify_transaction`

Verify a Veya.sol commitment from a Robinhood Chain tx hash

- **Access:** Public
- **Use when:** Someone sent a tx hash; parse its Veya.sol events.
- **Returns:** Parsed proofs/events, digests, whether the receipt is a VEYA write

| Arg | Type | Required | Notes |
|---|---|---|---|
| `txHash` | string | yes | `0x` + 64 hex |

```json
{"tool": "veya_verify_transaction", "arguments": {"txHash": "0x…"}}
```

### `veya_verify_commitment_onchain`

Parse Veya.sol events from a tx and eth_call commitments(digest) for each CommitmentStored

- **Access:** Public
- **Use when:** Strongest check: events in the receipt and `commitments(digest)` still true.
- **Returns:** Parsed events plus on-chain mapping result per digest

| Arg | Type | Required | Notes |
|---|---|---|---|
| `txHash` | string | yes | `0x` + 64 hex |

```json
{"tool": "veya_verify_commitment_onchain", "arguments": {"txHash": "0x…"}}
```

### `veya_commitment_exists`

eth_call Veya.sol commitments(bytes32) for a digest

- **Access:** Public
- **Use when:** You have a 32-byte digest and want a yes/no.
- **Returns:** `{ digestHex, onChain }`

| Arg | Type | Required | Notes |
|---|---|---|---|
| `digestHex` | string | yes | 32 bytes (64 hex) |

```json
{"tool": "veya_commitment_exists", "arguments": {"digestHex": "ea8f…200f"}}
```

### `veya_read_environment_onchain`

Read environment record from Veya.sol by 16-byte UUID hex

- **Access:** Public
- **Use when:** Read an environment record by 16-byte UUID.
- **Returns:** Environment record from Veya.sol

| Arg | Type | Required | Notes |
|---|---|---|---|
| `environmentUuidHex` | string | yes | 16 bytes (32 hex) |

```json
{"tool": "veya_read_environment_onchain", "arguments": {"environmentUuidHex": "7d0af0ba2ae34d0ebc750bbaccb3dd71"}}
```

### `veya_read_agent_onchain`

Read agent record from Veya.sol by 16-byte UUID hex

- **Access:** Public
- **Use when:** Read an agent record by 16-byte UUID.
- **Returns:** Agent record from Veya.sol

| Arg | Type | Required | Notes |
|---|---|---|---|
| `agentUuidHex` | string | yes | 16 bytes (32 hex) |

```json
{"tool": "veya_read_agent_onchain", "arguments": {"agentUuidHex": "55c7a27d64a3420589ab9fa0ab13a9af"}}
```

## Post-quantum identity

Workflow guide: [identity.md](identity.md)

### `veya_pq_keygen`

Generate an ML-DSA-44 post-quantum identity keypair (local, no chain write)

- **Access:** Public
- **Use when:** New ML-DSA-44 identity for an agent or operator.
- **Returns:** `publicKeyHex`, `privateKeyHex`, `publicKeyHash`

_No arguments._

```json
{"tool": "veya_pq_keygen", "arguments": {}}
```

### `veya_pq_sign`

Sign UTF-8 text with ML-DSA-44

- **Access:** Public
- **Use when:** Sign UTF-8 text with ML-DSA-44.
- **Returns:** `{ signatureHex }`

| Arg | Type | Required | Notes |
|---|---|---|---|
| `message` | string | yes |  |
| `privateKeyHex` | string | yes |  |

```json
{"tool": "veya_pq_sign", "arguments": {"message": "hello", "privateKeyHex": "…"}}
```

### `veya_pq_verify`

Verify an ML-DSA-44 signature over UTF-8 text

- **Access:** Public
- **Use when:** Check an ML-DSA-44 signature.
- **Returns:** `{ valid }`

| Arg | Type | Required | Notes |
|---|---|---|---|
| `message` | string | yes |  |
| `signatureHex` | string | yes |  |
| `publicKeyHex` | string | yes |  |

```json
{"tool": "veya_pq_verify", "arguments": {"message": "hello", "signatureHex": "…", "publicKeyHex": "…"}}
```

### `veya_pq_fingerprint`

BLAKE3 fingerprint of an ML-DSA public key hex

- **Access:** Public
- **Use when:** BLAKE3 fingerprint of a public key (the identity hash used on-chain).
- **Returns:** `{ publicKeyHash }`

| Arg | Type | Required | Notes |
|---|---|---|---|
| `publicKeyHex` | string | yes |  |

```json
{"tool": "veya_pq_fingerprint", "arguments": {"publicKeyHex": "…"}}
```

## Accounts, environments, agents

Workflow guide: [workspaces.md](workspaces.md)

### `veya_guest_login`

Create a guest session on the product API (Use listing only). Guest cannot mint API keys or write from MCP.

- **Access:** Public
- **Use when:** Browse showcase rooms without a key.
- **Returns:** Guest JWT, `mode: guest` (listing only)

_No arguments._

```json
{"tool": "veya_guest_login", "arguments": {}}
```

### `veya_account`

Return the wallet bound to a product API key

- **Access:** API key
- **Use when:** Confirm which wallet a key belongs to.
- **Returns:** `{ httpStatus, body }` with `wallet`

| Arg | Type | Required | Notes |
|---|---|---|---|
| `apiKey` | string | yes (or `sessionToken` for listing) | product key `veya_dev_…` / `veya_live_…` |
| `sessionToken` | string | no | guest JWT, listing only |

```json
{"tool": "veya_account", "arguments": {"apiKey": "veya_live_…"}}
```

### `veya_tier`

Your $VEYA account tier and the limits it sets on the product API. Without a key: `GET /public/holder-tiers` (the tier table). With `apiKey` or a session token: `GET /v1/holder/tier` (your tier, summed balance, counted wallets, limits, today's use).

- **Access:** Public (table) · API key (your tier)
- **Use when:** Show the public tier table, or the caller's tier / daily limits / rate limits.
- **Returns:** Tier table, or `{ httpStatus, body }` with `tier`, `balance`, `source`, `wallets`, `limits`, `today`, `nextTier`
- **Notes:** The API sums $VEYA on the sign-in wallet plus up to 3 linked wallets on Robinhood Chain mainnet. Over daily limits → HTTP 429 `DAILY_LIMIT`. Over per-minute key rate → HTTP 429 `RATE_LIMIT`. Matches `@veyanet/mcp` 1.2.13.

| Arg | Type | Required | Notes |
|---|---|---|---|
| `apiKey` | string | no | product key for your tier; omit for public table |
| `sessionToken` | string | no | guest/session token alternative |

```json
{"tool": "veya_tier", "arguments": {}}
```

```json
{"tool": "veya_tier", "arguments": {"apiKey": "veya_live_…"}}
```

### `veya_list_environments`

List environments for a product API key (guest JWT sees showcase / Use scope only)

- **Access:** API key (guest: listing)
- **Use when:** List environments for the key.
- **Returns:** `{ httpStatus, body }`

| Arg | Type | Required | Notes |
|---|---|---|---|
| `apiKey` | string | yes (or `sessionToken` for listing) | product key `veya_dev_…` / `veya_live_…` |
| `sessionToken` | string | no | guest JWT, listing only |

```json
{"tool": "veya_list_environments", "arguments": {"apiKey": "veya_live_…"}}
```

### `veya_get_environment`

Get one environment by id

- **Access:** API key (guest: listing)
- **Use when:** Fetch one environment.
- **Returns:** `{ httpStatus, body }`

| Arg | Type | Required | Notes |
|---|---|---|---|
| `apiKey` | string | yes (or `sessionToken` for listing) | product key `veya_dev_…` / `veya_live_…` |
| `sessionToken` | string | no | guest JWT, listing only |
| `environmentId` | string | yes |  |

```json
{"tool": "veya_get_environment", "arguments": {"apiKey": "veya_live_…", "environmentId": "…"}}
```

### `veya_create_environment`

Create a workspace in the product API. This does not spend gas and sends no transaction. Guests and non-keys are refused. type is stored for the API and defaults to research when omitted. Names that start with test, demo, tmp, sandbox, or e2e are refused. Use a real workspace name of at least 6 characters, or a name that starts with VEYA.

- **Access:** API key
- **Use when:** Create a workspace (API row, no gas).
- **Returns:** `{ httpStatus, body }` with the new environment
- **Notes:** `type` defaults to `research`. Register it on-chain later with `veya_register_environment`. Use a product-style name such as `VEYA Treasury Ops`: names with the words `test`, `demo`, `tmp` or `sandbox` return `CONFLICT` (HTTP 409).

| Arg | Type | Required | Notes |
|---|---|---|---|
| `apiKey` | string | yes | product key `veya_dev_…` / `veya_live_…` |
| `sessionToken` | string | no | guest JWT, listing only |
| `name` | string | yes |  |
| `type` | enum: `research`, `governance`, `treasury`, `contributor`, `protocol`, `desci` | no |  |

```json
{"tool": "veya_create_environment", "arguments": {"apiKey": "veya_live_…", "name": "VEYA Treasury Ops", "type": "treasury"}}
```

### `veya_list_agents`

List agents in an environment

- **Access:** API key (guest: listing)
- **Use when:** List agents in an environment.
- **Returns:** `{ httpStatus, body }`

| Arg | Type | Required | Notes |
|---|---|---|---|
| `apiKey` | string | yes (or `sessionToken` for listing) | product key `veya_dev_…` / `veya_live_…` |
| `sessionToken` | string | no | guest JWT, listing only |
| `environmentId` | string | yes |  |

```json
{"tool": "veya_list_agents", "arguments": {"apiKey": "veya_live_…", "environmentId": "…"}}
```

### `veya_deploy_agent`

Create an agent in a workspace. This does not spend gas and sends no transaction. Limit the agent with permissionConfig.allowedTools. If allowedTools is omitted, the agent cannot call a tool. type is stored for the API and defaults to coordination when omitted.

- **Access:** API key
- **Use when:** Create an agent with an explicit tool allowlist (no gas).
- **Returns:** `{ httpStatus, body }` with the new agent
- **Notes:** `type` defaults to `coordination`. List tools in `permissionConfig.allowedTools`; an agent with an empty list calls no tools.

| Arg | Type | Required | Notes |
|---|---|---|---|
| `apiKey` | string | yes | product key `veya_dev_…` / `veya_live_…` |
| `sessionToken` | string | no | guest JWT, listing only |
| `environmentId` | string | yes |  |
| `type` | string | no |  |
| `agentKind` | string | yes (the API refuses an agent without it) | display name, at least 3 characters, example `Record Agent` |
| `permissionConfig` | object | no |  |

```json
{"tool": "veya_deploy_agent", "arguments": {"apiKey": "veya_live_…", "environmentId": "…", "agentKind": "Record Agent", "permissionConfig": {"allowedTools": ["veya_prove"]}}}
```

## Policy and routing

Workflow guide: [policy-and-routing.md](policy-and-routing.md)

### `veya_set_tool_policy`

Allow or deny a tool for an agent. Requires the API key that owns the environment. Saved on that account.

- **Access:** API key (owner)
- **Use when:** Allow or deny one tool for one agent.
- **Returns:** Saved rule

| Arg | Type | Required | Notes |
|---|---|---|---|
| `apiKey` | string | yes | product key `veya_dev_…` / `veya_live_…` |
| `environmentId` | string | yes |  |
| `agentId` | string | yes |  |
| `tool` | string | yes |  |
| `allowed` | boolean | yes |  |

```json
{"tool": "veya_set_tool_policy", "arguments": {"apiKey": "veya_live_…", "environmentId": "…", "agentId": "…", "tool": "veya_anchor_proof", "allowed": false}}
```

### `veya_route_message`

Check the saved tool rule for this API key before routing. Deny stays deny.

- **Access:** API key (owner)
- **Use when:** Check the saved rule before passing a message between agents.
- **Returns:** Route result with `policyStatus` (`allowed`/`denied`)
- **Notes:** A denied rule stays denied.

| Arg | Type | Required | Notes |
|---|---|---|---|
| `apiKey` | string | yes | product key `veya_dev_…` / `veya_live_…` |
| `environmentId` | string | yes |  |
| `id` | string | yes |  |
| `fromAgent` | string | yes |  |
| `toAgent` | string | yes |  |
| `tool` | string | yes |  |
| `payload` | any JSON | no |  |

```json
{"tool": "veya_route_message", "arguments": {"apiKey": "veya_live_…", "environmentId": "…", "id": "msg-1", "fromAgent": "…", "toAgent": "…", "tool": "veya_prove"}}
```

### `veya_route_secure_message`

Check the saved tool rule for this API key, then wrap an allowed message in a Kyber-768 session and an ML-DSA-44 signature.

- **Access:** API key (owner)
- **Use when:** Same check, then Kyber-768 session + ML-DSA-44 signature envelope.
- **Returns:** Secure envelope (session id, signature, `policyStatus`)

| Arg | Type | Required | Notes |
|---|---|---|---|
| `apiKey` | string | yes | product key `veya_dev_…` / `veya_live_…` |
| `environmentId` | string | yes |  |
| `id` | string | yes |  |
| `fromAgent` | string | yes |  |
| `toAgent` | string | yes |  |
| `tool` | string | yes |  |
| `payload` | any JSON | no |  |
| `senderPrivateKeyHex` | string | yes |  |
| `senderPublicKeyHex` | string | yes |  |

```json
{"tool": "veya_route_secure_message", "arguments": {"apiKey": "veya_live_…", "environmentId": "…", "id": "msg-2", "fromAgent": "…", "toAgent": "…", "tool": "veya_prove", "senderPrivateKeyHex": "…", "senderPublicKeyHex": "…"}}
```

### `veya_verify_secure_message`

Verify ML-DSA signature on a secure routed message envelope

- **Access:** Public
- **Use when:** Receiver checks the envelope signature.
- **Returns:** `{ valid }`

| Arg | Type | Required | Notes |
|---|---|---|---|
| `message` | object | yes |  |
| `senderPublicKeyHex` | string | yes |  |

```json
{"tool": "veya_verify_secure_message", "arguments": {"message": {}, "senderPublicKeyHex": "…"}}
```

### `veya_boundnet_invoke`

Invoke a Boundnet / coordination tool via product API (policy gated)

- **Access:** API key
- **Use when:** Call a coordination tool as an agent; policy gated.
- **Returns:** `{ httpStatus, body }`

| Arg | Type | Required | Notes |
|---|---|---|---|
| `apiKey` | string | yes | product key `veya_dev_…` / `veya_live_…` |
| `sessionToken` | string | no | guest JWT, listing only |
| `environmentId` | string | yes |  |
| `agentId` | string | yes |  |
| `toolName` | string | yes |  |
| `arguments` | object | no |  |

```json
{"tool": "veya_boundnet_invoke", "arguments": {"apiKey": "veya_live_…", "environmentId": "…", "agentId": "…", "toolName": "…", "arguments": {}}}
```

## Execution

Workflow guide: [execution.md](execution.md)

### `veya_run_consensus`

Run 2-of-3 BLAKE3 consensus on this server's validator list. Needs a product API key. Returns consensus_reached: false if validators are unreachable.

- **Access:** API key · 30/min per key
- **Use when:** 2-of-3 BLAKE3 quorum digest before attesting.
- **Returns:** `task_id`, `agreed_blake3_hash`, `node_results`, `consensus_reached`, `threshold`, `reachable`

| Arg | Type | Required | Notes |
|---|---|---|---|
| `apiKey` | string | yes | product key `veya_dev_…` / `veya_live_…` |
| `taskId` | string | yes |  |
| `payload` | object | no |  |

```json
{"tool": "veya_run_consensus", "arguments": {"apiKey": "veya_live_…", "taskId": "task-8819", "payload": {"amount": 250}}}
```

### `veya_client_run_consensus`

Deprecated: use veya_run_consensus. Same server validator list. Needs a product API key.

- **Access:** API key · 30/min per key
- **Use when:** Deprecated alias. Use `veya_run_consensus`.
- **Returns:** Same as `veya_run_consensus`

| Arg | Type | Required | Notes |
|---|---|---|---|
| `apiKey` | string | yes | product key `veya_dev_…` / `veya_live_…` |
| `taskId` | string | yes |  |
| `payload` | object | no |  |

```json
{"tool": "veya_client_run_consensus", "arguments": {"apiKey": "veya_live_…", "taskId": "task-8819"}}
```

### `veya_sealed_execute`

AES-256-GCM protected execution on this server's sealed node. Needs a product API key. The call fails closed when that capacity is unreachable.

- **Access:** API key · 30/min per key
- **Use when:** Run a sensitive payload on the AES-256-GCM sealed node.
- **Returns:** Sealed result (commitment, `verified`, hashes); unverified seals return an error
- **Notes:** `sessionEntropyHex` minimum 16 hex chars; use 32 random bytes (64 hex).

| Arg | Type | Required | Notes |
|---|---|---|---|
| `apiKey` | string | yes | product key `veya_dev_…` / `veya_live_…` |
| `environmentId` | string | yes |  |
| `agentId` | string | yes |  |
| `eventType` | string | yes |  |
| `payload` | object | yes |  |
| `sessionEntropyHex` | string | yes |  |

```json
{"tool": "veya_sealed_execute", "arguments": {"apiKey": "veya_live_…", "environmentId": "…", "agentId": "…", "eventType": "decision", "payload": {}, "sessionEntropyHex": "<64 hex>"}}
```

### `veya_run_protected_execution`

Run protected execution via product API (Build). Product API key required.

- **Access:** API key
- **Use when:** Protected execution through the product API (Build).
- **Returns:** `{ httpStatus, body }`
- **Notes:** The sealed result is stored in the product API (`veya_list_executions`). This MCP call does not write on-chain. To put the result on Robinhood Chain, commit its hash with `veya_store_commitment` or `veya_prove` with `anchor: true`.

| Arg | Type | Required | Notes |
|---|---|---|---|
| `apiKey` | string | yes | product key `veya_dev_…` / `veya_live_…` |
| `sessionToken` | string | no | guest JWT, listing only |
| `environmentId` | string | yes |  |
| `agentId` | string | yes |  |
| `eventType` | string | yes |  |
| `payload` | object | yes |  |
| `disclose` | string[] | no |  |
| `seal` | string[] | no |  |

```json
{"tool": "veya_run_protected_execution", "arguments": {"apiKey": "veya_live_…", "environmentId": "…", "agentId": "…", "eventType": "decision", "payload": {}}}
```

### `veya_list_executions`

List executions for an environment via product API

- **Access:** API key (guest: listing)
- **Use when:** List executions for an environment.
- **Returns:** `{ httpStatus, body }`

| Arg | Type | Required | Notes |
|---|---|---|---|
| `apiKey` | string | yes (or `sessionToken` for listing) | product key `veya_dev_…` / `veya_live_…` |
| `sessionToken` | string | no | guest JWT, listing only |
| `environmentId` | string | yes |  |

```json
{"tool": "veya_list_executions", "arguments": {"apiKey": "veya_live_…", "environmentId": "…"}}
```

## Memory

Workflow guide: [memory.md](memory.md)

### `veya_store_memory`

Store agent memory for the account behind this API key. No key, no write.

- **Access:** API key (owner)
- **Use when:** Save agent memory on the account.
- **Returns:** Stored row with `id`

| Arg | Type | Required | Notes |
|---|---|---|---|
| `apiKey` | string | yes | product key `veya_dev_…` / `veya_live_…` |
| `environmentId` | string | yes |  |
| `agentId` | string | yes |  |
| `data` | string | yes |  |

```json
{"tool": "veya_store_memory", "arguments": {"apiKey": "veya_live_…", "environmentId": "…", "agentId": "…", "data": "…"}}
```

### `veya_read_memory`

Read one memory row owned by this API key.

- **Access:** API key (owner)
- **Use when:** Read one memory row.
- **Returns:** Memory row

| Arg | Type | Required | Notes |
|---|---|---|---|
| `apiKey` | string | yes | product key `veya_dev_…` / `veya_live_…` |
| `environmentId` | string | yes |  |
| `id` | string | yes |  |

```json
{"tool": "veya_read_memory", "arguments": {"apiKey": "veya_live_…", "environmentId": "…", "id": "…"}}
```

### `veya_invalidate_memory`

Nullify one memory row owned by this API key.

- **Access:** API key (owner)
- **Use when:** Nullify one memory row.
- **Returns:** Nullified row

| Arg | Type | Required | Notes |
|---|---|---|---|
| `apiKey` | string | yes | product key `veya_dev_…` / `veya_live_…` |
| `environmentId` | string | yes |  |
| `id` | string | yes |  |

```json
{"tool": "veya_invalidate_memory", "arguments": {"apiKey": "veya_live_…", "environmentId": "…", "id": "…"}}
```

### `veya_list_api_memory`

List memory entries via product API for an environment

- **Access:** API key (guest: listing)
- **Use when:** List memory entries for an environment.
- **Returns:** `{ httpStatus, body }`
- **Notes:** `scope`: `environment`, `agent`, or `session`.

| Arg | Type | Required | Notes |
|---|---|---|---|
| `apiKey` | string | yes (or `sessionToken` for listing) | product key `veya_dev_…` / `veya_live_…` |
| `sessionToken` | string | no | guest JWT, listing only |
| `environmentId` | string | yes |  |
| `scope` | enum: `environment`, `agent`, `session` | no |  |

```json
{"tool": "veya_list_api_memory", "arguments": {"apiKey": "veya_live_…", "environmentId": "…", "scope": "agent"}}
```

## Public registry

Workflow guide: [registry.md](registry.md)

### `veya_public_stats`

Public registry stats from the product API (verified agents / proofs)

- **Access:** Public
- **Use when:** Public counts (verified agents, proofs).
- **Returns:** `{ httpStatus, body }`

_No arguments._

```json
{"tool": "veya_public_stats", "arguments": {}}
```

### `veya_public_list_agents`

List public agent registry rows

- **Access:** Public
- **Use when:** Browse public agents (limit 1–100).
- **Returns:** `{ httpStatus, body }`
- **Notes:** `limit` 1–100.

| Arg | Type | Required | Notes |
|---|---|---|---|
| `limit` | number | no |  |
| `offset` | number | no |  |

```json
{"tool": "veya_public_list_agents", "arguments": {"limit": 20, "offset": 0}}
```

### `veya_public_get_agent`

Fetch one public agent by id

- **Access:** Public
- **Use when:** One public agent.
- **Returns:** `{ httpStatus, body }`

| Arg | Type | Required | Notes |
|---|---|---|---|
| `id` | string | yes |  |

```json
{"tool": "veya_public_get_agent", "arguments": {"id": "…"}}
```

### `veya_public_list_certificates`

List public certificate / attestation rows

- **Access:** Public
- **Use when:** Browse public certificates/attestations.
- **Returns:** `{ httpStatus, body }`

| Arg | Type | Required | Notes |
|---|---|---|---|
| `limit` | number | no |  |
| `offset` | number | no |  |

```json
{"tool": "veya_public_list_certificates", "arguments": {"limit": 20}}
```

### `veya_public_get_certificate`

Fetch one public certificate by id

- **Access:** Public
- **Use when:** One public certificate.
- **Returns:** `{ httpStatus, body }`

| Arg | Type | Required | Notes |
|---|---|---|---|
| `id` | string | yes |  |

```json
{"tool": "veya_public_get_certificate", "arguments": {"id": "…"}}
```

### `veya_public_get_execution`

Fetch a public execution proof by id

- **Access:** Public
- **Use when:** One public execution proof.
- **Returns:** `{ httpStatus, body }`

| Arg | Type | Required | Notes |
|---|---|---|---|
| `id` | string | yes |  |

```json
{"tool": "veya_public_get_execution", "arguments": {"id": "…"}}
```

## On-chain writes

Workflow guide: [onchain-writes.md](onchain-writes.md)

### `veya_store_commitment`

Write a 32-byte commitment to Veya.sol from YOUR wallet (apiKey + payerPrivateKey)

- **Access:** API key + wallet
- **Use when:** Store a 32-byte digest for a 16-byte environment UUID (`storeCommitment`).
- **Returns:** `{ txHash, explorer, from }`

| Arg | Type | Required | Notes |
|---|---|---|---|
| `apiKey` | string | yes | product key `veya_dev_…` / `veya_live_…` |
| `payerPrivateKey` | string | yes (or server env) | funded wallet bound to the API key (or `VEYA_PAYER_PRIVATE_KEY` on a self-hosted server) |
| `environmentUuidHex` | string | yes | 16 bytes (32 hex) |
| `commitmentHex` | string | yes | 32 bytes (64 hex) |

```json
{"tool": "veya_store_commitment", "arguments": {"apiKey": "veya_live_…", "environmentUuidHex": "…", "commitmentHex": "…"}}
```

### `veya_attest_execution`

attestExecution on Veya.sol from YOUR wallet with ML-DSA bytes

- **Access:** API key + wallet
- **Use when:** Write an execution hash + ML-DSA signature (`attestExecution`).
- **Returns:** `{ txHash, explorer, from }`

| Arg | Type | Required | Notes |
|---|---|---|---|
| `apiKey` | string | yes | product key `veya_dev_…` / `veya_live_…` |
| `payerPrivateKey` | string | yes (or server env) | funded wallet bound to the API key (or `VEYA_PAYER_PRIVATE_KEY` on a self-hosted server) |
| `environmentUuidHex` | string | yes | 16 bytes (32 hex) |
| `blake3HashHex` | string | yes | 32 bytes (64 hex) |
| `mldsaSigHex` | string | yes |  |

```json
{"tool": "veya_attest_execution", "arguments": {"apiKey": "veya_live_…", "environmentUuidHex": "…", "blake3HashHex": "…", "mldsaSigHex": "…"}}
```

### `veya_register_environment`

registerEnvironment on Veya.sol from YOUR wallet. Pass product environmentId to confirm the tx on the product API.

- **Access:** API key + wallet
- **Use when:** Register an environment on-chain; with `environmentId` the product console is synced.
- **Returns:** `txHash`, `explorer`, `from`, `environmentUuidHex`, `consoleSynced`, `confirmed`, optional `warning`
- **Notes:** Pass `environmentId` (product id) or `environmentUuidHex`. `envType` 0–10, default 0. `pqPubkeyHashHex` defaults to 32 zero bytes. If `warning` is set the chain write succeeded; do not resend.

| Arg | Type | Required | Notes |
|---|---|---|---|
| `apiKey` | string | yes | product key `veya_dev_…` / `veya_live_…` |
| `payerPrivateKey` | string | yes (or server env) | funded wallet bound to the API key (or `VEYA_PAYER_PRIVATE_KEY` on a self-hosted server) |
| `environmentUuidHex` | string | one of the two | 16 bytes (32 hex) |
| `environmentId` | string | one of the two |  |
| `pqPubkeyHashHex` | string | no | 32 bytes (64 hex) |
| `envType` | number | no |  |

```json
{"tool": "veya_register_environment", "arguments": {"apiKey": "veya_live_…", "environmentId": "…"}}
```

### `veya_register_pq_onchain`

Generate ML-DSA identity and register environment + commitment on Veya.sol from YOUR wallet

- **Access:** API key + wallet
- **Use when:** Generate an ML-DSA identity and register environment + commitment in one step.
- **Returns:** Public key hex/hash, `environmentTx`, `memoTx`, `explorer`, `from`
- **Notes:** `envType` 0–10, default 1.

| Arg | Type | Required | Notes |
|---|---|---|---|
| `apiKey` | string | yes | product key `veya_dev_…` / `veya_live_…` |
| `payerPrivateKey` | string | yes (or server env) | funded wallet bound to the API key (or `VEYA_PAYER_PRIVATE_KEY` on a self-hosted server) |
| `envType` | number | no |  |

```json
{"tool": "veya_register_pq_onchain", "arguments": {"apiKey": "veya_live_…", "envType": 1}}
```

### `veya_anchor_pq_attestation`

Link PQ identity hash to execution hash on Veya.sol from YOUR wallet

- **Access:** API key + wallet
- **Use when:** Link a PQ identity hash to an execution hash (`anchorPqAttestation`).
- **Returns:** `{ txHash, explorer, from }`

| Arg | Type | Required | Notes |
|---|---|---|---|
| `apiKey` | string | yes | product key `veya_dev_…` / `veya_live_…` |
| `payerPrivateKey` | string | yes (or server env) | funded wallet bound to the API key (or `VEYA_PAYER_PRIVATE_KEY` on a self-hosted server) |
| `environmentUuidHex` | string | yes | 16 bytes (32 hex) |
| `identityHashHex` | string | yes | 32 bytes (64 hex) |
| `executionHashHex` | string | yes | 32 bytes (64 hex) |

```json
{"tool": "veya_anchor_pq_attestation", "arguments": {"apiKey": "veya_live_…", "environmentUuidHex": "…", "identityHashHex": "…", "executionHashHex": "…"}}
```

## Metered Call Guard

Workflow guide: [call-guard.md](call-guard.md). These three tools are listed when the server runs with the Metered Call Guard on; the hosted server has it on. With Guard on, the full catalog is **55** tools (52 without).

### `veya_paid_discover`

Read-only. List the resources this VEYA policy allows, with their input fields, a live quote (nothing is sent), the provider quote and total charged to your task budget for each resource, the per-call cap, task budget maximum and default, and a policy preview. Cannot pay.

- **Access:** Public
- **Use when:** Before `veya_paid_call`, to pick a `resourceId` and see its `input` fields.
- **Returns:** `{ disabled, killSwitch, killSwitchNote, limits, receiptsNote, runsPrivacy, requires, defaultEnvironmentUuid, resources: [{ id, provider, payer, description, input, quote, amounts, policyPreview }] }`

No arguments.

```json
{"tool": "veya_paid_discover", "arguments": {}}
```

### `veya_paid_call`

Run one metered x402 resource call through the VEYA policy gate. Needs your VEYA apiKey, your Solana key (solanaPrivateKey, pays the USDC) and your Robinhood Chain key (robinhoodPrivateKey, signs the VEYA record and pays its ETH gas). Keys are used in memory for this call only and never stored. Before anything is signed VEYA checks the apiKey, both keys, your USDC and ETH balances, and the policy (tool, host, resource, network, asset, payee, per-call cap, task budget, input). It then pays, verifies the Solana payment at finalized, runs a 2-of-3 validator check, records a commitment on Robinhood Chain from your address and saves the run to your VEYA account. Every refusal returns decision blocked, a stable code and payment: null. The kill switch is operator-only; callers cannot change it. Allow at least 120 s for the tool call timeout.

- **Access:** API key + your Solana and Robinhood Chain wallets
- **Use when:** The agent needs an allowed x402 resource and you want a policy check and an on-chain record of the call.
- **Returns:** `{ runId, decision, state, reason, statuses, payment, validators, veya, receipt, account, output }`. The same `idempotencyKey` again returns the stored run with `replay: true` and never pays twice.

| Arg | Type | Required | Notes |
|---|---|---|---|
| `apiKey` | string | yes | product key `veya_dev_…` / `veya_live_…`; the run is saved to its account |
| `taskId` | string | yes | calls with the same `taskId` share one task budget |
| `idempotencyKey` | string | yes | 8–120 chars, new per payment; reuse only to retry |
| `input` | object | yes | string fields exactly as `veya_paid_discover` lists them |
| `resourceId` | string | no | a resource id from `veya_paid_discover` |
| `taskBudgetAtomic` | string | no | USDC atomic units (`10000` = 0.01 USDC); cannot exceed the policy maximum |
| `environmentUuid` | string | no | defaults to the environment `veya_paid_discover` shows |
| `url` | string | no | normally omit; anything outside the allowlist is blocked |
| `solanaPrivateKey` | string | yes, unless the server has a limit set for your key id | your Solana key (base58 or JSON array of 64 numbers); pays the USDC; used in memory for this call only |
| `robinhoodPrivateKey` | string | yes, unless the server has a limit set for your key id | your Robinhood Chain key (`0x` + 64 hex); signs the record and pays its gas; used in memory for this call only |

Limits on the hosted server (read live from `veya_paid_discover`): 0.01 USDC per-call cap, 0.02 USDC largest total per call, 0.04 USDC task budget maximum, 2 calls per task.

```json
{"tool": "veya_paid_call", "arguments": {"apiKey": "veya_live_…", "taskId": "task-1", "idempotencyKey": "<new uuid>", "resourceId": "…", "input": {"…": "…"}}}
```

### `veya_paid_run`

Read your VEYA runs. Private to the account of the apiKey you pass; another key cannot read them. With runId: one run (state, payment, validators, Robinhood record, receipt). Without runId: the runs saved to your VEYA account. With resume=true: retries Solana verification, the 2-of-3 validator check or the Robinhood record for an unfinished run; pass robinhoodPrivateKey to sign the record. Never pays.

- **Access:** API key (your own runs only)
- **Use when:** A call ended in `payment_unverifiable`, `commit_failed` or `resource_error`, or you want the proof links again.
- **Returns:** with `runId`, the `veya_paid_call` shape without `output`; without it, `{ paidRuns: [...] }`

| Arg | Type | Required | Notes |
|---|---|---|---|
| `apiKey` | string | yes | product key |
| `runId` | string | no | omit to list your runs |
| `resume` | boolean | no | retry unfinished steps |
| `robinhoodPrivateKey` | string | no | needed to resume a `commit_failed` run you signed yourself |

```json
{"tool": "veya_paid_run", "arguments": {"apiKey": "veya_live_…", "runId": "…"}}
```
